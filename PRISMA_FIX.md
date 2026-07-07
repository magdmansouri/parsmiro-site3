# رفع قطعی خطای Prisma (gregorianDate / Unknown argument)

این خطا با همون فیلدهای قدیمی (name/email/phone/date/message) دوباره برگشته. این یعنی یکی از این اتفاق‌ها افتاده:
- فایل `prisma/schema.prisma` واقعاً عوض نشده (شاید موقع extract کردن zip، یه پوشه‌ی جدید جدا ساخته شده به‌جای overwrite کردن فایل توی پوشه‌ی پروژه)
- یا `npx prisma generate` رو اجرا کردی ولی قبلش فایل رو عوض نکرده بودی
- یا Prisma Client قدیمی هنوز توی `node_modules` کش شده

## مرحله‌به‌مرحله، این بار با حذف کامل کش‌ها

### ۱. سرور دev رو کامل ببند
توی ترمینالی که `npm run dev` توش اجراست، `Ctrl+C` بزن. مطمئن شو واقعاً بسته شده (یه بار دیگه enter بزن، نباید چیزی پرینت بشه).

### ۲. محتوای schema.prisma رو دستی چک/جایگزین کن
فایل `prisma/schema.prisma` رو با یه ادیتور (مثل VS Code یا Notepad) باز کن. **هرچی توشه پاک کن** و این رو کامل جایگزینش کن:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

enum BookingStatus {
  PENDING
  CONFIRMED
  CANCELLED
}

model Booking {
  id            String        @id @default(cuid())
  fullName      String
  phone         String
  packageId     String
  jalaliDate    String
  gregorianDate DateTime
  time          String
  note          String?
  status        BookingStatus @default(PENDING)
  createdAt     DateTime      @default(now())

  @@index([gregorianDate])
  @@index([status])
}
```

فایل رو Save کن.

### ۳. کش‌های Prisma و Next.js رو کامل پاک کن
توی ترمینال، داخل پوشه‌ی پروژه (`cd C:\Users\user1\Desktop\parsmiro-site`):

```bash
rmdir /s /q .next
rmdir /s /q node_modules\.prisma
```

(اگه PowerShell داری به‌جاش: `Remove-Item -Recurse -Force .next, node_modules\.prisma`)

### ۴. دیتابیس رو با schema جدید sync کن
```bash
npx prisma db push
```
این دستور باید خروجی موفقیت‌آمیز بده و بگه جدول Booking آپدیت شد. اگه خطا داد (مثلاً «database is locked»)، یعنی هنوز یه پردازش دیگه (مثل دev سرور یا یه ادیتور دیتابیس مثل DB Browser for SQLite) فایل `dev.db` رو باز نگه داشته — همه‌شون رو ببند و دوباره امتحان کن.

### ۵. Prisma Client رو از نو بساز
```bash
npx prisma generate
```
این دستور باید بنویسه چیزی شبیه «Generated Prisma Client». اگه خطا داد، دقیقاً متن خطا رو برام بفرست.

### ۶. دev سرور رو دوباره اجرا کن
```bash
npm run dev
```

### ۷. تست نهایی
برو `/booking` و یه رزرو امتحان کن.

---

**اگه بازم همین خطا اومد**، یعنی یه چیز دیگه داره schema رو override می‌کنه (مثلاً یه فیلد `"prisma"` توی `package.json` که به مسیر دیگه‌ای اشاره می‌کنه). توی همین حالت لطفاً این دو تا رو برام بفرست:
1. محتوای کامل فایل `prisma/schema.prisma`
2. خروجی کامل دستور `npx prisma generate` توی ترمینال

