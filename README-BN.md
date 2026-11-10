# ঘুষ সাইড রাজবাড়ী — কাস্টম ওয়েব অ্যাপ

এই স্টার্টার প্রজেক্টে রয়েছে:
- মোবাইল-ফ্রেন্ডলি বাংলা পাবলিক ওয়েবসাইট
- পরিচয় না চেয়ে অভিযোগ জমা দেওয়ার ফরম
- Supabase-এ ব্যক্তিগত অভিযোগ সংরক্ষণ
- অ্যাডমিন লগইন ও অভিযোগ যাচাইয়ের স্ট্যাটাস
- উপজেলা অনুযায়ী সমষ্টিগত অভিযোগ সংখ্যা
- Vercel-এ ডেপ্লয় করার উপযোগী Next.js অ্যাপ

## ১) যা লাগবে
- GitHub অ্যাকাউন্ট: https://github.com/
- Supabase অ্যাকাউন্ট: https://supabase.com/
- Vercel অ্যাকাউন্ট: https://vercel.com/
সবগুলোতেই প্রাথমিকভাবে বিনামূল্যের স্তর আছে; ব্যবহারসীমা/শর্ত পরিবর্তিত হতে পারে।

## ২) Supabase সেটআপ
1. Supabase-এ নতুন project তৈরি করুন।
2. Project Settings → API থেকে Project URL, anon/public key এবং service_role key নিন।
3. SQL Editor-এ `supabase/schema.sql` ফাইলের SQL চালান।
4. Authentication → Providers-এ Email provider চালু রাখুন।
5. Authentication → Users থেকে নিজের অ্যাডমিন ইউজার তৈরি করুন।
6. SQL Editor-এ নিচের কমান্ড চালিয়ে ওই ইউজারকে অ্যাডমিন করুন (USER_UUID বদলাবেন):

```sql
update auth.users
set raw_app_meta_data = coalesce(raw_app_meta_data, '{}'::jsonb) || '{"role":"admin"}'::jsonb
where id = 'USER_UUID';
```

অ্যাডমিনের role কখনো user-editable metadata-তে রাখবেন না। `app_metadata` ব্যবহার করতে হবে।

## ৩) লোকাল কম্পিউটারে চালানো
Node.js LTS ইনস্টল করুন: https://nodejs.org/

প্রজেক্ট ফোল্ডারে টার্মিনাল খুলে চালান:

```bash
npm install
```

`.env.example` কপি করে `.env.local` নামে রাখুন এবং Supabase-এর তথ্য বসান:

```env
NEXT_PUBLIC_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=YOUR_ANON_KEY
SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
```

তারপর:

```bash
npm run dev
```

ব্রাউজারে http://localhost:3000 খুলুন।

## ৪) GitHub ও Vercel-এ প্রকাশ
1. এই ফোল্ডারটি GitHub-এ নতুন repository-তে আপলোড করুন।
2. Vercel-এ GitHub দিয়ে লগইন করে Add New → Project নির্বাচন করুন।
3. repository Import করে Deploy দিন।
4. Vercel Project → Settings → Environment Variables-এ `.env.local`-এর তিনটি variable যোগ করুন।
5. Redeploy করুন।
6. Vercel থেকে পাওয়া `https://....vercel.app` ঠিকানায় সাইট খুলুন।

## ৫) গুরুত্বপূর্ণ নিরাপত্তা ও প্রকাশনীতি
- `SUPABASE_SERVICE_ROLE_KEY` কখনো `NEXT_PUBLIC_` নামে রাখবেন না, GitHub-এ দেবেন না, কারও সঙ্গে শেয়ার করবেন না।
- অভিযোগের মূল টেবিল public করা যাবে না। RLS policy কেবল app_metadata-তে role=admin থাকা ব্যবহারকারীকে পড়া/স্ট্যাটাস বদলানোর অনুমতি দেয়।
- বর্তমান নমুনা ফরমে ফাইল/ছবি আপলোড নেই। প্রমাণ সংগ্রহ দরকার হলে আলাদা নিরাপদ ব্যবস্থা ও retention policy তৈরি করুন।
- এই নমুনা API-তে production-grade CAPTCHA/rate limiting এখনো যুক্ত নেই। জনসাধারণের জন্য চালুর আগে Cloudflare Turnstile বা সমমানের CAPTCHA, IP-ভিত্তিক rate limit, abuse monitoring ও backup যোগ করুন।
- ওয়েব সার্ভার/হোস্টিং/Google/Supabase-এর প্রযুক্তিগত লগে IP বা অন্যান্য metadata থাকতে পারে। তাই “শতভাগ পরিচয় অজানা” বলে প্রচার করবেন না।
- অভিযোগ মানেই প্রমাণিত অপরাধ নয়। অভিযোগ যাচাই, সংশ্লিষ্ট পক্ষের বক্তব্য নেওয়া, নথি সংরক্ষণ, মানহানি/গোপনীয়তা আইন মেনে চলা এবং সম্পাদকের অনুমোদন ছাড়া কিছু প্রকাশ করবেন না।
- অভিযোগে জাতীয় পরিচয়পত্র, ফোন, ঠিকানা, পাসওয়ার্ড বা অপ্রয়োজনীয় ব্যক্তিগত তথ্য চাইবেন না।
- প্রকাশ্য পরিসংখ্যানও আপাতত সব জমা অভিযোগ গণনা করে। ভুয়া/স্প্যাম জমা আলাদা করে বাদ দিতে চাইলে dashboard-এ শুধু যাচাইকৃত গণনা দেখানোর ব্যবস্থা করতে হবে।

## ৬) বর্তমান সীমাবদ্ধতা
এটি একটি কার্যকরী starter codebase, কিন্তু আপনার Supabase/Vercel অ্যাকাউন্টে deploy করা হয়নি। অ্যাকাউন্টের key, অ্যাডমিন user এবং deploy অনুমতি আপনি নিজে সেট করবেন। Production চালুর আগে security review এবং বাস্তব ডিভাইসে পরীক্ষা আবশ্যক।
