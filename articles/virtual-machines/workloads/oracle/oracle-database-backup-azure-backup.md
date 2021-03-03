---
title: Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 19c na maszynie wirtualnej z systemem Linux przy użyciu systemu Azure Backup
description: Dowiedz się, jak utworzyć kopię zapasową i odzyskać bazę danych Oracle Database 19c przy użyciu usługi Azure Backup.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673886"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 19c na maszynie wirtualnej z systemem Linux przy użyciu systemu Azure Backup

W tym artykule pokazano, jak używać Azure Backup do tworzenia migawek dysków maszyn wirtualnych, takich jak pliki bazy danych i szybkie odzyskiwanie. Za pomocą Azure Backup można tworzyć pełne migawki dysków odpowiednie jako kopie zapasowe, które są przechowywane w [magazynie Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md).  Azure Backup również zapewnia kopie zapasowe spójne z aplikacją, co zapewnia, że dodatkowe poprawki nie są wymagane do przywrócenia danych. Przywracanie danych spójnych na poziomie aplikacji skraca czas przywracania, co pozwala szybko powrócić do stanu roboczego.

> [!div class="checklist"]
>
> * Utwórz kopię zapasową bazy danych za pomocą kopii zapasowej spójnej z aplikacją
> * Przywracanie i odzyskiwanie bazy danych z punktu odzyskiwania
> * Przywracanie maszyny wirtualnej z punktu odzyskiwania

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Aby wykonać proces tworzenia kopii zapasowej i odzyskiwania, należy najpierw utworzyć maszynę wirtualną z systemem Linux, która ma zainstalowane wystąpienie Oracle Database 19c. Obraz portalu Marketplace aktualnie używany do tworzenia maszyny wirtualnej to  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Najnowsza**. Postępuj zgodnie z instrukcjami w temacie [Tworzenie bazy danych firmy Oracle — szybki start](./oracle-database-quick-create.md) , aby utworzyć bazę danych Oracle w celu wykonania tego samouczka.


## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

Aby przygotować środowisko, wykonaj następujące kroki:

1. Nawiąż połączenie z maszyną wirtualną.
1. Przygotuj bazę danych.

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Aby utworzyć sesję Secure Shell (SSH) z maszyną wirtualną, użyj następującego polecenia. Zastąp adres IP i kombinację nazwy hosta `<publicIpAddress>` wartością dla maszyny wirtualnej.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Przełącz do *głównego* użytkownika:

   ```bash
   sudo su -
   ```
    
1. Dodaj użytkownika Oracle do pliku */etc/sudoers* :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Przygotowywanie bazy danych

W tym kroku przyjęto założenie, że masz wystąpienie Oracle (*test*), które działa na maszynie wirtualnej o nazwie *vmoracle19c*.

