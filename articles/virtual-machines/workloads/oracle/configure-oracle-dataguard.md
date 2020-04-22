---
title: Wdrażanie oracle data guard na maszynie wirtualnej systemu Azure Linux | Dokumenty firmy Microsoft
description: Szybko uruchamiaj oracle data guard w środowisku platformy Azure.
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
ms.openlocfilehash: 96528dc34305e77602634110a0153f7623a15c96
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676769"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Wdrażanie oracle data guard na maszynie wirtualnej systemu Azure Linux 

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym artykule opisano sposób używania interfejsu wiersza polecenia platformy Azure do wdrażania bazy danych Oracle Database 12c z obrazu portalu Azure Marketplace. W tym artykule przedstawiono krok po kroku, jak zainstalować i skonfigurować usługę Data Guard na maszynie wirtualnej platformy Azure (VM).

Przed rozpoczęciem upewnij się, że jest zainstalowany zestaw wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [podręcznik instalacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska
### <a name="assumptions"></a>Założenia

Aby zainstalować oracle data guard, należy utworzyć dwie maszyny wirtualne platformy Azure na tym samym zestawie dostępności:

- Podstawowa maszyna wirtualna (myVM1) ma uruchomione wystąpienie Oracle.
- Maszyna wirtualna w trybie gotowości (myVM2) ma tylko zainstalowane oprogramowanie Oracle.

Obraz portalu Marketplace używany do tworzenia maszyn wirtualnych to Oracle:Oracle-Database-Ee:12.1.0.2:latest.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [az logowania](/cli/azure/reference-index) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to kontener logiczny, w którym zasoby platformy Azure są wdrażane i zarządzane. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności

Tworzenie zestawu dostępności jest opcjonalne, ale zalecamy. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące dostępności platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Poniższy przykład tworzy dwie `myVM1` maszyny `myVM2`wirtualne o nazwie i . Tworzy również klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

Tworzenie myVM1 (podstawowy):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Po utworzeniu maszyny Wirtualnej interfejsu wiersza polecenia platformy Azure pokazuje informacje podobne do poniższego przykładu. Zanotuj `publicIpAddress`wartość . Ten adres służy do uzyskiwania dostępu do maszyny Wirtualnej.

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

Tworzenie myVM2 (w trybie gotowości):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Zwróć uwagę `publicIpAddress` na wartość po utworzeniu myVM2.

### <a name="open-the-tcp-port-for-connectivity"></a>Otwieranie portu TCP w celu uzyskania łączności

Ten krok konfiguruje zewnętrzne punkty końcowe, które umożliwiają zdalny dostęp do bazy danych Oracle.

Otwórz port dla myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Wynik powinien wyglądać podobnie do następującej odpowiedzi:

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

Otwórz port dla myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp adres `publicIpAddress` IP wartością maszyny wirtualnej.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Tworzenie bazy danych na myVM1 (podstawowy)

Oprogramowanie Oracle jest już zainstalowane na obrazie Marketplace, więc następnym krokiem jest zainstalowanie bazy danych. 

Przełącz się na superużytnika Oracle:

```bash
$ sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

Ustaw zmienne ORACLE_SID i ORACLE_HOME:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Opcjonalnie można dodać ORACLE_HOME i ORACLE_SID do pliku /home/oracle/.bashrc, aby te ustawienia były zapisywane dla przyszłych loginów:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Konfigurowanie osłony danych

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

Włącz rejestrowanie siłowe i upewnij się, że jest obecny co najmniej jeden plik dziennika:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Tworzenie dzienników ponawiania w trybie czuwania:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Włącz Flashback (co sprawia, że odzyskiwanie o\_wiele\_łatwiejsze) i ustawić zarządzanie plikami wstrzymania do auto. Po tym dź.1.exit SQL*Plus after that.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Konfigurowanie usługi na myVM1 (podstawowy)

Edytuj lub utwórz plik tnsnames.ora, który znajduje się w folderze $ORACLE_HOME\network\admin.

Dodaj następujące wpisy:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Edytuj lub utwórz plik listener.ora, który znajduje się w folderze $ORACLE_HOME\network\admin.

Dodaj następujące wpisy:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Włącz brokera ochrony danych:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

Uruchom odbiornik:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Konfigurowanie usługi na myVM2 (w trybie gotowości)

SSH do myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Zaloguj się jako Oracle:

```bash
$ sudo su - oracle
```

Edytuj lub utwórz plik tnsnames.ora, który znajduje się w folderze $ORACLE_HOME\network\admin.

Dodaj następujące wpisy:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Edytuj lub utwórz plik listener.ora, który znajduje się w folderze $ORACLE_HOME\network\admin.

Dodaj następujące wpisy:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Uruchom odbiornik:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Przywracanie bazy danych do myVM2 (w trybie gotowości)

Utwórz plik parametrów /tmp/initcdb1_stby.ora z następującą zawartością:

```bash
*.db_name='cdb1'
```

Tworzenie folderów:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Utwórz plik hasła:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Uruchom bazę danych na myVM2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Przywróć bazę danych za pomocą narzędzia RMAN:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Uruchom następujące polecenia w programze RMAN:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Po zakończeniu polecenia powinny być widoczne komunikaty podobne do następujących. Wyjdź z RMAN.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

Opcjonalnie można dodać ORACLE_HOME i ORACLE_SID do pliku /home/oracle/.bashrc, aby te ustawienia były zapisywane dla przyszłych loginów:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Włącz brokera ochrony danych:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Konfigurowanie brokera ochrony danych w myVM1 (podstawowy)

Uruchom Menedżera ochrony danych i zaloguj się za pomocą SYS i hasła. (Nie należy używać uwierzytelniania systemu operacyjnego). Wykonaj następujące czynności:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Sprawdź konfigurację:

```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Ukończono konfigurację Oracle Data Guard. W następnej sekcji pokazano, jak przetestować łączność i przełączyć się.

### <a name="connect-the-database-from-the-client-machine"></a>Łączenie bazy danych z komputera klienckiego

Zaktualizuj lub utwórz plik tnsnames.ora na komputerze klienckim. Ten plik jest zwykle w $ORACLE_HOME\network\admin.

Zastąp adresy IP `publicIpAddress` wartościami dla myVM1 i myVM2:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Uruchom SQL*Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Testowanie konfiguracji ochrony danych

### <a name="switch-over-the-database-on-myvm1-primary"></a>Przełączanie bazy danych na myVM1 (podstawowy)

Aby przełączyć się z podstawowego na stan wstrzymania (cdb1 na cdb1_stby):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Teraz można połączyć się z rezerwowej bazy danych.

Uruchom SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Przełączanie bazy danych na myVM2 (w trybie gotowości)

Aby przełączyć się, uruchom na myVM2 następujące czynności:

```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Po raz kolejny powinieneś teraz mieć możliwość nawiązania połączenia z podstawową bazą danych.

Uruchom SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Zakończono instalację i konfigurację Data Guard w systemie Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Usuń maszynę wirtualną

Jeśli maszyna wirtualna nie jest już potrzebna, można użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)

[Zapoznaj się z przykładami interfejsu wiersza polecenia platformy Azure dotyczące wdrażania maszyn wirtualnych](../../linux/cli-samples.md)
