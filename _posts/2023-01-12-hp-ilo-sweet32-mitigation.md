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
 
  #!/bin/bash
  ##case sensitive ignore ediliyor
  shopt -s nocasematch

  ##sunucu fiziksel mi sanal mi kontrol ediliyor
  case $(systemd-detect-virt) in
    none)
      echo "Physical Server"

      ##uretici firma HPE mi degil mi kontrol ediliyor
      case $(dmidecode -s system-manufacturer) in
        HPE)
          echo "Brand:" $(dmidecode -s system-manufacturer)

          ##firma HPE ise hponcfg komutu isletilebilir mi kontrol yapiliyor
          ##komut var ise /tmp/ENFORCE_AES_VALUE.xml olusuturuluyor
          ##daha onceden var ise silinip tekrar olusturuluyor
          if ! command -v hponcfg &> /dev/null
          then
             echo "hponcfg could not be found"
             exit
          else
             echo "hponcfg found"
             echo "creating xml file"
             rm -rf /tmp/ENFORCE_AES_VALUE.xml
             touch /tmp/ENFORCE_AES_VALUE.xml
             echo "<RIBCL VERSION=\"2.0\">" >>/tmp/ENFORCE_AES_VALUE.xml
             echo "  <LOGIN USER_LOGIN=\"USERNAME\" PASSWORD=\"PASSWORD\">" >>/tmp/ENFORCE_AES_VALUE.xml
             echo "<RIB_INFO mode=\"write\"><MOD_GLOBAL_SETTINGS>" >>/tmp/ENFORCE_AES_VALUE.xml
             echo "    <ENFORCE_AES VALUE=\"Y\"/>"  >>/tmp/ENFORCE_AES_VALUE.xml
             echo "</MOD_GLOBAL_SETTINGS></RIB_INFO>" >>/tmp/ENFORCE_AES_VALUE.xml
             echo "</LOGIN>" >>/tmp/ENFORCE_AES_VALUE.xml
             echo "</RIBCL>" >>/tmp/ENFORCE_AES_VALUE.xml
             /sbin/hponcfg -f /tmp/ENFORCE_AES_VALUE.xml
             RESULT=$?
             if [ $RESULT -eq 0 ]; then
               echo "ENFORCE_AES_VALUE.xml file uploaded successfully"
             else
               echo "ENFORCE_AES_VALUE.xml file upload failed"
             fi 
          exit
          fi

          ;;
        ##üretici firma HPE degilse cikiliyor
        *)
          echo "Brand:" $(dmidecode -s system-manufacturer)
          ;;
      esac
      ;;
    *)
      echo "Virtual Server"
      echo "Brand: " $(dmidecode -s system-manufacturer)
      ;;
  esac