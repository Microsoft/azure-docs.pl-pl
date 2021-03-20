---
title: Przywracanie za pomocą narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Zawiera Przewodnik dotyczący uruchamiania polecenia Restore narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632898"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Przywracanie za pomocą narzędzia migawek spójnej na platformie Azure (wersja zapoznawcza)

Ten artykuł zawiera Przewodnik uruchamiania polecenia Restore narzędzia migawek spójnej na poziomie aplikacji platformy Azure, którego można używać z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Przywracanie woluminu z migawki odbywa się przy użyciu `azacsnap -c restore` polecenia.

> [!IMPORTANT]
> Nie powoduje to przeprowadzenia odzyskiwania bazy danych, tylko przywracania woluminów zgodnie z opisem dla każdej z poniższych opcji.

## <a name="command-options"></a>Opcje polecenia

`-c restore`Polecenie ma następujące opcje:

- `--restore snaptovol` Tworzy nowy wolumin oparty na najnowszej migawce na woluminie docelowym. To polecenie tworzy nowy "sklonowany" wolumin oparty na skonfigurowanym woluminie docelowym przy użyciu najnowszej migawki woluminu jako podstawy do utworzenia nowego woluminu.  To polecenie nie przerywa replikacji magazynu od podstawowego do pomocniczego. Zamiast tego są tworzone klony najnowszej dostępnej migawki w lokacji odzyskiwania po awarii, a zalecane mountpoints systemu plików dla sklonowanych woluminów są prezentowane. To polecenie powinno być uruchamiane w systemie Azure Large instance system **w regionie** odzyskiwania po awarii (czyli w systemie docelowym w trybie failover).

- `--restore revertvolume` Przywraca wolumin docelowy do poprzedniego stanu w oparciu o najnowszą migawkę.  Użycie tego polecenia jako części trybu failover odzyskiwania po awarii w sparowanym regionie DR. To polecenie powoduje zatrzymanie replikacji magazynu z lokacji głównej do lokacji dodatkowej i przywrócenie docelowych woluminów DR do ich najnowszej dostępnej migawki na woluminach odzyskiwania po **awarii** oraz zalecanym mountpoints systemu plików dla przywróconych woluminów Dr. To polecenie powinno być uruchamiane w systemie Azure Large instance system **w regionie** odzyskiwania po awarii (czyli w systemie docelowym w trybie failover).
    > [!NOTE]
    > Polecenie sub ( `--restore revertvolume` ) jest dostępne tylko dla dużych wystąpień platformy Azure i nie jest dostępne dla Azure NetApp Files.
- `--hanasid <SAP HANA SID>` Czy SAP HANA identyfikator SID jest wybierany z pliku konfiguracji w celu zastosowania poleceń przywracania woluminu do programu.

- `[--configfile <config filename>]` jest opcjonalnym parametrem umożliwiającym stosowanie niestandardowych nazw plików konfiguracji.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Wykonaj test pracy awaryjnej odzyskiwania po awarii `azacsnap -c restore --restore snaptovol`

To polecenie przypomina "pełne" polecenie `--restore restorevolume` odzyskiwania po awarii (), ale zamiast przerywać replikację między lokacją główną a lokacją przywracania awaryjnego, wolumin klonu jest tworzony poza woluminami odzyskiwania po awarii, co pozwala na przywrócenie najnowszej migawki w lokacji Dr. Te sklonowane woluminy są następnie wykorzystywane przez klienta do testowania odzyskiwania po awarii bez konieczności wykonywania kompletnej pracy w trybie failover środowiska platformy HANA, które dzieli umowę replikacji między lokacją główną a lokacją odzyskiwania po awarii.

- W ten sposób można testować wiele różnych punktów przywracania, z których każdy ma własny punkt przywracania.
- Klon jest wyznaczany przez sygnaturę czasową podczas wykonywania polecenia i reprezentuje najnowsze dane oraz inną migawkę dostępną podczas uruchamiania.

