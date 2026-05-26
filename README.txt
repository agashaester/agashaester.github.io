SENLA RESEARCH HUB — SITE FILES
================================

What's in this folder
---------------------
• SENLA Homepage.html       The landing page.
• SENLA About.html          Who we are, mission/vision, team.
• SENLA Projects.html       Project list with filters & search.
• SENLA Blog.html           Blog (reader + author workspace).
• SENLA Contact.html        Contact form + office details.
• SENLA Course Learner.html Course experience (learner side).
• SENLA Course Admin.html   Course experience (admin side).
• images/                   All photos and the logo.

All the content was rebuilt from your agashaester.github.io repo so the
team list, services, projects, and contact details all match.


How the Blog works (READ THIS)
------------------------------
By default the Blog stores posts in the BROWSER (localStorage). That's fine
for testing, but if you clear the browser, posts are lost. To make posts
PERSIST and to make GOOGLE sign-in work, connect the Blog to Supabase.

It's free and takes ~10 minutes.

Step 1 — Create a Supabase project
  1. Go to https://supabase.com → New project.
  2. Name it (e.g. "senla-blog"), set a strong DB password, pick a region.
  3. Wait for it to provision (~1 min).

Step 2 — Create the posts table
  1. In the left sidebar click "SQL Editor" → "New query".
  2. Paste the SQL block from the Blog page's Setup panel (or below), Run.

    create table if not exists posts (
      id uuid primary key default gen_random_uuid(),
      title text not null,
      lead text,
      body text,
      cover text,
      category text,
      tags jsonb default '[]'::jsonb,
      author text,
      author_email text,
      author_avatar text,
      status text default 'draft',
      created_at timestamptz default now(),
      updated_at timestamptz default now()
    );
    alter table posts enable row level security;
    create policy "read published" on posts for select using (status = 'published');
    create policy "read own" on posts for select using (auth.jwt() ->> 'email' = author_email);
    create policy "insert own" on posts for insert with check (auth.jwt() ->> 'email' = author_email);
    create policy "update own" on posts for update using (auth.jwt() ->> 'email' = author_email);
    create policy "delete own" on posts for delete using (auth.jwt() ->> 'email' = author_email);

Step 3 — Enable Google sign-in
  1. In Google Cloud Console (https://console.cloud.google.com) →
     APIs & Services → Credentials → Create OAuth 2.0 Client ID
     (Application type: Web application).
  2. In Supabase: Authentication → Providers → Google → Enable.
     - Copy the "Redirect URL" Supabase shows you.
     - Paste it back into your Google OAuth client's "Authorized redirect URIs".
     - Paste your Google client ID + secret into Supabase, click Save.

Step 4 — Plug it into the Blog
  1. Open SENLA Blog.html in your browser.
  2. Click "Sign in" → "Open setup" (or click the orange banner at the top).
  3. From Supabase: Settings → API → copy Project URL and the anon public key.
  4. Paste them into the Setup panel.
  5. Add your team's emails to the "Author allow-list" so they get the Author role.
  6. Click "Save & connect".

That's it. Click "Continue with Google" and you'll be signed in. Anyone with an
allow-listed email becomes an Author and can write/publish posts. Posts are now
stored in your Supabase database.

Contact form
------------
The form on SENLA Contact.html currently opens the user's mail client
prefilled to info@senlaresearchhub.com. If you want it to send directly,
let me know and I can wire it through a service like Formspree or Supabase too.

Updating content
----------------
All files are plain HTML — open in any text editor (or the Claude project)
and change text directly. Team members, services, projects, and contact
details are all hand-editable.
