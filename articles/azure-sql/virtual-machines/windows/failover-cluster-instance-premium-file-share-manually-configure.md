---
title: Tworzenie pliku FCI z udziałem plików w wersji Premium
description: Użyj udziału plików w chmurze w chmurze (PFS, Premium File Share), aby utworzyć wystąpienie klastra trybu failover (FCI) z SQL Server maszynami wirtualnymi platformy Azure.
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
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482198"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Tworzenie pliku FCI z udziałem plików w witrynie Premium (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover (FCI) z usługą SQL Server na platformie Azure Virtual Machines (maszyn wirtualnych) przy użyciu udziału plików w [witrynie Premium.](../../../storage/files/storage-how-to-create-file-share.md)

Udziały plików w warstwie Premium Bezpośrednie miejsca do magazynowania (SSD) wspierane przez stale małe opóźnienia, które są w pełni obsługiwane do użytku z wystąpieniami klastra trybu failover dla systemu SQL Server 2012 lub nowszego w systemie Windows Server 2012 lub nowszym. Udziały plików Premium zapewniają większą elastyczność, umożliwiając zmianę rozmiaru i skalowanie udziału plików bez żadnych przestojów.

Aby dowiedzieć się więcej, zobacz omówienie usługi [FCI](failover-cluster-instance-overview.md) z SQL Server maszyn wirtualnych platformy Azure i [najlepszych rozwiązań w zakresie klastrów.](hadr-cluster-best-practices.md) 

## <a name="prerequisites"></a>Wymagania wstępne

Przed ukończeniem instrukcji w tym artykule musisz mieć:

- Subskrypcja platformy Azure.
- Konto z uprawnieniami do tworzenia obiektów zarówno na maszynach wirtualnych platformy Azure, jak i w usłudze Active Directory.
- [Co najmniej dwie przygotowane maszyny wirtualne platformy Azure z](failover-cluster-instance-prepare-vm.md) systemem Windows w zestawie dostępności [lub](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) w [różnych strefach dostępności.](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)
- Udział [plików w chmurze w](../../../storage/files/storage-how-to-create-file-share.md) chmurze, który będzie używany jako dysk klastrowany, na podstawie limitu przydziału magazynu bazy danych dla plików danych.
- Najnowsza wersja programu [PowerShell.](/powershell/azure/install-az-ps) 

## <a name="mount-premium-file-share"></a>Zainstaluj udział plików w premium

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). i przejdź do konta magazynu.
1. Przejdź do **opcji Udziały plików** w **obszarze Usługa plików,** a następnie wybierz udział plików Premium, którego chcesz użyć dla magazynu SQL.
1. Wybierz **pozycję** Połącz, aby uzyskać parametrów połączenia dla udziału plików.
1. Z listy rozwijanej wybierz literę dysku, której chcesz użyć, a następnie skopiuj oba bloki kodu do Notatnika.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Skopiuj oba polecenia programu PowerShell z portalu programu File Share Connect":::

