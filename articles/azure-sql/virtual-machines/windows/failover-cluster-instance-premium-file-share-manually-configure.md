---
title: SQL Server FCI z udziałem plików w warstwie Premium — Azure Virtual Machines
description: W tym artykule wyjaśniono, jak utworzyć wystąpienie klastra trybu failover SQL Server przy użyciu udziału plików w warstwie Premium w usłudze Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 6929f6a8ca79c63d6d6d6fc2f3eee1b22ec49d39
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656898"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurowanie SQL Server wystąpienia klastra trybu failover z udziałem plików w warstwie Premium na platformie Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule opisano sposób tworzenia SQL Server wystąpienia klastra trybu failover (FCI) w usłudze Azure Virtual Machines przy użyciu [udziału plików w warstwie Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Udziały plików w warstwie Premium to dyski SSD, spójne udziały plików o małym opóźnieniu, które są w pełni obsługiwane do użycia z wystąpieniami klastrów trybu failover dla SQL Server 2012 lub nowszych w systemie Windows Server 2012 lub nowszym. Udziały plików w warstwie Premium zapewniają większą elastyczność, co pozwala na zmianę rozmiaru i skalowanie udziału plików bez przestojów.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem pracy należy poznać kilka rzeczy.

Należy mieć praktyczne zrozumienie tych technologii:

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jednym z aspektów, które należy wiedzieć, jest to, że w klastrze trybu failover maszyny wirtualnej platformy Azure IaaS zalecamy pojedyncze karty sieciowe na serwer (węzeł klastra) i jedną podsieć. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej Azure IaaS. Raport weryfikacji klastra ostrzega o tym, że węzły są dostępne tylko w jednej sieci. To ostrzeżenie można zignorować w klastrach trybu failover maszyny wirtualnej usługi Azure IaaS.

Należy również uzyskać ogólne informacje na temat tych technologii:

