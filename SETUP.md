# Setup guide — lowfuzion booking site

## What changed in this update
- Rebranded to **lowfuzion** with the tagline **"based on earth"**.
- Entire site is now in **English**, except the `/booking` page, which stays **Persian/RTL** as requested.
- Hero video now displays correctly even when the source clip is **vertical/portrait**: the real video is centered at its natural aspect ratio, with a blurred, scaled-up copy of the same video filling the rest of the frame (a standard cinematic technique).
- Added a serif display font (Cormorant Garamond) for English headings and a sans body font (Inter), paired with the existing Vazirmatn font, which is now scoped only to the booking page.

## 1. Copy the files
Drop everything from `lowfuzion-files.zip` into your project root, overwriting files with the same name. Changed/new files in this update:

```
app/layout.tsx            ← new fonts, lang="en" dir="ltr", new metadata
app/globals.css           ← new font tokens, .font-fa utility for the booking page
app/page.tsx              ← English copy, fixed vertical-video hero
app/gallery/page.tsx      ← English
app/about/page.tsx        ← English
app/booking/page.tsx      ← wrapped in dir="rtl" / font-fa (content itself unchanged, still Persian)
app/admin/login/page.tsx  ← English
app/admin/login/actions.ts ← English error messages
app/admin/dashboard/page.tsx ← English
components/Navbar.tsx     ← English, lowfuzion branding
components/Footer.tsx     ← English, lowfuzion branding
components/BookingForm.tsx ← updated to use the new Persian-specific package fields
data/packages.ts          ← now has separate English (*En) and Persian (*Fa) fields per package
data/gallery.ts           ← English categories
lib/session.ts            ← English internal error message
```

`components/JalaliCalendar.tsx` is unchanged from the last fix — no need to re-copy it if you already have the working version, but it's harmless to overwrite again.

## 2. Your video and photo assets
Same two locations as before, just a reminder of what each one is:

```
public/videos/hero.mp4          ← homepage hero video (vertical is fine now)
public/videos/hero-poster.jpg   ← shown only for a split second before the video starts playing
public/images/booking-hero.jpg  ← the banner image at the top of the /booking page — this is a SEPARATE file from hero-poster.jpg
```

If you want the same photo you sent me to also appear as the booking page banner, just save a copy of it as `public/images/booking-hero.jpg` too.

## 3. Run it
```bash
npm run dev
```

- `/` — English homepage, vertical video hero
- `/gallery` — English, category filters (All / Portrait / Wedding / Brand)
- `/booking` — Persian/RTL, unchanged behavior, just visually isolated from the rest of the English site
- `/about` — English
- `/admin/login` → `/admin/dashboard` — English

No database or `.env` changes were needed for this update.