> [!IMPORTANT]
> Ta operacja dotyczy tylko dużych wystąpień platformy Azure.
>
> - Wykonanie tego polecenia wymaga, aby kontaktowy adres e-mail w celu współdziałania z usługą przed usunięciem klonów po 4 tygodniach.
> - Każde wykonanie tego polecenia spowoduje utworzenie nowego klonu, który musi zostać usunięty przez operacje firmy Microsoft, gdy test zostanie zakończony.
> - Wszystkie utworzone woluminy klonowania zostaną automatycznie usunięte po 4 tygodniach.

Plik konfiguracji (na przykład `DR.json` ) powinien zawierać tylko woluminy Dr, a nie woluminy produkcyjne, w przeciwnym razie woluminy produkcyjne mogą mieć utworzone klony.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>Dane wyjściowe `azacsnap -c restore --restore snaptovol` polecenia (dla scenariusza Single-Node)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> Dane wyjściowe "Wyświetlanie punktów instalacji według woluminów" są różne dla różnych scenariuszy.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Wykonaj pełną tryb failover odzyskiwania po awarii `azacsnap -c restore --restore revertvolume`

To polecenie powoduje **zatrzymanie** replikacji magazynu z lokacji głównej do lokacji dodatkowej, przywrócenie najnowszej migawki na woluminach Dr i udostępnia mountpoints dla woluminów Dr.

To polecenie musi zostać wykonane na serwerze odzyskiwania po awarii przy użyciu pliku konfiguracji (na przykład `DR.json` ) tylko z woluminami Dr.

Przeprowadź przejście w tryb failover do lokacji DR, wykonując polecenie `azacsnap -c restore --restore revertvolume` .  To polecenie wymaga dodania identyfikatora SID jako parametru. Jest to identyfikator SID wystąpienia platformy HANA, który musi zostać odzyskany w lokacji DR.

> [!IMPORTANT]
> To polecenie można uruchomić tylko wtedy, gdy planujesz wykonać ćwiczenia odzyskiwania po awarii lub testy. To polecenie przerywa replikację. Aby ponownie włączyć replikację, należy skontaktować się z pomocą techniczną firmy Microsoft.

Na wysokim poziomie poniżej przedstawiono kroki wykonywania trybu failover odzyskiwania po awarii:

- Należy zamknąć wystąpienie platformy HANA w lokacji **głównej** . Ta akcja jest wymagana tylko wtedy, gdy przejdziesz do trybu failover w lokacji odzyskiwania po awarii, aby uniknąć niespójności danych.
- Zamknij wystąpienie HANA w węźle DR dla produkcyjnego identyfikatora SID.
- Wykonaj polecenie `azacsnap -c restore --restore revertvolume` w WĘŹLE Dr z identyfikatorem SID, który ma zostać odzyskany.
  - Polecenie przerywa link replikacji magazynu z podstawowego do lokacji odzyskiwania po awarii
  - Polecenie przywraca woluminy "dane" i "inne" jako skonfigurowane.  Zwykle ta operacja będzie dotyczyć woluminów dla systemów `/hana/data` i `/hana/logbackups` .  `/hana/shared`System plików nie został odzyskany, ale zamiast tego używa istniejącego `/hana/shared` identyfikatora SID w lokalizacji odzyskiwania po awarii.
  - Zainstaluj `/hana/data` `/hana/logbackups` woluminy i — upewnij się, że są one dodane do `/etc/fstab` pliku
- Przywróć migawkę programu HANA SYSTEMDB. W programie HANA Studio wyświetlana jest tylko najnowsza migawka platformy HANA dostępna w ramach migawki magazynu przywróconej jako część wykonywania polecenia migawki `azacsnap -c restore --restore revertvolume` .
- Odzyskaj bazę danych dzierżawy.
- Uruchom wystąpienie HANA w witrynie DR dla produkcyjnego identyfikatora SID (przykład: H80 w tym przypadku).
- Wykonaj dowolne testy bazy danych.

## <a name="next-steps"></a>Następne kroki

- [Utwórz kopię zapasową](azacsnap-cmd-ref-backup.md)
- [Pobierz szczegóły migawki](azacsnap-cmd-ref-details.md)
