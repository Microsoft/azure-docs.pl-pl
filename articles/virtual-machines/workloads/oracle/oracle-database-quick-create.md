---
title: Tworzenie bazy danych Oracle na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Szybko zapoznaj się z Oracle Database bazą danych 12c w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dbakevlar
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 31743fd292bb45ee7107bc17f7d5dc43db8ed3f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728288"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Tworzenie Oracle Database na maszynie wirtualnej platformy Azure

W tym przewodniku szczegółowo przedstawiono użycie interfejsu wiersza polecenia platformy Azure w celu wdrożenia maszyny wirtualnej platformy Azure z [obrazu galerii Marketplace firmy Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) w celu utworzenia bazy danych Oracle 12c. Po wdrożeniu serwera nastąpi połączenie za pośrednictwem protokołu SSH w celu skonfigurowania bazy danych Oracle. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną, użyj polecenia [AZ VM Create](/cli/azure/vm) . 

W poniższym przykładzie utworzono maszynę wirtualną o nazwie `myVM`. Program tworzy również klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do poniższego przykładu. Zwróć uwagę na wartość parametru `publicIpAddress` . Ten adres jest używany do uzyskiwania dostępu do maszyny wirtualnej.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Aby utworzyć sesję SSH z maszyną wirtualną, użyj następującego polecenia. Zastąp adres IP `publicIpAddress` wartością dla maszyny wirtualnej.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie Oracle jest już zainstalowane w obrazie portalu Marketplace. Utwórz przykładową bazę danych w następujący sposób. 

1.  Przejdź do użytkownika *Oracle* , a następnie uruchom odbiornik Oracle:

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    Dane wyjściowe będą podobne do następujących:

    ```output
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
2. Tworzenie katalogu danych dla plików danych Oracle

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Utwórz bazę danych:

    ```bash
    dbca -silent \
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
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Utworzenie bazy danych może potrwać kilka minut.

    Zobaczysz dane wyjściowe podobne do następujących:

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

4. Ustaw zmienne Oracle

    Przed nawiązaniem połączenia należy ustawić dwie zmienne środowiskowe: *ORACLE_HOME* i *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    Można również dodać zmienne ORACLE_HOME i ORACLE_SID do pliku bashrc. Spowoduje to zapisanie zmiennych środowiskowych dla przyszłych logowań. Upewnij się, że dodano następujące instrukcje do `~/.bashrc` pliku za pomocą edytora.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
    ```

## <a name="oracle-em-express-connectivity"></a>Łączność z firmą Oracle EM Express

Aby skorzystać z narzędzia do zarządzania graficznym interfejsem użytkownika, którego możesz użyć do eksplorowania bazy danych, skonfiguruj Oracle EM Express. Aby nawiązać połączenie z serwerem Oracle EM Express, należy najpierw skonfigurować port w programie Oracle. 

1. Nawiąż połączenie z bazą danych przy użyciu polecenia sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po nawiązaniu połączenia Ustaw port 5502 dla EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otwórz kontener PDB1, jeśli nie został jeszcze otwarty, ale najpierw sprawdź stan:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Dane wyjściowe będą podobne do następujących:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Jeśli OPEN_MODE dla `PDB1` nie odczytuje zapisu, uruchom następujące polecenia, aby otworzyć PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Musisz wpisać, `quit` Aby zakończyć sesję sqlplus i wpisz, `exit` Aby wylogować użytkownika Oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatyzowanie uruchamiania i zamykania bazy danych

Domyślnie baza danych Oracle nie jest uruchamiana automatycznie po ponownym uruchomieniu maszyny wirtualnej. Aby skonfigurować bazę danych Oracle do automatycznego uruchamiania, najpierw Zaloguj się jako element główny. Następnie utwórz i zaktualizuj niektóre pliki systemowe.

1. Zaloguj się jako główny

    ```bash
    sudo su -
    ```

2.  Za pomocą ulubionego edytora Edytuj plik `/etc/oratab` i zmień wartość domyślną `N` na `Y` :

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Utwórz plik o nazwie `/etc/init.d/dbora` i wklej następującą zawartość:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Zmień uprawnienia do plików z *chmod* w następujący sposób:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Utwórz linki symboliczne do uruchamiania i zamykania w następujący sposób:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Aby przetestować zmiany, uruchom ponownie maszynę wirtualną:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otwórz porty na potrzeby łączności

Ostatnim zadaniem jest skonfigurowanie niektórych zewnętrznych punktów końcowych. Aby skonfigurować grupę zabezpieczeń sieci platformy Azure chroniącą maszynę wirtualną, najpierw Wyjdź z sesji SSH na maszynie wirtualnej (powinna zostać wykorzystana z protokołu SSH podczas ponownego uruchamiania w poprzednim kroku). 

1.  Aby otworzyć punkt końcowy, który służy do zdalnego uzyskiwania dostępu do bazy danych Oracle, Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule) w następujący sposób: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Aby otworzyć punkt końcowy, który służy do zdalnego uzyskiwania dostępu do programu Oracle EM Express, Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule) w następujący sposób:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. W razie konieczności Uzyskaj ponownie publiczny adres IP maszyny wirtualnej za pomocą [AZ Network Public-IP show](/cli/azure/network/public-ip) w następujący sposób:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Połącz z przeglądarką EM Express. Upewnij się, że przeglądarka jest zgodna z programem EM Express (wymagana jest instalacja programu Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Możesz zalogować się przy użyciu konta **sys** i zaznaczyć pole wyboru **jako SYSDBA** . Użyj **OraPasswd1** hasła ustawianego podczas instalacji. 

![Zrzut ekranu strony logowania do programu Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu eksplorowania pierwszej bazy danych Oracle na platformie Azure nie jest już potrzebne, możesz użyć polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Poznaj inne [rozwiązania firmy Oracle na platformie Azure](./oracle-overview.md). 

Spróbuj [zainstalować i skonfigurować samouczek zautomatyzowanego zarządzania magazynem firmy Oracle](configure-oracle-asm.md) .
