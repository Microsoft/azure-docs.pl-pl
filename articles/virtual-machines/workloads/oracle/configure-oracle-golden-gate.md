---
title: Implementowanie Złotej Bramy Oracle na maszynie Wirtualnej systemu Azure z systemem Linux | Dokumenty firmy Microsoft
description: Szybko uruchamiaj i uruchamiaj oracle golden gate w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: ae6bfb0ab0208d0f778476c9f0959b0c0f1d6471
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683729"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementowanie Złotej Bramy Oracle na maszynie Wirtualnej systemu Azure z systemem Linux 

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku opisano, jak używać interfejsu wiersza polecenia platformy Azure do wdrażania bazy danych Oracle 12c z obrazu galerii portalu portalu portalu Azure Marketplace. 

W tym dokumencie pokazano krok po kroku, jak tworzyć, instalować i konfigurować Oracle Golden Gate na maszynie Wirtualnej platformy Azure. W tym samouczku dwie maszyny wirtualne są konfigurowane w dostępności ustawionej w jednym regionie. Ten sam samouczek może służyć do konfigurowania OracleGolden Gate dla maszyn wirtualnych w różnych strefach dostępności w jednym regionie platformy Azure lub dla konfiguracji maszyn wirtualnych w dwóch różnych regionach.

Przed rozpoczęciem upewnij się, że interfejs wiersza polecenia platformy Azure został zainstalowany. Aby uzyskać więcej informacji, zobacz [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli) (Przewodnik instalacji interfejsu wiersza polecenia platformy Azure).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

Aby wykonać instalację Oracle Golden Gate, należy utworzyć dwie maszyny wirtualne platformy Azure na tym samym zestawie dostępności. Obraz marketplace używany do tworzenia maszyn wirtualnych to **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Musisz również znać edytor Unix vi i mieć podstawową wiedzę na temat x11 (X Windows).

Poniżej przedstawiono podsumowanie konfiguracji środowiska:
> 
> |  | **Lokacja główna** | **Replikacja witryny** |
> | --- | --- | --- |
> | **Wydanie Oracle** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Nazwa maszyny** |myVM1 |myVM2 |
> | **System operacyjny** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |Płyta CDB1 |Płyta CDB1 |
> | **Schemat replikacji** |TEST|TEST |
> | **Golden Gate właściciel / replika** |C##GGADMIN |PRZEDSTAWICIEL |
> | **Proces Złotej Bramy** |WYŁUDNIK |REPORA|


### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [az login.](/cli/azure/reference-index) Następnie postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to kontener logiczny, w którym są wdrażane zasoby platformy Azure i z którego można nimi zarządzać. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Poniższy krok jest opcjonalny, ale zalecane. Aby uzyskać więcej informacji, zobacz [Przewodnik po zestawach dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Poniższy przykład tworzy dwie `myVM1` maszyny `myVM2`wirtualne o nazwie i . Utwórz klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Tworzenie myVM1 (podstawowy):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure pokazuje informacje podobne do poniższego przykładu. (Zanotuj `publicIpAddress`. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej.)

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>Tworzenie myVM2 (replikacja):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Zwróć uwagę `publicIpAddress` na to, jak również po jego utworzeniu.

### <a name="open-the-tcp-port-for-connectivity"></a>Otwieranie portu TCP w celu uzyskania łączności

Następnym krokiem jest skonfigurowanie zewnętrznych punktów końcowych, które umożliwiają zdalny dostęp do bazy danych Oracle. Aby skonfigurować zewnętrzne punkty końcowe, uruchom następujące polecenia.

#### <a name="open-the-port-for-myvm1"></a>Otwórz port dla myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Wyniki powinny wyglądać podobnie do następującej odpowiedzi:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>Otwórz port dla myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp adres IP adresem `publicIpAddress` swojej maszyny wirtualnej.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Tworzenie bazy danych na myVM1 (podstawowy)

Oprogramowanie Oracle jest już zainstalowane na obrazie Marketplace, więc następnym krokiem jest zainstalowanie bazy danych. 

Uruchom oprogramowanie jako superużytnik "oracle":

```bash
sudo su - oracle
```

Tworzenie bazy danych:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Dane wyjściowe powinny wyglądać podobnie do następującej odpowiedzi:

