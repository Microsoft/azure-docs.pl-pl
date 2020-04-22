---
title: Urządzenie usługi Azure Migrate
description: Zawiera omówienie urządzenia migracji platformy Azure używanego w ocenie i migracji serwera.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1c21f06e674871aefde1ae952a459db16feeb717
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676337"
---
# <a name="azure-migrate-appliance"></a>Urządzenie usługi Azure Migrate

W tym artykule podsumowano wymagania wstępne i wymagania dotyczące pomocy technicznej dla urządzenia migracji platformy Azure. 

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Urządzenie migracji platformy Azure jest używane w następujących scenariuszach.

**Scenariusz** | **Narzędzie** | **Używana do** 
--- | --- | ---
**Ocena maszyn wirtualnych VMware** | Migracja platformy Azure:Ocena serwera | Odkryj maszyny wirtualne VMware<br/><br/> Odkryj aplikacje maszynowe i zależności<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.
**Migracja bezgentowa VMware VM** | Migracja platformy Azure:migracja serwera | Odkryj maszyny wirtualne VMware <br/><br/> Replikowanie maszyn wirtualnych VMware z migracją bez agenta.
**Ocena maszyny wirtualnej funkcji Hyper-V** | Migracja platformy Azure:Ocena serwera | Odkryj maszyny wirtualne funkcji Hyper V<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.
**Ocena maszyny fizycznej** |  Migracja platformy Azure:Ocena serwera |  Odnajdowanie serwerów fizycznych (lub maszyn wirtualnych, które traktujesz jako serwery fizyczne).<br/><br/> Zbieranie metadanych maszyny i metadanych wydajności do oceny.

## <a name="deployment-methods"></a>Metody wdrażania

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V (szablon OVA dla oprogramowania VMware lub VHD dla funkcji Hyper-V).
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla VMware lub Hyper-V przy użyciu skryptu programu PowerShell.
- W usłudze Azure Dla Instytucji, należy wdrożyć urządzenie przy użyciu skryptu.
- W przypadku serwerów fizycznych urządzenie jest zawsze wdrażane przy użyciu skryptu.
- Linki do pobrania są dostępne w poniższych tabelach.


## <a name="appliance---vmware"></a>Urządzenie - VMware 

W poniższej tabeli podsumowano wymagania urządzenia migracji platformy Azure dla oprogramowania VMware.

