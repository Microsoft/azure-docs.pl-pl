---
title: Tworzenie kopii zapasowych i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej systemu Azure Linux | Dokumenty firmy Microsoft
description: Dowiedz się, jak zrobić zapas kopii zapasowych i odzyskać bazę danych Oracle Database 12c w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: c5f02117d3af7fb411c75d783df82f6008d8104e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687002"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Tworzenie kopii zapasowych i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej systemu Azure Linux

Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć zasoby platformy Azure i zarządzać nimi w wierszu polecenia lub używać skryptów. W tym artykule używamy skryptów interfejsu wiersza polecenia platformy Azure do wdrażania bazy danych Oracle Database 12c z obrazu galerii portalu portalu azure marketplace.

Przed rozpoczęciem upewnij się, że jest zainstalowany zestaw wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [podręcznik instalacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="step-1-prerequisites"></a>Krok 1: Wymagania wstępne

*   Aby wykonać proces tworzenia kopii zapasowej i odzyskiwania, należy najpierw utworzyć maszynę wirtualną z systemem Linux, która ma zainstalowane wystąpienie bazy danych Oracle Database 12c. Obraz marketplace używany do tworzenia maszyny Wirtualnej nosi nazwę *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Aby dowiedzieć się, jak utworzyć bazę danych Oracle, zobacz [Szybki start tworzenia bazy danych Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Połącz się z maszyną wirtualną

*   Aby utworzyć sesję bezpiecznej powłoki (SSH) za pomocą maszyny Wirtualnej, użyj następującego polecenia. Zastąp adres IP i `publicIpAddress` kombinację nazw hostów wartością maszyny Wirtualnej.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Przygotowanie bazy danych

1.  W tym kroku przyjęto założenie, że masz wystąpienie Oracle (cdb1), które jest uruchomione na maszynie wirtualnej o nazwie *myVM*.

    Uruchom katalog główny superużytnika *oracle,* a następnie zaiwartuj odbiornika:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Opcjonalnie) Upewnij się, że baza danych jest w trybie dziennika archiwum:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

3.  (Opcjonalnie) Utwórz tabelę, aby przetestować zatwierdzenie:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```

4.  Sprawdź lub zmień lokalizację i rozmiar pliku kopii zapasowej:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Użyj Oracle Recovery Manager (RMAN), aby zrobić zapas kopii zapasowej bazy danych:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Tworzenie kopii zapasowych spójnych aplikacji dla maszyn wirtualnych z systemem Linux

Tworzenie kopii zapasowych spójnych z aplikacjami to nowa funkcja w usłudze Azure Backup. Można tworzyć i wybierać skrypty do wykonania przed i po migawki maszyny Wirtualnej (pre-migawki i po migawki).

1. Pobierz plik JSON.

    Pobierz VMSnapshotScriptPluginConfig.json https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfigz . Zawartość pliku wygląda podobnie do następującej:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Utwórz folder /etc/azure na maszynie wirtualnej:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Skopiuj plik JSON.

    Skopiuj plik VMSnapshotScriptPluginConfig.json do folderu /etc/azure.

4. Edytuj plik JSON.

    Edytuj plik VMSnapshotScriptPluginConfig.json, `PreScriptLocation` aby `PostScriptlocation` uwzględnić parametry i parametry. Przykład:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Utwórz pliki skryptów przed migawką i po migawkach.

    Oto przykład skryptów przed migawką i po migawkach dla "zimnej kopii zapasowej" (kopii zapasowej w trybie offline, z zamknięciem i ponownym uruchomieniem):

    Dla /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Dla /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Oto przykład skryptów przed migawką i po migawkach dla "gorącej kopii zapasowej" (kopii zapasowej online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Dla /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Dla /etc/azure/pre_script.sql, zmodyfikuj zawartość pliku zgodnie z wymaganiami:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Dla /etc/azure/post_script.sql, zmodyfikuj zawartość pliku zgodnie z wymaganiami:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Zmień uprawnienia do pliku:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Przetestuj skrypty.

    Aby przetestować skrypty, najpierw zaloguj się jako katalog główny. Następnie upewnij się, że nie ma żadnych błędów:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych spójnych z aplikacjami dla maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: Użyj magazynów usług Azure Recovery Services do utworzenia kopii zapasowej maszyny Wirtualnej

1.  W witrynie Azure portal wyszukaj **magazyny usług odzyskiwania**.

    ![Strona magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_01.png)

2.  W bloku **Magazyny usług odzyskiwania,** aby dodać nowy magazyn, kliknij przycisk **Dodaj**.

    ![Strony dodawania przechowalni usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Aby kontynuować, kliknij **myVault**.

    ![Strona szczegółów magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na bloku **myVault** kliknij pozycję **Kopia zapasowa**.

    ![Strona kopii zapasowej magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_04.png)

5.  W bloku **Cel kopii zapasowej** użyj wartości domyślnych platformy **Azure** i **maszyny wirtualnej**. Kliknij przycisk **OK**.

    ![Strona szczegółów magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_05.png)

6.  W polu **Zasady tworzenia kopii zapasowych**użyj opcji **Domyślnapolicja**lub wybierz pozycję **Utwórz nową zasadę**. Kliknij przycisk **OK**.

    ![Strona szczegółów zasad tworzenia kopii zapasowych magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_06.png)

7.  W bloku **Wybierz maszyny wirtualne** zaznacz pole wyboru **myVM1,** a następnie kliknij przycisk **OK**. Kliknij przycisk **Włącz tworzenie kopii zapasowej.**

    ![Usługi odzyskiwania przechowalniuj elementy na stronie szczegółów kopii zapasowej](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknięciu **przycisku Włącz tworzenie kopii zapasowej**proces tworzenia kopii zapasowej nie rozpocznie się do momentu wygaśnięcia zaplanowanego czasu. Aby skonfigurować natychmiastową kopię zapasową, wykonaj następny krok.

8.  Na **bloku myVault — zapas elementów** w obszarze **LICZBA elementów kopii zapasowej**wybierz liczbę elementów kopii zapasowej.

    ![Recovery Services przechwyca stronę szczegółów usługi myVault](./media/oracle-backup-recovery/recovery_service_08.png)

9.  W bloku **Elementy kopii zapasowej (Maszyna wirtualna platformy Azure)** po prawej stronie kliknij przycisk wielokropek (**...**), a następnie kliknij przycisk **Kopia zapasowa teraz**.

    ![Usługi odzyskiwania magazyny Kopia zapasowa teraz polecenia](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kliknij przycisk **Kopia zapasowa.** Poczekaj na zakończenie procesu tworzenia kopii zapasowej. Następnie przejdź do [kroku 6: Usuń pliki bazy danych](#step-6-remove-the-database-files).

    Aby wyświetlić stan zadania tworzenia kopii zapasowej, kliknij przycisk **Zadania**.

    ![Strona zadania magazynów usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_10.png)

    Stan zadania tworzenia kopii zapasowej pojawia się na poniższej ilustracji:

    ![Strona zadania magazynów usług odzyskiwania o stanie](./media/oracle-backup-recovery/recovery_service_11.png)

11. W przypadku kopii zapasowej spójnej z aplikacją należy rozwiązać wszelkie błędy w pliku dziennika. Plik dziennika znajduje się pod adresem /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Usuwanie plików bazy danych 
W dalszej części tego artykułu dowiesz się, jak przetestować proces odzyskiwania. Przed przetestowaniem procesu odzyskiwania należy usunąć pliki bazy danych.

1.  Usuń obszar tabel i pliki kopii zapasowych:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Opcjonalnie) Zamknij wystąpienie Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Przywracanie usuniętych plików z magazynów usług odzyskiwania
Aby przywrócić usunięte pliki, wykonaj następujące czynności:

1. W witrynie Azure portal wyszukaj element magazynów usług *myVault* Recovery Services. Na **przegląd bloku w** obszarze **Zapasy zapasowe wybierz**liczbę elementów.

    ![Usługi odzyskiwania przechowalnią elementy kopii zapasowej usługi myVault](./media/oracle-backup-recovery/recovery_service_12.png)

2. W obszarze **LICZBA ELEMENTÓW KOPII ZAPASOWEJ**wybierz liczbę elementów.

    ![Usługi odzyskiwania przechowalnia usługi Azure Virtual Machine liczba elementów kopii zapasowej](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na bloku **myvm1** kliknij pozycję **Odzyskiwanie plików (podgląd)**.

    ![Zrzut ekranu przedstawiający stronę odzyskiwania plików magazynów magazynów usług odzyskiwania odzyskiwania usług odzyskiwania](./media/oracle-backup-recovery/recovery_service_14.png)

4. W okienku **Odzyskiwanie plików (podgląd)** kliknij pozycję **Pobierz skrypt**. Następnie zapisz plik pobierania (sh) w folderze na komputerze klienckim.

    ![Pobierz opcje zapisywania pliku skryptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Skopiuj plik .sh do maszyny Wirtualnej.

    W poniższym przykładzie pokazano, jak użyć polecenia bezpiecznej kopii (scp) w celu przeniesienia pliku do maszyny Wirtualnej. Można również skopiować zawartość do schowka, a następnie wkleić zawartość nowego pliku skonfigurowanego na maszynie Wirtualnej.

    > [!IMPORTANT]
    > W poniższym przykładzie upewnij się, że aktualizujesz wartości adresu IP i folderu. Wartości muszą być mapowane do folderu, w którym plik jest zapisywany.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Zmień plik, tak aby był własnością katalogu głównego.

    W poniższym przykładzie zmień plik tak, aby był własnością katalogu głównego. Następnie zmień uprawnienia.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    W poniższym przykładzie pokazano, co powinno być widoczne po uruchomieniu poprzedniego skryptu. Po wyświetleniu monitu o kontynuowanie wprowadź **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Dostęp do zamontowanych woluminów zostanie potwierdzony.

    Aby wyjść, wprowadź **q**, a następnie wyszukaj zamontowane woluminy. Aby utworzyć listę dodanych woluminów, w wierszu polecenia wprowadź **df -k**.

    ![Polecenie df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Użyj następującego skryptu, aby skopiować brakujące pliki z powrotem do folderów:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```

9. W poniższym skrypcie użyj RMAN, aby odzyskać bazę danych:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

10. Odinstaluj dysk.

    W witrynie Azure portal w bloku **Odzyskiwanie plików (Wersja zapoznawcza)** kliknij pozycję **Odinstaluj dyski**.

    ![Odinstaluj dyski, polecenie](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Przywracanie całej maszyny Wirtualnej

Zamiast przywracać usunięte pliki z magazynów usług odzyskiwania, można przywrócić całą maszynę wirtualną.

### <a name="step-1-delete-myvm"></a>Krok 1: Usuń myVM

*   W witrynie Azure portal przejdź do przechowalni **myVM1,** a następnie wybierz pozycję **Usuń**.

    ![Polecenie usuń przechowalnię](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Odzyskaj maszynę wirtualną

1.  Przejdź do **magazynu usług odzyskiwania,** a następnie wybierz **myVault**.

    ![wpis myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na **przegląd bloku w** obszarze **Zapasy zapasowe wybierz**liczbę elementów.

    ![myVault kopii zapasowej elementów](./media/oracle-backup-recovery/recover_vm_03.png)

3.  W bloku **Elementy kopii zapasowej (Maszyna wirtualna platformy Azure)** wybierz **opcję myvm1**.

    ![Strona maszyny wirtualnej odzyskiwania](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na bloku **myvm1** kliknij przycisk wielokropka (**...**), a następnie kliknij przycisk **Przywróć maszynę wirtualną**.

    ![Przywróć maszynę wirtualną, polecenie](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na bloku **Wybierz punkt przywracania** zaznacz element, który chcesz przywrócić, a następnie kliknij przycisk **OK**.

    ![Zaznaczanie punktu przywracania](./media/oracle-backup-recovery/recover_vm_06.png)

    Jeśli włączono tworzenie kopii zapasowych spójnych z aplikacją, zostanie wyświetlony niebieski pasek pionowy.

6.  W bloku **Przywracanie konfiguracji** wybierz nazwę maszyny wirtualnej, wybierz grupę zasobów, a następnie kliknij przycisk **OK**.

    ![Przywracanie wartości konfiguracyjnych](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Aby przywrócić maszynę wirtualną, kliknij przycisk **Przywróć.**

8.  Aby wyświetlić stan procesu przywracania, kliknij pozycję **Zadania**, a następnie kliknij pozycję **Zadania kopii zapasowej**.

    ![Polecenie Stan zadań tworzenia kopii zapasowej](./media/oracle-backup-recovery/recover_vm_08.png)

    Na poniższej ilustracji przedstawiono stan procesu przywracania:

    ![Stan procesu przywracania](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Ustawianie publicznego adresu IP
Po przywróceniu maszyny Wirtualnej należy skonfigurować publiczny adres IP.

1.  W polu wyszukiwania wprowadź **publiczny adres IP**.

    ![Lista publicznych adresów IP](./media/oracle-backup-recovery/create_ip_00.png)

2.  W bloku **Publiczne adresy IP** kliknij pozycję **Dodaj**. W bloku **Tworzenie publicznego adresu IP** wybierz publiczną nazwę IP. **Name** W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**. Następnie kliknij pozycję **Utwórz**.

    ![Tworzenie adresu IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Aby skojarzyć publiczny adres IP z interfejsem sieciowym maszyny Wirtualnej, wyszukaj i wybierz **myVMip**. Następnie kliknij przycisk **Skojarz**.

    ![Kojarzenie adresu IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  W obszarze **Typ zasobu**wybierz **pozycję Interfejs sieciowy**. Wybierz interfejs sieciowy używany przez wystąpienie myVM, a następnie kliknij przycisk **OK**.

    ![Wybieranie wartości typu zasobu i karty sieciowej](./media/oracle-backup-recovery/create_ip_03.png)

5.  Wyszukaj i otwórz wystąpienie myVM, które jest przenoszone z portalu. Adres IP skojarzony z maszyną wirtualną pojawia się w bloku **przegląd** myVM.

    ![Wartość adresu IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Połącz się z maszyną wirtualną

*   Aby połączyć się z maszyną wirtualną, użyj następującego skryptu:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: Sprawdź, czy baza danych jest dostępna
*   Aby przetestować dostępność, użyj następującego skryptu:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Jeśli polecenie **uruchamiania** bazy danych generuje błąd, aby odzyskać bazę danych, zobacz [Krok 6: Odzyskiwanie bazy danych za pomocą polecenia RMAN.](#step-6-optional-use-rman-to-recover-the-database)

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (Opcjonalnie) Użyj RMAN, aby odzyskać bazę danych
*   Aby odzyskać bazę danych, użyj następującego skryptu:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Kopia zapasowa i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej systemu Azure Linux zostało zakończone.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Jeśli maszyna wirtualna nie jest już potrzebna, można użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Zapoznaj się z przykładami interfejsu wiersza polecenia platformy Azure dotyczące wdrażania maszyn wirtualnych](../../linux/cli-samples.md)



