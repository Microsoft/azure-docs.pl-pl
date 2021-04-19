---
title: Urządzenie usługi Azure Migrate
description: Zawiera podsumowanie obsługi urządzenia Azure Migrate wirtualnego.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d7fc04e65e2b79d43c48acd5a8c621f28d5c0403
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714672"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

Ten artykuł zawiera podsumowanie wymagań wstępnych i wymagań dotyczących pomocy technicznej Azure Migrate urządzenia.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie Azure Migrate jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Służy do**
--- | --- | ---
**Odnajdywanie i ocena serwerów uruchomionych w środowisku VMware** | Azure Migrate: Odnajdywanie i ocena | Odnajdywanie serwerów uruchomionych w środowisku VMware<br/><br/> Odnajdywanie spisu zainstalowanego oprogramowania, analiza zależności bez agenta i odnajdywanie SQL Server i baz danych.<br/><br/> Zbierz metadane konfiguracji i wydajności serwera dla ocen.
**Migracja bez agenta serwerów działających w środowisku VMware** | Azure Migrate: Migracja serwera | Odnajdowanie serwerów uruchomionych w środowisku VMware. <br/><br/> Replikowanie serwerów bez instalowania na nich agentów.
**Odnajdywanie i ocena serwerów uruchomionych w środowisku funkcji Hyper-V** | Azure Migrate: Odnajdywanie i ocena | Odnajdowanie serwerów uruchomionych w środowisku funkcji Hyper-V.<br/><br/> Zbierz metadane konfiguracji i wydajności serwera dla ocen.
**Odnajdywanie i ocena serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym** |  Azure Migrate: Odnajdywanie i ocena |  Odnajdywanie serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym.<br/><br/> Zbierz metadane dotyczące konfiguracji i wydajności serwera dla ocen.

