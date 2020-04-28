---
title: Konfigurowanie wysokiej dostępności dla Azure Resource Manager maszyn wirtualnych | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć zawsze włączona Grupa dostępności z maszynami wirtualnymi platformy Azure w trybie Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75374263"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Automatyczne konfigurowanie grup dostępności na platformie Azure Virtual Machines: Menedżer zasobów

W tym samouczku pokazano, jak utworzyć SQL Server grupę dostępności, która używa Azure Resource Manager maszyn wirtualnych. W tym samouczku do konfigurowania szablonu są wykorzystywane usługi Azure. Możesz przejrzeć ustawienia domyślne, wpisać wymagane ustawienia i zaktualizować bloki w portalu w ramach tego samouczka.

Pełny samouczek tworzy SQL Server grupę dostępności na platformie Azure Virtual Machines, która obejmuje następujące elementy:

* Sieć wirtualna z wieloma podsieciami, w tym fronton i podsieć zaplecza
* Dwa kontrolery domeny z domeną Active Directory
* Dwie maszyny wirtualne z systemem SQL Server i wdrożone w podsieci zaplecza i dołączone do domeny Active Directory
* Klaster trybu failover z trzema węzłami z modelem kworum większościowego węzłów
* Grupa dostępności, która ma dwie repliki zatwierdzające synchronicznie bazy danych dostępności

Poniższa ilustracja przedstawia kompletne rozwiązanie.