1. Przełącz użytkownika na użytkownika *Oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
2. Przed nawiązaniem połączenia należy ustawić zmienną środowiskową ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Należy również dodać zmienną ORACLE_SID do `oracle` `.bashrc` pliku users dla przyszłych logowań przy użyciu następującego polecenia:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Uruchom odbiornik Oracle, jeśli nie jest jeszcze uruchomiony:

    ```output
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

4.  Utwórz lokalizację szybkiego odzyskiwania (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Połącz z bazą danych:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Uruchom bazę danych, jeśli nie jest jeszcze uruchomiona:

    ```bash
    SQL> startup
    ```

7.  Ustaw zmienne środowiskowe bazy danych dla obszaru szybkiego odzyskiwania:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Upewnij się, że baza danych jest w trybie dziennika archiwizacji, aby włączyć tworzenie kopii zapasowych online.

    Najpierw sprawdź stan archiwum dzienników:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Jeśli jest w trybie NOARCHIVELOG, uruchom następujące polecenia:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Utwórz tabelę do testowania operacji tworzenia kopii zapasowej i przywracania:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Skonfiguruj RMAN kopii zapasowej w obszarze szybkiego odzyskiwania znajdującym się na dysku maszyny wirtualnej:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Potwierdź szczegóły zmiany konfiguracji:

    ```bash
    RMAN> show all;
    ```    

12.  Teraz uruchom kopię zapasową. Następujące polecenie zajmie pełną kopię zapasową bazy danych, w tym pliki dziennika archiwalne, jako zestawu kopii zapasowych w formacie skompresowanym:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Korzystanie z Azure Backup (wersja zapoznawcza)

Usługa Azure Backup udostępnia proste, bezpieczne i ekonomiczne rozwiązania do wykonywania kopii zapasowych danych i odzyskiwania ich z chmury platformy Microsoft Azure. usługa Azure Backup umożliwia wykonywanie niezależnych i odizolowanych kopii zapasowych, co chroni przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi funkcjami zarządzania punktami odzyskiwania. Konfiguracja i skalowalność są proste, kopie zapasowe są optymalizowane i można je łatwo przywrócić w razie potrzeby.

Usługa Azure Backup udostępnia [platformę](../../../backup/backup-azure-linux-app-consistent.md) do osiągnięcia spójności aplikacji podczas tworzenia kopii zapasowych maszyn wirtualnych z systemami Windows i Linux dla różnych aplikacji, takich jak Oracle, MySQL, Mongo DB i PostGreSQL. Obejmuje to Wywoływanie skryptu wstępnego (w celu przełączenia w tryb spoczynku) przed wykonaniem migawki dysków i wywołaniem skryptu po zakończeniu wykonywania operacji po wykonaniu migawki, aby przywrócić aplikacje do trybu normalnego. Podczas gdy przykładowe skrypty wstępne i skrypty są udostępniane w serwisie GitHub, jego zadaniem jest tworzenie i obsługa tych skryptów.

Teraz Azure Backup zapewnia ulepszone środowisko skryptów i skryptów po skrypcie (**który jest obecnie dostępny w wersji zapoznawczej**), gdzie usługa Azure Backup udostępnia spakowane skrypty wstępne i skrypty dla wybranych aplikacji. Azure Backup użytkownicy muszą mieć nazwę aplikacji, a następnie kopia zapasowa maszyny wirtualnej platformy Azure automatycznie wywoła odpowiednie skrypty wstępne. Spakowane przed skryptami i po nim skrypty będą obsługiwane przez zespół Azure Backup i dlatego użytkownicy będą mogli zapewnić pomoc techniczną, własność i ważność tych skryptów. Obecnie obsługiwane aplikacje dla rozszerzonej struktury to *Oracle* i *MySQL*.

W tej sekcji zostanie użyta Azure Backup ulepszona platforma do tworzenia migawek spójnych na poziomie aplikacji dla uruchomionej maszyny wirtualnej i bazy danych Oracle. Baza danych zostanie umieszczona w trybie tworzenia kopii zapasowej, co pozwala na wypróbowanie bezczynnościowej kopii zapasowej online, gdy Azure Backup wykonuje migawkę dysków maszyny wirtualnej. Migawka będzie pełną kopią magazynu, a nie przyrostową lub kopią na migawce zapisu, więc jest efektywnym czynnikiem do przywrócenia bazy danych z programu. Zaletą Azure Backup korzystania z migawek spójnych na poziomie aplikacji jest to, że bardzo szybkie nie zależą od dużej ilości bazy danych, a migawka może być używana do operacji przywracania, gdy tylko zostanie wykonana, bez konieczności oczekiwania na przekazanie jej do magazynu Recovery Services.

Aby użyć Azure Backup do utworzenia kopii zapasowej bazy danych programu, wykonaj następujące kroki:

1. Przygotuj środowisko do tworzenia kopii zapasowej spójnej na poziomie aplikacji.
1. Konfigurowanie kopii zapasowych spójnych na poziomie aplikacji.
1. Wyzwalanie kopii zapasowej maszyny wirtualnej spójnej na poziomie aplikacji.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Przygotowywanie środowiska do tworzenia kopii zapasowej spójnej na poziomie aplikacji

1. Przełącz do *głównego* użytkownika:

   ```bash
   sudo su -
   ```

1. Utwórz nowego użytkownika kopii zapasowej:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Skonfiguruj środowisko użytkownika kopii zapasowej:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Skonfiguruj uwierzytelnianie zewnętrzne dla nowego użytkownika kopii zapasowej. Użytkownik kopii zapasowej musi mieć możliwość uzyskania dostępu do bazy danych przy użyciu uwierzytelniania zewnętrznego, aby nie zakwestionować hasła.

   Najpierw wróć do użytkownika *Oracle* :

   ```bash
   su - oracle
   ```

   Zaloguj się do bazy danych przy użyciu polecenia sqlplus i sprawdź ustawienia domyślne uwierzytelniania zewnętrznego:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Dane wyjściowe powinny wyglądać podobnie do tego przykładu: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Teraz Utwórz użytkownika bazy danych *azbackup* uwierzytelniany zewnętrznie i Udziel uprawnień sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Jeśli wystąpi błąd `ORA-46953: The password file is not in the 12.2 format.`  podczas uruchamiania `GRANT` instrukcji, wykonaj następujące kroki, aby zmigrować plik orapwd do formatu 12,2:
   >
   > 1. Wyjdź z sqlplus.
   > 1. Przenieś plik hasła o starym formacie do nowej nazwy.
   > 1. Migruj plik hasła.
   > 1. Usuń stary plik.
   > 1. Uruchom następujące polecenie:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Ponownie uruchom `GRANT` operację w sqlplus.
   >
   
4. Utwórz procedurę przechowywaną do rejestrowania komunikatów kopii zapasowych w dzienniku alertów bazy danych:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Konfigurowanie kopii zapasowych spójnych na poziomie aplikacji  

1. Przełącz do *głównego* użytkownika:

   ```bash
   sudo su -
   ```

2. Sprawdź folder "etc/Azure". Jeśli ta wartość nie istnieje, Utwórz katalog roboczy kopii zapasowej spójnej na poziomie aplikacji:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Sprawdź "obciążenie. conf" w folderze. Jeśli nie istnieje, Utwórz plik w katalogu */etc/Azure* o nazwie obciążeni *. conf* z następującą zawartością, która musi rozpoczynać się od `[workload]` . Jeśli plik już istnieje, po prostu Edytuj pola tak, aby odpowiadały następującej zawartości. W przeciwnym razie następujące polecenie utworzy plik i wypełni zawartość:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Wyzwalanie kopii zapasowej maszyny wirtualnej spójnej na poziomie aplikacji

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  W Azure Portal przejdź do grupy zasobów **RG-Oracle** i kliknij maszynę wirtualną **vmoracle19c**.

2.  W bloku **kopia zapasowa** Utwórz nowy **Magazyn Recovery Services** w grupie zasobów **RG-Oracle** o nazwie Moja **Magazyn**.
    Dla **opcji wybierz zasady tworzenia kopii zapasowych** Użyj **(New) DailyPolicy**. Jeśli chcesz zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania, wybierz opcję **Utwórz nowe zasady** .

    ![Dodawanie strony do magazynów Recovery Services](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Aby kontynuować, kliknij przycisk **Włącz kopię zapasową**.

    > [!IMPORTANT]
    > Po kliknięciu przycisku **Włącz kopię zapasową** proces tworzenia kopii zapasowej nie zostanie uruchomiony do czasu wygaśnięcia zaplanowanego czasu. Aby skonfigurować natychmiastową kopię zapasową, wykonaj następny krok.

4. Na stronie Grupa zasobów kliknij nowo utworzony **magazyn** Recovery Services magazynu. Wskazówka: może być konieczne odświeżenie strony, aby ją wyświetlić.

5.  W bloku moje **magazyny — elementy kopii zapasowej** w obszarze **Liczba elementów kopii** zapasowej wybierz liczbę elementów kopii zapasowej.

    ![Strona szczegółów magazynu Recovery Services magazynów](./media/oracle-backup-recovery/recovery-service-02.png)

6.  W bloku **elementy kopii zapasowej (maszyna wirtualna platformy Azure)** w prawej części strony kliknij przycisk wielokropka (**...**), a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.

    ![Tworzenie kopii zapasowej Recovery Services magazynów teraz — polecenie](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Zaakceptuj wartość domyślną zachowaj kopię zapasową do i kliknij przycisk **OK** . Poczekaj na zakończenie procesu tworzenia kopii zapasowej. 

    Aby wyświetlić stan zadania tworzenia kopii zapasowej, kliknij przycisk **zadania tworzenia kopii zapasowej**.

    ![Strona zadania magazynu Recovery Services](./media/oracle-backup-recovery/recovery-service-04.png)

    Stan zadania tworzenia kopii zapasowej zostanie wyświetlony na poniższej ilustracji:

    ![Strona zadań magazynu Recovery Services ze stanem](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Należy pamiętać, że podczas wykonywania migawki trwa zaledwie kilka sekund, a zadanie tworzenia kopii zapasowej nie zostanie ukończone do momentu zakończenia transferu.

8. W przypadku kopii zapasowej spójnej na poziomie aplikacji należy rozwiązać wszelkie błędy w pliku dziennika. Plik dziennika znajduje się w lokalizacji/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Utwórz magazyn usługi Recovery Services:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Włącz ochronę kopii zapasowej dla maszyny wirtualnej:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Wyzwól wykonywanie kopii zapasowej do uruchomienia teraz, zamiast czekać, aż kopia zapasowa zostanie wyzwolona zgodnie z harmonogramem domyślnym (5 godzin UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Postęp zadania tworzenia kopii zapasowej można monitorować przy użyciu `az backup job list` i `az backup job show` .

---

## <a name="recovery"></a>Odzyskiwania

Aby odzyskać bazę danych, wykonaj następujące kroki:

1. Usuń pliki bazy danych.
1. Wygeneruj skrypt przywracania z magazynu Recovery Services.
1. Zainstaluj punkt przywracania.
1. Wykonaj odzyskiwanie.

### <a name="remove-the-database-files"></a>Usuń pliki bazy danych 

W dalszej części tego artykułu dowiesz się, jak przetestować proces odzyskiwania. Aby można było przetestować proces odzyskiwania, należy usunąć pliki bazy danych.

1.  Zamknij wystąpienie programu Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Usuń datafiles i kopie zapasowe:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generowanie skryptu przywracania z magazynu Recovery Services

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal Wyszukaj element magazyny Recovery Services *magazynu, a* następnie wybierz go.

    ![Magazyn Recovery Services elementy kopii zapasowej magazynu](./media/oracle-backup-recovery/recovery-service-06.png)

2. W bloku **Przegląd** wybierz pozycję **elementy kopii zapasowej** i wybierz **maszynę wirtualną platformy Azure**, która powinna zawierać liczbę elementów kopii zapasowej Anon-zero.

    ![Magazyn Recovery Services liczba elementów kopii zapasowej maszyny wirtualnej platformy Azure](./media/oracle-backup-recovery/recovery-service-07.png)

3. Na stronie elementy kopii zapasowej (Azure Virtual Machines) znajduje się lista **vmoracle19c** maszyny wirtualnej. Kliknij przycisk wielokropka po prawej stronie, aby wyświetlić menu, a następnie wybierz pozycję **odzyskiwanie plików**.

    ![Zrzut ekranu przedstawiający stronę odzyskiwania plików Recovery Services magazynów](./media/oracle-backup-recovery/recovery-service-08.png)

4. W okienku **odzyskiwanie plików (wersja zapoznawcza)** kliknij pozycję **Pobierz skrypt**. Następnie Zapisz plik Download (. PR) do folderu na komputerze klienckim. Zostanie wygenerowane hasło do uruchomienia skryptu. Skopiuj hasło do pliku do późniejszego użycia. 

    ![Pobieranie pliku skryptu — opcje zapisywania](./media/oracle-backup-recovery/recovery-service-09.png)

5. Skopiuj plik. PR do maszyny wirtualnej.

    W poniższym przykładzie pokazano, jak za pomocą polecenia Secure Copy (SCP) przenieść plik na maszynę wirtualną. Możesz również skopiować zawartość do schowka, a następnie wkleić zawartość do nowego pliku, który jest skonfigurowany na maszynie wirtualnej.

    > [!IMPORTANT]
    > W poniższym przykładzie upewnij się, że Zaktualizowano wartości adresów IP i folderów. Wartości muszą być mapowane do folderu, w którym zapisano plik.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę punktów odzyskiwania dla maszyny wirtualnej, użyj polecenie AZ Backup Recovery Point list. W tym przykładzie wybieramy najnowszy punkt odzyskiwania dla maszyny wirtualnej o nazwie myVM, która jest chroniona w myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Aby uzyskać skrypt umożliwiający połączenie z punktem odzyskiwania — innymi słowy, zainstalowanie go — na maszynie wirtualnej, użyj polecenia az backup restore files mount-rp. Poniższy przykład umożliwia pobranie skryptu dla maszyny wirtualnej o nazwie myVM, która jest chroniona w myRecoveryServicesVault.

Zastąp ciąg myRecoveryPointName nazwą punktu odzyskiwania uzyskaną przy użyciu poprzedniego polecenia:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Skrypt zostanie pobrany i zostanie wyświetlone hasło, zgodnie z poniższym przykładem:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Skopiuj plik. PR do maszyny wirtualnej.

W poniższym przykładzie pokazano, jak za pomocą polecenia Secure Copy (SCP) przenieść plik na maszynę wirtualną. Możesz również skopiować zawartość do schowka, a następnie wkleić zawartość do nowego pliku, który jest skonfigurowany na maszynie wirtualnej.

> [!IMPORTANT]
> W poniższym przykładzie upewnij się, że Zaktualizowano wartości adresów IP i folderów. Wartości muszą być mapowane do folderu, w którym zapisano plik.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Instalowanie punktu przywracania

1. Utwórz punkt instalacji przywracania i skopiuj do niego skrypt.

    W poniższym przykładzie Utwórz katalog */Restore* dla migawki, w której ma zostać wykonana instalacja, Przenieś plik do katalogu i Zmień plik tak, aby należał do użytkownika root i został utworzony jako plik wykonywalny.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Teraz wykonaj skrypt, aby przywrócić kopię zapasową. Zostanie wyświetlony monit o podanie hasła wygenerowanego w Azure Portal. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Poniższy przykład pokazuje, co powinno być widoczne po uruchomieniu powyższego skryptu. Po wyświetleniu monitu o kontynuowanie wprowadź **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Zatwierdzono dostęp do zainstalowanych woluminów.

    Aby wyjść, wprowadź **q**, a następnie wyszukaj woluminy zainstalowane. Aby utworzyć listę dodanych woluminów, w wierszu polecenia wprowadź wartość **DF-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Przeprowadzanie odzyskiwania

1. Skopiuj brakujące pliki kopii zapasowej z powrotem do obszaru szybkie odzyskiwanie:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Następujące polecenia używają RMAN do przywrócenia brakującej datafiles i odzyskania bazy danych:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Sprawdź, czy zawartość bazy danych została w pełni odzyskana:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Odinstaluj punkt przywracania.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    W Azure Portal w bloku **odzyskiwanie plików (wersja zapoznawcza)** kliknij pozycję **odinstalowanie dysków**.

    ![Odinstalowywanie dysków — polecenie](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Można również odinstalować woluminy odzyskiwania, uruchamiając skrypt języka Python ponownie z opcją **-Clean** .

## <a name="restore-the-entire-vm"></a>Przywróć całą maszynę wirtualną

Zamiast przywracać usunięte pliki z magazynów Recovery Services, można przywrócić całą maszynę wirtualną.

Aby przywrócić całą maszynę wirtualną, wykonaj następujące kroki:

1. Zatrzymaj i Usuń vmoracle19c.
1. Odzyskaj maszynę wirtualną.
1. Ustaw publiczny adres IP.
1. Nawiąż połączenie z maszyną wirtualną.
1. Uruchom bazę danych w celu zainstalowania etapu i przeprowadzenia odzyskiwania.

### <a name="stop-and-delete-vmoracle19c"></a>Zatrzymywanie i usuwanie vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal przejdź do maszyny wirtualnej **vmoracle19c** , a następnie wybierz pozycję **Zatrzymaj**.

1. Gdy maszyna wirtualna nie jest już uruchomiona, wybierz pozycję **Usuń** , a następnie przycisk **tak**.

   ![Polecenie usunięcia magazynu](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zatrzymaj i Cofnij przydział maszyny wirtualnej:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Usuń maszynę wirtualną. Wprowadź "y" po wyświetleniu monitu:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Odzyskiwanie maszyny wirtualnej

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Utwórz konto magazynu na potrzeby przemieszczania w Azure Portal.

   1. W Azure Portal wybierz pozycję **+ Utwórz zasób** , a następnie wyszukaj i wybierz pozycję **konto magazynu**.
    
      ![Zrzut ekranu pokazujący lokalizację, w której ma zostać utworzony zasób.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Na stronie Tworzenie konta magazynu wybierz istniejącą grupę zasobów **RG-Oracle**, nadaj nazwę swojemu kontu magazynu **oracrestore** i wybierz pozycję **Storage v2 (GeneralPurpose v2)** dla rodzaju konta. Zmień replikację na **Magazyn lokalnie nadmiarowy (LRS)** i ustaw wydajność na **Standard**. Upewnij się, że lokalizacja jest ustawiona na ten sam region, co w przypadku wszystkich innych zasobów w grupie zasobów. 
    
      ![Strona dodawania konta magazynu](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Kliknij przycisk przegląd + Utwórz, a następnie kliknij przycisk Utwórz.

2. W Azure Portal Wyszukaj element magazyny Recovery Services *magazynu, a* następnie kliknij go.

    ![Magazyn Recovery Services elementy kopii zapasowej magazynu](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  W bloku **Przegląd** wybierz pozycję **elementy kopii zapasowej** i wybierz **maszynę wirtualną platformy Azure**, która powinna zawierać liczbę elementów kopii zapasowej Anon-zero.

    ![Magazyn Recovery Services liczba elementów kopii zapasowej maszyny wirtualnej platformy Azure](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Na liście elementy kopii zapasowej (Azure Virtual Machines) znajduje się Strona **vmoracle19c** maszyny wirtualnej. Kliknij nazwę maszyny wirtualnej.

    ![Strona maszyny wirtualnej odzyskiwania](./media/oracle-backup-recovery/recover-vm-02.png)

5.  W bloku **vmoracle19c** wybierz punkt przywracania z typem spójności **aplikacji spójny** i kliknij przycisk wielokropka (**...**) po prawej stronie, aby wyświetlić menu.  W menu kliknij pozycję **Przywróć maszynę wirtualną**.

    ![Polecenie Restore VM](./media/oracle-backup-recovery/recover-vm-03.png)

6.  W bloku **przywracanie maszyny wirtualnej** wybierz pozycję **Utwórz nową** **maszynę wirtualną i Utwórz nową**. Wprowadź nazwę maszyny wirtualnej **vmoracle19c** i wybierz sieć wirtualną **vmoracle19cVNET**, podsieć zostanie automatycznie wypełniona dla Ciebie w oparciu o wybór sieci wirtualnej. Proces przywracania maszyny wirtualnej wymaga konta usługi Azure Storage w tej samej grupie zasobów i regionie. Możesz wybrać wcześniej skonfigurowane konto magazynu **orarestore** .

    ![Przywróć wartości konfiguracyjne](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Aby przywrócić maszynę wirtualną, kliknij przycisk **Przywróć** .

8.  Aby wyświetlić stan procesu przywracania, kliknij pozycję **zadania**, a następnie kliknij pozycję **zadania tworzenia kopii zapasowej**.

    ![Stan zadań kopii zapasowej — polecenie](./media/oracle-backup-recovery/recover-vm-05.png)

    Kliknij operację przywracania **w toku** , aby wyświetlić stan procesu przywracania:

    ![Stan procesu przywracania](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować konto magazynu i udział plików, uruchom następujące polecenia w interfejsie wiersza polecenia platformy Azure.

1. Utwórz konto magazynu w tej samej grupie zasobów i lokalizacji co maszyna wirtualna:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Pobierz listę dostępnych punktów odzyskiwania. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Przywróć punkt odzyskiwania na koncie magazynu. Zastąp `<myRecoveryPointName>` punkt odzyskiwania z listy wygenerowanej w poprzednim kroku:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Pobierz szczegóły zadania przywracania. Następujące polecenie pobiera więcej szczegółów dotyczących wyzwolonego zadania, w tym jego nazwy, co jest konieczne do pobrania identyfikatora URI szablonu. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Dane wyjściowe będą wyglądać podobnie do tego `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Pobierz szczegóły identyfikatora URI, który ma być używany do ponownego tworzenia maszyny wirtualnej. Zastąp nazwę zadania przywracania z poprzedniego kroku dla `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Dane wyjściowe są podobne do tego:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Nazwa szablonu, która znajduje się na końcu identyfikatora URI obiektu BLOB szablonu, który jest w tym przykładzie `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` , i nazwą kontenera obiektów blob, która znajduje się na `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` liście. 

   Użyj tych wartości w poniższym poleceniu, aby przypisać zmienne w przygotowaniu do tworzenia maszyny wirtualnej. Klucz sygnatury dostępu współdzielonego jest generowany dla kontenera magazynu z 30-minutowym czasem trwania.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Teraz Wdróż szablon w celu utworzenia maszyny wirtualnej.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Zostanie wyświetlony monit o podanie nazwy dla maszyny wirtualnej.

---

### <a name="set-the-public-ip-address"></a>Ustaw publiczny adres IP

Po przywróceniu maszyny wirtualnej należy ponownie przypisać oryginalny adres IP do nowej maszyny wirtualnej.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  W Azure Portal przejdź do maszyny wirtualnej **vmoracle19c**. Zobaczysz, że został mu przypisany nowy publiczny adres IP i karta sieciowa podobna do vmoracle19c-nic-XXXXXXXXXXXX, ale nie ma adresu DNS. Gdy oryginalna maszyna wirtualna została usunięta, publiczny adres IP i karta sieciowa są zachowywane, a następne kroki zostaną dołączone ponownie do nowej maszyny wirtualnej.

    ![Lista publicznych adresów IP](./media/oracle-backup-recovery/create-ip-01.png)

2.  Zatrzymywanie maszyny wirtualnej

    ![Utwórz adres IP](./media/oracle-backup-recovery/create-ip-02.png)

3.  Przejdź do **sieci**

    ![Skojarz adres IP](./media/oracle-backup-recovery/create-ip-03.png)

4.  Kliknij pozycję **Dołącz interfejs sieciowy**, wybierz oryginalną kartę sieciową * * vmoracle19cVMNic, z którą nadal jest skojarzony oryginalny publiczny adres IP, a następnie kliknij przycisk **OK** .

    ![Wybieranie wartości typu zasobu i karty sieciowej](./media/oracle-backup-recovery/create-ip-04.png)

5.  Teraz musisz odłączyć kartę sieciową, która została utworzona przy użyciu operacji przywracania maszyny wirtualnej, ponieważ jest ona skonfigurowana jako interfejs podstawowy. Kliknij przycisk **Odłącz interfejs sieciowy** i wybierz nową kartę sieciową podobną do **vmoracle19c-nic-xxxxxxxxxxxx**, a następnie kliknij przycisk **OK** .

    ![Zrzut ekranu pokazujący, gdzie wybrać opcję Odłącz interfejs sieciowy.](./media/oracle-backup-recovery/create-ip-05.png)
    
    Utworzona przez siebie maszyna wirtualna będzie miała teraz oryginalną kartę sieciową, która jest skojarzona z oryginalnymi regułami adresów IP i sieciowych grup zabezpieczeń
    
    ![Wartość adresu IP](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Wróć do **omówienia** , a następnie kliknij przycisk **Start** 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zatrzymaj i Cofnij przydział maszyny wirtualnej:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Wyświetl bieżącą, przytworzoną kartę sieciową maszyny wirtualnej

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Dane wyjściowe będą wyglądać podobnie do tego, co spowoduje wyświetlenie listy wygenerowanej nazwy karty sieciowej jako `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Dołącz oryginalną kartę sieciową, która powinna mieć nazwę `<VMName>VMNic` , w tym przypadku `vmoracle19cVMNic` . Oryginalny publiczny adres IP jest nadal dołączony do tej karty sieciowej i zostanie przywrócony do maszyny wirtualnej po odłączeniu karty sieciowej. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Odłączanie wygenerowanej karty sieciowej

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Uruchom maszynę wirtualną:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Aby nawiązać połączenie z maszyną wirtualną, użyj następującego skryptu:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Uruchom bazę danych, aby zainstalować etap i wykonać odzyskiwanie

1. Może się okazać, że wystąpienie jest uruchomione, ponieważ próba uruchomienia bazy danych przy rozruchu maszyny wirtualnej została podjęta przy użyciu autostartu. Jednak baza danych wymaga odzyskiwania i może być tylko na etapie instalacji, więc najpierw należy uruchomić polecenie przygotowawcze.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Sprawdź, czy zawartość bazy danych została odzyskana:

    ```bash
    SQL> select * from scott.scott_table;
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