## <a name="deployment-methods"></a>Metody wdrażania

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla serwerów działających w środowisku VMware lub Funkcji Hyper-V (szablon OVA dla[oprogramowania VMware](how-to-set-up-appliance-vmware.md) lub wirtualnego dysku twardego dla [funkcji Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla środowiska VMware lub Hyper-V za pomocą skryptu [instalatora programu PowerShell.](deploy-appliance-script.md)
- W Azure Government należy wdrożyć urządzenie przy użyciu skryptu instalatora programu PowerShell. Kroki wdrażania można znaleźć [tutaj.](deploy-appliance-script-government.md)
- W przypadku serwerów fizycznych lub zwirtualizowanych w środowisku lokalnym lub dowolnej innej chmurze urządzenie zawsze jest wdrażane przy użyciu skryptu instalatora programu PowerShell. Kroki wdrażania można znaleźć [tutaj.](how-to-set-up-appliance-physical.md)
- Linki pobierania są dostępne w poniższych tabelach.

## <a name="appliance---vmware"></a>Urządzenie — VMware

W poniższej tabeli przedstawiono podsumowanie Azure Migrate urządzenia dla oprogramowania VMware.

> [!Note]
> Odnajdywanie i ocena SQL Server i baz danych uruchomionych w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

**Wymaganie** | **VMware**
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do menedżera konfiguracji urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzenia.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia:** jest to aplikacja internetowa, którą można skonfigurować przy użyciu szczegółów źródła, aby rozpocząć odnajdywanie i ocenę serwerów.<br/> - **Agent odnajdywania programu VMware:** agent zbiera metadane konfiguracji serwera, których można użyć do tworzenia ocen lokalnych.<br/>- **Agent oceny programu VMware:** agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa automatycznej aktualizacji:** usługa zapewnia, że wszyscy agenci na urządzeniu są aktualne. Jest ona uruchamiana automatycznie co 24 godziny.<br/>- **Agent dra:** orkiestruje replikację serwera i koordynuje komunikację między serwerami replikowanym a platformą Azure. Używany tylko podczas replikowania serwerów na platformę Azure przy użyciu migracji bez agenta.<br/>- **Brama:** wysyła replikowane dane na platformę Azure. Używany tylko podczas replikowania serwerów na platformę Azure przy użyciu migracji bez agenta.<br/>- **Agent odnajdywania i oceny SQL:** wysyła metadane konfiguracji i wydajności SQL Server i baz danych na platformę Azure.
**Limity projektu** |  Urządzenie można zarejestrować tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.
**Limity odnajdywania** | Urządzenie może odnaleźć do 10 000 serwerów uruchomionych na vCenter Server.<br/> Urządzenie może łączyć się z jednym vCenter Server.
**Obsługiwane wdrożenie** | Wdrażanie jako nowego serwera działającego na vCenter Server przy użyciu szablonu OVA.<br/><br/> Wdrażanie na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu instalatora programu PowerShell.
**Szablon OVA** | Pobierz z projektu lub [tutaj](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Rozmiar pobierania to 11,9 GB.<br/><br/> Pobrany szablon urządzenia jest dostarczany z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/>Jeśli okres ewaluowania jest bliski wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia przy użyciu szablonu OVA lub aktywowanie licencji systemu operacyjnego serwera urządzenia.
**Weryfikacja OVA** | [Sprawdź](tutorial-discover-vmware.md#verify-security) szablon OVA pobrany z projektu, sprawdzając wartości skrótu.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem,](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) aby dowiedzieć się, jak wdrożyć urządzenie za pomocą skryptu instalatora programu PowerShell.<br/><br/> 
**Wymagania dotyczące sprzętu i sieci** |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorów wirtualnych, około 80 GB miejsca na dysku i zewnętrznym przełącznikiem wirtualnym.<br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie jest wdrażane przy użyciu szablonu OVA, potrzebujesz wystarczającej ilości zasobów na vCenter Server, aby utworzyć serwer spełniający wymagania sprzętowe.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że działa ono w systemie Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._
**Wymagania dotyczące oprogramowania VMware** | W przypadku wdrożenia urządzenia jako serwera na platformie vCenter Server należy je wdrożyć na serwerze z systemem vCenter Server w wersji 5.5, 6.0, 6.5 lub 6.7 oraz na hoście ESXi w wersji 5.5 lub nowszej.<br/><br/> 
**VDDK (migracja bez agenta)** | Aby można było korzystać z urządzenia do migracji serwerów bez agenta, VMware vSphere VDDK musi być zainstalowany na serwerze urządzenia.

## <a name="appliance---hyper-v"></a>Urządzenie — funkcja Hyper-V

**Wymaganie** | **Hyper-V**
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do menedżera konfiguracji urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzenia.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia:** jest to aplikacja internetowa, którą można skonfigurować przy użyciu szczegółów źródła, aby rozpocząć odnajdywanie i ocenę serwerów.<br/> - **Agent odnajdywania:** agent zbiera metadane konfiguracji serwera, których można użyć do tworzenia ocen lokalnych.<br/>- **Agent oceny:** agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa automatycznej aktualizacji:** usługa zapewnia, że wszyscy agenci na urządzeniu są aktualne. Jest ona uruchamiana automatycznie co 24 godziny.
**Limity projektu** |  Urządzenie można zarejestrować tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.
**Limity odnajdywania** | Urządzenie może odnajdywać maksymalnie 5000 serwerów działających w środowisku funkcji Hyper-V.<br/> Urządzenie może łączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Obsługiwane wdrożenie** | Wdrażanie jako serwer uruchomiony na hoście funkcji Hyper-V przy użyciu szablonu wirtualnego dysku twardego.<br/><br/> Wdrażanie na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu instalatora programu PowerShell.
**Szablon wirtualnego dysku twardego** | Plik zip, który zawiera wirtualny dysk twardy. Pobierz z projektu lub [tutaj.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> Rozmiar pobierania to 8,91 GB.<br/><br/> Pobrany szablon urządzenia jest dostarczany z licencją ewaluacyjną systemu Windows Server 2016, która jest ważna przez 180 dni.<br/> Jeśli okres ewaluowania jest bliski wygaśnięcia, zalecamy pobranie i wdrożenie nowego urządzenia lub aktywowanie licencji systemu operacyjnego serwera urządzenia.
**Weryfikacja wirtualnego dysku twardego** | [Sprawdź](tutorial-discover-hyper-v.md#verify-security) szablon wirtualnego dysku twardego pobrany z projektu, sprawdzając wartości skrótu.
**Skrypt programu PowerShell** | Zapoznaj się z tym [artykułem,](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) aby dowiedzieć się, jak wdrożyć urządzenie za pomocą skryptu instalatora programu PowerShell.<br/>
**Wymagania dotyczące sprzętu i sieci**  |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych, około 80 GB miejsca na dysku i zewnętrznym przełącznikiem wirtualnym.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i wymaga dostępu do Internetu, bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane jako serwer uruchomiony na hoście funkcji Hyper-V, potrzebujesz na hoście wystarczającej ilości zasobów, aby utworzyć serwer spełniający wymagania sprzętowe.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że działa ono w systemie Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._
**Wymagania funkcji Hyper-V** | W przypadku wdrożenia urządzenia za pomocą szablonu wirtualnego dysku twardego urządzeniem dostarczanym przez program Azure Migrate jest maszyna wirtualna funkcji Hyper-V w wersji 5.0.<br/><br/> Na hoście funkcji Hyper-V musi działać system Windows Server 2012 R2 lub nowszy.

## <a name="appliance---physical"></a>Urządzenie — fizyczne

**Wymaganie** | **Fizyczny**
--- | ---
**Uprawnienia** | Aby uzyskać dostęp do menedżera konfiguracji urządzenia lokalnie lub zdalnie, musisz mieć konto użytkownika lokalnego lub domeny z uprawnieniami administracyjnymi na serwerze urządzenia.
**Usługi urządzeń** | Urządzenie ma następujące usługi:<br/><br/> - **Menedżer konfiguracji urządzenia:** jest to aplikacja internetowa, którą można skonfigurować przy użyciu szczegółów źródła, aby rozpocząć odnajdywanie i ocenę serwerów.<br/> - **Agent odnajdywania:** agent zbiera metadane konfiguracji serwera, których można użyć do tworzenia ocen lokalnych.<br/>- **Agent oceny:** agent zbiera metadane wydajności serwera, których można użyć do tworzenia ocen opartych na wydajności.<br/>- **Usługa automatycznej aktualizacji:** usługa zapewnia, że wszyscy agenci na urządzeniu są aktualne. Jest ona uruchamiana automatycznie co 24 godziny.
**Limity projektu** |  Urządzenie można zarejestrować tylko w jednym projekcie.<br/> Pojedynczy projekt może mieć wiele zarejestrowanych urządzeń.<br/>
**Limity odnajdywania** | Urządzenie może odnajdywać maksymalnie 1000 serwerów fizycznych.
**Obsługiwane wdrożenie** | Wdrażanie na istniejącym serwerze z systemem Windows Server 2016 przy użyciu skryptu instalatora programu PowerShell.
**Skrypt programu PowerShell** | Pobierz skrypt (AzureMigrateInstaller.ps1) w pliku zip z projektu lub [tutaj.](https://go.microsoft.com/fwlink/?linkid=2140334) [Dowiedz się więcej](tutorial-discover-physical.md).<br/><br/> Rozmiar pobierania to 85,8 MB.
**Weryfikacja skryptu** | [Sprawdź](tutorial-discover-physical.md#verify-security) skrypt instalatora programu PowerShell pobrany z projektu, sprawdzając wartości skrótu.
**Wymagania dotyczące sprzętu i sieci** |  Urządzenie powinno działać na serwerze z systemem Windows Server 2016, 16 GB pamięci RAM, 8 procesorów wirtualnych, około 80 GB miejsca na dysku.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i wymaga dostępu do Internetu, bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane na istniejącym serwerze, upewnij się, że działa ono w systemie Windows Server 2016 i spełnia wymagania sprzętowe.<br/>_(Obecnie wdrożenie urządzenia jest obsługiwane tylko w systemie Windows Server 2016)._

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie Azure Migrate wymaga łączności z Internetem.

- Podczas wdrażania urządzenia program Azure Migrate sprawdzanie łączności z wymaganymi adresami URL.
- Musisz zezwolić na dostęp do wszystkich adresów URL na liście. Jeśli oceniasz tylko tę ocenę, możesz pominąć adresy URL oznaczone jako wymagane w przypadku migracji bez agenta VMware.
- Jeśli używasz serwera proxy opartego na adresach URL do łączenia się z Internetem, upewnij się, że serwer proxy rozpozna wszystkie rekordy CNAME odebrane podczas szukania adresów URL.

### <a name="public-cloud-urls"></a>Adresy URL chmury publicznej

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do witryny Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Utwórz Azure Active Directory (AD) dla urządzenia w celu komunikowania się z Azure Migrate.
management.azure.com | Tworzenie aplikacji usługi Azure AD dla urządzenia w celu komunikowania się z Azure Migrate.
*.services.visualstudio.com | Przekazywanie dzienników urządzenia używanych do monitorowania wewnętrznego.
*.vault.azure.net | Zarządzanie wpisami tajnymi w Azure Key Vault.<br/> Uwaga: Upewnij się, że serwery replikowane mają do tego dostęp.
aka.ms/* | Zezwalaj na dostęp do linków Służy do pobierania i instalowania najnowszych aktualizacji dla usług urządzeń.
download.microsoft.com/download | Zezwalaj na pobieranie z Centrum pobierania Microsoft.
*.servicebus.windows.net | Komunikacja między urządzeniem a Azure Migrate usługi.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Połącz się z Azure Migrate URL usługi.
*.hypervrecoverymanager.windowsazure.com | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z Azure Migrate URL usługi.
*.blob.core.windows.net |  **Używany do migracji bez agenta VMware**<br/><br/>Przekazywanie danych do magazynu w celu migracji.

### <a name="government-cloud-urls"></a>Adresy URL chmury dla instytucji rządowych

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.us  | Przejdź do witryny Azure Portal.
graph.windows.net | Zaloguj się do subskrypcji platformy Azure.
login.microsoftonline.us  | Utwórz Azure Active Directory (AD), aby urządzenie komunikło się z Azure Migrate.
management.usgovcloudapi.net | Tworzenie aplikacji usługi Azure AD dla urządzenia w celu komunikowania się z Azure Migrate usługą.
*.services.visualstudio.com | Przekazywanie dzienników urządzeń używanych do monitorowania wewnętrznego.
*.vault.usgovcloudapi.net | Zarządzanie wpisami tajnymi w Azure Key Vault.
aka.ms/* | Zezwalaj na dostęp do linków; służy do pobierania i instalowania najnowszych aktualizacji usług urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z Centrum pobierania Microsoft.
*.servicebus.usgovcloudapi.net  | Komunikacja między urządzeniem a Azure Migrate usługi.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Połącz się z Azure Migrate URL usługi.
*.hypervrecoverymanager.windowsazure.us | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z Azure Migrate URL usługi.
*.blob.core.usgovcloudapi.net  |  **Używany do migracji bez agenta VMware**<br/><br/>Przekazywanie danych do magazynu w celu migracji.
*.applicationinsights.us | Przekazywanie dzienników urządzenia używanych do monitorowania wewnętrznego.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>Adresy URL chmury publicznej dla łączności z linkami prywatnymi

Urządzenie musi mieć dostęp do następujących adresów URL (bezpośrednio lub za pośrednictwem serwera proxy) za pośrednictwem lub powyżej dostępu do linku prywatnego. 

**Adres URL** | **Szczegóły**  
--- | --- | 
*.portal.azure.com  | Przejdź do witryny Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Tworzenie Azure Active Directory (AD) dla urządzenia w celu komunikowania się z Azure Migrate.
management.azure.com | Tworzenie aplikacji usługi Azure AD dla urządzenia w celu komunikowania się z Azure Migrate.
*.services.visualstudio.com (opcjonalnie) | Przekazywanie dzienników urządzenia używanych do monitorowania wewnętrznego.
aka.ms/* (opcjonalnie) | Zezwalaj na dostęp do linków aka; Służy do pobierania i instalowania najnowszych aktualizacji dla usług urządzeń.
download.microsoft.com/download | Zezwalaj na pobieranie z Centrum pobierania Microsoft.
*.servicebus.windows.net | **Używany do migracji bez agenta VMware**<br/><br/> Komunikacja między urządzeniem a Azure Migrate usługi.
*.vault.azure.net | **Używany do migracji bez agenta VMware**<br/><br/>  Upewnij się, że serwery do replikacji mają do tego dostęp.
*.hypervrecoverymanager.windowsazure.com | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z Azure Migrate URL usługi.
*.blob.core.windows.net |  **Używany do migracji bez agenta VMware**<br/><br/>Przekazywanie danych do magazynu w celu migracji.

### <a name="government-cloud-urls-for-private-link-connectivity"></a>Adresy URL chmury dla instytucji rządowych dla łączności z linkami prywatnymi   

Urządzenie musi mieć dostęp do następujących adresów URL (bezpośrednio lub za pośrednictwem serwera proxy) za pośrednictwem lub powyżej dostępu do łącza prywatnego. 

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.us  | Przejdź do witryny Azure Portal.
graph.windows.net | Zaloguj się do subskrypcji platformy Azure.
login.microsoftonline.us  | Utwórz Azure Active Directory (AD), aby urządzenie komunikło się z Azure Migrate.
management.usgovcloudapi.net | Tworzenie aplikacji usługi Azure AD dla urządzenia w celu komunikowania się z Azure Migrate usługą.
*.services.visualstudio.com (opcjonalnie) | Przekazywanie dzienników urządzeń używanych do monitorowania wewnętrznego.
aka.ms/* (opcjonalnie) | Zezwalaj na dostęp do linków; Służy do pobierania i instalowania najnowszych aktualizacji usług urządzenia.
download.microsoft.com/download | Zezwalaj na pobieranie z Centrum pobierania Microsoft.
*.servicebus.usgovcloudapi.net  | **Używany do migracji bez agenta VMware**<br/><br/> Komunikacja między urządzeniem a Azure Migrate usługi. 
*.vault.usgovcloudapi.net | **Używany do migracji bez agenta VMware**<br/><br/> Zarządzanie wpisami tajnymi w Azure Key Vault.
*.hypervrecoverymanager.windowsazure.us | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z Azure Migrate URL usługi.
*.blob.core.usgovcloudapi.net  |  **Używany do migracji bez agenta VMware**<br/><br/>Przekazywanie danych do magazynu w celu migracji.
*.applicationinsights.us (opcjonalnie) | Przekazywanie dzienników urządzenia używanych do monitorowania wewnętrznego.  

## <a name="collected-data---vmware"></a>Zebrane dane — VMware

Urządzenie zbiera metadane konfiguracji, metadane wydajności i dane zależności serwera (jeśli jest używana analiza zależności [bez](concepts-dependency-visualization.md) agenta).

### <a name="metadata"></a>Metadane

Metadane wykryte przez urządzenie Azure Migrate pomagają ustalić, czy serwery są gotowe do migracji na platformę Azure, serwery o odpowiednim rozmiarze, planują koszty i analizują zależności aplikacji. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Oto pełna lista metadanych serwera, które urządzenie zbiera i wysyła na platformę Azure.

**Danych** | **Licznik**
--- | ---
**Szczegóły serwera** |
Identyfikator serwera | vm.Config. InstanceUuid
Nazwa serwera | vm.Config. Nazwa
vCenter Server identyfikator | VMwareClient.Instance.Uuid
Opis serwera | vm.Summary.Config. Adnotacji
Nazwa produktu licencji | vm.Client.ServiceContent.About.LicenseProductName
Typ systemu operacyjnego | vm.SummaryConfig.GuestFullName
Typ rozruchu | vm.Config. Firmware
Liczba rdzeni | vm.Config. Hardware.NumCPU
Pamięć (MB) | vm.Config. Hardware.MemoryMB
Liczba dysków | vm.Config. Hardware.Device.ToList(). FindAll(x => to VirtualDisk).count
Lista rozmiarów dysków | vm.Config. Hardware.Device.ToList(). FindAll(x => to VirtualDisk)
Lista kart sieciowych | vm.Config. Hardware.Device.ToList(). FindAll(x => to VirtualEthernet).count
Wykorzystanie procesora | cpu.usage.average
Wykorzystanie pamięci |mem.usage.average
**Szczegóły 1 dysku** |
Wartość klucza dysku | Dysku. Klucz
Numer dikunitu | Dysku. UnitNumber (Numer jednostki)
Wartość klucza kontrolera dysku | Dysku. ControllerKey.Value
Aprowizowane gigabajty | virtualDisk.DeviceInfo.Summary
Nazwa dysku | Wartość wygenerowana przy użyciu dysku. UnitNumber, disk. Klucz, dysk. ControllerKey.VAlue
Operacje odczytu na sekundę | virtualDisk.numberReadAveraged.average
Operacje zapisu na sekundę | virtualDisk.numberWriteAveraged.average
Przepływność odczytu (MB na sekundę) | virtualDisk.read.average
Przepływność zapisu (MB na sekundę) | virtualDisk.write.average
**Szczegóły 1 karty sieciowej** |
Nazwa karty sieciowej | karta sieciowa. Klucz
Adres MAC | ((VirtualEthernetCard)nic). Adres MacAddress
Adresy IPv4 | vm.Guest.Net
Adresy IPv6 | vm.Guest.Net
Przepływność odczytu (MB na sekundę) | net.received.average
Przepływność zapisu (MB na sekundę) | net.transmitted.average
**Szczegóły ścieżki spisu** |
Nazwa | Kontenera. GetType(). Nazwa
Typ obiektu podrzędnego | Kontenera. Typ podrzędny
Szczegóły odwołania | Kontenera. MoRef
Szczegóły elementu nadrzędnego | Container.Parent
Szczegóły folderu na serwer | ((Folder)kontener). ChildEntity.Type
Szczegóły centrum danych na serwer | ((Datacenter)container). VmFolder
Szczegóły centrum danych na folder hosta | ((Datacenter)container). HostFolder
Szczegóły klastra na hosta | ((ClusterComputeResource)container). Hosta
Szczegóły hosta na serwer | ((HostSystem)container). Vm

### <a name="performance-data"></a>Dane wydajności

Oto dane wydajności, które urządzenie zbiera dla serwera działającego w programie VMware i wysyła na platformę Azure.

**Dane** | **Licznik** | **Wpływ oceny**
--- | --- | ---
Wykorzystanie procesora | cpu.usage.average | Zalecany rozmiar/koszt serwera
Wykorzystanie pamięci | mem.usage.average | Zalecany rozmiar/koszt serwera
Przepływność odczytu dysku (MB na sekundę) | virtualDisk.read.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Przepływność zapisu na dysku (MB na sekundę) | virtualDisk.write.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Operacje odczytu dysku na sekundę | virtualDisk.numberReadAveraged.average | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Operacje zapisu na dysku na sekundę | virtualDisk.numberWriteAveraged.average  | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Przepływność odczytu karty sieciowej (MB na sekundę) | net.received.average | Obliczanie rozmiaru serwera
Przepływność zapisu karty sieciowej (MB na sekundę) | net.transmitted.average  |Obliczanie rozmiaru serwera

### <a name="installed-software-inventory"></a>Zainstalowany spis oprogramowania

Urządzenie zbiera dane dotyczące spisu zainstalowanego oprogramowania na serwerach.

#### <a name="windows-server-software-inventory-data"></a>Dane spisu oprogramowania systemu Windows Server

Oto dane spisu oprogramowania, które urządzenie zbiera z każdego serwera z systemem Windows wykrytego w środowisku VMware.

**Dane** | **Lokalizacja rejestru** | **Klucz**
--- | --- | ---
Nazwa aplikacji  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Nazwa wyświetlana
Wersja  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Dostawca  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Dane funkcji systemu Windows Server

Oto dane funkcji, które urządzenie zbiera z każdego serwera z systemem Windows wykrytego w środowisku VMware.

**Dane**  | **Polecenie cmdlet programu PowerShell** | **Właściwość**
--- | --- | ---
Nazwa  | Get-WindowsFeature  | Nazwa
Typ funkcji | Get-WindowsFeature  | Typ funkcji
Nadrzędny  | Get-WindowsFeature  | Nadrzędny

#### <a name="sql-server-metadata"></a>SQL Server metadanych

Oto dane SQL Server, które urządzenie zbiera z każdego serwera z systemem Windows wykrytego w środowisku VMware.

**Dane**  | **Lokalizacja rejestru**  | **Klucz**
--- | --- | ---
Nazwa  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Wersja  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Wersja
Dodatek Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Sp
Wersja  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Wersja

#### <a name="windows-server-operating-system-data"></a>Dane systemu operacyjnego Windows Server

Oto dane systemu operacyjnego, które urządzenie zbiera z każdego serwera z systemem Windows wykrytego w środowisku VMware.

**Dane**  | **WMI, klasa**  | **Właściwość klasy WMI**
--- | --- | ---
Nazwa  | Win32_operatingsystem  | Caption
Wersja  | Win32_operatingsystem  | Wersja
Architektura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Dane spisu oprogramowania serwera z systemem Linux

Oto dane spisu oprogramowania, które urządzenie zbiera z każdego serwera z systemem Linux wykrytego w środowisku VMware. W zależności od systemu operacyjnego serwera uruchamiane jest co najmniej jedno polecenie.

**Dane**  | **Polecenia**
--- | ---
Nazwa | rpm, dpkg-query, snap
Wersja | rpm, dpkg-query, snap
Dostawca | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Dane systemu operacyjnego serwera z systemem Linux

Oto dane systemu operacyjnego, które urządzenie zbiera z każdego serwera z systemem Linux wykrytego w środowisku VMware.

**Dane**  | **Polecenia**
--- | ---
Nazwa <br/> Wersja | Zebrane z co najmniej jednego z następujących plików:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architektura | Uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server danych wystąpień i baz danych

Urządzenie zbiera dane w SQL Server i baz danych.

> [!Note]
> Odnajdywanie i ocena SQL Server i baz danych uruchomionych w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

#### <a name="sql-database-metadata"></a>Metadane bazy danych SQL

**Metadane bazy danych** | **Widoki/SQL Server właściwości**
--- | ---
Unikatowy identyfikator bazy danych | sys.databases
Identyfikator bazy danych zdefiniowanej przez serwer | sys.databases
Nazwa bazy danych | sys.databases
Poziom zgodności bazy danych | sys.databases
Nazwa sortowania bazy danych | sys.databases
Stan bazy danych | sys.databases
Rozmiar bazy danych (w KB/s) | Sys.master_files
Litera dysku lokalizacji zawierającej pliki danych | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Lista plików bazy danych | sys.databases, sys.master_files
Usługa Service Broker jest włączona lub nie | sys.databases
Baza danych jest włączona do przechwytywania zmian danych lub nie | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server metadanych

**Metadane serwera** | **Widoki/właściwości serwera SQL**
--- | ---
Nazwa serwera |Serverproperty
Nazwa FQDN | Parametrów połączenia pochodzących z odnajdywania zainstalowanych aplikacji
Identyfikator instalacji | sys.dm_server_registry
Wersja serwera | Serverproperty
Wersja serwera | Serverproperty
Platforma hosta serwera (Windows/Linux) | Serverproperty
Poziom produktu serwera (RTM SP CTP) | Serverproperty
Domyślna ścieżka kopii zapasowej | Serverproperty
Domyślna ścieżka plików danych | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Domyślna ścieżka plików dziennika | SERVERPROPERTY i Software\Microsoft\MSSQLServer\MSSQLServer
Nie. rdzeni na serwerze | sys.dm_os_schedulers, sys.dm_os_sys_info
Nazwa sortowania serwera | Serverproperty
Nie. rdzeni na serwerze ze stanem VISIBLE ONLINE | sys.dm_os_schedulers
Unikatowy identyfikator serwera | sys.dm_server_registry
Włączona lub nie włączona ha | Serverproperty
Włączono lub nie włączono rozszerzenia puli buforów | sys.dm_os_buffer_pool_extension_configuration
Klaster trybu failover skonfigurowany lub nie | Serverproperty
Serwer korzystający tylko z trybu uwierzytelniania systemu Windows | Serverproperty
Serwer instaluje program PolyBase | Serverproperty
Nie. procesorów logicznych w systemie | sys.dm_server_registry, sys.dm_os_sys_info
Stosunek liczby rdzeni logicznych lub fizycznych, które są udostępniane przez jeden pakiet procesora fizycznego | sys.dm_os_schedulers, sys.dm_os_sys_info
Brak fizycznych procesorów CPU w systemie | sys.dm_os_schedulers, sys.dm_os_sys_info
Data i godzina ostatniego rozpoczęcia serwera | sys.dm_server_registry
Maksymalne użycie pamięci serwera (w GB) | sys.dm_os_process_memory
Łączna liczba nie. użytkowników we wszystkich bazach danych | sys.databases, sys.logins
Całkowity rozmiar wszystkich baz danych użytkowników | sys.databases
Rozmiar tymczasowej bazy danych | sys.master_files, sys.config, sys.dm_os_sys_info
Nie. identyfikatorów logowania | sys.logins
Lista serwerów połączonych | Sys.servers
Lista zadań agenta | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadane wydajności

**Wydajność** | **Widoki/właściwości serwera SQL** | **Wpływ oceny**
--- | --- | ---
SQL Server wykorzystania procesora CPU| sys.dm_os_ring_buffers| Zalecany rozmiar SKU (wymiar procesora CPU)
Liczba procesorów logicznych SQL| Sys.dm_os_sys_info| Zalecany rozmiar SKU (wymiar procesora CPU)
Pamięć fizyczna SQL w użyciu| sys.dm_os_process_memory| Nieużywane
Procent użycia pamięci SQL| sys.dm_os_process_memory | Nieużywane
Wykorzystanie procesora CPU bazy danych| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Zalecany rozmiar SKU (wymiar procesora CPU)
Pamięć bazy danych w użyciu (pula buforów)| sys.dm_os_buffer_descriptors| Zalecany rozmiar SKU (wymiar pamięci)
We/Wy odczytu/zapisu plików| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar SKU (wymiar we/wy)
Liczba odczytów/zapisu w pliku| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar jednostki SKU (wymiar przepływności)
Wstrzymanie odczytu/zapisu we/wy pliku (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Zalecany rozmiar SKU (wymiar opóźnienia operacji we/wy)
Rozmiar pliku| Sys.master_files| Zalecany rozmiar SKU (wymiar magazynu)


### <a name="application-dependency-data"></a>Dane zależności aplikacji

Analiza zależności bez agenta zbiera dane połączenia i przetwarzania.

#### <a name="windows-server-dependencies-data"></a>Dane zależności systemu Windows Server

Oto dane połączenia, które urządzenie zbiera z każdego serwera z systemem Windows, włączone do analizy zależności bez agenta.

**Dane** | **Polecenia**
--- | ---
Port lokalny | Netstat
Lokalny adres IP | Netstat
Port zdalny | Netstat
Zdalny adres IP | Netstat
Stan połączenia TCP | Netstat
Identyfikator procesu | Netstat
Liczba aktywnych połączeń | Netstat

Oto dane połączenia, które urządzenie zbiera z każdego serwera z systemem Windows, włączone do analizy zależności bez agenta.

**Dane** | **WMI, klasa** | **Właściwość klasy WMI**
--- | --- | ---
Nazwa procesu | Win32_Process | ExecutablePath
Argumenty procesu | Win32_Process | CommandLine
Nazwa aplikacji | Win32_Process | Parametr VersionInfo.ProductName właściwości ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Dane zależności serwera z systemem Linux

Oto dane połączenia, które urządzenie zbiera z każdego serwera z systemem Linux, włączone do analizy zależności bez agenta.

**Dane** | **Polecenia**
--- | ---
Port lokalny | Netstat
Lokalny adres IP | Netstat
Port zdalny | Netstat
Zdalny adres IP | Netstat
Stan połączenia TCP | Netstat
Liczba aktywnych połączeń | Netstat
Identyfikator procesu  | Netstat
Nazwa procesu | Ps
Argumenty procesu | Ps
Nazwa aplikacji | dpkg lub rpm

## <a name="collected-data---hyper-v"></a>Zebrane dane — funkcja Hyper-V

Urządzenie zbiera metadane konfiguracji i wydajności z serwerów działających w środowisku funkcji Hyper-V.

### <a name="metadata"></a>Metadane
Metadane odnalezione przez urządzenie Azure Migrate pomagają ustalić, czy serwery są gotowe do migracji na platformę Azure, serwery o właściwym rozmiarze i koszty planów. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

Oto pełna lista metadanych serwera, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **WMI, klasa** | **Właściwość klasy WMI**
--- | --- | ---
**Szczegóły serwera** | 
Numer seryjny systemu BIOS | Msvm_BIOSElement | BIOSSerialNumber
Typ serwera (Gen 1 lub 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nazwa wyświetlana serwera | Msvm_VirtualSystemSettingData | ElementName
Wersja serwera | Msvm_ProcessorSettingData | VirtualQuantity
Pamięć (w bajtach) | Msvm_MemorySettingData | VirtualQuantity
Maksymalna ilość pamięci, która może być zużywana przez serwer | Msvm_MemorySettingData | Limit
Włączona pamięć dynamiczna | Msvm_MemorySettingData | DynamicMemoryEnabled
Nazwa/wersja systemu operacyjnego/nazwa FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems Name Data
Stan zasilania serwera | Msvm_ComputerSystem | EnabledState
**Szczegóły 1 dysku** |
Identyfikator dysku | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Typ
Rozmiar wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Nadrzędny wirtualny dysk twardy | Msvm_VirtualHardDiskSettingData | ParentPath
**Szczegóły 1 karty sieciowej** |
Adresy IP (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | Adres IP IPAddresses
Włączony protokół DHCP (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Identyfikator karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adres MAC karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | Adres
Identyfikator karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting Data | InstanceID
Identyfikator MAC karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting Data | Adres

### <a name="performance-data"></a>Dane wydajności

Oto dane wydajności serwera, które urządzenie zbiera i wysyła na platformę Azure.

**Klasa licznika wydajności** | **Licznik** | **Wpływ oceny**
--- | --- | ---
Procesor wirtualny funkcji Hypervisor funkcji Hyper-V | % czasu uruchomienia gościa | Zalecany rozmiar/koszt serwera
Serwer pamięć dynamiczna Hyper-V | Bieżące ciśnienie (%)<br/> Pamięć fizyczna widoczna przez gościa (MB) | Zalecany rozmiar/koszt serwera
Wirtualne urządzenie magazynujące funkcji Hyper-V | Bajty odczytu/sekundę | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Wirtualne urządzenie magazynujące funkcji Hyper-V | Liczba bajtów zapisu na sekundę | Obliczanie rozmiaru dysku, kosztu magazynu, rozmiaru serwera
Karta sieciowa Virtual Network Hyper-V | Bajty odebrane/sekundę | Obliczanie rozmiaru serwera
Karta sieciowa Virtual Network Hyper-V | Bajty wysłane/sekundę | Obliczanie rozmiaru serwera

- Użycie procesora CPU to suma całego użycia dla wszystkich procesorów wirtualnych dołączonych do serwera.
- Wykorzystanie pamięci wynosi (bieżące wykorzystanie * widoczna pamięć fizyczna gościa) / 100.
- Wartości wykorzystania dysku i sieci są zbierane z wymienionych liczników wydajności funkcji Hyper-V.

## <a name="collected-data---physical"></a>Zebrane dane — fizyczne

Urządzenie zbiera metadane konfiguracji i wydajności z serwerów fizycznych lub wirtualnych działających lokalnie.

### <a name="metadata"></a>Metadane

Metadane odnalezione przez urządzenie Azure Migrate pomagają ustalić, czy serwery są gotowe do migracji na platformę Azure, serwery o właściwym rozmiarze i koszty planów. Firma Microsoft nie używa tych danych w żadnej inspekcji zgodności licencji.

### <a name="windows-server-metadata"></a>Metadane serwera z systemem Windows

Oto pełna lista metadanych serwera z systemem Windows, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **WMI, klasa** | **Właściwość klasy WMI**
--- | --- | ---
Nazwa FQDN | Win32_ComputerSystem | Domena, Nazwa, PartOfDomain
Liczba rdzeni procesora | Win32_PRocessor | Liczba rdzeni
Przydzielona pamięć | Win32_ComputerSystem | TotalPhysicalMemory
Numer seryjny systemu BIOS | Win32_ComputerSystemProduct | Numer identyfikujący
BIOS GUID | Win32_ComputerSystemProduct | Uuid
Typ rozruchu | Win32_DiskPartition | Sprawdzanie partycji z typem = **GPT:System** dla interfejsu EFI/BIOS
Nazwa systemu operacyjnego | Win32_OperatingSystem | Caption
Wersja systemu operacyjnego |Win32_OperatingSystem | Wersja
Architektura systemu operacyjnego | Win32_OperatingSystem | OSArchitecture
Liczba dysków | Win32_DiskDrive | Model, Rozmiar, DeviceID, MediaType, Nazwa
Rozmiar dysku | Win32_DiskDrive | Rozmiar
Lista kart sieciowych | Win32_NetworkAdapterConfiguration | Opis, indeks
Adres IP karty sieciowej | Win32_NetworkAdapterConfiguration | IPAddress
Adres MAC karty sieciowej | Win32_NetworkAdapterConfiguration | Adres MACAddress

### <a name="linux-server-metadata"></a>Metadane serwera z systemem Linux

Oto pełna lista metadanych serwera z systemem Linux, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **Polecenia**
--- | ---
Nazwa FQDN | cat /proc/sys/kernel/hostname, nazwa hosta -f
Liczba rdzeni procesora |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Przydzielono pamięć | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Numer seryjny systemu BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS GUID | cat /sys/class/dmi/id/product_uuid
Typ rozruchu | [ -d /sys/firmware/efi ] && echo EFI \| \| echo BIOS
Nazwa/wersja systemu operacyjnego | Uzyskujemy dostęp do tych plików dla wersji i nazwy systemu operacyjnego:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architektura systemu operacyjnego | Uname -m
Liczba dysków | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Dysk rozruchowy | df /boot \| sed -n 2p \| awk '{print $1}'
Rozmiar dysku | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Lista kart sieciowych | ip -o -4 addr show \| awk '{print $2}'
Adres IP karty sieciowej | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Adres MAC karty sieciowej | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dane dotyczące wydajności systemu Windows

Oto dane dotyczące wydajności systemu Windows Server, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **WMI, klasa** | **Właściwość klasy WMI**
--- | --- | ---
Użycie procesora | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Użycie pamięci | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Liczba kart sieciowych | Win32_PerfFormattedData_Tcpip_NetworkInterface | Pobierz liczbę urządzeń sieciowych.
Dane odebrane na kartę sieciową | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dane przesyłane na kartę sieciową | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Liczba dysków | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Liczba dysków
Szczegóły dysku | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dane dotyczące wydajności systemu Linux

Oto dane wydajności serwera z systemem Linux, które urządzenie zbiera i wysyła na platformę Azure.

| **Dane** | **Polecenia** |
| --- | --- |
| Użycie procesora | cat /proc/stat/ \| grep 'cpu' /proc/stat |
| Użycie pamięci | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| Liczba kart sieciowych | lshw -class network \| grep eth[0-60] \| wc -l |
| Dane odebrane na kartę sieciową | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Dane przesyłane na kartę sieciową | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Liczba dysków | fdisk -l \| egrep 'Dysk. \* bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| Szczegóły dysku | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Uaktualnienia urządzenia

Urządzenie jest uaktualniane w Azure Migrate usługi uruchomione na urządzeniu. Dzieje się to automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. To ustawienie domyślne można zmienić, aby ręcznie zaktualizować usługi urządzenia.

### <a name="turn-off-auto-update"></a>Wyłączanie automatycznej aktualizacji

1. Na serwerze z uruchomionym urządzeniem otwórz Edytor rejestru.
2. Przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Aby wyłączyć automatyczną aktualizację, utwórz klucz rejestru **AutoUpdate** o wartości DWORD 0.

    ![Ustawianie klucza rejestru](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Włączanie automatycznej aktualizacji

Automatyczną aktualizację można włączyć przy użyciu jednej z tych metod:

- Usuwając klucz rejestru AutoUpdate z HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Kliknij pozycję **Wyświetl usługi urządzeń z** najnowszych testów aktualizacji na panelu Konfigurowanie wymagań **wstępnych,** aby włączyć automatyczną aktualizację.

Aby usunąć klucz rejestru:

1. Na serwerze z uruchomionym urządzeniem otwórz Edytor rejestru.
2. Przejdź do **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Usuń utworzony wcześniej klucz rejestru **AutoUpdate,** aby wyłączyć automatyczną aktualizację.

Aby włączyć z urządzenia Menedżer konfiguracji, po zakończeniu odnajdywania:

1. W menedżerze konfiguracji urządzenia przejdź do **panelu Konfigurowanie wymagań wstępnych**
2. W najnowszej aktualizacji sprawdź, kliknij pozycję **Wyświetl usługi urządzenia** i kliknij link, aby włączyć automatyczną aktualizację.

    ![Włączanie aktualizacji automatycznych](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Sprawdzanie wersji usług urządzenia

Wersję usług urządzenia można sprawdzić przy użyciu jednej z tych metod:

- W menedżerze konfiguracji urządzenia przejdź do **panelu Konfigurowanie wymagań wstępnych.**
- Na urządzeniu w chmurze **Panel sterowania**  >  **programy i funkcje.**

Aby zaewidencjonować menedżera konfiguracji urządzenia:

1. W menedżerze konfiguracji urządzenia przejdź do **panelu Konfigurowanie wymagań wstępnych**
2. Podczas sprawdzania najnowszych aktualizacji kliknij pozycję **Wyświetl usługi urządzenia.**

    ![Sprawdzanie wersji](./media/migrate-appliance/versions.png)

Aby zaewidencjnić Panel sterowania:

1. Na urządzeniu kliknij pozycję **Uruchom**  >  **Panel sterowania**  >  **i funkcje**
2. Sprawdź wersje usług urządzenia na liście.

    ![Sprawdzanie wersji w Panel sterowania](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Ręczne aktualizowanie starszej wersji

Jeśli używasz starszej wersji dowolnej z usług, musisz odinstalować usługę i ręcznie zaktualizować ją do najnowszej wersji.

1. Aby sprawdzić, czy są dostępne najnowsze wersje usługi urządzenia, [pobierz](https://aka.ms/latestapplianceservices) LatestComponents.jspliku .
2. Po pobraniu otwórz plik LatestComponents.jsw Notatniku.
3. Znajdź najnowszą wersję usługi w pliku i link pobierania dla niego. Na przykład:

    "Name": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi ", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a6a81e75469b84"

4. Pobierz najnowszą wersję nieaktualnej usługi, korzystając z linku pobierania w pliku .
5. Po pobraniu uruchom następujące polecenie w oknie polecenia administratora, aby sprawdzić integralność pobranego pliku MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Na przykład: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Sprawdź, czy dane wyjściowe polecenia są takie, jak wpis wartości skrótu dla usługi w pliku (na przykład wartość skrótu MD5 powyżej).
6. Teraz uruchom pakiet MSI, aby zainstalować usługę. Jest to instalacja dyskretna, a po jej zakończeniu okno instalacji zostanie zamknięte.
7. Po zakończeniu instalacji sprawdź wersję usługi w Panelu sterowania  >  **Programy i funkcje.** Wersja usługi powinna zostać uaktualniona do najnowszej wersji pokazanej w pliku JSON.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się,](how-to-set-up-appliance-vmware.md) jak skonfigurować urządzenie dla oprogramowania VMware.
- [Dowiedz się,](how-to-set-up-appliance-hyper-v.md) jak skonfigurować urządzenie dla funkcji Hyper-V.
- [Dowiedz się,](how-to-set-up-appliance-physical.md) jak skonfigurować urządzenie dla serwerów fizycznych.