1. Użyj Remote Desktop Protocol (RDP), aby nawiązać połączenie z maszyną wirtualną usługi SQL Server przy użyciu konta, które będzie SQL Server FCI dla konta usługi.
1. Otwórz administracyjną konsolę poleceń programu PowerShell.
1. Uruchom polecenia zapisane wcześniej podczas pracy w portalu.
1. Przejdź do udziału przy użyciu opcji Eksplorator plików lub okna **dialogowego** Uruchamianie (wybierz pozycję Windows + R). Użyj ścieżki sieciowej `\\storageaccountname.file.core.windows.net\filesharename` . Na przykład `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Utwórz co najmniej jeden folder w nowo połączonym udziału plików, aby umieścić w nich pliki danych SQL.
1. Powtórz te kroki na każdej SQL Server wirtualnej, która będzie uczestniczyć w klastrze.

  > [!IMPORTANT]
  > - Rozważ użycie oddzielnego udziału plików dla plików kopii zapasowej w celu zapisania operacji we/wy na sekundę (IOPS) i pojemności tego udziału na dane i pliki dziennika. W przypadku plików kopii zapasowych można użyć udziału plików w chmurze Premium lub Standardowa.
  > - Jeśli korzystasz z systemu Windows 2012 R2 lub starszego, wykonaj te same kroki, aby zainstalować udział plików, który będzie używać jako witness udziału plików. 
  > 


## <a name="add-windows-cluster-feature"></a>Dodawanie funkcji klastra systemu Windows

1. Połącz się z pierwszą maszyną wirtualną za pomocą protokołu RDP przy użyciu konta domeny, które jest członkiem administratorów lokalnych i ma uprawnienia do tworzenia obiektów w usłudze Active Directory. Użyj tego konta w pozostałej części konfiguracji.

1. [Dodaj klaster trybu failover do każdej maszyny wirtualnej.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

   Aby zainstalować klaster trybu failover z interfejsu użytkownika, wykonaj następujące czynności na obu maszynach wirtualnych:
   1. W **Menedżer serwera** wybierz pozycję **Zarządzaj,** a następnie wybierz **pozycję Dodaj role i funkcje.**
   1. W **kreatorze Dodaj role i funkcje** wybieraj przycisk **Dalej,** aż do momentu wybrania **opcji Wybierz funkcje.**
   1. W **chmurze Wybierz funkcje** wybierz pozycję **Klaster trybu failover.** Uwzględnij wszystkie wymagane funkcje i narzędzia do zarządzania. 
   1. Wybierz **pozycję Dodaj funkcje.**
   1. Wybierz **pozycję Dalej,** a następnie wybierz **pozycję Zakończ,** aby zainstalować funkcje.

   Aby zainstalować klaster trybu failover przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Weryfikowanie klastra

Zweryfikuj klaster w interfejsie użytkownika lub przy użyciu programu PowerShell.

Aby zweryfikować klaster przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W **Menedżer serwera** wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
1. W **Menedżer klastra trybu failover** wybierz pozycję **Akcja**, a następnie wybierz pozycję **Weryfikuj konfigurację.**
1. Wybierz opcję **Dalej**.
1. W **obszarze Wybierz serwery lub klaster** wprowadź nazwy obu maszyn wirtualnych.
1. W **obszarze Opcje testowania** wybierz **pozycję Uruchom tylko testy I select**. 
1. Wybierz opcję **Dalej**.
1. W **obszarze Test Selection (Wybór** testu) wybierz wszystkie testy z wyjątkiem storage i **Bezpośrednie miejsca do magazynowania**, jak pokazano poniżej: 

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Wybieranie testów weryfikacji klastra":::

1. Wybierz opcję **Dalej**.
1. W **obszarze Potwierdzenie** wybierz pozycję **Dalej.**

Kreator **weryfikacji konfiguracji** uruchamia testy weryfikacyjne.

Aby zweryfikować klaster przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po zweryfikowaniu klastra utwórz klaster trybu failover.


## <a name="create-failover-cluster"></a>Tworzenie klastra trybu failover

Aby utworzyć klaster trybu failover, potrzebne są:

- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover.
- Adres IP klastra trybu failover. Możesz użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej i podsieci platformy Azure co węzły klastra.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012–2016](#tab/windows2012)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2012 do wersji Windows Server 2016. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019.  Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Aby uzyskać więcej informacji, zobacz [Klaster trybu failover: obiekt sieciowy klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurowanie kworum

Skonfiguruj rozwiązanie kworum, które najlepiej odpowiada Twoim potrzebom biznesowym. Możesz skonfigurować narzędzie [Disk Witness,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)a [Cloud Witness](/windows-server/failover-clustering/deploy-cloud-witness)lub File [Share Witness.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) Aby uzyskać więcej informacji, zobacz [Quorum with SQL Server VMs (Kworum SQL Server maszyn wirtualnych).](hadr-cluster-best-practices.md#quorum) 


## <a name="test-cluster-failover"></a>Testowanie trybu failover klastra

Przetestuj tryb failover klastra. W **Menedżer klastra trybu failover** kliknij prawym przyciskiem myszy klaster, wybierz pozycję Więcej akcji Przenieś zasób klastra podstawowego Wybierz węzeł , a następnie wybierz drugi węzeł   >    >  klastra. Przenieś zasób klastra podstawowego do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli możesz pomyślnie przenieść klaster do każdego węzła, możesz zainstalować SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Przetestuj tryb failover klastra, przenosząc zasób podstawowy do innych węzłów":::


## <a name="create-sql-server-fci"></a>Tworzenie SQL Server FCI

Po skonfigurowaniu klastra trybu failover można utworzyć klaster trybu failover SQL Server FCI.

1. Połącz się z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover** upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby przenieś wszystkie zasoby na tę maszynę wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów Azure Marketplace, nośnik znajduje się `C:\SQLServer_<version number>_Full` w . 

1. Wybierz **pozycję Setup (Skonfiguruj).**

1. W centrum **SQL Server wybierz** pozycję **Instalacja.**

1. Wybierz pozycję SQL Server instalacji klastra trybu **failover,** a następnie postępuj zgodnie z instrukcjami w kreatorze, aby zainstalować SQL Server klastra trybu failover.

   Katalogi danych fci muszą być w udziałach plików w premium. Wprowadź pełną ścieżkę udziału w formacie `\\storageaccountname.file.core.windows.net\filesharename\foldername` : . Zostanie wyświetlone ostrzeżenie informujące o tym, że określono serwer plików jako katalog danych. To ostrzeżenie jest oczekiwane. Upewnij się, że konto użytkownika użyte do uzyskania dostępu do maszyny wirtualnej za pośrednictwem protokołu RDP podczas utrwalania udziału plików jest tym samym kontem, które jest używane przez usługę SQL Server, aby uniknąć możliwych błędów.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Używanie udziału plików jako katalogów danych SQL":::

1. Po zakończeniu kroków kreatora Instalator zainstaluje w pierwszym węźle SQL Server fci.

1. Po zainstalowaniu przez Instalatora klastra klastra w pierwszym węźle połącz się z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz centrum **SQL Server,** a następnie wybierz pozycję **Instalacja.**

1. Wybierz **pozycję Dodaj węzeł do SQL Server trybu failover.** Postępuj zgodnie z instrukcjami w kreatorze, aby SQL Server i dodać serwer do interfejsu FCI.

   >[!NOTE]
   >Jeśli używasz obrazu Azure Marketplace z galerii z SQL Server, SQL Server zostały dołączone do obrazu. Jeśli nie używasz jednego z tych obrazów, zainstaluj SQL Server oddzielnie. Aby uzyskać więcej informacji, zobacz [Download SQL Server Management Studio (SSMS) (Pobieranie za SQL Server Management Studio SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)

1. Powtórz te kroki w innych węzłach, które chcesz dodać do SQL Server trybu failover. 

## <a name="register-with-the-sql-vm-rp"></a>Rejestrowanie przy użyciu jednostki żądania maszyny wirtualnej SQL

Aby zarządzać maszyną wirtualną usługi SQL Server z portalu, zarejestruj ją przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonego zarządzania [,](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)obecnie jedynym trybem obsługiwanym przez wystąpienia fci i SQL Server na maszynach wirtualnych platformy Azure. 

Rejestrowanie maszyny SQL Server wirtualnej w trybie lekkim przy użyciu programu PowerShell (-LicenseType może być `PAYG` lub `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurowanie łączności 

