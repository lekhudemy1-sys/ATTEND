# Attendance System (User + Admin)

## Features
- **User Page**: Select name → Mark Present (location restricted) or Mark Leave + reason
- **Admin Page**: Password protected (`1234`) → View attendance, edit time, add new employees
- Uses **Supabase** as database

---

## 1. Supabase Setup (Do this first)

1. Go to [https://supabase.com](https://supabase.com) and create a free project.
2. Go to **SQL Editor** → New query → Paste and run the following:

```sql
-- Employees table
CREATE TABLE employees (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL UNIQUE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Attendance table
CREATE TABLE attendance (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  employee_id UUID REFERENCES employees(id) ON DELETE CASCADE,
  employee_name TEXT NOT NULL,
  date DATE NOT NULL,
  time TIME NOT NULL,
  status TEXT NOT NULL CHECK (status IN ('present', 'leave')),
  reason TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Allow public access (for simplicity - improve later with auth)
ALTER TABLE employees ENABLE ROW LEVEL SECURITY;
ALTER TABLE attendance ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow all on employees" ON employees FOR ALL USING (true) WITH CHECK (true);
CREATE POLICY "Allow all on attendance" ON attendance FOR ALL USING (true) WITH CHECK (true);
```

3. Go to **Project Settings → API**
   - Copy your **Project URL**
   - Copy your **anon public** key

4. Open both `user.html` and `admin.html`
   - Replace `YOUR_SUPABASE_URL` with your Project URL
   - Replace `YOUR_SUPABASE_ANON_KEY` with your anon key

---

## 2. Location Settings

In `user.html` find these two lines and change them to your office location:

```js
const OFFICE_LAT = 28.6139;   // ← Change this
const OFFICE_LNG = 77.2090;   // ← Change this
const MAX_DISTANCE = 100;     // meters (you can increase if needed)
```

You can get your exact coordinates from Google Maps (right-click → coordinates).

---

## 3. How to use

- Open `user.html` → Employees select their name and mark attendance
- Open `admin.html` → Enter password `1234` → Manage everything

---

## Notes
- Present is **only allowed** when the user is within the set location.
- Leave can be marked from **anywhere**.
- Time is taken from the user's device (for simplicity). For production you can use server time.
