---
title: Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej platformy Azure z systemem Linux | Microsoft Docs
description: Dowiedz się, jak utworzyć kopię zapasową i odzyskać bazę danych Oracle Database 12c w środowisku platformy Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687002"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej platformy Azure z systemem Linux

Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć zasoby platformy Azure i zarządzać nimi w wierszu poleceń lub używać skryptów. W tym artykule używamy skryptów interfejsu wiersza polecenia platformy Azure do Oracle Database wdrażania bazy danych 12c Database z poziomu obrazu z galerii portalu Azure Marketplace.

Przed rozpoczęciem upewnij się, że jest zainstalowany interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="step-1-prerequisites"></a>Krok 1: wymagania wstępne

*   Aby wykonać proces tworzenia kopii zapasowej i odzyskiwania, należy najpierw utworzyć maszynę wirtualną z systemem Linux, która ma zainstalowane wystąpienie Oracle Database 12c. Obraz portalu Marketplace używany do tworzenia maszyny wirtualnej nosi nazwę *Oracle: Oracle-Database-EE: 12.1.0.2: Najnowsze*.

    Aby dowiedzieć się, jak utworzyć bazę danych Oracle, zobacz [Przewodnik Szybki Start dotyczący tworzenia bazy danych Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2. Nawiązywanie połączenia z maszyną wirtualną

*   Aby utworzyć sesję Secure Shell (SSH) z maszyną wirtualną, użyj następującego polecenia. Zastąp adres IP i kombinację nazwy hosta `publicIpAddress` wartością dla maszyny wirtualnej.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3. Przygotowywanie bazy danych

1.  W tym kroku przyjęto założenie, że masz wystąpienie Oracle (cdb1), które działa na maszynie wirtualnej o nazwie *myVM*.

    Uruchom polecenie *Oracle* administratora root, a następnie zainicjuj odbiornik:

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

2.  Obowiązkowe Upewnij się, że baza danych jest w trybie dziennika archiwum:

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

3.  Obowiązkowe Utwórz tabelę, aby przetestować zatwierdzenie:

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

4.  Sprawdź lub Zmień lokalizację i rozmiar pliku kopii zapasowej:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Aby utworzyć kopię zapasową bazy danych, użyj programu Oracle Recovery Manager (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4. Tworzenie kopii zapasowej spójnej na poziomie aplikacji dla maszyn wirtualnych z systemem Linux

Kopie zapasowe spójne z aplikacjami są nową funkcją w Azure Backup. Można tworzyć i wybierać skrypty do wykonania przed i po migawce maszyny wirtualnej (przed migawką i po utworzeniu migawki).

1. Pobierz plik JSON.

    Pobierz VMSnapshotScriptPluginConfig.jsz https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig . Zawartość pliku wygląda podobnie do poniższego:

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

2. Utwórz folder/etc/Azure na maszynie wirtualnej:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Skopiuj plik JSON.

    Skopiuj VMSnapshotScriptPluginConfig.jsdo folderu/etc/Azure.

4. Edytuj plik JSON.

    Edytuj VMSnapshotScriptPluginConfig.jspliku, aby uwzględnić `PreScriptLocation` `PostScriptlocation` Parametry i. Przykład:

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

5. Tworzenie plików skryptów przedmigawkowych i po migawce.

    Oto przykład skryptów przedmigawkowych i po migawce dla "zimnej kopii zapasowej" (kopia zapasowa offline, z zamknięciem i ponownym uruchomieniem):

    Dla/etc/Azure/pre_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Dla/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Oto przykład skryptów przedmigawkowych i po migawce dla "gorąca kopia zapasowa" (kopia zapasowa online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Dla/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    W przypadku/etc/Azure/pre_script. SQL zmodyfikuj zawartość pliku zgodnie z wymaganiami:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    W przypadku/etc/Azure/post_script. SQL zmodyfikuj zawartość pliku zgodnie z wymaganiami:

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

    Aby przetestować skrypty, najpierw Zaloguj się jako element główny. Następnie upewnij się, że nie ma żadnych błędów:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej spójnej na poziomie aplikacji dla maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5. Tworzenie kopii zapasowej maszyny wirtualnej za pomocą magazynów usługi Azure Recovery Services

1.  W Azure Portal Wyszukaj pozycję **magazyny Recovery Services**.

    ![Strona Recovery Services magazynów](./media/oracle-backup-recovery/recovery_service_01.png)

2.  W bloku **Recovery Services magazynów** , aby dodać nowy magazyn, kliknij przycisk **Dodaj**.

    ![Dodawanie strony do magazynów Recovery Services](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Aby kontynuować, kliknij pozycję Moja **Magazyn**.

    ![Strona szczegółów Recovery Services magazynów](./media/oracle-backup-recovery/recovery_service_03.png)

4.  W bloku moje **Magazyn** kliknij pozycję **kopia zapasowa**.

    ![Strona kopii zapasowych magazynów Recovery Services](./media/oracle-backup-recovery/recovery_service_04.png)

5.  W bloku **cel kopii zapasowej** Użyj wartości domyślnych **platformy Azure** i **maszyny wirtualnej**. Kliknij przycisk **OK**.

    ![Strona szczegółów Recovery Services magazynów](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Dla **zasad tworzenia kopii zapasowych**Użyj **DefaultPolicy**lub wybierz pozycję **Utwórz nowe zasady**. Kliknij przycisk **OK**.

    ![Strona szczegółów zasad tworzenia kopii zapasowych magazynów Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  W bloku **Wybieranie maszyn wirtualnych** zaznacz pole wyboru **myVM1** , a następnie kliknij przycisk **OK**. Kliknij przycisk **Włącz kopię zapasową** .

    ![Recovery Services elementy magazynów na stronie szczegółów kopii zapasowej](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknięciu przycisku **Włącz kopię zapasową**proces tworzenia kopii zapasowej nie zostanie uruchomiony do czasu wygaśnięcia zaplanowanego czasu. Aby skonfigurować natychmiastową kopię zapasową, wykonaj następny krok.

8.  W bloku moje **magazyny — elementy kopii zapasowej** w obszarze **Liczba elementów kopii**zapasowej wybierz liczbę elementów kopii zapasowej.

    ![Strona szczegółów magazynu Recovery Services magazynów](./media/oracle-backup-recovery/recovery_service_08.png)

9.  W bloku **elementy kopii zapasowej (maszyna wirtualna platformy Azure)** w prawej części strony kliknij przycisk wielokropka (**...**), a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.

    ![Tworzenie kopii zapasowej Recovery Services magazynów teraz — polecenie](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kliknij przycisk **kopia zapasowa** . Poczekaj na zakończenie procesu tworzenia kopii zapasowej. Następnie przejdź do [kroku 6: usuwanie plików bazy danych](#step-6-remove-the-database-files).

    Aby wyświetlić stan zadania tworzenia kopii zapasowej, kliknij pozycję **zadania**.

    ![Strona zadania magazynu Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    Stan zadania tworzenia kopii zapasowej zostanie wyświetlony na poniższej ilustracji:

    ![Strona zadań magazynu Recovery Services ze stanem](./media/oracle-backup-recovery/recovery_service_11.png)

11. W przypadku kopii zapasowej spójnej na poziomie aplikacji należy rozwiązać wszelkie błędy w pliku dziennika. Plik dziennika znajduje się w lokalizacji/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6. Usuwanie plików bazy danych 
W dalszej części tego artykułu dowiesz się, jak przetestować proces odzyskiwania. Aby można było przetestować proces odzyskiwania, należy usunąć pliki bazy danych.

1.  Usuń obszar tabel i pliki kopii zapasowej:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Obowiązkowe Zamknij wystąpienie programu Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Przywracanie usuniętych plików z magazynów Recovery Services
Aby przywrócić usunięte pliki, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj *element magazyny Recovery Services magazynu* . W bloku **Przegląd** w obszarze **elementy kopii zapasowej**wybierz liczbę elementów.

    ![Magazyn Recovery Services elementy kopii zapasowej magazynu](./media/oracle-backup-recovery/recovery_service_12.png)

2. W obszarze **Liczba elementów kopii zapasowej**wybierz liczbę elementów.

    ![Magazyn Recovery Services liczba elementów kopii zapasowej maszyny wirtualnej platformy Azure](./media/oracle-backup-recovery/recovery_service_13.png)

3. W bloku **myvm1** kliknij pozycję **odzyskiwanie plików (wersja zapoznawcza)**.

    ![Zrzut ekranu przedstawiający stronę odzyskiwania plików Recovery Services magazynów](./media/oracle-backup-recovery/recovery_service_14.png)

4. W okienku **odzyskiwanie plików (wersja zapoznawcza)** kliknij pozycję **Pobierz skrypt**. Następnie Zapisz plik Download (. sh) do folderu na komputerze klienckim.

    ![Pobieranie pliku skryptu — opcje zapisywania](./media/oracle-backup-recovery/recovery_service_15.png)

5. Skopiuj plik. sh na maszynę wirtualną.

    W poniższym przykładzie pokazano, jak za pomocą polecenia Secure Copy (SCP) przenieść plik na maszynę wirtualną. Możesz również skopiować zawartość do schowka, a następnie wkleić zawartość do nowego pliku, który jest skonfigurowany na maszynie wirtualnej.

    > [!IMPORTANT]
    > W poniższym przykładzie upewnij się, że Zaktualizowano wartości adresów IP i folderów. Wartości muszą być mapowane do folderu, w którym zapisano plik.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Zmień plik, tak aby należał do katalogu głównego.

    W poniższym przykładzie Zmień plik tak, aby należał do katalogu głównego. Następnie Zmień uprawnienia.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    Poniższy przykład pokazuje, co powinno być widoczne po uruchomieniu powyższego skryptu. Po wyświetleniu monitu o kontynuowanie wprowadź **Y**.

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

7. Zatwierdzono dostęp do zainstalowanych woluminów.

    Aby wyjść, wprowadź **q**, a następnie wyszukaj woluminy zainstalowane. Aby utworzyć listę dodanych woluminów, w wierszu polecenia wprowadź wartość **DF-k**.

    ![DF-k — polecenie](./media/oracle-backup-recovery/recovery_service_16.png)

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

9. W poniższym skrypcie Użyj RMAN do odzyskania bazy danych:

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

    W Azure Portal w bloku **odzyskiwanie plików (wersja zapoznawcza)** kliknij pozycję **odinstalowanie dysków**.

    ![Odinstalowywanie dysków — polecenie](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Przywróć całą maszynę wirtualną

Zamiast przywracać usunięte pliki z magazynów Recovery Services, można przywrócić całą maszynę wirtualną.

### <a name="step-1-delete-myvm"></a>Krok 1. Usuwanie myVM

*   W Azure Portal przejdź do magazynu **myVM1** , a następnie wybierz pozycję **Usuń**.

    ![Polecenie usunięcia magazynu](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2. odzyskanie maszyny wirtualnej

1.  Przejdź do **Recovery Services magazynów**, a następnie wybierz pozycję Usuń **Magazyn**.

    ![wpis magazynu](./media/oracle-backup-recovery/recover_vm_02.png)

2.  W bloku **Przegląd** w obszarze **elementy kopii zapasowej**wybierz liczbę elementów.

    ![Tworzenie kopii zapasowej elementów w magazynie](./media/oracle-backup-recovery/recover_vm_03.png)

3.  W bloku **elementy kopii zapasowej (maszyna wirtualna platformy Azure)** wybierz pozycję **myvm1**.

    ![Strona maszyny wirtualnej odzyskiwania](./media/oracle-backup-recovery/recover_vm_04.png)

4.  W bloku **myvm1** kliknij przycisk wielokropka (**...**), a następnie kliknij pozycję **Przywróć maszynę wirtualną**.

    ![Polecenie Restore VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  W bloku **Wybierz punkt przywracania** wybierz element, który chcesz przywrócić, a następnie kliknij przycisk **OK**.

    ![Wybierz punkt przywracania](./media/oracle-backup-recovery/recover_vm_06.png)

    Jeśli włączono tworzenie kopii zapasowej spójnej na poziomie aplikacji, zostanie wyświetlony pionowy niebieski pasek.

6.  W bloku **Przywracanie konfiguracji** wybierz nazwę maszyny wirtualnej, wybierz grupę zasobów, a następnie kliknij przycisk **OK**.

    ![Przywróć wartości konfiguracyjne](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Aby przywrócić maszynę wirtualną, kliknij przycisk **Przywróć** .

8.  Aby wyświetlić stan procesu przywracania, kliknij pozycję **zadania**, a następnie kliknij pozycję **zadania tworzenia kopii zapasowej**.

    ![Stan zadań kopii zapasowej — polecenie](./media/oracle-backup-recovery/recover_vm_08.png)

    Na poniższej ilustracji przedstawiono stan procesu przywracania:

    ![Stan procesu przywracania](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3. Ustawianie publicznego adresu IP
Po przywróceniu maszyny wirtualnej Skonfiguruj publiczny adres IP.

1.  W polu wyszukiwania wprowadź **publiczny adres IP**.

    ![Lista publicznych adresów IP](./media/oracle-backup-recovery/create_ip_00.png)

2.  W bloku **publiczne adresy IP** kliknij pozycję **Dodaj**. W bloku **Utwórz publiczny adres IP** w polu **Nazwa**wybierz publiczną nazwę IP. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**. Następnie kliknij pozycję **Utwórz**.

    ![Utwórz adres IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Aby skojarzyć publiczny adres IP z interfejsem sieciowym dla maszyny wirtualnej, Wyszukaj i wybierz pozycję **myVMip**. Następnie kliknij pozycję **Skojarz**.

    ![Skojarz adres IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  W obszarze **Typ zasobu**wybierz pozycję **interfejs sieciowy**. Wybierz interfejs sieciowy, który jest używany przez wystąpienie myVM, a następnie kliknij przycisk **OK**.

    ![Wybieranie wartości typu zasobu i karty sieciowej](./media/oracle-backup-recovery/create_ip_03.png)

5.  Wyszukaj i Otwórz wystąpienie myVM, które jest przewidziane z portalu. Adres IP skojarzony z maszyną wirtualną pojawia się w bloku **Przegląd** myVM.

    ![Wartość adresu IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4. Nawiązywanie połączenia z maszyną wirtualną

*   Aby nawiązać połączenie z maszyną wirtualną, użyj następującego skryptu:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5. Sprawdzanie, czy baza danych jest dostępna
*   Aby przetestować ułatwienia dostępu, użyj następującego skryptu:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Jeśli polecenie **uruchamiania** bazy danych generuje błąd, aby odzyskać bazę danych, zobacz [krok 6. Użyj RMAN do odzyskania bazy danych](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6. (opcjonalnie) Użyj RMAN do odzyskania bazy danych
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

Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej platformy Azure z systemem Linux zostało zakończone.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Jeśli maszyna wirtualna nie jest już potrzebna, możesz użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Eksplorowanie przykładów interfejsu wiersza polecenia platformy Azure wdrożenia maszyny wirtualnej](../../linux/cli-samples.md)



