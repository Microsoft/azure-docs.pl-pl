---
title: Opcje rozpoznawania nazw DNS dla maszyn wirtualnych z systemem Linux
description: Scenariusze rozpoznawania nazw dla maszyn wirtualnych systemu Linux w usłudze Azure IaaS, w tym świadczone usługi DNS, hybrydowy zewnętrzny system DNS i serwer DNS Bring Your Own.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 0910b31685aa408c319b40ea23782b11724b6237
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641717"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opcje rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure
Platforma Azure domyślnie zapewnia rozpoznawanie nazw DNS dla wszystkich maszyn wirtualnych, które znajdują się w jednej sieci wirtualnej. Można zaimplementować własne rozwiązanie rozpoznawania nazw DNS, konfigurując własne usługi DNS na maszynach wirtualnych hostów platformy Azure. Poniższe scenariusze powinny pomóc w wyborze tego, który działa w twojej sytuacji.

* [Rozpoznawanie nazw zapewniane przez platformę Azure](#name-resolution-that-azure-provides)
* [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server)

Typ rozpoznawania nazw, którego używasz, zależy od tego, jak maszyny wirtualne i wystąpienia ról muszą komunikować się ze sobą.

W poniższej tabeli przedstawiono scenariusze i odpowiadające im rozwiązania rozpoznawania nazw:

| **Scenariusz** | **Rozwiązanie** | **Sufiks** |
| --- | --- | --- |
| Rozpoznawanie nazw między wystąpieniami roli lub maszynami wirtualnymi w tej samej sieci wirtualnej |Rozpoznawanie nazw zapewniane przez platformę Azure |nazwa hosta lub w pełni kwalifikowana nazwa domeny (FQDN) |
| Rozpoznawanie nazw między wystąpieniami ról lub maszynami wirtualnymi w różnych sieciach wirtualnych |Serwery DNS zarządzane przez klienta, które przekazyzuje zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazw komputerów lokalnych i usług z wystąpień ról lub maszyn wirtualnych na platformie Azure |Serwery DNS zarządzane przez klienta (na przykład lokalny kontroler domeny, lokalny kontroler domeny tylko do odczytu lub pomocniczy dns zsynchronizowany przy użyciu transferów stref). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazwy hostów platformy Azure z komputerów lokalnych |Przesyłanie dalej kwerend do serwera proxy DNS zarządzanego przez klienta w odpowiedniej sieci wirtualnej. Serwer proxy przekazuje zapytania do platformy Azure w celu rozwiązania. Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko FQDN |
| Odwrócony DNS dla wewnętrznych serwerów IP |[Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server) |Nie dotyczy |

## <a name="name-resolution-that-azure-provides"></a>Rozpoznawanie nazw zapewniane przez platformę Azure
Wraz z rozdzielczością publicznych nazw DNS platforma Azure zapewnia wewnętrzne rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról, które znajdują się w tej samej sieci wirtualnej. W sieciach wirtualnych opartych na usłudze Azure Resource Manager sufiks DNS jest spójny w całej sieci wirtualnej; fqdn nie jest potrzebne. Nazwy DNS można przypisać zarówno do kart interfejsu sieciowego (NIC), jak i do maszyn wirtualnych. Mimo że rozpoznawanie nazw, które zapewnia platformę Azure nie wymaga żadnej konfiguracji, nie jest odpowiedni wybór dla wszystkich scenariuszy wdrażania, jak widać w powyższej tabeli.

### <a name="features-and-considerations"></a>Cechy i zagadnienia
**Funkcje:**

* Do używania rozpoznawania nazw, które zapewnia platforma Azure, nie jest wymagana żadna konfiguracja.
* Usługa rozpoznawania nazw, którą zapewnia platforma Azure, jest wysoce dostępna. Nie trzeba tworzyć klastrów własnych serwerów DNS i zarządzać nimi.
* Usługa rozpoznawania nazw, którą udostępnia platforma Azure, może być używana wraz z własnymi serwerami DNS w celu rozpoznawania nazw hostów lokalnych i hostów platformy Azure.
* Rozpoznawanie nazw jest dostarczane między maszynami wirtualnymi w sieciach wirtualnych bez konieczności korzystania z nazwy FQDN.
* Można użyć nazw hostów, które najlepiej opisują wdrożenia, a nie pracy z automatycznie generowanymi nazwami.

**Zagadnienia dotyczące:**

* Nie można zmodyfikować sufiksu DNS tworzonego przez platformę Azure.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługi WINS i NetBIOS nie są obsługiwane.
* Nazwy hostów muszą być zgodne z systemem DNS.
    Nazwy muszą używać tylko 0-9, a-z i '-', i nie mogą zaczynać się ani kończyć na '-'. Patrz RFC 3696 Sekcja 2.
* Ruch zapytań DNS jest ograniczany dla każdej maszyny wirtualnej. Ograniczanie przepustowości nie powinno mieć wpływu na większość aplikacji.  Jeśli dławienie żądania jest przestrzegane, upewnij się, że buforowanie po stronie klienta jest włączona.  Aby uzyskać więcej informacji, zobacz [Jak najwięcej z rozpoznawania nazw zapewniana przez platformę Azure.](#getting-the-most-from-name-resolution-that-azure-provides)

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Jak najwięcej z rozpoznawania nazw zapewniane przez platformę Azure
**Buforowanie po stronie klienta:**

Niektóre zapytania DNS nie są wysyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszyć opóźnienia i zwiększyć odporność na niespójności sieciowe, rozwiązując powtarzające się zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają czas wygaśnięcia (TTL), który umożliwia pamięci podręcznej do przechowywania rekordu tak długo, jak to możliwe bez wpływu na świeżość rekordu. W rezultacie buforowanie po stronie klienta jest odpowiednie w większości sytuacji.

Niektóre dystrybucje systemu Linux domyślnie nie zawierają buforowania. Zaleca się dodanie pamięci podręcznej do każdej maszyny wirtualnej systemu Linux po sprawdzeniu, czy nie ma już lokalnej pamięci podręcznej.

Dostępnych jest kilka różnych pakietów buforowania DNS, takich jak dnsmasq. Oto kroki, aby zainstalować dnsmasq na najpopularniejszych dystrybucjach:

**Ubuntu (używa resolvconf)**
  * Zainstaluj pakiet dnsmasq ("sudo apt-get install dnsmasq").

**SUSE (używa netconf)**:
1. Zainstaluj pakiet dnsmasq ("sudo zypper install dnsmasq").
2. Włącz usługę dnsmasq ("systemctl enable dnsmasq.service").
3. Uruchom usługę dnsmasq ("systemctl start dnsmasq.service").
4. Edytuj "/etc/sysconfig/network/config" i zmień NETCONFIG_DNS_FORWARDER="" na "dnsmasq".
5. Zaktualizuj plik resolv.conf ("netconfig update"), aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.

**CentOS przez Rogue Wave Software (dawniej OpenLogic; używa NetworkManager)**
1. Zainstaluj pakiet dnsmasq ("sudo yum install dnsmasq").
2. Włącz usługę dnsmasq ("systemctl enable dnsmasq.service").
3. Uruchom usługę dnsmasq ("systemctl start dnsmasq.service").
4. Dodaj "prepend domain-name-servers 127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Uruchom ponownie usługę sieciową ("ponowne uruchomienie sieci usługi"), aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS

> [!NOTE]
> : Pakiet 'dnsmasq' jest tylko jedną z wielu pamięci podręcznych DNS dostępnych dla linuksa. Przed użyciem należy sprawdzić jego przydatność do własnych potrzeb i czy nie zainstalowano żadnej innej pamięci podręcznej.
>
>

**Ponownych prób po stronie klienta**

DNS jest przede wszystkim protokołem UDP. Ponieważ protokół UDP nie gwarantuje dostarczania wiadomości, sam protokół DNS obsługuje logikę ponawiania. Każdy klient DNS (system operacyjny) może wykazywać inną logikę ponawiania prób w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponowiają próbę po jednej sekundzie, a następnie ponownie po kolejnych dwóch, czterech i kolejnych czterech sekundach.
* Domyślna konfiguracja systemu Linux ponawia ponawia ponawianie po pięciu sekundach.  Należy to zmienić, aby ponowić próbę pięć razy w odstępach jednosekundowych.  

Aby sprawdzić bieżące ustawienia na maszynie wirtualnej Systemu Linux, 'cat /etc/resolv.conf', i przyjrzyj się na przykład linii "opcje":

    options timeout:1 attempts:5

Plik resolv.conf jest generowany automatycznie i nie powinien być edytowany. Konkretne kroki, które dodają wiersz "opcje", różnią się w zależności od rozkładu:

**Ubuntu** (używa resolvconf)
1. Dodaj linię opcji do '/etc/resolvconf/resolv.conf.d/head'.
2. Uruchom 'resolvconf -u', aby zaktualizować.

**SUSE** (używa netconf)
1. Dodaj parametr "timeout:1 attempts:5" do parametru NETCONFIG_DNS_RESOLVER_OPTIONS="" w '/etc/sysconfig/network/config'.
2. Uruchom "netconfig update", aby zaktualizować.

**CentOS przez Rogue Wave Software (dawniej OpenLogic)** (używa NetworkManager)
1. Dodaj 'RES_OPTIONS="timeout:1 attempts:5"' do '/etc/sysconfig/network'.
2. Uruchom "ponowne uruchomienie sieci usługi", aby zaktualizować.

## <a name="name-resolution-using-your-own-dns-server"></a>Rozpoznawanie nazw przy użyciu własnego serwera DNS
Potrzeby dotyczące rozpoznawania nazw mogą wykraczać poza funkcje, które zapewnia platforma Azure. Na przykład może wymagać rozpoznawania dns między sieciami wirtualnymi. Aby omówić ten scenariusz, można użyć własnych serwerów DNS.  

Serwery DNS w sieci wirtualnej mogą przesyłać dalej kwerendy DNS do cyklicznych programów rozpoznawania platformy Azure w celu rozpoznawania nazw hostów znajdujących się w tej samej sieci wirtualnej. Na przykład serwer DNS uruchamiany na platformie Azure może odpowiadać na zapytania DNS dla własnych plików strefy DNS i przesyłać dalej wszystkie inne zapytania na platformę Azure. Ta funkcja umożliwia maszynom wirtualnym wyświetlanie zarówno wpisów w plikach strefy, jak i nazwy hostów, które udostępnia platforma Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do cyklicznych programów rozpoznawania nazw platformy Azure jest zapewniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przekazywanie dns umożliwia również rozpoznawanie DNS między sieciami wirtualnymi i umożliwia maszynom lokalnym rozpoznawanie nazw hostów udostępnianych przez platformę Azure. Aby rozpoznać nazwa hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i być skonfigurowana do przekazywania zapytań o nazwy hosta na platformę Azure. Ponieważ sufiks DNS jest inny w każdej sieci wirtualnej, można użyć reguł przekazywania warunkowego do wysyłania zapytań DNS do właściwej sieci wirtualnej w celu rozwiązania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną wykonującą rozpoznawanie DNS między sieciami wirtualnymi przy użyciu tej metody:

![Rozpoznawanie DNS między sieciami wirtualnymi](./media/azure-dns/inter-vnet-dns.png)

W przypadku używania rozpoznawania nazw udostępnianej przez platformę Azure wewnętrzny sufiks DNS jest dostarczany każdej maszynie wirtualnej przy użyciu usługi DHCP. W przypadku korzystania z własnego rozwiązania rozpoznawania nazw ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ sufiks koliduje z innymi architekturami DNS. Aby odwołać się do komputerów przez FQDN lub skonfigurować sufiks na maszynach wirtualnych, można użyć programu PowerShell lub interfejsu API do określenia sufiksu:

* W przypadku sieci wirtualnych zarządzanych przez usługę Azure Resource Manager sufiks jest dostępny za pośrednictwem zasobu [karty interfejsu sieciowego.](https://msdn.microsoft.com/library/azure/mt163668.aspx) Można również uruchomić `azure network public-ip show <resource group> <pip name>` polecenie, aby wyświetlić szczegóły publicznego adresu IP, który zawiera numer FQDN karty sieciowej.

Jeśli przekazywanie zapytań na platformę Azure nie odpowiada Twoim potrzebom, musisz podać własne rozwiązanie DNS.  Rozwiązanie DNS musi:

* Zapewnij odpowiednią rozdzielczość nazwy hosta, na przykład za pośrednictwem [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczania rekordów DNS. Dzierżawy platformy Azure dhcp są bardzo długie, a oczyszczanie może przedwcześnie usunąć rekordy DNS.
* Zapewnij odpowiednią rozdzielczość cykliczną, aby umożliwić rozpoznawanie zewnętrznych nazw domen.
* Bądź dostępny (TCP i UDP na porcie 53) od obsługiwanych klientów i mieć dostęp do Internetu.
* Być zabezpieczone przed dostępem z Internetu w celu złagodzenia zagrożeń stwarzanych przez agentów zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, gdy używasz maszyn wirtualnych na serwerach DNS platformy Azure, wyłącz protokół IPv6 i przypisz [publiczny adres IP na poziomie wystąpienia](../../virtual-network/virtual-networks-instance-level-public-ip.md) do każdej maszyny wirtualnej serwera DNS.  
>
>
