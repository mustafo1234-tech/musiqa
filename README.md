# 🎵 Telegram musiqa topuvchi bot

YouTube'dan ashula qidiradigan, natijalarni sahifalab ko'rsatadigan va tanlangan
ashulani mp3 formatda yuboradigan Telegram bot.

## Texnologiyalar

- Python 3.11+
- aiogram 3.x
- yt-dlp
- PostgreSQL + asyncpg
- python-dotenv
- ffmpeg (mp3 ga aylantirish uchun)

## Papka tuzilishi

```text
alone bot/
├── main.py              # botning asosiy kodi
├── .env                 # maxfiy sozlamalar (Git'ga qo'shilmaydi)
├── .env.example         # .env uchun namuna
├── requirements.txt     # kerakli kutubxonalar
├── README.md            # qo'llanma
├── ffmpeg/
│   └── bin/             # ffmpeg.exe va ffprobe.exe (loyiha ichida)
└── downloads/           # vaqtinchalik audio fayllar (avtomatik yaratiladi)
```

## 1. Python o'rnatish

1. https://www.python.org/downloads/ saytidan Python 3.11 yoki undan yangiroq
   versiyani yuklab oling.
2. O'rnatishda **"Add python.exe to PATH"** belgisini albatta qo'ying.
3. Terminalni qayta ochib tekshiring:

```powershell
python --version
```

## 2. Virtual muhit yaratish

Loyiha papkasida terminal oching va quyidagilarni bajaring:

```powershell
python -m venv venv
venv\Scripts\activate
python -m pip install --upgrade pip
```

Terminal satri boshida `(venv)` ko'rinsa, virtual muhit faol.

## 3. Kutubxonalarni o'rnatish

```powershell
pip install -r requirements.txt
```

## 4. PostgreSQL tayyorlash

