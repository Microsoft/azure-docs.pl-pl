---
title: Wdrażanie platformy SAP BusinessObjects BI na platformie Azure dla systemu Linux | Microsoft Docs
description: Wdrażanie i konfigurowanie platformy SAP BusinessObjects BI Platform na platformie Azure dla systemu Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520024"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Przewodnik wdrażania platformy SAP BusinessObjects BI dla systemu Linux na platformie Azure

W tym artykule opisano strategię wdrażania platformy SAP BOBI na platformie Azure dla systemu Linux. W tym przykładzie dwie maszyny wirtualne z SSD w warstwie Premium Dyski zarządzane jako katalogiem instalacji. Azure Database for MySQL jest używana dla bazy danych programu CMS, a Azure NetApp Files dla serwera repozytorium plików jest współużytkowana na obu serwerach. Domyślna aplikacja internetowa Tomcat Java i aplikacja platformy usługi BI są instalowane razem na obu maszynach wirtualnych. Aby zrównoważyć obciążenie żądania użytkownika, Application Gateway ma natywne funkcje odciążania protokołu TLS/SSL.

Ten typ architektury jest skuteczny w przypadku małych wdrożeń lub nieprodukcyjnych środowisk. W przypadku wdrożenia produkcyjnego lub na dużą skalę można mieć oddzielne hosty dla aplikacji internetowej oraz wiele hostów aplikacji BOBI, co umożliwia serwerowi przetwarzanie większej ilości informacji.

