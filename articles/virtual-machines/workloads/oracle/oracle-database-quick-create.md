---
title: Tworzenie bazy danych Oracle na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Szybko zapoznaj się z Oracle Database bazą danych 12c w środowisku platformy Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: ec6a8382e2c0ce2cb359a62dd3f80fc977c4b1c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674652"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Tworzenie Oracle Database na maszynie wirtualnej platformy Azure

W tym przewodniku szczegółowo przedstawiono użycie interfejsu wiersza polecenia platformy Azure w celu wdrożenia maszyny wirtualnej platformy Azure z [obrazu galerii Marketplace firmy Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) w celu utworzenia bazy danych Oracle 19c. Po wdrożeniu serwera nastąpi połączenie za pośrednictwem protokołu SSH w celu skonfigurowania bazy danych Oracle. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład tworzy grupę zasobów o nazwie *RG-Oracle* w lokalizacji *Wschodnie* .

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną, użyj polecenia [AZ VM Create](/cli/azure/vm) . 

W poniższym przykładzie utworzono maszynę wirtualną o nazwie `vmoracle19c`. Program tworzy również klucze SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do poniższego przykładu. Zwróć uwagę na wartość parametru `publicIpAddress` . Ten adres jest używany do uzyskiwania dostępu do maszyny wirtualnej.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Tworzenie i dołączanie nowego dysku dla oprogramowania Oracle datafiles i FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Otwórz porty na potrzeby łączności
W tym zadaniu musisz skonfigurować niektóre zewnętrzne punkty końcowe dla odbiornika bazy danych i EM Express do użycia przez skonfigurowanie grupy zabezpieczeń sieci platformy Azure, która chroni maszynę wirtualną. 

1. Aby otworzyć punkt końcowy, który służy do zdalnego uzyskiwania dostępu do bazy danych Oracle, Utwórz regułę sieciowej grupy zabezpieczeń w następujący sposób:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Aby otworzyć punkt końcowy, który służy do zdalnego uzyskiwania dostępu do programu Oracle EM Express, Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu AZ Network sieciowej grupy zabezpieczeń Rule Create w następujący sposób:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. W razie konieczności Uzyskaj ponownie publiczny adres IP maszyny wirtualnej za pomocą AZ Network Public-IP show w następujący sposób:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Przygotuj środowisko maszyny wirtualnej

1. Łączenie z maszyną wirtualną

   Aby utworzyć sesję SSH z maszyną wirtualną, użyj następującego polecenia. Zastąp adres IP `publicIpAddress` wartością dla maszyny wirtualnej.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Przełącz do głównego użytkownika

   ```bash
   sudo su -
   ```

3. Sprawdź w poszukiwaniu ostatnio utworzonego urządzenia dyskowego, które będzie używane do przechowywania danych z programu Oracle datafiles

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   Dane wyjściowe będą wyglądać podobnie do tego:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Sformatuj urządzenie. 
   Jako część uruchomienia użytkownika głównego na urządzeniu 
   
   Najpierw Utwórz etykietę dysku:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Następnie Utwórz partycję podstawową obejmującą cały dysk:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Na koniec sprawdź szczegóły urządzenia, drukując jego metadane:
   ```bash
   parted /dev/sdc print
   ```
   Dane wyjściowe powinny wyglądać mniej więcej tak:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Tworzenie systemu plików na partycji urządzeń

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Utwórz punkt instalacji
   ```bash
   mkdir /u02
   ```

7. Instalowanie dysku

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Zmień uprawnienia w punkcie instalacji

   ```bash
   chmod 777 /u02
   ```

9. Dodaj instalację do pliku/etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Zaktualizuj plik ***/etc/hosts*** za pomocą publicznego adresu IP i nazwy hosta.

    Zmień ***publiczny adres IP i VMName*** , aby odzwierciedlał wartości rzeczywiste:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Zaktualizuj plik hostname
    
    Użyj poniższego polecenia, aby dodać nazwę domeny maszyny wirtualnej do pliku **/etc/hostname** . Przyjęto założenie, że utworzono grupę zasobów i maszynę wirtualną w regionie **wschodnim** :
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Otwórz porty zapory
    
    Ponieważ SELinux jest domyślnie włączona w obrazie portalu Marketplace, musimy otworzyć Zaporę do ruchu dla portu nasłuchiwania bazy danych 1521 i Enterprise Manager Express port 5502. Uruchom następujące polecenia jako użytkownik główny:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie Oracle jest już zainstalowane w obrazie portalu Marketplace. Utwórz przykładową bazę danych w następujący sposób. 

1.  Przejdź do użytkownika **Oracle** :

    ```bash
    $ sudo su - oracle
    ```
2. Uruchom odbiornik bazy danych

   ```bash
   $ lsnrctl start
   ```
   Dane wyjściowe będą podobne do następujących:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Utwórz katalog danych dla plików danych Oracle:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Uruchom asystenta tworzenia bazy danych:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Utworzenie bazy danych może potrwać kilka minut.

    Zobaczysz dane wyjściowe podobne do następujących:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Ustaw zmienne Oracle

    Przed nawiązaniem połączenia należy ustawić zmienną środowiskową *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    Należy również dodać zmienną ORACLE_SID do `oracle` `.bashrc` pliku users dla przyszłych logowań przy użyciu następującego polecenia:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Łączność z firmą Oracle EM Express

Aby skorzystać z narzędzia do zarządzania graficznym interfejsem użytkownika, którego możesz użyć do eksplorowania bazy danych, skonfiguruj Oracle EM Express. Aby nawiązać połączenie z serwerem Oracle EM Express, należy najpierw skonfigurować port w programie Oracle. 

1. Nawiąż połączenie z bazą danych przy użyciu polecenia sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Po nawiązaniu połączenia Ustaw port 5502 dla EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Połącz z przeglądarką EM Express. Upewnij się, że przeglądarka jest zgodna z programem EM Express (wymagana jest instalacja programu Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Możesz zalogować się przy użyciu konta **sys** i zaznaczyć pole wyboru **jako SYSDBA** . Użyj **OraPasswd1** hasła ustawianego podczas instalacji. 

    ![Zrzut ekranu strony logowania do programu Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatyzowanie uruchamiania i zamykania bazy danych

Domyślnie baza danych Oracle nie jest uruchamiana automatycznie po ponownym uruchomieniu maszyny wirtualnej. Aby skonfigurować bazę danych Oracle do automatycznego uruchamiania, najpierw Zaloguj się jako element główny. Następnie utwórz i zaktualizuj niektóre pliki systemowe.

1. Zaloguj się jako główny

    ```bash
    sudo su -
    ```

2.  Uruchom następujące polecenie, aby zmienić flagę automatycznego uruchamiania z `N` na `Y` w `/etc/oratab` pliku:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Utwórz plik o nazwie `/etc/init.d/dbora` i wklej następującą zawartość:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu eksplorowania pierwszej bazy danych Oracle na platformie Azure nie jest już potrzebne, możesz użyć polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Informacje o ochronie bazy danych na platformie Azure przy użyciu [strategii tworzenia kopii zapasowych programu Oracle](./oracle-database-backup-strategies.md)

Poznaj inne [rozwiązania firmy Oracle na platformie Azure](./oracle-overview.md). 

Spróbuj [zainstalować i skonfigurować samouczek zautomatyzowanego zarządzania magazynem firmy Oracle](configure-oracle-asm.md) .
