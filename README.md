# 🤖 بوت تليجرام للصوت والفيديو والـ PDF

بوت شخصي بيشتغل على **Google Colab** وبيستخدم **Local Telegram Bot API Server** عشان يتعامل مع ملفات لحد **2GB**.
الكود على GitHub، والتشغيل من notebook صغير وثابت بيحمّل آخر Release.

## واجهة الاستخدام
- **لوحة رئيسية دايمة بأزرار حقيقية ملونة** تحت الشات: 🎵 صوت • 🎬 فيديو • 📄 PDF • ⬇️ تحميل • ✏️ إعادة تسمية • 🏷️ Metadata • 📊 الحالة • ❓ القدرات • ⚙️ إعدادات.
- ابعت **أي ملف** ← كارت ببياناته (الحجم، الكودك، bitrate، المدة، الأبعاد، الصفحات…) + أزرار inline مناسبة لنوعه.
- **لغة الألوان:** 🟦 أزرق = الأساسي/المقترح • 🟩 أخضر = ابدأ/تأكيد/مفعّل • 🟥 أحمر = إلغاء/إغلاق.
- أول ما البوت يشتغل يبعتلك رسالة **"✅ البوت اشتغل"** فيها الحالة والأدوات وقدراته، ولو رجع بعد انقطاع بيقولك وبيبلّغك بالمهام اللي ضاعت.
- الأزرار والقدرات متولّدة من `core/registry.py`: **مفيش زرار بيظهر لوظيفة مش شغّالة**.

> الألوان (`style`) متاحة من Bot API 9.4 وتظهر في تطبيقات تليجرام الحديثة؛ على النسخ القديمة بتظهر الأزرار بشكلها العادي.

## حالة المراحل
| المرحلة | المحتوى | الحالة |
|---|---|---|
| 1 | الأساس: whitelist، السيرفر المحلي، قايمة، بيانات الملف، **إعادة التسمية**، طابور وإلغاء وتنظيف، notebook | ✅ جاهزة |
| 2 | الصوت: تحويل، قص، تقسيم `(part1)`، دمج، تنقية (FFmpeg/AI) | ✅ جاهزة |
| 3 | الفيديو: قص (سريع/دقيق)، دمج، ضغط (+GPU/NVENC)، فصل الصوت، تغيير الصيغة | ✅ جاهزة |
| 4 | PDF: ضغط، استخراج صفحات، OCR عربي، دمج (+Word)، تقسيم، PDF↔صور، تدوير/حذف/علامة مائية/حماية، Word/PPTX→PDF | ✅ جاهزة |
| 5 | تحميل من روابط (yt-dlp) + تحديث yt-dlp + cookies | ✅ جاهزة |
| 6 | Metadata: عرض (قديم/جديد)، تعديل، حذف، غلاف | ✅ جاهزة |
| 7 | لمسات: اقتراحات ذكية، إعدادات افتراضية لكل مستخدم (⚙️ إعدادات) | ✅ جاهزة |

## المتطلبات والـ Secrets
| الاسم | مطلوب؟ | الوصف |
|---|---|---|
| `BOT_TOKEN` | نعم | من @BotFather |
| `API_ID` / `API_HASH` | نعم | my.telegram.org ← API development tools |
| `ALLOWED_USER_IDS` | نعم | `123456789,987654321` — لو فاضي البوت **يرفض يشتغل**. رقمك من @userinfobot أو `/id` |
| `YT_COOKIES` | اختياري | محتوى cookies.txt لو يوتيوب حظر الـ IP  |
| `GITHUB_TOKEN` | اختياري | لو الريبو private |

لازم **تفعّل Notebook access** لكل Secret. الأسرار مبتدخلش الكود ولا GitHub ولا اللوج (بتتخفى بـ `***`).

## النشر (خطوة بخطوة)
**أ) تجهيز:** خد التوكن، `api_id/api_hash`، رقمك. اعمل ريبو **public**.

