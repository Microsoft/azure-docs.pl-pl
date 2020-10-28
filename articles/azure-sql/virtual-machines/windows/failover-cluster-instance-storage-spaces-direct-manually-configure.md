---
title: Tworzenie FCI przy użyciu Bezpośrednie miejsca do magazynowania
description: Użyj Bezpośrednie miejsca do magazynowania, aby utworzyć wystąpienie klastra trybu failover (FCI) z SQL Server na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 848f3cd2d5719d62e39f46c166d51e09ec89bd4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792518"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Tworzenie FCI przy użyciu Bezpośrednie miejsca do magazynowania (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób tworzenia wystąpienia klastra trybu failover (FCI) przy użyciu [bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) z SQL Server na platformie Azure Virtual Machines. Bezpośrednie miejsca do magazynowania działa jako oparta na oprogramowaniu wirtualna sieć San (sieci vSAN), która synchronizuje magazyn (dyski danych) między węzłami (maszynami wirtualnymi platformy Azure) w klastrze systemu Windows. 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach klastra](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Omówienie 

[Bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) obsługuje dwa typy architektur: zbieżne i bardziej zbieżne. Infrastruktura ze zbyt zbieżnością umieszcza magazyn na tych samych serwerach, na których jest hostowana aplikacja klastrowana, tak że magazyn znajduje się na każdym SQL Server węźle FCI. 

Na poniższym diagramie przedstawiono kompletne rozwiązanie, w którym są wykorzystywane Bezpośrednie miejsca do magazynowania i SQL Server na maszynach wirtualnych platformy Azure: 

