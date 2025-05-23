# راهنمای رفع مشکل صدا در کارت صوتی ES8336 در لینوکس (Debian/Ubuntu)

در صورتی که پس از نصب لینوکس (مانند Debian یا Ubuntu) روی لپ‌تاپی که کارت صوتی آن از نوع ES8336 است، با مشکل صدا مواجه هستید، این راهنما می‌تواند به شما کمک کند تا مشکل را شناسایی و رفع کنید.

---

## بررسی اولیه سرویس‌های صوتی

ابتدا مطمئن شوید که سرویس‌های مربوط به PipeWire به درستی فعال هستند:

```bash
systemctl --user status pipewire
systemctl --user status pipewire-pulse
systemctl --user status wireplumber
```

در صورت نیاز برای فعال‌سازی مجدد:

```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```

---

## بررسی میکسر صدا با `alsamixer`

در ترمینال دستور زیر را وارد کنید:

```bash
alsamixer
```

سپس با فشردن کلید `F6`، کارت صوتی `sof-essx8336` را انتخاب کنید. در اینجا، مطمئن شوید که گزینه‌های مربوط به بلندگو (مانند `Speaker`, `Headphone`, `DAC`) بی‌صدا (mute) نیستند و حجم صدا در سطح مناسبی تنظیم شده است.

- برای تغییر وضعیت بی‌صدا: کلید `M`
- برای افزایش یا کاهش حجم صدا: کلیدهای جهت‌دار چپ و راست

---

## بررسی تنظیمات با `pavucontrol`

اگر `pavucontrol` را نصب نکرده‌اید:

```bash
sudo apt install pavucontrol
```

سپس با اجرای دستور زیر، رابط گرافیکی کنترل صدا را باز کنید:

```bash
pavucontrol
```

در تب‌های مختلف (مانند `Playback`, `Output Devices`, `Configuration`) بررسی کنید که:

- خروجی صدا بر روی بلندگوهای داخلی تنظیم شده باشد.
- پروفایل صوتی مناسب انتخاب شده باشد.

---

## استفاده از اسکریپت رفع مشکل در GitHub

اگر هنوز صدا کار نمی‌کند، می‌توانید از اسکریپت آماده زیر استفاده کنید:

```bash
git clone https://github.com/gnickm/sof-essx8336-debian-fix.git
cd sof-essx8336-debian-fix
sudo ./setup-alsa.sh
```

این اسکریپت پیکربندی لازم برای کارت صوتی ES8336 را اعمال می‌کند.

---

## افزودن گزینه به فایل تنظیمات ALSA

در برخی موارد، افزودن گزینه زیر به فایل تنظیمات ALSA نیز مشکل را حل می‌کند:

فایل را ویرایش کنید:

```bash
sudo nano /etc/modprobe.d/alsa-base.conf
```

و این خط را به انتهای فایل اضافه کنید:

```bash
options snd_soc_sof_es8336 quirk=0x02
```

سپس سیستم را ریستارت کنید:

```bash
sudo reboot
```

---

## بررسی دستگاه‌های پخش با `pactl`

برای اطمینان از شناسایی کارت صوتی توسط PipeWire:

```bash
pactl list sinks
```

اطمینان حاصل کنید که دستگاهی مانند `alsa_output.pci-0000_00_1f.3-platform-sof-essx8336.stereo-fallback` وجود دارد و وضعیت آن `RUNNING` باشد.

---

## بررسی خروجی `aplay`

در صورت باقی ماندن مشکل:

```bash
aplay -l
```

خروجی این دستور اطلاعات دقیق‌تری از وضعیت سخت‌افزار صوتی به شما می‌دهد.

---

اگر با انجام تمام این مراحل هنوز مشکل دارید، می‌توانید در انجمن‌های لینوکسی یا GitHub پروژه مربوطه موضوع را مطرح کنید و اطلاعات `pactl`, `aplay`, و `dmesg` را نیز ضمیمه کنید.

---

**لپ‌تاپ تست‌شده:**
- برند: Huawei MateBook D15
- کارت صوتی: ES8336
- توزیع: Debian 12

با طی مراحل بالا، مشکل صدا به‌طور کامل برطرف شد.

---

پیشنهاد برای نام ریپوی گیت‌هاب: `es8336-linux-audio-fix`