- [Udział plików platformy Azure w warstwie Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupa zasobów platformy Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> W tej chwili SQL Server wystąpienia klastra trybu failover na platformie Azure Virtual Machines są obsługiwane tylko w SQL Server [trybie uproszczonego zarządzania](sql-vm-resource-provider-register.md#management-modes) [rozszerzenia IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Aby zmienić tryb pełnego rozszerzenia na lekki, Usuń zasób **maszyny wirtualnej SQL** dla odpowiednich maszyn wirtualnych, a następnie zarejestruj je u dostawcy zasobów maszyny wirtualnej SQL w trybie uproszczonym. Podczas usuwania zasobu **maszyny wirtualnej SQL** przy użyciu Azure Portal **Wyczyść pole wyboru obok odpowiedniej maszyny wirtualnej**. Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, stosowanie poprawek i zaawansowane zarządzanie portalem. Te funkcje nie będą działały w przypadku maszyn wirtualnych SQL po ponownym zainstalowaniu agenta w trybie uproszczonego zarządzania.
> 


Udziały plików w warstwie Premium zapewniają możliwości IOPS i przepływności, które będą spełniały potrzeby wielu obciążeń. W przypadku obciążeń intensywnie korzystających z operacji we/wy należy rozważyć [SQL Server wystąpienia klastra trybu failover z bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md), na podstawie zarządzanych dysków Premium lub Ultra dysków.  

Sprawdź działanie IOPS w środowisku i sprawdź, czy w wersji Premium udziały plików będą wymagały potrzebnych operacji we/wy przed rozpoczęciem wdrażania lub migracji. Liczniki dysku monitora wydajności systemu Windows służą do monitorowania łącznej liczby operacji we/wy (transfery dysku/s) i przepływności (bajty dysku/s) wymaganych dla plików SQL Server, dzienników i tymczasowych baz danych.

Wiele obciążeń ma rozbicie operacji we/wy, dlatego dobrym pomysłem jest sprawdzenie przez duże użycie i uwzględnienie zarówno maksymalnej liczby IOPS, jak i średniego czasu IOPS. Udziały plików w warstwie Premium zapewniają operacje we/wy na podstawie rozmiaru udziału. Udziały plików w warstwie Premium zapewniają również rozbicie, które pozwala na przebicie operacji we/wy do trzykrotnej kwoty linii bazowej przez maksymalnie jedną godzinę.

Aby uzyskać więcej informacji na temat wydajności udziałów plików w warstwie Premium, zobacz [warstwy wydajności udziału plików](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licencjonowanie i Cennik

Na platformie Azure Virtual Machines można licencjonować SQL Server przy użyciu obrazów maszyn wirtualnych "płatność zgodnie z rzeczywistym użyciem" lub "Przenieś własną licencję" (BYOL). Wybrany typ obrazu ma wpływ na sposób naliczania opłat.

Dzięki licencjonowaniu z płatnością zgodnie z rzeczywistym użyciem wystąpienie klastra trybu failover (FCI) SQL Server na platformie Azure Virtual Machines powoduje naliczanie opłat za wszystkie węzły FCI, w tym węzły pasywne. Aby uzyskać więcej informacji, zobacz [SQL Server Enterprise Virtual Machines cennika](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Jeśli masz Umowa Enterprise z programem Software Assurance, możesz użyć jednego wolnego węzła pasywnej FCI dla każdego aktywnego węzła. Aby skorzystać z tej korzyści na platformie Azure, użyj obrazów maszyn wirtualnych BYOL i Użyj tej samej licencji na węzłach aktywnych i pasywnych FCI. Aby uzyskać więcej informacji, zobacz [Umowa Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Aby porównać usługę płatność zgodnie z rzeczywistym użyciem i BYOL Licencjonowanie na potrzeby SQL Server na maszynie wirtualnej platformy Azure, zobacz Wprowadzenie [do SQL Server maszyn wirtualnych](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

Aby uzyskać pełne informacje na temat licencjonowania SQL Server, zobacz [Cennik](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Strumień pliku

Element FILESTREAM nie jest obsługiwany w przypadku klastra trybu failover z udziałem plików w warstwie Premium. Aby użyć FILESTREAM, wdróż klaster przy użyciu [bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule należy dysponować:

- Subskrypcję platformy Microsoft Azure.
- Domena systemu Windows w usłudze Azure Virtual Machines.
- Konto użytkownika domeny, które ma uprawnienia do tworzenia obiektów na platformie Azure Virtual Machines i w Active Directory.
- Konto użytkownika domeny do uruchamiania usługi SQL Server i służy do logowania się do maszyny wirtualnej podczas instalowania udziału plików. 
- Sieć wirtualna platformy Azure i podsieć z wystarczającą przestrzenią adresową IP dla tych składników:
   - Dwie maszyny wirtualne
   - Adres IP klastra trybu failover
   - Adres IP dla każdego FCIu
- System DNS skonfigurowany w sieci platformy Azure, wskazujący kontrolery domeny.
- [Udział plików w warstwie Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) , który ma być używany jako dysk klastrowany, na podstawie przydziału magazynu bazy danych dla plików danych.
- Jeśli korzystasz z systemu Windows Server 2012 R2 lub starszego, będziesz potrzebować innego udziału plików, który będzie używany jako monitor udostępniania plików, ponieważ monitory chmur są obsługiwane dla systemu Windows 2016 i nowszych wersji. Możesz użyć innego udziału plików platformy Azure lub użyć udziału plików na oddzielnej maszynie wirtualnej. Jeśli zamierzasz użyć innego udziału plików platformy Azure, możesz go zainstalować przy użyciu tego samego procesu co w przypadku udziału plików Premium używanego na dysku klastrowym. 

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

   - W tej samej grupie zasobów platformy Azure, w której znajduje się zestaw dostępności.
   - W tej samej sieci, w której znajduje się kontroler domeny.
   - W podsieci, która ma wystarczającą przestrzeń adresową IP dla maszyn wirtualnych i wszystkich FCIs, które mogą ostatecznie być używane w klastrze.
   - W zestawie dostępności platformy Azure.

      >[!IMPORTANT]
      >Nie można ustawić ani zmienić zestawu dostępności po utworzeniu maszyny wirtualnej.
      >

   Wybierz obraz z witryny Azure Marketplace. Możesz użyć obrazu portalu Azure Marketplace zawierającego system Windows Server i SQL Server lub użyć systemu z systemem Windows Server. Aby uzyskać szczegółowe informacje, zobacz [omówienie SQL Server w usłudze Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

   Oficjalne obrazy SQL Server w galerii platformy Azure obejmują zainstalowane wystąpienie SQL Server, oprogramowanie instalacyjne SQL Server i wymagany klucz.

   > [!IMPORTANT]
   > Po utworzeniu maszyny wirtualnej Usuń wstępnie zainstalowane wystąpienie SQL Server autonomicznej. Przed skonfigurowaniem klastra trybu failover i udziału plików w warstwie Premium jako magazynu użyjesz wstępnie zainstalowanego SQL Server nośnika SQL Server.
   > 

   Alternatywnie możesz użyć obrazów portalu Azure Marketplace, które zawierają tylko system operacyjny. Wybierz obraz **systemu Windows Server 2016 Datacenter** i zainstaluj SQL Server FCI po skonfigurowaniu klastra trybu failover i udziału plików w warstwie Premium jako magazynu. Ten obraz nie zawiera nośnika instalacyjnego SQL Server. Umieść nośnik instalacyjny SQL Server w lokalizacji, w której można uruchomić go dla każdego serwera.

1. Po utworzeniu maszyn wirtualnych przez platformę Azure Połącz się z nimi przy użyciu Remote Desktop Protocol (RDP).

   Gdy po raz pierwszy łączysz się z maszyną wirtualną przy użyciu protokołu RDP, zostanie wyświetlony monit z pytaniem, czy chcesz zezwolić na odnajdywanie komputera w sieci. Wybierz pozycję **Tak**.

1. Jeśli używasz jednego z obrazów maszyn wirtualnych opartych na SQL Server, Usuń wystąpienie SQL Server.

   1. W obszarze **programy i funkcje**kliknij prawym przyciskiem myszy pozycję **Microsoft SQL Server 201_ (64-bitowe)** i wybierz polecenie **Odinstaluj/Zmień**.
   1. Wybierz pozycję **Usuń**.
   1. Wybierz wystąpienie domyślne.
   1. Usuń wszystkie funkcje w obszarze **usługi aparatu bazy danych**. Nie usuwaj **funkcji udostępnionych**. Zobaczysz coś podobnego do poniższego zrzutu ekranu:

        ![Wybieranie funkcji](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Usuń**.

1. <span id="ports"> </span> Otwórz porty zapory.  

   Na każdej maszynie wirtualnej Otwórz te porty w zaporze systemu Windows:

   | Przeznaczenie | Port TCP | Uwagi
   | ------ | ------ | ------
   | SQL Server | 1433 | Normalny port dla domyślnych wystąpień SQL Server. Jeśli obraz został użyty z galerii, ten port zostanie automatycznie otwarty.
   | Sonda kondycji | 59999 | Dowolny otwarty port TCP. W późniejszym kroku należy skonfigurować [sondę kondycji](#probe) modułu równoważenia obciążenia oraz klaster, aby używać tego portu.
   | Udział plików | 445 | Port używany przez usługę udziału plików.

1. [Dodaj maszyny wirtualne do istniejącej domeny](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Po utworzeniu i skonfigurowaniu maszyn wirtualnych można skonfigurować udział plików w warstwie Premium.

## <a name="step-2-mount-the-premium-file-share"></a>Krok 2. Instalowanie udziału plików w warstwie Premium

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do konta magazynu.
1. Przejdź do obszaru **udziały plików** w obszarze **Usługa plików** i wybierz udział plików w warstwie Premium, którego chcesz użyć dla magazynu SQL.
1. Wybierz pozycję **Połącz** , aby wyświetlić parametry połączenia dla udziału plików.
1. Wybierz literę dysku, której chcesz użyć, z listy rozwijanej, a następnie skopiuj oba bloki kodu do Notatnika.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Kopiuj Oba polecenia programu PowerShell z portalu usługi File Share Connect":::

1. Użyj protokołu RDP, aby nawiązać połączenie z maszyną wirtualną SQL Server przy użyciu konta, które będzie używane przez SQL Server FCI dla konta usługi.
1. Otwórz konsolę administracyjną poleceń programu PowerShell.
1. Uruchom polecenia zapisane wcześniej podczas pracy z portalem.
1. Przejdź do udziału za pomocą Eksploratora plików lub okna dialogowego **uruchamiania** (logo systemu Windows + r). Użyj ścieżki sieciowej `\\storageaccountname.file.core.windows.net\filesharename` . Na przykład: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Utwórz co najmniej jeden folder w nowo połączonym udziale plików, w którym mają zostać umieszczone pliki danych SQL.
1. Powtórz te kroki na każdej maszynie SQL Server VM, która będzie uczestniczyć w klastrze.

  > [!IMPORTANT]
  > - Rozważ użycie oddzielnego udziału plików dla plików kopii zapasowej w celu zapisania liczby IOPS i pojemności miejsca w tym udziale dla plików danych i dziennika. W przypadku plików kopii zapasowej można użyć Premium lub standardowego udziału plików.
  > - Jeśli korzystasz z systemu Windows 2012 R2 lub starszego, wykonaj te same kroki, aby zainstalować udział plików, który ma być używany jako monitor udziału plików. 
  > 

## <a name="step-3-configure-the-failover-cluster"></a>Krok 3. Konfigurowanie klastra trybu failover

Teraz można skonfigurować klaster trybu failover. W tej sekcji wykonaj następujące czynności:

1. Dodaj funkcję Klaster trybu failover systemu Windows Server.
1. Sprawdź poprawność klastra.
1. Utwórz klaster trybu failover.
1. Utwórz monitor w chmurze (dla systemu Windows Server 2016 lub nowszego) lub monitora udostępniania plików (dla systemu Windows Server 2012 R2 i starszej wersji).


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

### <a name="validate-the-cluster"></a>Weryfikowanie klastra

Sprawdź poprawność klastra w interfejsie użytkownika lub za pomocą programu PowerShell.

Aby sprawdzić poprawność klastra przy użyciu interfejsu użytkownika, wykonaj następujące czynności na jednej z maszyn wirtualnych:

1. W obszarze **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
1. W obszarze **Menedżer klastra trybu failover**wybierz pozycję **Akcja**, a następnie wybierz pozycję **Weryfikuj konfigurację**.
1. Wybierz przycisk **Dalej**.
1. W obszarze **Wybierz serwery lub klaster**wprowadź nazwy obu maszyn wirtualnych.
1. W obszarze **opcje testowania**wybierz opcję **Uruchom tylko wybrane testy**. Wybierz przycisk **Dalej**.
1. W obszarze **wybór testu**zaznacz wszystkie testy z wyjątkiem **magazynu** i **bezpośrednie miejsca do magazynowania**, jak pokazano poniżej:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Wybierz testy weryfikacji klastra":::

1. Wybierz przycisk **Dalej**.
1. W obszarze **potwierdzenie**wybierz pozycję **dalej**.

**Kreator weryfikacji konfiguracji** uruchamia testy weryfikacyjne.

Aby sprawdzić poprawność klastra przy użyciu programu PowerShell, uruchom następujący skrypt z sesji programu PowerShell administratora na jednej z maszyn wirtualnych:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po sprawdzeniu poprawności klastra utwórz klaster trybu failover.

### <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

Aby utworzyć klaster trybu failover, potrzebne są:

- Nazwy maszyn wirtualnych, które staną się węzłami klastra.
- Nazwa klastra trybu failover.
- Adres IP klastra trybu failover. Można użyć adresu IP, który nie jest używany w tej samej sieci wirtualnej platformy Azure i podsieci co węzły klastra.

#### <a name="windows-server-2012-through-windows-server-2016"></a>System Windows Server 2012 za poorednictwem systemu Windows Server 2016

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2012 za pomocą systemu Windows Server 2016. Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Poniższy skrypt programu PowerShell tworzy klaster trybu failover dla systemu Windows Server 2019. Aby uzyskać więcej informacji, zobacz [klaster trybu failover: obiekt sieci klastra](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Zaktualizuj skrypt przy użyciu nazw węzłów (nazw maszyn wirtualnych) i dostępnego adresu IP z sieci wirtualnej platformy Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-and-later"></a>Utwórz monitor w chmurze (win 2016 i nowszych)

Jeśli korzystasz z systemu Windows Server 2016 lub nowszego, musisz utworzyć monitor w chmurze. Monitor w chmurze to nowy typ monitora kworum klastra, który jest przechowywany w obiekcie blob usługi Azure Storage. Eliminuje to konieczność użycia oddzielnej maszyny wirtualnej, która hostuje udział monitora lub korzysta z oddzielnego udziału plików.

1. [Utwórz monitor chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Utwórz kontener obiektów BLOB.

1. Zapisz klucze dostępu i adres URL kontenera.

### <a name="configure-quorum"></a>Konfigurowanie kworum 

W przypadku systemu Windows Server 2016 i nowszych skonfiguruj klaster tak, aby korzystał z właśnie utworzonego monitora chmurowego. Wykonaj wszystkie kroki [konfigurowania monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

W przypadku systemu Windows Server 2012 R2 lub starszego wykonaj te same czynności w temacie [Konfigurowanie monitora kworum w interfejsie użytkownika](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) , ale na stronie **Wybieranie monitora kworum** wybierz opcję **Konfiguruj udostępnianie plików** . Określ udział plików, który został przydzielony jako monitor udostępniania plików, niezależnie od tego, czy jest on skonfigurowany na oddzielnej maszynie wirtualnej, czy zainstalowany z platformy Azure. 


## <a name="step-4-test-cluster-failover"></a>Krok 4. Testowanie trybu failover klastra

Testowanie pracy w trybie failover w klastrze. W **Menedżer klastra trybu failover**kliknij prawym przyciskiem myszy klaster i wybierz pozycję **więcej akcji**  >  **Przenieś podstawowy zasób klastra**  >  **Wybierz węzeł**, a następnie wybierz inny węzeł klastra. Przenieś zasób podstawowego klastra do każdego węzła klastra, a następnie przenieś go z powrotem do węzła podstawowego. Jeśli możesz pomyślnie przenieść klaster do każdego węzła, możesz zainstalować SQL Server.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Testowanie trybu failover klastra przez przeniesienie zasobu podstawowego do innych węzłów":::

## <a name="step-5-create-the-sql-server-fci"></a>Krok 5. tworzenie SQL Server FCI

Po skonfigurowaniu klastra trybu failover można utworzyć SQL Server FCI.

1. Nawiąż połączenie z pierwszą maszyną wirtualną przy użyciu protokołu RDP.

1. W **Menedżer klastra trybu failover**upewnij się, że wszystkie podstawowe zasoby klastra znajdują się na pierwszej maszynie wirtualnej. W razie potrzeby Przenieś wszystkie zasoby do tej maszyny wirtualnej.

1. Znajdź nośnik instalacyjny. Jeśli maszyna wirtualna używa jednego z obrazów portalu Azure Marketplace, nośnik znajduje się w lokalizacji `C:\SQLServer_<version number>_Full` . Wybierz pozycję **Konfiguracja**.

1. W **centrum instalacji SQL Server**wybierz opcję **Instalacja**.

1. Wybierz pozycję **nowa SQL Server Instalacja klastra trybu failover**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server FCI.

   Katalogi danych FCI muszą znajdować się w udziale plików w warstwie Premium. Wprowadź pełną ścieżkę udziału w tej formie: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Zostanie wyświetlone ostrzeżenie z informacją o tym, że jako katalog danych został określony serwer plików. To ostrzeżenie jest oczekiwane. Upewnij się, że konto użytkownika używane do uzyskiwania dostępu do maszyny wirtualnej za pośrednictwem protokołu RDP podczas utrwalania udziału plików jest tym samym kontem, którego używa Usługa SQL Server, aby uniknąć możliwych błędów.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Korzystanie z udziału plików jako katalogów danych SQL":::

1. Po wykonaniu kroków opisanych w Kreatorze Instalator zainstaluje SQL Server FCI na pierwszym węźle.

1. Po zainstalowaniu przez Instalatora FCI na pierwszym węźle Nawiąż połączenie z drugim węzłem przy użyciu protokołu RDP.

1. Otwórz **centrum instalacji SQL Server**. Wybierz opcję **Instalacja**.

1. Wybierz pozycję **Dodaj węzeł do klastra trybu failover SQL Server**. Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, aby zainstalować SQL Server i dodać serwer do FCI.

   >[!NOTE]
   >Jeśli korzystasz z obrazu galerii portalu Azure Marketplace z SQL Server, narzędzia SQL Server zostały dołączone do obrazu. Jeśli nie korzystasz z jednego z tych obrazów, zainstaluj SQL Server narzędzia osobno. Zobacz [pobieranie SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="step-6-create-the-azure-load-balancer"></a>Krok 6. Tworzenie modułu równoważenia obciążenia platformy Azure

W przypadku usługi Azure Virtual Machines klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP SQL Server FCI.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie modułu równoważenia obciążenia platformy Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Utwórz moduł równoważenia obciążenia w Azure Portal

Aby utworzyć moduł równoważenia obciążenia:

1. W Azure Portal przejdź do grupy zasobów zawierającej maszyny wirtualne.

1. Wybierz pozycję **Dodaj**. Wyszukaj **Load Balancer**w portalu Azure Marketplace. Wybierz **Load Balancer**.

1. Wybierz przycisk **Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia przy użyciu następujących wartości:

   - **Subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów**: Grupa zasobów zawierająca maszyny wirtualne.
   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Region**: Lokalizacja platformy Azure, w której znajdują się maszyny wirtualne.
   - **Typ**: publiczny lub prywatny. Dostęp do prywatnego modułu równoważenia obciążenia można uzyskać z poziomu sieci wirtualnej. Większość aplikacji platformy Azure może korzystać z prywatnego modułu równoważenia obciążenia. Jeśli aplikacja wymaga dostępu do SQL Server bezpośrednio za pośrednictwem Internetu, użyj publicznego modułu równoważenia obciążenia.
   - **Jednostka SKU**: Standard.
   - **Sieć wirtualna**: sieć, w której znajduje się maszyna wirtualna.
   - **Przypisanie adresu IP**: statyczne. 
   - **Prywatny adres IP**: adres IP przypisany do zasobu sieci klastra SQL Server FCI.

   Na poniższej ilustracji przedstawiono interfejs użytkownika **tworzenia modułu równoważenia obciążenia** :

   ![Konfigurowanie modułu równoważenia obciążenia](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia

1. Wróć do grupy zasobów platformy Azure zawierającej maszyny wirtualne i Znajdź nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Wybierz moduł równoważenia obciążenia.

1. Wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracja IP sieci docelowej**wybierz pozycję **maszyna wirtualna** i wybierz maszyny wirtualne, które będą uczestniczyć w węzłach klastra. Pamiętaj, aby uwzględnić wszystkie maszyny wirtualne, które będą hostować FCI.

1. Wybierz **przycisk OK** , aby utworzyć pulę zaplecza.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurowanie sondy kondycji modułu równoważenia obciążenia

1. W bloku moduł równoważenia obciążenia wybierz pozycję **sondy kondycji**.

1. Wybierz pozycję **Dodaj**.

1. W bloku **Dodawanie sondy kondycji** <span id="probe"> </span> ustaw następujące parametry sondy kondycji.

   - **Name**: Nazwa sondy kondycji.
   - **Protokół**: TCP.
   - **Port**: Port utworzony w zaporze dla sondy kondycji w [tym kroku](#ports). W tym artykule w przykładzie zastosowano port TCP `59999` .
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejne błędy.

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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Krok 7. Konfigurowanie klastra dla sondy

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

## <a name="step-8-test-fci-failover"></a>Krok 8. Testowanie pracy w trybie failover FCI

Przetestuj tryb failover FCI, aby sprawdzić poprawność działania klastra. Wykonaj następujące czynności:

1. Połącz się z jednym z SQL Server węzłów klastra FCI przy użyciu protokołu RDP.

1. Otwórz **Menedżer klastra trybu failover**. Wybierz pozycję **role**. Zwróć uwagę na to, który węzeł jest właścicielem SQL Server roli FCI.

1. Kliknij prawym przyciskiem myszy SQL Server rolę FCI.

1. Wybierz pozycję **Przenieś**, a następnie wybierz pozycję **najlepszy możliwy węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę i jej zasoby przechodzą do trybu offline. Zasoby zostaną następnie przeniesione i przywrócone do trybu online w innym węźle.

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

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
