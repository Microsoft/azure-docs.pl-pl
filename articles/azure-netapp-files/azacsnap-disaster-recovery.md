---
title: Odzyskiwanie po awarii za pomocą narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Wyjaśnia, jak przeprowadzić odzyskiwanie awaryjne w przypadku korzystania z narzędzia migawek spójnych w aplikacji Azure, którego można używać z Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730874"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Odzyskiwanie po awarii za pomocą narzędzia migawek spójnej na platformie Azure (wersja zapoznawcza)

W tym artykule wyjaśniono, jak przeprowadzić odzyskiwanie awaryjne w przypadku korzystania z narzędzia migawek spójnych w aplikacji Azure, którego można używać z Azure NetApp Files.

> [!IMPORTANT]
> Ta operacja dotyczy tylko **dużych wystąpień platformy Azure** .

## <a name="introduction"></a>Wprowadzenie

Platforma Azure Large instance może również mieć skonfigurowaną lokację odzyskiwania po awarii, w której można zreplikować woluminy woluminu magazynu.  Jeśli migawki zostały prawidłowo skonfigurowane z taką konfiguracją, można wykonać odzyskiwanie awaryjne w tej lokacji.  Ten dokument ma stanowić Przewodnik po przeprowadzeniu odzyskiwania po awarii dla tego Instalatora.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Wymagania wstępne dotyczące konfiguracji odzyskiwania po awarii

Przed zaplanowaniem trybu failover odzyskiwania po awarii należy spełnić następujące wymagania wstępne.

- Masz węzeł DR zainicjowany w witrynie DR. Dostępne są dwie opcje odzyskiwania po awarii. Jednym z nich jest normalne DR, a inne to Multipurpose DR.
- Działa replikacja magazynu. Zespół operacyjny firmy Microsoft przeprowadza konfigurację replikacji magazynu w chwili automatycznego inicjowania obsługi programu DR. Replikację magazynu można monitorować przy użyciu polecenia `azacsnap -c details --details replication` w witrynie odzyskiwania po awarii.
- W lokalizacji podstawowej skonfigurowano i skonfigurowano migawki magazynu.
- W witrynie odzyskiwania po awarii jest zainstalowane wystąpienie platformy HANA z tym samym identyfikatorem SID co wystąpieniem podstawowym.
- Przeczytaj i rozumiemy procedurę trybu failover odzyskiwania po awarii opisaną w [SAP HANA — duże wystąpienia wysokiej dostępności i odzyskiwaniu danych na platformie Azure](../virtual-machines/workloads/sap/hana-failover-procedure.md)
- W lokalizacji DR skonfigurowano i skonfigurowano migawki magazynu.
- Plik konfiguracji (na przykład `DR.json` ) został utworzony przy użyciu woluminów magazynu Dr i skojarzonych informacji na serwerze odzyskiwania po awarii.
- Kroki w witrynie DR zostały wykonane w celu:
  - Włącz komunikację z magazynem.
  - Włącz komunikację z SAP HANA.

## <a name="set-up-disaster-recovery"></a>Konfigurowanie odzyskiwania po awarii

Firma Microsoft obsługuje replikację na poziomie magazynu na potrzeby odzyskiwania po awarii. Istnieją dwa sposoby konfigurowania odzyskiwania po awarii.

Jest to **normalne** , a inne to **Multipurpose**. W przypadku **normalnego** odzyskiwania po awarii można korzystać z dedykowanego wystąpienia w lokalizacji Dr w celu przełączenia w tryb failover. W scenariuszu **Multipurpose** Dr można korzystać z innego wystąpienia usług pytań i odpowiedzi na rozwój Ale zainstalowano również wstępnie zainstalowane wystąpienie platformy HANA, które jest w stanie uśpienia i ma ten sam identyfikator SID co wystąpienie platformy HANA, które ma zostać przełączone w tryb failover do tej jednostki platformy HANA. Firma Microsoft konfiguruje środowisko dla użytkownika, w tym replikację magazynu na podstawie danych wejściowych wprowadzonych w formularzu żądania obsługi (SRF) w momencie dołączania.

