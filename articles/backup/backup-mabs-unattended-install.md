---
title: Instalacja dyskretna Azure Backup Server v2
description: Użyj skryptu programu PowerShell, aby zainstalować w trybie dyskretnym Azure Backup Server v2. Ten rodzaj instalacji jest również nazywany instalacją nienadzorowaną.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74172240"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Uruchom instalację nienadzorowaną Azure Backup Server

Dowiedz się, jak uruchomić nienadzorowaną instalację programu Azure Backup Server.

Te kroki nie mają zastosowania, Jeśli instalujesz Azure Backup Server v1.

## <a name="install-backup-server"></a>Zainstaluj serwer kopii zapasowej

1. Na serwerze, który jest hostem Azure Backup Server v2 lub nowszym, Utwórz plik tekstowy. (Plik można utworzyć w Notatniku lub w innym edytorze tekstu). Zapisz plik jako MABSSetup.ini.

2. Wklej następujący kod w pliku MABSSetup.ini. Zastąp tekst w nawiasach ( \< \> ) wartościami z Twojego środowiska. Następujący tekst jest przykładem:

   ```text
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Zapisz plik. Następnie w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze instalacji wprowadź następujące polecenie:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Możesz użyć tych flag do instalacji:</br>
**/f**:. ini ścieżka pliku</br>
**/l**: ścieżka dziennika</br>
**/i**: ścieżka instalacji</br>
**/x**: ścieżka odinstalowania</br>

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu serwera kopii zapasowej należy dowiedzieć się, jak przygotować serwer lub rozpocząć ochronę obciążeń.

- [Przygotowywanie obciążeń serwera kopii zapasowej](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu serwera kopii zapasowej](backup-azure-backup-server-vmware.md)
- [Użyj serwera kopii zapasowej, aby utworzyć kopię zapasową SQL Server](backup-azure-sql-mabs.md)
- [Dodawanie Nowoczesny magazyn kopii zapasowych do serwera zapasowego](backup-mabs-add-storage.md)
