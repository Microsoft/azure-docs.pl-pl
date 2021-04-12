---
title: Tworzenie kopii zapasowej bazy danych Oracle Database 19c na maszynie wirtualnej z systemem Linux platformy Azure z usługą RMAN i usługą Azure Storage
description: Dowiedz się, jak utworzyć kopię zapasową bazy danych programu Oracle Database 19c w magazynie w chmurze platformy Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670019"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 19c na maszynie wirtualnej z systemem Linux systemu Azure przy użyciu usługi Azure Storage

W tym artykule pokazano, jak używać usługi Azure Storage jako nośnika do tworzenia kopii zapasowych i przywracania bazy danych Oracle działającej na maszynie wirtualnej platformy Azure. Utworzysz kopię zapasową bazy danych przy użyciu programu Oracle RMAN do usługi Azure File Storage zainstalowanej na maszynie wirtualnej przy użyciu protokołu SMB. Korzystanie z usługi Azure Storage na potrzeby nośnika kopii zapasowych jest niezwykle kosztowne i wydajne. Jednak w przypadku bardzo dużych baz danych Azure Backup zapewnia lepsze rozwiązanie.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Aby wykonać proces tworzenia kopii zapasowej i odzyskiwania, należy najpierw utworzyć maszynę wirtualną z systemem Linux, która ma zainstalowane wystąpienie Oracle Database 19c. Obraz portalu Marketplace aktualnie używany do tworzenia maszyny wirtualnej to  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Najnowsza**. Postępuj zgodnie z instrukcjami w temacie [Tworzenie bazy danych firmy Oracle — szybki start](./oracle-database-quick-create.md) , aby utworzyć bazę danych Oracle w celu wykonania tego samouczka.

## <a name="prepare-the-database-environment"></a>Przygotowanie środowiska bazy danych

1. Aby utworzyć sesję Secure Shell (SSH) z maszyną wirtualną, użyj następującego polecenia. Zastąp adres IP i kombinację nazwy hosta `publicIpAddress` wartością dla maszyny wirtualnej.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Przełącz do ***głównego*** użytkownika:
 
   ```bash
   sudo su -
   ```
    
3. Dodaj użytkownika Oracle do pliku ***/etc/sudoers*** :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. W tym kroku przyjęto założenie, że masz wystąpienie Oracle (test), które działa na maszynie wirtualnej o nazwie *vmoracle19c*.

   Przełącz użytkownika na użytkownika *Oracle* :

   ```bash
   sudo su - oracle
   ```
    
