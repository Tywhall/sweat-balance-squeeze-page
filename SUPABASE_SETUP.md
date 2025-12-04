# Supabase Setup for Sweat Balance Waitlist

## 1. Create a Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up/login
2. Click "New Project"
3. Fill in project details:
   - **Name:** sweat-balance-waitlist
   - **Database Password:** (create a strong password)
   - **Region:** Choose closest to your users
4. Wait for project to be created (~2 minutes)

## 2. Create the Waitlist Table

Go to **SQL Editor** in your Supabase dashboard and run this query:

```sql
-- Create the waitlist table
CREATE TABLE waitlist (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    first_name TEXT NOT NULL,
    email TEXT NOT NULL UNIQUE,
    signed_up_at TIMESTAMPTZ DEFAULT NOW(),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Create an index on email for faster lookups
CREATE INDEX idx_waitlist_email ON waitlist(email);

-- Create an index on signed_up_at for sorting
CREATE INDEX idx_waitlist_signed_up_at ON waitlist(signed_up_at DESC);

-- Enable Row Level Security (RLS)
ALTER TABLE waitlist ENABLE ROW LEVEL SECURITY;

-- Create a policy that allows anonymous inserts (for the signup form)
CREATE POLICY "Allow anonymous inserts" ON waitlist
    FOR INSERT
    TO anon
    WITH CHECK (true);

-- Optional: Create a policy for authenticated users to read all entries
-- Uncomment if you want admin access to view signups
-- CREATE POLICY "Allow authenticated reads" ON waitlist
--     FOR SELECT
--     TO authenticated
--     USING (true);
```

## 3. Get Your API Credentials

1. Go to **Settings** → **API** in your Supabase dashboard
2. Copy:
   - **Project URL** (looks like: `https://xxxxx.supabase.co`)
   - **anon public** key (under "Project API keys")

## 4. Update the Squeeze Page

Open `index.html` and find these lines near the bottom:

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Replace with your actual credentials:

```javascript
const SUPABASE_URL = 'https://your-project-id.supabase.co';
const SUPABASE_ANON_KEY = 'your-anon-key-here';
```

## 5. Test the Form

1. Open `index.html` in a browser
2. Fill out the form and submit
3. Check your Supabase dashboard → **Table Editor** → **waitlist**
4. You should see the new entry with timestamp!

## 6. View Your Signups

In Supabase, go to **Table Editor** → **waitlist** to see all signups.

You can also run SQL queries:

```sql
-- Get all signups ordered by most recent
SELECT * FROM waitlist ORDER BY signed_up_at DESC;

-- Count total signups
SELECT COUNT(*) as total_signups FROM waitlist;

-- Get signups from the last 7 days
SELECT * FROM waitlist 
WHERE signed_up_at >= NOW() - INTERVAL '7 days'
ORDER BY signed_up_at DESC;
```

## 7. Export Signups (Optional)

To export your waitlist as CSV:
1. Go to **Table Editor** → **waitlist**
2. Click the "Export" button (download icon)
3. Select CSV format

---

## Troubleshooting

### "Permission denied" error
Make sure you ran the RLS policy in step 2 that allows anonymous inserts.

### "Duplicate key" error
This is expected when someone tries to sign up with an email that's already registered. The form handles this gracefully.

### Form not submitting
- Check browser console for errors
- Verify your Supabase URL and anon key are correct
- Make sure the table name is exactly `waitlist`

---

## Security Notes

- The `anon` key is safe to expose in frontend code - it only allows operations permitted by RLS policies
- We only allow INSERT operations for anonymous users
- Email uniqueness is enforced at the database level
- Consider adding rate limiting in production (Supabase has built-in options)
