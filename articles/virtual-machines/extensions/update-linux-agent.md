---
title: Aktualizowanie agenta systemu Azure Linux z usługi GitHub
description: Dowiedz się, jak zaktualizować usługę Azure Linux Agent dla maszyny Wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: e3b2819b33feba52c3b02f0e2104d4106bd04cbb
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770056"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak zaktualizować agenta systemu Azure Linux na maszynie Wirtualnej

Aby zaktualizować [agenta systemu Azure linux](https://github.com/Azure/WALinuxAgent) na maszynie Wirtualnej systemu Linux na platformie Azure, musisz już mieć:

- Uruchomiona maszyna wirtualna z systemem Linux na platformie Azure.
- Połączenie z maszyną wirtualną systemu Linux przy użyciu SSH.

Zawsze należy najpierw sprawdzić pakiet w repozytorium dystrybucji Linuksa. Możliwe, że dostępny pakiet może nie być najnowszą wersją, jednak włączenie autoupdate zapewni, że Agent Systemu Linux zawsze otrzyma najnowszą aktualizację. Jeśli masz problemy z instalacją z menedżerów pakietów, należy zwrócić się o pomoc od dostawcy dystrybucji.

> ! [UWAGA] Aby uzyskać więcej informacji, zobacz [Zatwierdzone dystrybucje linuksa na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimalna obsługa agenta maszyny wirtualnej na platformie Azure
Przed kontynuowaniem sprawdź [obsługę minimalnej wersji dla agentów maszyn wirtualnych na platformie Azure.](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="updating-the-azure-linux-agent"></a>Aktualizowanie agenta systemu Azure linux

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aktualizacja pamięci podręcznej pakietu

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Ponowne uruchamianie usługi waagent

#### <a name="restart-agent-for-1404"></a>Agent ponownego uruchamiania dla 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Agent ponownego uruchamiania dla 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Czerwony kapelusz / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Ponowne uruchamianie usługi waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Ponowne uruchamianie usługi waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

Powyższe dane wyjściowe pokażą, czy pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Ponowne uruchamianie usługi waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

W danych wyjściowych z powyższego, to pokaże, czy pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Ponowne uruchamianie usługi waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizacja pamięci podręcznej pakietu

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Włącz automatyczną aktualizację agenta
Ta wersja Debiana nie ma wersji >= 2.0.16, dlatego AutoUpdate nie jest dla niej dostępna. Dane wyjściowe z powyższego polecenia pokażą, czy pakiet jest aktualny.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Sprawdź aktualną wersję pakietu

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizacja pamięci podręcznej pakietu

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona
Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 i Oracle Linux 7

W przypadku systemu Oracle `Addons` Linux upewnij się, że repozytorium jest włączone. Wybierz, aby `/etc/yum.repos.d/public-yum-ol6.repo`edytować plik (Oracle `/etc/yum.repos.d/public-yum-ol7.repo`Linux 6) lub `enabled=0` `enabled=1` (Oracle Linux) i zmienić wiersz na pod **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

Następnie, aby zainstalować najnowszą wersję agenta systemu Azure Linux, należy wpisać:

```bash
sudo yum install WALinuxAgent
```

Jeśli nie znajdziesz repozytorium dodatków, możesz po prostu dodać te wiersze na końcu pliku .repo zgodnie z wydaniem Oracle Linux:

Dla maszyn wirtualnych Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Dla maszyn wirtualnych Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Następnie wpisz:

```bash
sudo yum update WALinuxAgent
```

Zazwyczaj jest to wszystko, czego potrzebujesz, ale jeśli z https://github.com jakiegoś powodu musisz zainstalować go bezpośrednio, skorzystaj z poniższych kroków.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizowanie agenta systemu Linux, gdy nie istnieje żaden pakiet agenta dla dystrybucji

Zainstaluj wget (istnieją pewne dystrybucje, które nie instalują go domyślnie, takie jak Red Hat, CentOS i Oracle Linux `sudo yum install wget` w wersjach 6.4 i 6.5), wpisując w wierszu polecenia.

### <a name="1-download-the-latest-version"></a>1. Pobierz najnowszą wersję
Otwórz [wersję usługi Azure Linux Agent w usłudze GitHub](https://github.com/Azure/WALinuxAgent/releases) na stronie sieci Web i dowiedz się, czy jest to najnowszy numer wersji. (Bieżącą wersję można zlokalizować, wpisując `waagent --version`polecenie .)

#### <a name="for-version-22x-or-later-type"></a>W przypadku wersji 2.2.x lub nowszej wpisz:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

W poniższej linii użyto na przykład wersji 2.2.0:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Zainstaluj agenta systemu Azure Linux

#### <a name="for-version-22x-use"></a>W przypadku wersji 2.2.x należy użyć:
Może być konieczne zainstalowanie pakietu `setuptools` po raz pierwszy - zobacz [tutaj](https://pypi.python.org/pypi/setuptools). Następnie uruchom polecenie:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że automatyczna aktualizacja jest włączona

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź "AutoUpdate.Enabled". Jeśli widzisz to dane wyjściowe, jest włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć uruchamianie:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Ponowne uruchomienie usługi waagent
Dla większości dystrybucji Linuksa:

```bash
sudo service waagent restart
```

Dla Ubuntu, użyj:

```bash
sudo service walinuxagent restart
```

W przypadku CoreOS użyj:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Potwierdź wersję programu Azure Linux Agent
    
```bash
waagent -version
```

W przypadku systemu CoreOS powyższe polecenie może nie działać.

Zobaczysz, że wersja usługi Azure Linux Agent została zaktualizowana do nowej wersji.

Aby uzyskać więcej informacji dotyczących agenta systemu Azure Linux, zobacz [program README agenta systemu Azure Linux .](https://github.com/Azure/WALinuxAgent)
