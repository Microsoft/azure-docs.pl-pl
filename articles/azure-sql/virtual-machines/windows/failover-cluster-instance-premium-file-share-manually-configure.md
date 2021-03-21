---
title: Tworzenie FCI z udziałem plików w warstwie Premium
description: Użyj udziału plików w warstwie Premium (PFS), aby utworzyć wystąpienie klastra trybu failover (FCI) z SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 80fe9f03f2c57eab8527e553153f3e65315a54bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034850"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Tworzenie FCI z udziałem plików w warstwie Premium (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób tworzenia wystąpienia klastra trybu failover (FCI) z SQL Server na platformie Azure Virtual Machines (maszyny wirtualne) przy użyciu [udziału plików w warstwie Premium](../../../storage/files/storage-how-to-create-file-share.md).

Udziały plików w warstwie Premium to Bezpośrednie miejsca do magazynowania (SSD), spójnie spójne udziały plików o małym opóźnieniu, które są w pełni obsługiwane do użycia z wystąpieniami klastra trybu failover dla SQL Server 2012 lub nowszych w systemie Windows Server 2012 lub nowszym. Udziały plików w warstwie Premium zapewniają większą elastyczność, co pozwala na zmianę rozmiaru i skalowanie udziału plików bez przestojów.

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach klastra](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji przedstawionych w tym artykule należy posiadać następujące czynności:

- Subskrypcja platformy Azure.
- Konto, które ma uprawnienia do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w Active Directory.
- [Dwie lub więcej przygotowanych maszyn wirtualnych systemu Windows Azure](failover-cluster-instance-prepare-vm.md) w [zestawie dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) lub w różnych [strefach dostępności](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- [Udział plików w warstwie Premium](../../../storage/files/storage-how-to-create-file-share.md) , który ma być używany jako dysk klastrowany, na podstawie przydziału magazynu bazy danych dla plików danych.
- Najnowsza wersja programu [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Zainstaluj udział plików w warstwie Premium

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). i przejdź do konta magazynu.
1. Przejdź do obszaru **udziały plików** w obszarze **Usługa plików**, a następnie wybierz udział plików w warstwie Premium, który ma być używany dla magazynu SQL.
1. Wybierz pozycję **Połącz** , aby wyświetlić parametry połączenia dla udziału plików.
1. Z listy rozwijanej wybierz literę dysku, której chcesz użyć, a następnie skopiuj oba bloki kodu do Notatnika.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopiuj Oba polecenia programu PowerShell z portalu usługi File Share Connect":::

1. Użyj Remote Desktop Protocol (RDP), aby nawiązać połączenie z maszyną wirtualną SQL Server przy użyciu konta, które będzie używane przez SQL Server FCI dla konta usługi.
1. Otwórz konsolę administracyjną poleceń programu PowerShell.
1. Uruchom polecenia zapisane wcześniej podczas pracy z portalem.
1. Przejdź do udziału za pomocą Eksploratora plików lub okna dialogowego **Uruchamianie** (wybierz pozycję Windows + R). Użyj ścieżki sieciowej `\\storageaccountname.file.core.windows.net\filesharename` . Na przykład `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Utwórz co najmniej jeden folder w nowo połączonym udziale plików, w którym mają zostać umieszczone pliki danych SQL.
1. Powtórz te kroki na każdej maszynie SQL Server VM, która będzie uczestniczyć w klastrze.

  > [!IMPORTANT]
  > - Rozważ użycie oddzielnego udziału plików dla plików kopii zapasowej w celu zapisania operacji wejścia/wyjścia na sekundę (IOPS) i pojemności miejsca dla tego udziału dla plików danych i dziennika. W przypadku plików kopii zapasowej można użyć Premium lub standardowego udziału plików.
  > - Jeśli korzystasz z systemu Windows 2012 R2 lub starszego, wykonaj te same kroki, aby zainstalować udział plików, który ma być używany jako monitor udziału plików. 
  > 


## <a name="add-windows-cluster-feature"></a>Dodaj funkcję klastra systemu Windows

1. Nawiąż połączenie z pierwszą maszyną wirtualną za pomocą protokołu RDP przy użyciu konta domeny, które jest członkiem lokalnej grupy administratorów i ma uprawnienia do tworzenia obiektów w Active Directory. Użyj tego konta do pozostałej części konfiguracji.

1. [Dodaj klaster trybu failover do każdej maszyny wirtualnej](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować klaster trybu failover z interfejsu użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych:
   1. W **Menedżer serwera** wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **Dodaj role i funkcje**.
   1. W kreatorze **dodawania ról i funkcji** wybierz pozycję **dalej** , aż **wybierzesz pozycję funkcje**.
   1. W obszarze **Wybierz funkcje** wybierz pozycję **klaster trybu failover**. Dołącz wszystkie wymagane funkcje i narzędzia do zarządzania. 
   1. Wybierz pozycję **Dodaj funkcje**.
   1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Zakończ** , aby zainstalować funkcje.

   Aby zainstalować klaster trybu failover przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Weryfikuj klaster

Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera** wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
1. W obszarze **Menedżer klastra trybu failover** wybierz pozycję **Akcja**, a następnie wybierz pozycję **Weryfikuj konfigurację**.
1. Wybierz opcję **Dalej**.
1. W obszarze **Wybierz serwery lub klaster** wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **opcje testowania** wybierz opcję **Uruchom tylko wybrane testy**. 
1. Wybierz opcję **Dalej**.
1. W obszarze **wybór testu** zaznacz wszystkie testy z wyjątkiem **magazynu** i **bezpośrednie miejsca do magazynowania**, jak pokazano poniżej:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Wybierz testy weryfikacji klastra":::

1. Wybierz opcję **Dalej**.
1. W obszarze **potwierdzenie** wybierz pozycję **dalej**.

Kreator **weryfikacji konfiguracji** uruchamia testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.


## <a name="create-failover-cluster"></a>Utwórz klaster trybu failover

Aby utworzyć klaster trybu failover, potrzebne są:

- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover.
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci co węzły klastra.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 — 2016](#tab/windows2012)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2012 za pomocą systemu Windows Server 2016. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019.  Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Aby uzyskać więcej informacji, zobacz [klaster trybu failover: obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurowanie kworum

Skonfiguruj rozwiązanie kworum, które najlepiej odpowiada Twoim potrzebom biznesowym. Można skonfigurować [monitor dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), [monitor w chmurze](/windows-server/failover-clustering/deploy-cloud-witness)lub [monitor udostępniania plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Aby uzyskać więcej informacji, zobacz [kworum z maszynami wirtualnymi SQL Server](hadr-cluster-best-practices.md#quorum). 


## <a name="test-cluster-failover"></a>Testowanie trybu failover klastra

Przetestuj tryb failover klastra. W **Menedżer klastra trybu failover** kliknij prawym przyciskiem myszy klaster, wybierz pozycję **więcej akcji**  >  **Przenieś zasób klastra podstawowego**  >  **Wybierz węzeł**, a następnie wybierz inny węzeł klastra. Przenieś zasób podstawowego klastra do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli możesz pomyślnie przenieść klaster do każdego węzła, możesz zainstalować SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testowanie trybu failover klastra przez przeniesienie zasobu podstawowego do innych węzłów":::


## <a name="create-sql-server-fci"></a>Utwórz SQL Server FCI

Po skonfigurowaniu klastra trybu failover można utworzyć SQL Server FCI.

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover** upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby do tej maszyny wirtualnej.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w lokalizacji `C:\SQLServer_<version number>_Full` . 

1. Wybierz pozycję **Konfiguracja**.

1. W **centrum instalacji SQL Server** wybierz opcję **Instalacja**.

1. Wybierz pozycję **nowa SQL Server Instalacja klastra trybu failover**, a następnie postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w udziale plików w warstwie Premium. Wprowadź pełną ścieżkę udziału w tym formacie: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Zostanie wyświetlone ostrzeżenie z informacją o tym, że jako katalog danych został określony serwer plików. To ostrzeżenie jest oczekiwane. Upewnij się, że konto użytkownika używane do uzyskiwania dostępu do maszyny wirtualnej za pośrednictwem protokołu RDP podczas utrwalania udziału plików jest tym samym kontem, którego używa Usługa SQL Server, aby uniknąć możliwych błędów.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Korzystanie z udziału plików jako katalogów danych SQL":::

1. Po wykonaniu kroków opisanych w Kreatorze Instalator zainstaluje SQL Server FCI na pierwszym węźle.

1. Po zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji programu SQL Server**, a następnie wybierz pozycję **Instalacja**.

1. Wybierz pozycję **Dodaj węzeł do klastra trybu failover SQL Server**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server i dodać serwer do FCI.

   >[!NOTE]
   >Jeśli korzystasz z obrazu galerii portalu Azure Marketplace z SQL Server, narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z jednego z tych obrazów, zainstaluj SQL Server narzędzia osobno. Aby uzyskać więcej informacji, zobacz [pobieranie SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Powtórz te czynności na wszystkich węzłach, które chcesz dodać do wystąpienia klastra trybu failover SQL Server. 

## <a name="register-with-the-sql-vm-rp"></a>Rejestrowanie w ramach jednostki odzyskiwania maszyny wirtualnej SQL

Aby zarządzać maszyną wirtualną SQL Server z poziomu portalu, zarejestruj ją za pomocą rozszerzenia programu SQL IaaS Agent (RP) w [trybie zarządzania uproszczonego](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), obecnie jedynym trybem obsługiwanym z FCI i SQL Server na maszynach wirtualnych platformy Azure. 

Zarejestruj maszynę wirtualną SQL Server w trybie uproszczonym przy użyciu programu PowerShell (-LicenseType może być `PAYG` lub `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurowanie łączności 

Aby skierować ruch odpowiednio do bieżącego węzła podstawowego, należy skonfigurować opcję łączności, która jest odpowiednia dla danego środowiska. Można utworzyć [moduł równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub, jeśli używasz SQL Server 2019 zastosujesz pakietu CU2 (lub nowszego) i systemu Windows Server 2016 (lub nowszego), zamiast tego możesz użyć funkcji [rozproszonej nazwy sieciowej](failover-cluster-instance-distributed-network-name-dnn-configure.md) . 

Aby uzyskać więcej informacji na temat opcji łączności klastra, zobacz [Route HADR Cluster Connections to SQL Server na maszynach wirtualnych platformy Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Ograniczenia

- Usługa Microsoft Distributed Transaction Coordinator (MSDTC) nie jest obsługiwana w systemie Windows Server 2016 i jego starszych wersjach. 
- Element FILESTREAM nie jest obsługiwany w przypadku klastra trybu failover z udziałem plików w warstwie Premium. Aby użyć FILESTREAM, wdróż klaster przy użyciu [bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md) lub [udostępnionych dysków platformy Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) .
- Obsługiwane jest tylko rejestrowanie przy użyciu rozszerzenia agenta SQL IaaS w [trybie uproszczonego zarządzania](sql-server-iaas-agent-extension-automate-management.md#management-modes) . 

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj łączność z FCI przy użyciu [nazwy sieci wirtualnej i modułu równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub [nazwy sieci rozproszonej (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Jeśli udziały plików w warstwie Premium nie są odpowiednim rozwiązaniem magazynu FCI, rozważ utworzenie FCI przy użyciu [udostępnionych dysków platformy Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) lub [bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md) . 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach dotyczących konfiguracji klastra](hadr-cluster-best-practices.md). 

Aby uzyskać więcej informacji, zobacz: 
- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)