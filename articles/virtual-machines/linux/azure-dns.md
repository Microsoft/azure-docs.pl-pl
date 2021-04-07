---
title: Opcje rozpoznawania nazw DNS dla maszyn wirtualnych z systemem Linux
description: Scenariusze rozpoznawania nazw dla maszyn wirtualnych z systemem Linux w usłudze Azure IaaS, w tym z uwzględnieniem usług DNS, hybrydowego serwera DNS i przyłączania do własnych serwerów DNS.
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556795"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opcje rozpoznawania nazw DNS dla maszyn wirtualnych z systemem Linux na platformie Azure
System Azure domyślnie udostępnia rozpoznawanie nazw DNS dla wszystkich maszyn wirtualnych, które znajdują się w jednej sieci wirtualnej. Możesz zaimplementować własne rozwiązanie rozpoznawania nazw DNS, konfigurując własne usługi DNS na maszynach wirtualnych hostowanych przez platformę Azure. Poniższe scenariusze powinny pomóc w wyborze tego, który działa w przypadku danej sytuacji.

* [Rozpoznawanie nazw zapewniane przez platformę Azure](#name-resolution-that-azure-provides)
* [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server)

Typ rozpoznawania nazw, którego używasz, zależy od tego, jak maszyny wirtualne i wystąpienia ról muszą komunikować się ze sobą.

W poniższej tabeli przedstawiono scenariusze i odpowiednie rozwiązania rozpoznawania nazw:

| **Scenariusz** | **Rozwiązanie** | **Przedrostk** |
| --- | --- | --- |
| Rozpoznawanie nazw między wystąpieniami roli lub maszynami wirtualnymi w tej samej sieci wirtualnej |Rozpoznawanie nazw zapewniane przez platformę Azure |nazwy hosta lub w pełni kwalifikowana nazwa domeny (FQDN) |
| Rozpoznawanie nazw między wystąpieniami roli lub maszynami wirtualnymi w różnych sieciach wirtualnych |Serwery DNS zarządzane przez klienta, które przesyłają dalej zapytania między sieciami wirtualnymi w celu rozpoznania ich przez platformę Azure (DNS proxy). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie lokalnych komputerów i nazw usług z wystąpień ról lub maszyn wirtualnych na platformie Azure |Zarządzane przez klienta serwery DNS (na przykład kontroler domeny lokalnej, lokalny kontroler domeny tylko do odczytu lub pomocniczy serwer DNS z zastosowaniem transferów stref). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw hostów platformy Azure z komputerów lokalnych |Przekazuj zapytania do serwera proxy DNS zarządzanego przez klienta w odpowiedniej sieci wirtualnej. Serwer proxy przekazuje zapytania do platformy Azure w celu rozwiązania problemu. Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Zwrotny serwer DNS dla wewnętrznych adresów IP |[Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server) |n/d |

## <a name="name-resolution-that-azure-provides"></a>Rozpoznawanie nazw zapewniane przez platformę Azure
Podobnie jak w przypadku nazw publicznych DNS, platforma Azure udostępnia wewnętrzne rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról, które znajdują się w tej samej sieci wirtualnej. W przypadku sieci wirtualnych opartych na Azure Resource Manager sufiks DNS jest spójny w sieci wirtualnej. Nazwa FQDN nie jest wymagana. Nazwy DNS można przypisać do obu kart interfejsu sieciowego (nic) i maszyn wirtualnych. Chociaż rozpoznawanie nazw zapewniane przez platformę Azure nie wymaga żadnej konfiguracji, nie jest to odpowiedni wybór dla wszystkich scenariuszy wdrażania, jak pokazano w powyższej tabeli.

### <a name="features-and-considerations"></a>Funkcje i zagadnienia
**Oferowanych**

* Do rozpoznawania nazw udostępnianych przez platformę Azure nie jest wymagana żadna konfiguracja.
* Usługa rozpoznawania nazw udostępniana przez platformę Azure jest wysoce dostępna. Nie musisz tworzyć klastrów własnych serwerów DNS i zarządzać nimi.
* Usługa rozpoznawania nazw udostępniana przez platformę Azure może być używana razem z własnymi serwerami DNS, aby rozwiązać zarówno lokalne, jak i nazwy hostów platformy Azure.
* Rozpoznawanie nazw jest zapewniane między maszynami wirtualnymi w sieciach wirtualnych bez konieczności stosowania nazwy FQDN.
* Można użyć nazw hostów, które najlepiej opisują Twoje wdrożenia, a nie pracując z automatycznie generowanymi nazwami.

**Zagadnienia dotyczące**

* Nie można zmodyfikować sufiksu DNS tworzonego przez platformę Azure.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługi WINS i NetBIOS nie są obsługiwane.
* Nazwy hostów muszą być zgodne z systemem DNS.
    Nazwy muszą używać tylko 0-9, a-z i "-" i nie mogą rozpoczynać się ani kończyć znakiem "-". Zobacz dokument RFC 3696 w sekcji 2.
* Ruch zapytań DNS jest ograniczany dla każdej maszyny wirtualnej. Ograniczanie przepływności nie powinno mieć wpływu na większość aplikacji.  Jeśli zaobserwowane jest ograniczanie żądań, upewnij się, że buforowanie po stronie klienta jest włączone.  Aby uzyskać więcej informacji, zobacz [Uzyskiwanie najbardziej do rozpoznawania nazw udostępnianych przez platformę Azure](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Maksymalne rozpoznawanie nazw zapewniane przez platformę Azure
**Buforowanie po stronie klienta:**

Niektóre zapytania DNS nie są wysyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszyć opóźnienia i zwiększyć odporność na niespójności sieci przez rozpoznawanie cyklicznych zapytań DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają czas wygaśnięcia (TTL), który umożliwia przechowywanie rekordów w pamięci podręcznej tak długo, jak to możliwe, bez wpływu na świeżość rekordów. W efekcie buforowanie po stronie klienta jest odpowiednie dla większości sytuacji.

Niektóre dystrybucje systemu Linux domyślnie nie obejmują buforowania. Zalecamy dodanie pamięci podręcznej do każdej maszyny wirtualnej systemu Linux po sprawdzeniu, czy nie istnieje już lokalna pamięć podręczna.

Dostępnych jest kilka różnych pakietów buforowania DNS, takich jak dnsmasq. Poniżej przedstawiono procedurę instalowania programu dnsmasq na najpopularniejszych dystrybucjach:

**Ubuntu (używa resolvconf)**
  * Zainstaluj pakiet dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (używa serviceconf)**:
1. Zainstaluj pakiet dnsmasq ("sudo użyciu narzędzia zypper Install dnsmasq").
2. Włącz usługę dnsmasq ("systemctl Enable dnsmasq. Service").
3. Uruchom usługę dnsmasq ("systemctl Start dnsmasq. Service").
4. Edytuj "/etc/sysconfig/Network/config" i zmień wartość NETCONFIG_DNS_FORWARDER = "" na "dnsmasq".
5. Aktualizacja plik resolv. conf ("Konfiguracja aktualizacji") w celu ustawienia pamięci podręcznej jako lokalnego programu rozpoznawania nazw DNS.

**CentOS przez nieautoryzowane oprogramowanie Wave (dawniej OpenLogic; używa programu NetworkManager)**
1. Zainstaluj pakiet dnsmasq ("sudo yum install dnsmasq").
2. Włącz usługę dnsmasq ("systemctl Enable dnsmasq. Service").
3. Uruchom usługę dnsmasq ("systemctl Start dnsmasq. Service").
4. Dodaj ciąg "poprzedź domenę-Name-serwery 127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Uruchom ponownie usługę sieciową ("ponowne uruchomienie sieci usługi") w celu ustawienia pamięci podręcznej jako lokalnego programu rozpoznawania nazw DNS

> [!NOTE]
> : Pakiet "dnsmasq" jest tylko jednym z wielu pamięci podręcznych DNS, które są dostępne dla systemu Linux. Przed rozpoczęciem korzystania z tej opcji Sprawdź swoją przydatność do potrzeb i nie zainstalowano żadnej innej pamięci podręcznej.
>
>

**Ponowne próby po stronie klienta**

System DNS jest przede wszystkim protokołem UDP. Ponieważ protokół UDP nie gwarantuje dostarczania komunikatów, sam protokół DNS obsługuje logikę ponowień. Każdy klient DNS (system operacyjny) może wyróżnić inną logikę ponowień w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponawiają próbę po jednej sekundzie, a następnie ponownie po drugim, cztery i inne cztery sekundy.
* Domyślna instalacja systemu Linux ponawia próbę po upływie pięciu sekund.  Należy to zmienić, aby ponowić próbę pięciokrotnie w ciągu jednego sekundy.  

Aby sprawdzić bieżące ustawienia na maszynie wirtualnej z systemem Linux, "Cat/etc/resolv.conf" i zajrzeć do wiersza "Options", na przykład:

```config-conf
options timeout:1 attempts:5
```

Plik plik resolv. conf został wygenerowany automatycznie i nie powinien być edytowany. Określone kroki, które dodają wiersz "Options", różnią się w zależności od dystrybucji:

**Ubuntu** (używa resolvconf)
1. Dodaj wiersz opcji do pozycji "/etc/resolvconf/resolv.conf.d/Head".
2. Uruchom element "resolvconf-u", aby przeprowadzić aktualizację.

**SUSE** (używa proconf)
1. Dodaj element "timeout: 1 próby: 5" do parametru NETCONFIG_DNS_RESOLVER_OPTIONS = "" w elemencie "/etc/sysconfig/Network/config".
2. Uruchom plik "config Update", aby zaktualizować.

**CentOS przez nieautoryzowane oprogramowanie Wave (dawniej OpenLogic)** (używa programu NetworkManager)
1. Dodaj element "RES_OPTIONS =" timeout: 1 próby: 5 "" na "/etc/sysconfig/Network".
2. Uruchom "ponowne uruchomienie sieci usługi", aby przeprowadzić aktualizację.

## <a name="name-resolution-using-your-own-dns-server"></a>Rozpoznawanie nazw przy użyciu własnego serwera DNS
Wymagania dotyczące rozpoznawania nazw mogą wykraczać poza funkcje udostępniane przez platformę Azure. Na przykład może być wymagane rozpoznanie nazw DNS między sieciami wirtualnymi. Aby zakryć ten scenariusz, można użyć własnych serwerów DNS.  

Serwery DNS w sieci wirtualnej mogą przekazywać zapytania DNS do rekursywnych resolverów platformy Azure w celu rozpoznawania nazw hostów znajdujących się w tej samej sieci wirtualnej. Na przykład serwer DNS, który działa na platformie Azure, może odpowiadać na zapytania DNS dotyczące własnych plików stref DNS i przekazywać wszystkie inne zapytania do platformy Azure. Ta funkcja umożliwia maszynom wirtualnym wyświetlanie zarówno wpisów w plikach strefy, jak i nazw hostów udostępnianych przez platformę Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do cyklicznych tłumaczeń platformy Azure jest udostępniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przesyłanie dalej DNS umożliwia również rozpoznawanie nazw DNS między sieciami wirtualnymi i umożliwia maszynom lokalnym rozpoznawanie nazw hostów udostępnianych przez platformę Azure. Aby można było rozpoznać nazwę hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i być skonfigurowana do przekazywania zapytań hostname do platformy Azure. Ponieważ sufiks DNS różni się w każdej sieci wirtualnej, można użyć reguł przekazywania warunkowego do wysyłania zapytań DNS do odpowiedniej sieci wirtualnej w celu rozwiązania tego problemu. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalna wykonująca rozpoznawanie nazw DNS między sieciami wirtualnymi za pomocą tej metody:

![Rozpoznawanie nazw DNS między sieciami wirtualnymi](./media/azure-dns/inter-vnet-dns.png)

W przypadku korzystania z rozpoznawania nazw udostępnianej przez platformę Azure, wewnętrzny sufiks DNS jest dostarczany dla każdej maszyny wirtualnej przy użyciu protokołu DHCP. W przypadku korzystania z własnego rozwiązania rozpoznawania nazw ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ ten sufiks zakłóca inne architektury DNS. Aby odwołać się do maszyn przy użyciu nazwy FQDN lub skonfigurować sufiks na maszynach wirtualnych, można użyć programu PowerShell lub interfejsu API do określenia sufiksu:

* W przypadku sieci wirtualnych zarządzanych przez Azure Resource Manager sufiks jest dostępny za pośrednictwem zasobu [karty interfejsu sieciowego](/rest/api/virtualnetwork/networkinterfaces) . Możesz również uruchomić polecenie, `azure network public-ip show <resource group> <pip name>` Aby wyświetlić szczegóły publicznego adresu IP, w tym nazwę FQDN karty sieciowej.

Jeśli przekazywanie zapytań do platformy Azure nie odpowiada Twoim potrzebom, należy podać własne rozwiązanie DNS.  Rozwiązanie DNS musi:

* Podaj odpowiednie rozwiązanie do rozpoznawania nazw hostów, na przykład za pośrednictwem [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczania rekordów DNS. Dzierżawy DHCP na platformie Azure są bardzo długie, a oczyszczanie może spowodować przedwczesne usunięcie rekordów DNS.
* Podaj odpowiednie rozwiązanie cykliczne, aby umożliwić rozpoznanie zewnętrznych nazw domen.
* Dostęp do Internetu (TCP i UDP na porcie 53) od klientów, które obsługuje, i dostępu do sieci Internet.
* Zabezpieczanie przed dostępem z Internetu w celu ograniczenia zagrożeń powodowanych przez agentów zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, w przypadku używania maszyn wirtualnych na serwerach Azure DNS należy wyłączyć protokół IPv6 i przypisać [publiczny adres IP na poziomie wystąpienia](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) do każdej maszyny wirtualnej serwera DNS.  
>
>