![Diagram kompletnego rozwiązania przy użyciu Bezpośrednie miejsca do magazynowania](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Na powyższym diagramie przedstawiono następujące zasoby w tej samej grupie zasobów:

- Dwie maszyny wirtualne w klastrze trybu failover systemu Windows Server. Jeśli maszyna wirtualna znajduje się w klastrze trybu failover, jest również nazywana *węzłem klastra* lub *węzłem* .
- Każda maszyna wirtualna ma co najmniej dwa dyski danych.
- Bezpośrednie miejsca do magazynowania synchronizuje dane na dyskach danych i przedstawia zsynchronizowany magazyn jako pulę magazynów.
- Pula magazynów prezentuje udostępniony wolumin klastra (CSV) do klastra trybu failover.
- Rola klastra usługi SQL Server FCI używa woluminu CSV dla dysków danych.
- Moduł równoważenia obciążenia platformy Azure do przechowywania adresu IP SQL Server FCI.
- Zestaw dostępności platformy Azure zawiera wszystkie zasoby.

   > [!NOTE]
   > To całe rozwiązanie można utworzyć na platformie Azure na podstawie szablonu. Przykładowy szablon jest dostępny na stronie [Szablony szybkiego startu platformy Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) . Ten przykład nie jest zaprojektowany ani testowany pod kątem określonego obciążenia. Można uruchomić szablon w celu utworzenia SQL Server FCI z magazynem Bezpośrednie miejsca do magazynowania podłączonym do domeny. Możesz oszacować szablon i zmodyfikować go do swoich celów.


## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji przedstawionych w tym artykule należy posiadać następujące czynności:

- Subskrypcja platformy Azure. Zacznij korzystać [bezpłatnie](https://azure.microsoft.com/free/). 
- [Co najmniej dwie przygotowane maszyny wirtualne Windows Azure](failover-cluster-instance-prepare-vm.md) w [zestawie dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Konto, które ma uprawnienia do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w Active Directory.
- Najnowsza wersja programu [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>Dodawanie funkcji klastra systemu Windows

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu Remote Desktop Protocol (RDP) z kontem domeny, które jest członkiem lokalnej grupy administratorów i ma uprawnienia do tworzenia obiektów w Active Directory. Użyj tego konta do pozostałej części konfiguracji.

1. Dodaj klaster trybu failover do każdej maszyny wirtualnej.

   Aby zainstalować klaster trybu failover z interfejsu użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych:

   1. W **Menedżer serwera** wybierz pozycję **Zarządzaj** , a następnie wybierz pozycję **Dodaj role i funkcje** .
   1. W kreatorze **dodawania ról i funkcji** wybierz pozycję **dalej** , aż **wybierzesz pozycję funkcje** .
   1. W obszarze **Wybierz funkcje** wybierz pozycję **klaster trybu failover** . Dołącz wszystkie wymagane funkcje i narzędzia do zarządzania. 
   1. Wybierz pozycję **Dodaj funkcje** .
   1. Wybierz pozycję **dalej** , a następnie wybierz pozycję **Zakończ** , aby zainstalować funkcje.

   Aby zainstalować klaster trybu failover przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Aby uzyskać więcej informacji o następnych krokach, zapoznaj się z instrukcjami w sekcji "krok 3: Konfigurowanie Bezpośrednie miejsca do magazynowania" [rozwiązania moje zbieżne rozwiązanie przy użyciu bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Weryfikowanie klastra

Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera** wybierz pozycję **Narzędzia** , a następnie wybierz pozycję **Menedżer klastra trybu failover** .
1. W obszarze **Menedżer klastra trybu failover** wybierz pozycję **Akcja** , a następnie wybierz pozycję **Weryfikuj konfigurację** .
1. Wybierz pozycję **Dalej** .
1. W obszarze **Wybierz serwery lub klaster** wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **opcje testowania** wybierz opcję **Uruchom tylko wybrane testy** . 
1. Wybierz pozycję **Dalej** .
1. W obszarze **wybór testu** zaznacz wszystkie testy z wyjątkiem **magazynu** , jak pokazano poniżej:

   ![Wybierz testy weryfikacji klastra](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Wybierz pozycję **Dalej** .
1. W obszarze **potwierdzenie** wybierz pozycję **dalej** .

    Kreator **weryfikacji konfiguracji** uruchamia testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
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

## <a name="add-storage"></a>Dodawanie magazynu

Dyski dla Bezpośrednie miejsca do magazynowania muszą być puste. Nie mogą zawierać partycji ani innych danych. Aby wyczyścić dyski, postępuj zgodnie z instrukcjami podanymi w temacie [wdrażanie bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Włącz bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Poniższy skrypt programu PowerShell umożliwia Bezpośrednie miejsca do magazynowania:  

   ```powershell
   Enable-ClusterS2D
   ```

   W **Menedżer klastra trybu failover** można teraz zobaczyć pulę magazynów.

1. [Utwórz wolumin](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Bezpośrednie miejsca do magazynowania automatycznie tworzy pulę magazynów po jej włączeniu. Teraz można przystąpić do tworzenia woluminu. Polecenie cmdlet programu PowerShell `New-Volume` automatyzuje proces tworzenia woluminu. Ten proces obejmuje formatowanie, dodanie woluminu do klastra i utworzenie pliku CSV. W tym przykładzie tworzony jest wolumin CSV 800 gigabajta (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po uruchomieniu poprzedniego polecenia wolumin 800-GB jest instalowany jako zasób klastra. Wolumin jest pod adresem `C:\ClusterStorage\Volume1\` .

   Ten zrzut ekranu przedstawia wolumin CSV z Bezpośrednie miejsca do magazynowania:

   ![Zrzut ekranu przedstawiający udostępniony wolumin klastra z Bezpośrednie miejsca do magazynowania](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testowanie trybu failover klastra

Przetestuj tryb failover klastra. W **Menedżer klastra trybu failover** kliknij prawym przyciskiem myszy klaster, wybierz pozycję **więcej akcji**  >  **Przenieś zasób klastra podstawowego**  >  **Wybierz węzeł** , a następnie wybierz inny węzeł klastra. Przenieś zasób podstawowego klastra do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli możesz pomyślnie przenieść klaster do każdego węzła, możesz zainstalować SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testowanie trybu failover klastra przez przeniesienie zasobu podstawowego do innych węzłów":::

## <a name="create-sql-server-fci"></a>Utwórz SQL Server FCI

Po skonfigurowaniu klastra trybu failover i wszystkich składników klastra, w tym magazynu, można utworzyć SQL Server FCI.

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover** upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby na tę maszynę wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w lokalizacji `C:\SQLServer_<version number>_Full` . Wybierz pozycję **Konfiguracja** .

1. W **SQL Server centrum instalacji** wybierz opcję **Instalacja** .

1. Wybierz pozycję **nowa SQL Server Instalacja klastra trybu failover** . Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w magazynie klastrowanym. W przypadku Bezpośrednie miejsca do magazynowania nie jest to dysk udostępniony, ale punkt instalacji do woluminu na każdym serwerze. Bezpośrednie miejsca do magazynowania synchronizuje wolumin między obydwoma węzłami. Wolumin jest prezentowany w klastrze jako plik CSV. Użyj punktu instalacji woluminu CSV dla katalogów danych.

   ![Katalogi danych](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Po wykonaniu instrukcji w Kreatorze Instalator instaluje SQL Server FCI na pierwszym węźle.

1. Po zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji SQL Server** . Wybierz opcję **Instalacja** .

1. Wybierz pozycję **Dodaj węzeł do klastra trybu failover SQL Server** . Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server i dodać serwer do FCI.

   >[!NOTE]
   >W przypadku korzystania z obrazu galerii portalu Azure Marketplace zawierającego SQL Server narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z jednego z tych obrazów, zainstaluj SQL Server narzędzia osobno. Aby uzyskać więcej informacji, zobacz [pobieranie SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Rejestrowanie w ramach jednostki odzyskiwania maszyny wirtualnej SQL

Aby zarządzać maszyną wirtualną SQL Server z poziomu portalu, zarejestruj ją z dostawcą zasobów maszyny wirtualnej SQL (RP) w [trybie zarządzania uproszczonego](sql-vm-resource-provider-register.md#lightweight-management-mode), obecnie jedynym trybem obsługiwanym z FCI i SQL Server na maszynach wirtualnych platformy Azure. 


Rejestrowanie maszyny wirtualnej SQL Server w trybie uproszczonym przy użyciu programu PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurowanie łączności 

Aby skierować ruch odpowiednio do bieżącego węzła podstawowego, należy skonfigurować opcję łączności, która jest odpowiednia dla danego środowiska. Można utworzyć [moduł równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub, jeśli używasz SQL Server 2019 zastosujesz pakietu CU2 (lub nowszego) i systemu Windows Server 2016 (lub nowszego), zamiast tego możesz użyć funkcji [rozproszonej nazwy sieciowej](failover-cluster-instance-distributed-network-name-dnn-configure.md) . 

## <a name="limitations"></a>Ograniczenia

- Usługa Azure Virtual Machines obsługuje program Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem na CSV i w ramach [standardowego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md).
- Dyski dołączone jako dyski sformatowane w systemie plików NTFS mogą być używane z Bezpośrednie miejsca do magazynowania tylko wtedy, gdy opcja kwalifikowania dysku nie jest zaznaczona lub wyczyszczona, gdy do klastra jest dodawany magazyn. 
- Obsługiwane jest tylko rejestrowanie w [trybie uproszczonego zarządzania](sql-vm-resource-provider-register.md#management-modes) przy użyciu dostawcy zasobów maszyny wirtualnej SQL.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj łączność z FCI przy użyciu [nazwy sieci wirtualnej i modułu równoważenia obciążenia platformy Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub [nazwy sieci rozproszonej (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Jeśli Bezpośrednie miejsca do magazynowania nie jest odpowiednim rozwiązaniem magazynu FCI, rozważ utworzenie FCI przy użyciu [udostępnionych dysków Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) lub [Premium udziałów plików](failover-cluster-instance-premium-file-share-manually-configure.md) . 

Aby dowiedzieć się więcej, zobacz Omówienie [FCI z SQL Server na maszynach wirtualnych platformy Azure](failover-cluster-instance-overview.md) i [najlepszych rozwiązaniach dotyczących konfiguracji klastra](hadr-cluster-best-practices.md). 

Aby uzyskać więcej informacji, zobacz: 
- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)