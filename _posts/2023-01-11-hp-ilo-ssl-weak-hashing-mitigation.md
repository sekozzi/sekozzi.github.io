---
published: true
layout: post
title: Redfish RESTful api ile HP ILO üzerinde SSL yenileme
author_profile: true
categories:
  - ILO
---
HP ILO üzerinde Redfish RESTful api kullanarak ILO sertikasını silebiliriz. Bu operasyona ihtiyaç duyulmasının nedenlerinde biri ILO  sertifikasını **sha1** ile imzalanmış olması ve bundan dolayı **SSL Certificate Signed Using Weak Hashing Algorithm** açığının geliyor olması, bir diğeride sertifika süresinin bitmiş olmasıdır. 

ILO sertifikası silindiğinde ILO resetlenecek ve yeni sertifika oluşacaktır. Oluşan sertifikanın sha256 ile imzalandığı görülebilir. Eğer sertifika yenilenmesine rağmen, sertifika hala sha1 ile imzalı gözüküyor ise ILO firmware eski olabilir.

Aşağıdaki komutu ILO erişiminin olduğu bir yerden bash CLI üzerinden çalıştırırsanız ILO sertifikası yenilenecektir. 

```bash
curl -kX DELETE https://ilo_ipaddress/redfish/v1/Managers/1/SecurityService/HttpsCert/ -u user:pass
```

Linux içerisinde **hponcfg** ile bu işlem aşağıdaki script ile yapılabilir.

```bash
#!/bin/bash
hponcfg_check=$(rpm -qa|grep -c hponcfg)

if [ $hponcfg_check -eq 1 ]; then
    echo "hponcfg found"
    /sbin/hponcfg -w /tmp/iloconfig.txt
    ilo_ip_address=$(/bin/cat /tmp/iloconfig.txt|/bin/grep -w "IP_ADDRESS"|/bin/cut -d'"' -f2)
    /usr/bin/curl -kX DELETE https://$ilo_ip_address/redfish/v1/Managers/1/SecurityService/HttpsCert/ -u user:pass
    rm -rf /tmp/iloconfig.txt
    exit 0
else
    echo "Sunucu sanal veya HP degil"
    exit 0
fi
```
