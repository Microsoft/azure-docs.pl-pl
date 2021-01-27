---
title: Wdrażanie oprogramowania SAP BusinessObjects BI platform na platformie Azure dla systemu Linux | Microsoft Docs
description: Wdrażanie i Konfigurowanie platformy SAP BusinessObjects BI na platformie Azure dla systemu Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: a7361dafce30b07e76d971bdcda41cf4b3cd9e6e
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806167"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Przewodnik wdrażania platformy SAP BusinessObjects BI dla systemu Linux na platformie Azure

W tym artykule opisano strategię wdrażania platformy SAP BOBI na platformie Azure dla systemu Linux. W tym przykładzie są skonfigurowane dwie maszyny wirtualne z SSD w warstwie Premium Managed Disks jako katalog instalacji. Azure Database for MySQL jest używany w przypadku bazy danych CMS, a Azure NetApp Files dla serwera repozytorium plików jest współużytkowany na obu serwerach. Domyślna aplikacja sieci Web Tomcat Java i aplikacja platformy BI są instalowane razem na obu maszynach wirtualnych. W celu zrównoważenia obciążenia żądania użytkownika używane jest Application Gateway, które ma natywne możliwości odciążania protokołów TLS/SSL.

Ten typ architektury jest skuteczny dla małego wdrożenia lub środowiska nieprodukcyjnego. W przypadku wdrożenia produkcyjnego lub w dużej skali można mieć oddzielne hosty dla aplikacji sieci Web, a także wiele hostów aplikacji BOBI umożliwiających serwerowi przetworzenie dodatkowych informacji.