**Wymaganie** | **Vmware** 
--- | ---
**Elementy urządzenia** | Urządzenie posiada następujące elementy:<br/><br/> - **Aplikacja do zarządzania:** Jest to aplikacja sieci web do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceny maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania:** Agent zbiera dane konfiguracji maszyny. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Agent oceny**: Agent zbiera dane dotyczące wydajności. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Usługa automatycznej aktualizacji:** Aktualizuje składniki urządzenia (uruchamia się co 24 godziny).<br/>- **Agent DRA:** Organizuje replikację maszyn wirtualnych i koordynuje komunikację między replikowanymi maszynami a platformą Azure. Używane tylko podczas replikowania maszyn wirtualnych VMware na platformie Azure przy użyciu migracji bez agenta.<br/>- **Brama:** Wysyła replikowane dane na platformę Azure. Używane tylko podczas replikowania maszyn wirtualnych VMware na platformie Azure przy użyciu migracji bez agenta.
**Obsługiwane wdrażanie** | Wdrażanie jako maszyna wirtualna VMware przy użyciu szablonu OVA.<br/><br/> Wdrażanie jako maszyna wirtualna VMware lub komputer fizyczny przy użyciu skryptu instalacyjnego programu PowerShell.
**Wsparcie projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Z jednym projektem można skojarzyć dowolną liczbę urządzeń.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 10 000 maszyn wirtualnych VMware na serwerze vCenter.<br/> Urządzenie może łączyć się z jednym serwerem vCenter.
**Szablon OVA** | Pobierz z portalu https://aka.ms/migrate/appliance/vmwarelub z .<br/><br/> Rozmiar pobierania wynosi 11,2 GB.<br/><br/> Pobrany szablon urządzenia jest dostarczany z licencją ewaluacją systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres oceny jest bliski wygaśnięcia, zaleca się pobranie i wdrożenie nowego urządzenia lub aktywowanie licencji systemu operacyjnego maszyny Wirtualnej urządzenia.
**Skrypt programu PowerShell** | [Pobieranie skryptu](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Oprogramowanie/sprzęt** |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorami wirtualnymi, około 80 GB pamięci masowej na dysku i zewnętrznym przełącznikiem wirtualnym.<br/> Urządzenie wymaga dostępu do Internetu, bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie zostanie uruchomione na maszynie wirtualnej VMware, potrzebujesz wystarczającej ilości zasobów na serwerze vCenter, aby przydzielić maszynę wirtualną, która spełnia wymagania.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomione w systemie Windows Server 2016 i spełnia wymagania sprzętowe. 
**Wymagania dotyczące VMware** | Jeśli urządzenie zostanie wdrożone jako maszyna wirtualna VMware, musi zostać wdrożona na hoście ESXi z systemem 5.5 lub nowszym.<br/><br/> Serwer vCenter z systemem 5.5, 6.0, 6.5 lub 6.7.
**VDDK (migracja bez agenta)** | Jeśli urządzenie zostanie wdrożone jako maszyna wirtualna VMware i zostanie uruchomiona migracja bez agenta, VMware vSphere VDDK musi być zainstalowany na maszynie wirtualnej urządzenia.
**Wartość skrótu-OVA** | [Sprawdź](tutorial-assess-vmware.md#verify-security) wartości skrótu szablonu OVA.
**Wartość skrótu — skrypt programu PowerShell** | [Sprawdź](deploy-appliance-script.md#verify-file-security) wartości skrótu skryptu programu PowerShell.




## <a name="appliance---hyper-v"></a>Urządzenie - Hyper-V

**Wymaganie** | **Funkcja Hyper-V** 
--- | ---
**Elementy urządzenia** | Urządzenie posiada następujące elementy:<br/><br/>- **Aplikacja do zarządzania:** Jest to aplikacja sieci web do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceny maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania:** Agent zbiera dane konfiguracji maszyny. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Agent oceny**: Agent zbiera dane dotyczące wydajności. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Usługa automatycznej aktualizacji:** Aktualizuje składniki urządzenia (uruchamia się co 24 godziny).
**Obsługiwane wdrażanie** | Wdrażanie jako maszyna wirtualna funkcji Hyper-V przy użyciu szablonu VHD.<br/><br/> Wdrażanie jako maszyny wirtualnej funkcji Hyper-V lub komputera fizycznego przy użyciu skryptu instalacyjnego programu PowerShell.
**Wsparcie projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Z jednym projektem można skojarzyć dowolną liczbę urządzeń.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 5000 maszyn wirtualnych z funkcjią Hyper V.<br/> Urządzenie może łączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Szablon VHD** | Spakowany folder z VHD. Pobierz z portalu https://aka.ms/migrate/appliance/hypervlub z .<br/><br/> Rozmiar pobierania wynosi 10 GB.<br/><br/> Pobrany szablon urządzenia jest dostarczany z licencją ewaluacją systemu Windows Server 2016, która jest ważna przez 180 dni. Jeśli okres oceny jest bliski wygaśnięcia, zaleca się pobranie i wdrożenie nowego urządzenia lub aktywowanie licencji systemu operacyjnego maszyny Wirtualnej urządzenia.
**Skrypt programu PowerShell** | [Pobieranie skryptu](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Oprogramowanie/sprzęt***   |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorami wirtualnymi, około 80 GB pamięci masowej na dysku i zewnętrznym przełącznikiem wirtualnym.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i wymaga dostępu do Internetu bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie zostanie uruchomione jako maszyna wirtualna funkcji Hyper-V, potrzebujesz wystarczającej ilości zasobów na hoście funkcji Hyper-V, aby przydzielić 16 GB pamięci RAM, 8 procesorów wirtualnych, około 80 GB miejsca do magazynowania i zewnętrzny przełącznik dla maszyny wirtualnej urządzenia.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomione w systemie Windows Server 2016 i spełnia wymagania sprzętowe. 
**Wymagania funkcji Hyper-V** | Jeśli wdrożysz urządzenie z szablonem VHD, maszyną wirtualną urządzenia dostarczoną przez usługę Azure Migrate jest maszyna wirtualna funkcji Hyper-V w wersji 5.0.<br/><br/> Na hoście funkcji Hyper-V musi być uruchomiony system Windows Server 2012 R2 lub nowszy. 
**Wartość skrótu VHD** | [Sprawdź](tutorial-assess-hyper-v.md#verify-security) wartości skrótu szablonu VHD.
**Wartość skrótu — skrypt programu PowerShell** | [Sprawdź](deploy-appliance-script.md#verify-file-security) wartości skrótu skryptu programu PowerShell.


## <a name="appliance---physical"></a>Urządzenie - Fizyczne

**Wymaganie** | **Fizyczne** 
--- | ---
**Elementy urządzenia** | Urządzenie posiada następujące elementy: <br/><br/> - **Aplikacja do zarządzania:** Jest to aplikacja sieci web do wprowadzania danych przez użytkownika podczas wdrażania urządzenia. Używane podczas oceny maszyn do migracji na platformę Azure.<br/> - **Agent odnajdywania:** Agent zbiera dane konfiguracji maszyny. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Agent oceny**: Agent zbiera dane dotyczące wydajności. Używane podczas oceny maszyn do migracji na platformę Azure.<br/>- **Usługa automatycznej aktualizacji:** Aktualizuje składniki urządzenia (uruchamia się co 24 godziny).
**Obsługiwane wdrażanie** | Wdrażanie jako dedykowanej maszyny fizycznej lub maszyny Wirtualnej przy użyciu skryptu instalacyjnego programu PowerShell. Skrypt jest dostępny do pobrania z portalu.
**Wsparcie projektu** |  Urządzenie może być skojarzone z pojedynczym projektem. <br/> Z jednym projektem można skojarzyć dowolną liczbę urządzeń.<br/> 
**Limity odnajdywania** | Urządzenie może wykryć do 250 serwerów fizycznych.
**Skrypt programu PowerShell** | Pobierz skrypt (AzureMigrateInstaller.ps1) w folderze spakowanym z portalu. [Dowiedz się więcej](tutorial-assess-physical.md#set-up-the-appliance). Alternatywnie, [pobierz bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> Rozmiar pobierania wynosi 59,7 MB.
**Oprogramowanie/sprzęt** |  Urządzenie powinno działać na komputerze z systemem Windows Server 2016, 32 GB pamięci RAM, 8 procesorami wirtualnymi, około 80 GB pamięci masowej na dysku i zewnętrznym przełącznikiem wirtualnym.<br/> Urządzenie wymaga statycznego lub dynamicznego adresu IP i wymaga dostępu do Internetu bezpośrednio lub za pośrednictwem serwera proxy.<br/><br/> Jeśli urządzenie jest uruchamiane na komputerze fizycznym, upewnij się, że jest uruchomione w systemie Windows Server 2016 i spełnia wymagania sprzętowe. 
**Wartość skrótu** | [Sprawdź](deploy-appliance-script.md#verify-file-security) wartości skrótu skryptu programu PowerShell.

## <a name="url-access"></a>Dostęp do adresu URL

Urządzenie usługi Azure Migrate wymaga łączności z Internetem.

- Podczas wdrażania urządzenia usługa Azure Migrate sprawdza łączność z wymaganymi adresami URL.
- Jeśli używasz serwera proxy opartego na adresach URL do łączenia się z Internetem, musisz zezwolić na dostęp do tych adresów URL, upewniając się, że serwer proxy rozpoznaje wszystkie rekordy CNAME odebrane podczas wyszukywania adresów URL.

### <a name="public-cloud-urls"></a>Adresy URL w chmurze publicznej

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.com  | Przejdź do witryny Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Zaloguj się do subskrypcji platformy Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Tworzenie aplikacji usługi Azure Active Directory (AD) dla urządzenia do komunikowania się z usługą Azure Migrate.
management.azure.com | Tworzenie aplikacji usługi Azure AD dla urządzenia do komunikowania się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż dzienniki aplikacji używane do monitorowania wewnętrznego.
*.vault.azure.net | Zarządzanie wpisami tajnymi w magazynie kluczy platformy Azure.
aka.ms/* | Zezwól na dostęp do łączy aka. Używane do aktualizacji urządzenia migracji platformy Azure.
download.microsoft.com/download | Zezwalaj na pobieranie z firmy Microsoft.
*.servicebus.windows.net | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Połącz się z adresami URL usługi Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.windows.net |  **Używany do migracji bez agenta VMware**<br/><br/>Przekaż dane do magazynu w celu migracji.

### <a name="government-cloud-urls"></a>Adresy URL chmury rządów

**Adres URL** | **Szczegóły**  
--- | --- |
*.portal.azure.us  | Przejdź do witryny Azure Portal.
graph.windows.net | Zaloguj się do subskrypcji platformy Azure.
login.microsoftonline.us  | Tworzenie aplikacji usługi Azure Active Directory (AD) dla urządzenia do komunikowania się z usługą Azure Migrate.
management.usgovcloudapi.net | Tworzenie aplikacji usługi Azure AD dla urządzenia do komunikowania się z usługą Azure Migrate.
dc.services.visualstudio.com | Przekaż dzienniki aplikacji używane do monitorowania wewnętrznego.
*.vault.usgovcloudapi.net | Zarządzanie wpisami tajnymi w magazynie kluczy platformy Azure.
aka.ms/* | Zezwól na dostęp do łączy aka. Używane do aktualizacji urządzenia migracji platformy Azure.
download.microsoft.com/download | Zezwalaj na pobieranie z firmy Microsoft.
*.servicebus.usgovcloudapi.net  | Komunikacja między urządzeniem a usługą Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Połącz się z adresami URL usługi Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Używany do migracji bez agenta VMware**<br/><br/> Połącz się z adresami URL usługi Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Używany do migracji bez agenta VMware**<br/><br/>Przekaż dane do magazynu w celu migracji.
*.applicationinsights.us | Przekaż dzienniki aplikacji używane do monitorowania wewnętrznego.





## <a name="collected-data---vmware"></a>Zebrane dane - VMware

Urządzenie zbiera metadane, dane wydajności i dane analizy zależności (jeśli używana jest analiza zależności bez [agenta).](concepts-dependency-visualization.md)

### <a name="metadata"></a>Metadane

Metadane wykryte przez urządzenie migracji platformy Azure pomaga ustalić, czy maszyny i aplikacje są gotowe do migracji na platformę Azure, odpowiednie maszyny i aplikacje, planuje koszty i analizuje zależności aplikacji. Firma Microsoft nie używa tych danych w żadnym audycie zgodności licencji.

Oto pełna lista metadanych maszyn wirtualnych VMware, które urządzenie zbiera i wysyła do platformy Azure.

**Danych** | **Licznik**
--- | --- 
**Szczegóły maszyny** | 
Identyfikator maszyny wirtualnej | Vm. Config.InstanceUuid 
Nazwa maszyny wirtualnej | Vm. Config.Name
Identyfikator serwera vCenter | VMwareClient.Instance.Uuid
Opis maszyny Wirtualnej | Vm. Podsumowanie.Config.Annotation
Nazwa produktu licencji | Vm. Klient.ServiceContent.About.LicenseProductName
Typ systemu operacyjnego | Vm. PodsumowanieConfig.GuestFullName
Typ rozruchu | Vm. Config.Firmware
Liczba rdzeni | Vm. Plik config.hardware.NumCPU
Pamięć (MB) | Vm. Plik config.hardware.memoryMB
Liczba dysków | Vm. Plik config.hardware.device.toList(). FindAll(x => jest VirtualDisk).count
Lista rozmiarów dysku | Vm. Plik config.hardware.device.toList(). FindAll(x => jest VirtualDisk)
Lista kart sieciowych | Vm. Plik config.hardware.device.toList(). FindAll(x => jest VirtualEthernet).count
Wykorzystanie procesora | cpu.usage.average
Wykorzystanie pamięci |mem.usage.average
**Szczegóły na dysku** | 
Wartość klucza dysku | Dysku. Klucz
Numer Dikunit | Dysku. Numer jednostki
Wartość klucza kontrolera dysku | Dysku. ControllerKey.Value
Gigabajty aprowizowane | virtualDisk.DeviceInfo.Podsumowanie
Nazwa dysku | Wartość generowana przy użyciu dysku. UnitNumber, dysk. Klucz, dysk. ControllerKey.VAlue
Operacje odczytu na sekundę | virtualDisk.numberReadAveraged.average
Operacje zapisu na sekundę | virtualDisk.numberWriteAveraged.average
Przepływność odczytu (MB na sekundę) | virtualDisk.read.average
Przepływność zapisu (MB na sekundę) | virtualDisk.write.average
**Na szczegóły karty sieciowej** | 
Nazwa karty sieciowej | nic. Klucz
Adres MAC | ((VirtualEthernetCard)nic). MacAddress (MacAddress)
Adresy IPv4 | Vm. Guest.Net
Adresy IPv6 | Vm. Guest.Net
Przepływność odczytu (MB na sekundę) | net.received.average
Przepływność zapisu (MB na sekundę) | net.transmitted.average
**Szczegóły ścieżki zapasów** | 
Nazwa | Kontenera. GetType(). Nazwa
Typ obiektu podrzędnego | Kontenera. Typ podrzędny
Szczegóły referencyjne | Kontenera. MoRef (właśc.
Dane rodzica | Kontener.Nadrzędny
Szczegóły folderu na maszynę wirtualną | ((Folder)kontener). ChildEntity.Type
Szczegóły centrum danych na maszynę wirtualną | ((Centrum danych)kontener). VmFolder ( VmFolder )
Szczegóły centrum danych na folder hosta | ((Centrum danych)kontener). HostFolder (HostFolder)
Szczegóły klastra na hoście | ((ClusterComputeResource)kontener). Hosta
Szczegóły hosta na maszynę wirtualną | ((HostSystem)kontener). Vm

### <a name="performance-data"></a>Dane wydajności


Oto dane dotyczące wydajności maszyny Wirtualnej VMware, które urządzenie zbiera i wysyła na platformę Azure.

**Dane** | **Licznik** | **Wpływ na ocenę**
--- | --- | ---
Wykorzystanie procesora | cpu.usage.average | Zalecany rozmiar/koszt maszyny Wirtualnej
Wykorzystanie pamięci | mem.usage.average | Zalecany rozmiar/koszt maszyny Wirtualnej
Przepustowość odczytu dysku (MB na sekundę) | virtualDisk.read.average | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Przepływność zapisu dysku (MB na sekundę) | virtualDisk.write.average | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Operacje odczytu dysku na sekundę | virtualDisk.numberReadAveraged.average | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Operacje zapisu dysku na sekundę | virtualDisk.numberWriteAveraged.average  | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Przepustowość odczytu nic (MB na sekundę) | net.received.average | Obliczanie rozmiaru maszyny Wirtualnej
Nic zapisuje przepływność (MB na sekundę) | net.transmitted.average  |Obliczanie rozmiaru maszyny Wirtualnej

### <a name="app-dependencies-metadata"></a>Metadane zależności aplikacji

Analiza zależności bez agenta zbiera dane połączenia i przetwarzania.

#### <a name="connection-data"></a>Dane połączenia

Oto dane połączenia, które urządzenie zbiera z każdej maszyny Wirtualnej włączone dla analizy zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Używane polecenie** 
--- | --- 
Port lokalny | Netstat
Lokalny adres IP | Netstat
Port zdalny | Netstat
Zdalny adres IP | Netstat
Stan połączenia TCP | Netstat
Identyfikator procesu | Netstat
Nie. aktywnych połączeń | Netstat

#### <a name="process-data"></a>Przetwarzanie danych
Oto dane procesu, które urządzenie zbiera z każdej maszyny Wirtualnej włączone dla analizy zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Klasa WMI** | **Właściwość klasy WMI**
--- | --- | ---
Nazwa procesu | Win32_Process | Ścieżka wykonywalna
Argumenty procesu | Win32_Process | Commandline
Nazwa aplikacji | Win32_Process | Parametr VersionInfo.ProductName właściwości ExecutablePath

#### <a name="linux-vm-data"></a>Dane maszyny Wirtualnej systemu Linux

Oto dane połączenia i przetwarzania, które urządzenie zbiera z każdej maszyny Wirtualnej z systemem Linux włączone dla analizy zależności bez agenta. Te dane są wysyłane do platformy Azure.

**Dane** | **Używane polecenie** 
--- | ---
Port lokalny | Netstat 
Lokalny adres IP | Netstat 
Port zdalny | Netstat 
Zdalny adres IP | Netstat 
Stan połączenia TCP | Netstat 
Nie. aktywnych połączeń | Netstat
Identyfikator procesu  | Netstat 
Nazwa procesu | Ps
Argumenty procesu | Ps
Nazwa aplikacji | dpkg lub obr./min



## <a name="collected-data---hyper-v"></a>Zebrane dane - Hyper-V

Urządzenie zbiera metadane, dane wydajności i dane analizy zależności (jeśli używana jest analiza zależności bez [agenta).](concepts-dependency-visualization.md)

### <a name="metadata"></a>Metadane
Metadane wykryte przez urządzenie migracji platformy Azure pomaga ustalić, czy maszyny i aplikacje są gotowe do migracji na platformę Azure, odpowiednie maszyny i aplikacje, planuje koszty i analizuje zależności aplikacji. Firma Microsoft nie używa tych danych w żadnym audycie zgodności licencji.

Oto pełna lista metadanych funkcji Hyper-VM, które urządzenie zbiera i wysyła do platformy Azure.

**Danych* | **KLASA WMI** | **WŁAŚCIWOŚĆ KLASY WMI**
--- | --- | ---
**Szczegóły maszyny** | 
Numer seryjny systemu BIOS _ Msvm_BIOSElement | NUMER BIOSSerialNumer
Typ maszyny Wirtualnej (Gen 1 lub 2) | Msvm_VirtualSystemSettingData | Typ systemu wirtualnego
Nazwa wyświetlana maszyny Wirtualnej | Msvm_VirtualSystemSettingData | ElementName
Wersja maszyny Wirtualnej | Msvm_ProcessorSettingData | VirtualQuantity (VirtualQuantity)
Pamięć (bajty) | Msvm_MemorySettingData | VirtualQuantity (VirtualQuantity)
Maksymalna ilość pamięci, która może być zużywana przez maszynę wirtualną | Msvm_MemorySettingData | Limit
Pamięć dynamiczna włączona | Msvm_MemorySettingData | DynamicMemoryEnabled
Nazwa/wersja systemu operacyjnego/FQDN | Msvm_KvpExchangeComponent | Dane nazwy guestintrinsicexchangeItems
Stan zasilania maszyny Wirtualnej | Msvm_ComputerSystem | EnabledState
**Szczegóły na dysku** | 
Identyfikator dysku | Msvm_VirtualHardDiskSettingData | Identyfikator wirtualny
Typ wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Typ
Rozmiar wirtualnego dysku twardego | Msvm_VirtualHardDiskSettingData | Rozmiar MaxInternalSize
Wirtualny dysk twardy nadrzędny | Msvm_VirtualHardDiskSettingData | ParentPath
**Na szczegóły karty sieciowej** | 
Adresy IP (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | Adres IP IPAddresses
Usługa DHCP z włączoną funkcją (syntetyczne karty sieciowe) | Msvm_GuestNetworkAdapterConfiguration | Usługa DHCPEnabled
Identyfikator karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adres MAC karty sieciowej (syntetyczne karty sieciowe) | Msvm_SyntheticEthernetPortSettingData | Adres
Identyfikator karty sieciowej (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting Dane | InstanceID
Identyfikator MAC nic (starsze karty sieciowe) | MsvmEmulatedEthernetPortSetting Dane | Adres

### <a name="performance-data"></a>Dane wydajności

Oto dane dotyczące wydajności maszyny Wirtualnej hyper, które urządzenie zbiera i wysyła do platformy Azure.

**Klasa licznika wydajności** | **Licznik** | **Wpływ na ocenę**
--- | --- | ---
Procesor wirtualny Hypervisor Hyper-V | % czasu wykonywania gościa | Zalecany rozmiar/koszt maszyny Wirtualnej
Maszyna wirtualna pamięci dynamicznej funkcji Hyper-V | Ciśnienie prądu (%)<br/> Widoczna pamięć fizyczna gościa (MB) | Zalecany rozmiar/koszt maszyny Wirtualnej
Wirtualne urządzenie pamięci masowej Hyper-V | Odczyt bajtów/sekundę | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Wirtualne urządzenie pamięci masowej Hyper-V | Bajty zapisu na sekundę | Obliczanie rozmiaru dysku, kosztu magazynowania, rozmiaru maszyny Wirtualnej
Karta sieci wirtualnej Hyper-V | Bajty odebrane/sekundę | Obliczanie rozmiaru maszyny Wirtualnej
Karta sieci wirtualnej Hyper-V | Bajty wysłane/sekundę | Obliczanie rozmiaru maszyny Wirtualnej

- Wykorzystanie procesora CPU jest sumą wszystkich użycia dla wszystkich procesorów wirtualnych dołączonych do maszyny Wirtualnej.
- Wykorzystanie pamięci jest (ciśnienie prądu * Gość Visible Physical Memory) / 100.
- Wartości wykorzystania dysku i sieci są zbierane z wymienionych liczników wydajności funkcji Hyper-V.

## <a name="appliance-upgrades"></a>Modernizacje urządzeń

Urządzenie jest uaktualniane, jak agenci migracji platformy Azure uruchomione na urządzeniu są aktualizowane. Dzieje się tak automatycznie, ponieważ automatyczna aktualizacja jest domyślnie włączona na urządzeniu. To ustawienie domyślne można zmienić, aby ręcznie zaktualizować agentów.

- **Wyłącz automatyczną aktualizację:** Wyłącz automatyczną aktualizację w rejestrze, ustawiając HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" na 0 (DWORD). Jeśli zdecydujesz się użyć ręcznych aktualizacji, ważne jest, aby zaktualizować wszystkich agentów na urządzeniu w tym samym czasie, za pomocą przycisku **Aktualizuj** dla każdego przestarzałego agenta na urządzeniu.
- **Aktualizacja ręcznie:** W przypadku aktualizacji ręcznych należy zaktualizować wszystkie agentów na urządzeniu, używając przycisku **Aktualizuj** dla każdego przestarzałego agenta na urządzeniu. Ustawienie aktualizacji można przełączyć z powrotem na aktualizacje automatyczne w dowolnym momencie.

![Automatyczne aktualizowanie urządzenia](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak](how-to-set-up-appliance-vmware.md) skonfigurować urządzenie dla VMware.
- [Dowiedz się, jak](how-to-set-up-appliance-hyper-v.md) skonfigurować urządzenie dla funkcji Hyper-V.
- [Dowiedz się, jak](how-to-set-up-appliance-physical.md) skonfigurować urządzenie dla serwerów fizycznych.

