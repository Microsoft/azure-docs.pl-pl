---
title: Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure
titlesuffix: Azure Virtual Network
description: Scenariusze rozpoznawania nazw dla platformy Azure IaaS, rozwiązań hybrydowych, między różnymi usługami w chmurze, Active Directory i korzystania z własnego serwera DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222857"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure

W zależności od tego, jak korzystasz z platformy Azure do hostowania rozwiązań IaaS, PaaS i hybrydowych, może być konieczne zezwolenie maszynom wirtualnym i innym zasobom wdrożonym w sieci wirtualnej na komunikowanie się ze sobą. Mimo że można włączyć komunikację przy użyciu adresów IP, łatwiej jest używać nazw, które mogą być w łatwy sposób zapamiętywane i nie zmieniać. 

Gdy zasoby wdrożone w sieciach wirtualnych muszą rozpoznawać nazwy domen jako wewnętrzne adresy IP, mogą korzystać z jednej z trzech metod:

* [Azure DNS stref prywatnych](../dns/private-dns-overview.md)
* [Rozpoznawanie nazw udostępniane przez platformę Azure](#azure-provided-name-resolution)
* [Rozpoznawanie nazw wykorzystujące własny serwer DNS](#name-resolution-that-uses-your-own-dns-server) (co może przekazywać zapytania do serwerów DNS udostępnianych przez platformę Azure)

Używany typ rozpoznawania nazw zależy od tego, w jaki sposób zasoby muszą komunikować się ze sobą. W poniższej tabeli przedstawiono scenariusze i odpowiednie rozwiązania rozpoznawania nazw:

> [!NOTE]
> Azure DNS strefach prywatnych jest preferowanym rozwiązaniem i zapewnia elastyczność zarządzania strefami i rekordami DNS. Aby uzyskać więcej informacji, zobacz [Using Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](../dns/private-dns-overview.md).

> [!NOTE]
> Jeśli używasz systemu DNS z systemem Azure, odpowiedni sufiks DNS zostanie automatycznie zastosowany do maszyn wirtualnych. W przypadku wszystkich innych opcji należy użyć w pełni kwalifikowanych nazw domen (FQDN) lub ręcznie zastosować odpowiedni sufiks DNS do maszyn wirtualnych.

| **Scenariusz** | **Rozwiązanie** | **Sufiks DNS** |
| --- | --- | --- |
| Rozpoznawanie nazw między maszynami wirtualnymi znajdującymi się w tej samej sieci wirtualnej lub wystąpieniami roli Cloud Services platformy Azure w tej samej usłudze w chmurze. | [Azure DNS stref prywatnych](../dns/private-dns-overview.md) lub [rozpoznawania nazw udostępnianych przez platformę Azure](#azure-provided-name-resolution) |Nazwa hosta lub nazwa FQDN |
| Rozpoznawanie nazw między maszynami wirtualnymi w różnych sieciach wirtualnych lub wystąpieniach ról w różnych usługach w chmurze. |[Azure DNS strefy prywatne](../dns/private-dns-overview.md) lub serwery DNS zarządzane przez klienta przekazujące zapytania między sieciami wirtualnymi w celu rozpoznania ich przez platformę Azure (DNS proxy). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw z Azure App Service (aplikacja sieci Web, funkcja lub bot) przy użyciu integracji sieci wirtualnej z wystąpieniami roli lub maszynami wirtualnymi w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłają zapytania między sieciami wirtualnymi w celu rozpoznawania przez platformę Azure (DNS proxy). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw z App Service Web Apps do maszyn wirtualnych w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłają zapytania między sieciami wirtualnymi w celu rozpoznawania przez platformę Azure (DNS proxy). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw od App Service Web Apps w jednej sieci wirtualnej do maszyn wirtualnych w innej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłają zapytania między sieciami wirtualnymi w celu rozpoznawania przez platformę Azure (DNS proxy). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie lokalnych nazw komputerów i usług z maszyn wirtualnych lub wystąpień ról na platformie Azure. |Serwery DNS zarządzane przez klienta (kontroler domeny lokalnej, lokalny kontroler domeny tylko do odczytu lub pomocniczy serwer DNS z użyciem transferów stref, na przykład). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw hostów platformy Azure z komputerów lokalnych. |Przekazuj zapytania do serwera proxy DNS zarządzanego przez klienta w odpowiedniej sieci wirtualnej, serwer proxy przekazuje zapytania do platformy Azure w celu rozwiązania problemu. Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Zwrotny serwer DNS dla wewnętrznych adresów IP. |[Azure DNS stref prywatnych](../dns/private-dns-overview.md) lub [rozpoznawania nazw udostępnianych przez platformę Azure](#azure-provided-name-resolution) lub [rozpoznawania nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Nie dotyczy |
| Rozpoznawanie nazw między maszynami wirtualnymi lub wystąpieniami ról znajdującymi się w różnych usługach w chmurze, a nie w sieci wirtualnej. |Nie dotyczy. Łączność między maszynami wirtualnymi i wystąpieniami ról w różnych usługach w chmurze nie jest obsługiwana poza siecią wirtualną. |Nie dotyczy|

## <a name="azure-provided-name-resolution"></a>Rozpoznawanie nazw udostępniane przez platformę Azure

Rozpoznawanie nazw udostępniane przez platformę Azure zapewnia tylko podstawowe autorytatywne funkcje DNS. W przypadku użycia tej opcji nazwy stref DNS i rekordy będą automatycznie zarządzane przez platformę Azure i nie będzie można kontrolować nazw stref DNS ani cyklu życia rekordów DNS. Jeśli potrzebne jest w pełni proponowane rozwiązanie DNS dla sieci wirtualnych, należy użyć [Azure DNS stref prywatnych](../dns/private-dns-overview.md) lub [serwerów DNS zarządzanych przez klienta](#name-resolution-that-uses-your-own-dns-server).

Podobnie jak w przypadku nazw publicznych DNS, platforma Azure udostępnia wewnętrzne rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról znajdujących się w tej samej sieci wirtualnej lub w ramach usługi w chmurze. Maszyny wirtualne i wystąpienia w usłudze w chmurze współużytkują ten sam sufiks DNS, więc wystarczy sama nazwa hosta. Jednak w przypadku sieci wirtualnych wdrożonych przy użyciu klasycznego modelu wdrażania różne usługi w chmurze mają różne sufiksy DNS. W takiej sytuacji do rozpoznawania nazw między różnymi usługami w chmurze potrzebna jest nazwa FQDN. W przypadku sieci wirtualnych wdrożonych przy użyciu Azure Resource Manager model wdrażania sufiks DNS jest spójny dla wszystkich maszyn wirtualnych w ramach sieci wirtualnej, więc nazwa FQDN nie jest wymagana. Nazwy DNS można przypisywać zarówno do maszyn wirtualnych, jak i interfejsów sieciowych. Chociaż rozpoznawanie nazw udostępniane przez platformę Azure nie wymaga żadnej konfiguracji, nie jest to odpowiedni wybór dla wszystkich scenariuszy wdrażania, zgodnie z opisem w poprzedniej tabeli.

> [!NOTE]
> W przypadku korzystania z ról Sieć Web i proces roboczy usług Cloud Services można także uzyskać dostęp do wewnętrznych adresów IP wystąpień ról przy użyciu interfejsu API REST usługi Azure Service Management. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API REST zarządzania usługami](/previous-versions/azure/ee460799(v=azure.100)). Adres jest oparty na nazwie roli i numerze wystąpienia. 
>

### <a name="features"></a>Funkcje

Rozpoznawanie nazw udostępniane przez platformę Azure obejmuje następujące funkcje:
* Łatwość użycia. Nie jest wymagana żadna konfiguracja.
* Wysoka dostępność. Nie musisz tworzyć klastrów własnych serwerów DNS i zarządzać nimi.
* Możesz użyć usługi w połączeniu z własnymi serwerami DNS, aby rozwiązać zarówno lokalne, jak i nazwy hostów platformy Azure.
* Można użyć rozpoznawania nazw między maszynami wirtualnymi i wystąpieniami roli w ramach tej samej usługi w chmurze bez konieczności używania nazwy FQDN.
* Można użyć rozpoznawania nazw między maszynami wirtualnymi w sieciach wirtualnych, które używają Azure Resource Manager modelu wdrażania, bez konieczności używania nazwy FQDN. Sieci wirtualne w klasycznym modelu wdrażania wymagają nazwy FQDN podczas rozpoznawania nazw w różnych usługach w chmurze. 
* Można użyć nazw hostów, które najlepiej opisują Twoje wdrożenia, zamiast korzystać z automatycznie generowanych nazw.

### <a name="considerations"></a>Zagadnienia do rozważenia

Kwestie, które należy wziąć pod uwagę podczas korzystania z rozpoznawania nazw udostępnianej przez platformę Azure:
* Nie można zmodyfikować sufiksu DNS utworzonego przez platformę Azure.
* Funkcja wyszukiwania DNS jest objęta zakresem sieci wirtualnej. Nie można rozpoznać nazw DNS utworzonych dla jednej sieci wirtualnej z innych sieci wirtualnych.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługi WINS i NetBIOS nie są obsługiwane. Nie można zobaczyć maszyn wirtualnych w Eksploratorze Windows.
* Nazwy hostów muszą być zgodne z systemem DNS. Nazwy muszą używać tylko 0-9, a-z i "-" i nie mogą rozpoczynać się ani kończyć znakiem "-".
* Ruch zapytań DNS jest ograniczany dla każdej maszyny wirtualnej. Ograniczanie przepływności nie powinno mieć wpływu na większość aplikacji. Jeśli zaobserwowane jest ograniczanie żądań, upewnij się, że buforowanie po stronie klienta jest włączone. Aby uzyskać więcej informacji, zobacz [Konfiguracja klienta DNS](#dns-client-configuration).
* Dla każdej sieci wirtualnej w klasycznym modelu wdrażania są rejestrowane tylko maszyny wirtualne w pierwszych 180 usługach w chmurze. Ten limit nie dotyczy sieci wirtualnych w Azure Resource Manager.
* Azure DNS adres IP to 168.63.129.16. Jest to statyczny adres IP, który nie ulegnie zmianie.

### <a name="reverse-dns-considerations"></a>Odwróć uwagi DNS
Odwrotny serwer DNS jest obsługiwany we wszystkich sieciach wirtualnych opartych na usłudze ARM. Można wydać odwrotne kwerendy DNS (kwerendy PTR), aby mapować adresy IP maszyn wirtualnych na nazwy FQDN maszyn wirtualnych.
* Wszystkie zapytania PTR dla adresów IP maszyn wirtualnych będą zwracać nazwy FQDN formularza \[ VMName \] . Internal.cloudapp.NET
* Wyszukiwanie do przodu w nazwach FQDN formularza \[ VMName \] . Internal.cloudapp.NET zostanie rozpoznane jako adres IP przypisany do maszyny wirtualnej.
* Jeśli sieć wirtualna jest połączona z [Azure DNS strefami prywatnymi](../dns/private-dns-overview.md) jako sieć wirtualną rejestracji, zapytania ODWROTNe DNS zwrócą dwa rekordy. Jeden rekord będzie miał postać \[ VMName \] . [ privatednszonename], a drugi będzie miał postać \[ VMName \] . Internal.cloudapp.NET
* Odwrotne wyszukiwanie DNS jest ograniczone do danej sieci wirtualnej, nawet jeśli jest połączona z innymi sieciami wirtualnymi. Odwrotne zapytania DNS (kwerendy PTR) dla adresów IP maszyn wirtualnych znajdujących się w równorzędnych sieciach wirtualnych będą zwracały NXDOMAIN.
* Jeśli chcesz wyłączyć odwrotną funkcję DNS w sieci wirtualnej, możesz to zrobić, tworząc strefę wyszukiwania wstecznego przy użyciu [stref prywatnych Azure DNS](../dns/private-dns-overview.md) i Połącz tę strefę z siecią wirtualną. Na przykład jeśli przestrzeń adresów IP sieci wirtualnej to 10.20.0.0/16, można utworzyć pustą prywatną strefę DNS 20.10.in-addr. arpa i połączyć ją z siecią wirtualną. Podczas łączenia strefy z siecią wirtualną należy wyłączyć rejestrację autorejestrowania na łączu. Ta strefa zastąpi domyślne strefy wyszukiwania wstecznego dla sieci wirtualnej i ponieważ ta strefa jest pusta, uzyskasz NXDOMAIN dla zapytań wstecz DNS. Zapoznaj się z naszym [przewodnikiem Szybki Start](../dns/private-dns-getstarted-portal.md) , aby uzyskać szczegółowe informacje na temat sposobu tworzenia prywatnej strefy DNS i łączenia jej z siecią wirtualną.

> [!NOTE]
> Jeśli chcesz, aby odwrotne wyszukiwanie DNS było podzielone między sieciami wirtualnymi, możesz utworzyć strefę wyszukiwania wstecznego (w-addr. arpa) [Azure DNS strefy prywatne](../dns/private-dns-overview.md) i połączyć ją z wieloma sieciami wirtualnymi. Konieczne będzie jednak ręczne Zarządzanie rekordami odwrotnego systemu DNS dla maszyn wirtualnych.
>


## <a name="dns-client-configuration"></a>Konfiguracja klienta DNS

Ta sekcja dotyczy buforowania po stronie klienta i ponownych prób po stronie klienta.

### <a name="client-side-caching"></a>Buforowanie po stronie klienta

Nie wszystkie zapytania DNS muszą być przesyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszyć opóźnienia i zwiększyć odporność usługi Network Blips, rozwiązując cykliczne zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają mechanizm czasu wygaśnięcia (TTL, Time-to-Live), który umożliwia przechowywanie rekordów w pamięci podręcznej tak długo, jak to możliwe, bez wpływu na świeżość rekordów. W ten sposób buforowanie po stronie klienta jest odpowiednie dla większości sytuacji.

Domyślny klient DNS systemu Windows ma wbudowaną pamięć podręczną usługi DNS. Niektóre dystrybucje systemu Linux domyślnie nie obejmują buforowania. Jeśli okaże się, że nie ma już lokalnej pamięci podręcznej, Dodaj pamięć podręczną DNS do każdej maszyny wirtualnej z systemem Linux.

Dostępnych jest wiele różnych pakietów buforowania DNS (takich jak dnsmasq). Poniżej przedstawiono sposób instalowania dnsmasq na najbardziej typowych dystrybucjach:

* **Ubuntu (używa resolvconf)**:
  * Zainstaluj pakiet dnsmasq przy użyciu programu `sudo apt-get install dnsmasq` .
* **SUSE (używa serviceconf)**:
  * Zainstaluj pakiet dnsmasq przy użyciu programu `sudo zypper install dnsmasq` .
  * Włącz usługę dnsmasq przy użyciu usługi `systemctl enable dnsmasq.service` . 
  * Uruchom usługę dnsmasq za pomocą programu `systemctl start dnsmasq.service` . 
  * Edytuj **/etc/sysconfig/Network/config** i zmień wartość *NETCONFIG_DNS_FORWARDER = ""* na *dnsmasq*.
  * Zaktualizuj plik resolv. conf z `netconfig update` , aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.
* **CentOS (korzysta z gniazd sieciowych)**:
  * Zainstaluj pakiet dnsmasq przy użyciu programu `sudo yum install dnsmasq` .
  * Włącz usługę dnsmasq przy użyciu usługi `systemctl enable dnsmasq.service` .
  * Uruchom usługę dnsmasq za pomocą programu `systemctl start dnsmasq.service` .
  * Dodaj *ciąg Domain-Name-Server 127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Uruchom ponownie usługę sieciową za pomocą programu `service network restart` , aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.

> [!NOTE]
> Pakiet dnsmasq jest tylko jedną z wielu pamięci podręcznych DNS dostępnych dla systemu Linux. Przed użyciem sprawdź jego przydatność do określonych potrzeb i sprawdź, czy nie zainstalowano żadnej innej pamięci podręcznej.

    
### <a name="client-side-retries"></a>Ponowne próby po stronie klienta

System DNS jest przede wszystkim protokołem UDP. Ponieważ protokół UDP nie gwarantuje dostarczania komunikatów, logika ponowień jest obsługiwana w samym protokole DNS. Każdy klient DNS (system operacyjny) może wyróżnić inną logikę ponowień, w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponowią próbę po upływie jednej sekundy, a następnie ponownie po upływie dwóch sekund, cztery sekundy i inne cztery sekundy. 
* Domyślna instalacja systemu Linux ponawia próbę po upływie pięciu sekund. Zalecamy zmianę specyfikacji ponownych prób na pięć razy, w odstępach jednosekundowych.

Sprawdź bieżące ustawienia na maszynie wirtualnej z systemem Linux za pomocą programu `cat /etc/resolv.conf` . Zapoznaj się z wierszem *opcji* , na przykład:

```bash
options timeout:1 attempts:5
```

Plik plik resolv. conf jest zwykle generowany automatycznie i nie powinien być edytowany. Poszczególne kroki dotyczące dodawania linii *opcji* różnią się w zależności od dystrybucji:

* **Ubuntu** (używa resolvconf):
  1. Dodaj wiersz *opcji* do **/etc/resolvconf/resolv.conf.d/tail**.
  2. Uruchom `resolvconf -u` , aby zaktualizować.
* **SUSE** (używa serviceconf):
  1. Dodaj *limit czasu: 1 próby: 5* do parametru **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** w **/etc/sysconfig/Network/config**.
  2. Uruchom `netconfig update` , aby zaktualizować.
* **CentOS** (korzysta z gniazd sieciowych):
  1. Dodaj *Echo "Opcje limitu czasu: 1 próby: 5"* do **/etc/NetworkManager/Dispatcher.d/11-dhclient**.
  2. Aktualizuj przy użyciu programu `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozpoznawanie nazw wykorzystujące własny serwer DNS

Ta sekcja obejmuje maszyny wirtualne, wystąpienia ról i aplikacje sieci Web.

### <a name="vms-and-role-instances"></a>Maszyny wirtualne i wystąpienia ról

Wymagania dotyczące rozpoznawania nazw mogą wykraczać poza funkcje udostępniane przez platformę Azure. Na przykład może być konieczne użycie domen programu Microsoft Windows Server Active Directory, rozpoznanie nazw DNS między sieciami wirtualnymi. Aby uwzględnić te scenariusze, platforma Azure umożliwia korzystanie z własnych serwerów DNS.

Serwery DNS w sieci wirtualnej mogą przekazywać zapytania DNS do rozpoznawania cyklicznego na platformie Azure. Pozwala to na rozpoznawanie nazw hostów w ramach tej sieci wirtualnej. Na przykład kontroler domeny (DC) uruchomiony na platformie Azure może odpowiadać na zapytania DNS dotyczące swoich domen i przekazywać wszystkie inne zapytania do platformy Azure. Przekazywanie zapytań umożliwia maszynom wirtualnym wyświetlanie zasobów lokalnych (za pośrednictwem kontrolera domeny) i nazw hostów udostępnianych przez platformę Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do tłumaczeń cyklicznych na platformie Azure jest udostępniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przesyłanie dalej DNS umożliwia również rozpoznawanie nazw DNS między sieciami wirtualnymi i pozwala komputerom lokalnym rozpoznać nazwy hostów podane przez platformę Azure. Aby można było rozpoznać nazwę hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i być skonfigurowana do przekazywania zapytań o nazwy hosta do platformy Azure. Ponieważ sufiks DNS różni się w każdej sieci wirtualnej, można użyć reguł przekazywania warunkowego do wysyłania zapytań DNS do odpowiedniej sieci wirtualnej w celu rozwiązania tego problemu. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalna wykonująca rozpoznawanie nazw DNS między sieciami wirtualnymi za pomocą tej metody. Przykład usługi przesyłania dalej DNS jest dostępny w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) i w serwisie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Wystąpienie roli może przeprowadzać rozpoznawanie nazw maszyn wirtualnych w ramach tej samej sieci wirtualnej. Robi to za pomocą nazwy FQDN, która składa się z nazw hosta maszyny wirtualnej i sufiksu DNS **Internal.cloudapp.NET** . Jednak w takim przypadku rozpoznawanie nazw powiedzie się tylko wtedy, gdy wystąpienie roli ma zdefiniowaną nazwę maszyny wirtualnej w [schemacie roli (plik cscfg)](/previous-versions/azure/reference/jj156212(v=azure.100)).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Wystąpienia roli, które wymagają rozpoznawania nazw maszyn wirtualnych w innej sieci wirtualnej (nazwa FQDN przy użyciu sufiksu **Internal.cloudapp.NET** ), należy wykonać przy użyciu metody opisanej w tej sekcji (przekazujące niestandardowe serwery DNS między dwiema sieciami wirtualnymi).
>

![Diagram systemu DNS między sieciami wirtualnymi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

W przypadku korzystania z rozpoznawania nazw udostępnianej przez platformę Azure usługa Azure Dynamic Host Configuration Protocol (DHCP) udostępnia wewnętrzny sufiks DNS (**Internal.cloudapp.NET**) do każdej maszyny wirtualnej. Ten sufiks umożliwia rozpoznawanie nazw hostów, ponieważ rekordy nazw hostów znajdują się w strefie **Internal.cloudapp.NET** . Jeśli używasz własnego rozwiązania rozpoznawania nazw, ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ zakłóca inne architektury DNS (na przykład scenariusze przyłączone do domeny). Zamiast tego, platforma Azure udostępnia niedziałający symbol zastępczy (*reddog.Microsoft.com*).

W razie potrzeby można określić wewnętrzny sufiks DNS przy użyciu programu PowerShell lub interfejsu API:

* W przypadku sieci wirtualnych w Azure Resource Manager modele wdrażania sufiks jest dostępny za pośrednictwem interfejsu [API REST interfejsu sieciowego](/rest/api/virtualnetwork/networkinterfaces), polecenia cmdlet programu PowerShell [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) i polecenia [AZ Network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI.
* W klasycznych modelach wdrażania sufiks jest dostępny za pośrednictwem wywołania [interfejsu API pobierania](/previous-versions/azure/reference/ee460804(v=azure.100)) lub polecenia cmdlet [Get-AzureVM-Debug](/powershell/module/servicemanagement/azure.service/get-azurevm) .

Jeśli przekazywanie zapytań do platformy Azure nie odpowiada Twoim potrzebom, należy podać własne rozwiązanie DNS. Rozwiązanie DNS musi:

* Na przykład podaj odpowiednie rozpoznawanie nazw hostów za pośrednictwem [DDNS](virtual-networks-name-resolution-ddns.md). Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczania rekordów DNS. Dzierżawy usługi DHCP platformy Azure są długie, a oczyszczanie może spowodować przedwczesne usunięcie rekordów DNS. 
* Podaj odpowiednie rozwiązanie cykliczne, aby umożliwić rozpoznanie zewnętrznych nazw domen.
* Dostęp do Internetu (TCP i UDP na porcie 53) od obsługiwanych przez niego klientów
* Zabezpieczanie przed dostępem z Internetu w celu ograniczenia zagrożeń powodowanych przez agentów zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, podczas korzystania z maszyn wirtualnych platformy Azure jako serwerów DNS należy wyłączyć protokół IPv6.

### <a name="web-apps"></a>Aplikacje internetowe
Załóżmy, że konieczne jest przeprowadzenie rozpoznawania nazw z aplikacji sieci Web utworzonej przy użyciu App Service połączonej z siecią wirtualną z maszynami wirtualnymi w tej samej sieci wirtualnej. Oprócz konfigurowania niestandardowego serwera DNS, który ma usługę przesyłania dalej DNS, która przekazuje zapytania do platformy Azure (Virtual IP 168.63.129.16), wykonaj następujące czynności:
1. Włącz integrację sieci wirtualnej dla aplikacji sieci Web, jeśli nie została jeszcze wykonana, zgodnie z opisem w temacie [Integrowanie aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. W Azure Portal, w przypadku planu App Service hostujący aplikację sieci Web, wybierz pozycję **Synchronizuj sieć** w obszarze **Sieć**, **Virtual Network integrację**.

    ![Zrzut ekranu przedstawiający rozpoznawanie nazw sieci wirtualnej](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Jeśli konieczne jest przeprowadzenie rozpoznawania nazw z aplikacji sieci Web skompilowanej przy użyciu App Service połączonej z siecią wirtualną do maszyn wirtualnych w innej sieci wirtualnej, należy użyć niestandardowych serwerów DNS w obu sieciach wirtualnych w następujący sposób:

* Skonfiguruj serwer DNS w docelowej sieci wirtualnej na maszynie wirtualnej, która może również przekazywać zapytania do programu rozpoznawania cyklicznego na platformie Azure (wirtualny adres IP 168.63.129.16). Przykład usługi przesyłania dalej DNS jest dostępny w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) i w serwisie [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Skonfiguruj usługę przesyłania dalej DNS w źródłowej sieci wirtualnej na maszynie wirtualnej. Skonfiguruj usługę przesyłania dalej DNS do przesyłania dalej zapytań do serwera DNS w docelowej sieci wirtualnej.
* Skonfiguruj źródłowy serwer DNS w ustawieniach źródłowej sieci wirtualnej.
* Włącz integrację sieci wirtualnej dla aplikacji sieci Web, aby połączyć się ze źródłową siecią wirtualną, postępując zgodnie z instrukcjami w temacie [Integrowanie aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* W Azure Portal, w przypadku planu App Service hostujący aplikację sieci Web, wybierz pozycję **Synchronizuj sieć** w obszarze **Sieć**, **Virtual Network integrację**.

## <a name="specify-dns-servers"></a>Określ serwery DNS
Jeśli używasz własnych serwerów DNS, platforma Azure umożliwia określenie wielu serwerów DNS na potrzeby sieci wirtualnej. Można również określić wiele serwerów DNS dla każdego interfejsu sieciowego (w przypadku usługi Azure Resource Manager) lub dla usługi w chmurze (w przypadku klasycznego modelu wdrażania). Serwery DNS określone dla interfejsu sieciowego lub usługi w chmurze mają pierwszeństwo przed serwerami DNS określonymi dla sieci wirtualnej.

> [!NOTE]
> Właściwości połączenia sieciowego, takie jak adresy IP serwera DNS, nie powinny być edytowane bezpośrednio w maszynach wirtualnych. Dzieje się tak, ponieważ mogą one zostać wymazane podczas stosowania poprawek do usługi, gdy karta sieci wirtualnej zostanie zastąpiona. Dotyczy to zarówno maszyn wirtualnych z systemem Windows, jak i Linux.

W przypadku korzystania z modelu wdrażania Azure Resource Manager można określić serwery DNS dla sieci wirtualnej i interfejsu sieciowego. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie siecią wirtualną](manage-virtual-network.md) i [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md).

> [!NOTE]
> Jeśli wybierzesz niestandardowy serwer DNS dla sieci wirtualnej, musisz określić co najmniej jeden adres IP serwera DNS. w przeciwnym razie Sieć wirtualna zignoruje konfigurację i użyje usługi DNS udostępnionej przez platformę Azure.

W przypadku korzystania z klasycznego modelu wdrażania można określić serwery DNS dla sieci wirtualnej w Azure Portal lub w [pliku konfiguracji sieci](/previous-versions/azure/reference/jj157100(v=azure.100)). W przypadku usług Cloud Services serwery DNS można określić za pomocą [pliku konfiguracji usługi](/previous-versions/azure/reference/ee758710(v=azure.100)) lub programu PowerShell z poleceniem [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> W przypadku zmiany ustawień DNS dla sieci wirtualnej lub maszyny wirtualnej, która została już wdrożona, aby nowe ustawienia DNS zaczęły obowiązywać, należy przeprowadzić odnowienie dzierżawy DHCP na wszystkich maszynach wirtualnych, których dotyczy ta sieć wirtualna. W przypadku maszyn wirtualnych z systemem operacyjnym Windows można to zrobić, wpisując je `ipconfig /renew` bezpośrednio na maszynie wirtualnej. Kroki różnią się w zależności od systemu operacyjnego. Zapoznaj się z odpowiednią dokumentacją dla danego typu systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

Azure Resource Manager model wdrażania:

* [Zarządzanie siecią wirtualną](manage-virtual-network.md)
* [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md)

Klasyczny model wdrażania:

* [Schemat konfiguracji usługi platformy Azure](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Schemat konfiguracji Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Konfigurowanie Virtual Network przy użyciu pliku konfiguracji sieci](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)