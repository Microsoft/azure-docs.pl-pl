---
title: Urządzenie usługi Azure Migrate
description: Zawiera podsumowanie obsługi urządzenia Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 08cd0e9d33dd88b9bdc418f3d1bbd382b2d80632
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038768"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagania dotyczące obsługi urządzenia Azure Migrate.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Używane do**
--- | --- | ---
**Odnajdywanie i ocenianie serwerów działających w środowisku VMware** | Azure Migrate: Ocena serwera | Odnajdź serwery działające w środowisku programu VMware<br/><br/> Wykonaj odnajdywanie zainstalowanych aplikacji, analizy zależności bez agenta i odnajdź SQL Server wystąpienia i bazy danych.<br/><br/> Zbieranie informacji o konfiguracji serwera i metadanych wydajności dla ocen.
**Migracja serwerów z systemem w środowisku VMware bez wykorzystania agentów** | Azure Migrate: Migracja serwera | Odnajdź serwery działające w środowisku programu VMware. <br/><br/> Replikowanie serwerów bez instalowania na nich agentów.
**Odnajdywanie i ocenianie serwerów działających w środowisku funkcji Hyper-V** | Azure Migrate: Ocena serwera | Odnajdź serwery działające w środowisku funkcji Hyper-V.<br/><br/> Zbieranie informacji o konfiguracji serwera i metadanych wydajności dla ocen.
**Odnajdywanie i ocenianie serwerów fizycznych lub zwirtualizowanych lokalnie** |  Azure Migrate: Ocena serwera |  Odnajdywanie serwerów fizycznych lub zwirtualizowanych lokalnie.<br/><br/> Zbieranie informacji o konfiguracji serwera i metadanych wydajności dla ocen.

