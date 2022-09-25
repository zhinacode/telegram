# اتصال آزاد به تلگرام

شما به دو سرور  احتیاج خواهید داشت. یکی از سرور ها داخل ایران (اینترانت ملی) قرار خواهد داشت و دیگری در خارج از ایران. شما از طریق دیتای موبایل می‌توانید به اینترانت ملی دسترسی داشته باشید ولی به اینترنت خیر. سرور های داخل ایرانی می‌توانند به اینترنت محدود شده دسترسی داشته باشند ولی به اینترنت جهانی خیر. راه حل؟


1. درخواست خود را از طریق دیتای موبایل به یک سرور داخل ایرانی میفرستیم.
2. درخواست ما از سرور ایرانی به سرور خارجی تغییر مسیر میدهد.
3. درخواست ما از سرور خارجی به مقصد میرسد و پاسخ تا گوشی موبایل شما برمیگردد.


## چگونه؟

دو سرور VPS خریداری کنید. یکی از آنها باید ایرانی و دیگری خارجی باشد.

به سرور ایرانی SSH بزنید:

```
ssh root@[آیپی سرور ایرانی]
```

حال از داخل سرور ایرانی، به سرور خارجی SSH بزنید:

```
ssh root@[آیپی سرور خارجی]
```

با اجرای خط به خط دستورات زیر، نرم افزار Dante را نصب کنید:

```
apt update
apt install dante-server
```

حال این نرم افزار را کانفیگ کنید:

```
tee <<EOF >/dev/null /etc/danted.conf
internal: 0.0.0.0 port = 9090
external: [کارت شبکه در حال استفاده]

clientmethod: none
socksmethod: none

client pass {
  from: 0.0.0.0/0 port 1-65535 to: 0.0.0.0/0
}

socks pass {
  from: 0.0.0.0/0 port 1-65535 to: 0.0.0.0/0 port 1-65535
  proxyprotocol: socks_v5
}
EOF
```

سرویس را اجرا کنید:

```
systemctl enable danted
systemctl restart danted
```

حال از سرور خارجی، با فشردن دکمه های `Ctrl + D`، خارج شوید. به سرور ایرانی برمیگردید.

نرم افزار Dante را درست مانند سرور خارجی، در سرور ایرانی نیز نصب کنید:

```
apt update
apt install dante-server
```

آن را کانفیگ کنید:

```
tee <<EOF >/dev/null /etc/danted.conf
internal: 0.0.0.0 port = 9090
external: [کارت شبکه در حال استفاده]

clientmethod: none
socksmethod: none

client pass {
  from: 0.0.0.0/0 port 1-65535 to: 0.0.0.0/0
}

socks pass {
  from: 0.0.0.0/0 port 1-65535 to: 0.0.0.0/0 port 1-65535
  proxyprotocol: socks_v5
}

route {
  from: 0.0.0.0/0 port 1-65535 to: 0.0.0.0/0 port 1-65535 via: [آیپی سرور خارجی] port = 9090
  proxyprotocol: socks_v5
}
EOF
```

***توجه داشته باشید که باید بجای `[آیپی سرور خارجی]` باید آدرس آیپی سرور خارجی را قرار دهید!***
*** بجای کارت شبکه در حال استفاده از دستور زیر برای یافتن آن استفاده کنید ***
‍‍‍```
ip a
```

*** مثال خروجی دستور بالا ***


‍‍```
root@your_host:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:50:56:06:d1:d0 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.1/24 brd 10.0.0.1 scope global eth0
       valid_lft forever preferred_lft forever
```
*** در بالا کارت شیکه ی در حال استفاده = eth0 ***

سرویس را اجرا کنید:

```
systemctl enable danted
systemctl restart danted
```

حالا می‌توانید در تلگرام خود از این پروکسی Socks5 استفاده کنید.

آیپی همان آیپی سرور ایرانی است.
پورت: ۹۰۹۰

اگر می‌توانید این راهنما را بهبود دهید، تغییرات خود را از طریق Pull-Request پذیرا هستم!


***❤️به امید ایرانی آزاد❤️***
