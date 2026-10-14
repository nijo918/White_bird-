# White Bird Tiling & Painting Specialists

Static single page website with Supabase PostgreSQL, Auth, Row Level Security, and Storage support. The current page design is kept in `white_bird_tiling_painting_specialists_mvp.html`.

## Project files

- `white_bird_tiling_painting_specialists_mvp.html` — website layout and owner dashboard.
- `whitebird-app.js` — public data, customer quote submissions, owner login, admin tools, and image uploads.
- `whitebird.config.js` — browser safe Supabase URL, anon key, and storage bucket.
- `supabase_setup.sql` — tables, RLS policies, upload bucket, and starter public data.
- `.env.example` — public config names for hosted build/deploy workflows.

## Create and configure Supabase

1. Create a Supabase account and a new project. Save the database password in a password manager; it is not used by this website.
2. In the project dashboard, open **Project Settings → API** (or **API Keys**). Copy the Project URL and the publishable/anon public key. Never use the `service_role` or secret key in frontend code.
3. Open **SQL Editor**, create a query, paste the entire `supabase_setup.sql`, and run it. It creates the tables, RLS rules, `portfolio-images` bucket, and starter service/settings rows.
4. In **Authentication → Users**, add the owner's email and a strong password. Disable public sign-ups if they are enabled; owners should be provisioned by the project administrator.
5. Copy the owner's user UUID from the Auth user list. Run this in SQL Editor, replacing the placeholder:

   ```sql
   insert into public.owner_profiles (user_id, display_name)
   values ('AUTH_USER_UUID_HERE', 'White Bird Owner');
   ```

6. Put the Project URL and public anon/publishable key in `whitebird.config.js`:

   ```js
   window.WHITE_BIRD_CONFIG = {
     supabaseUrl: "https://your-project-ref.supabase.co",
     supabaseAnonKey: "your-public-anon-key",
  businessPhone: "072 112 5693",
     storageBucket: "portfolio-images"
   };
   ```

   Or use your hosting provider's build-time variables from `.env.example` to generate this browser config. A `.env` file alone is not read by this static HTML page.

7. Serve the folder with a static web server and open the site. Sign in through **Owner Login** with the Auth email and password. Test the customer quote form while signed out. In the owner dashboard, confirm the request appears, then upload a JPEG, PNG, or WebP project photo (up to 5 MB).

## Local preview and security

Without Supabase values, the site uses placeholder content and localStorage preview mode; owner login and real uploads are unavailable. The browser only needs the project URL and public key. RLS limits customer quote writes and prevents public quote reads; only users added to `owner_profiles` can access owner data and upload/delete images. The `portfolio-images` bucket is public for published website image display, while writes require an authorized owner session.

The SQL setup uses `create table if not exists` and replaces its policies/triggers, so it can be rerun to repair policy changes. It seeds initial data only when the corresponding tables are empty. Do not put customer personal data in public pages or image URLs.

## Testing checklist

- Public navigation, mobile menu, ZAR calculator, and estimate disclaimer.
- Quote form required fields, South African phone validation, and a real unauthenticated insert.
- Confirm an unauthenticated client cannot select quote rows.
- Owner login, logout, and session restore; non-owner Auth users must be rejected.
- Owner can view/update/delete quotes and manage projects, news, and settings.
- Owner can upload an image to `portfolio-images`; public image URL loads; unauthorized upload/delete fails.
- Public users can read only public services/settings and published projects/updates.
- Empty gallery/news states, image fallback, backend errors, desktop, and mobile layout.

## Business details to confirm

Before publishing, the owner should confirm the real telephone/WhatsApp numbers, email, address, service areas, rates, guarantees, registration details, and any testimonials.