## <a name="deployment-methods"></a>Metody wdrażania

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla serwerów działających w środowisku VMware lub funkcji Hyper-V ([szablon komórki jajowe dla oprogramowania VMware](how-to-set-up-appliance-vmware.md) lub [wirtualnego dysku twardego dla funkcji Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla środowiska VMware lub funkcji Hyper-V przy użyciu [skryptu Instalatora programu PowerShell](deploy-appliance-script.md).
- W Azure Government należy wdrożyć urządzenie przy użyciu skryptu Instalatora programu PowerShell. W [tym miejscu](deploy-appliance-script-government.md)zapoznaj się z krokami wdrożenia.
- W przypadku serwerów fizycznych lub zwirtualizowanych lokalnie lub w innych chmurach urządzenie jest zawsze wdrażane przy użyciu skryptu Instalatora programu PowerShell. W [tym miejscu](how-to-set-up-appliance-physical.md)zapoznaj się z krokami wdrożenia.
- Linki do pobrania są dostępne w poniższych tabelach.


## <a name="appliance---vmware"></a>Urządzenie — VMware

Poniższa tabela zawiera podsumowanie wymagań dotyczących urządzeń Azure Migrate dla programu VMware.

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

**Wymaganie** | **VMware** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do programu Configuration Manager lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia**: jest to aplikacja sieci Web, która może być skonfigurowana ze szczegółowymi informacjami na temat uruchamiania odnajdywania i oceny serwerów.<br/> - **Agent odnajdywania VMware**: Agent zbiera metadane konfiguracji serwera, których można użyć do utworzenia jako oceny lokalne.<br/>- **Agent oceny VMware**: Agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa autoaktualizacji**: usługa utrzymuje wszystkie agenci uruchomione na urządzeniu. Jest ona automatycznie uruchamiana co 24 godziny.<br/>- **Agent dra**: organizuje replikację serwera i koordynuje komunikację między replikowanymi serwerami i platformą Azure. Używane tylko w przypadku replikowania serwerów na platformę Azure przy użyciu migracji bez wykorzystania agentów.<br/>- **Brama**: wysyła zreplikowane dane na platformę Azure. Używane tylko w przypadku replikowania serwerów na platformę Azure przy użyciu migracji bez wykorzystania agentów.<br/>- **Agent odnajdywania i oceny SQL**: wysyła metadane konfiguracji i wydajności SQL Server wystąpień i baz danych na platformę Azure.
**Limity projektu** |  Urządzenie może być zarejestrowane tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.
**Limity odnajdywania** | Urządzenie może wykryć do 10 000 serwerów uruchomionych na vCenter Server.<br/> Urządzenie może połączyć się z pojedynczym vCenter Server.
**Obsługiwane wdrożenie** | Wdróż jako nowy serwer działający na vCenter Server przy użyciu szablonu komórki jajowe.<br/><br/> Wdróż program na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu Instalatora programu PowerShell.
**Szablon komórki jajowe** | Pobierz z projektu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Rozmiar pobieranych plików to 11,9 GB.<br/><br/> Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/>Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia przy użyciu szablonu komórki jajowe lub Aktywowanie licencji systemu operacyjnego serwera urządzeń.
**Weryfikacja komórek jajowych** | [Sprawdź, czy](tutorial-discover-vmware.md#verify-security) szablon komórki jajowe został pobrany z projektu, sprawdzając wartości skrótu.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) dotyczącym sposobu wdrażania urządzenia za pomocą skryptu Instalatora programu PowerShell.<br/><br/> 
**Wymagania dotyczące sprzętu i sieci** |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> W przypadku wdrożenia urządzenia przy użyciu szablonu komórki jajowe należy uzyskać wystarczającą ilość zasobów na vCenter Server, aby utworzyć serwer, który spełnia wymagania sprzętowe.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._
**Wymagania dotyczące oprogramowania VMware** | Jeśli urządzenie zostanie wdrożone jako serwer na vCenter Server, musi zostać wdrożone na vCenter Server z systemem 5,5, 6,0, 6,5 lub 6,7 oraz hostem ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> 
**VDDK (migracja bez wykorzystania agentów)** | Aby można było korzystać z urządzenia w celu migracji serwerów bez wykorzystania agentów, na serwerze urządzenia musi być zainstalowany VMware vSphere VDDK.

## <a name="appliance---hyper-v"></a>Urządzenie-Hyper-V

**Wymaganie** | **Hyper-V** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do programu Configuration Manager lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia**: jest to aplikacja sieci Web, która może być skonfigurowana ze szczegółowymi informacjami na temat uruchamiania odnajdywania i oceny serwerów.<br/> - **Agent odnajdywania**: Agent zbiera metadane konfiguracji serwera, których można użyć do utworzenia jako oceny lokalne.<br/>- **Agent oceny**: Agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa autoaktualizacji**: usługa utrzymuje wszystkie agenci uruchomione na urządzeniu. Jest ona automatycznie uruchamiana co 24 godziny.
**Limity projektu** |  Urządzenie może być zarejestrowane tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.
**Limity odnajdywania** | Urządzenie może wykryć do 5000 serwerów działających w środowisku funkcji Hyper-V.<br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Obsługiwane wdrożenie** | Wdróż program jako serwer uruchomiony na hoście funkcji Hyper-V przy użyciu szablonu wirtualnego dysku twardego.<br/><br/> Wdróż program na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu Instalatora programu PowerShell.
**Szablon wirtualnego dysku twardego** | Plik zip zawierający dysk VHD. Pobierz z projektu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Rozmiar pobieranych plików to 8,91 GB.<br/><br/> Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/> Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji systemu operacyjnego serwera urządzeń.
**Weryfikacja wirtualnego dysku twardego** | [Sprawdź, czy](tutorial-discover-hyper-v.md#verify-security) szablon wirtualnego dysku twardego został pobrany z projektu, sprawdzając wartości skrótu.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) dotyczącym sposobu wdrażania urządzenia za pomocą skryptu Instalatora programu PowerShell.<br/>
**Wymagania dotyczące sprzętu i sieci**  |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP i wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> Jeśli urządzenie zostanie uruchomione jako serwer działający na hoście funkcji Hyper-V, do utworzenia serwera, który spełnia wymagania sprzętowe, potrzeba wystarczającej ilości zasobów na hoście.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._
**Wymagania dotyczące funkcji Hyper-V** | W przypadku wdrożenia urządzenia z szablonem dysku VHD urządzenie udostępnione przez Azure Migrate jest maszyną wirtualną funkcji Hyper-V w wersji 5,0.<br/><br/> Na hoście funkcji Hyper-V musi być uruchomiony system Windows Server 2012 R2 lub nowszy.

## <a name="appliance---physical"></a>Urządzenie — fizyczne

**Wymaganie** | **Fizyczny** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do programu Configuration Manager lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia**: jest to aplikacja sieci Web, która może być skonfigurowana ze szczegółowymi informacjami na temat uruchamiania odnajdywania i oceny serwerów.<br/> - **Agent odnajdywania**: Agent zbiera metadane konfiguracji serwera, których można użyć do utworzenia jako oceny lokalne.<br/>- **Agent oceny**: Agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa autoaktualizacji**: usługa utrzymuje wszystkie agenci uruchomione na urządzeniu. Jest ona automatycznie uruchamiana co 24 godziny.
**Limity projektu** |  Urządzenie może być zarejestrowane tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 1000 serwerów fizycznych.
**Obsługiwane wdrożenie** | Wdróż program na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu Instalatora programu PowerShell.
**Skrypt programu PowerShell** | Pobierz skrypt (AzureMigrateInstaller.ps1) w pliku zip z projektu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140334). [Dowiedz się więcej](tutorial-discover-physical.md).<br/><br/> Rozmiar pobieranych plików to 85,8 MB.
**Weryfikacja skryptu** | [Sprawdź, czy](tutorial-discover-physical.md#verify-security) skrypt Instalatora programu PowerShell został pobrany z projektu, sprawdzając wartości skrótu.
**Wymagania dotyczące sprzętu i sieci** |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP i wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._


## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie Azure Migrate wymaga połączenia z Internetem.

- Po wdrożeniu urządzenia Azure Migrate sprawdza łączność z wymaganymi adresami URL.
- Musisz zezwolić na dostęp do wszystkich adresów URL na liście. Jeśli wykonujesz tylko ocenę, możesz pominąć adresy URL, które są oznaczone jako wymagane dla migracji bez agenta programu VMware.
-  Jeśli używasz serwera proxy opartego na adresie URL do łączenia się z Internetem, upewnij się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

### <a name="public-cloud-urls"></a>Adresy URL chmury publicznej

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do witryny Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Utwórz aplikacje Azure Active Directory (AD) dla urządzenia, aby komunikować się z Azure Migrate.
management.azure.com | Utwórz aplikacje usługi Azure AD dla urządzenia, aby komunikować się z Azure Migrate.
*.services.visualstudio.com | Przekazywanie dzienników urządzeń używanych do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.<br/> Uwaga: Upewnij się, że serwery do replikacji mają dostęp do tego obiektu.
aka.ms/* | Zezwalanie na dostęp do aliasów linków; służy do pobierania i instalowania najnowszych aktualizacji dla usług urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z centrum pobierania firmy Microsoft.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Używane do migracji bez agentów programu VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net |  **Używane do migracji bez agentów programu VMware**<br/><br/>Przekaż dane do magazynu na potrzeby migracji.

### <a name="government-cloud-urls"></a>Adresy URL w chmurze dla instytucji rządowych

**Adres URL** | **Szczegóły**  
--- | --- |
*. portal.azure.us  | Przejdź do witryny Azure Portal.
graph.windows.net | Zaloguj się do subskrypcji platformy Azure.
login.microsoftonline.us  | Utwórz aplikacje Azure Active Directory (AD) dla urządzenia, aby komunikować się z Azure Migrate.
management.usgovcloudapi.net | Utwórz aplikacje usługi Azure AD dla urządzenia, aby komunikować się z usługą Azure Migrate.
*.services.visualstudio.com | Przekazywanie dzienników urządzeń używanych do wewnętrznego monitorowania.
*. vault.usgovcloudapi.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
aka.ms/* | Zezwalanie na dostęp do aliasów linków; służy do pobierania i instalowania najnowszych aktualizacji dla usług urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z centrum pobierania firmy Microsoft.
*. servicebus.usgovcloudapi.net  | Komunikacja między urządzeniem a usługą Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Połącz się z adresami URL usługi Azure Migrate.
*. hypervrecoverymanager.windowsazure.us | **Używane do migracji bez agentów programu VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*. blob.core.usgovcloudapi.net  |  **Używane do migracji bez agentów programu VMware**<br/><br/>Przekaż dane do magazynu na potrzeby migracji.
*. applicationinsights.us | Przekazywanie dzienników urządzeń używanych do wewnętrznego monitorowania.


## <a name="collected-data---vmware"></a>Zebrane dane — VMware

Urządzenie zbiera dane dotyczące metadanych konfiguracji, metadanych wydajności i zależności serwera (jeśli jest używana [analiza zależności](concepts-dependency-visualization.md) bez wykorzystania agentów).

### <a name="metadata"></a>Metadane

Metadane wykryte przez urządzenie Azure Migrate ułatwiają ustalenie, czy serwery są gotowe do migracji na platformę Azure, serwery o odpowiednim rozmiarze, plany kosztów i analizować zależności aplikacji. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych serwera zbieranych i wysyłanych przez urządzenie do platformy Azure.

**DATA** | **PRZECIW**
--- | --- 
**Szczegóły serwera** | 
Identyfikator serwera | vm.Config. InstanceUuid 
Nazwa serwera | vm.Config. Nazwij
Identyfikator vCenter Server | VMwareClient. Instance. UUID
Opis maszyny wirtualnej | vm.Summary.Config. Wskazani
Nazwa produktu licencji | VM. Client. servicecontent. informacje. LicenseProductName
Typ systemu operacyjnego | VM. SummaryConfig. GuestFullName
Typ rozruchu | vm.Config. Wbudowane
Liczba rdzeni | vm.Config. Sprzęt. NumCPU
Pamięć (MB) | vm.Config. Sprzęt. MemoryMB
Liczba dysków | vm.Config. Sprzęt. Device. ToList — (). FindAll (x => to VirtualDisk). Count
Lista rozmiarów dysku | vm.Config. Sprzęt. Device. ToList — (). FindAll (x => jest VirtualDisk)
Lista kart sieciowych | vm.Config. Sprzęt. Device. ToList — (). FindAll (x => to VirtualEthernet). Count
Wykorzystanie procesora | CPU. Usage. Average
Użycie pamięci |MEM. Usage. Average
**Szczegóły dysku** | 
Wartość klucza dysku | 3,5. Głównych
Numer Dikunit | 3,5. UnitNumber
Wartość klucza kontrolera dysku | 3,5. ControllerKey. Value
Gigabajty inicjowane | virtualDisk. DeviceInfo. Summary
Nazwa dysku | Wartość wygenerowana przy użyciu dysku. UnitNumber, dysk. Klucz, dysk. ControllerKey. VAlue
Operacje odczytu na sekundę | virtualDisk. numberReadAveraged. Average
Operacje zapisu na sekundę | virtualDisk. numberWriteAveraged. Average
Przepływność odczytu (MB na sekundę) | virtualDisk. Read. Average
Przepływność zapisu (MB na sekundę) | virtualDisk. Write. Average
**Na szczegóły karty sieciowej** | 
Nazwa karty sieciowej | 10/100/1000. Głównych
Adres MAC | (Karta sieciowa (VirtualEthernetCard)). MacAddress
Adresy IPv4 | vm.Guest.Net
Adresy IPv6 | vm.Guest.Net
Przepływność odczytu (MB na sekundę) | NET. Receive. Average
Przepływność zapisu (MB na sekundę) | NET. reprzesłane. średnia
**Szczegóły ścieżki spisu** | 
Nazwa | wbudowane. GetType (). Nazwij
Typ obiektu podrzędnego | wbudowane. Typ podrzędny
Szczegóły odwołania | wbudowane. MoRef
Szczegóły nadrzędne | Kontener. Parent
Szczegóły folderu na maszynę wirtualną | (Folder). ChildEntity. Type
Szczegóły centrum danych na maszynę wirtualną | ((Centrum danych) kontener). VmFolder
Szczegóły centrum danych na folder hosta | ((Centrum danych) kontener). HostFolder
Szczegóły klastra na hosta | ((ClusterComputeResource) kontener). Host
Szczegóły hosta na maszynę wirtualną | ((HostSystem) kontener). MASZYN

### <a name="performance-data"></a>Dane wydajności


Oto dane wydajności maszyny wirtualnej VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Licznik** | **Wpływ oceny**
--- | --- | ---
Wykorzystanie procesora | CPU. Usage. Average | Zalecany rozmiar maszyny wirtualnej/koszt
Użycie pamięci | MEM. Usage. Average | Zalecany rozmiar maszyny wirtualnej/koszt
Przepływność odczytu dysku (MB na sekundę) | virtualDisk. Read. Average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność zapisu na dysku (MB na sekundę) | virtualDisk. Write. Average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje odczytu z dysku na sekundę | virtualDisk. numberReadAveraged. Average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Operacje zapisu na dysku na sekundę | virtualDisk. numberWriteAveraged. Average  | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Przepływność odczytu karty sieciowej (MB na sekundę) | NET. Receive. Average | Obliczanie rozmiaru maszyny wirtualnej
Przepływność zapisu kart sieciowych (MB na sekundę) | NET. reprzesłane. średnia  |Obliczanie rozmiaru maszyny wirtualnej


### <a name="installed-applications-data"></a>Dane zainstalowanych aplikacji

Urządzenie zbiera dane dotyczące zainstalowanych aplikacji, ról i funkcji na serwerach.

#### <a name="windows-server-application-data"></a>Dane aplikacji systemu Windows Server

Oto dane aplikacji, które urządzenie zbiera z każdego serwera z systemem Windows odnalezionego w środowisku VMware.

**Dane** | **Lokalizacja rejestru** | **Klucz**
--- | --- | ---
Nazwa aplikacji  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Nazwa wyświetlana
Wersja  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Dostawca  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Dane funkcji systemu Windows Server

Poniżej przedstawiono informacje o funkcjach zbieranych przez urządzenie z każdego serwera z systemem Windows, który został odnaleziony w środowisku programu VMware.

**Dane**  | **Polecenie cmdlet programu PowerShell** | **Właściwość**
--- | --- | ---
Nazwa  | Get-WindowsFeature  | Nazwa
Typ funkcji | Get-WindowsFeature  | Element featuretype
Nadrzędny  | Get-WindowsFeature  | Nadrzędny

#### <a name="sql-server-metadata"></a>Metadane SQL Server

Poniżej przedstawiono SQL Server dane zbierane przez urządzenie z każdego serwera z systemem Windows odnalezionego w środowisku programu VMware.

**Dane**  | **Lokalizacja rejestru**  | **Klucz**
--- | --- | ---
Nazwa  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Wersja  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Wersja 
Dodatek Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | REQUIREMENT
Wersja  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Wersja 

#### <a name="windows-server-operating-system-data"></a>Dane systemu operacyjnego Windows Server

Oto dane systemu operacyjnego zbierane przez urządzenie z każdego serwera z systemem Windows odnalezionego w środowisku programu VMware.

**Dane**  | **Klasa WMI**  | **Właściwość klasy usługi WMI**
--- | --- | ---
Nazwa  | Win32_operatingsystem  | Caption
Wersja  | Win32_operatingsystem  | Wersja
Architektura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-application-data"></a>Dane aplikacji serwera z systemem Linux

Oto dane aplikacji, które urządzenie zbiera z każdego serwera z systemem Linux wykrytego w środowisku VMware. W oparciu o system operacyjny serwera, uruchamiane jest jedno lub więcej poleceń.

**Dane**  | **Polecenia**
--- | --- 
Nazwa | rpm, serwerach dpkg-Query, Snap
Wersja | rpm, serwerach dpkg-Query, Snap
Dostawca | rpm, serwerach dpkg-Query, Snap

#### <a name="linux-server-operating-system-data"></a>Dane systemu operacyjnego serwera z systemem Linux

Oto dane systemu operacyjnego zbierane przez urządzenie z każdego serwera z systemem Linux wykrytego w środowisku programu VMware.

**Dane**  | **Polecenia**
--- | --- 
Nazwa <br/> Wersja | Zebrane z co najmniej jednego z następujących plików:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architektura | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server dane wystąpień i baz danych

Urządzenie zbiera dane na SQL Server wystąpieniach i bazach danych.

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

#### <a name="sql-database-metadata"></a>Metadane bazy danych SQL

**Metadane bazy danych** | **Widoki/SQL Server właściwości**
--- | ---
Unikatowy identyfikator bazy danych | sys.databases
Identyfikator bazy danych zdefiniowanej przez serwer | sys.databases
Nazwa bazy danych | sys.databases
Poziom zgodności bazy danych | sys.databases
Nazwa sortowania bazy danych | sys.databases
Stan bazy danych | sys.databases
Rozmiar bazy danych (w MB) | sys.master_files
Litera dysku zawierającego pliki danych | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Lista plików bazy danych | sys. databases, sys.master_files
Broker usług jest włączony lub nie | sys.databases
W bazie danych włączono obsługę funkcji przechwytywania zmian danych. | sys.databases

#### <a name="sql-server-metadata"></a>Metadane SQL Server

**Metadane serwera** | **Widoki/właściwości serwera SQL**
--- | ---
Nazwa serwera |SERVERPROPERTY 
Nazwa FQDN | Parametry połączenia pochodzące z odnajdywania zainstalowanych aplikacji
Identyfikator instalacji | sys.dm_server_registry
Wersja serwera | SERVERPROPERTY
Wersja serwera | SERVERPROPERTY
Platforma hosta serwera (Windows/Linux) | SERVERPROPERTY
Poziom produktu serwera (RTM SP CTP) | SERVERPROPERTY 
Domyślna ścieżka kopii zapasowej | SERVERPROPERTY
Ścieżka domyślna plików danych | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Domyślna ścieżka plików dziennika | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Nie. rdzeni na serwerze | sys.dm_os_schedulers, sys.dm_os_sys_info
Nazwa sortowania serwera | SERVERPROPERTY
Nie. rdzeni na serwerze z WIDOCZNYm stanem ONLINE | sys.dm_os_schedulers
Unikatowy identyfikator serwera | sys.dm_server_registry
HA włączona lub nie | SERVERPROPERTY
Rozszerzenie puli buforów jest włączone lub nie | sys.dm_os_buffer_pool_extension_configuration
Klaster trybu failover został skonfigurowany | SERVERPROPERTY
Tylko serwer z trybem uwierzytelniania systemu Windows | SERVERPROPERTY 
Serwer jest instalowany z bazą | SERVERPROPERTY 
Nie. logicznych procesorów CPU w systemie | sys.dm_server_registry, sys.dm_os_sys_info
Stosunek liczby rdzeni logicznych lub fizycznych, które są udostępniane przez jeden fizyczny pakiet procesora | sys.dm_os_schedulers, sys.dm_os_sys_info
Brak fizycznych procesorów CPU w systemie | sys.dm_os_schedulers, sys.dm_os_sys_info
Data i godzina ostatniego uruchomienia serwera | sys.dm_server_registry
Maksymalne użycie pamięci serwera (w MB) | sys.dm_os_process_memory
Suma nr użytkowników we wszystkich bazach danych | sys. databases, sys. Logins
Łączny rozmiar wszystkich baz danych użytkownika | sys.databases
Rozmiar tymczasowej bazy danych | sys.master_files, sys.configurations, sys.dm_os_sys_info
Nie. logowań | sys. Logins
Lista połączonych serwerów | sys. serwery
Lista zadań agenta | [msdb]. [dbo]. [tabela sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadane wydajności

**Wydajność** | **Widoki/właściwości serwera SQL** | **Wpływ oceny**
--- | --- | ---
Użycie procesora SQL Server| sys.dm_os_ring_buffers| Zalecany rozmiar jednostki SKU (wymiar procesora)
Liczba logicznych procesorów CPU SQL| sys.dm_os_sys_info| Zalecany rozmiar jednostki SKU (wymiar procesora)
Używana pamięć fizyczna SQL| sys.dm_os_process_memory| Nieużywane
Procent użycia pamięci SQL| sys.dm_os_process_memory | Nieużywane
Użycie procesora CPU bazy danych| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Zalecany rozmiar jednostki SKU (wymiar procesora)
Używana pamięć bazy danych (pula buforów)| sys.dm_os_buffer_descriptors| Zalecany rozmiar jednostki SKU (wymiar pamięci)
Operacje we/wy odczytu/zapisu pliku| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar jednostki SKU (wymiar we/wy)
Liczba plików odczytu/zapisu| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar jednostki SKU (wymiar przepływności)
Operacje we/wy pliku w trybie odczytu/zapisu (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar jednostki SKU (wymiar opóźnienia we/wy)
Rozmiar pliku| sys.master_files| Zalecany rozmiar jednostki SKU (wymiar magazynu)


### <a name="application-dependency-data"></a>Dane zależności aplikacji

Analiza zależności bez agenta zbiera dane dotyczące połączenia i przetwarzania.

#### <a name="windows-server-dependencies-data"></a>Dane zależności systemu Windows Server

Poniżej przedstawiono dane połączenia zbierane przez urządzenie z każdego systemu Windows Server, które obsługują analizę zależności bez agentów.

**Dane** | **Polecenia** 
--- | --- 
Port lokalny | netstat
Lokalny adres IP | netstat
Port zdalny | netstat
Zdalny adres IP | netstat
Stan połączenia TCP | netstat
Identyfikator procesu | netstat
Liczba aktywnych połączeń | netstat

Poniżej przedstawiono dane połączenia zbierane przez urządzenie z każdego systemu Windows Server, które obsługują analizę zależności bez agentów.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
Nazwa procesu | Win32_Process | Ścieżka pliku wykonywalnego
Argumenty procesu | Win32_Process | CommandLine
Nazwa aplikacji | Win32_Process | VersionInfo. ProductName — parametr właściwości ścieżka pliku wykonywalnego

#### <a name="linux-server-dependencies-data"></a>Dane zależności serwera z systemem Linux

Oto dane połączenia, które urządzenie zbiera z każdego serwera z systemem Linux, z włączoną obsługą analizy zależności bez agenta.

**Dane** | **Polecenia** 
--- | ---
Port lokalny | netstat 
Lokalny adres IP | netstat 
Port zdalny | netstat 
Zdalny adres IP | netstat 
Stan połączenia TCP | netstat 
Liczba aktywnych połączeń | netstat
Identyfikator procesu  | netstat 
Nazwa procesu | iloczyn
Argumenty procesu | iloczyn
Nazwa aplikacji | serwerach dpkg lub RPM


## <a name="collected-data---hyper-v"></a>Zebrane dane — funkcja Hyper-V

Urządzenie zbiera metadane dotyczące konfiguracji i wydajności z serwerów z systemem w środowisku funkcji Hyper-V.

### <a name="metadata"></a>Metadane
Metadane wykryte przez urządzenie Azure Migrate pomagają ustalić, czy serwery są gotowe do migracji na platformę Azure, serwery o odpowiednim rozmiarze i plany kosztów. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych serwera zbieranych i wysyłanych przez urządzenie do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
**Szczegóły serwera** | 
Numer seryjny systemu BIOS | Msvm_BIOSElement | BIOSSerialNumber
Typ maszyny wirtualnej (Gen 1 lub 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nazwa wyświetlana maszyny wirtualnej | Msvm_VirtualSystemSettingData | ElementName
Wersja maszyny wirtualnej | Msvm_ProcessorSettingData | VirtualQuantity
Pamięć (w bajtach) | Msvm_MemorySettingData | VirtualQuantity
Maksymalna ilość pamięci, która może być używana przez maszynę wirtualną | Msvm_MemorySettingData | Limit
Włączono pamięć dynamiczną | Msvm_MemorySettingData | DynamicMemoryEnabled
Nazwa/wersja systemu operacyjnego/nazwa FQDN | Msvm_KvpExchangeComponent | Dane nazwy GuestIntrinsicExchangeItems
Stan zasilacza maszyny wirtualnej | Msvm_ComputerSystem | EnabledState
**Szczegóły dysku** | 
Identyfikator dysku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Typ
Rozmiar wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Element nadrzędny wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | ParentPath
**Na szczegóły karty sieciowej** | 
Adresy IP (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | Adres IP IPAddresses
Protokół DHCP włączony (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Identyfikator karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adres MAC karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | Adres
Identyfikator karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting dane | InstanceID
Identyfikator MAC karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting dane | Adres

### <a name="performance-data"></a>Dane wydajności

Poniżej przedstawiono dane wydajności serwera zbierane i wysyłane przez urządzenie do platformy Azure.

**Klasa licznika wydajności** | **Licznik** | **Wpływ oceny**
--- | --- | ---
Procesor wirtualny funkcji hypervisor funkcji Hyper-V | Czas działania gościa (%) | Zalecany rozmiar maszyny wirtualnej/koszt
Maszyna wirtualna pamięć dynamiczna funkcji Hyper-V | Bieżące ciśnienie (%)<br/> Ilość pamięci fizycznej widocznej dla gościa (MB) | Zalecany rozmiar maszyny wirtualnej/koszt
Wirtualne urządzenie magazynujące funkcji Hyper-V | Bajty odczytu/s | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Wirtualne urządzenie magazynujące funkcji Hyper-V | Bajty zapisu/s | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru maszyny wirtualnej
Karta Virtual Network funkcji Hyper-V | Bajty odebrane/s | Obliczanie rozmiaru maszyny wirtualnej
Karta Virtual Network funkcji Hyper-V | Bajty wysłane/s | Obliczanie rozmiaru maszyny wirtualnej

- Użycie procesora CPU jest sumą wszystkich zastosowań dla wszystkich procesorów wirtualnych podłączonych do maszyny wirtualnej.
- Użycie pamięci to (bieżące ciśnienie * widoczna pamięć fizyczna gościa)/100.
- Wartości wykorzystania dysku i sieci są zbierane z wymienionych liczników wydajności funkcji Hyper-V.


## <a name="collected-data---physical"></a>Zebrane dane — fizyczne

Urządzenie zbiera metadane dotyczące konfiguracji i wydajności z serwerów fizycznych lub wirtualnych działających lokalnie.

### <a name="metadata"></a>Metadane

Metadane wykryte przez urządzenie Azure Migrate pomagają ustalić, czy serwery są gotowe do migracji na platformę Azure, serwery o odpowiednim rozmiarze i plany kosztów. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

### <a name="windows-server-metadata"></a>Metadane systemu Windows Server

Poniżej znajduje się pełna lista metadanych systemu Windows Server, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
Nazwa FQDN | Win32_ComputerSystem | Domena, nazwa, PartOfDomain
Liczba rdzeni procesora | Win32_PRocessor | NumberOfCores
Przydzielono pamięć | Win32_ComputerSystem | TotalPhysicalMemory
Numer seryjny systemu BIOS | Win32_ComputerSystemProduct | Identyfikacyjnemu
IDENTYFIKATOR GUID SYSTEMU BIOS | Win32_ComputerSystemProduct | INTERFEJSU
Typ rozruchu | Win32_DiskPartition | Wyszukaj partycję z typem = **GPT: system** dla interfejsu EFI/BIOS
Nazwa systemu operacyjnego | Win32_OperatingSystem | Caption
Wersja systemu operacyjnego |Win32_OperatingSystem | Wersja
Architektura systemu operacyjnego | Win32_OperatingSystem | OSArchitecture
Liczba dysków | Win32_DiskDrive | Model, rozmiar, DeviceID, MediaType, nazwa
Rozmiar dysku | Win32_DiskDrive | Rozmiar
Lista kart sieciowych | Win32_NetworkAdapterConfiguration | Opis, indeks
Adres IP karty sieciowej | Win32_NetworkAdapterConfiguration | IPAddress
Adres MAC karty sieciowej | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Metadane serwera z systemem Linux

Oto pełna lista metadanych serwera z systemem Linux, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Polecenia** 
--- | --- 
Nazwa FQDN | Kot/proc/sys/kernel/hostname, hostname-f
Liczba rdzeni procesora |  /proc/cpuinfo \| AWK '/^ Procesor/{print $3} ' \|
Przydzielono pamięć | Kot/proc/meminfo \| grep MemTotal \| AWK "{printf"%. 0f ", $2/1024}"
Numer seryjny systemu BIOS | lshw \| grep "serial:" \| szef-N1 \| AWK "{Print $2}" <br/> /usr/sbin/dmidecode-t 1 \| grep "serial" \| AWK "{$1 =" "; $2 =" "; Print}"
IDENTYFIKATOR GUID SYSTEMU BIOS | /sys/Class/DMI/ID/Cat product_uuid
Typ rozruchu | [-d/sys/Firmware/EFI]  && echo EFI \| \| echo BIOS
Nazwa/wersja systemu operacyjnego | Uzyskujemy dostęp do tych plików dla wersji i nazwy systemu operacyjnego:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architektura systemu operacyjnego | Uname — m
Liczba dysków | Dysk fdisk-l \| egrep '. * b ' \| AWK ' {Print $2} ' \| Wytnij-F1-d ': '
Dysk rozruchowy | DF/Boot \| SED-n \| AWK "{Print $1}"
Rozmiar dysku | Dysk fdisk-l \| egrep '. * b ' \| egrep $Disk: \| AWK ' {Print $5} '
Lista kart sieciowych | IP-o-4 addr show \| AWK "{print $2}"
Adres IP karty sieciowej | IP addr show $nic \| grep inet \| AWK "{Print $2}" \| ciętych-F1-d "/" 
Adres MAC karty sieciowej | adres IP Pokaż $nic \| eteru grep  \| AWK "{Print $2}"

### <a name="windows-performance-data"></a>Dane wydajności systemu Windows

Oto dane wydajności systemu Windows Server, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
Użycie procesora | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Użycie pamięci | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Liczba kart sieciowych | Win32_PerfFormattedData_Tcpip_NetworkInterface | Pobierz liczbę urządzeń sieciowych.
Odebrane dane na kartę sieciową | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dane przesyłane na kartę sieciową | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Liczba dysków | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Liczba dysków
Szczegóły dysku | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dane wydajności systemu Linux

Poniżej przedstawiono dane wydajności serwera z systemem Linux, które urządzenie zbiera i wysyła do platformy Azure.

**Dane** | **Polecenia** 
--- | --- 
Użycie procesora | /proc/stat/Cat| /proc/stat procesora grep
Użycie pamięci | bezpłatny \| grep MEM \| AWK "{Print $3/$ 2 * 100,0}"
Liczba kart sieciowych | lshw — Klasa \| ETH grep [0-60], Sieć \| 1
Odebrane dane na kartę sieciową | Kot/sys/Class/NET/ETH $ nic/Statystyka/rx_bytes
Dane przesyłane na kartę sieciową | Kot/sys/Class/NET/ETH $ nic/Statystyka/tx_bytes
Liczba dysków | Dysk fdisk-l \| egrep '. * b ' \| AWK ' {Print $2} ' \| Wytnij-F1-d ': '
Szczegóły dysku | /proc/diskstats Cat


## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie zostanie uaktualnione, ponieważ usługi Azure Migrate uruchomione na urządzeniu zostaną zaktualizowane. Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować usługi urządzeń.

### <a name="turn-off-auto-update"></a>Wyłącz funkcję autoaktualizacji

1. Na serwerze, na którym działa urządzenie, Otwórz Edytor rejestru.
2. Przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Aby wyłączyć automatyczne aktualizowanie, **Utwórz klucz rejestru Autoaktualizacja klucza z** wartością DWORD równą 0.

    ![Ustawianie klucza rejestru](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Włącz funkcję autoaktualizacji

Można włączyć funkcję autoaktualizacji przy użyciu jednej z następujących metod:

- Przez usunięcie klucza rejestru Autoaktualizacja z HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Kliknij pozycję **Wyświetl usługi urządzenia** , aby sprawdzić najnowsze aktualizacje w panelu **Konfiguracja wymagań wstępnych** , aby włączyć funkcję autoaktualizowania.

Aby usunąć klucz rejestru:

1. Na maszynie, na której działa urządzenie, Otwórz Edytor rejestru.
2. Przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Usuń funkcję autouzupełniania **klucza rejestru**, która została wcześniej utworzona w celu wyłączenia automatycznej aktualizacji.

Aby włączyć Configuration Manager urządzeń, po zakończeniu odnajdywania:

1. W Menedżerze konfiguracji urządzenia przejdź do pozycji Konfiguracja — panel **wymagania wstępne**
2. W oknie Sprawdź najnowsze aktualizacje kliknij pozycję **Wyświetl usługi urządzeń** , a następnie kliknij link, aby włączyć funkcję autoaktualizowania.

    ![Włącz aktualizacje AutoUpdate](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Sprawdź wersję usług urządzenia

Możesz sprawdzić wersję usługi urządzenia przy użyciu jednej z następujących metod:

- W Menedżerze konfiguracji urządzeń przejdź do pozycji **Konfigurowanie panelu wymagania wstępne** .
- Na urządzeniu urządzenia w **Panelu sterowania**  >  **programy i funkcje**.

Aby zaewidencjonować Menedżera konfiguracji urządzeń:

1. W Menedżerze konfiguracji urządzenia przejdź do pozycji Konfiguracja — panel **wymagania wstępne**
2. W oknie Sprawdzanie najnowszych aktualizacji kliknij pozycję **Wyświetl usługi urządzeń**.

    ![Sprawdź wersję](./media/migrate-appliance/versions.png)

Aby zaewidencjonować Panel sterowania:

1. Na urządzeniu kliknij kolejno pozycje **Start**  >  **Panelu sterowania**  >  **programy i funkcje** .
2. Sprawdź wersje usług urządzenia na liście.

    ![Sprawdź wersję w panelu sterowania](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Ręczna aktualizacja starszej wersji

Jeśli używasz starszej wersji dla którejkolwiek z tych usług, musisz odinstalować usługę i ręcznie ją zaktualizować do najnowszej wersji.

1. Aby sprawdzić dostępność najnowszych wersji usługi dla urządzeń, [pobierz](https://aka.ms/latestapplianceservices) LatestComponents.jspliku.
2.    Po pobraniu otwórz LatestComponents.jsplik w Notatniku.
3. Znajdź najnowszą wersję usługi w pliku i link do pobierania. Na przykład:

    "Name": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Pobierz najnowszą wersję nieaktualnej usługi przy użyciu linku pobierania w pliku.
5. Po pobraniu programu uruchom następujące polecenie w oknie polecenia administratora, aby sprawdzić integralność pobranego pliku MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Na przykład: C: \> certutil-HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Sprawdź, czy dane wyjściowe polecenia są zgodne z wpisem wartości skrótu dla usługi w pliku (na przykład wartość skrótu MD5 powyżej).
6. Teraz uruchom plik MSI, aby zainstalować usługę. Jest to instalacja dyskretna, a okno instalacji zostaje zamknięte po zakończeniu.
7. Po zakończeniu instalacji sprawdź wersję usługi w **Panelu sterowania**  >  **programy i funkcje**. Wersję usługi należy teraz uaktualnić do najnowszej podanej w pliku JSON.



## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak](how-to-set-up-appliance-vmware.md) skonfigurować urządzenie dla programu VMware.
- [Dowiedz się, jak](how-to-set-up-appliance-hyper-v.md) skonfigurować urządzenie dla funkcji Hyper-V.
- [Dowiedz się, jak](how-to-set-up-appliance-physical.md) skonfigurować urządzenie dla serwerów fizycznych.