Aby odpowiednio rozsyłać ruch do bieżącego węzła podstawowego, skonfiguruj opcję łączności odpowiednią dla Twojego środowiska. Możesz utworzyć usługę [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub, jeśli używasz programu SQL Server 2019 CU2 (lub nowszego) i systemu Windows [](failover-cluster-instance-distributed-network-name-dnn-configure.md) Server 2016 (lub nowszego), możesz zamiast tego użyć funkcji rozproszonej nazwy sieci. 

Aby uzyskać więcej informacji na temat opcji łączności klastra, zobacz [Route HADR connections to SQL Server on Azure VMs (Route HADR connections to SQL Server on Azure VMs (Route HADR connections to SQL Server on Azure VMs (Route HADR connections to SQL Server on Azure VMs](hadr-cluster-best-practices.md#connectivity)(Przekier 

## <a name="limitations"></a>Ograniczenia

- Microsoft Distributed Transaction Coordinator (MSDTC) nie jest obsługiwana w systemie Windows Server 2016 i starszych. 
- Usługa Filestream nie jest obsługiwana w przypadku klastra trybu failover z udziałem plików w chmurze Premium. Aby użyć strumienia plików, należy wdrożyć klaster przy użyciu Bezpośrednie miejsca do magazynowania [lub](failover-cluster-instance-storage-spaces-direct-manually-configure.md) dysków udostępnionych [platformy Azure.](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Obsługiwane jest tylko rejestrowanie za pomocą rozszerzenia agenta SQL IaaS w [trybie uproszczonego](sql-server-iaas-agent-extension-automate-management.md#management-modes) zarządzania. 
- Migawki bazy danych nie są obecnie obsługiwane w programie Azure Files z powodu [ograniczeń plików rozrzednych.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)  

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, skonfiguruj łączność z usługą FCI przy użyciu nazwy sieci wirtualnej i usługi [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub rozproszonej nazwy sieci [(DNN).](failover-cluster-instance-distributed-network-name-dnn-configure.md) 


Jeśli udziały plików w warstwie Premium nie są dla Ciebie odpowiednim rozwiązaniem magazynu FCI, rozważ utworzenie sieci FCI przy użyciu dysków udostępnionych platformy [Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) [lub](failover-cluster-instance-storage-spaces-direct-manually-configure.md) Bezpośrednie miejsca do magazynowania zamiast tego. 

Aby dowiedzieć się więcej, zobacz omówienie usługi [FCI](failover-cluster-instance-overview.md) z SQL Server maszyn wirtualnych platformy Azure i najlepszych rozwiązań w zakresie [konfiguracji klastra.](hadr-cluster-best-practices.md) 

Aby uzyskać więcej informacji, zobacz: 
- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpień klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
