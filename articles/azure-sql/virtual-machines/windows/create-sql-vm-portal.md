---
title: Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem Windows przy użyciu Azure Portal
description: W tym przewodniku omówiono opcje dostępne przy użyciu Azure Portal do aprowizacji SQL Server na maszynie wirtualnej z systemem Windows.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 347948734da439691bced01cf3693d0990567748
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146232"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Jak za pomocą Azure Portal zainicjować obsługę administracyjną maszyny wirtualnej z systemem Windows za pomocą SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym przewodniku omówiono opcje dostępne przy użyciu Azure Portal do aprowizacji SQL Server na maszynie wirtualnej z systemem Windows. W tym artykule opisano więcej opcji konfiguracji niż [SQL Server maszyny wirtualnej — szybki start](sql-vm-create-portal-quickstart.md), które koncentrują się na jednej konfiguracji. 

Użyj tego przewodnika, aby utworzyć własną SQL Server maszynę wirtualną. Lub użyj go jako odwołania do dostępnych opcji w Azure Portal.

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> Obrazy z galerii maszyn wirtualnych SQL Server

Podczas tworzenia maszyny wirtualnej SQL Server można wybrać jeden z kilku wstępnie skonfigurowanych obrazów z galerii maszyn wirtualnych. W poniższych krokach pokazano, jak wybrać jeden z SQL Server obrazów 2017.

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie Azure Portal. Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi** , a następnie wpisz *SQL Azure* w polu wyszukiwania. 

   Możesz również wybrać gwiazdkę obok **usługi Azure SQL** , aby zapisać ją jako ulubioną i dodać jako element do nawigacji po lewej stronie. 

1. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Dodatkowe informacje można wyświetlić, wybierając pozycję **Pokaż szczegóły** . 
1. Wpisz *2017* w polu wyszukiwania obrazu SQL Server na kafelku **maszyny wirtualne SQL** , a następnie wybierz pozycję **bezpłatna SQL Server Licencja: SQL Server 2017 deweloper w systemie Windows Server 2016** z listy rozwijanej. 

   ![Wybieranie obrazu maszyny wirtualnej SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > Wersja Developer jest używana w tym artykule, ponieważ jest to w pełni funkcjonalna, bezpłatna wersja SQL Server do testowania projektowania. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. Można jednak wybrać dowolny z obrazów do użycia w tym instruktażu. Opis dostępnych obrazów znajduje się w temacie [omówienie Windows Virtual Machines SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Koszty licencjonowania dla SQL Server są uwzględniane w cenach na sekundę utworzonej maszyny wirtualnej i różnią się w zależności od wersji i rdzeni. Jednak wersja SQL Server Developer jest bezpłatna do programowania i testowania, a nie do produkcji. Ponadto program SQL Express jest bezpłatny dla obciążeń lekkich (mniej niż 1 GB pamięci, mniej niż 10 GB magazynu). Możesz również przenieść własną licencję (BYOL) i zapłacił tylko za maszynę wirtualną. Nazwy tych obrazów mają prefiks {BYOL}. 
   >
   > Aby uzyskać więcej informacji na temat tych opcji, zobacz [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).


1. Wybierz pozycję **Utwórz** .


## <a name="1-configure-basic-settings"></a>1. Skonfiguruj ustawienia podstawowe

Na karcie **podstawowe** podaj następujące informacje:

* W obszarze **szczegóły projektu** upewnij się, że wybrano poprawną subskrypcję. 
* W sekcji **Grupa zasobów** wybierz istniejącą grupę zasobów z listy lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów. Grupa zasobów to kolekcja powiązanych zasobów platformy Azure (maszyny wirtualne, konta magazynu, sieci wirtualne itp.). 

  ![Subskrypcja](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Nowa grupa zasobów jest przydatna, jeśli tylko testujesz lub poznajesz wdrożenia programu SQL Server na platformie Azure. Po zakończeniu testu usuń grupę zasobów, aby automatycznie usunąć maszynę wirtualną i wszystkie skojarzone z nią zasoby. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Azure Resource Manager przegląd](../../../active-directory-b2c/overview.md).


* W obszarze **szczegóły wystąpienia** :

    1. Wprowadź unikatową **nazwę maszyny wirtualnej** .  
    1. Wybierz lokalizację dla **regionu** . 
    1. Na potrzeby tego przewodnika pozostaw opcję **dostępność** ustawioną na _niewymaganą nadmiarowość infrastruktury_ . Aby uzyskać więcej informacji na temat opcji dostępności, zobacz [dostępność](../../../virtual-machines/availability.md). 
    1. Na liście **obraz** wybierz opcję _bezpłatna SQL Server licencja: SQL Server 2017 Developer w systemie Windows Server 2016_ .  
    1. Wybierz opcję **zmiany rozmiaru** maszyny wirtualnej **Size** i wybierz **podstawową ofertę a2** . Pamiętaj, aby wyczyścić zasoby po wykonaniu tych czynności, aby zapobiec nieoczekiwanym opłatom. W przypadku obciążeń produkcyjnych zobacz zalecane rozmiary i konfiguracje maszyn w artykule [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](performance-guidelines-best-practices.md).

    ![Szczegóły wystąpienia](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> Szacowany koszt miesięczny wyświetlany w oknie **Wybieranie rozmiaru** nie uwzględnia kosztów licencjonowania programu SQL Server. To oszacowanie jest kosztem samej maszyny wirtualnej. W przypadku wersji Express i Developer SQL Server tym oszacowaniem jest całkowity szacowany koszt. W przypadku innych wersji zobacz [cennik maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i wybierz docelową wersję programu SQL Server. Zapoznaj się również ze [wskazówkami dotyczącymi cen dla SQL Server](pricing-guidance.md) maszyn [wirtualnych i rozmiarów](../../../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)platformy Azure.

* W obszarze **konto administratora** Podaj nazwę użytkownika i hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](./media/create-sql-vm-portal/basics-administrator-account.png)

* W obszarze **reguły portów ruchu przychodzącego** wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej. 

   ![Reguły portów wejściowych](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Skonfiguruj funkcje opcjonalne

### <a name="disks"></a>Dyski

Na karcie **dyski** Skonfiguruj opcje dysków. 

* W obszarze **typ dysku systemu operacyjnego** wybierz typ dysku, który ma być używany dla systemu operacyjnego, z listy rozwijanej. W systemach produkcyjnych zaleca się używanie wersji Premium, ale nie jest ona dostępna w przypadku podstawowej maszyny wirtualnej. Aby użyć SSD w warstwie Premium, Zmień rozmiar maszyny wirtualnej. 
* W obszarze **Zaawansowane** wybierz pozycję **tak** w obszarze Użyj **Managed disks** .

   > [!NOTE]
   > Firma Microsoft zaleca funkcję Dyski zarządzane dla programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Aby uzyskać szczegóły dotyczące dysków zarządzanych w zestawie dostępności, zobacz sekcję [Use managed disks for VMs in availability set](../../../virtual-machines/manage-availability.md) (Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności).

![Ustawienia dysku maszyny wirtualnej SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Sieć

Na karcie **Sieć** Skonfiguruj opcje sieci. 

* Utwórz nową **sieć wirtualną** lub Użyj istniejącej sieci wirtualnej dla maszyny wirtualnej SQL Server. Wyznacz również **podsieć** . 

* W obszarze **sieciowe grupy zabezpieczeń kart sieciowych** wybierz podstawową grupę zabezpieczeń lub zaawansowaną grupę zabezpieczeń. Wybranie opcji podstawowa umożliwia wybranie portów ruchu przychodzącego dla SQL Server maszyny wirtualnej, które są te same wartości skonfigurowanych na karcie **podstawowa** . Wybranie opcji Zaawansowane umożliwia wybranie istniejącej sieciowej grupy zabezpieczeń lub utworzenie nowej. 

* Możesz wprowadzić inne zmiany w ustawieniach sieci lub zachować wartości domyślne.

![Ustawienia sieci maszyny wirtualnej SQL](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorowanie

Na karcie **monitorowanie** Skonfiguruj monitorowanie i automatyczne zamykanie. 

* System Azure domyślnie umożliwia **diagnostykę rozruchu** przy użyciu tego samego konta magazynu wyznaczenie dla maszyny wirtualnej. Na tej karcie można zmienić te ustawienia i włączyć **diagnostykę gościa systemu operacyjnego** . 
* Na tej karcie można również włączyć **zarządzaną tożsamość przypisaną przez system** i **automatyczne zamykanie** . 

![Ustawienia zarządzania maszyną wirtualną SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Skonfiguruj ustawienia SQL Server

Na karcie **ustawienia SQL Server** Skonfiguruj określone ustawienia i optymalizacje dla SQL Server. Dla SQL Server można skonfigurować następujące ustawienia:

- [Połączenia](#connectivity)
- [Authentication](#authentication)
- [Integracja magazynu kluczy Azure](#azure-key-vault-integration)
- [Konfiguracja usługi Storage](#storage-configuration)
- [Automatyczne stosowanie poprawek](#automated-patching)
- [Automatyczna kopia zapasowa](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Łączność

W obszarze **Łączność z serwerem SQL** określ żądany typ dostępu do wystąpienia programu SQL Server na tej maszynie wirtualnej. Na potrzeby tego przewodnika wybierz opcję **Public (Internet)** , aby zezwolić na połączenia SQL Server z maszyn lub usług w Internecie. Po wybraniu tej opcji platforma Azure automatycznie skonfiguruje zaporę i grupę zabezpieczeń sieci w taki sposób, aby zezwalały na ruch na wybranym porcie.

> [!TIP]
> Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie **1433** . Aby zwiększyć bezpieczeństwo, zmień port w poprzednim oknie dialogowym do nasłuchiwania na porcie innym niż domyślny, takim jak 1401. W przypadku zmiany portu należy nawiązać połączenie przy użyciu tego portu z dowolnego narzędzia klienckiego, takiego jak SQL Server Management Studio (SSMS).

![Zabezpieczenia maszyny wirtualnej SQL](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Aby ustanowić połączenie z programem SQL Server za pośrednictwem Internetu, musisz również włączyć funkcję Uwierzytelnianie programu SQL Server opisaną w następnej sekcji.

Jeśli nie chcesz zezwolić na połączenia z aparatem bazy danych za pośrednictwem Internetu, wybierz jedną z następujących opcji:

* **Lokalne (tylko wewnątrz maszyny wirtualnej)** , aby zezwolić na połączenia z programem SQL Server tylko z maszyny wirtualnej.
* **Prywatne (wewnątrz usługi Virtual Network)** , aby zezwolić na połączenia z programem SQL Server tylko z maszyn wirtualnych lub usług w tej samej sieci wirtualnej.

Ogólnie rzecz biorąc, można ulepszyć zabezpieczenia, wybierając łączność z najbardziej restrykcyjnymi ograniczeniami akceptowanymi w danym scenariuszu. Wszystkie opcje są zabezpieczane za pomocą reguł grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń) i uwierzytelniania SQL/systemu Windows. Po utworzeniu maszyny wirtualnej można edytować sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

### <a name="authentication"></a>Authentication

Jeśli wymagane jest uwierzytelnianie SQL Server, wybierz pozycję **Włącz** w obszarze **uwierzytelnianie SQL** na karcie **Ustawienia SQL Server** .

![Uwierzytelnianie programu SQL Server](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Jeśli planujesz uzyskać dostęp do SQL Server za pośrednictwem Internetu (opcja łączności publicznej), musisz w tym miejscu włączyć uwierzytelnianie SQL. Publiczny dostęp do SQL Server wymaga uwierzytelniania SQL.

Jeśli włączasz opcję uwierzytelniania programu SQL Server, podaj informacje w polach **Nazwa logowania** i **Hasło** . Ta nazwa logowania jest konfigurowana jako SQL Server Identyfikator logowania uwierzytelniania i członek stałej roli serwera **sysadmin** . Aby uzyskać więcej informacji na temat trybów uwierzytelniana, zobacz artykuł [Choose an Authentication Mode](/sql/relational-databases/security/choose-an-authentication-mode) (Wybieranie trybu uwierzytelniania).

Jeśli wolisz nie włączać uwierzytelniania SQL Server, możesz użyć konta administratora lokalnego na maszynie wirtualnej, aby nawiązać połączenie z wystąpieniem SQL Server.

### <a name="azure-key-vault-integration"></a>Integracja magazynu kluczy Azure

Aby przechowywać wpisy tajne zabezpieczeń na platformie Azure na potrzeby szyfrowania, wybierz pozycję **ustawienia SQL Server** i przewiń w dół do opcji  **integracja z magazynem kluczy Azure** . Wybierz pozycję **Włącz** i wypełnij żądane informacje. 

![Integracja magazynu kluczy Azure](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

Poniższa tabela zawiera listę parametrów wymaganych do skonfigurowania integracji z usługą Azure Key Vault (AKV).

| PARAMETR | OPIS | PRZYKŁAD |
| --- | --- | --- |
| **Adres URL magazynu kluczy** |Lokalizacja magazynu kluczy. |`https://contosokeyvault.vault.azure.net/` |
| **Nazwa główna** |Nazwa główna usługi Azure Active Directory. Ta nazwa jest również nazywana identyfikatorem klienta. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Główny klucz tajny** |Główny klucz tajny usługi Azure Active Directory. Ten klucz tajny jest również nazywany kluczem tajnym klienta. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Nazwa poświadczenia** |**Nazwa poświadczenia** : integracja z usługą AKV tworzy poświadczenie w ramach SQL Server i umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |`mycred1` |

Aby uzyskać więcej informacji, zobacz [Configure Azure Key Vault Integration for SQL Server on Azure VMs](azure-key-vault-integration-configure.md) (Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych Azure).

### <a name="storage-configuration"></a>Konfiguracja usługi Storage

Na karcie **ustawienia SQL Server** w obszarze **Konfiguracja magazynu** wybierz pozycję **Zmień konfigurację** , aby otworzyć stronę Konfiguracja magazynu zoptymalizowanego pod kątem wydajności i określić wymagania dotyczące magazynu.

![Zrzut ekranu, który pokazuje, gdzie można zmienić konfigurację magazynu.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

W obszarze **Usługa Storage zoptymalizowana dla** wybierz jedną z następujących opcji:

* **Ogólne** to ustawienie domyślne obsługujące większość obciążeń.
* **Przetwarzanie transakcyjne** optymalizuje magazyn dla tradycyjnych obciążeń bazy danych OLTP.
* **Magazynowanie danych** optymalizuje magazyn dla tradycyjnych obciążeń związanych z analizą i raportami.

![Konfiguracja magazynu maszyny wirtualnej SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Możesz pozostawić wartości domyślne lub ręcznie zmienić topologię magazynu, aby odpowiadała potrzebom IOPS. Aby uzyskać więcej informacji, zobacz [Konfiguracja magazynu](storage-configuration.md). 

### <a name="sql-server-license"></a>Licencja SQL Server

Jeśli jesteś klientem programu Software Assurance, możesz użyć [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) , aby zapewnić własne licencje SQL Server i zaoszczędzić na zasobach. 

![Licencja maszyny wirtualnej SQL](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatyczne stosowanie poprawek

Opcja **Automatyczne stosowanie poprawek** jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Określ dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi używa ustawień regionalnych maszyny wirtualnej. Jeśli nie chcesz, aby platforma Azure automatycznie dołączyła SQL Server i system operacyjny, wybierz pozycję **Wyłącz** .  

![Zautomatyzowane stosowanie poprawek maszyny wirtualnej SQL](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Aby uzyskać więcej informacji, zobacz [Automated Patching for SQL Server in Azure Virtual Machines](automated-patching.md) (Automatyczne stosowanie poprawek programu SQL Server w usłudze Azure Virtual Machines).

### <a name="automated-backup"></a>Automatyczne kopie zapasowe

Automatyczną obsługę kopii zapasowych baz danych możesz włączyć dla wszystkich baz danych w obszarze **Automatyczne tworzenie kopii zapasowych** . Opcja Automatyczne kopie zapasowe jest domyślnie wyłączona.

Po włączeniu automatycznej obsługi kopii zapasowych SQL możesz skonfigurować poniższe ustawienia:

* Okres przechowywania (dni) kopii zapasowych
* Konto usługi Storage używane dla kopii zapasowych
* Opcja szyfrowania i hasło dla kopii zapasowych
* Bazy danych systemu tworzenia kopii zapasowych
* Konfigurowanie harmonogramu tworzenia kopii zapasowych

Aby zaszyfrować kopię zapasową, wybierz pozycję **Włącz** . Następnie określ ustawienie **Hasło** . Platforma Azure tworzy certyfikat do szyfrowania kopii zapasowych i używa określonego hasła do ochrony tego certyfikatu. Domyślnie harmonogram jest ustawiany automatycznie, ale można utworzyć harmonogram ręczny, wybierając pozycję **Ręczne** . 

![Automatyczne kopie zapasowe maszyny wirtualnej SQL](./media/create-sql-vm-portal/automated-backup.png)

Aby uzyskać więcej informacji, zobacz [Automated Backup for SQL Server in Azure Virtual Machines](automated-backup-sql-2014.md) (Automatyczne tworzenie kopii zapasowych dla programu SQL Server w usłudze Azure Virtual Machines).


### <a name="machine-learning-services"></a>Machine Learning Services

Możesz włączyć [Machine Learning Services](/sql/advanced-analytics/). Ta opcja umożliwia korzystanie z uczenia maszynowego z językami Python i R w SQL Server 2017. Wybierz pozycję **Włącz** w oknie **Ustawienia SQL Server** .


## <a name="4-review--create"></a>4. Przejrzyj i Utwórz

Na karcie **Recenzja + tworzenie** :
1. Przejrzyj podsumowanie.
1. Wybierz pozycję **Utwórz** , aby utworzyć SQL Server, grupę zasobów i zasoby określone dla tej maszyny wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia.

> [!NOTE]
> Przykład czasu na platformę Azure w celu wdrożenia maszyny wirtualnej SQL Server: test SQL Server maszyna wirtualna, która została zainicjowana do regionu Wschodnie stany USA z ustawieniami domyślnymi, trwa około 12 minut. Czas wdrożenia może być krótszy lub wolniejszy w zależności od regionu i wybranych ustawień.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Otwórz maszynę wirtualną przy użyciu Pulpit zdalny

Aby nawiązać połączenie z maszyną wirtualną SQL Server przy użyciu Remote Desktop Protocol (RDP), wykonaj następujące czynności:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Zdalne nawiązywanie połączenia z usługą SQL Server

W tym instruktażu wybrano opcję dostęp **publiczny** dla maszyny wirtualnej i **uwierzytelnianie SQL Server** . Te ustawienia powodują automatyczne skonfigurowanie maszyny wirtualnej do zezwalania na połączenia z programem SQL Server z dowolnego klienta za pośrednictwem Internetu (zakładając, że ma on poprawny identyfikator logowania SQL).

> [!NOTE]
> Jeśli podczas aprowizacji nie wybrano opcji Publiczne, możesz zmienić ustawienia łączności SQL za pośrednictwem portalu po aprowizacji. Aby uzyskać więcej informacji, zobacz [Zmiana ustawień łączności SQL](ways-to-connect-to-sql.md#change).

W poniższych sekcjach pokazano, jak nawiązać połączenie za pośrednictwem Internetu z wystąpieniem maszyny wirtualnej SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > W tym przykładzie zastosowano typowy port 1433. Należy jednak zmodyfikować tę wartość, jeśli podczas wdrażania SQL Server maszyny wirtualnej określono inny port (na przykład 1401). 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać inne informacje na temat użycia programu SQL Server na platformie Azure, zobacz [SQL Server on Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) (Program SQL Server w usłudze Azure Virtual Machines) i [Frequently Asked Questions](frequently-asked-questions-faq.md) (Często zadawane pytania).