> [!IMPORTANT]
> Upewnij się, że spełniono wszystkie wymagania wstępne dotyczące instalacji programu DR.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Monitoruj replikację danych z lokacji podstawowej do usługi DR

Zespół operacyjny firmy Microsoft już zarządza łączem DR z lokacji głównej do lokacji DR i monitoruje go.
Replikację danych z serwera podstawowego na serwer DR można monitorować przy użyciu polecenia Snapshot `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>Przeprowadź przejście w tryb failover do lokacji DR

Uruchom polecenie trybu failover w witrynie DR ( `azacsnap -c restore --restore revertvolume` ).

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume`Polecenie przerywa replikację magazynu z lokacji produkcyjnej do lokacji odzyskiwania po awarii. Musisz skontaktować się z usługą Microsoft Operations, aby ponownie skonfigurować replikację. Po ponownym włączeniu replikacji zostaną zainicjowane wszystkie dane w magazynie DR dla tego identyfikatora SID. Polecenie przełączenia w tryb failover umożliwia udostępnienie ostatnio zreplikowanej migawki magazynu. Jeśli chcesz przywrócić poprzednią migawkę, Otwórz żądanie pomocy technicznej, aby umożliwić działanie może pomóc w zapewnieniu przywrócenia wcześniejszej migawki w lokacji odzyskiwania po awarii.

Na wysokim poziomie poniżej przedstawiono kroki do wykonania w przypadku trybu failover odzyskiwania po awarii:

- Należy zamknąć wystąpienie platformy HANA w lokacji **głównej** . Ta akcja jest wymagana tylko wtedy, gdy przejdziesz do trybu failover w lokacji odzyskiwania po awarii, aby nie mieć niespójności danych.
- Zamknij wystąpienie HANA w węźle DR dla produkcyjnego identyfikatora SID.
- Wykonaj polecenie `azacsnap -c restore --restore revertvolume` w WĘŹLE Dr z identyfikatorem SID, który ma zostać odzyskany
  - Polecenie przerywa link replikacji magazynu z podstawowego do lokacji odzyskiwania po awarii
  - Polecenie przywraca tylko wolumin/Data i/logbackups, wolumin/Shared nie jest odzyskiwany, ale zamiast tego używa istniejącego/Shared dla identyfikatora SID w lokalizacji odzyskiwania po awarii.
  - Zainstaluj wolumin/Data i/logbackups — upewnij się, że został on dodany do pliku fstab
- Przywróć migawkę programu HANA SYSTEMDB. W programie HANA Studio wyświetlana jest tylko najnowsza migawka platformy HANA dostępna w przypadku przywrócenia migawki magazynu w ramach `azacsnap -c restore --restore revertvolume` wykonywania polecenia.
- Odzyskaj bazę danych dzierżawy.
- Uruchom wystąpienie HANA w witrynie DR dla produkcyjnego identyfikatora SID (przykład: H80 w tym przypadku).
- Wykonaj testy.

### <a name="example-performing-disaster-recovery"></a>Przykład przeprowadzania odzyskiwania po awarii

W tej podsekcji opisano szczegółowe kroki przejścia w tryb failover do lokacji odzyskiwania po awarii.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>Krok 1. uzyskiwanie szczegółowych informacji o woluminie węzła DR

Wykonaj polecenie, `df –h` Aby wyświetlić listę systemów plików i skojarzonych woluminów, do których odnosi się po przejściu do trybu failover.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>Krok 2. wyłączenie platformy HANA w lokacji głównej

Jeśli wykonujesz pełną pracę w trybie failover w przypadku obciążeń produkcyjnych i istnieje możliwość nawiązywania połączenia z podstawową lokacją produkcyjną, a następnie wyłączając wystąpienia SAP HANA przełączenia w tryb pracy awaryjnej do trybu odzyskiwania po awarii.

Na przykład jeśli użytkownik jest zalogowany jako główny, Poniższy przykład pokazuje, jak można wyłączyć SAP HANA.  Zastąp ciąg <sid> identyfikatorem SID SAP HANA.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>Krok 3. wyłączenie platformy HANA w witrynie odzyskiwania po awarii

