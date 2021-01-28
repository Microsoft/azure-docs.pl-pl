---
title: Urządzenie usługi Azure Migrate
description: Zawiera podsumowanie obsługi urządzenia Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 17ac10fd4e48043b79db5448fad2f29c5eb3f2a3
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986823"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

W tym artykule przedstawiono podsumowanie wymagań wstępnych i wymagania dotyczące obsługi urządzenia Azure Migrate. 

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Sposób użycia** 
--- | --- | ---
**Ocena maszyny wirtualnej VMware** | Azure Migrate: Ocena serwera | Odnajdywanie maszyn wirtualnych VMware<br/><br/> Odnajdywanie aplikacji i zależności maszyn<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.
**Migracja bez agenta maszyny wirtualnej VMware** | Azure Migrate: Migracja serwera | Odnajdywanie maszyn wirtualnych VMware <br/><br/> Replikowanie maszyn wirtualnych VMware z migracją bez agenta.
**Ocena maszyny wirtualnej funkcji Hyper-V** | Azure Migrate: Ocena serwera | Odnajdywanie maszyn wirtualnych Hyper-V<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.
**Ocena komputera fizycznego** |  Azure Migrate: Ocena serwera |  Odnajdywanie serwerów fizycznych (lub maszyn wirtualnych traktowanych jako serwery fizyczne).<br/><br/> Zbieraj metadane maszyn i metadane wydajności dla ocen.

## <a name="deployment-methods"></a>Metody wdrażania

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V (szablon komórki jajowe dla oprogramowania VMware lub wirtualnego dysku twardego dla funkcji Hyper-V).
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla oprogramowania VMware lub funkcji Hyper-V za pomocą skryptu programu PowerShell.
- W Azure Government należy wdrożyć urządzenie przy użyciu skryptu.
- W przypadku serwerów fizycznych urządzenie jest zawsze wdrażane przy użyciu skryptu.
- Linki do pobrania są dostępne w poniższych tabelach.


## <a name="appliance---vmware"></a>Urządzenie — VMware 

Poniższa tabela zawiera podsumowanie wymagań dotyczących urządzeń Azure Migrate dla programu VMware.