5. Przed nawiązaniem połączenia należy ustawić zmienną środowiskową ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Należy również dodać zmienną ORACLE_SID do `oracle` `.bashrc` pliku users dla przyszłych logowań przy użyciu następującego polecenia:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Uruchom odbiornik Oracle, jeśli nie jest jeszcze uruchomiony:
    
   ```bash
   $ lsnrctl start
   ```

    Dane wyjściowe powinny wyglądać mniej więcej tak jak w tym przykładzie:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Utwórz lokalizację szybkiego odzyskiwania (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Połącz z bazą danych:

    ```bash
    sqlplus / as sysdba
    ```

9.  Uruchom bazę danych, jeśli nie została jeszcze uruchomiona:

    ```bash
    SQL> startup
    ```

10. Ustaw zmienne środowiskowe bazy danych dla obszaru szybkiego odzyskiwania:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Upewnij się, że baza danych jest w trybie dziennika archiwizacji, aby włączyć tworzenie kopii zapasowych online.
    Najpierw sprawdź stan archiwum dzienników:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    A jeśli w trybie NOARCHIVELOG, uruchom następujące polecenia w sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Utwórz tabelę do testowania operacji tworzenia kopii zapasowej i przywracania:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Utwórz kopię zapasową do Azure Files

Aby utworzyć kopię zapasową do Azure Files, wykonaj następujące kroki:

1. Skonfiguruj File Storage platformy Azure.
1. Zainstaluj udział plików usługi Azure Storage na maszynie wirtualnej.
1. Wykonaj kopię zapasową bazy danych programu.
1. Przywróć i Odzyskaj bazę danych.

### <a name="set-up-azure-file-storage"></a>Konfigurowanie usługi Azure File Storage

W tym kroku utworzysz kopię zapasową bazy danych Oracle przy użyciu programu Oracle Recovery Manager (RMAN) do usługi Azure File Storage. Udziały plików platformy Azure to w pełni zarządzane udziały plików w chmurze. Dostęp do nich można uzyskać przy użyciu protokołu bloku komunikatów serwera (SMB) lub protokołu sieciowego systemu plików (NFS). W tym kroku opisano tworzenie udziału plików, który używa protokołu SMB do instalacji na maszynie wirtualnej. Aby uzyskać informacje o sposobach instalowania za pomocą systemu plików NFS, zobacz [Instalowanie magazynu obiektów BLOB przy użyciu protokołu nfs 3,0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Podczas instalowania Azure Files użyjemy, `cache=none` Aby wyłączyć buforowanie danych udziałów plików. I aby upewnić się, że pliki utworzone w udziale są własnością użytkownika Oracle, `uid=oracle` Ustaw `gid=oinstall` również opcje i. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Najpierw skonfiguruj konto magazynu.

1. Konfigurowanie File Storage w Azure Portal

    W Azure Portal wybierz pozycję ***+ Utwórz zasób** _ i Wyszukaj i wybierz pozycję _ *_konto magazynu_* .*
    
    ![Zrzut ekranu pokazujący miejsce utworzenia zasobu i wybierz pozycję konto magazynu.](./media/oracle-backup-recovery/storage-1.png)
    
2. Na stronie Tworzenie konta magazynu wybierz istniejącą grupę zasobów ***RG-Oracle** _, nadaj nazwę swojemu kontu magazynu _*_oracbkup1_*_ i wybierz pozycję _*_Storage v2 (GeneralPurpose v2)_*_ dla rodzaju konta. Zmień replikację na _*_Magazyn lokalnie nadmiarowy (LRS)_*_ i ustaw wydajność na _ *_Standard_* *. Upewnij się, że lokalizacja jest ustawiona na ten sam region, co w przypadku wszystkich innych zasobów w grupie zasobów. 
    
    ![Zrzut ekranu pokazujący, gdzie wybrać istniejącą grupę zasobów.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Kliknij kartę ***Advanced** _ i w obszarze Azure Files Ustaw _*_duże udziały plików_*_ na _ *_włączone_* *. Kliknij przycisk przegląd + Utwórz, a następnie kliknij przycisk Utwórz.
    
    ![Zrzut ekranu pokazujący, gdzie należy ustawić duże udziały plików do włączenia.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Po utworzeniu konta magazynu przejdź do zasobu i wybierz pozycję ***udziały plików*** .
    
    ![Zrzut ekranu pokazujący, gdzie wybierać udziały plików.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Kliknij pozycję ***+ udział plików** _ i w _*_nowym bloku udziału plików_*_ Nadaj plikowi udział plików _*_orabkup1_*_. Ustaw _*_limit przydziału_*_ na _*_10240_*_ GIB i sprawdź, czy _*_transakcja została zoptymalizowana_*_ jako warstwa. Przydział odzwierciedla górną granicę, do której może się zwiększać udział plików. Ponieważ korzystamy z magazynu w warstwie Standardowa, zasoby są w postaci PAYG i nie są obsługiwane, więc jego ustawienie na wartość 10 TiB nie spowoduje naliczania kosztów poza to, czego używasz. Jeśli strategia tworzenia kopii zapasowych wymaga więcej miejsca w magazynie, należy ustawić limit przydziału na odpowiedni poziom, aby pomieścić wszystkie kopie zapasowe.   Po zakończeniu nowego bloku udostępniania plików kliknij _ *_Utwórz_* *.
    
    ![Zrzut ekranu pokazujący, gdzie dodać nowy udział plików.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Po utworzeniu kliknij pozycję ***orabkup1*** na stronie Ustawienia udziału plików. 
    Kliknij kartę ***Connect** _, aby otworzyć blok Połącz, a następnie kliknij kartę _ *_Linux_**. Skopiuj podane polecenia, aby zainstalować udział plików przy użyciu protokołu SMB. 
    
    ![Strona dodawania konta magazynu](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować konto magazynu i udział plików, uruchom następujące polecenia w interfejsie wiersza polecenia platformy Azure.

1. Utwórz konto magazynu w tej samej grupie zasobów i lokalizacji co maszyna wirtualna:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Utwórz udział plików na koncie magazynu o limicie przydziału równym 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Pobierz klucz podstawowy konta magazynu (Klucz1), który jest wymagany podczas instalowania udziału plików na maszynie wirtualnej:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Instalowanie udziału plików usługi Azure Storage na maszynie wirtualnej

1. Utwórz punkt instalacji:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Skonfiguruj poświadczenia:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   `<Your Storage Account Key1>`Przed uruchomieniem następującego polecenia Zastąp ciąg wcześniej pobranym kluczem konta magazynu:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Zmień uprawnienia do pliku poświadczeń:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Dodaj instalację do/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Uruchom polecenia, aby zainstalować magazyn Azure Files przy użyciu protokołu SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Jeśli zostanie wyświetlony komunikat o błędzie podobny do:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   Pakiet CIFS nie może być zainstalowany na hoście z systemem Linux. 
   
   Aby sprawdzić, czy usługa CIS jest zainstalowana, uruchom następujące polecenie:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Jeśli polecenie nie zwróci danych wyjściowych, zainstaluj pakiet CIFS przy użyciu następującego polecenia:

   ```bash 
   sudo yum install cifs-utils
   ```

   Teraz ponownie uruchom polecenie Mount powyżej, aby zainstalować Azure Files magazyn.

6. Sprawdź, czy udział plików został prawidłowo zainstalowany przy użyciu następującego polecenia:

   ```bash
   df -h
   ```

   Dane wyjściowe powinny wyglądać mniej więcej tak:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Tworzenie kopii zapasowej bazy danych

W tej sekcji będziemy korzystać z programu Oracle Recovery Manager (RMAN) w celu utworzenia pełnej kopii zapasowej bazy danych i archiwum dzienników oraz zapisania kopii zapasowej jako zestawu kopii zapasowych w ramach zainstalowanego wcześniej udziału plików platformy Azure. 

1. Skonfiguruj RMAN do wykonywania kopii zapasowej w punkcie instalacji Azure Files:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Ponieważ standardowe udziały plików platformy Azure mają maksymalny rozmiar pliku równy 1 TiB, ograniczy rozmiar RMAN kopii zapasowych do 1 TiB. (Zwróć uwagę, że udziały plików w warstwie Premium mają maksymalny limit rozmiaru pliku wynoszący 4 TiB. Aby uzyskać więcej informacji, zobacz [Azure Files cele dotyczące skalowalności i wydajności](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Potwierdź szczegóły zmiany konfiguracji:

    ```bash
    RMAN> show all;
    ```

4. Teraz uruchom kopię zapasową. Następujące polecenie zajmie pełną kopię zapasową bazy danych, w tym pliki dziennika archiwalne, jako zestawu kopii zapasowych w formacie skompresowanym:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Utworzono kopię zapasową bazy danych w trybie online przy użyciu programu Oracle RMAN z kopią zapasową znajdującą się w usłudze Azure File Storage. Ta metoda ma zalety korzystania z funkcji RMAN podczas zapisywania kopii zapasowych w usłudze Azure File Storage, z których można uzyskać dostęp z innych maszyn wirtualnych, przydatnych w przypadku konieczności klonowania bazy danych.  
    
W przypadku korzystania z RMAN i usługi Azure File Storage do tworzenia kopii zapasowych bazy danych ma wiele zalet, a czas wykonywania kopii zapasowych i przywracania jest połączony z rozmiarem bazy danych, dlatego w przypadku bardzo dużych baz danych może to zająć znaczną ilość czasu na te operacje.  Alternatywny mechanizm tworzenia kopii zapasowych, który używa Azure Backup tworzenia kopii zapasowych spójnych na poziomie aplikacji, korzysta z technologii migawek do wykonywania kopii zapasowych, co pozwala korzystać z bardzo szybkich kopii zapasowych niezależnie od rozmiaru bazy danych. Integracja z magazynem Recovery Services umożliwia bezpieczne przechowywanie Oracle Database kopii zapasowych w usłudze Azure Cloud Storage dostępnych z innych maszyn wirtualnych i regionów świadczenia usługi Azure. 

### <a name="restore-and-recover-the-database"></a>Przywracanie i odzyskiwanie bazy danych

1.  Zamknij wystąpienie programu Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Usuń datafiles i kopie zapasowe:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Następujące polecenia używają RMAN do przywrócenia brakującej datafiles i odzyskania bazy danych:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Sprawdź, czy zawartość bazy danych została w pełni odzyskana:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 19c na maszynie wirtualnej platformy Azure z systemem Linux zostało zakończone.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Jeśli maszyna wirtualna nie jest już potrzebna, możesz użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Eksplorowanie przykładów interfejsu wiersza polecenia platformy Azure wdrożenia maszyny wirtualnej](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)