![Architektura laboratorium testowego dla grup dostępności na platformie Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Wszystkie zasoby w tym rozwiązaniu należą do jednej grupy zasobów.

Przed rozpoczęciem tego samouczka Potwierdź następujące kwestie:

* Masz już konto platformy Azure. Jeśli go nie masz, [Utwórz konto w celu korzystania z wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Już wiesz, jak używać graficznego interfejsu użytkownika do aprowizacji SQL Server maszyny wirtualnej z galerii maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi maszyny wirtualnej SQL Server na platformie Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Masz już pełną wiedzę na temat grup dostępności. Aby uzyskać więcej informacji, zobacz [Always On Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).

> [!NOTE]
> Jeśli interesuje Cię korzystanie z grup dostępności w programie SharePoint, zobacz sekcję [Konfigurowanie grup dostępności w systemie SQL Server 2012 dla programu sharepoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

W tym samouczku Użyj Azure Portal do:

* Wybierz szablon zawsze włączony z portalu.
* Przejrzyj ustawienia szablonu i zaktualizuj kilka ustawień konfiguracji dla danego środowiska.
* Monitoruj platformę Azure w miarę tworzenia całego środowiska.
* Połącz się z kontrolerem domeny, a następnie na serwerze z uruchomionym SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Inicjowanie obsługi administracyjnej klastra z galerii
Platforma Azure udostępnia obraz galerii dla całego rozwiązania. Aby zlokalizować szablon:

1. Zaloguj się do Azure Portal przy użyciu swojego konta.
2. W Azure Portal kliknij pozycję **Utwórz zasób** , aby otworzyć **nowe** okienko.
3. W **nowym** okienku Wyszukaj funkcję **AlwaysOn**.
   ![Znajdź szablon AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. W wynikach wyszukiwania odszukaj **SQL Server zawsze włączony klaster**.
   ![Szablon AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. W obszarze **Wybierz model wdrażania**wybierz pozycję **Menedżer zasobów**.

### <a name="basics"></a>Podstawy
Kliknij pozycję **podstawowe** i skonfiguruj następujące ustawienia:

* **Nazwa użytkownika administratora** to konto użytkownika, które ma uprawnienia administratora domeny i jest członkiem stałej roli serwera sysadmin SQL Server na obu wystąpieniach SQL Server. Na potrzeby tego samouczka Użyj **administrator domeny**.
* **Hasło** jest hasłem konta administratora domeny. Użyj złożonego hasła. Potwierdź hasło.
* **Subskrypcja** to subskrypcja, którą usługa Azure Bill ma uruchamiać wszystkie wdrożone zasoby dla grupy dostępności. Jeśli Twoje konto ma wiele subskrypcji, możesz określić inną subskrypcję.
* **Grupa zasobów** to nazwa grupy, do której należą wszystkie zasoby platformy Azure, które są tworzone przez ten szablon. Na potrzeby tego samouczka Użyj **języka SQL-ha-RG**. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Lokalizacja** to region platformy Azure, w którym samouczek tworzy zasoby. Wybierz region platformy Azure.

Poniższy zrzut ekranu przedstawia kompletny blok **podstawy** :

![Podstawy](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Kliknij przycisk **OK**.

### <a name="domain-and-network-settings"></a>Ustawienia domeny i sieci
Ten szablon galerii platformy Azure tworzy domeny i kontrolery domeny. Tworzy również sieć i dwie podsieci. Szablon nie może tworzyć serwerów w istniejącej domenie lub sieci wirtualnej. W następnym kroku są konfigurowane ustawienia domeny i sieci.

W bloku **Ustawienia domeny i sieci** Sprawdź ustawienia wstępne ustawień domeny i sieci:

* **Nazwa domeny głównej lasu** to nazwa domeny dla domeny Active Directory, która hostuje klaster. Aby zapoznać się z samouczkiem, użyj **contoso.com**.
* **Nazwa Virtual Network** jest nazwą sieciową sieci wirtualnej platformy Azure. Aby zapoznać się z samouczkiem, użyj **autohaVNET**.
* **Nazwa podsieci kontrolera domeny** to nazwa części sieci wirtualnej, która hostuje kontroler domeny. Użyj **podsieci-1**. Ta podsieć używa prefiksu adresu **10.0.0.0/24**.
* **Nazwa podsieci SQL Server** jest nazwą części sieci wirtualnej, która hostuje serwery z systemem SQL Server i monitor udziału plików. Użyj **podsieci 2**. Ta podsieć używa prefiksu adresu **10.0.1.0/26**.

Aby dowiedzieć się więcej na temat sieci wirtualnych na platformie Azure, zobacz [Omówienie usługi Virtual Network](../../../virtual-network/virtual-networks-overview.md).  

**Ustawienia domeny i sieci** powinny wyglądać podobnie jak na poniższym zrzucie ekranu:

![Ustawienia domeny i sieci](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

W razie potrzeby można zmienić te wartości. Na potrzeby tego samouczka Użyj wartości domyślnych.

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="availability-group-settings"></a>Ustawienia grupy dostępności
W obszarze **Ustawienia grupy dostępności**przejrzyj wartości wstępne dla grupy dostępności i odbiornika.

* **Nazwa grupy dostępności** to nazwa zasobu klastra dla grupy dostępności. Na potrzeby tego samouczka Użyj **contoso-AG**.
* **Nazwa odbiornika grupy dostępności** jest używana przez klaster i wewnętrzną usługę równoważenia obciążenia. Klienci łączący się z SQL Server mogą używać tej nazwy do nawiązywania połączenia z odpowiednią repliką bazy danych. Na potrzeby tego samouczka Użyj usługi **contoso-Listener**.
* **Port odbiornika grupy dostępności** określa port TCP odbiornika SQL Server. Na potrzeby tego samouczka Użyj domyślnego portu **1433**.

W razie potrzeby można zmienić te wartości. Na potrzeby tego samouczka Użyj wartości domyślnych.  

![ustawienia grupy dostępności](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Kliknij przycisk **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Rozmiar maszyny wirtualnej, ustawienia magazynu
Na stronie **rozmiar maszyny wirtualnej, ustawienia magazynu**, wybierz rozmiar SQL Server maszynę wirtualną i zapoznaj się z innymi ustawieniami.

* Rozmiar **maszyny wirtualnej SQL Server** jest rozmiarem obu maszyn wirtualnych z systemem SQL Server. Wybierz odpowiedni rozmiar maszyny wirtualnej dla obciążenia. Jeśli tworzysz to środowisko dla samouczka, użyj **DS2**. W przypadku obciążeń produkcyjnych wybierz rozmiar maszyny wirtualnej, który może obsługiwać obciążenie. Wiele obciążeń produkcyjnych wymaga **DS4** lub większych. Szablon tworzy dwie maszyny wirtualne o tym rozmiarze i instaluje SQL Server na każdym z nich. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Na platformie Azure jest instalowana wersja Enterprise SQL Server. Koszt zależy od wersji i rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje na temat bieżących kosztów, zobacz [Cennik usługi Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Rozmiar maszyny wirtualnej kontrolera domeny** to rozmiar maszyny wirtualnej dla kontrolerów domeny. W tym samouczku użyto **D2**.
* **Rozmiar maszyny wirtualnej monitora udostępniania plików** jest rozmiarem maszyny wirtualnej monitora udziału plików. W tym samouczku należy użyć **a1**.
* **Konto magazynu SQL** to nazwa konta magazynu, które zawiera dane SQL Server i dyski systemu operacyjnego. Na potrzeby tego samouczka Użyj **alwaysonsql01**.
* **Konto magazynu DC** to nazwa konta magazynu dla kontrolerów domeny. Na potrzeby tego samouczka Użyj **alwaysondc01**.
* **Rozmiar dysku danych SQL Server** w TB to rozmiar dysku danych SQL Server w TB. Określ liczbę z przenumerów od 1 do 4. W tym samouczku należy użyć **1**.
* **Optymalizacja magazynu** ustawia określone ustawienia konfiguracji magazynu dla maszyn wirtualnych SQL Server na podstawie typu obciążenia. Wszystkie SQL Server maszyny wirtualne w tym scenariuszu używają usługi Premium Storage z pamięcią podręczną hosta Azure Disk Set jako tylko do odczytu. Ponadto można zoptymalizować ustawienia SQL Server dla obciążenia, wybierając jedno z tych trzech ustawień:

  * **Ogólne obciążenie** nie ustawia żadnych określonych ustawień konfiguracji.
  * Zestawy **przetwarzania transakcyjnego** — flaga śledzenia 1117 i 1118.
  * Zestawy **magazynów danych** — flaga śledzenia 1117 i 610.

Na potrzeby tego samouczka Użyj **ogólnego obciążenia**.

![Ustawienia przechowywania rozmiaru maszyny wirtualnej](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

#### <a name="a-note-about-storage"></a>Notatka dotycząca magazynu
Dodatkowe optymalizacje zależą od rozmiaru SQL Server dysków z danymi. Dla każdego dużego dysku danych platforma Azure dodaje dodatkowy magazyn Premium w wersji 1 TB. Gdy serwer wymaga co najmniej 2 TB, szablon tworzy pulę magazynów na każdej SQL Server maszynie wirtualnej. Pula magazynów jest formą wirtualizacji magazynu, w której skonfigurowano wiele dysków w celu zapewnienia większej pojemności, odporności i wydajności.  Następnie szablon tworzy miejsce do magazynowania w puli magazynów i przedstawia jeden dysk danych do systemu operacyjnego. Szablon określa ten dysk jako dysk danych dla SQL Server. Szablon dostosowuje pulę magazynów dla SQL Server przy użyciu następujących ustawień:

* Rozmiar paska jest ustawieniem przeplotu dla dysku wirtualnego. Obciążenia transakcyjne używają 64 KB. Obciążenia związane z magazynowaniem danych używają 256 KB.
* Odporność jest prosta (bez odporności).

> [!NOTE]
> Usługa Azure Premium Storage jest lokalnie nadmiarowa i utrzymuje trzy kopie danych w jednym regionie, więc nie jest wymagana dodatkowa odporność w puli magazynów.
>
>

* Liczba kolumn jest równa liczbie dysków w puli magazynów.

Aby uzyskać dodatkowe informacje na temat miejsca do magazynowania i pul magazynu, zobacz:

* [Miejsca do magazynowania — omówienie](https://technet.microsoft.com/library/hh831739.aspx)
* [Kopia zapasowa systemu Windows Server i pule magazynu](https://technet.microsoft.com/library/dn390929.aspx)

Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących konfiguracji SQL Server, zobacz [najlepsze rozwiązania w zakresie wydajności dla SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Ustawienia programu SQL Server
Na **SQL Server ustawienia**, przejrzyj i zmodyfikuj prefiks nazwy maszyny wirtualnej SQL Server, wersję SQL Server, SQL Server konto usługi i hasło oraz harmonogram konserwacji automatycznego stosowania poprawek SQL.

* **Prefiks nazwy SQL Server** jest używany do utworzenia nazwy dla każdej SQL Serverj maszyny wirtualnej. W tym samouczku Użyj programu **SqlServer**. Szablon nazywa SQL Server maszyn wirtualnych *SqlServer-0* i *SqlServer-1*.
* **Wersja SQL Server** jest wersją SQL Server. W tym samouczku użyto **SQL Server 2014**. Możesz również wybrać **SQL Server 2012** lub **SQL Server 2016**.
* **Nazwa użytkownika konta usługi SQL Server** jest nazwą konta domeny dla usługi SQL Server. W tym samouczku Użyj **SqlService**.
* **Hasło** jest hasłem dla konta usługi SQL Server.  Użyj złożonego hasła. Potwierdź hasło.
* **Harmonogram konserwacji automatycznego stosowania poprawek SQL** identyfikuje dzień tygodnia, w którym platforma Azure automatycznie naprawia serwery SQL. W tym samouczku wpisz **niedzielę**.
* **Godzina rozpoczęcia konserwacji automatycznej poprawki SQL** to godzina dla regionu platformy Azure, w którym rozpoczyna się automatyczne stosowanie poprawek.

> [!NOTE]
> Okno poprawek dla każdej maszyny wirtualnej jest rozkładane o jedną godzinę. W celu uniknięcia przerw w działaniu usług można zainstalować tylko jedną maszynę wirtualną.
>
>

![Ustawienia programu SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Przejrzyj ustawienia, a następnie kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie
Na stronie Podsumowanie platforma Azure weryfikuje ustawienia. Możesz również pobrać szablon. Przejrzyj podsumowanie. Kliknij przycisk **OK**.

### <a name="buy"></a>Kup
Ten końcowy blok zawiera **warunki użytkowania**i **zasady zachowania poufności informacji**. Przejrzyj te informacje. Gdy wszystko będzie gotowe do rozpoczęcia tworzenia maszyn wirtualnych i wszystkich innych wymaganych zasobów dla grupy dostępności, kliknij przycisk **Utwórz**.

Azure Portal tworzy grupę zasobów i wszystkie zasoby.

## <a name="monitor-deployment"></a>Monitorowanie wdrażania
Monitoruj postęp wdrażania w Azure Portal. Ikona reprezentująca wdrożenie jest automatycznie przypięta do pulpitu nawigacyjnego Azure Portal.

![Pulpit nawigacyjny platformy Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server
Nowe wystąpienia SQL Server są uruchomione na maszynach wirtualnych z adresami IP połączonymi z Internetem. Pulpit zdalny (RDP) można bezpośrednio do każdej SQL Serverj maszyny wirtualnej.

W celu uzyskania połączenia RDP z SQL Server wykonaj następujące kroki:

1. Na pulpicie nawigacyjnym Azure Portal Sprawdź, czy wdrożenie zakończyło się pomyślnie.
2. Kliknij pozycję **zasoby**.
3. W bloku **zasoby** kliknij pozycję **SqlServer-0**, która jest nazwą komputera z maszynami wirtualnymi, na których działa SQL Server.
4. W bloku dla programu **SqlServer-0**kliknij pozycję **Połącz**. Przeglądarka pyta, czy chcesz otworzyć lub zapisać obiekt połączenia zdalnego. Kliknij przycisk **Open** (Otwórz).
5. Podłączanie **pulpitu zdalnego** może ostrzec użytkownika o tym, że nie można zidentyfikować wydawcy tego połączenia zdalnego. Kliknij pozycję **Połącz**.
6. Zabezpieczenia systemu Windows monitują o wprowadzenie poświadczeń w celu nawiązania połączenia z adresem IP podstawowego kontrolera domeny. Kliknij pozycję **Użyj innego konta**. W obszarze **Nazwa użytkownika**wpisz **contoso\DomainAdmin**. To konto zostało skonfigurowane podczas ustawiania nazwy użytkownika administratora w szablonie. Użyj złożonego hasła, które zostało wybrane podczas konfigurowania szablonu.
7. **Pulpit zdalny** może ostrzec użytkownika o tym, że nie można uwierzytelnić komputera zdalnego ze względu na problemy z jego certyfikatem zabezpieczeń. Zostanie wyświetlona nazwa certyfikatu zabezpieczeń. Jeśli wykonano samouczek, nazwą jest **SqlServer-0.contoso.com**. Kliknij przycisk **tak**.

Nawiązano połączenie z protokołem RDP z maszyną wirtualną SQL Server. Możesz otworzyć SQL Server Management Studio, nawiązać połączenie z domyślnym wystąpieniem SQL Server i sprawdzić, czy grupa dostępności została skonfigurowana.