**Wymaganie** | **VMware** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do aplikacji sieci Web urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Składniki urządzenia** | Urządzenie ma następujące składniki:<br/><br/> - **Aplikacja zarządzania**: jest to aplikacja internetowa do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania**: Agent zbiera dane konfiguracji maszyny. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Agent modułu zbierającego**: Agent zbiera dane wydajności. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Usługa Autoaktualizacja**: aktualizuje składniki urządzenia (jest uruchamiany co 24 godziny).<br/>- **Agent dra**: organizuje REPLIKACJĘ maszyny wirtualnej i koordynuje komunikację między replikowanymi maszynami i platformą Azure. Używane tylko w przypadku replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów.<br/>- **Brama**: wysyła zreplikowane dane na platformę Azure. Używane tylko w przypadku replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów.
**Obsługiwane wdrożenie** | Wdróż jako maszynę wirtualną VMware przy użyciu szablonu komórki jajowe.<br/><br/> Wdróż aplikację jako maszynę wirtualną VMware lub maszynę fizyczną przy użyciu skryptu instalacyjnego programu PowerShell.
**Obsługa projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 10 000 maszyn wirtualnych VMware na vCenter Server.<br/> Urządzenie może połączyć się z pojedynczym vCenter Server.
**Szablon komórki jajowe** | Pobierz z portalu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Rozmiar pobieranych plików to 11,9 GB.<br/><br/> Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Oprogramowanie/sprzęt** |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane na maszynie wirtualnej VMware, konieczne jest posiadanie wystarczającej ilości zasobów na vCenter Server do przydzielenia maszyny wirtualnej spełniającej wymagania.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe.
**Wymagania dotyczące oprogramowania VMware** | W przypadku wdrożenia urządzenia jako maszyny wirtualnej VMware należy je wdrożyć na hoście ESXi z systemem w wersji 5,5 lub nowszej.<br/><br/> vCenter Server uruchomione 5,5, 6,0, 6,5 lub 6,7.
**VDDK (migracja bez wykorzystania agentów)** | W przypadku wdrożenia urządzenia jako maszyny wirtualnej VMware i korzystania z migracji bez agenta należy zainstalować na maszynie wirtualnej urządzenia VMware vSphere VDDK.
**Wartość skrótu — komórki jajowe** | [Sprawdź](tutorial-discover-vmware.md#verify-security) wartości skrótu szablonu komórki jajowe.
**Wartość skrótu — skrypt programu PowerShell** | [Sprawdź](deploy-appliance-script.md#verify-file-security) wartości skrótu skryptu programu PowerShell.




## <a name="appliance---hyper-v"></a>Urządzenie-Hyper-V

**Wymaganie** | **Hyper-V** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do aplikacji sieci Web urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Składniki urządzenia** | Urządzenie ma następujące składniki:<br/><br/>- **Aplikacja zarządzania**: jest to aplikacja internetowa do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania**: Agent zbiera dane konfiguracji maszyny. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Agent modułu zbierającego**: Agent zbiera dane wydajności. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Usługa Autoaktualizacja**: aktualizuje składniki urządzenia (jest uruchamiany co 24 godziny).
**Obsługiwane wdrożenie** | Wdróż jako maszynę wirtualną funkcji Hyper-V przy użyciu szablonu wirtualnego dysku twardego.<br/><br/> Wdróż aplikację jako maszynę wirtualną lub maszynę fizyczną z funkcją Hyper-V przy użyciu skryptu instalacyjnego programu PowerShell.
**Obsługa projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 5000 maszyn wirtualnych funkcji Hyper-V.<br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Szablon wirtualnego dysku twardego** | Folder spakowany zawierający dysk VHD. Pobierz z portalu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Rozmiar pobieranych plików to 8,91 GB.<br/><br/> Pobrany szablon urządzenia zawiera licencję ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres próbny zbliża się do wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia albo Aktywowanie licencji na maszynę wirtualną urządzenia.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Oprogramowanie/sprzęt** _   |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP i wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> Jeśli urządzenie zostanie uruchomione jako maszyna wirtualna funkcji Hyper-V, na hoście funkcji Hyper-V potrzeba przydzielenia wymagań sprzętowych.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe. 
_ *Wymagania dotyczące funkcji Hyper-V** | Jeśli urządzenie zostanie wdrożone przy użyciu szablonu VHD, maszyna wirtualna z urządzeniem udostępniona przez Azure Migrate jest maszyną wirtualną funkcji Hyper-V w wersji 5,0.<br/><br/> Na hoście funkcji Hyper-V musi być uruchomiony system Windows Server 2012 R2 lub nowszy. 
**Wartość skrótu — wirtualny dysk twardy** | [Sprawdź](tutorial-discover-hyper-v.md#verify-security) Wartości skrótu szablonu wirtualnego dysku twardego.
**Wartość skrótu — skrypt programu PowerShell** | [Sprawdź](deploy-appliance-script.md#verify-file-security) wartości skrótu skryptu programu PowerShell.


## <a name="appliance---physical"></a>Urządzenie — fizyczne

**Wymaganie** | **Fizyczny** 
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do aplikacji sieci Web urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzeń.
**Składniki urządzenia** | Urządzenie ma następujące składniki: <br/><br/> - **Aplikacja zarządzania**: jest to aplikacja internetowa do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania**: Agent zbiera dane konfiguracji maszyny. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Agent modułu zbierającego**: Agent zbiera dane wydajności. Używane podczas oceniania maszyn do migracji na platformę Azure.<br/>- **Usługa Autoaktualizacja**: aktualizuje składniki urządzenia (jest uruchamiany co 24 godziny).
**Obsługiwane wdrożenie** | Wdróż jako dedykowaną maszynę fizyczną lub maszynę wirtualną przy użyciu skryptu instalacyjnego programu PowerShell. Skrypt jest dostępny do pobrania z portalu.
**Obsługa projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Dowolna liczba urządzeń może być skojarzona z pojedynczym projektem.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 1000 serwerów fizycznych.
**Skrypt programu PowerShell** | Pobierz skrypt (AzureMigrateInstaller.ps1) w folderze spakowanym z portalu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140334). [Dowiedz się więcej](tutorial-discover-physical.md).<br/><br/> Rozmiar pobieranych plików to 85,8 MB.
**Oprogramowanie/sprzęt** |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB miejsca na dysku.<br/> Urządzenie musi mieć statyczny lub dynamiczny adres IP i wymaga dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomiony system Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._
**Wartość skrótu** | [Sprawdź](tutorial-discover-physical.md#verify-security) wartości skrótu skryptu programu PowerShell.

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie Azure Migrate wymaga połączenia z Internetem.

- Po wdrożeniu urządzenia Azure Migrate sprawdza łączność z wymaganymi adresami URL.
- Musisz zezwolić na dostęp do wszystkich adresów URL na liście. Jeśli wykonujesz tylko ocenę, możesz pominąć adresy URL, które są oznaczone jako wymagane tylko dla migracji bez agenta.
-  Jeśli używasz serwera proxy opartego na adresie URL do łączenia się z Internetem, upewnij się, że serwer proxy rozpoznaje wszystkie rekordy CNAME otrzymane podczas wyszukiwania adresów URL.

### <a name="public-cloud-urls"></a>Adresy URL chmury publicznej

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do witryny Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Utwórz aplikacje Azure Active Directory (AD) dla urządzenia, aby komunikować się z Azure Migrate.
management.azure.com | Utwórz aplikacje usługi Azure AD dla urządzenia, aby komunikować się z usługą Azure Migrate.
*.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault. Uwaga: Upewnij się, że maszyny do replikacji mają dostęp do tego obiektu.
aka.ms/* | Zezwalaj na dostęp do linków aliasów. Używany do Azure Migrate aktualizacji urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z usługi Microsoft Download.
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
*.services.visualstudio.com | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.
*. vault.usgovcloudapi.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
aka.ms/* | Zezwalaj na dostęp do linków aliasów. Używany do Azure Migrate aktualizacji urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z usługi Microsoft Download.
*. servicebus.usgovcloudapi.net  | Komunikacja między urządzeniem a usługą Azure Migrate.
*. discoverysrv.windowsazure.us <br/> *. migration.windowsazure.us | Połącz się z adresami URL usługi Azure Migrate.
*. hypervrecoverymanager.windowsazure.us | **Używane do migracji bez agentów programu VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*. blob.core.usgovcloudapi.net  |  **Używane do migracji bez agentów programu VMware**<br/><br/>Przekaż dane do magazynu na potrzeby migracji.
*. applicationinsights.us | Przekaż Dzienniki aplikacji używane do wewnętrznego monitorowania.





## <a name="collected-data---vmware"></a>Zebrane dane — VMware

Urządzenie zbiera dane dotyczące metadanych, danych wydajności i analizy zależności (jeśli jest używana [analiza zależności](concepts-dependency-visualization.md) bez wykorzystania agentów).

### <a name="metadata"></a>Metadane

Metadane wykryte przez urządzenie Azure Migrate ułatwiają ustalenie, czy maszyny i aplikacje są gotowe do migracji na platformę Azure, maszyny i aplikacje o odpowiednim rozmiarze, planuje koszty i analizuje zależności aplikacji. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych VMware, które urządzenie zbiera i wysyła do platformy Azure.

**DATA** | **PRZECIW**
--- | --- 
**Szczegóły maszyny** | 
Identyfikator maszyny wirtualnej | vm.Config. InstanceUuid 
Nazwa maszyny wirtualnej | vm.Config. Nazwij
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


### <a name="installed-apps-metadata"></a>Metadane zainstalowanych aplikacji

Funkcja odnajdywania aplikacji zbiera zainstalowane aplikacje i dane systemu operacyjnego.

#### <a name="windows-vm-apps-data"></a>Dane aplikacji maszyny wirtualnej z systemem Windows

Oto dane aplikacji, które są zbierane przez urządzenie z każdej maszyny wirtualnej z włączoną funkcją odnajdowania aplikacji. Te dane są wysyłane do platformy Azure.

**Dane** | **Lokalizacja rejestru** | **Klucz**
--- | --- | ---
Nazwa aplikacji  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Nazwa wyświetlana
Wersja  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Dostawca  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Dane funkcji maszyny wirtualnej z systemem Windows

Poniżej przedstawiono informacje o funkcjach zbieranych przez urządzenie z każdej maszyny wirtualnej z włączoną funkcją odnajdowania aplikacji. Te dane są wysyłane do platformy Azure.

**Dane**  | **Polecenie cmdlet programu PowerShell** | **Właściwość**
--- | --- | ---
Nazwa  | Get-WindowsFeature  | Nazwa
Typ funkcji | Get-WindowsFeature  | Element featuretype
Nadrzędny  | Get-WindowsFeature  | Nadrzędny

#### <a name="windows-vm-sql-server-metadata"></a>Metadane SQL Server maszyny wirtualnej z systemem Windows

Oto metadane programu SQL Server zbierane przez urządzenie z maszyn wirtualnych, na których działa program Microsoft SQL Server z włączoną funkcją odnajdywania aplikacji. Te dane są wysyłane do platformy Azure.

**Dane**  | **Lokalizacja rejestru**  | **Klucz**
--- | --- | ---
Nazwa  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Wersja  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Wersja 
Dodatek Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | REQUIREMENT
Wersja  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Wersja 

#### <a name="windows-vm-operating-system-data"></a>Dane systemu operacyjnego maszyny wirtualnej z systemem Windows

Oto dane systemu operacyjnego, które urządzenie zbiera dla każdej maszyny wirtualnej z włączoną funkcją odnajdywania aplikacji. Te dane są wysyłane do platformy Azure.

Dane  | Klasa WMI  | Właściwość klasy usługi WMI
--- | --- | ---
Nazwa  | Win32_operatingsystem  | Caption
Wersja  | Win32_operatingsystem  | Wersja
Architektura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Dane aplikacji maszyny wirtualnej z systemem Linux

Oto dane aplikacji, które są zbierane przez urządzenie z każdej maszyny wirtualnej z włączoną funkcją odnajdowania aplikacji. W oparciu o system operacyjny maszyny wirtualnej uruchamiane jest jedno lub więcej poleceń. Te dane są wysyłane do platformy Azure.

Dane  | Polecenie
--- | --- 
Nazwa | rpm, serwerach dpkg-Query, Snap
Wersja | rpm, serwerach dpkg-Query, Snap
Dostawca | rpm, serwerach dpkg-Query, Snap

#### <a name="linux-vm-operating-system-data"></a>Dane systemu operacyjnego maszyny wirtualnej z systemem Linux

Oto dane systemu operacyjnego, które urządzenie zbiera dla każdej maszyny wirtualnej z włączoną funkcją odnajdywania aplikacji. Te dane są wysyłane do platformy Azure.

**Dane**  | **Polecenie** 
--- | --- | ---
Nazwa <br/> Wersja | Zebrane z co najmniej jednego z następujących plików:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Architektura | uname


### <a name="app-dependencies-metadata"></a>Metadane zależności aplikacji

Analiza zależności bez agenta zbiera dane połączeń i procesów.

#### <a name="windows-vm-app-dependencies-data"></a>Dane zależności aplikacji maszyny wirtualnej systemu Windows

Oto dane połączenia, które urządzenie zbiera z każdej maszyny wirtualnej z włączoną obsługą analizy zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Użyte polecenie** 
--- | --- 
Port lokalny | netstat
Lokalny adres IP | netstat
Port zdalny | netstat
Zdalny adres IP | netstat
Stan połączenia TCP | netstat
Identyfikator procesu | netstat
Liczba aktywnych połączeń | netstat


Poniżej przedstawiono dane procesu zbierane przez urządzenie z każdej maszyny wirtualnej z włączoną analizą zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy usługi WMI**
--- | --- | ---
Nazwa procesu | Win32_Process | Ścieżka pliku wykonywalnego
Argumenty procesu | Win32_Process | CommandLine
Nazwa aplikacji | Win32_Process | VersionInfo. ProductName — parametr właściwości ścieżka pliku wykonywalnego

#### <a name="linux-vm-app-dependencies-data"></a>Dane zależności aplikacji maszyny wirtualnej z systemem Linux

Poniżej przedstawiono dane dotyczące połączenia i przetwarzania, które urządzenie zbiera z każdej maszyny wirtualnej z systemem Linux z włączoną obsługą analizy zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Użyte polecenie** 
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

Urządzenie zbiera dane dotyczące metadanych, danych wydajności i analizy zależności (jeśli jest używana [analiza zależności](concepts-dependency-visualization.md) bez wykorzystania agentów).

### <a name="metadata"></a>Metadane
Metadane wykryte przez urządzenie Azure Migrate ułatwiają ustalenie, czy maszyny i aplikacje są gotowe do migracji na platformę Azure, maszyny i aplikacje o odpowiednim rozmiarze, planuje koszty i analizuje zależności aplikacji. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych maszyn wirtualnych funkcji Hyper-V, które urządzenie zbiera i wysyła do platformy Azure.

**DATA** | **KLASA WMI** | **WŁAŚCIWOŚĆ KLASY USŁUGI WMI**
--- | --- | ---
**Szczegóły maszyny** | 
Numer seryjny BIOS _ Msvm_BIOSElement | BIOSSerialNumber
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

Poniżej przedstawiono dane wydajności maszyny wirtualnej funkcji Hyper-IT zbierane i wysyłane na platformę Azure.

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

Urządzenie zbiera dane dotyczące metadanych, danych wydajności i analizy zależności (jeśli jest używana [analiza zależności](concepts-dependency-visualization.md) bez wykorzystania agentów).

### <a name="windows-metadata"></a>Metadane systemu Windows

Metadane wykryte przez urządzenie Azure Migrate ułatwiają ustalenie, czy maszyny i aplikacje są gotowe do migracji na platformę Azure, maszyny i aplikacje o odpowiednim rozmiarze, planuje koszty i analizuje zależności aplikacji. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Poniżej znajduje się pełna lista metadanych systemu Windows Server, które urządzenie zbiera i wysyła do platformy Azure.

**DATA** | **KLASA WMI** | **WŁAŚCIWOŚĆ KLASY USŁUGI WMI**
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

### <a name="linux-metadata"></a>Metadane systemu Linux

Oto pełna lista metadanych serwera z systemem Linux, które urządzenie zbiera i wysyła do platformy Azure.

**DATA** | **System** 
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

**Dane** | **Linux** 
--- | --- 
Użycie procesora | /proc/stat/Cat| /proc/stat procesora grep
Użycie pamięci | bezpłatny \| grep MEM \| AWK "{Print $3/$ 2 * 100,0}"
Liczba kart sieciowych | lshw — Klasa \| ETH grep [0-60], Sieć \| 1
Odebrane dane na kartę sieciową | Kot/sys/Class/NET/ETH $ nic/Statystyka/rx_bytes
Dane przesyłane na kartę sieciową | Kot/sys/Class/NET/ETH $ nic/Statystyka/tx_bytes
Liczba dysków | Dysk fdisk-l \| egrep '. * b ' \| AWK ' {Print $2} ' \| Wytnij-F1-d ': '
Szczegóły dysku | /proc/diskstats Cat


## <a name="appliance-upgrades"></a>Uaktualnienia urządzeń

Urządzenie zostanie uaktualnione w miarę aktualizowania Azure Migrate agentów działających na urządzeniu. Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. Można zmienić to ustawienie domyślne, aby ręcznie zaktualizować usługi urządzeń.

### <a name="turn-off-auto-update"></a>Wyłącz funkcję autoaktualizacji

1. Na maszynie, na której działa urządzenie, Otwórz Edytor rejestru.
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

W przypadku korzystania ze starszej wersji programu należy odinstalować usługę i ręcznie ją zaktualizować do najnowszej wersji.

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