![Wdrażanie oprogramowania SAP BOBI na platformie Azure dla systemu Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

W tym przykładzie jest używany poniżej wersji produktu i układ systemu plików

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (wersja: 8.0.15)
- Łącznik interfejsu API języka C mySQL — libmysqlclient (wersja: 6.1.11)

| System plików        | Opis                                                                                                               | Rozmiar (GB)             | Właściciel  | Group (Grupa)  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | System plików do instalacji wystąpienia SAP BOBI, domyślna aplikacja internetowa Tomcat i sterowniki bazy danych (w razie potrzeby) | Wytyczne dotyczące rozmiarów oprogramowania SAP | bl1adm | sapsys | Zarządzany dysk w warstwie Premium — SSD |
| /usr/sap/frsinput  | Katalog instalacji jest dla plików udostępnionych na wszystkich hostach BOBI, które będą używane jako katalog repozytorium plików wejściowych  | Potrzeby biznesowe         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Katalog instalacji jest dla plików udostępnionych na wszystkich hostach BOBI, które będą używane jako katalog repozytorium plików wyjściowych | Potrzeby biznesowe         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Wdrażanie maszyny wirtualnej z systemem Linux za pośrednictwem Azure Portal

W tej sekcji utworzymy dwie maszyny wirtualne z obrazem systemu operacyjnego Linux dla platformy SAP BOBI. Kroki wysokiego poziomu w celu utworzenia Virtual Machines są następujące:

1. Tworzenie [grupy zasobów](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Utwórz [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Nie używaj pojedynczej podsieci dla wszystkich usług platformy Azure we wdrożeniu platformy SAP BI. W oparciu o architekturę platformy SAP BI Platform należy utworzyć wiele podsieci. W tym wdrożeniu utworzymy trzy podsieci — podsieć aplikacji, podsieć magazynu repozytoriów plików i podsieć Application Gateway Podsieci.
   - Na platformie Azure Application Gateway i Azure NetApp Files zawsze muszą być w oddzielnej podsieci. Zobacz [Azure Application Gateway](../../../application-gateway/configuration-overview.md) i [wskazówki dotyczące Azure NetApp Files planowania sieci,](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) aby uzyskać więcej szczegółów.

3. Utwórz zestaw dostępności.

   - Aby uzyskać nadmiarowość dla każdej warstwy we wdrożeniu z wieloma wystąpieniami, umieść maszyny wirtualne dla każdej warstwy w zestawie dostępności. Upewnij się, że zestawy dostępności są oddzielone dla każdej warstwy na podstawie architektury.

4. Utwórz maszynę wirtualną 1 **(azusbosl1).**

   - Możesz użyć obrazu niestandardowego lub wybrać obraz z Azure Marketplace. Zapoznaj się [z tematem Deploying a VM from the Azure Marketplace for SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) (Wdrażanie maszyny wirtualnej na potrzeby oprogramowania SAP) lub [Deploying a VM with a custom image for SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) (Wdrażanie maszyny wirtualnej z niestandardowym obrazem dla oprogramowania SAP w zależności od potrzeb).

5. Utwórz maszynę wirtualną 2 **(azusbosl2).**
6. Dodaj jeden SSD w warstwie Premium dysku. Będzie on używany jako katalog instalacyjny SAP BOBI.

## <a name="provision-azure-netapp-files"></a>Aprowizuj Azure NetApp Files

Przed kontynuowaniem konfigurowania usługi Azure NetApp Files zapoznaj się z dokumentacją usługi Azure [NetApp Files.](../../../azure-netapp-files/azure-netapp-files-introduction.md)

Azure NetApp Files jest dostępna w kilku regionach [świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Sprawdź, czy wybrany region świadczenia usługi Azure oferuje Azure NetApp Files.

Użyj [Azure NetApp Files dostępność według regionów platformy Azure,](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) aby sprawdzić dostępność Azure NetApp Files według regionów.

Przed wdrożeniem aplikacji Azure NetApp Files żądanie dołączania do usługi Azure NetApp Files się w celu Azure NetApp Files do Azure NetApp Files. [](../../../azure-netapp-files/azure-netapp-files-register.md)

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie Azure NetApp Files zasobów

W poniższych instrukcjach przyjęto założenie, że sieć wirtualna platformy Azure została [już wdrożona.](../../../virtual-network/virtual-networks-overview.md) Zasoby Azure NetApp Files i maszyny wirtualne, na których zostaną zainstalowane zasoby usługi Azure NetApp Files, muszą zostać wdrożone w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych platformy Azure.

1. Jeśli zasoby nie zostały jeszcze wdrożone, [zażądaj](../../../azure-netapp-files/azure-netapp-files-register.md)do Azure NetApp Files .

2. Utwórz konto usługi NetApp w wybranym regionie platformy Azure, zgodnie z instrukcjami w tece [Tworzenie konta usługi NetApp.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3. Skonfiguruj pulę Azure NetApp Files pojemności, zgodnie z instrukcjami w te Azure NetApp Files [puli pojemności.](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

   - Architektura platformy SAP BI przedstawiona w tym artykule używa jednej Azure NetApp Files pojemności na poziomie *usługi Premium.* W przypadku serwera repozytorium plików SAP BI na platformie Azure zalecamy użycie usługi Azure NetApp Files *Premium* lub *Ultra.* [](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4. Deleguj podsieć do Azure NetApp Files, zgodnie z opisem w instrukcjach w tece [Delegowanie](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)podsieci do Azure NetApp Files .

5. Wd Azure NetApp Files woluminów sieciowych, zgodnie z instrukcjami w te [tematu Create an NFS volume for Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)(Tworzenie woluminu NFS dla Azure NetApp Files ).

   Wolumin ANF można wdrożyć jako NFSv3 i NFSv4.1, ponieważ oba protokoły są obsługiwane w przypadku platformy SAP BOBI. Wd wdrażaj woluminy w odpowiedniej Azure NetApp Files podsieci. Adresy IP woluminów Usługi Azure NetApp są przypisywane automatycznie.

Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych platformy Azure. Na przykład azusbobi-frsinput, azusbobi-frsoutput to nazwy woluminów i nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput są ścieżkami plików dla woluminów Azure NetApp Files woluminów.

- Wolumin azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Wolumin azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Istotne zagadnienia

Podczas tworzenia aplikacji dla Azure NetApp Files SAP BOBI Platform File Repository Server należy pamiętać o następujących zagadnieniach:

1. Minimalna pula pojemności to 4 tebibajty (TiB).
2. Minimalny rozmiar woluminu to 100 gibibajtów (GiB).
3. Azure NetApp Files i wszystkie maszyny wirtualne, na których zostaną zainstalowane woluminy Azure NetApp Files, muszą [](../../../virtual-network/virtual-network-peering-overview.md) znajdować się w tej samej sieci wirtualnej platformy Azure lub w wirtualnych sieciach równorzędnych w tym samym regionie. Azure NetApp Files jest teraz obsługiwany dostęp za pośrednictwem komunikacji równorzędnej sieci wirtualnych w tym samym regionie. Dostęp do usługi Azure NetApp za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
4. Wybrana sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
5. Za pomocą Azure NetApp Files [eksportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)można kontrolować dozwolonych klientów, typ dostępu (odczyt-zapis, tylko do odczytu itd.).
6. Funkcja Azure NetApp Files nie ma jeszcze funkcji strefowej. Obecnie funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Należy pamiętać o potencjalnych konsekwencjach opóźnienia w niektórych regionach świadczenia usługi Azure.
7. Azure NetApp Files można wdrożyć jako woluminy NFSv3 lub NFSv4.1. Oba protokoły są obsługiwane w przypadku aplikacji platformy SAP BI.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurowanie systemów plików na serwerach z systemem Linux

W krokach w tej sekcji są następujące prefiksy:

**[A]**: Krok ma zastosowanie do wszystkich hostów

### <a name="format-and-mount-sap-file-system"></a>Formatowanie i montowania systemu plików SAP

1. **[A] Lista** wszystkich dołączonych dysków

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

2. **[A] Formatuj** urządzenie blokowe dla /usr/sap

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A] Tworzenie** katalogu instalacji

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Pobierz UUID urządzenia blokowego

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Obsługa wpisu instalacji systemu plików w /etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A] Zainstaluj** system plików

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

### <a name="mount-azure-netapp-files-volume"></a>Zainstaluj Azure NetApp Files woluminu

1. **[A]** Tworzenie katalogów instalacji

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Skonfiguruj system operacyjny klienta do obsługi instalacji NFSv4.1 (dotyczy tylko w przypadku korzystania z **NFSv4.1)**

   Jeśli używasz woluminów Azure NetApp Files z protokołem NFSv4.1, wykonaj następującą konfigurację na wszystkich maszyn wirtualnych, na których należy Azure NetApp Files NFSv4.1.

   **Weryfikowanie ustawień domeny NFS**

   Upewnij się, że domena jest skonfigurowana jako domyślna  Azure NetApp Files, czyli defaultv4iddomain.com, a mapowanie jest **ustawione** na nikt.

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
   > Upewnij się, że ustawiono domenę NFS w pliku /etc/idmapd.conf na maszynie wirtualnej, aby dopasować domyślną konfigurację domeny na Azure NetApp Files: **defaultv4iddomain.com**. Jeśli wystąpi niezgodność między konfiguracją domeny na kliencie systemu plików NFS (tj. maszynie wirtualnej) a serwerem NFS, tj. konfiguracji usługi Azure NetApp, uprawnienia do plików na woluminach usługi Azure NetApp zainstalowanych na maszynach wirtualnych będą wyświetlane jako nikt.

   Zweryfikuj `nfs4_disable_idmapping` . Powinna być ustawiona wartość **Y**. Aby utworzyć strukturę katalogów, w `nfs4_disable_idmapping` której się znajduje, wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze /sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.

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

   W przypadku korzystania z systemu plików NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   W przypadku korzystania z systemu plików NFSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** Zainstaluj woluminy NFS

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

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurowanie bazy danych programu CMS — Azure Database for MySQL

Ta sekcja zawiera szczegółowe informacje na temat Azure Database for MySQL przy użyciu Azure Portal. Zawiera on również instrukcje dotyczące tworzenia baz danych cms i inspekcji dla platformy SAP BOBI oraz konta użytkownika w celu uzyskania dostępu do bazy danych.

Wytyczne mają zastosowanie tylko wtedy, gdy używasz Azure DB for MySQL. Instrukcje dotyczące innych baz danych można znaleźć w dokumentacji oprogramowania SAP lub specyficznej dla bazy danych.

### <a name="create-an-azure-database-for-mysql"></a>Tworzenie bazy danych platformy Azure dla programu MySQL

Zaloguj się do Azure Portal i wykonaj kroki opisane w przewodniku Szybki [start](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)Azure Database for MySQL . Kilka punktów, na które należy zwrócić uwagę podczas Azure Database for MySQL —

1. Wybierz ten sam region dla Azure Database for MySQL, w którym działają serwery aplikacji platformy SAP BI.

2. Wybierz obsługiwaną wersję bazy danych na podstawie macierzy dostępności [produktu (PAM) dla usługi SAP BI](https://support.sap.com/pam) specyficznej dla danej wersji SAP BOBI. Postępuj zgodnie z tymi samymi wytycznymi dotyczącymi zgodności, które zostały rozwiązane dla usługi MySQL AB w programie SAP PAM

3. W warstwie "obliczenia+magazyn" wybierz pozycję **Konfiguruj serwer** i wybierz odpowiednią warstwę cenową na podstawie rozmiaru danych wyjściowych.

4. **Automatyczne skalowanie magazynu** jest domyślnie włączone. Należy pamiętać, że [magazyn można](../../../mysql/concepts-pricing-tiers.md#storage) skalować tylko w górę, a nie w dół.

5. Domyślnie okres **przechowywania kopii zapasowej** wynosi siedem dni, ale [opcjonalnie](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) można go skonfigurować do 35 dni.

6. Kopie zapasowe Azure Database for MySQL są domyślnie lokalnie nadmiarowe, więc jeśli chcesz, aby kopie  zapasowe serwera były przechowywane w magazynie geograficznie nadmiarowym, wybierz pozycję Geograficznie nadmiarowe w obszarze Opcje **nadmiarowości kopii zapasowej.**

> [!NOTE]
> Zmiana opcji [nadmiarowości kopii zapasowej](../../../mysql/concepts-backup.md#backup-redundancy-options) po utworzeniu serwera nie jest obsługiwana.

### <a name="configure-connection-security"></a>Konfigurowanie zabezpieczeń połączeń

Domyślnie utworzony serwer jest chroniony przez zaporę i nie jest dostępny publicznie. Aby zapewnić dostęp do sieci wirtualnej, w której działają serwery aplikacji platformy SAP BI, wykonaj następujące kroki:  

1. Przejdź do zasobów serwera w Azure Portal i wybierz pozycję **Zabezpieczenia** połączeń z menu po lewej stronie dla zasobu serwera.
2. Wybierz **pozycję Tak,** **aby zezwolić na dostęp do usług platformy Azure.**
3. W obszarze Reguły sieci wirtualnej wybierz **pozycję Dodawanie istniejącej sieci wirtualnej.** Wybierz sieć wirtualną i podsieć serwera aplikacji platformy SAP BI. Należy również zapewnić dostęp do serwera przeskoku lub innych serwerów, z których można połączyć program [MySQL Workbench](../../../mysql/connect-workbench.md) z Azure Database for MySQL. Baza danych programu MySQL Workbench zostanie użyta do utworzenia bazy danych cms i inspekcji
4. Po dodaniu sieci wirtualnych wybierz pozycję **Zapisz.**

### <a name="create-cms-and-audit-database"></a>Tworzenie bazy danych cms i inspekcji

1. Pobierz i zainstaluj aplikację MySQL Workbench z [witryny internetowej MySQL.](https://dev.mysql.com/downloads/workbench/) Upewnij się, że instalujesz aplikację MySQL Workbench na serwerze, który może uzyskać dostęp Azure Database for MySQL.

2. Nawiązywanie połączenia z serwerem przy użyciu aplikacji MySQL Workbench. Postępuj zgodnie z instrukcjami wymienionymi w tym [artykule.](../../../mysql/connect-workbench.md#get-connection-information) Jeśli test połączenia zostanie pomyślny, zostanie wyświetlony następujący komunikat :

   ![Połączenie z usługą SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Na karcie Zapytanie SQL uruchom poniższe zapytanie, aby utworzyć schemat dla bazy danych cms i inspekcji.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Tworzenie konta użytkownika w celu nawiązania połączenia ze schematem

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

5. Aby sprawdzić uprawnienia i role konta użytkownika mySQL

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

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Instalowanie łącznika interfejsu API języka C mySQL (libmysqlclient) na serwerze z systemem Linux

Aby serwer aplikacji SAP BOBI uzyskać dostęp do bazy danych, wymaga klienta/sterowników bazy danych. Łącznik interfejsu API języka C mySQL dla systemu Linux musi być używany do uzyskiwania dostępu do baz danych cms i inspekcji. Połączenie ODBC z bazą danych programu CMS nie jest obsługiwane. Ta sekcja zawiera instrukcje dotyczące sposobu skonfigurowania łącznika interfejsu API języka C mySQL w systemie Linux.

1. Zapoznaj się ze sterownikami i narzędziami do zarządzania [MySQL](../../../mysql/concepts-compatibility.md) zgodnymi z Azure Database for MySQL, w którym opisano sterowniki zgodne z Azure Database for MySQL. Sprawdź sterownik **łącznika MySQL/C (libmysqlclient)** w artykule.

2. Skorzystaj z tego [linku, aby](https://downloads.mysql.com/archives/c-c/) pobrać sterowniki.

3. Wybierz system operacyjny i pobierz pakiet rpm składnika udostępnionego łącznika MySQL. W tym przykładzie jest używana wersja łącznika mysql-connector-c-shared-6.1.11.

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

6. Ustaw LD_LIBRARY_PATH, aby `/usr/lib64` wskazać katalog dla konta użytkownika, które będzie używane do instalacji.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Przygotowanie serwera

W krokach w tej sekcji są następujące prefiksy:

**[A]**: Krok ma zastosowanie do wszystkich hostów.

1. **[A]** W zależności od wersji systemu Linux (SLES lub RHEL) należy ustawić parametry jądra i zainstalować wymagane biblioteki. Zapoznaj się z **sekcją Wymagania systemowe** w przewodniku instalacji platformy analizy [biznesowej dla systemu Unix.](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)

2. **[A]** Upewnij się, że strefa czasowa na maszynie została prawidłowo ustawiona. Zapoznaj się z [sekcją Dodatkowe wymagania dotyczące systemów Unix i Linux w](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) przewodniku instalacji.

3. **[A]** Utwórz konto użytkownika **(bl1** adm) i grupę (sapsys), w ramach której mogą być uruchamiane procesy oprogramowania w tle. To konto umożliwia wykonanie instalacji i uruchomienie oprogramowania. Konto nie wymaga uprawnień użytkownika głównego.

4. **[A]** Ustaw środowisko konta użytkownika **(bl1** adm) do używania obsługiwanych ustawieniach regionalnych UTF-8 i upewnij się, że oprogramowanie konsolowe obsługuje zestawy znaków UTF-8. Aby upewnić się, że system operacyjny używa prawidłowych wartości regionalnych, ustaw zmienne środowiskowe LC_ALL i LANG na preferowane ustawienia lokalne w środowisku użytkownika (**bl1** adm).

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Konfigurowanie konta użytkownika **(bl1** adm).

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

6. Pobierz i wyodrębnij nośnik dla platformy SAP BusinessObjects BI Platform z witryny SAP Service Marketplace.

## <a name="installation"></a>Instalacja

Sprawdź ustawienia lokalne konta użytkownika **bl1** adm na serwerze

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Przejdź do nośnika platformy SAP BusinessObjects BI i uruchom poniższe polecenie z **użytkownikiem bl1** adm —

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Postępuj [zgodnie z przewodnikiem instalacji platformy SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) dla systemu Unix specyficznym dla Danej wersji. Kilka punktów, na które należy zwrócić uwagę podczas instalowania platformy SAP BOBI.

- Na **ekranie Konfigurowanie** rejestracji produktu można użyć tymczasowego klucza licencji dla rozwiązań SAP BusinessObjects z oprogramowania SAP Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) lub wygenerować klucz licencji w witrynie SAP Service Marketplace

- Na **ekranie** Wybierz  typ instalacji wybierz pozycję Pełna instalacja na pierwszym serwerze (azusbosl1), a dla innego serwera (azusbosl2) wybierz pozycję **Niestandardowy/Rozwiń,** co spowoduje rozwinięcie istniejącej konfiguracji bobi.

- Na **ekranie Wybierz domyślną lub** istniejącą bazę danych wybierz pozycję Skonfiguruj istniejącą **bazę** danych, co spowoduje monit o wybranie poleceń CMS i Audit database. Wybierz **pozycję MySQL dla** opcji Typ bazy danych programu CMS i Typ bazy danych inspekcji.

  Możesz również wybrać pozycję Brak bazy danych inspekcji, jeśli nie chcesz konfigurować inspekcji podczas instalacji.

- Wybierz odpowiednie opcje na **ekranie Wybieranie serwera aplikacji internetowej Java** na podstawie architektury SAP BOBI. W tym przykładzie wybraliśmy opcję 1, która instaluje serwer tomcat na tej samej platformie SAP BOBI.

- Wprowadź informacje o bazie danych programu CMS w **te tematu Configure CMS Repository Database - MySQL (Konfigurowanie bazy danych repozytorium programu CMS — MySQL).** Przykładowe dane wejściowe dla informacji o bazie danych cms dla instalacji systemu Linux. Azure Database for MySQL jest używany na domyślnym porcie 3306
  
  ![Wdrażanie oprogramowania SAP BOBI w systemie Linux — baza danych PROGRAMU CMS](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Opcjonalnie) Wprowadź informacje o bazie danych inspekcji w **konfiguracji bazy danych repozytorium inspekcji — MySQL.** Przykładowe dane wejściowe inspekcji informacji o bazie danych dla instalacji systemu Linux.

  ![Wdrażanie oprogramowania SAP BOBI w systemie Linux — baza danych inspekcji](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Postępuj zgodnie z instrukcjami i wprowadź wymagane dane wejściowe, aby ukończyć instalację.

W przypadku wdrożenia z wieloma wystąpieniami uruchom konfigurację instalacji na drugim hoście (azusbosl2). Na **ekranie Wybierz typ instalacji** wybierz pozycję **Niestandardowy/Rozwiń,** co spowoduje rozwinięcie istniejącej konfiguracji bobi.

W ofercie usługi Azure Database for MySQL brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. Dlatego w programie Central Management Console (CMC) znajdziesz inną wersję bazy danych, która zasadniczo jest wersją ustawioną na bramie. Aby [uzyskać więcej informacji, Azure Database for MySQL obsługiwane](../../../mysql/concepts-supported-versions.md) wersje serwera.

![Wdrażanie oprogramowania SAP BOBI w systemie Linux — ustawienia cmc](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

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

### <a name="tomcat-clustering---session-replication"></a>Klastrowanie Tomcat — replikacja sesji

Serwer Tomcat obsługuje klastrowanie co najmniej dwóch serwerów aplikacji w celu replikacji sesji i trybu failover. Sesje platformy SAP BOBI są serializowane, a sesja użytkownika może bezproblemowo przejść w trybu fail over do innego wystąpienia serwera tomcat, nawet jeśli serwer aplikacji ulegnie awarii.

Na przykład jeśli użytkownik jest połączony z serwerem internetowym, który ulegnie awarii, gdy użytkownik nawiguje po hierarchii folderów w aplikacji SAP BI. W przypadku prawidłowo skonfigurowanego klastra użytkownik może kontynuować nawigowanie po hierarchii folderów bez przekierowania do strony logowania.

Uwaga [2808640](https://launchpad.support.sap.com/#/notes/2808640)oprogramowania SAP umożliwia skonfigurowanie klastrowania tomcat przy użyciu multiemisji. Jednak na platformie Azure multiemisja nie jest obsługiwana. Aby klaster Tomcat działał na platformie Azure, należy użyć [klasy StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (uwaga [SAP 2764907).](https://launchpad.support.sap.com/#/notes/2764907) Aby skonfigurować klaster tomcat na platformie Azure, zobacz blog [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) (Klastrowanie Tomcat przy użyciu członkostwa statycznego dla platformy SAP BusinessObjects BI Platform).

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Warstwa internetowa równoważenia obciążenia platformy SAP BI

W przypadku wdrażania wielu wystąpień systemu SAP BOBI serwery aplikacji internetowej Java (warstwa internetowa) działają na co najmniej dwóch hostach. Aby równomiernie dystrybuować obciążenie użytkowników między serwerami internetowymi, można użyć usługi równoważenia obciążenia między użytkownikami i serwerami internetowymi. Na platformie Azure do zarządzania ruchem do serwerów aplikacji internetowych można Azure Load Balancer lub Azure Application Gateway za pomocą usługi azure. Szczegółowe informacje o każdej ofercie zostały wyjaśnione w poniższej sekcji.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (oparty na sieci)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) jest wysoką wydajnością i małym opóźnieniem w warstwie 4 (TCP, UDP), który dystrybuuje ruch między sieciami o dobrej Virtual Machines. Sonda kondycji usługi równoważenia obciążenia monitoruje dany port na każdej maszynie wirtualnej i dystrybuuje ruch tylko do operacyjnych maszyn wirtualnych. Możesz wybrać publiczny lub wewnętrzny usługę równoważenia obciążenia w zależności od tego, czy platforma SAP BI Ma być dostępna z Internetu, czy nie. Jest strefowo nadmiarowy, zapewniając wysoką dostępność Strefy dostępności.

Zapoznaj się z sekcją Load Balancer na poniższej ilustracji, w której serwer aplikacji internetowej działa na porcie 8080 (domyślnym porcie HTTP serwera Tomcat), który będzie monitorowany przez sondę kondycji. Dlatego każde żądanie przychodzące od użytkowników końcowych zostanie przekierowane do serwerów aplikacji internetowych (azusbosl1 lub azusbosl2) w puli zaplecza. Równoważenie obciążenia nie obsługuje kończania protokołu TLS/SSL (znanego również jako odciążanie protokołu TLS/SSL). Jeśli używasz usługi Azure Load Balancer do dystrybucji ruchu między serwerami internetowymi, zalecamy użycie usługa Load Balancer w warstwie Standardowa.

> [!NOTE]
> Jeśli maszyny wirtualne bez publicznych adresów IP zostaną umieszczone w puli zaplecza wewnętrznego (bez publicznego adresu IP) usługi Load Balancer w witrynie Azure Standard, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja zezwalania na routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz Public [endpoint connectivity for Virtual Machines using Azure usługa Load Balancer w warstwie Standardowa in SAP high-availability scenarios](high-availability-guide-standard-load-balancer-outbound-connections.md)(Łączność z publicznym punktem końcowym dla usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności oprogramowania SAP).

![Azure Load Balancer równoważenia ruchu między serwerami internetowymi](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (usługa równoważenia obciążenia aplikacji internetowej)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) zapewniają kontroler dostarczania aplikacji (ADC, Application Delivery Controller) jako usługę, która pomaga aplikacji kierować ruch użytkowników do co najmniej jednego serwera aplikacji internetowej. Oferuje ona różne możliwości równoważenia obciążenia warstwy 7, takie jak odciążanie protokołu TLS/SSL, Web Application Firewall (WAF), koligacja sesji na podstawie plików cookie i inne dla Twoich aplikacji.

Na platformie SAP BI Gateway brama aplikacji kieruje ruch internetowy aplikacji do określonych zasobów w puli zaplecza — azusbosl1 lub azusbos2. Przypiszesz odbiornik do portu, utworzysz reguły i dodasz zasoby do puli zaplecza. Na poniższej ilustracji brama aplikacji z prywatnym adresem IP frontonu (10.31.3.20) działa jako punkt wejścia dla użytkowników, obsługuje przychodzące połączenia TLS/SSL (HTTPS - TCP/443), odszyfrowuje protokół TLS/SSL i przesyła niezaszyfrowane żądanie (HTTP - TCP/8080) do serwerów w puli zaplecza. Dzięki wbudowanej funkcji końowania protokołu TLS/SSL wystarczy zachować jeden certyfikat TLS/SSL w bramie aplikacji, co upraszcza operacje.

![Application Gateway równoważenia ruchu między serwerami internetowymi](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Aby skonfigurować Application Gateway dla serwera internetowego SAP BOBI, można znaleźć w blogu [Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) (Równoważenie obciążenia serwerów sieci Web SAP BOBI przy Azure Application Gateway sap).

> [!NOTE]
> Firma Microsoft zaleca używanie usługi Azure Application Gateway do równoważenia obciążenia ruchu do serwera internetowego, ponieważ zapewnia funkcje takie jak odciążanie protokołu SSL, scentralizowane zarządzanie protokołem SSL w celu zmniejszenia narzutu związanego z szyfrowaniem i odszyfrowywaniem na serwerze, algorytm Round-Robin do dystrybucji ruchu, funkcje Web Application Firewall (WAF), wysoka dostępność i tak dalej.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>Platforma SAP BusinessObjects BI — kopii zapasowej i przywracania

Tworzenie kopii zapasowych i przywracanie to proces tworzenia okresowych kopii danych i aplikacji w oddzielnej lokalizacji. W przypadku zgubionych lub uszkodzonych oryginalnych danych lub aplikacji można je przywrócić lub odzyskać do poprzedniego stanu. Jest to również podstawowy składnik każdej strategii odzyskiwania po awarii firmy.

Aby opracować kompleksową strategię tworzenia kopii zapasowych i przywracania dla platformy SAP BOBI, zidentyfikuj składniki, które prowadzą do przestojów lub zakłóceń w działaniu systemu w aplikacji. Na platformie SAP BOBI kopia zapasowa następujących składników jest niezbędna do ochrony aplikacji.

- Katalog instalacyjny SAP BOBI (zarządzane dyski w warstwie Premium)
- Serwer repozytorium plików (Azure NetApp Files lub Azure Premium Files)
- Baza danych programu CMS (Azure Database for MySQL lub baza danych na maszynie wirtualnej platformy Azure)

W poniższej sekcji opisano sposób implementacji strategii tworzenia kopii zapasowych i przywracania dla każdego składnika na platformie SAP BOBI.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Tworzenie kopii & kopii zapasowej dla katalogu instalacyjnego SAP BOBI

Na platformie Azure najprostszym sposobem na kopię zapasową serwerów aplikacji i wszystkich dołączonych dysków jest użycie [usługi Azure Backup](../../../backup/backup-overview.md) Service. Zapewnia ona niezależne i odizolowane kopie zapasowe, aby chronić niezamierzone zniszczenie danych na twoich maszyn wirtualnych. Kopie zapasowe są przechowywane w magazynie usługi Recovery Services z wbudowanymi funkcjami zarządzania punktami odzyskiwania. Konfiguracja i skalowanie są proste, kopie zapasowe są zoptymalizowane i można je łatwo przywrócić w razie potrzeby.

W ramach procesu tworzenia kopii zapasowej jest nosowana migawka, a dane są przesyłane do magazynu usługi Recovery Service bez wpływu na obciążenia produkcyjne. Migawka zapewnia inny poziom spójności, zgodnie z opisem w artykule [Snapshot Consistency (Spójność migawki).](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) Możesz również utworzyć kopię zapasową podzestawu dysków danych na maszynie wirtualnej przy użyciu funkcji tworzenia kopii zapasowej i przywracania dysków selektywnych. Aby uzyskać więcej informacji, zobacz [dokument Kopia zapasowa maszyny wirtualnej platformy Azure](../../../backup/backup-azure-vms-introduction.md) i Często zadawane pytania — tworzenie kopii zapasowych maszyn wirtualnych platformy [Azure.](../../../backup/backup-azure-vm-backup-faq.yml)

#### <a name="backup--restore-for-file-repository-server"></a>Przywracanie & kopii zapasowej dla serwera repozytorium plików

Na **Azure NetApp Files** można utworzyć migawki na żądanie i zaplanować automatyczne tworzenie migawek przy użyciu zasad migawek. Kopie migawki zapewniają kopię woluminu ANF do punktu w czasie. Aby uzyskać więcej informacji, zobacz [Zarządzanie migawkami przy użyciu Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

**Azure Files** kopii zapasowej jest zintegrowana z natywną [usługą Azure Backup,](../../../backup/backup-overview.md) która scentralizować funkcję tworzenia kopii zapasowych i przywracania oraz tworzenie kopii zapasowych maszyn wirtualnych i upraszcza pracę. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej udziału plików](../../../backup/azure-file-share-backup-overview.md) platformy Azure i często zadawane pytania — tworzenie kopii [zapasowej Azure Files.](../../../backup/backup-azure-files-faq.yml)

#### <a name="backup--restore-for-cms-database"></a>Tworzenie kopii & kopii zapasowej bazy danych programu CMS

Usługa Azure Database of MySQL to oferta DBaaS na platformie Azure, która automatycznie tworzy kopie zapasowe serwera i przechowuje je w magazynie lokalnie nadmiarowym lub geograficznie nadmiarowym skonfigurowanym przez użytkownika. Usługa Azure Database of MySQL pobiera kopie zapasowe plików danych i dziennika transakcji. W zależności od obsługiwanego maksymalnego rozmiaru magazynu tworzyć będą pełne i różnicowe kopie zapasowe (maksymalnie 4 TB serwerów magazynu) lub kopie zapasowe migawek (maksymalnie 16 TB maksymalnych serwerów magazynu). Te kopie zapasowe umożliwiają przywrócenie serwera w dowolnym momencie w skonfigurowanym okresie przechowywania kopii zapasowej. Domyślny okres przechowywania kopii zapasowej wynosi siedem dni. Opcjonalnie można go [skonfigurować do](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) trzech dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES.

Te pliki kopii zapasowej nie są udostępniane przez użytkownika i nie można ich wyeksportować. Tych kopii zapasowych można używać tylko w przypadku operacji przywracania w Azure Database for MySQL. Aby skopiować bazę danych, możesz użyć programu [mysqldump.](../../../mysql/concepts-migrate-dump-restore.md) Aby uzyskać więcej informacji, zobacz [Backup and restore in Azure Database for MySQL](../../../mysql/concepts-backup.md)(Tworzenie kopii zapasowych i przywracanie w Azure Database for MySQL ).

W przypadku bazy danych zainstalowanej Virtual Machines można użyć standardowych narzędzi do tworzenia kopii zapasowych [lub](../../../backup/sap-hana-db-about.md) Azure Backup bazy danych HANA. Ponadto jeśli usługi i narzędzia platformy Azure nie spełniają wymagań, możesz użyć innych narzędzi do tworzenia kopii zapasowych lub skryptu, aby utworzyć kopię zapasową dysków.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Niezawodność platformy SAP BusinessObjects BI

Platforma SAP BusinessObjects BI Platform zawiera różne warstwy, które są zoptymalizowane pod kątem określonych zadań i operacji. Gdy składnik z dowolnej warstwy stanie się niedostępny, aplikacja SAP BOBI stanie się niedostępna lub niektóre funkcje aplikacji nie będą działać. Dlatego należy upewnić się, że każda warstwa została zaprojektowana tak, aby zapewnić niezawodność, aby zapewnić działanie aplikacji bez żadnych zakłóceń w działalności biznesowej.

Ta sekcja koncentruje się na następujących opcjach dla platformy SAP BOBI —

- **Wysoka dostępność:** Wysoka dostępna platforma ma co najmniej dwa z wszystkich zasobów w regionie świadczenia usługi Azure, co pozwala zapewnić, że aplikacja będzie działać, jeśli jeden z serwerów stanie się niedostępny.
- **Odzyskiwanie po awarii:** Jest to proces przywracania funkcji aplikacji w przypadku utraty w razie katastrofalnej utraty, jak w przypadku niedostępności całego regionu świadczenia usługi Azure z powodu klęski żywiołowej.

Implementacja tego rozwiązania różni się w zależności od charakteru konfiguracji systemu na platformie Azure. Dlatego klient musi dostosować rozwiązanie wysokiej dostępności i odzyskiwania po awarii na podstawie swoich wymagań biznesowych.

### <a name="high-availability"></a>Wysoka dostępność

Wysoka dostępność odnosi się do zestawu technologii, które mogą zminimalizować przerwy w działaniu it, zapewniając ciągłość działania aplikacji/usług za pośrednictwem nadmiarowych składników, które są zabezpieczone przed błędami lub w trybie failover w tym samym centrum danych. W naszym przypadku centra danych znajdują się w jednym regionie świadczenia usługi Azure. Artykuł [High-availability Architecture and Scenarios for SAP](sap-high-availability-architecture-scenarios.md) (Architektura i scenariusze wysokiej dostępności dla systemu SAP) zawiera wstępne informacje na temat różnych technik wysokiej dostępności i rekomendacji oferowanych na platformie Azure dla aplikacji SAP, które uzupełnią instrukcje w tej sekcji.

Na podstawie wyniku ustalania rozmiaru platformy SAP BOBI należy zaprojektować krajobraz i określić dystrybucję składników usługi BI w różnych Virtual Machines Azure i podsieciach. Poziom nadmiarowości w architekturze rozproszonej zależy od wymaganego przez firmę celu czasu odzyskiwania (RTO) i celu punktu odzyskiwania (RPO, Recovery Point Objective). Platforma SAP BOBI platform zawiera różne warstwy i składniki w każdej warstwie powinny być zaprojektowane w celu zapewnienia nadmiarowości. Dlatego jeśli jeden składnik ulegnie awarii, nie będzie żadnych zakłóceń w działaniu aplikacji SAP BOBI. Na przykład

- Nadmiarowe serwery aplikacji, takie jak serwery aplikacji usługi BI i serwer internetowy
- Unikatowe składniki, takie jak baza danych cms, serwer repozytorium plików, Load Balancer

W poniższej sekcji opisano sposób osiągnięcia wysokiej dostępności dla każdego składnika platformy SAP BOBI.

#### <a name="high-availability-for-application-servers"></a>Wysoka dostępność dla serwerów aplikacji

W przypadku serwerów usługi BI i aplikacji internetowych, niezależnie od tego, czy są instalowane oddzielnie, czy razem, nie jest potrzebne konkretne rozwiązanie o wysokiej dostępności. Wysoką dostępność można osiągnąć przez nadmiarowość, czyli konfigurując wiele wystąpień usługi BI i serwerów internetowych w różnych usługach Azure Virtual Machines.

Aby zmniejszyć wpływ przestoju spowodowanego co najmniej jednym z zdarzeń, zaleca się stosowanie poniższych rozwiązań wysokiej dostępności dla serwerów aplikacji uruchomionych na wielu maszynach wirtualnych.

- Użyj Strefy dostępności, aby chronić awarie centrum danych.
- Skonfiguruj wiele Virtual Machines w zestawie dostępności w celu zapewnienia nadmiarowości.
- Użyj Dyski zarządzane dla maszyn wirtualnych w zestawie dostępności.
- Skonfiguruj każdą warstwę aplikacji w oddzielnych zestawach dostępności.

Aby uzyskać więcej informacji, zobacz [Manage the availability of Linux virtual machines (Zarządzanie dostępnością maszyn wirtualnych z systemem Linux)](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Wysoka dostępność bazy danych programu CMS

Jeśli używasz usługi Azure Database as a Service (DBaaS) dla bazy danych CMS, platforma wysokiej dostępności jest domyślnie dostarczana. Wystarczy wybrać region i usługę z możliwością wysokiej dostępności, nadmiarowości i odporności bez konieczności konfigurowania dodatkowych składników. Aby uzyskać więcej informacji na temat umowy SLA dotyczącej obsługiwanej oferty DBaaS na platformie Azure, zapoznaj się z tematem Wysoka dostępność w usłudze [Azure Database for MySQL](../../../mysql/concepts-high-availability.md) oraz Wysoka dostępność [dla Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

Inne wdrożenie systemu DBMS dla bazy danych CMS można znaleźć w przewodnikach wdrażania systemu [DBMS](dbms_guide_general.md)dla obciążenia SAP, które zapewniają szczegółowe informacje na temat różnych wdrożeń systemu DBMS i jego podejścia do osiągnięcia wysokiej dostępności.

#### <a name="high-availability-for-file-repository-server"></a>Wysoka dostępność serwera repozytorium plików

File Repository Server (FRS) odnosi się do katalogów dysków, w których jest przechowywana zawartość, na przykład raporty, uniwersum i połączenia. Jest on udostępniany na wszystkich serwerach aplikacji tego systemu. Dlatego należy upewnić się, że jest wysoce dostępna.

Na platformie Azure możesz wybrać [](../../../azure-netapp-files/azure-netapp-files-introduction.md) usługę [Azure Premium Files](../../../storage/files/storage-files-introduction.md) lub Azure NetApp Files dla udziału plików, który został zaprojektowany tak, aby zapewniał wysoką dostęp i trwałość. Aby uzyskać więcej informacji, zobacz [sekcję Nadmiarowość](../../../storage/files/storage-files-planning.md#redundancy) dla Azure Files.

> [!NOTE]
> Protokół SMB dla Azure Files jest ogólnie dostępny, ale obsługa protokołu NFS Azure Files jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [NFS 4.1 support for Azure Files is now in preview (Obsługa systemu plików NFS 4.1 w wersji zapoznawczej)](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Ponieważ ta usługa udziału plików nie jest dostępna we [](https://azure.microsoft.com/en-us/global-infrastructure/services/) wszystkich regionach, zapoznaj się z tematem Produkty dostępne według witryny regionów, aby uzyskać aktualne informacje. Jeśli usługa nie jest dostępna w Twoim regionie, możesz utworzyć serwer NFS, z którego można udostępnić system plików aplikacji SAP BOBI. Należy jednak również wziąć pod uwagę jej wysoką dostępność.

#### <a name="high-availability-for-load-balancer"></a>Wysoka dostępność dla usługi równoważenia obciążenia

Aby dystrybuować ruch między serwerami internetowymi, możesz użyć Azure Load Balancer lub Azure Application Gateway. Nadmiarowość dla jednego z usług równoważenia obciążenia można osiągnąć w oparciu oku SKU, który wybierzesz do wdrożenia.

- Na Azure Load Balancer nadmiarowość można osiągnąć, konfigurując usługa Load Balancer w warstwie Standardowa frontonie jako strefowo nadmiarowy. Aby uzyskać więcej informacji, [zobacz usługa Load Balancer w warstwie Standardowa i Strefy dostępności](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Na Application Gateway wysoką dostępność można osiągnąć na podstawie typu warstwy wybranej podczas wdrażania.
  - Wersja 1 SKU obsługuje scenariusze wysokiej dostępności po wdrożeniu co najmniej dwóch wystąpień. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby zapewnić, że wystąpienia nie wszystkie utkną w tym samym czasie. W związku z tym w ramach tej sku można osiągnąć nadmiarowość w strefie
  - Wersja 2 SKU automatycznie zapewnia, że nowe wystąpienia są rozłożone między domenami błędów i domenami aktualizacji. W przypadku wybrania nadmiarowości strefy najnowsze wystąpienia są również rozłożone w różnych strefach dostępności, aby zapewnić strefową odporność na awarie. Aby uzyskać więcej informacji, zobacz [Autoscaling and Zone-redundant Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md) (Skalowanie automatyczne i strefowo nadmiarowe Application Gateway wersji 2)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Odwołanie do architektury wysokiej dostępności dla platformy SAP BusinessObjects BI

Poniżej opisano architekturę referencyjną konfiguracji platformy SAP BOBI przy użyciu zestawu dostępności, który zapewnia nadmiarowość i dostępność maszyn wirtualnych w strefie. Architektura przedstawia użycie różnych usług platformy Azure, takich jak Azure Application Gateway, Azure NetApp Files i Azure Database for MySQL, dla platformy SAP BOBI Platform, która oferuje wbudowaną nadmiarowość, co zmniejsza złożoność zarządzania różnymi rozwiązaniami o wysokiej dostępności.

Na poniższej ilustracji ruch przychodzący (HTTPS - TCP/443) jest równoważyć obciążenie przy użyciu SKU programu Azure Application Gateway v1, która jest wysoce dostępna w przypadku wdrożenia w co najmniej dwóch wystąpieniach. Wiele wystąpień serwera internetowego, serwerów zarządzania i serwerów przetwarzania jest wdrażanych w osobnych Virtual Machines celu zapewnienia nadmiarowości, a każda warstwa jest wdrażana w oddzielnych zestawach dostępności. Azure NetApp Files ma wbudowaną nadmiarowość w centrum danych, więc woluminy ANF dla serwera repozytorium plików będą wysoce dostępne. Baza danych programu CMS jest aprowizowana Azure Database for MySQL (DBaaS), która ma naturalną wysoką dostępność. Aby uzyskać więcej informacji, zobacz [High availability in Azure Database for MySQL](../../../mysql/concepts-high-availability.md) guide (Wysoka dostępność w Azure Database for MySQL przewodniku).

![Nadmiarowość platformy SAP BusinessObjects BI przy użyciu zestawów dostępności](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Powyższy przykład architektury zapewnia wgląd w sposób, w jaki można wykonać wdrożenie SAP BOBI na platformie Azure. Nie obejmuje jednak wszystkich możliwych opcji konfiguracji platformy SAP BOBI Platform na platformie Azure. Klient może dostosować swoje wdrożenie do swoich wymagań biznesowych, wybierając różne produkty/usługi dla różnych składników, takich jak Load Balancer, serwer repozytorium plików i system DBMS.

W kilku regionach świadczenia Strefy dostępności platformy Azure, co oznacza, że ma niezależne źródło zasilania, chłodzenie i sieć. Umożliwia to klientowi wdrażanie aplikacji w dwóch lub trzech strefach dostępności. Klient, który chce uzyskać wysoką dostępność w strefach dostępności, może wdrożyć platformę SAP BOBI Platform w różnych strefach dostępności, upewniąc się, że każdy składnik w aplikacji jest strefowo nadmiarowy.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

W instrukcji w tej sekcji wyjaśniono strategię zapewnienia ochrony odzyskiwania po awarii dla platformy SAP BOBI. Uzupełnia on dokument [Odzyskiwanie po awarii dla oprogramowania SAP,](../../../site-recovery/site-recovery-sap.md) który reprezentuje podstawowe zasoby dla ogólnego podejścia do odzyskiwania po awarii sap.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referencyjna architektura odzyskiwania po awarii dla platformy SAP BusinessObjects BI

W tej architekturze referencyjnej jest uruchomione wdrożenie wielu wystąpień platformy SAP BOBI z nadmiarowych serwerów aplikacji. W przypadku odzyskiwania po awarii należy przesieć wszystkie warstwy do regionu pomocniczego w trybie fail over. Każda warstwa używa innej strategii w celu zapewnienia ochrony odzyskiwania po awarii.

![Odzyskiwanie po awarii platformy SAP BusinessObjects BI](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Moduł równoważenia obciążenia

Load Balancer służy do dystrybucji ruchu między serwerami aplikacji internetowych platformy SAP BOBI. Aby uzyskać dostęp do Azure Application Gateway, należy zaimplementować równoległą konfigurację bramy aplikacji w regionie pomocniczym.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Maszyny wirtualne z serwerami aplikacji internetowych i usługi BI

Azure Site Recovery może służyć do replikowania Virtual Machines serwerów aplikacji sieci Web i usługi BI w regionie pomocniczym. Serwery są replikowane w regionie pomocniczym, dzięki czemu w przypadku wystąpienia awarii i awarii można łatwo przejść w stan fail over w zreplikowanym środowisku i kontynuować pracę

#### <a name="file-repository-servers"></a>Serwery repozytorium plików

- **Azure NetApp Files** udostępnia woluminy NFS i SMB, dzięki czemu do replikowania danych między regionami platformy Azure można użyć dowolnego narzędzia kopiowania opartego na plikach. Aby uzyskać więcej informacji na temat kopiowania woluminu ANF w innym regionie, zobacz często zadawane pytania [dotyczące Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  Można użyć usługi Azure NetApp Files replikacji między regionami, która jest obecnie w wersji [zapoznawczej,](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) która korzysta z technologii NetApp SnapMirror®. Dlatego tylko zmienione bloki są wysyłane przez sieć w skompresowanym, wydajnym formacie. Ta zastrzeżona technologia minimalizuje ilość danych wymaganych do replikacji między regionami, co pozwala zmniejszyć koszty transferu danych. Skraca również czas replikacji, dzięki czemu można osiągnąć mniejszy cel punktu przywracania (RPO, Restore Point Objective). Aby uzyskać więcej informacji, zobacz [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami.](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)

- **Usługa Azure Premium Files** obsługuje tylko magazyn lokalnie nadmiarowy (LRS) i magazyn strefowo nadmiarowy (ZRS). W przypadku strategii drowania usługi Azure Premium Files możesz użyć narzędzia [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) [lub Azure PowerShell,](/powershell/module/az.storage/) aby skopiować pliki na inne konto magazynu w innym regionie. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii i tryb failover konta magazynu](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>Baza danych programu CMS

Azure Database for MySQL zapewnia wiele opcji odzyskiwania bazy danych w przypadku awarii. Wybierz odpowiednią opcję, która będzie odpowiednia dla Twojej firmy.

- Włącz repliki odczytu w różnych regionach, aby zwiększyć ciągłość działalności biznesowej i planowanie odzyskiwania po awarii. Można replikować z serwera źródłowego do maksymalnie pięciu replik. Repliki do odczytu są aktualizowane asynchronicznie przy użyciu technologii replikacji dzienników binarnych programu MySQL. Repliki to nowe serwery, które można zarządzać podobnie jak zwykłe Azure Database for MySQL serwerów. Dowiedz się więcej na temat replik do odczytu, dostępnych regionów, ograniczeń i sposobu pracy awaryjnej z artykułu [read replicas concepts (Pojęcia](../../../mysql/concepts-read-replicas.md)dotyczące replik do odczytu).

- Użyj Azure Database for MySQL funkcji przywracania geograficznego, która przywraca serwer przy użyciu geograficznie nadmiarowych kopii zapasowych. Te kopie zapasowe są dostępne nawet wtedy, gdy region, w którym jest hostowany serwer, jest w trybie offline. Możesz przywrócić z tych kopii zapasowych do dowolnego innego regionu i przywrócić serwer z powrotem do trybu online.

  > [!NOTE]
  > Przywracanie geograficzne jest możliwe tylko w przypadku aprowizacji serwera z geograficznie nadmiarowym magazynem kopii zapasowych. Zmiana opcji **nadmiarowości kopii zapasowej** po utworzeniu serwera nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [artykuł Nadmiarowość kopii](../../../mysql/concepts-backup.md#backup-redundancy-options) zapasowych.

Poniżej przedstawiono zalecenie dotyczące odzyskiwania po awarii dla każdej warstwy używanej w tym przykładzie.

| Warstwy platformy SAP BOBI   | Zalecenie                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Równoległa konfiguracja Application Gateway w regionie pomocniczym                                                |
| Serwery aplikacji internetowych   | Replikowanie przy użyciu Site Recovery                                                                         |
| Serwery aplikacji usługi BI    | Replikowanie przy użyciu Site Recovery                                                                         |
| Azure NetApp Files        | Narzędzie do kopiowania opartego na plikach do replikowania danych do regionu pomocniczego **lub** replikacji między regionami ANF (wersja zapoznawcza) |
| Azure Database for MySQL  | Repliki odczytu między regionami **lub przywracanie** kopii zapasowej z geograficznie nadmiarowych kopii zapasowych.                             |

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie odzyskiwania po awarii dla wielowarstwowego wdrożenia aplikacji SAP](../../../site-recovery/site-recovery-sap.md)
- [Planowanie Virtual Machines wdrożenia oprogramowania SAP na platformie Azure](planning-guide.md)
- [Wdrożenie usługi Azure Virtual Machines sap](deployment-guide.md)
- [Wdrażanie usługi Azure Virtual Machines DBMS dla oprogramowania SAP](./dbms_guide_general.md)