Przed przystąpieniem do przywracania woluminów należy wyłączyć SAP HANA w lokacji DR.

Na przykład jeśli użytkownik jest zalogowany jako główny, Poniższy przykład pokazuje, jak można wyłączyć SAP HANA.  Zastąp ciąg <sid> identyfikatorem SID SAP HANA.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Upewnij się, że wystąpienia HANA w lokacji odzyskiwania po awarii są wyłączone przed przywróceniem woluminów.

#### <a name="step-4-restore-the-volumes"></a>Krok 4. Przywracanie woluminów

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_Dane wyjściowe polecenia odzyskiwania po awarii_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Aby ukończyć przygotowywanie magazynu dla trybu failover odzyskiwania po awarii, należy podjąć kroki opisane na końcu ekranu konsoli.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>Krok 5. Odinstalowywanie niepotrzebnych systemów plików

Wykonaj polecenie, `umount` Aby odinstalować system plików/woluminy, które nie są konieczne.

```bash
umount <Mount point>
```

Odinstaluj mountpoints kopii zapasowych danych i dziennika. W scenariuszu skalowalnym w poziomie może istnieć wiele mountpoint danych.

#### <a name="step-6-configure-the-mount-points"></a>Krok 6. Konfigurowanie punktów instalacji

Zmodyfikuj plik, `/etc/fstab` Aby dodać komentarz do wpisów kopii zapasowych danych i dziennika dla podstawowego identyfikatora SID (w tym przykładzie identyfikator SID = H80), a następnie Dodaj nowe wpisy punktu instalacji utworzone na podstawie woluminów Dr lokacji głównej. Nowe wpisy punktu instalacji są dostępne w danych wyjściowych polecenia.

- Dodaj komentarz do istniejących punktów instalacji uruchomionych w witrynie DR przy użyciu `#` znaku:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Dodaj następujące wiersze do `/etc/fstab`
  > powinno to być te same dane wyjściowe z polecenia

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>Krok 7. Instalowanie woluminów odzyskiwania

Wykonaj polecenie, `mount –a` Aby zainstalować wszystkie punkty instalacji.

```bash
mount -a
```

Teraz, po uruchomieniu należy `df –h` zobaczyć `*_dp` zainstalowane woluminy.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>Krok 8. odzyskiwanie SYSTEMDB

W programie HANA Studio kliknij prawym przyciskiem myszy wystąpienie SYSTEMDB i wybierz pozycję "kopia zapasowa i odzyskiwanie", a następnie "Odzyskaj systemową bazę danych".

Zapoznaj się z przewodnikiem, aby odzyskać bazę danych z migawki, w tym SYSTEMDB.

#### <a name="step-9-recover-the-tenant-database"></a>Krok 9. odzyskiwanie bazy danych dzierżawy

W programie HANA Studio kliknij prawym przyciskiem myszy wystąpienie SYSTEMDB i wybierz pozycję "kopia zapasowa i odzyskiwanie", a następnie "Odzyskaj bazę danych dzierżawy".

Zapoznaj się z przewodnikiem, aby odzyskać bazę danych z migawki, w tym bazy danych DZIERŻAW.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>Uruchom `azacsnap -c backup` w witrynie Dr

W przypadku wykonywania kopii zapasowych opartych na migawce w lokacji odzyskiwania po awarii nazwa serwera HANA skonfigurowana w `azacsnap` pliku konfiguracji w lokacji Dr powinna być taka sama jak nazwa serwera produkcyjnego.

> [!IMPORTANT]
> Uruchomienie programu `azacsnap -c backup` może spowodować utworzenie migawek magazynu w witrynie odzyskiwania po awarii, które nie są automatycznie replikowane do innej lokacji.  Pracuj z firmą Microsoft, aby lepiej zrozumieć zwracanie dowolnych plików lub danych z powrotem do oryginalnej lokacji produkcyjnej.

## <a name="next-steps"></a>Następne kroki

- [Pobierz szczegóły migawki](azacsnap-cmd-ref-details.md)
- [Utwórz kopię zapasową](azacsnap-cmd-ref-backup.md)
