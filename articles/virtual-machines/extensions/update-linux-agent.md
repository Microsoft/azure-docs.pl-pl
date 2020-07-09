---
title: Aktualizowanie agenta systemu Linux platformy Azure z usługi GitHub
description: Dowiedz się, jak zaktualizować agenta systemu Linux dla maszyny wirtualnej z systemem Linux na platformie Azure
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
ms.openlocfilehash: ab846eeb09c4f3d2db71abf58ef5d55dc74962a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82112052"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Jak zaktualizować agenta systemu Linux dla platformy Azure na maszynie wirtualnej

Aby zaktualizować [agenta systemu Azure Linux](https://github.com/Azure/WALinuxAgent) na maszynie wirtualnej z systemem Linux na platformie Azure, musisz mieć:

- Uruchomiona maszyna wirtualna z systemem Linux na platformie Azure.
- Połączenie z tą maszyną wirtualną z systemem Linux przy użyciu protokołu SSH.

Należy zawsze sprawdzać pakiet w repozytorium dystrybucji systemu Linux. Być może dostępny jest pakiet, który nie jest najnowszą wersją, jednak włączenie funkcji autoaktualizacje zapewni, że Agent systemu Linux zawsze pobierze najnowszą aktualizację. W przypadku problemów z instalacją z menedżerów pakietów należy zasięgnąć pomocy technicznej od dostawcy dystrybucji.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [rozpowszechniane dystrybucje systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimalna obsługa agenta maszyny wirtualnej na platformie Azure
Przed kontynuowaniem sprawdź [minimalną obsługę wersji agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) .

## <a name="updating-the-azure-linux-agent"></a>Aktualizowanie agenta systemu Azure Linux

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Aktualizowanie pamięci podręcznej pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

#### <a name="restart-agent-for-1404"></a>Uruchom ponownie agenta dla 14,04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Uruchom ponownie agenta dla 16,04/17,04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

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

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

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

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 Z DODATKIEM SP4

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

Powyższe dane wyjściowe będą wyświetlane, jeśli pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 Z DODATKIEM SP2

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Sprawdź dostępne aktualizacje

W danych wyjściowych z powyższego poziomu spowoduje to wyświetlenie tego, czy pakiet jest aktualny.

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona 

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Uruchom ponownie usługę waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "Rozciągnij"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizowanie pamięci podręcznej pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Włącz autoaktualizację agenta
Ta wersja programu Debian nie ma wersji >= 2.0.16, dlatego program AutoUpdate nie jest dostępny dla tego elementu. Dane wyjściowe powyższego polecenia pokazują, czy pakiet jest aktualny.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Rozciągnij"

#### <a name="check-your-current-package-version"></a>Sprawdź bieżącą wersję pakietu

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Aktualizowanie pamięci podręcznej pakietów

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Zainstaluj najnowszą wersję pakietu

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona
Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 i Oracle Linux 7

W przypadku Oracle Linux upewnij się, że `Addons` repozytorium jest włączone. Wybierz, aby edytować plik `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux), a następnie zmień wiersz `enabled=0` na `enabled=1` **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

Następnie, aby zainstalować najnowszą wersję agenta systemu Azure Linux, wpisz:

```bash
sudo yum install WALinuxAgent
```

Jeśli nie znajdziesz repozytorium dodatków, możesz po prostu dodać te wiersze na końcu pliku. Repository zgodnie z wersją Oracle Linux:

W przypadku maszyn wirtualnych Oracle Linux 6:

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

Zwykle jest to potrzebne, ale jeśli z jakiegoś powodu należy koniecznie zainstalować ją https://github.com bezpośrednio, wykonaj następujące czynności.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualizacja agenta systemu Linux, gdy żaden pakiet agenta nie istnieje na potrzeby dystrybucji

Zainstaluj Wget (istnieje kilka dystrybucje, które nie instalują go domyślnie, takich jak Red Hat, CentOS i Oracle Linux wersje 6,4 i 6,5), wpisując `sudo yum install wget` w wierszu polecenia.

### <a name="1-download-the-latest-version"></a>1. Pobierz najnowszą wersję
Otwórz wersję [agenta systemu Linux platformy Azure w usłudze GitHub](https://github.com/Azure/WALinuxAgent/releases) na stronie sieci Web i sprawdź numer najnowszej wersji. (Możesz zlokalizować bieżącą wersję, wpisując `waagent --version` .)

#### <a name="for-version-22x-or-later-type"></a>W przypadku wersji 2.2. x lub nowszej wpisz:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Poniższy wiersz używa wersji 2.2.0 jako przykładu:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Zainstaluj agenta systemu Linux platformy Azure

#### <a name="for-version-22x-use"></a>W przypadku wersji 2.2. x Użyj:
Może być konieczne zainstalowanie pakietu `setuptools` najpierw — zobacz [tutaj](https://pypi.python.org/pypi/setuptools). Następnie uruchom polecenie:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Upewnij się, że funkcja autoaktualizacji jest włączona

Najpierw sprawdź, czy jest włączona:

```bash
cat /etc/waagent.conf
```

Znajdź element "AutoUpdate. Enabled". Jeśli zobaczysz te dane wyjściowe, jest ono włączone:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Aby włączyć przebieg:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Uruchom ponownie usługę waagent
W przypadku większości dystrybucje systemu Linux:

```bash
sudo service waagent restart
```

W przypadku Ubuntu Użyj:

```bash
sudo service walinuxagent restart
```

W przypadku CoreOS Użyj:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Potwierdź wersję agenta systemu Linux platformy Azure
    
```bash
waagent -version
```

W przypadku CoreOS powyższe polecenie może nie zadziałało.

Zobaczysz, że wersja agenta systemu Linux platformy Azure została zaktualizowana do nowej wersji.

Aby uzyskać więcej informacji na temat agenta systemu Azure Linux, zobacz [plik Readme agenta platformy Azure dla systemu Linux](https://github.com/Azure/WALinuxAgent).