**ب) رفع الكود:** فك الـ zip وارفع **محتوياته** للريبو. فولدر `.github` مخفي وبيضيع كتير — الأضمن:
1. Add file ← Create new file ← اكتب `.github/workflows/build.yml` (الـ `/` بتعمل الفولدرات) ← الصق محتوى `build.yml` ← Commit على main.
2. تبويب **Actions** ← **build-release** ← **Run workflow** ← اكتب tag مثل `v0.1.0` (أو اعمل tag من الـ git).
3. استنى (البناء 20-40 دقيقة لأنه بيبني الـ binary لنسختين Ubuntu) وتأكد في **Releases** من: `bot.zip` + `telegram-bot-api-ubuntu-22.04` + `…-24.04` + ملفات `.sha256`.

لو الـ workflow مش ظاهر: الملف لازم في `.github/workflows/` على **main**، وActions مش مقفولة (Settings ← Actions ← General ← Allow all actions)، و**Workflow permissions = Read and write**، ومفيش خطأ مسافات YAML.

**ج) على Colab:** ارفع `tgbot_colab.ipynb` ← غيّر `REPO` ← **Run all** ← اسمح بالـ Secrets والـ Drive ← ابعت `/start` للبوت.

**د) التحديث:** عدّل في الريبو ← tag جديد ← Release جديد ← أعد تشغيل الـ notebook. **من غير ما تلمس الـ notebook.**

## عن `logOut`
البوت لازم يخرج من سيرفر تليجرام الرسمي قبل المحلي. الـ notebook بيعملها مرة واحدة ويحفظ علامة. حسب التوثيق الرسمي تقدر تشتغل على المحلي **فورًا**؛ الانتظار 10 دقايق خاص بالرجوع للسيرفر الرسمي. ولو رجعت له بعد كده لازم `logOut` من المحلي.

## حل المشاكل
- **الـ Run all وقف عند الأسرار:** فعّل Notebook access لكل Secret.
- **يوتيوب محظور :** ضيف `YT_COOKIES`. المواقع التانية غالبًا تمام.
- **GPU مش ظاهر:** Runtime ← Change runtime type. وNVENC بيتختبر فعليًا وقت التشغيل.
- **الرسالة "ملف كبير":** معناها البوت مش على السيرفر المحلي — شغّل خلية 8.
- **الجلسة اتقفلت:** شغّل Run all تاني؛ البوت هيبلّغك إنه رجع وبالمهام اللي ضاعت.
- **المساحة خلصت:** التنظيف تلقائي (كروت الملفات بتنتهي بعد 90 دقيقة، وأسرع لو المساحة قليلة).
- **Build مش بيظهر في Actions:** شوف نقط (ب) فوق.
- **مفيش رسالة "اشتغلت":** لازم ترسل `/start` للبوت مرة على الأقل.

- **LibreOffice (Word/PPTX→PDF) والـ DeepFilterNet:** بيتثبّتوا لأول مرة وقت الاستخدام (كام دقيقة)، وبعدها بيفضلوا لحد ما الجلسة تقفل.
- **الإعدادات الافتراضية:** من ⚙️ إعدادات (صيغة/bitrate الصوت، دقة وكودك الفيديو، preset الـ PDF، لغة OCR، GPU).

## ⚠️ تنبيه Colab
استخدام Colab لاستضافة خدمة/ميديا ممكن يخالف سياسته أو يتقطع. الاستخدام الشخصي المحدود غالبًا بيعدي، لكن الجلسات بتنتهي (idle أو بعد ساعات) ومفيش ضمان استمرار. البوت مصمّم يتعافى ويبلّغك، مش يمنع الانقطاع.

## الأوامر
`/start` `/menu` `/id` `/cancel` `/help`

## الهيكل
```
main.py  bootstrap.py  config.py  requirements.txt  VERSION
core/      server, state, queue, progress, probe, cleanup, boot, registry, smart, sender, assets, app, utils
handlers/  start, menu, file_router, rename, settings, tools (محرك الأدوات), audio, video, pdf, ytdlp, metadata, fallback
services/  audio, video, pdf, ocr, convert, ytdlp, metadata
ui/        keyboards (كل الأزرار)  texts (كل النصوص)
scripts/   build_binary.sh  healthcheck.py
tests/     smoke_test.py  tools_test.py   (python tests/smoke_test.py && python tests/tools_test.py)
.github/workflows/build.yml
```