![Wdrażanie oprogramowania SAP BOBI na platformie Azure dla systemu Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

W tym przykładzie użyto wersji produktu i układu systemu plików

- Platforma SAP BusinessObjects 4,3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (wersja: 8.0.15)
- Łącznik interfejsu API MySQL C — libmysqlclient (wersja: 6.1.11)

| System plików        | Opis                                                                                                               | Rozmiar (GB)             | Właściciel  | Group (Grupa)  | Magazyn                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | System plików na potrzeby instalacji wystąpienia SAP BOBI, domyślnej aplikacji sieci Web Tomcat i sterowników bazy danych (w razie potrzeby) | Wytyczne dotyczące ustalania wielkości SAP | bl1adm | sapsys | Zarządzany dysk w warstwie Premium — SSD |
| /usr/sap/frsinput  | Katalog instalacji jest przeznaczony dla plików udostępnionych na wszystkich hostach BOBI, które będą używane jako katalog repozytorium plików wejściowych  | Potrzeby biznesowe         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Katalog instalacji jest przeznaczony dla plików udostępnionych na wszystkich hostach BOBI, które będą używane jako katalog plików wyjściowych repozytorium | Potrzeby biznesowe         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Wdróż maszynę wirtualną z systemem Linux za pomocą Azure Portal

W tej sekcji utworzymy dwie maszyny wirtualne z obrazem systemu operacyjnego Linux dla platformy SAP BOBI. Poniżej przedstawiono procedurę wysokiego poziomu służącą do tworzenia Virtual Machines:

1. Tworzenie [grupy zasobów](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Utwórz [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Nie używaj jednej podsieci dla wszystkich usług platformy Azure we wdrożeniu platformy SAP BI. Na podstawie architektury platformy SAP BI należy utworzyć wiele podsieci. W tym wdrożeniu utworzymy trzy podsieci — podsieć aplikacji, podsieć magazynu repozytorium plików oraz podsieć Application Gateway.
   - Na platformie Azure Application Gateway i Azure NetApp Files zawsze muszą znajdować się w osobnej podsieci. Aby uzyskać więcej informacji, sprawdź [Application Gateway platformy Azure](../../../application-gateway/configuration-overview.md) i [wskazówki dotyczące artykułu Planowanie sieci Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) .

3. Utwórz zestaw dostępności.

   - Aby zapewnić nadmiarowość dla każdej warstwy w przypadku wdrożenia z obsługą wiele wystąpień, należy umieścić maszyny wirtualne dla każdej warstwy w zestawie dostępności. Upewnij się, że zestawy dostępności dla każdej warstwy są rozdzielone na podstawie architektury.

4. Utwórz maszynę wirtualną 1 **(azusbosl1).**

   - Możesz użyć obrazu niestandardowego lub wybrać obraz z witryny Azure Marketplace. Zapoznaj się z artykułem [Wdrażanie maszyny wirtualnej z poziomu portalu Azure Marketplace dla oprogramowania SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) lub [Wdrażanie maszyny wirtualnej z niestandardowym obrazem dla oprogramowania SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) na podstawie Twoich potrzeb.

5. Utwórz maszynę wirtualną 2 **(azusbosl2).**
6. Dodaj jeden SSD w warstwie Premium dysk. Będzie on używany jako katalog instalacji SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Azure NetApp Files udostępniania

Przed kontynuowaniem instalacji Azure NetApp Files zapoznaj się z dokumentacją usługi Azure [NetApp](../../../azure-netapp-files/azure-netapp-files-introduction.md).

Azure NetApp Files jest dostępna w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files.

Użyj [Azure NetApp Files dostępność według regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) , aby sprawdzić dostępność Azure NetApp Files według regionów.

Zażądaj dołączenia do Azure NetApp Files, przechodząc do [instrukcji Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) przed wdrożeniem Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files

W poniższych instrukcjach przyjęto założenie, że [usługa Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)została już wdrożona. Azure NetApp Files zasoby i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.

1. Jeśli zasoby nie zostały jeszcze wdrożone, zażądaj dołączenia [do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z instrukcjami w temacie [Tworzenie konta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3. Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [konfigurowanie Azure NetApp Files puli pojemności](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

   - Architektura platformy SAP BI przedstawiona w tym artykule korzysta z jednej Azure NetApp Files puli pojemności na poziomie usługi *Premium* . W przypadku serwera repozytorium plików analizy biznesowej SAP na platformie Azure zalecamy użycie poziomu Azure NetApp Files *Premium* lub *Ultra* [Service](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

4. Delegowanie podsieci do Azure NetApp Files, zgodnie z opisem w temacie [delegowanie podsieci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z instrukcjami w temacie [Tworzenie woluminu NFS dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

   Wolumin ANF można wdrożyć jako NFSv3 i NFSv 4.1, ponieważ oba protokoły są obsługiwane przez platformę SAP BOBI. Wdróż woluminy w odpowiedniej podsieci Azure NetApp Files. Adresy IP woluminów NetApp platformy Azure są przypisywane automatycznie.

Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. Na przykład azusbobi-frsinput, azusbobi-frsoutput są nazwami woluminów i nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput są ścieżkami plików dla woluminów Azure NetApp Files.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Istotne zagadnienia

Podczas tworzenia Azure NetApp Files dla serwera repozytorium plików platformy SAP BOBI należy pamiętać o następujących kwestiach:

1. Minimalna Pula pojemności to 4 tebibajtów (TiB).
2. Minimalny rozmiar woluminu to 100 gibibajtach (GiB).
3. Azure NetApp Files i wszystkie maszyny wirtualne, na których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie. Azure NetApp Files dostęp za pośrednictwem komunikacji równorzędnej sieci wirtualnej w tym samym regionie jest obecnie obsługiwany. Dostęp do usługi Azure NetApp za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
4. Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
5. Za pomocą [zasad eksportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)Azure NetApp Files można kontrolować dozwolonych klientów, typ dostępu (odczyt i zapis, tylko do odczytu itd.).
6. Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure.
7. Woluminy Azure NetApp Files można wdrożyć jako woluminy NFSv3 lub NFSv 4.1. Oba protokoły są obsługiwane w przypadku aplikacji platformy SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurowanie systemów plików na serwerach z systemem Linux

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

**[A]**: krok dotyczy wszystkich hostów

### <a name="format-and-mount-sap-file-system"></a>Sformatuj i zainstaluj system plików SAP

1. **[A]** Wyświetl listę wszystkich dołączonych dysków

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** format bloku urządzenia dla/usr/SAP

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Utwórz katalog instalacji

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Pobieranie identyfikatora UUID urządzenia blokowego

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** obsługa wpisu instalacji systemu plików w/etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Zainstaluj system plików

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Zainstaluj wolumin Azure NetApp Files

1. **[A]** Tworzenie katalogów instalacji

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Skonfiguruj system operacyjny klienta do obsługi instalacji nfsv 4.1 **(dotyczy tylko sytuacji, gdy używany jest nfsv 4.1)**

   Jeśli używasz Azure NetApp Files woluminów z protokołem NFSv 4.1, wykonaj następującą konfigurację na wszystkich maszynach wirtualnych, gdzie należy zainstalować woluminy Azure NetApp Files NFSv 4.1.

   **Weryfikowanie ustawień domeny systemu plików NFS**

   Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files,  **defaultv4iddomain.com** , a mapowanie jest ustawione na wartość **nikt**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Upewnij się, że ustawiono domenę systemu plików NFS w/etc/idmapd.conf na maszynie wirtualnej tak, aby była zgodna z domyślną konfiguracją domeny w Azure NetApp Files: **defaultv4iddomain.com**. Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako nikt.

   Sprawdź `nfs4_disable_idmapping` . Powinna być ustawiona na wartość **Y**. Aby utworzyć strukturę katalogów, w której `nfs4_disable_idmapping` znajduje się, wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** Dodawanie wpisów instalacji

   Jeśli jest używany NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Jeśli jest używany program NFSv 4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** Zainstaluj WOLUMINy NFS

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurowanie usługi CMS Database — Azure Database for MySQL

Ta sekcja zawiera szczegółowe informacje na temat udostępniania Azure Database for MySQL przy użyciu Azure Portal. Zawiera również instrukcje dotyczące tworzenia baz danych CMS i Audit dla platformy SAP BOBI oraz konta użytkownika w celu uzyskania dostępu do bazy danych.

Wytyczne są stosowane tylko w przypadku korzystania z usługi Azure DB for MySQL. Aby uzyskać instrukcje dotyczące innych baz danych, zapoznaj się z dokumentacją dotyczącą oprogramowania SAP lub bazy danych.

### <a name="create-an-azure-database-for-mysql"></a>Tworzenie usługi Azure Database for MySQL

Zaloguj się do Azure Portal i wykonaj kroki opisane w tym [przewodniku szybki start dotyczące Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Kilka punktów do zanotowania podczas aprowizacji Azure Database for MySQL —

1. Wybierz ten sam region dla Azure Database for MySQL, w którym są uruchomione serwery aplikacji platformy SAP BI platform.

2. Wybierz obsługiwaną wersję bazy danych opartą na [macierzy dostępności produktu (PAM) dla usługi SAP BI](https://support.sap.com/pam) specyficznej dla wersji SAP BOBI. Postępuj zgodnie z tymi samymi wskazówkami dotyczącymi zgodności, które zostały uwzględnione w programie MySQL

3. W obszarze "obliczeniowe + magazyn" Wybierz pozycję **Konfiguruj serwer** i wybierz odpowiednią warstwę cenową na podstawie wielkości danych wyjściowych.

4. Funkcja **automatycznego wzrostu magazynu** jest domyślnie włączona. Należy pamiętać, że [Magazyn](../../../mysql/concepts-pricing-tiers.md#storage) można skalować w górę, nie w dół.

5. Domyślnie **kopia zapasowa okresu przechowywania** wynosi siedem dni, ale opcjonalnie można ją [skonfigurować](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) do 35 dni.

6. Kopie zapasowe Azure Database for MySQL są domyślnie lokalnie nadmiarowe, więc jeśli chcesz, aby kopie zapasowe serwera były wykonywane w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** z **opcji nadmiarowości kopii zapasowej**.

> [!NOTE]
> Zmiana [opcji nadmiarowości kopii zapasowej](../../../mysql/concepts-backup.md#backup-redundancy-options) po utworzeniu serwera nie jest obsługiwana.

### <a name="configure-connection-security"></a>Konfigurowanie zabezpieczeń połączeń

Domyślnie utworzony serwer jest chroniony za pomocą zapory i nie jest dostępny publicznie. Aby zapewnić dostęp do sieci wirtualnej, w której działają serwery aplikacji usługi SAP BI platform, wykonaj poniższe czynności.  

1. Przejdź do zasobów serwera w Azure Portal i wybierz pozycję **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera.
2. Wybierz pozycję **tak** , aby **zezwolić na dostęp do usług platformy Azure**.
3. W obszarze reguły sieci wirtualnej wybierz pozycję **Dodawanie istniejącej sieci wirtualnej**. Wybierz sieć wirtualną i podsieć serwera aplikacji SAP BI platform. Ponadto należy zapewnić dostęp do pola skoku lub innych serwerów, z których można połączyć [MySQL Workbench](../../../mysql/connect-workbench.md) z Azure Database for MySQL. Program MySQL Workbench zostanie użyty do utworzenia usługi CMS i bazy danych inspekcji
4. Po dodaniu sieci wirtualnych wybierz pozycję **Zapisz**.

### <a name="create-cms-and-audit-database"></a>Tworzenie usługi CMS i bazy danych inspekcji

1. Pobierz i zainstaluj program MySQL Workbench z [witryny sieci Web MySQL](https://dev.mysql.com/downloads/workbench/). Upewnij się, że instalujesz program MySQL Workbench na serwerze, który ma dostęp do Azure Database for MySQL.

2. Połącz się z serwerem za pomocą programu MySQL Workbench. Postępuj zgodnie z instrukcjami wymienionymi w tym [artykule](../../../mysql/connect-workbench.md#get-connection-information). Jeśli test połączenia zakończy się pomyślnie, zostanie wyświetlony następujący komunikat:

   ![Połączenie SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Na karcie zapytanie SQL Uruchom poniższe zapytanie, aby utworzyć schemat dla usługi CMS i bazy danych inspekcji.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Utwórz konto użytkownika, aby nawiązać połączenie ze schematem

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. Aby sprawdzić uprawnienia i role konta użytkownika programu MySQL

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Zainstaluj łącznik interfejsu API MySQL C (libmysqlclient) na serwerze z systemem Linux

Aby serwer aplikacji SAP BOBI mógł uzyskać dostęp do bazy danych, wymaga klienta/sterowników bazy danych. Łącznik interfejsu API MySQL C dla systemu Linux musi być używany w celu uzyskiwania dostępu do baz danych CMS i Audit. Połączenie ODBC z bazą danych CMS nie jest obsługiwane. Ta sekcja zawiera instrukcje dotyczące konfigurowania łącznika interfejsu API MySQL C w systemie Linux.

1. Zapoznaj się z artykułami [dotyczącymi sterowników MySQL i narzędzi do zarządzania, które są zgodne z Azure Database for MySQL](../../../mysql/concepts-compatibility.md) artykule, w którym opisano sterowniki, które są zgodne z Azure Database for MySQL. Sprawdź, czy w artykule znajduje się sterownik **łącznika MySQL/C (libmysqlclient)** .

2. Skorzystaj z tego [linku](https://downloads.mysql.com/archives/c-c/) , aby pobrać sterowniki.

3. Wybierz system operacyjny i Pobierz pakiet współużytkowanego składnika RPM łącznika programu MySQL. W tym przykładzie używany jest program MySQL-Connector-c-Shared-6.1.11 Connector.

4. Zainstaluj łącznik we wszystkich wystąpieniach aplikacji SAP BOBI.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Sprawdź ścieżkę libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Ustaw LD_LIBRARY_PATH na `/usr/lib64` katalog dla konta użytkownika, które będzie używane do instalacji.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Przygotowywanie serwera

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

**[A]**: krok ma zastosowanie do wszystkich hostów.

1. **[A]** na podstawie wersji systemu Linux (SLES lub RHEL), należy ustawić parametry jądra i zainstalować wymagane biblioteki. Zapoznaj się z sekcją **wymagania systemowe** w [podręczniku instalacji platformy analizy biznesowej dla systemu UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** upewnij się, że strefa czasowa na maszynie jest prawidłowo ustawiona. Zapoznaj się z [sekcją dodatkowe wymagania dotyczące systemów UNIX i Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) w podręczniku instalacji.

3. **[A]** Utwórz konto użytkownika (**BL1** adm) i grupę (sapsys), w ramach którego mogą być uruchamiane procesy w tle oprogramowania. Użyj tego konta do wykonania instalacji i uruchomienia oprogramowania. Konto nie wymaga uprawnień głównych.

4. **[A]** Skonfiguruj środowisko konta użytkownika (**BL1** adm) do korzystania z obsługiwanych ustawień regionalnych UTF-8 i upewnij się, że oprogramowanie konsoli obsługuje zestawy znaków UTF-8. Aby upewnić się, że system operacyjny używa prawidłowych ustawień regionalnych, Ustaw zmienne środowiskowe LC_ALL i LANG na preferowane ustawienia regionalne w środowisku użytkownika (**BL1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Skonfiguruj konto użytkownika (**BL1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Pobierz i Wyodrębnij multimedia dla platformy SAP BusinessObjects BI platform z witryny SAP Service Marketplace.

## <a name="installation"></a>Instalacja

Sprawdź ustawienia regionalne konta użytkownika **BL1** adm na serwerze

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Przejdź do nośnika platformy SAP BusinessObjects BI platform i uruchom polecenie poniżej z użytkownikiem **BL1** adm —

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Postępuj zgodnie z przewodnikiem instalacji [platformy SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) dla systemu UNIX, który jest specyficzny dla danej wersji. Kilka punktów do zanotowania podczas instalowania platformy SAP BOBI.

- Na ekranie **Konfigurowanie rejestracji produktu** możesz użyć tymczasowego klucza licencji dla rozwiązań SAP BusinessObjects z poziomu programu sap Uwaga [1288121](https://launchpad.support.sap.com/#/notes/1288121) lub wygenerować klucz licencji w portalu usługi SAP

- Na ekranie **Wybieranie typu instalacji** wybierz pozycję **pełna** instalacja na pierwszym serwerze (azusbosl1), a dla opcji inny serwer (Azusbosl2) wybierz pozycję **niestandardowy/rozwiń** , która spowoduje rozwinięcie istniejącej konfiguracji BOBI.

- Na ekranie **Wybierz domyślną lub istniejącą bazę** danych wybierz pozycję **Konfiguruj istniejącą bazę danych**, która wyświetli monit o wybranie opcji CMS i Audit Database. Wybierz typ bazy danych **MySQL** dla usługi CMS i typ bazy danych inspekcji.

  Możesz również wybrać opcję Brak bazy danych inspekcji, jeśli nie chcesz konfigurować inspekcji podczas instalacji.

- Wybierz odpowiednie opcje na **ekranie Wybieranie serwera aplikacji sieci Web Java** na podstawie architektury SAP BOBI. W tym przykładzie wybrano opcję 1, która instaluje serwer Tomcat na tej samej platformie SAP BOBI.

- Wprowadź informacje o bazie danych CMS w temacie **Konfigurowanie REPOZYTORIUM CMS baza danych — MySQL**. Przykładowe dane wejściowe dotyczące instalacji systemu Linux dotyczącej bazy danych CMS. Azure Database for MySQL jest używany na domyślnym porcie 3306
  
  ![Wdrażanie oprogramowania SAP BOBI w systemie Linux — baza danych CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Obowiązkowe Wprowadź informacje o bazie danych inspekcji w obszarze **Konfigurowanie repozytorium inspekcji bazy danych programu MySQL**. Przykładowe dane wejściowe dla informacji dotyczących bazy danych inspekcji dla instalacji systemu Linux.

  ![Wdrażanie oprogramowania SAP BOBI w systemie Linux — baza danych inspekcji](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Postępuj zgodnie z instrukcjami i wprowadź wymagane dane wejściowe, aby ukończyć instalację.

W przypadku wdrożenia z obsługą wiele wystąpień należy uruchomić Instalatora instalacji na drugim hoście (azusbosl2). Na ekranie **Wybieranie typu instalacji** wybierz opcję **niestandardowy/rozwiń** , która spowoduje rozwinięcie istniejącej konfiguracji BOBI.

W przypadku oferty usługi Azure Database for MySQL Brama jest używana do przekierowywania połączeń z wystąpieniami serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. W tym celu w programie Central Management Console (CMC) znajdziesz inną wersję bazy danych, która jest zasadniczo wersją ustawioną w bramie. Sprawdź [obsługiwane wersje serwera Azure Database for MySQL](../../../mysql/concepts-supported-versions.md) , aby uzyskać więcej szczegółów.

![Wdrażanie oprogramowania SAP BOBI w systemie Linux — ustawienia interfejsu CMC](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Po instalacji

### <a name="tomcat-clustering---session-replication"></a>Tomcat klastrowanie — replikacja sesji

Tomcat obsługuje klastrowanie co najmniej dwóch serwerów aplikacji na potrzeby replikacji sesji i trybu failover. Sesje platformy SAP BOBI są serializowane, sesja użytkownika może bezproblemowo przełączać w tryb failover do innego wystąpienia programu Tomcat, nawet w przypadku awarii serwera aplikacji.

Na przykład jeśli użytkownik jest połączony z serwerem sieci Web, który się nie powiódł podczas nawigowania w hierarchii folderów w aplikacji SAP BI. Po prawidłowym skonfigurowaniu klastra użytkownik może kontynuować nawigowanie w hierarchii folderów bez przekierowania na stronę logowania.

W programie SAP Uwaga [2808640](https://launchpad.support.sap.com/#/notes/2808640)kroki konfigurowania klastrowania Tomcat są udostępniane przy użyciu multiemisji. Jednak Multiemisja nie jest obsługiwana w systemie Azure. Aby klaster Tomcat działał na platformie Azure, musisz użyć [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (Uwaga dotycząca oprogramowania SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Sprawdź [Tomcat klastrowanie przy użyciu członkostwa statycznego dla platformy SAP BUSINESSOBJECTS BI platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) na blogu SAP, aby skonfigurować klaster Tomcat na platformie Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Warstwa sieci Web równoważenia obciążenia platformy SAP BI

W przypadku wdrażania z wieloma wystąpieniami w programie SAP BOBI serwery aplikacji sieci Web Java (warstwa sieci Web) są uruchomione na co najmniej dwóch hostach. Aby równomiernie rozłożyć obciążenie użytkownikami między serwerami sieci Web, można użyć modułu równoważenia obciążenia między użytkownikami końcowymi i serwerami sieci Web. Na platformie Azure Możesz użyć Azure Load Balancer lub Application Gateway platformy Azure do zarządzania ruchem do serwerów aplikacji sieci Web. Szczegółowe informacje o poszczególnych ofertach zostały omówione w sekcji poniżej.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Moduł równoważenia obciążenia platformy Azure (usługa równoważenia obciążenia opartego na sieci)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) to usługa równoważenia obciążenia w warstwie 4 (TCP, UDP) o wysokiej wydajności, która dystrybuuje ruch między Virtual Machines w dobrej kondycji. Sonda kondycji modułu równoważenia obciążenia monitoruje dany port na poszczególnych maszynach wirtualnych i dystrybuuje ruch tylko do operacyjnych maszyn wirtualnych. Możesz wybrać publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia w zależności od tego, czy chcesz, aby platforma SAP BI była dostępna z Internetu, czy nie. Jego strefowo nadmiarowe, zapewniając wysoką dostępność w Strefy dostępności.

Zapoznaj się z sekcją Load Balancer wewnętrzna na poniższej ilustracji, na której serwer aplikacji sieci Web działa na porcie 8080, domyślny port HTTP Tomcat, który będzie monitorowany przez sondę kondycji. W związku z tym wszystkie przychodzące żądania, które pochodzą od użytkowników końcowych, zostaną przekierowane do serwerów aplikacji sieci Web (azusbosl1 lub azusbosl2) w puli zaplecza. Moduł równoważenia obciążenia nie obsługuje kończenia protokołu TLS/SSL (znanego również jako odciążania protokołu TLS/SSL). Jeśli używasz modułu równoważenia obciążenia platformy Azure do dystrybucji ruchu między serwerami sieci Web, zalecamy korzystanie z usługa Load Balancer w warstwie Standardowa.

> [!NOTE]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Azure Load Balancer równoważenia ruchu między serwerami sieci Web](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (moduł równoważenia obciążenia aplikacji sieci Web)

Usługa [Azure Application Gateway (AGW)](../../../application-gateway/overview.md) udostępnia kontroler dostarczania aplikacji (ADC, Application Delivery Controller) jako usługę, która ułatwia aplikacji kierowanie ruchu użytkowników do co najmniej jednego serwera aplikacji sieci Web. Oferuje różne możliwości równoważenia obciążenia warstwy 7, takie jak wyciążanie protokołu TLS/SSL, Zapora aplikacji sieci Web (WAF), koligacja sesji oparta na plikach cookie i inne aplikacje.

W platformie SAP BI Usługa Application Gateway kieruje ruch internetowy aplikacji do określonych zasobów w puli zaplecza — azusbosl1 lub azusbos2. Do portu, tworzenia reguł i dodawania zasobów do puli zaplecza można przypisać odbiornik. Na poniższej ilustracji usługi Application Gateway z prywatnym adresem IP frontonu (10.31.3.20) pełnią rolę punktu wejścia dla użytkowników, obsługują połączenia przychodzące protokołu TLS/SSL (HTTPS), odszyfrowywania protokołu TLS/SSL i przekazywania żądania nieszyfrowanego (HTTP-TCP/8080) do serwerów w puli zaplecza. Dzięki wbudowanej funkcji obsługi protokołu TLS/SSL wystarczy zachować jeden certyfikat TLS/SSL w bramie aplikacji, co upraszcza operacje.

![Application Gateway równoważenia ruchu między serwerami sieci Web](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Aby skonfigurować Application Gateway dla serwera sieci Web SAP BOBI, można odwoływać się do [równoważenia obciążenia serwerów sieci Web SAP BOBI przy użyciu usługi Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) na blogu SAP.

> [!NOTE]
> Zalecamy używanie Application Gateway platformy Azure do równoważenia obciążenia ruchu na serwerze sieci Web, ponieważ udostępnia on funkcję, takie jak odciążanie protokołu SSL, scentralizowane zarządzanie protokołem SSL w celu zredukowania obciążenia szyfrowania i odszyfrowywania na serwerze, Round-Robin algorytmem do dystrybucji ruchu, funkcji zapory aplikacji sieci Web (WAF), wysokiej dostępności i tak dalej.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Platforma SAP BusinessObjects BI — wykonywanie kopii zapasowych i przywracanie

Tworzenie i przywracanie kopii zapasowych to proces tworzenia okresowych kopii danych i aplikacji w oddzielnym miejscu. W związku z tym można go przywrócić lub odzyskać do poprzedniego stanu, jeśli oryginalne dane lub aplikacje zostaną utracone lub uszkodzone. Jest to również zasadniczy składnik każdej strategii odzyskiwania po awarii firmy.

Aby opracować kompleksową strategię tworzenia kopii zapasowych i przywracania dla platformy SAP BOBI, zidentyfikuj składniki, które prowadzą do przestoju systemu lub przerwy w działaniu aplikacji. W przypadku platformy SAP BOBI kopia zapasowa następujących składników jest konieczna do ochrony aplikacji.

- Katalog instalacyjny oprogramowania SAP BOBI (zarządzane dyski w warstwie Premium)
- Serwer repozytorium plików (Azure NetApp Files lub pliki Azure Premium)
- Baza danych CMS (Azure Database for MySQL lub baza danych na maszynie wirtualnej platformy Azure)

W poniższej sekcji opisano sposób implementacji strategii tworzenia kopii zapasowych i przywracania dla każdego składnika na platformie SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Kopia zapasowa & przywracanie dla katalogu instalacyjnego SAP BOBI

Najprostszym sposobem tworzenia kopii zapasowych serwerów aplikacji i wszystkich dołączonych dysków jest użycie usługi [Azure Backup](../../../backup/backup-overview.md) na platformie Azure. Zapewnia niezależne i izolowane kopie zapasowe w celu ochrony niezamierzonego zniszczenia danych na maszynach wirtualnych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi funkcjami zarządzania punktami odzyskiwania. Konfiguracja i skalowanie są proste, kopie zapasowe są optymalizowane i mogą być łatwo przywracane w razie potrzeby.

W ramach procesu tworzenia kopii zapasowej wykonywana jest migawka, a dane są przesyłane do magazynu usługi Recovery Service bez wpływu na obciążenia produkcyjne. Migawka zapewnia różny poziom spójności, zgodnie z opisem w artykule [spójności migawek](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . Można również utworzyć kopię zapasową podzbioru dysków danych w maszynie wirtualnej, używając funkcji tworzenia kopii zapasowych i przywracania dysków selektywnych. Aby uzyskać więcej informacji, zobacz dokument i często zadawane pytania dotyczące [usługi Azure VM](../../../backup/backup-azure-vms-introduction.md) [— Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](../../../backup/backup-azure-vm-backup-faq.yml).

#### <a name="backup--restore-for-file-repository-server"></a>Przywracanie & kopii zapasowej dla serwera repozytorium plików

W przypadku **Azure NetApp Files** można utworzyć migawki na żądanie i zaplanować automatyczną migawkę przy użyciu zasad migawek. Kopie migawek zawierają kopię woluminu ANF w danym momencie. Aby uzyskać więcej informacji, zobacz [Zarządzanie migawkami przy użyciu Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure Files** kopia zapasowa jest zintegrowana z natywną usługą [Azure Backup](../../../backup/backup-overview.md) , która scentralizowano funkcję tworzenia kopii zapasowych i przywracania wraz z tworzeniem kopii zapasowych maszyn wirtualnych i upraszcza pracę. Aby uzyskać więcej informacji, zobacz kopia zapasowa i często zadawane pytania dotyczące [udziałów plików platformy Azure](../../../backup/azure-file-share-backup-overview.md) [— Tworzenie kopii zapasowej Azure Files](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Przywracanie & kopii zapasowej dla bazy danych CMS

Usługa Azure Database of MySQL to oferta DBaaS na platformie Azure, która automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub magazynu geograficznie nadmiarowego. Usługa Azure Database of MySQL pobiera kopie zapasowe plików danych i dziennika transakcji. W zależności od obsługiwanego maksymalnego rozmiaru magazynu są to pełne i różnicowe kopie zapasowe (maksymalnie 4 serwery magazynu) lub kopia zapasowa migawek (maksymalnie 16 TB serwerów magazynu). Te kopie zapasowe umożliwiają przywrócenie serwera w dowolnym momencie w ramach skonfigurowanego okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni, a [Opcjonalnie można skonfigurować go](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) do trzech dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Te pliki kopii zapasowej nie są uwidaczniane przez użytkownika i nie można ich eksportować. Te kopie zapasowe mogą być używane tylko w przypadku operacji przywracania w Azure Database for MySQL. Możesz użyć [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) , aby skopiować bazę danych. Aby uzyskać więcej informacji, zobacz [kopia zapasowa i przywracanie w Azure Database for MySQL](../../../mysql/concepts-backup.md).

W przypadku bazy danych zainstalowanej na Virtual Machines można użyć standardowych narzędzi do tworzenia kopii zapasowych lub [Azure Backup](../../../backup/sap-hana-db-about.md) bazy danych Hana. Ponadto, jeśli usługi i narzędzia platformy Azure nie spełniają wymagań, można utworzyć kopię zapasową dysków przy użyciu innych narzędzi do tworzenia kopii zapasowych lub skryptu.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Niezawodność usługi SAP BusinessObjects BI platform

Platforma SAP BusinessObjects BI obejmuje różne warstwy, które są zoptymalizowane pod kątem określonych zadań i operacji. Gdy składnik z jednej warstwy staje się niedostępny, aplikacja SAP BOBI stanie się niedostępna, a niektóre funkcje aplikacji nie będą działać. Dlatego należy upewnić się, że każda warstwa jest zaprojektowana tak, aby zapewnić niezawodne działanie aplikacji bez zakłócania działania firmy.

Ta sekcja koncentruje się na następujących opcjach platformy SAP BOBI —

- **Wysoka dostępność:** Duża dostępna platforma ma co najmniej dwa elementy wszystkich elementów w regionie świadczenia usługi Azure, aby aplikacja działała, jeśli jeden z serwerów przestanie być dostępny.
- **Odzyskiwanie po awarii:** Jest to proces przywracania funkcjonalności aplikacji, jeśli wystąpi spadek strat, taki jak cały region platformy Azure, z powodu klęski żywiołowej.

Implementacja tego rozwiązania różni się w zależności od rodzaju konfiguracji systemu na platformie Azure. Dlatego klient musi dostosować rozwiązanie wysokiej dostępności i odzyskiwania po awarii w oparciu o ich wymagania biznesowe.

### <a name="high-availability"></a>Wysoka dostępność

Wysoka dostępność odnosi się do zestawu technologii, które mogą zminimalizować zakłócenia, zapewniając ciągłość działania aplikacji/usług za pomocą nadmiarowych, odpornych na uszkodzenia lub składników chronionych przez tryb failover w tym samym centrum danych. W naszym przypadku centra danych znajdują się w obrębie jednego regionu świadczenia usługi Azure. [Architektura i scenariusze wysokiej dostępności artykułu dla oprogramowania SAP](sap-high-availability-architecture-scenarios.md) zapewniają wstępny wgląd w różne techniki wysokiej dostępności i rekomendacje oferowane w przypadku aplikacji SAP na platformie Azure, które pomogą wykonać instrukcje podane w tej sekcji.

Na podstawie wyniku zmiany wielkości platformy SAP BOBI należy zaprojektować krajobraz i określić dystrybucję składników analizy biznesowej między Virtual Machinesami i podsieciami platformy Azure. Poziom nadmiarowości w architekturze rozproszonej zależy od wymaganego przez firmę celu czasu odzyskiwania (RTO) i celu punktu odzyskiwania. Platforma SAP BOBI obejmuje różne warstwy i składniki w każdej warstwie, powinny być zaprojektowane w celu zapewnienia nadmiarowości. W związku z tym, jeśli jeden składnik ulegnie awarii, nie ma przerw w działaniu aplikacji SAP BOBI. Na przykład

- Nadmiarowe serwery aplikacji, takie jak serwery aplikacji analizy biznesowej i serwer sieci Web
- Unikatowe składniki, takie jak CMS Database, serwer repozytorium plików, Load Balancer

W poniższej sekcji opisano, jak uzyskać wysoką dostępność na każdym składniku platformy SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Wysoka dostępność dla serwerów aplikacji

W przypadku serwerów aplikacji analizy biznesowej i sieci Web, niezależnie od tego, czy są one instalowane osobno, czy też razem, nie potrzebują określonego rozwiązania o wysokiej dostępności. Wysoką dostępność można osiągnąć przez nadmiarowość, czyli konfigurując wiele wystąpień serwerów analizy biznesowej i sieci Web w różnych Virtual Machines platformy Azure.

Aby zmniejszyć wpływ przestoju ze względu na jedno lub więcej zdarzeń, zaleca się przestrzeganie poniższych zasad wysokiej dostępności dla serwerów aplikacji działających na wielu maszynach wirtualnych.

- Użyj Strefy dostępności, aby chronić awarie centrów danych.
- Skonfiguruj wiele Virtual Machines w zestawie dostępności w celu zapewnienia nadmiarowości.
- Użyj Managed Disks dla maszyn wirtualnych w zestawie dostępności.
- Skonfiguruj poszczególne warstwy aplikacji w osobnych zestawach dostępności.

Aby uzyskać więcej informacji, sprawdź [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux](../../manage-availability.md)

#### <a name="high-availability-for-cms-database"></a>Wysoka dostępność dla bazy danych CMS

Jeśli używasz usługi Azure Database as a Service (DBaaS) dla bazy danych CMS, domyślnie jest dostępna platforma o wysokiej dostępności. Wystarczy wybrać region i usługę nieodłączną dostępność, nadmiarowość i możliwości odporności, bez konieczności konfigurowania dodatkowych składników. Aby uzyskać więcej informacji na temat umowy SLA obsługiwanej oferty DBaaS na platformie Azure, sprawdź [wysoką dostępność w Azure Database for MySQL](../../../mysql/concepts-high-availability.md) i [wysoką dostępność dla Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

W przypadku innych wdrożeń systemu DBMS dla bazy danych CMS zapoznaj się z [przewodnikami wdrażania systemu DBMS dotyczącymi obciążeń SAP](dbms_guide_general.md), które zapewniają wgląd w różne wdrożenie systemu DBMS i podejście do uzyskania wysokiej dostępności.

#### <a name="high-availability-for-file-repository-server"></a>Wysoka dostępność dla serwera repozytorium plików

Serwer repozytorium plików (FRS) odnosi się do katalogów dysków, w których są przechowywane zawartości, takie jak raporty, miejsca i połączenia. Jest ona udostępniana na wszystkich serwerach aplikacji tego systemu. Dlatego należy się upewnić, że jest ona wysoce dostępna.

Na platformie Azure Możesz wybrać [pliki usługi Azure Premium](../../../storage/files/storage-files-introduction.md) lub [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) dla udziału plików, który ma być wysoce dostępny i wysoce trwały. Aby uzyskać więcej informacji, zobacz sekcję [nadmiarowości](../../../storage/files/storage-files-planning.md#redundancy) dla Azure Files.

> [!NOTE]
> Protokół SMB dla Azure Files jest ogólnie dostępny, ale obsługa protokołu NFS dla Azure Files jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna NFS 4,1 dla Azure Files jest teraz w wersji zapoznawczej](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Ponieważ ta usługa udziału plików nie jest dostępna we wszystkich regionach, należy się upewnić, że zapoznaj się z tematem [produkty dostępne według regionów](https://azure.microsoft.com/en-us/global-infrastructure/services/) , aby dowiedzieć się, jak uzyskać aktualne informacje. Jeśli usługa nie jest dostępna w Twoim regionie, można utworzyć serwer systemu plików NFS, z którego można udostępnić system plików aplikacji SAP BOBI. Należy również wziąć pod uwagę jego wysoką dostępność.

#### <a name="high-availability-for-load-balancer"></a>Wysoka dostępność dla usługi równoważenia obciążenia

Aby dystrybuować ruch między serwerami sieci Web, można użyć Azure Load Balancer lub Application Gateway platformy Azure. Nadmiarowość dla dowolnego modułu równoważenia obciążenia można osiągnąć w oparciu o jednostkę SKU wybraną do wdrożenia.

- Aby uzyskać Azure Load Balancer, nadmiarowość można osiągnąć przez skonfigurowanie usługa Load Balancer w warstwie Standardowa frontonu jako strefy nadmiarowej. Aby uzyskać więcej informacji, zobacz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Aby uzyskać Application Gateway, można uzyskać wysoką dostępność na podstawie typu warstwy wybranej podczas wdrażania.
  - jednostka SKU w wersji 1 obsługuje scenariusze wysokiej dostępności, gdy wdrożono dwa lub więcej wystąpień. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby upewnić się, że wystąpienia nie będą działać w tym samym czasie. Tak więc w przypadku tej jednostki SKU można osiągnąć nadmiarowość w obrębie strefy
  - jednostka SKU v2 automatycznie zapewnia, że nowe wystąpienia są rozłożone w domenach błędów i domenach aktualizacji. W przypadku wybrania nadmiarowości strefy najnowsze wystąpienia są również rozproszone w różnych strefach dostępności, aby zaoferować odporność na awarie. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Architektura wysokiej dostępności referencyjnej dla platformy SAP BusinessObjects BI

W poniższej architekturze referencyjnej opisano konfigurację platformy SAP BOBI przy użyciu zestawu dostępności, która zapewnia nadmiarowość i dostępność maszyn wirtualnych w strefie. Architektura umożliwia korzystanie z różnych usług platformy Azure, takich jak Azure Application Gateway, Azure NetApp Files i Azure Database for MySQL dla platformy SAP BOBI, która oferuje wbudowaną nadmiarowość, co zmniejsza złożoność zarządzania różnymi rozwiązaniami wysokiej dostępności.

Na poniższej ilustracji ruch przychodzący (HTTPS-TCP/443) jest zrównoważony przy użyciu jednostki SKU platformy Azure Application Gateway V1, która jest wysoce dostępna w przypadku wdrożenia w dwóch lub większej liczbie wystąpień. Wiele wystąpień serwera sieci Web, serwerów zarządzania i serwerów przetwarzania są wdrażane w osobnych Virtual Machinesach w celu zapewnienia nadmiarowości, a każda warstwa jest wdrażana w różnych zestawach dostępności. Azure NetApp Files ma wbudowaną nadmiarowość w centrum danych, więc woluminy ANF dla serwera repozytorium plików będą wysoce dostępne. Obsługa bazy danych CMS została zainicjowana na Azure Database for MySQL (DBaaS), która ma nieodłączną wysoką dostępność. Aby uzyskać więcej informacji, zobacz [wysoka dostępność w](../../../mysql/concepts-high-availability.md) przewodniku Azure Database for MySQL.

![Nadmiarowość platformy SAP BusinessObjects BI przy użyciu zestawów dostępności](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Powyższa architektura zawiera szczegółowe informacje na temat sposobu wdrażania rozwiązań SAP BOBI na platformie Azure. Nie dotyczy to jednak wszystkich możliwych opcji konfiguracji platformy SAP BOBI na platformie Azure. Klient może dostosować wdrożenie w zależności od wymagań firmy, wybierając różne produkty/usługi dla różnych składników, takich jak Load Balancer, serwer repozytorium plików i system DBMS.

W kilku regionach świadczenia usługi Azure są oferowane Strefy dostępności, co oznacza, że ma niezależną dostawę do źródła prądu, chłodzenia i sieci. Umożliwia klientowi wdrażanie aplikacji w dwóch lub trzech strefach dostępności. Klienci, którzy chcą uzyskać wysoką dostępność w ramach usługi AZs, mogą wdrożyć platformę SAP BOBI w różnych strefach dostępności, upewniając się, że każdy składnik w aplikacji jest nadmiarowy strefy.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Instrukcje w tej sekcji wyjaśniają strategię zapewniania ochrony przed odzyskiwaniem po awarii dla platformy SAP BOBI. Uzupełnia ona [odzyskiwanie po awarii dla dokumentu SAP](../../../site-recovery/site-recovery-sap.md) , który reprezentuje podstawowe zasoby dla ogólnego podejścia do odzyskiwania po awarii oprogramowania SAP.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Architektura odzyskiwania po awarii odwołania dla platformy SAP BusinessObjects BI

Ta architektura referencyjna uruchamia wdrożenie wielu wystąpień platformy SAP BOBI z nadmiarowymi serwerami aplikacji. W przypadku odzyskiwania po awarii należy przełączyć warstwę do trybu failover do regionu pomocniczego. Każda warstwa używa innej strategii w celu zapewnienia ochrony przed odzyskiwaniem po awarii.

![Odzyskiwanie po awarii platformy SAP BusinessObjects BI](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Moduł równoważenia obciążenia

Load Balancer służy do dystrybuowania ruchu między serwerami aplikacji sieci Web platformy SAP BOBI. Aby uzyskać Application Gateway dla platformy Azure, zaimplementuj konfigurację równoległą bramy Application Gateway w regionie pomocniczym.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Maszyny wirtualne z serwerami aplikacji sieci Web i analizy biznesowej

Usługa Azure Site Recovery może służyć do replikowania Virtual Machines uruchomionych serwerów aplikacji sieci Web i analizy biznesowej w regionie pomocniczym. Replikuje serwery w regionie pomocniczym, dzięki czemu w przypadku awarii i przestoju można łatwo przejść do trybu failover w replikowanym środowisku i kontynuować pracę

#### <a name="file-repository-servers"></a>Serwery repozytorium plików

- **Azure NetApp Files** udostępnia woluminy NFS i SMB, dlatego można użyć dowolnego narzędzia do kopiowania opartego na plikach do replikowania danych między regionami platformy Azure. Aby uzyskać więcej informacji na temat kopiowania woluminu ANF w innym regionie, zobacz [często zadawane pytania dotyczące Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Możesz użyć Azure NetApp Files replikacji między regionami, która jest obecnie dostępna w [wersji zapoznawczej](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) , która korzysta z technologii NetApp SnapMirror®. Dlatego tylko zmienione bloki są wysyłane przez sieć w skompresowanym i wydajnym formacie. Ta technologia własnościowa minimalizuje ilość danych wymaganych do replikacji w regionach, co pozwala zaoszczędzić koszty transferu danych. Skraca również czas replikacji, aby można było osiągnąć mniejszy cel punktu przywracania (RPO). Aby uzyskać więcej informacji, zapoznaj się z [wymaganiami i zagadnieniami dotyczącymi korzystania z replikacji między regionami](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) .

- **Pliki systemu Azure Premium** obsługują tylko lokalnie nadmiarowy (LRS) i magazyn strefowo nadmiarowy (ZRS). W przypadku strategii odzyskiwania plików na platformie Azure Premium możesz użyć [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) lub [Azure PowerShell](/powershell/module/az.storage/) , aby skopiować pliki do innego konta magazynu w innym regionie. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Baza danych CMS

Azure Database for MySQL udostępnia wiele opcji odzyskania bazy danych w przypadku awarii. Wybierz odpowiednią opcję, która działa dla Twojej firmy.

- Włącz repliki odczytu między regionami, aby zwiększyć ciągłość działania i planowanie odzyskiwania po awarii. Można replikować z serwera źródłowego do maksymalnie pięciu replik. Repliki odczytu są aktualizowane asynchronicznie przy użyciu technologii replikacji binarnych dzienników MySQL. Repliki to nowe serwery, którymi można zarządzać podobnie jak regularne Azure Database for MySQL serwery. Dowiedz się więcej na temat odczytywania replik, dostępnych regionów, ograniczeń oraz sposobu przełączenia w tryb failover z [artykułu pojęć dotyczących replik](../../../mysql/concepts-read-replicas.md).

- Użyj funkcji przywracania geograficznego Azure Database for MySQL, która przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym znajduje się serwer, jest w trybie offline. Można przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer do trybu online.

  > [!NOTE]
  > Przywracanie geograficzne jest możliwe tylko w przypadku aprowizacji serwera z magazynem kopii zapasowych nadmiarowego. Zmiana **opcji nadmiarowości kopii zapasowej** po utworzeniu serwera nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz artykuł [nadmiarowości kopii zapasowej](../../../mysql/concepts-backup.md#backup-redundancy-options) .

Poniżej znajduje się zalecenie dotyczące odzyskiwania po awarii dla każdej warstwy używanej w tym przykładzie.

| Warstwy platformy SAP BOBI   | Zalecenie                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Równoległa konfiguracja Application Gateway w regionie pomocniczym                                                |
| Serwery aplikacji sieci Web   | Replikacja przy użyciu Site Recovery                                                                         |
| Serwery aplikacji analizy biznesowej    | Replikacja przy użyciu Site Recovery                                                                         |
| Azure NetApp Files        | Narzędzie kopiowania oparte na plikach do replikowania danych do regionu pomocniczego **lub** replikacji między regionami ANF (wersja zapoznawcza) |
| Azure Database for MySQL  | Repliki odczytu między regionami **lub** przywracania kopii zapasowych z kopii lustrzanych nadmiarowych.                             |

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia aplikacji SAP](../../../site-recovery/site-recovery-sap.md)
- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP](planning-guide.md)
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP](deployment-guide.md)
- [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP](./dbms_guide_general.md)
