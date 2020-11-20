---
title: Kopia zapasowa i przywracanie systemu operacyjnego SAP HANA na platformie Azure (duże wystąpienia) Typ II SKU | Microsoft Docs
description: Wykonaj kopię zapasową i Przywróć systemu operacyjnego dla SAP HANA na platformie Azure (duże wystąpienia) Typ II SKU
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e690f6abc9c597cc1facb7b3e59604bb711cf274
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955671"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Kopia zapasowa i przywracanie systemu operacyjnego dla jednostek SKU typu II poprawki 3

W tym dokumencie opisano kroki tworzenia kopii zapasowej i przywracania na poziomie pliku systemu operacyjnego dla **jednostek SKU typu II** o dużych wystąpieniach poprawki 3. 

>[!Important]
> **Ten artykuł nie ma zastosowania do wdrożeń jednostek SKU typu II w programie Revisions 4 HANA duże wystąpienia.** Można utworzyć kopię zapasową rozruchowych jednostek LUN typu o dużej liczbie wystąpień programu HANA, które są wdrażane w wersji 2


>[!NOTE]
>Skrypty kopii zapasowej systemu operacyjnego korzystają z oprogramowania tylnego, które jest wstępnie zainstalowane na serwerze.  

Po zakończeniu aprowizacji przez zespół firmy Microsoft `Service Management` serwer jest domyślnie skonfigurowany przy użyciu dwóch harmonogramów tworzenia kopii zapasowych, aby utworzyć kopię zapasową na poziomie systemu plików z tyłu systemu operacyjnego. Harmonogramy zadań tworzenia kopii zapasowej można sprawdzić za pomocą następującego polecenia:
```
#crontab –l
```
Harmonogram tworzenia kopii zapasowych można zmienić w dowolnym momencie za pomocą następującego polecenia:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak wykonać ręczną kopię zapasową?

Kopia zapasowa systemu plików OS jest zaplanowana przy użyciu **zadania firmy CRONUS** . Można jednak ręcznie wykonać kopię zapasową na poziomie pliku systemu operacyjnego. Aby ręcznie wykonać kopię zapasową, uruchom następujące polecenie:

```
#rear -v mkbackup
```
Poniższy ekran pokazuje przykład ręcznego tworzenia kopii zapasowej:

![Jaka](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak przywrócić kopię zapasową?

Można przywrócić pełną kopię zapasową lub pojedynczy plik z kopii zapasowej. Aby przywrócić, użyj następującego polecenia:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po przywróceniu plik zostanie odzyskany w bieżącym katalogu roboczym.

Następujące polecenie pokazuje przywracanie pliku */etc/fstabfrom* kopii zapasowej pliku *. tar. gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Należy skopiować plik do odpowiedniej lokalizacji po jego przywróceniu z kopii zapasowej.

Poniższy zrzut ekranu przedstawia przywracanie kompletnej kopii zapasowej:

![Zrzut ekranu przedstawia okno wiersza polecenia z przywróceniem.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak zainstalować narzędzie tylne i zmienić konfigurację? 

Pakiety "Osłabij i Odzyskaj" są **wstępnie zainstalowane** w jednostkach **SKU typu II** dużych wystąpień Hana i nie są wymagane żadne akcje. Możesz bezpośrednio rozpocząć korzystanie z tyłu dla kopii zapasowej systemu operacyjnego.
Jednak w sytuacjach, w których konieczne jest zainstalowanie własnych pakietów, można wykonać wymienione kroki, aby zainstalować i skonfigurować narzędzie tylne.

Aby zainstalować pakiety kopii zapasowych z **tyłu** , użyj następujących poleceń:

W przypadku systemu operacyjnego **SLES** Użyj następującego polecenia:
```
#zypper install <rear rpm package>
```
W przypadku systemu operacyjnego **RHEL** Użyj następującego polecenia: 
```
#yum install rear -y
```
Aby skonfigurować narzędzie tylne, należy zaktualizować parametry **OUTPUT_URL**  i **BACKUP_URL**  w *pliku/etc/Rear/Local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Poniższy zrzut ekranu przedstawia przywracanie kompletnej kopii zapasowej: ![ zrzut ekranu przedstawia okno wiersza polecenia z przywracaniem za pomocą narzędzia tylne.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
