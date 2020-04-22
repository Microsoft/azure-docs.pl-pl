---
title: Tworzenie bazy danych Oracle na maszynie Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Szybko uruchamiaj bazę danych Oracle Database 12c w środowisku platformy Azure.
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
ms.openlocfilehash: 77a374a83c178639052e8db6fc85c31e366ac0e6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683637"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Tworzenie bazy danych Oracle na maszynie Wirtualnej platformy Azure

This guide details using the Azure CLI to deploy an Azure virtual machine from the [Oracle marketplace gallery image](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) in order to create an Oracle 12c database. Po wdrożeniu serwera można połączyć się za pośrednictwem protokołu SSH w celu skonfigurowania bazy danych Oracle. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną (VM), należy użyć polecenia [az vm create.](/cli/azure/vm) 

W poniższym przykładzie utworzono maszynę wirtualną o nazwie `myVM`. Tworzy również klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po utworzeniu maszyny Wirtualnej platformy Azure CLI wyświetla informacje podobne do poniższego przykładu. Zanotuj `publicIpAddress`wartość dla . Ten adres służy do uzyskiwania dostępu do maszyny Wirtualnej.

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

Aby utworzyć sesję SSH z maszyną wirtualną, użyj następującego polecenia. Zastąp adres `publicIpAddress` IP wartością maszyny Wirtualnej.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie Oracle jest już zainstalowane na obrazie Marketplace. Utwórz przykładową bazę danych w następujący sposób. 

1.  Przełącz się do superużytka *oracle,* a następnie zainicjuj odbiornik do rejestrowania:

    ```bash
    $ sudo su - oracle
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

2.  Tworzenie bazy danych:

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
           -ignorePreReqs
    ```

    Utworzenie bazy danych zajmuje kilka minut.

3. Ustawianie zmiennych Oracle

Przed nawiązaniem połączenia należy ustawić dwie zmienne środowiskowe: *ORACLE_HOME* i *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

Do pliku bashrc można również dodawać zmienne ORACLE_HOME i ORACLE_SID. Pozwoliłoby to zapisać zmienne środowiskowe dla przyszłych logowania. `~/.bashrc`

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Łączność Oracle EM Express

Aby uzyskać narzędzie do zarządzania interfejsem użytkownika, którego można użyć do eksplorowania bazy danych, skonfiguruj program Oracle EM Express. Aby połączyć się z oracle EM Express, należy najpierw skonfigurować port w Oracle. 

1. Połącz się z bazą danych za pomocą sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po podłączeniu ustaw port 5502 dla EM Express

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

4. Jeśli OPEN_MODE for `PDB1` nie jest odczytu zapisu, uruchom następujące polecenia, aby otworzyć PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Należy wpisać, `quit` aby zakończyć `exit` sesję sqlplus i wpisać, aby wylogować użytkownika oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatyzacja uruchamiania i zamykania bazy danych

Baza danych Oracle domyślnie nie uruchamia się automatycznie po ponownym uruchomieniu maszyny Wirtualnej. Aby skonfigurować bazę danych Oracle, aby uruchomić automatycznie, najpierw zaloguj się jako root. Następnie utwórz i zaktualizuj niektóre pliki systemowe.

1. Zaloguj się jako root

    ```bash
    sudo su -
    ```

2.  Korzystając z ulubionego edytora, edytuj plik `/etc/oratab` i zmień domyślny `N` na: `Y`

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Utwórz plik `/etc/init.d/dbora` o nazwie i wklej następującą zawartość:

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

4.  Zmień uprawnienia do plików za pomocą *chmod w* następujący sposób:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Tworzenie dowiązań symbolicznych dla uruchamiania i zamykania w następujący sposób:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Aby przetestować zmiany, uruchom ponownie maszynę wirtualną:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otwarte porty dla łączności

Ostatnim zadaniem jest skonfigurowanie niektórych zewnętrznych punktów końcowych. Aby skonfigurować grupę zabezpieczeń sieciowej platformy Azure, która chroni maszynę wirtualną, najpierw zamknij sesję SSH na maszynie Wirtualnej (powinien zostać wyrzucony z SSH podczas ponownego uruchamiania w poprzednim kroku). 

1.  Aby otworzyć punkt końcowy używany do zdalnego uzyskiwania dostępu do bazy danych Oracle, utwórz regułę sieciowej grupy zabezpieczeń z [regułą nsg sieci az, należy utworzyć](/cli/azure/network/nsg/rule) w następujący sposób: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Aby otworzyć punkt końcowy używany do zdalnego uzyskiwania dostępu do oracle em express, utwórz regułę sieciowej grupy zabezpieczeń z [regułą nsg sieci az, należy utworzyć](/cli/azure/network/nsg/rule) w następujący sposób:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. W razie potrzeby ponownie uzyskaj publiczny adres IP maszyny Wirtualnej, aby [wyświetlić program az network public-ip](/cli/azure/network/public-ip) w następujący sposób:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Połącz EM Express z przeglądarki. Upewnij się, że twoja przeglądarka jest zgodna z EM Express (wymagana jest instalacja Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Możesz zalogować się za pomocą konta **SYS** i zaznacz pole wyboru **jako sysdba.** Użyj hasła **OraPasswd1** ustawionego podczas instalacji. 

![Zrzut ekranu przedstawiający stronę logowania oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu eksplorowania pierwszej bazy danych Oracle na platformie Azure i maszyny Wirtualnej nie jest już potrzebne, można użyć polecenia [az grupy delete,](/cli/azure/group) aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych [rozwiązaniach Oracle na platformie Azure](oracle-considerations.md). 

Wypróbuj [samouczek Instalowanie i konfigurowanie automatycznego zarządzania pamięcią masową Oracle.](configure-oracle-asm.md)