```output
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Ustaw zmienne ORACLE_SID i ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcjonalnie można dodać ORACLE_HOME i ORACLE_SID do pliku bashrc, aby te ustawienia były zapisywane dla przyszłych logów:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Uruchamianie odbiornika Oracle

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Tworzenie bazy danych na myVM2 (replikacja)

```bash
sudo su - oracle
```

Tworzenie bazy danych:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```

Ustaw zmienne ORACLE_SID i ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Opcjonalnie można dodać ORACLE_HOME i ORACLE_SID do pliku bashrc, aby te ustawienia były zapisywane dla przyszłych logów.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Uruchamianie odbiornika Oracle

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurowanie Złotej Bramy 
Aby skonfigurować Golden Gate, należy wykonać kroki opisane w tej sekcji.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Włącz tryb dziennika archiwum na myVM1 (podstawowy)

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
```
Włącz rejestrowanie siłowe i upewnij się, że jest obecny co najmniej jeden plik dziennika.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Pobierz oprogramowanie Golden Gate
Aby pobrać i przygotować oprogramowanie Oracle Golden Gate, wykonaj następujące czynności:

1. Pobierz plik **fbo_ggs_Linux_x64_shiphome.zip** ze [strony pobierania Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Pod tytułem pobierania **Oracle GoldenGate 12.x.x.x dla Oracle Linux x86-64,** powinien być zestaw plików .zip do pobrania.

2. Po pobraniu plików zip na komputer kliencki użyj protokołu Secure Copy Protocol (SCP), aby skopiować pliki na maszynę wirtualną:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Przenieś pliki zip do folderu **/opt.** Następnie zmień właściciela plików w następujący sposób:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Rozpaj pliki (zainstaluj narzędzie rozpakaj linuksa, jeśli nie jest jeszcze zainstalowane):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Zmień uprawnienie:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Przygotowanie klienta i maszyny Wirtualnej do uruchomienia x11 (tylko dla klientów systemu Windows)
Jest to opcjonalny krok. Możesz pominąć ten krok, jeśli używasz klienta systemu Linux lub masz już konfigurację x11.

1. Pobierz PuTTY i Xming na komputer z systemem Windows:

   * [Pobierz PuTTY](https://www.putty.org/)
   * [Pobieranie Xming](https://xming.en.softonic.com/)

2. Po zainstalowaniu PuTTY, w folderze PuTTY (na przykład C:\Program Files\PuTTY), uruchom puttygen.exe (PuTTY Key Generator).

3. W PuTTY Key Generator:

   - Aby wygenerować klawisz, wybierz przycisk **Generuj.**
   - Skopiuj zawartość klawisza (**Ctrl+C**).
   - Wybierz przycisk **Zapisz klucz prywatny.**
   - Zignoruj wyświetlone ostrzeżenie, a następnie wybierz **przycisk OK**.

   ![Zrzut ekranu przedstawiający stronę generatora kluczy PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4. Na maszynie wirtualnej uruchom następujące polecenia:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Tworzenie pliku o nazwie **authorized_keys**. Wklej zawartość klucza w tym pliku, a następnie zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać `ssh-rsa`ciąg . Ponadto zawartość klucza musi być pojedynczym wierszem tekstu.
   >  

6. Uruchom program PuTTY. W **okienku Kategoria** wybierz **pozycję Podłączanie** > **Auth****SSH** > . W **polu Plik klucza prywatnego do uwierzytelniania** przejdź do klucza wygenerowanego wcześniej.

   ![Zrzut ekranu przedstawiający stronę Ustaw klucz prywatny](./media/oracle-golden-gate/setprivatekey.png)

7. W **okienku Kategoria** wybierz **pozycję Połączenie** > **SSH** > **X11**. Następnie wybierz pole **Włącz przekazywanie X11.**

   ![Zrzut ekranu przedstawiający stronę Włącz X11](./media/oracle-golden-gate/enablex11.png)

8. W okienku **Kategoria** przejdź do **strony Sesja**. Wprowadź informacje o hoście, a następnie wybierz pozycję **Otwórz**.

   ![Zrzut ekranu przedstawiający stronę sesji](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Zainstaluj oprogramowanie Golden Gate

Aby zainstalować Oracle Golden Gate, wykonaj następujące kroki:

1. Zaloguj się jako wyrocznia. (Powinieneś być w stanie zalogować się bez monitu o podanie hasła). Upewnij się, że Xming jest uruchomiony przed rozpoczęciem instalacji.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Wybierz opcję "Oracle GoldenGate for Oracle Database 12c". Następnie wybierz **przycisk Dalej,** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Instalator Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Zmień lokalizację oprogramowania. Następnie zaznacz pole **Start Manager** i wprowadź lokalizację bazy danych. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Zmień katalog zapasów, a następnie wybierz pozycję **Dalej,** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na ekranie **Podsumowanie** wybierz pozycję **Zainstaluj,** aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Instalator Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Może zostać wyświetlony monit o uruchomienie skryptu jako "root". Jeśli tak, otwórz oddzielną sesję, ssh do maszyny Wirtualnej, sudo do katalogu głównego, a następnie uruchom skrypt. Wybierz **przycisk OK** kontynuuj.

   ![Zrzut ekranu przedstawiający stronę Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Po zakończeniu instalacji wybierz pozycję **Zamknij,** aby zakończyć proces.

   ![Zrzut ekranu przedstawiający stronę Wybierz instalację](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Konfigurowanie usługi na myVM1 (podstawowy)

1. Utwórz lub zaktualizuj plik tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Utwórz konta właściciela i użytkowników Golden Gate.

   > [!NOTE]
   > Konto właściciela musi mieć prefiks języka C##.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Utwórz konto użytkownika testowego Golden Gate:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Skonfiguruj plik parametru wyodrębniania.

   Uruchom interfejs wiersza polecenia Golden Gate (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. Dodaj następujące elementy do pliku parametru EXTRACT (za pomocą poleceń vi). Naciśnij klawisz Esc, ':wq!' , aby zapisać plik. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. Zarejestruj ekstrakt - zintegrowany ekstrakt:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Skonfiguruj punkty kontrolne wyodrębniania i rozpocznij wyodrębnianie w czasie rzeczywistym:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   W tym kroku można znaleźć początkową SCN, która będzie używana później, w innej sekcji:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Konfigurowanie usługi na myVM2 (replikacja)


1. Utwórz lub zaktualizuj plik tnsnames.ora:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Utwórz konto replikacji:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Utwórz konto użytkownika testowego Golden Gate:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. Plik parametru REPLICAT w celu replikacji zmian: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Zawartość pliku parametrów REPORA:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Konfigurowanie punktu kontrolnego replikacji:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Konfigurowanie replikacji (myVM1 i myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Konfigurowanie replikacji na myVM2 (replikacja)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Zaktualizuj plik o następujące elementy:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Następnie uruchom ponownie usługę Manager:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Konfigurowanie replikacji na myVM1 (podstawowy)

Uruchom początkowe obciążenie i sprawdź, czy nie ma błędów:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Konfigurowanie replikacji na myVM2 (replikacja)

Zmień numer SCN z numerem uzyskanym przed:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

Replikacja została rozpoczęta i można ją przetestować, wstawiając nowe rekordy do tabel TEST.


### <a name="view-job-status-and-troubleshooting"></a>Wyświetlanie stanu zadania i rozwiązywania problemów

#### <a name="view-reports"></a>Wyświetlanie raportów
Aby wyświetlić raporty dotyczące myVM1, uruchom następujące polecenia:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Aby wyświetlić raporty dotyczące myVM2, uruchom następujące polecenia:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Wyświetl stan i historię
Aby wyświetlić stan i historię na myVM1, uruchom następujące polecenia:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Aby wyświetlić stan i historię na myVM2, uruchom następujące polecenia:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
To kończy instalację i konfigurację Golden Gate na Oracle linux.


## <a name="delete-the-virtual-machine"></a>Usuń maszynę wirtualną

Gdy nie jest już potrzebne, następujące polecenie może służyć do usuwania grupy zasobów, maszyny Wirtualnej i wszystkich powiązanych zasobów.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek dotyczący tworzenia maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Przegląd przykładowych poleceń interfejsu wiersza polecenia umożliwiających wdrożenie maszyny wirtualnej](../../linux/cli-samples.md)