1. PostgreSQL'ni o'rnating (masalan, https://www.postgresql.org/download/windows/).
2. O'rnatishda kiritgan `postgres` foydalanuvchisining parolini eslab qoling.
3. Ma'lumotlar bazasini yarating. `pgAdmin` yoki `psql` orqali:

```sql
CREATE DATABASE music_bot;
```

4. Kerakli jadvallar bot birinchi marta ishga tushganda **avtomatik**
   yaratiladi (`CREATE TABLE IF NOT EXISTS`).

`.env` faylidagi baza sozlamalari shu ma'lumotlarga mos bo'lishi kerak:

```env
DB_HOST=localhost
DB_PORT=5432
DB_NAME=music_bot
DB_USER=postgres
DB_PASSWORD=postgres_parolingiz
```

## 5. ffmpeg o'rnatish (Windows)

`yt-dlp` audioni mp3 ga aylantirishi uchun `ffmpeg` va `ffprobe` kerak.

> Ushbu loyihada `ffmpeg\bin\ffmpeg.exe` va `ffmpeg\bin\ffprobe.exe` allaqachon
> mavjud. Bot avval PATH'dagi ffmpeg'ni, topilmasa loyiha ichidagi
> `ffmpeg\bin\ffmpeg.exe` ni avtomatik ishlatadi. Shu sabab PATH ga qo'shish
> shart emas. Tekshirish:
>
> ```powershell
> .\ffmpeg\bin\ffmpeg.exe -version
> .\ffmpeg\bin\ffprobe.exe -version
> ```

### 1-usul: qo'lda o'rnatish

1. https://www.gyan.dev/ffmpeg/builds/ sahifasidan
   **ffmpeg-release-essentials.zip** faylini yuklab oling.
2. ZIP faylni `C:\ffmpeg` papkasiga oching. Ichida `bin` papkasi bo'lishi kerak:
   `C:\ffmpeg\bin\ffmpeg.exe`.
3. Windows'da **Start** tugmasini bosib, `Environment Variables` deb qidiring va
   **"Edit the system environment variables"** ni oching.
4. **Environment Variables...** → `Path` → **Edit** → **New** tugmasini bosing.
5. `C:\ffmpeg\bin` manzilini qo'shing va barcha oynalarda **OK** ni bosing.
6. Terminalni **yopib, qaytadan oching** va tekshiring:

```powershell
ffmpeg -version
ffprobe -version
```

Ikkala buyruq ham versiya chiqarsa, hammasi to'g'ri.

### 2-usul: winget orqali

```powershell
winget install Gyan.FFmpeg
```

O'rnatgandan so'ng terminalni yopib, qaytadan oching va `ffmpeg -version` bilan
tekshiring.

> Agar `ffmpeg` topilmasa, bot qidiruvni bajaradi, lekin audioni mp3 ga
> aylantira olmaydi va foydalanuvchiga tushunarli xabar yuboradi.

## 6. Bot tokenini olish

1. Telegram'da **@BotFather** ga yozing.
2. `/newbot` buyrug'i bilan yangi bot yarating yoki mavjud bot uchun
   `/token` buyrug'idan foydalaning.
3. Olingan tokenni `.env` faylidagi `BOT_TOKEN` ga yozing.

Agar `.env` fayli hali yo'q bo'lsa:

```powershell
Copy-Item .env.example .env
```

So'ng `.env` faylini ochib to'ldiring:

```env
BOT_TOKEN=123456789:AA...sizning_tokeningiz
DB_HOST=localhost
DB_PORT=5432
DB_NAME=music_bot
DB_USER=postgres
DB_PASSWORD=postgres_parolingiz
```

> `.env` faylini hech kimga yubormang va Git'ga qo'shmang. U maxfiy ma'lumot.

## 7. Botni ishga tushirish

Virtual muhit faol bo'lgan holda loyiha papkasida:

```powershell
python main.py
```

Konsolda quyidagilarga o'xshash yozuvlar chiqadi:

```text
PostgreSQL ga muvaffaqiyatli ulandi.
Bot ishga tushdi: @sizning_bot_username
To'xtatish uchun Ctrl+C bosing.
```

To'xtatish uchun terminalda **Ctrl+C** bosing.

## Botdan foydalanish

1. Botga `/start` yuboring.
2. Bot salomlashib, ashula nomini so'raydi.
3. Ashula nomini yozing, masalan: `fairy tail`.
4. Bot 5–10 ta natijani ko'rsatadi. Har bir natija uchun raqamli inline tugma
   bor. Pastda **⬅️ Oldingi / Keyingi ➡️** sahifalash tugmalari mavjud.
5. Raqamli tugmani bosing. Bot `⏳ Yuklanmoqda...` xabarini ko'rsatib, audioni
   mp3 formatda yuboradi.
6. Audio yuborilgach bot yana ashula nomini so'raydi.

## Bot qanday ishlaydi

- YouTube qidiruvi `yt-dlp` orqali `ytsearch` bilan bajariladi.
- `yt-dlp` bloklovchi kutubxona bo'lgani uchun u `asyncio.to_thread` ichida
  ishlaydi; bot boshqa foydalanuvchilarga ham javob bera oladi.
- Natijalar foydalanuvchining FSM holatida saqlanadi, shuning uchun tugma
  bosilganda to'g'ri ashula topiladi.
- 10 daqiqadan uzun videolar qidiruv natijalaridan filtrlanadi.
- Yuklab olingan fayl `downloads/` papkasiga tushadi va yuborilgandan keyin
  avtomatik o'chiriladi.
- 50 MB dan katta fayllar yuborilmaydi (Telegram Bot API chegarasi).
- Har bir foydalanuvchi, qidiruv va yuklash PostgreSQL bazasiga yoziladi.
- Spam bo'lmasligi uchun oddiy throttling qo'llanilgan: qidiruvlar orasida 3
  soniya, yuklashlar orasida 5 soniya.
- Xatoliklar konsolga log qilinadi va foydalanuvchiga o'zbek tilida tushunarli
  xabar ko'rsatiladi.

## Bazadagi jadvallar

Bot ishga tushganda quyidagi jadvallar avtomatik yaratiladi:

- `users` — `id`, `telegram_id`, `full_name`, `username`, `created_at`
- `searches` — `id`, `user_id`, `query`, `created_at`
- `downloads` — `id`, `user_id`, `video_id`, `title`, `created_at`

## Tez-tez uchraydigan xatoliklar

### `BOT_TOKEN topilmadi`

`.env` fayli loyiha papkasida borligini va `BOT_TOKEN` to'ldirilganini
tekshiring.

### `PostgreSQL ga ulanishda xatolik`

- PostgreSQL xizmati ishlayotganini tekshiring.
- `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` qiymatlari to'g'ri
  ekanini tekshiring.
- `DB_NAME` da ko'rsatilgan baza mavjudligini tekshiring.
- Bot baza ulanmasa ham ishlaydi, faqat statistika yozilmaydi.

### `ffmpeg topilmadi`

Avval `.\ffmpeg\bin\ffmpeg.exe -version` buyrug'ini tekshiring. Loyiha ichidagi
fayl mavjud bo'lsa, bot uni avtomatik ishlatadi. Topilmasa, 5-bo'limdagi
yo'riqnoma bo'yicha ffmpeg'ni o'rnating yoki `ffmpeg\bin\` papkasiga qo'ying.

### `Hech narsa topilmadi`

Ashula nomini boshqacha yozib ko'ring. YouTube'da mavjud bo'lmagan yoki yopiq
videolar topilmasligi mumkin.

### `Bu ashula 50 MB dan katta`

Telegram botlar 50 MB dan katta fayl yubora olmaydi. Boshqa, qisqaroq versiyani
tanlang yoki videoni qisqartirib yuklang.

### Bot javob bermayapti

- Terminalda xatoliklar chiqmayotganini tekshiring.
- Internet aloqasi ishlayotganini tekshiring.
- `BOT_TOKEN` to'g'riligini tekshiring.
- Botni faqat bitta nusxada ishga tushirganingizga ishonch hosil qiling (bir
  vaqtda ikkita `python main.py` ishlamasin).

## Eslatmalar

- Botdan shaxsiy chatda foydalanish tavsiya etiladi.
- YouTube ba'zi videolarni mintaqa yoki yosh chegarasi bilan cheklashi mumkin.
- Botni ishlab chiqishda YouTube va kontent mualliflarining foydalanish
  shartlariga rioya qiling.
