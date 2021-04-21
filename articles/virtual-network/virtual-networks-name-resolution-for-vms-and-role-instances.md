---
title: Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure
titlesuffix: Azure Virtual Network
description: Scenariusze rozpoznawania nazw dla usługi Azure IaaS, rozwiązań hybrydowych między różnymi usługami w chmurze, usługą Active Directory i użyciem własnego serwera DNS.
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
ms.openlocfilehash: 80a0b4634a2e84181271b515d2f6f63271cce7f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784969"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure

W zależności od sposobu hostowania rozwiązań IaaS, PaaS i hybrydowych na platformie Azure może być konieczne umożliwienie maszynom wirtualnym i innym zasobom wdrożonym w sieci wirtualnej komunikowania się ze sobą. Chociaż komunikację można włączyć przy użyciu adresów IP, znacznie łatwiej jest użyć nazw, które można łatwo zapamiętać i które nie zmieniają się. 

Gdy zasoby wdrożone w sieciach wirtualnych muszą rozstrzygnieć nazwy domen jako wewnętrzne adresy IP, można użyć jednej z trzech metod:

* [Azure DNS stref prywatnych](../dns/private-dns-overview.md)
* [Rozpoznawania nazw dostarczanych przez platformę Azure](#azure-provided-name-resolution)
* [Rozpoznawanie nazw, które używa własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server) (który może przesyłać zapytania do serwerów DNS dostarczanych przez platformę Azure)

Używany typ rozpoznawania nazw zależy od tego, w jaki sposób zasoby muszą komunikować się ze sobą. W poniższej tabeli przedstawiono scenariusze i odpowiednie rozwiązania do rozpoznawania nazw:

> [!NOTE]
> Azure DNS jest preferowanym rozwiązaniem i zapewnia elastyczność zarządzania strefami i rekordami DNS. Aby uzyskać więcej informacji, zobacz [Using Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](../dns/private-dns-overview.md).

> [!NOTE]
> Jeśli używasz usługi DNS udostępnianej przez platformę Azure, odpowiedni sufiks DNS zostanie automatycznie zastosowany do maszyn wirtualnych. Dla wszystkich innych opcji należy użyć w pełni kwalifikowanych nazw domen (FQDN) lub ręcznie zastosować odpowiedni sufiks DNS do maszyn wirtualnych.

| **Scenariusz** | **Rozwiązanie** | **Sufiks DNS** |
| --- | --- | --- |
| Rozpoznawania nazw między maszynami wirtualnymi zlokalizowanymi w tej samej sieci wirtualnej lub Azure Cloud Services wystąpień roli w tej samej usłudze w chmurze. | [Azure DNS prywatnych lub rozpoznawania](../dns/private-dns-overview.md) [nazw dostarczanych przez platformę Azure](#azure-provided-name-resolution) |Nazwa hosta lub nazwa FQDN |
| Rozpoznawania nazw między maszynami wirtualnymi w różnych sieciach wirtualnych lub wystąpieniach roli w różnych usługach w chmurze. |[Azure DNS stref prywatnych lub,](../dns/private-dns-overview.md) serwery DNS zarządzane przez klienta, które wysyłają zapytania między sieciami wirtualnymi do rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko WQDN |
| Rozpoznawania nazw z Azure App Service (aplikacji internetowej, funkcji lub bota) przy użyciu integracji z siecią wirtualną do wystąpień ról lub maszyn wirtualnych w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta, które wysyłają zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko w witrynie FQDN |
| Rozpoznawania nazw z App Service Web Apps maszyn wirtualnych w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta, które wysyłają zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko w witrynie FQDN |
| Rozpoznawania nazw z App Service Web Apps jednej sieci wirtualnej do maszyn wirtualnych w innej sieci wirtualnej. |Serwery DNS zarządzane przez klienta, które wysyłają zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko w witrynie FQDN |
| Rozdzielczość nazw lokalnych komputerów i usług z maszyn wirtualnych lub wystąpień ról na platformie Azure. |Serwery DNS zarządzane przez klienta (na przykład lokalny kontroler domeny, lokalny kontroler domeny tylko do odczytu lub pomocniczy serwer DNS synchronizowany przy użyciu transferów strefy). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko w witrynie FQDN |
| Rozpoznawania nazw hostów platformy Azure z komputerów lokalnych. |Przesyła dalej zapytania do serwera proxy DNS zarządzanego przez klienta w odpowiedniej sieci wirtualnej, a serwer proxy przekazuje zapytania do platformy Azure w celu rozwiązania problemu. Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Tylko w witrynie FQDN |
| Odwrotny system DNS dla wewnętrznych ip. |[Azure DNS stref prywatnych,](../dns/private-dns-overview.md) rozpoznawania [nazw dostarczanych](#azure-provided-name-resolution) przez platformę Azure lub rozpoznawania [nazw przy użyciu własnego serwera DNS.](#name-resolution-that-uses-your-own-dns-server) |Nie dotyczy |
| Rozpoznawania nazw między maszynami wirtualnymi lub wystąpieniami roli zlokalizowanymi w różnych usługach w chmurze, a nie w sieci wirtualnej. |Nie dotyczy. Łączność między maszynami wirtualnymi i wystąpieniami roli w różnych usługach w chmurze nie jest obsługiwana poza siecią wirtualną. |Nie dotyczy|

## <a name="azure-provided-name-resolution"></a>Rozpoznawania nazw dostarczanych przez platformę Azure

Rozpoznawanie nazw udostępniane przez platformę Azure zapewnia tylko podstawowe autorytatywne możliwości systemu DNS. W przypadku użycia tej opcji nazwy i rekordy strefy DNS będą automatycznie zarządzane przez platformę Azure i nie będzie można kontrolować nazw stref DNS ani cyklu życia rekordów DNS. Jeśli potrzebujesz w pełni funkcjonalnego rozwiązania DNS [](../dns/private-dns-overview.md) dla sieci wirtualnych, musisz użyć Azure DNS prywatnych stref lub serwerów [DNS zarządzanych przez klienta.](#name-resolution-that-uses-your-own-dns-server)

Oprócz rozpoznawania publicznych nazw DNS platforma Azure zapewnia wewnętrzne rozpoznawanie nazw maszyn wirtualnych i wystąpień roli, które znajdują się w tej samej sieci wirtualnej lub usłudze w chmurze. Maszyny wirtualne i wystąpienia w usłudze w chmurze mają ten sam sufiks DNS, więc sama nazwa hosta jest wystarczająca. Jednak w sieciach wirtualnych wdrożonych przy użyciu klasycznego modelu wdrażania różne usługi w chmurze mają różne sufiksy DNS. W takiej sytuacji nazwa FQDN jest potrzebna do rozpoznawania nazw między różnymi usługami w chmurze. W sieciach wirtualnych wdrożonych przy użyciu Azure Resource Manager wdrażania sufiks DNS jest spójny na wszystkich maszynach wirtualnych w sieci wirtualnej, więc nazwa FQDN nie jest potrzebna. Nazwy DNS można przypisywać do maszyn wirtualnych i interfejsów sieciowych. Chociaż rozwiązanie nazw udostępniane przez platformę Azure nie wymaga żadnej konfiguracji, nie jest to odpowiedni wybór dla wszystkich scenariuszy wdrażania, zgodnie z informacjami w poprzedniej tabeli.

> [!NOTE]
> W przypadku korzystania z ról Sieć Web i Proces roboczy usług w chmurze można również uzyskać dostęp do wewnętrznych adresów IP wystąpień roli przy użyciu interfejsu API REST usługi Azure Service Management. Aby uzyskać więcej informacji, zobacz [Service Management REST API Reference (Dokumentacja interfejsu API REST usługi Service Management).](/previous-versions/azure/ee460799(v=azure.100)) Adres jest oparty na nazwie roli i numerze wystąpienia. 
>

### <a name="features"></a>Funkcje

Rozwiązanie nazw udostępniane przez platformę Azure obejmuje następujące funkcje:
* Łatwość użycia. Nie jest wymagana żadna konfiguracja.
* Wysoka dostępność. Nie musisz tworzyć klastrów własnych serwerów DNS i zarządzać nimi.
* Usługi można używać w połączeniu z własnymi serwerami DNS, aby rozstrzygić zarówno nazwy hostów lokalnych, jak i nazw hostów platformy Azure.
* Rozpoznawania nazw można używać między maszynami wirtualnych i wystąpieniami roli w ramach tej samej usługi w chmurze, bez konieczności używania nazwy FQDN.
* Rozpoznawania nazw można używać między maszynami wirtualnymi w sieciach wirtualnych, które korzystają Azure Resource Manager modelu wdrażania, bez konieczności używania nazwy FQDN. Sieci wirtualne w klasycznym modelu wdrażania wymagają nazwy FQDN podczas rozpoznawania nazw w różnych usługach w chmurze. 
* Zamiast pracować z nazwami generowanymi automatycznie, można użyć nazw hostów, które najlepiej opisują wdrożenia.

### <a name="considerations"></a>Zagadnienia do rozważenia

Punkty, które należy wziąć pod uwagę podczas korzystania z rozpoznawania nazw dostarczanych przez platformę Azure:
* Nie można zmodyfikować sufiksu DNS utworzonego przez platformę Azure.
* Zakres wyszukiwania DNS jest ograniczony do sieci wirtualnej. Nazw DNS utworzonych dla jednej sieci wirtualnej nie można rozpoznać z innych sieci wirtualnych.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługi WINS i NetBIOS nie są obsługiwane. Maszyny wirtualne nie są Eksplorator Windows.
* Nazwy hostów muszą być zgodne z systemem DNS. Nazwy muszą używać tylko 0–9, a–z i "-" i nie mogą zaczynać się ani kończyć znakiem "-".
* Ruch zapytań DNS jest ograniczany dla każdej maszyny wirtualnej. Ograniczanie przepustowości nie powinno mieć wpływu na większość aplikacji. Jeśli zaobserwowano ograniczanie żądań, upewnij się, że buforowanie po stronie klienta jest włączone. Aby uzyskać więcej informacji, zobacz [Konfiguracja klienta DNS.](#dns-client-configuration)
* Tylko maszyny wirtualne w pierwszych 180 usługach w chmurze są rejestrowane dla każdej sieci wirtualnej w klasycznym modelu wdrażania. Ten limit nie ma zastosowania do sieci wirtualnych w Azure Resource Manager.
* Adres AZURE DNS to 168.63.129.16. Jest to statyczny adres IP i nie zmieni się.

### <a name="reverse-dns-considerations"></a>Zagadnienia dotyczące odwrotnego systemu DNS
Odwrotny system DNS jest obsługiwany we wszystkich sieciach wirtualnych opartych na u usługach ARM. Możesz wysyłać odwrotne zapytania DNS (zapytania PTR), aby mapować adresy IP maszyn wirtualnych na nazwy FQDN maszyn wirtualnych.
* Wszystkie zapytania PTR dla adresów IP maszyn wirtualnych będą zwracać nazwy FQDN o postaci \[ vmname \] .internal.cloudapp.net
* Przekazywanie wyszukiwania w nazwach FQDN o postaci nazwa_maszyny_wirtualnej internal.cloudapp.net zostanie rozpoznawane jako \[ adres IP przypisany do maszyny \] wirtualnej.
* Jeśli sieć wirtualna jest połączona z Azure DNS [strefami](../dns/private-dns-overview.md) prywatnymi jako sieć wirtualną rejestracji, odwrotne zapytania DNS zwracają dwa rekordy. Jeden rekord będzie miał postać \[ vmname \] .[ privatednszonename], a drugi będzie miał postać \[ nazwa_maszyny_wirtualnej \] .internal.cloudapp.net
* Odwrotne wyszukiwania DNS jest ograniczony do danej sieci wirtualnej, nawet jeśli jest ona równorzędna z innymi sieciami wirtualnymi. Odwrotne zapytania DNS (zapytania PTR) dla adresów IP maszyn wirtualnych znajdujących się w wirtualnych sieciach równorzędnych będą zwracać nxdomain.
* Jeśli chcesz wyłączyć odwrotną funkcję DNS w sieci wirtualnej, możesz to zrobić, tworząc strefę [wyszukiwania](../dns/private-dns-overview.md) wstecznego przy użyciu stref prywatnych Azure DNS i połączyć tę strefę z siecią wirtualną. Jeśli na przykład przestrzeń adresów IP sieci wirtualnej to 10.20.0.0/16, możesz utworzyć pustą prywatną strefę DNS 20.10.in-addr.arpa i połączyć ją z siecią wirtualną. Łącząc strefę z siecią wirtualną, należy wyłączyć automatyczną rejestrację w linku. Ta strefa zastąpi domyślne strefy wyszukiwania wstecznego dla sieci wirtualnej, a ponieważ ta strefa jest pusta, otrzymasz wartość NXDOMAIN dla zapytań odwrotnego systemu DNS. Zobacz nasz [przewodnik Szybki start,](../dns/private-dns-getstarted-portal.md) aby uzyskać szczegółowe informacje na temat tworzenia prywatnej strefy DNS i łączenia jej z siecią wirtualną.

> [!NOTE]
> Jeśli chcesz, aby wsteczne wyszukiwania DNS rozciągały się w całej sieci wirtualnej, możesz utworzyć strefę wyszukiwania wstecznego (in-addr.arpa) Azure DNS stref [prywatnych](../dns/private-dns-overview.md) i utworzyć połączenie z wieloma sieciami wirtualnymi. Należy jednak ręcznie zarządzać rekordami odwrotnego systemu DNS dla maszyn wirtualnych.
>


## <a name="dns-client-configuration"></a>Konfiguracja klienta DNS

Ta sekcja obejmuje buforowanie po stronie klienta i ponowne próby po stronie klienta.

### <a name="client-side-caching"></a>Buforowanie po stronie klienta

Nie każde zapytanie DNS musi być wysyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszyć opóźnienie i zwiększyć odporność na błędy sieci, rozwiązując cykliczne zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają mechanizm czasu wygaśnięcia (TTL), który umożliwia pamięci podręcznej przechowywanie rekordu tak długo, jak to możliwe, bez wpływu na świeżość rekordu. W związku z tym buforowanie po stronie klienta jest odpowiednie w większości sytuacji.

Domyślny klient DNS systemu Windows ma wbudowaną pamięć podręczną DNS. Niektóre dystrybucje systemu Linux nie obejmują domyślnie buforowania. Jeśli nie masz jeszcze lokalnej pamięci podręcznej, dodaj pamięć podręczną DNS do każdej maszyny wirtualnej z systemem Linux.

Dostępnych jest wiele różnych pakietów buforowania DNS (na przykład dnsmasq). Poniżej opisano sposób instalowania usługi dnsmasq w najbardziej typowych dystrybucjach:

* **Ubuntu (używa resolvconf):**
  * Zainstaluj pakiet dnsmasq za pomocą programu `sudo apt-get install dnsmasq` .
* **SUSE (używa netconf):**
  * Zainstaluj pakiet dnsmasq za pomocą programu `sudo zypper install dnsmasq` .
  * Włącz usługę dnsmasq za `systemctl enable dnsmasq.service` pomocą . 
  * Uruchom usługę dnsmasq za `systemctl start dnsmasq.service` pomocą . 
  * Edytuj **/etc/sysconfig/network/config** i zmień *NETCONFIG_DNS_FORWARDER=""* na *dnsmasq*.
  * Zaktualizuj program resolv.conf za pomocą , `netconfig update` aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.
* **CentOS (używa klasy NetworkManager):**
  * Zainstaluj pakiet dnsmasq za pomocą programu `sudo yum install dnsmasq` .
  * Włącz usługę dnsmasq za `systemctl enable dnsmasq.service` pomocą .
  * Uruchom usługę dnsmasq za `systemctl start dnsmasq.service` pomocą .
  * Dodaj *wstępne serwery nazw domen 127.0.0.1;* do pliku **/etc/dhclient-eth0.conf.**
  * Uruchom ponownie usługę sieciową za pomocą programu `service network restart` , aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.

> [!NOTE]
> Pakiet dnsmasq jest tylko jedną z wielu pamięci podręcznych DNS dostępnych dla systemu Linux. Przed rozpoczęciem korzystania z tej pamięci sprawdź jej przydatność do konkretnych potrzeb i sprawdź, czy nie zainstalowano żadnej innej pamięci podręcznej.

    
### <a name="client-side-retries"></a>Ponowne próby po stronie klienta

SYSTEM DNS jest głównie protokołem UDP. Ponieważ protokół UDP nie gwarantuje dostarczania komunikatów, logika ponawiania jest obsługiwane w samym protokole DNS. Każdy klient DNS (system operacyjny) może mieć inną logikę ponawiania, w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponowią próbę po jednej sekundzie, a następnie ponownie po kolejnych dwóch sekundach, czterech sekundach i kolejnych czterech sekundach. 
* Domyślna konfiguracja systemu Linux ponownych prób po pięciu sekundach. Zalecamy zmianę specyfikacji ponawiania prób na pięć razy, co sekundę.

Sprawdź bieżące ustawienia na maszynie wirtualnej z systemem Linux za `cat /etc/resolv.conf` pomocą . Spójrz na *wiersz opcji,* na przykład:

```bash
options timeout:1 attempts:5
```

Plik resolv.conf jest zwykle generowany automatycznie i nie powinien być edytowany. Konkretne kroki dodawania wiersza *opcji różnią* się w zależności od dystrybucji:

* **Ubuntu** (używa resolvconf):
  1. Dodaj wiersz *opcji* do **polecenia /etc/resolvconf/resolv.conf.d/tail.**
  2. Uruchom, `resolvconf -u` aby zaktualizować.
* **SUSE** (używa netconf):
  1. Dodaj *wartość timeout:1 attempts:5* do **parametru NETCONFIG_DNS_RESOLVER_OPTIONS=""** w pliku **/etc/sysconfig/network/config.**
  2. Uruchom, `netconfig update` aby zaktualizować.
* **CentOS** (używa klasy NetworkManager):
  1. Dodano *echo "options timeout:1 attempts:5"* do **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Zaktualizuj za pomocą `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozpoznawanie nazw, które używa własnego serwera DNS

Ta sekcja obejmuje maszyny wirtualne, wystąpienia ról i aplikacje internetowe.

### <a name="vms-and-role-instances"></a>Maszyny wirtualne i wystąpienia roli

Twoje potrzeby w zakresie rozpoznawania nazw mogą wykraczać poza funkcje udostępniane przez platformę Azure. Na przykład może być konieczne użycie usługi Microsoft Windows Server Active Directory domen, rozpoznawanie nazw DNS między sieciami wirtualnymi. Aby skorzystać z tych scenariuszy, platforma Azure umożliwia korzystanie z własnych serwerów DNS.

Serwery DNS w sieci wirtualnej mogą przesyłać dalej zapytania DNS do cyklicznych funkcji rozpoznawania nazw na platformie Azure. Dzięki temu można rozpoznać nazwy hostów w ramach tej sieci wirtualnej. Na przykład kontroler domeny uruchomiony na platformie Azure może odpowiadać na zapytania DNS dotyczące swoich domen i przesyłać wszystkie inne zapytania do platformy Azure. Zapytania przekazywania umożliwiają maszynom wirtualnych wyświetlanie zarówno zasobów lokalnych (za pośrednictwem kontrolera domeny), jak i nazw hostów dostarczanych przez platformę Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do cyklicznych funkcji rozpoznawania nazw na platformie Azure jest zapewniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przekazywanie dalej DNS umożliwia również rozpoznawanie nazw DNS między sieciami wirtualnymi i umożliwia maszynom lokalnym rozpoznawanie nazw hostów dostarczanych przez platformę Azure. Aby można było rozpoznać nazwę hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i być skonfigurowana do przekazywania zapytań dotyczących nazw hostów na platformę Azure. Ponieważ sufiks DNS jest inny w każdej sieci wirtualnej, można użyć reguł przekazywania warunkowego do wysyłania zapytań DNS do odpowiedniej sieci wirtualnej w celu rozpoznawania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną wykonującą rozpoznawanie nazw DNS między sieciami wirtualnymi przy użyciu tej metody. Przykładowa usługa przesyłania dalej DNS jest dostępna w [galerii szablonów szybkiego startu platformy Azure i](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) [usłudze GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)

> [!NOTE]
> Wystąpienie roli może wykonywać rozpoznawania nazw maszyn wirtualnych w tej samej sieci wirtualnej. Robi to przy użyciu nazwy FQDN, która składa się z nazwy hosta maszyny wirtualnej i internal.cloudapp.net **DNS.** Jednak w tym przypadku rozpoznawania nazw powiedzie się tylko wtedy, gdy wystąpienie roli ma nazwę maszyny wirtualnej zdefiniowaną w schemacie roli [(plik cscfg).](/previous-versions/azure/reference/jj156212(v=azure.100))
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Wystąpienia ról, które muszą wykonywać rozpoznawanie nazw maszyn wirtualnych w innej sieci wirtualnej (FQDN przy użyciu sufiksu **internal.cloudapp.net),** muszą to zrobić przy użyciu metody opisanej w tej sekcji (niestandardowe serwery DNS przekierowywujące między dwiema sieciami wirtualnymi).
>

![Diagram systemu DNS między sieciami wirtualnymi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

W przypadku korzystania z rozpoznawania nazw udostępnianego przez platformę Azure usługa Azure Dynamic Host Configuration Protocol (DHCP) udostępnia wewnętrzny sufiks DNS **(internal.cloudapp.net**) każdej maszynie wirtualnej. Ten sufiks umożliwia rozpoznawania nazw hostów, ponieważ rekordy nazw hostów znajdują się **internal.cloudapp.net** strefie. Jeśli używasz własnego rozwiązania do rozpoznawania nazw, ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ zakłóca to działanie innych architektur DNS (takich jak scenariusze przyłączone do domeny). Zamiast tego platforma Azure udostępnia nie działający symbol zastępczy (*reddog.microsoft.com*).

W razie potrzeby można określić wewnętrzny sufiks DNS przy użyciu programu PowerShell lub interfejsu API:

* W przypadku sieci wirtualnych w modelach wdrażania usługi Azure Resource Manager sufiks jest dostępny za pośrednictwem interfejsu API REST interfejsu [sieciowego,](/rest/api/virtualnetwork/networkinterfaces)polecenia cmdlet [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) programu PowerShell i polecenia [az network nic show](/cli/azure/network/nic#az_network_nic_show) interfejsu wiersza polecenia platformy Azure.
* W klasycznych modelach wdrażania sufiks jest dostępny za pośrednictwem wywołania interfejsu [API](/previous-versions/azure/reference/ee460804(v=azure.100)) pobierz wdrożenie lub polecenia cmdlet [Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure.service/get-azurevm)

Jeśli przekazywanie zapytań na platformę Azure nie odpowiada Twoim potrzebom, musisz udostępnić własne rozwiązanie DNS. Rozwiązanie DNS musi:

* Podaj odpowiednie rozwiązanie nazwy hosta, na przykład za [pośrednictwem usługi DDNS.](virtual-networks-name-resolution-ddns.md) Jeśli używasz usługi DDNS, może być konieczne wyłączenie oczyszczania rekordów DNS. Dzierżawy DHCP platformy Azure są długie, a oczyszczanie może przedwcześnie usunąć rekordy DNS. 
* Zapewnij odpowiednie rekursywne rozwiązanie, aby umożliwić rozpoznawania nazw domen zewnętrznych.
* Być dostępne (TCP i UDP na porcie 53) z klientów, które obsługuje i mieć dostęp do Internetu.
* Zabezpiecz się przed dostępem z Internetu, aby ograniczyć zagrożenia ze strony agentów zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, w przypadku używania maszyn wirtualnych platformy Azure jako serwerów DNS należy wyłączyć protokół IPv6.

### <a name="web-apps"></a>Aplikacje internetowe
Załóżmy, że musisz wykonać rozpoznawania nazw z aplikacji internetowej, korzystając App Service, połączonej z siecią wirtualną, do maszyn wirtualnych w tej samej sieci wirtualnej. Oprócz konfigurowania niestandardowego serwera DNS z usługą przesyłania dalej DNS, która przekazuje zapytania do platformy Azure (wirtualny adres IP 168.63.129.16), wykonaj następujące kroki:
1. Włącz integrację sieci wirtualnej dla aplikacji internetowej, jeśli nie została jeszcze wykonana, zgodnie z opisem w tece [Integrowanie aplikacji z siecią wirtualną.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. W witrynie Azure Portal dla planu App Service hostowania aplikacji internetowej  wybierz pozycję Synchronizuj sieć w obszarze **Sieć**, **Virtual Network Integration**.

    ![Zrzut ekranu przedstawiający rozpoznawania nazw sieci wirtualnej](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Jeśli musisz wykonać rozpoznawanie nazw z aplikacji internetowej sbudowaną przy użyciu usługi App Service połączonej z siecią wirtualną do maszyn wirtualnych w innej sieci wirtualnej, musisz użyć niestandardowych serwerów DNS w obu sieciach wirtualnych w następujący sposób:

* Skonfiguruj serwer DNS w docelowej sieci wirtualnej na maszynie wirtualnej, która może również przesyłać zapytania do cyklicznego programu rozpoznawania nazw na platformie Azure (wirtualny adres IP 168.63.129.16). Przykładowa usługa przesyłania dalej DNS jest dostępna w [galerii szablonów szybkiego startu platformy Azure i](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) [usłudze GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) 
* Skonfiguruj usługę przesyłania dalej DNS w źródłowej sieci wirtualnej na maszynie wirtualnej. Skonfiguruj tę usługę przesyłania dalej DNS, aby przesyłać dalej zapytania do serwera DNS w docelowej sieci wirtualnej.
* Skonfiguruj źródłowy serwer DNS w ustawieniach źródłowej sieci wirtualnej.
* Włącz integrację sieci wirtualnej dla aplikacji internetowej, aby połączyć się ze źródłową siecią wirtualną, zgodnie z instrukcjami w tece Integrowanie aplikacji [z siecią wirtualną.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* W witrynie Azure Portal dla planu App Service hostowania aplikacji internetowej  wybierz pozycję Synchronizuj sieć w obszarze **Sieć**, **Virtual Network Integration**.

## <a name="specify-dns-servers"></a>Określanie serwerów DNS
W przypadku korzystania z własnych serwerów DNS platforma Azure umożliwia określenie wielu serwerów DNS na sieć wirtualną. Można również określić wiele serwerów DNS dla każdego interfejsu sieciowego (w przypadku usługi Azure Resource Manager) lub dla usługi w chmurze (w przypadku klasycznego modelu wdrażania). Serwery DNS określone dla interfejsu sieciowego lub usługi w chmurze mają pierwszeństwo przed serwerami DNS określonymi dla sieci wirtualnej.

> [!NOTE]
> Właściwości połączenia sieciowego, takie jak ip serwera DNS, nie powinny być edytowane bezpośrednio w obrębie maszyn wirtualnych. Jest to spowodowane tym, że mogą zostać usunięte podczas poprawiania usługi, gdy adapter sieci wirtualnej zostanie zastąpiony. Dotyczy to zarówno maszyn wirtualnych z systemem Windows, jak i Linux.

Podczas korzystania z modelu Azure Resource Manager można określić serwery DNS dla sieci wirtualnej i interfejsu sieciowego. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie siecią wirtualną](manage-virtual-network.md) i [Zarządzanie interfejsem sieciowym.](virtual-network-network-interface.md)

> [!NOTE]
> W przypadku wyboru niestandardowego serwera DNS dla sieci wirtualnej należy określić co najmniej jeden adres IP serwera DNS. W przeciwnym razie sieć wirtualna zignoruje konfigurację i zamiast tego użyje usługi DNS dostarczanej przez platformę Azure.

W przypadku korzystania z klasycznego modelu wdrażania można określić serwery DNS dla sieci wirtualnej w Azure Portal lub w pliku [konfiguracji sieci .](/previous-versions/azure/reference/jj157100(v=azure.100)) W przypadku usług w chmurze można określić serwery DNS za pośrednictwem pliku [konfiguracji](/previous-versions/azure/reference/ee758710(v=azure.100)) usługi lub programu PowerShell za pomocą [polecenia New-AzureVM.](/powershell/module/servicemanagement/azure.service/new-azurevm)

> [!NOTE]
> W przypadku zmiany ustawień DNS dla sieci wirtualnej lub maszyny wirtualnej, która została już wdrożona, aby nowe ustawienia DNS zostały wprowadzone, należy wykonać odnawianie dzierżawy DHCP na wszystkich maszynach wirtualnych, których to dotyczy, w sieci wirtualnej. W przypadku maszyn wirtualnych z systemem operacyjnym Windows możesz to zrobić, wpisując `ipconfig /renew` bezpośrednio na maszynie wirtualnej. Kroki różnią się w zależności od systemu operacyjnego. Zapoznaj się z odpowiednią dokumentacją dla danego typu systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

Azure Resource Manager wdrażania:

* [Zarządzanie siecią wirtualną](manage-virtual-network.md)
* [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md)

Klasyczny model wdrażania:

* [Schemat konfiguracji usługi platformy Azure](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network konfiguracji systemu](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Konfigurowanie Virtual Network przy użyciu pliku konfiguracji sieci](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)