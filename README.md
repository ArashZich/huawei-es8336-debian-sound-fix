# 🎧 راهنمای رفع مشکل صدای چیپ صوتی ES8336 در Debian

## 🖥 مشخصات سیستم

- **مدل لپ‌تاپ**: Huawei BDZ-WXX9
- **پردازنده**: Intel Core i5-1135G7 (نسل یازدهم)
- **کارت گرافیک**: Intel Xe Graphics (TGL GT2)
- **سیستم‌عامل**: Debian GNU/Linux
- **چیپ صوتی**: Everest Semiconductor ES8336

## 🧩 مشکل

پس از نصب Debian، صدایی از اسپیکرهای داخلی لپ‌تاپ پخش نمی‌شود. در حالی که سیستم کارت صوتی را شناسایی می‌کند، اما در حالت «SUSPENDED» قرار دارد و خروجی صدا فعال نیست.

## ✅ راه‌حل‌ها

### 1. بررسی و تنظیم اولیه با ابزارهای صوتی

#### 1.1 استفاده از `alsamixer`

در ترمینال دستور زیر را وارد کنید:

```bash
alsamixer
```

سپس با فشردن کلید `F6`، کارت صوتی `sof-essx8336` را انتخاب کرده و مطمئن شوید که گزینه‌های مربوط به اسپیکر و هدفون فعال و بدون بی‌صدا (unmute) هستند. برای بی‌صدا کردن یا فعال‌سازی، از کلید `M` استفاده کنید.

#### 1.2 بررسی وضعیت خروجی‌های صوتی با `pactl`

برای مشاهده لیست خروجی‌های صوتی، دستور زیر را اجرا کنید:

```bash
pactl list sinks
```

اطمینان حاصل کنید که خروجی مربوط به اسپیکرهای داخلی در حالت فعال (RUNNING) قرار دارد و به درستی پیکربندی شده است.

#### 1.3 راه‌اندازی مجدد سرویس‌های صوتی

برای راه‌اندازی مجدد سرویس‌های مربوط به PipeWire، دستورات زیر را وارد کنید:

```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```

سپس وضعیت سرویس‌ها را با دستورات زیر بررسی کنید:

```bash
systemctl --user status pipewire
systemctl --user status pipewire-pulse
systemctl --user status wireplumber
```

#### 1.4 نصب و استفاده از `pavucontrol`

برای مدیریت تنظیمات صوتی به صورت گرافیکی، بسته‌ی `pavucontrol` را نصب کنید:

```bash
sudo apt install pavucontrol
```

سپس برنامه را اجرا کرده و در تب‌های مختلف، خروجی صوتی مناسب را انتخاب و تنظیمات لازم را اعمال کنید.

#### 1.5 نصب پشتیبانی از PipeWire

در صورتی که PipeWire به درستی نصب نشده است، بسته‌ی `pipewire-audio` را نصب کنید:

```bash
sudo apt install pipewire-audio
```

### 2. استفاده از مخزن `sof-essx8336-debian-fix`

اگر روش‌های بالا مؤثر نبودند، می‌توانید از مخزن [gnickm/sof-essx8336-debian-fix](https://github.com/gnickm/sof-essx8336-debian-fix) استفاده کنید که شامل اسکریپت‌هایی برای تنظیمات لازم است.

#### مراحل نصب:

1. نصب پیش‌نیازها:

   ```bash
   sudo apt update
   sudo apt install git rsync
   ```

2. کلون کردن مخزن:

   ```bash
   git clone https://github.com/gnickm/sof-essx8336-debian-fix.git
   cd sof-essx8336-debian-fix
   ```

3. نصب کرنل جدید (در صورت نیاز):

   ```bash
   sudo dpkg -i dist/linux*
   ```

4. نصب فریم‌ور و توپولوژی‌های SOF:

   ```bash
   sudo ./setup-sof.sh
   ```

5. راه‌اندازی مجدد سیستم:

   ```bash
   sudo reboot
   ```

6. تنظیمات ALSA:

   ```bash
   ./setup-alsa.sh
   ```

   توجه: ممکن است نیاز باشد این اسکریپت را پس از هر بار راه‌اندازی سیستم اجرا کنید.

7. تنظیم PulseAudio (در صورت استفاده):

   ```bash
   sudo ./setup-pulseaudio.sh
   ```

## 📝 نتیجه

با انجام مراحل بالا، صدای اسپیکرهای داخلی لپ‌تاپ فعال می‌شود و می‌توانید از آن استفاده کنید. در صورت نیاز، می‌توانید این راهنما را در مخزن گیت‌هاب خود منتشر کنید تا دیگران نیز از آن بهره‌مند شوند.

## 🔗 منابع

- مخزن اصلی: [gnickm/sof-essx8336-debian-fix](https://github.com/gnickm/sof-essx8336-debian-fix)