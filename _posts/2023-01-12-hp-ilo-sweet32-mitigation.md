---
published: true
layout: post
author_profile: true
categories:
  - ILO
title: HP ILO SWEET32 SSL açığının kapatılması
---
HP ILO üzerindeki SWEET32 açığının kapatılması için Linux sunucularda aşağıdaki script çalıştırılabilir. Açığın kapatılabilmesi için ILO üzerinden **Security -> Encryption Settings** kısmından Enforce AES/3DES Encryption Enabled seçilmesi gerekiyor.

Script hponcfg ile beraber ILO'ya hazırladığımız config'i geçerek ILO'yu resetleyecektir.
 
