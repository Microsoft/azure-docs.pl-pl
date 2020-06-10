---
title: SQL Server FCI na platformie Azure Virtual Machines
description: W tym artykule opisano sposób tworzenia SQL Server wystąpienia klastra trybu failover (FCI) w usłudze Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 55ad535c965ae910b26900c2c555e21378ba49d9
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656746"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurowanie SQL Server wystąpienia klastra trybu failover na maszynach wirtualnych platformy Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób tworzenia SQL Server wystąpienia klastra trybu failover (FCI) na platformie Azure Virtual Machines w modelu Azure Resource Manager. To rozwiązanie używa [systemu Windows Server 2016 Datacenter edition bezpośrednie miejsca do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako wirtualnej sieci San opartej na oprogramowaniu, która synchronizuje magazyn (dyski danych) między węzłami (maszyn wirtualnych platformy Azure) w klastrze systemu Windows. Bezpośrednie miejsca do magazynowania Nowość w systemie Windows Server 2016.

Na poniższym diagramie przedstawiono kompletne rozwiązanie na platformie Azure Virtual Machines:

![Kompletne rozwiązanie](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Ten diagram przedstawia:

- Dwie maszyny wirtualne w klastrze trybu failover systemu Windows Server. Jeśli maszyna wirtualna znajduje się w klastrze trybu failover, jest również nazywana *węzłem klastra* lub *węzłem*.
- Każda maszyna wirtualna ma co najmniej dwa dyski danych.
- Bezpośrednie miejsca do magazynowania synchronizuje dane na dyskach danych i przedstawia zsynchronizowany magazyn jako pulę magazynów.
- Pula magazynów prezentuje udostępniony wolumin klastra (CSV) do klastra trybu failover.
- Rola klastra usługi SQL Server FCI używa woluminu CSV dla dysków danych.
- Moduł równoważenia obciążenia platformy Azure do przechowywania adresu IP SQL Server FCI.
- Zestaw dostępności platformy Azure zawiera wszystkie zasoby.

>[!NOTE]
>Wszystkie zasoby platformy Azure na diagramie znajdują się w tej samej grupie zasobów.

Aby uzyskać szczegółowe informacje na temat Bezpośrednie miejsca do magazynowania, zobacz [Windows Server 2016 Datacenter edition bezpośrednie miejsca do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Bezpośrednie miejsca do magazynowania obsługuje dwa typy architektur: zbieżne i z funkcją Hyper-zbieżność. Architektura w tym dokumencie jest technologią Hyper-zbieżną. Infrastruktura z technologią Hyper-zbieżną umieszcza magazyn na tych samych serwerach, które obsługują klastrowaną aplikację. W tej architekturze magazyn znajduje się na każdym SQL Server węźle FCI.

## <a name="licensing-and-pricing"></a>Licencjonowanie i Cennik

Na platformie Azure Virtual Machines można licencjonować SQL Server przy użyciu obrazów maszyn wirtualnych "płatność zgodnie z rzeczywistym użyciem" lub "Przenieś własną licencję" (BYOL). Wybrany typ obrazu ma wpływ na sposób naliczania opłat.

Dzięki licencjonowaniu z płatnością zgodnie z rzeczywistym użyciem wystąpienie klastra trybu failover (FCI) SQL Server na platformie Azure Virtual Machines powoduje naliczanie opłat za wszystkie węzły FCI, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [SQL Server Enterprise Virtual Machines cennika](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Jeśli masz Umowa Enterprise z programem Software Assurance, możesz użyć jednego wolnego węzła pasywnej FCI dla każdego aktywnego węzła. Aby skorzystać z tej korzyści na platformie Azure, użyj obrazów maszyn wirtualnych BYOL i Użyj tej samej licencji na węzłach aktywnych i pasywnych FCI. Aby uzyskać więcej informacji, zobacz [Umowa Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Aby porównać usługę płatność zgodnie z rzeczywistym użyciem i BYOL Licencjonowanie na potrzeby SQL Server w usłudze Azure Virtual Machines, zobacz [Rozpoczynanie pracy z maszynami wirtualnymi SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

Aby uzyskać pełne informacje na temat licencjonowania SQL Server, zobacz [Cennik](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Przykładowy szablon platformy Azure

To całe rozwiązanie można utworzyć na platformie Azure na podstawie szablonu. Przykładowy szablon jest dostępny w [szablonach szybkiego startu platformy Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Ten przykład nie jest zaprojektowany ani testowany pod kątem określonego obciążenia. Można uruchomić szablon w celu utworzenia SQL Server FCI z magazynem Bezpośrednie miejsca do magazynowania podłączonym do domeny. Możesz oszacować szablon i zmodyfikować go do swoich celów.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem pracy należy poznać kilka rzeczy.

### <a name="what-to-know"></a>Co należy wiedzieć

Należy mieć praktyczne zrozumienie tych technologii:

- [Technologie klastrów systemu Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server wystąpienia klastra trybu failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Należy pamiętać, że w przypadku klastra trybu failover gościa maszyny wirtualnej Azure IaaS zaleca się użycie jednej karty sieciowej na serwer (węzeł klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej Azure IaaS. Raport weryfikacji klastra ostrzega o tym, że węzły są dostępne tylko w jednej sieci. To ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej usługi Azure IaaS.

Należy również uzyskać ogólne informacje na temat tych technologii:

- [Rozwiązania z funkcją Hyper-zbieżne używające Bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupa zasobów platformy Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> W tej chwili SQL Server wystąpienia klastra trybu failover na platformie Azure Virtual Machines są obsługiwane tylko w SQL Server [trybie uproszczonego zarządzania](sql-vm-resource-provider-register.md#management-modes) [rozszerzenia IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Aby zmienić tryb pełnego rozszerzenia na lekki, Usuń zasób **maszyny wirtualnej SQL** dla odpowiednich maszyn wirtualnych, a następnie zarejestruj je u dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym. Podczas usuwania zasobu **maszyny wirtualnej SQL** przy użyciu Azure Portal **Wyczyść pole wyboru obok odpowiedniej maszyny wirtualnej**. Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, stosowanie poprawek i zaawansowane zarządzanie portalem. Te funkcje nie będą działały w przypadku maszyn wirtualnych SQL po ponownym zainstalowaniu agenta w trybie uproszczonego zarządzania.
> 

### <a name="what-to-have"></a>Co należy zrobić

Przed wykonaniem kroków opisanych w tym artykule należy dysponować:

- Subskrypcja Microsoft Azure
- Domena systemu Windows w usłudze Azure Virtual Machines
- Konto, które ma uprawnienia do tworzenia obiektów na obu maszynach wirtualnych i w Active Directory
- Sieć wirtualna platformy Azure i podsieć z wystarczającą przestrzenią adresową IP dla tych składników:
   - Obie maszyny wirtualne
   - Adres IP klastra trybu failover
   - Adres IP dla każdego FCIu
- System DNS skonfigurowany w sieci platformy Azure wskazujący kontrolery domeny

Po spełnieniu tych wymagań wstępnych można rozpocząć tworzenie klastra trybu failover. Pierwszym krokiem jest utworzenie maszyn wirtualnych.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1. Tworzenie maszyn wirtualnych

1. Zaloguj się do [Azure Portal](https://portal.azure.com) z subskrypcją.

1. [Utwórz zestaw dostępności platformy Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   Zestaw dostępności grupuje maszyny wirtualne w domenach błędów i domenach aktualizacji. Gwarantuje to, że aplikacja nie ma wpływ na pojedyncze punkty awarii, takie jak przełącznik sieciowy lub jednostka mocy stojaka serwerów.

   Jeśli nie utworzono grupy zasobów dla maszyn wirtualnych, zrób to podczas tworzenia zestawu dostępności platformy Azure. Jeśli używasz Azure Portal do utworzenia zestawu dostępności, wykonaj następujące czynności:

   1. W Azure Portal wybierz pozycję **Utwórz zasób** , aby otworzyć witrynę Azure Marketplace. Wyszukaj **zestaw dostępności**.
   1. Wybierz pozycję **zestaw dostępności**.
   1. Wybierz przycisk **Utwórz**.
   1. W obszarze **Tworzenie zestawu dostępności**podaj następujące wartości:
      - **Nazwa**: Nazwa zestawu dostępności.
      - **Subskrypcja**: Twoja subskrypcja platformy Azure.
      - **Grupa zasobów**: Jeśli chcesz użyć istniejącej grupy, kliknij pozycję **Wybierz istniejącą** , a następnie wybierz grupę z listy. W przeciwnym razie wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy.
      - **Lokalizacja**: Ustaw lokalizację, w której planujesz utworzyć maszyny wirtualne.
      - **Domeny błędów**: Użyj ustawienia domyślnego (**3**).
      - **Aktualizowanie domen**: Użyj domyślnego (**5**).
   1. Wybierz pozycję **Utwórz** , aby utworzyć zestaw dostępności.

1. Utwórz maszyny wirtualne w zestawie dostępności.

   Udostępnij dwie SQL Server maszyny wirtualne w zestawie dostępności platformy Azure. Aby uzyskać instrukcje, zobacz temat [udostępnianie maszyny wirtualnej SQL Server w Azure Portal](create-sql-vm-portal.md).

   Umieść obie maszyny wirtualne:

   - W tej samej grupie zasobów platformy Azure, w której znajduje się zestaw dostępności
   - W tej samej sieci, w której znajduje się kontroler domeny
   - W podsieci, która ma wystarczającą przestrzeń adresową IP dla maszyn wirtualnych i wszystkich FCIs, które mogą ostatecznie być używane w klastrze
   - W zestawie dostępności platformy Azure

      >[!IMPORTANT]
      >Nie można ustawić ani zmienić zestawu dostępności po utworzeniu maszyny wirtualnej.
      >

   Wybierz obraz z witryny Azure Marketplace. Możesz użyć obrazu portalu Azure Marketplace zawierającego system Windows Server i SQL Server lub użyć systemu z systemem Windows Server. Aby uzyskać szczegółowe informacje, zobacz [omówienie SQL Server w usłudze Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

   Oficjalne obrazy SQL Server w galerii platformy Azure obejmują zainstalowane wystąpienie SQL Server, oprogramowanie instalacyjne SQL Server i wymagany klucz.

   Wybierz odpowiedni obraz, w zależności od tego, jak chcesz uregulować licencję SQL Server:

   - **Licencjonowanie za użycie**. Koszt dla tych obrazów na sekundę obejmuje SQL Server Licencjonowanie:
      - **SQL Server 2016 Enterprise w systemie Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard w systemie Windows Server 2016 Datacenter**
      - **Deweloper SQL Server 2016 w systemie Windows Server 2016 Datacenter**

   - **Przenieś własną licencję (BYOL)**

      - **BYOL SQL Server 2016 Enterprise w systemie Windows Server 2016 Datacenter**
      - **BYOL SQL Server 2016 Standard w systemie Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Po utworzeniu maszyny wirtualnej Usuń wstępnie zainstalowane wystąpienie SQL Server autonomicznej. Przed skonfigurowaniem klastra trybu failover i Bezpośrednie miejsca do magazynowania należy użyć wstępnie zainstalowanego nośnika SQL Server do utworzenia SQL Server FCI.
   >

   Alternatywnie możesz użyć obrazów portalu Azure Marketplace, które zawierają tylko system operacyjny. Wybierz obraz **systemu Windows Server 2016 Datacenter** i zainstaluj SQL Server FCI po skonfigurowaniu klastra trybu failover i bezpośrednie miejsca do magazynowania. Ten obraz nie zawiera nośnika instalacyjnego SQL Server. Umieść nośnik instalacyjny SQL Server w lokalizacji, w której można uruchomić go dla każdego serwera.

1. Po utworzeniu maszyn wirtualnych przez platformę Azure Połącz się z nimi za pomocą protokołu RDP (Remote Desktop Protocol).

   Gdy po raz pierwszy łączysz się z maszyną wirtualną przy użyciu protokołu RDP, zostanie wyświetlony monit z pytaniem, czy chcesz zezwolić na odnajdywanie komputera w sieci. Wybierz pozycję **Tak**.

1. Jeśli używasz jednego z obrazów maszyn wirtualnych opartych na SQL Server, Usuń wystąpienie SQL Server.

   1. W obszarze **programy i funkcje**kliknij prawym przyciskiem myszy pozycję **Microsoft SQL Server 2016 (64-bitowe)** i wybierz polecenie **Odinstaluj/Zmień**.
   1. Wybierz pozycję **Usuń**.
   1. Wybierz wystąpienie domyślne.
   1. Usuń wszystkie funkcje w obszarze **usługi aparatu bazy danych**. Nie usuwaj **funkcji udostępnionych**. Zobaczysz coś podobnego do poniższego zrzutu ekranu:

      ![Wybieranie funkcji](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Usuń**.

1. <a name="ports"></a>Otwórz porty zapory.

   Na każdej maszynie wirtualnej Otwórz te porty w zaporze systemu Windows:

   | Przeznaczenie | Port TCP | Uwagi
   | ------ | ------ | ------
   | SQL Server | 1433 | Normalny port dla domyślnych wystąpień SQL Server. Jeśli obraz został użyty z galerii, ten port zostanie automatycznie otwarty.
   | Sonda kondycji | 59999 | Dowolny otwarty port TCP. W późniejszym kroku należy skonfigurować [sondę kondycji](#probe) modułu równoważenia obciążenia oraz klaster, aby używać tego portu.  

1. Dodaj magazyn do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie magazynu](../../../virtual-machines/linux/disks-types.md).

   Obie maszyny wirtualne potrzebują co najmniej dwóch dysków z danymi.

   Dołączanie dysków nieprzetworzonych, a nie dysków sformatowanych w systemie plików NTFS.

      >[!NOTE]
      >W przypadku dołączania dysków sformatowanych w systemie plików NTFS można włączyć Bezpośrednie miejsca do magazynowania tylko bez sprawdzania uprawnień dysku. 
      > 

   Dołącz co najmniej dwie dysków SSD w warstwie Premium do każdej maszyny wirtualnej. Zalecamy co najmniej P30 (1 TB) dysków.

   Ustaw buforowanie hosta na **tylko do odczytu**.

   Pojemność magazynu używana w środowiskach produkcyjnych zależy od obciążenia. Wartości opisane w tym artykule dotyczą demonstracji i testowania.

1. [Dodaj maszyny wirtualne do istniejącej domeny](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować klaster trybu failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Krok 2. Konfigurowanie klastra trybu failover z systemem Windows Server za pomocą Bezpośrednie miejsca do magazynowania

Teraz można skonfigurować klaster trybu failover przy użyciu Bezpośrednie miejsca do magazynowania. W tej sekcji wykonaj następujące kroki:

1. Dodaj funkcję Klaster trybu failover systemu Windows Server.
1. Sprawdź poprawność klastra.
1. Utwórz klaster trybu failover.
1. Utwórz monitor w chmurze.
1. Dodaj magazyn.

### <a name="add-windows-server-failover-clustering"></a>Dodaj klaster trybu failover systemu Windows Server

1. Nawiąż połączenie z pierwszą maszyną wirtualną za pomocą protokołu RDP przy użyciu konta domeny, które jest członkiem lokalnej grupy administratorów i ma uprawnienia do tworzenia obiektów w Active Directory. Użyj tego konta do pozostałej części konfiguracji.

1. [Dodaj klaster trybu failover do każdej maszyny wirtualnej](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Aby zainstalować klaster trybu failover z interfejsu użytkownika, wykonaj następujące kroki na obu maszynach wirtualnych:

   1. W **Menedżer serwera**wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **Dodaj role i funkcje**.
   1. W **Kreatorze dodawania ról i funkcji**wybierz pozycję **dalej** , aż **wybierzesz pozycję funkcje**.
   1. W obszarze **Wybierz funkcje**wybierz pozycję **klaster trybu failover**. Dołącz wszystkie wymagane funkcje i narzędzia do zarządzania. Wybierz pozycję **Dodaj funkcje**.
   1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Zakończ** , aby zainstalować funkcje.

   Aby zainstalować klaster trybu failover przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Aby uzyskać dalsze informacje o następnych krokach, zobacz instrukcje w sekcji Krok 3 [rozwiązania z funkcją Hyper-zbieżność przy użyciu bezpośrednie miejsca do magazynowania w systemie Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Weryfikowanie klastra

Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
1. W obszarze **Menedżer klastra trybu failover**wybierz pozycję **Akcja**, a następnie wybierz pozycję **Weryfikuj konfigurację**.
1. Wybierz przycisk **Dalej**.
1. W obszarze **Wybierz serwery lub klaster**wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **opcje testowania**wybierz opcję **Uruchom tylko wybrane testy**. Wybierz przycisk **Dalej**.
1. W obszarze **wybór testu**zaznacz wszystkie testy z wyjątkiem **magazynu**, jak pokazano poniżej:

   ![Wybierz testy weryfikacji klastra](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Wybierz przycisk **Dalej**.
1. W obszarze **potwierdzenie**wybierz pozycję **dalej**.

Kreator weryfikacji konfiguracji uruchamia testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Aby utworzyć klaster trybu failover, potrzebne są:

- Nazwy maszyn wirtualnych, które staną się węzłami klastra
- Nazwa klastra trybu failover
- Adres IP klastra trybu failover <br/>
  Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci co węzły klastra.

#### <a name="windows-server-2008-through-windows-server-2016"></a>System Windows Server 2008 za poorednictwem systemu Windows Server 2016

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2008 za pomocą systemu Windows Server 2016. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019. Aby uzyskać więcej informacji, zobacz [klaster trybu failover: obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Utwórz monitor w chmurze

Monitor w chmurze to nowy typ monitora kworum klastra, który jest przechowywany w obiekcie blob usługi Azure Storage. Eliminuje to konieczność stosowania oddzielnej maszyny wirtualnej, która hostuje udział monitora.

1. [Utwórz monitor chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów BLOB.

1. Zapisz klucze dostępu i adres URL kontenera.

1. Skonfiguruj monitor kworum klastra trybu failover. Zobacz [Konfigurowanie monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Dodawanie magazynu

Dyski dla Bezpośrednie miejsca do magazynowania muszą być puste. Nie mogą zawierać partycji ani innych danych. Aby wyczyścić dyski, wykonaj [kroki opisane w tym przewodniku](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Włącz bezpośrednie miejsca do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Poniższy skrypt programu PowerShell umożliwia Bezpośrednie miejsca do magazynowania:  

   ```powershell
   Enable-ClusterS2D
   ```

   W **Menedżer klastra trybu failover**można teraz zobaczyć pulę magazynów.

1. [Utwórz wolumin](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Bezpośrednie miejsca do magazynowania automatycznie tworzy pulę magazynów po jej włączeniu. Teraz można przystąpić do tworzenia woluminu. Polecenie cmdlet programu PowerShell `New-Volume` automatyzuje proces tworzenia woluminu. Ten proces obejmuje formatowanie, dodanie woluminu do klastra i utworzenie udostępniony wolumin klastra (CSV). Ten przykład tworzy wolumin CSV 800-gigabajt (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po zakończeniu wykonywania tego polecenia wolumin 800 GB zostanie zainstalowany jako zasób klastra. Wolumin jest pod adresem `C:\ClusterStorage\Volume1\` .

   Ten zrzut ekranu przedstawia udostępniony wolumin klastra z Bezpośrednie miejsca do magazynowania:

   ![udostępniony wolumin klastra](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3. Testowanie trybu failover klastra trybu failover

W **Menedżer klastra trybu failover**upewnij się, że można przenieść zasób magazynu do innego węzła klastra. Jeśli można nawiązać połączenie z klastrem trybu failover przy użyciu **Menedżer klastra trybu failover** i przenieść magazyn z jednego węzła do drugiego, można skonfigurować FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Krok 4. tworzenie SQL Server FCI

Po skonfigurowaniu klastra trybu failover i wszystkich składników klastra, w tym magazynu, można utworzyć SQL Server FCI.

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover**upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby na tę maszynę wirtualną.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w lokalizacji `C:\SQLServer_<version number>_Full` . Wybierz pozycję **Konfiguracja**.

1. W **SQL Server centrum instalacji**wybierz opcję **Instalacja**.

1. Wybierz pozycję **nowa SQL Server Instalacja klastra trybu failover**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w magazynie klastrowanym. W przypadku Bezpośrednie miejsca do magazynowania nie jest to dysk udostępniony, ale punkt instalacji do woluminu na każdym serwerze. Bezpośrednie miejsca do magazynowania synchronizuje wolumin między obydwoma węzłami. Wolumin jest prezentowany w klastrze jako udostępniony wolumin klastra. Użyj punktu instalacji woluminu CSV dla katalogów danych.

   ![Katalogi danych](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Po wykonaniu instrukcji w Kreatorze Instalator zainstaluje SQL Server FCI na pierwszym węźle.

1. Po zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji SQL Server**. Wybierz opcję **Instalacja**.

1. Wybierz pozycję **Dodaj węzeł do klastra trybu failover SQL Server**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server i dodać serwer do FCI.

   >[!NOTE]
   >W przypadku korzystania z obrazu galerii portalu Azure Marketplace zawierającego SQL Server narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z jednego z tych obrazów, zainstaluj SQL Server narzędzia osobno. Zobacz [pobieranie SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="step-5-create-the-azure-load-balancer"></a>Krok 5. Tworzenie modułu równoważenia obciążenia platformy Azure

W przypadku usługi Azure Virtual Machines klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP SQL Server FCI.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie modułu równoważenia obciążenia platformy Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Utwórz moduł równoważenia obciążenia w Azure Portal

Aby utworzyć moduł równoważenia obciążenia:

1. W Azure Portal przejdź do grupy zasobów zawierającej maszyny wirtualne.

1. Wybierz pozycję **Dodaj**. Wyszukaj **Load Balancer**w portalu Azure Marketplace. Wybierz **Load Balancer**.

1. Wybierz przycisk **Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia przy użyciu:

   - **Subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów**: Grupa zasobów zawierająca maszyny wirtualne.
   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Region**: Lokalizacja platformy Azure, w której znajdują się maszyny wirtualne.
   - **Typ**: publiczny lub prywatny. Dostęp do prywatnego modułu równoważenia obciążenia można uzyskać z poziomu sieci wirtualnej. Większość aplikacji platformy Azure może korzystać z prywatnego modułu równoważenia obciążenia. Jeśli aplikacja wymaga dostępu do SQL Server bezpośrednio za pośrednictwem Internetu, użyj publicznego modułu równoważenia obciążenia.
   - **Jednostka SKU**: Standard.
   - **Sieć wirtualna**: sieć, w której znajduje się maszyna wirtualna.
   - **Przypisanie adresu IP**: statyczne. 
   - **Prywatny adres IP**: adres IP przypisany do zasobu sieci klastra SQL Server FCI.

 Poniższy zrzut ekranu przedstawia interfejs użytkownika **tworzenia modułu równoważenia obciążenia** :

   ![Konfigurowanie modułu równoważenia obciążenia](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure zawierającej maszyny wirtualne i Znajdź nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Wybierz moduł równoważenia obciążenia.

1. Wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracja IP sieci docelowej**wybierz pozycję **maszyna wirtualna** i wybierz maszyny wirtualne, które będą uczestniczyć w węzłach klastra. Pamiętaj, aby uwzględnić wszystkie maszyny wirtualne, które będą hostować FCI.

1. Wybierz **przycisk OK** , aby utworzyć pulę zaplecza.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurowanie sondy kondycji modułu równoważenia obciążenia

1. W bloku moduł równoważenia obciążenia wybierz pozycję **sondy kondycji**.

1. Wybierz pozycję **Dodaj**.

1. W bloku **Dodawanie sondy kondycji** <a name="probe"></a> Ustaw parametry sondy kondycji.

   - **Name**: Nazwa sondy kondycji
   - **Protokół**: TCP
   - **Port**: Ustaw na port utworzony w zaporze dla sondy kondycji w [tym kroku](#ports) <br/>W tym artykule w przykładzie zastosowano port TCP `59999` .
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejne błędy

1. Wybierz przycisk **OK**.

### <a name="set-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. W bloku moduł równoważenia obciążenia wybierz pozycję **reguły równoważenia obciążenia**.

1. Wybierz pozycję **Dodaj**.

1. Ustaw parametry reguły równoważenia obciążenia:

   - **Name**: nazwa dla reguł równoważenia obciążenia.
   - **Adres IP frontonu**: adres IP dla zasobu sieci klastra SQL Server FCI.
   - **Port**: port TCP SQL Server FCI. Domyślnym portem wystąpienia jest 1433.
   - **Port zaplecza**: używa tego samego portu co wartość **portu** w przypadku włączenia **swobodnego adresu IP (bezpośredni zwrot serwera)**.
   - **Pula zaplecza**: Nazwa puli zaplecza, która została wcześniej skonfigurowana.
   - **Sonda kondycji**: Sonda kondycji, która została wcześniej skonfigurowana.
   - **Trwałość sesji**: Brak.
   - **Limit czasu bezczynności (w minutach)**: 4.
   - **Zmienny adres IP (bezpośredni zwrot serwera)**: włączony.

1. Wybierz przycisk **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Krok 6. Konfigurowanie klastra dla sondy

Ustaw parametr portu sondowania klastra w programie PowerShell.

Aby ustawić parametr port sondowania klastra, należy zaktualizować zmienne w następującym skrypcie przy użyciu wartości z danego środowiska. Usuń nawiasy kątowe ( `<` i `>` ) ze skryptu.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Na poniższej liście opisano wartości, które należy zaktualizować:

   - `<Cluster Network Name>`: Nazwa klastra trybu failover systemu Windows Server dla sieci. W **Menedżer klastra trybu failover**  >  **sieci**, kliknij prawym przyciskiem myszy sieć i wybierz pozycję **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** .

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server Nazwa zasobu adresu IP FCI. W **Menedżer klastra trybu failover**  >  **role**w obszarze SQL Server rolę FCI w obszarze **Nazwa serwera**kliknij prawym przyciskiem myszy zasób adres IP i wybierz pozycję **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** . 

   - `<ILBIP>`: Adres IP ILB. Ten adres jest skonfigurowany w Azure Portal jako adres frontonu ILB. Jest to również SQL Server FCI adres IP. Można go znaleźć w **Menedżer klastra trybu failover** na tej samej stronie właściwości, na której został umieszczony `<SQL Server FCI IP Address Resource Name>` .  

   - `<nnnnn>`: Port sondy skonfigurowany w sondie kondycji modułu równoważenia obciążenia. Dowolny nieużywany port TCP jest prawidłowy.

>[!IMPORTANT]
>Maska podsieci parametru klastra musi być adresem IP protokołu TCP: `255.255.255.255` .
>

Po ustawieniu sondy klastra można zobaczyć wszystkie parametry klastra w programie PowerShell. Uruchom ten skrypt:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7. Testowanie pracy w trybie failover FCI

Przetestuj tryb failover FCI, aby sprawdzić poprawność działania klastra. Wykonaj następujące czynności:

1. Połącz się z jednym z SQL Server węzłów klastra FCI przy użyciu protokołu RDP.

1. Otwórz **Menedżer klastra trybu failover**. Wybierz pozycję **role**. Zwróć uwagę na to, który węzeł jest właścicielem SQL Server roli FCI.

1. Kliknij prawym przyciskiem myszy SQL Server rolę FCI.

1. Wybierz pozycję **Przenieś**, a następnie wybierz pozycję **najlepszy możliwy węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę i jej zasobów przejdź do trybu offline. Zasoby są następnie przenoszone i dostępne w trybie online w innym węźle.

### <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **SQL Server Management Studio** i nawiąż połączenie z SQL Server nazwą FCI.

>[!NOTE]
>Jeśli zachodzi taka potrzeba, można [pobrać SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="limitations"></a>Ograniczenia

Usługa Azure Virtual Machines obsługuje usługę Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem w udostępnionych woluminach klastra (CSV) i w [standardowym module równoważenia obciążenia](../../../load-balancer/load-balancer-standard-overview.md).

Na platformie Azure Virtual Machines usługa MSDTC nie jest obsługiwana w systemie Windows Server 2016 lub starszym, ponieważ:

- Nie można skonfigurować klastrowanego zasobu usługi MSDTC do korzystania z magazynu udostępnionego. W systemie Windows Server 2016, jeśli utworzysz zasób MSDTC, nie będzie widoczny żaden magazyn udostępniony dostępny do użycia, nawet jeśli magazyn jest dostępny. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowa usługa równoważenia obciążenia nie obsługuje portów RPC.

## <a name="see-also"></a>Zobacz także

[Konfigurowanie Bezpośrednie miejsca do magazynowania przy użyciu pulpitu zdalnego (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Rozwiązanie z funkcją Hyper-zbieżne z technologią Bezpośrednie miejsca do magazynowania](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Bezpośrednie miejsca do magazynowania — omówienie](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server obsługa Bezpośrednie miejsca do magazynowania](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
