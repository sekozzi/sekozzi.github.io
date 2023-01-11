---
published: true
layout: post
title: "HP ILO SSL Certificate Signed Using Weak Hashing Algorithm açığının kapatılması"
---
HP ILO üzerinde **SSL Certificate Signed Using Weak Hashing Algorithm** açığını kapatmak için aşağıdaki komutu ILO erişiminin olduğu bir yerden bash CLI üzerinden çalıştırırsanız ILO sertifikası yenilenecektir. 


> curl -kX DELETE https://ilo_ipaddress/redfish/v1/Managers/1/SecurityService/HttpsCert/ -u user:pass


Linux içerisinde **hponcfg** ile bu işlem aşağıdaki script ile yapılabilir.


{% highlight bash %}
#!/bin/bash
hponcfg_check=$(rpm -qa|grep -c hponcfg)

if [ $hponcfg_check -eq 1 ]; then
    echo "hponcfg found"
    /sbin/hponcfg -w /tmp/iloconfig.txt
    ilo_ip_address=$(/bin/cat /tmp/iloconfig.txt|/bin/grep -w "IP_ADDRESS"|/bin/cut -d'"' -f2)
    /usr/bin/curl -kX DELETE https://$ilo_ip_address/redfish/v1/Managers/1/SecurityService/HttpsCert/ -u UNIXADM:Lnx4235Ux
    rm -rf /tmp/iloconfig.txt
    exit 0
else
    echo "Sunucu sanal veya HP degil"
    exit 0
fi
{% endhighlight %}