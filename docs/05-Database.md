# Database Design

## Engine

**PostgreSQL** — chosen for its strong ecosystem support, wide adoption 
in the industry, and free-tier compatibility with common deployment 
platforms (Render, Railway, Supabase).

## Image storage strategy

Images are **not stored in the database or on the application server**. 
They are uploaded to **Cloudinary** (external cloud storage), which 
returns a permanent public URL. Only this URL is persisted in the 
`images` table. This avoids relying on ephemeral filesystem storage, 
which many free hosting platforms wipe on redeploy.

## Schema

### `users`

| Column        | Type         | Constraints                  |
|---------------|--------------|-------------------------------|
| id            | UUID / SERIAL| PRIMARY KEY                   |
| email         | VARCHAR(255) | UNIQUE, NOT NULL               |
| password_hash | VARCHAR(255) | NOT NULL                       |
| display_name  | VARCHAR(100) | NOT NULL                       |
| role          | VARCHAR(20)  | NOT NULL, DEFAULT 'user'       |
| created_at    | TIMESTAMP    | NOT NULL, DEFAULT now()        |

### `listings`

| Column       | Type          | Constraints                                    |
|--------------|---------------|-------------------------------------------------|
| id           | UUID / SERIAL | PRIMARY KEY                                     |
| user_id      | UUID / INT    | NOT NULL, FOREIGN KEY → users(id)               |
| title        | VARCHAR(150)  | NOT NULL                                        |
| description  | TEXT          | NOT NULL                                        |
| price        | DECIMAL(10,2) | NOT NULL                                        |
| category     | VARCHAR(30)   | NOT NULL (enum-like, validated at app level)    |
| condition    | VARCHAR(20)   | NOT NULL (enum-like, validated at app level)    |
| status       | VARCHAR(20)   | NOT NULL, DEFAULT 'available'                   |
| created_at   | TIMESTAMP     | NOT NULL, DEFAULT now()                         |
| updated_at   | TIMESTAMP     | NOT NULL, DEFAULT now()                         |

### `images`

| Column        | Type          | Constraints                                |
|---------------|---------------|----------------------------------------------|
| id            | UUID / SERIAL | PRIMARY KEY                                 |
| listing_id    | UUID / INT    | NOT NULL, FOREIGN KEY → listings(id)        |
| url           | VARCHAR(500)  | NOT NULL (Cloudinary URL)                   |
| display_order | SMALLINT      | NOT NULL, DEFAULT 0                          |

## Relationships (foreign keys)

- `listings.user_id` → `users.id` (one user, many listings)
- `images.listing_id` → `listings.id` (one listing, up to 5 images)

Both foreign keys use `ON DELETE CASCADE`: if a user is deleted, their 
listings are deleted too; if a listing is deleted, its images are 
deleted too. This keeps the database consistent without orphaned rows.

## Indexes

- `users.email` — unique index (needed for login lookups and uniqueness 
  constraint)
- `listings.category` — index to speed up category-filtered searches
- `listings.status` — index to speed up default "available only" queries

## Design notes

- **UUID chosen as primary key type** for all tables, instead of 
  auto-incrementing integers (SERIAL). This avoids exposing sequential 
  information through IDs (e.g. a URL like `/listings/47` would reveal 
  the approximate number of listings created) and is consistent with 
  patterns used in modern, potentially distributed systems. The specific 
  ORM/library used to generate UUIDs will be confirmed in 
  06-Architecture.md.

- **Category and condition remain simple VARCHAR fields**, validated at 
  the application layer against the fixed lists defined in 
  04-DomainModel.md, rather than separate lookup tables — consistent 
  with the domain model decision to keep them as enums, not entities.
  
- **Enforcing max 5 images per listing** is a business rule, not easily 
  expressed as a pure database constraint — it will be validated at the 
  application/API layer (see 07-API-Design.md).