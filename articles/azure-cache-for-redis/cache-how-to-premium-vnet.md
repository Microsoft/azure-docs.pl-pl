---
title: Konfigurowanie pamięci podręcznej platformy Azure w Virtual Network Premium dla Redis
description: Dowiedz się, jak tworzyć i zarządzać Virtual Network obsługi usługi Azure cache w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 82003ef84571c8e07982826124b33763c0e53194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88205564"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować obsługę Virtual Network w przypadku pamięci podręcznej Premium platformy Azure dla Redis
Usługa Azure cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru i funkcji pamięci podręcznej, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. Sieć wirtualna jest siecią prywatną w chmurze. Gdy usługa Azure cache for Redis jest skonfigurowana przy użyciu sieci wirtualnej, nie jest ona publicznie adresowana i można uzyskać do niej dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej. W tym artykule opisano sposób konfigurowania obsługi sieci wirtualnej na potrzeby wystąpienia usługi Redis w warstwie Premium.

> [!NOTE]
> Usługa Azure cache for Redis obsługuje zarówno klasyczne, jak i Menedżer zasobów sieci wirtualnych.
> 
> 

## <a name="why-vnet"></a>Dlaczego sieć wirtualna?
Wdrożenie [usługi azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/) zapewnia ulepszone zabezpieczenia i izolację pamięci podręcznej platformy Azure dla usługi Redis, a także podsieci, zasady kontroli dostępu i inne funkcje w celu dodatkowego ograniczenia dostępu.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej
Obsługa Virtual Network (VNet) jest konfigurowana w **nowym bloku Azure cache for Redis** podczas tworzenia pamięci podręcznej. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po wybraniu warstwy cenowej Premium można skonfigurować integrację sieci wirtualnej Redis, wybierając sieć wirtualną znajdującą się w tej samej subskrypcji i lokalizacji co pamięć podręczna. Aby użyć nowej sieci wirtualnej, należy ją najpierw utworzyć, wykonując czynności opisane w temacie [Create a Virtual Network przy użyciu Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) lub [Utwórz sieć wirtualną (klasyczną) za pomocą Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a następnie wróć do **nowego bloku Azure cache for Redis** , aby utworzyć i skonfigurować pamięć podręczną Premium.

Aby skonfigurować sieć wirtualną dla nowej pamięci podręcznej, kliknij pozycję **Virtual Network** w **nowym bloku Azure cache for Redis** i wybierz żądaną sieć wirtualną z listy rozwijanej.

![Sieć wirtualna][redis-cache-vnet]

Wybierz żądaną podsieć z listy rozwijanej **podsieć** .  W razie potrzeby określ **statyczny adres IP**. Pole **statyczny adres IP** jest opcjonalne i jeśli nie jest określone, jeden z nich zostanie wybrany z wybranej podsieci.

> [!IMPORTANT]
> Podczas wdrażania pamięci podręcznej platformy Azure dla Redis w sieci wirtualnej Menedżer zasobów, pamięć podręczna musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych innych zasobów z wyjątkiem usługi Azure cache for Redis Instances. Jeśli podjęto próbę wdrożenia pamięci podręcznej platformy Azure dla Redis w sieci wirtualnej Menedżer zasobów w podsieci zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem.
> 
> 

![Sieć wirtualna][redis-cache-vnet-ip]

> [!IMPORTANT]
> Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, każde wystąpienie Redis w podsieci używa dwóch adresów IP na fragmentu i jednego dodatkowego adresu IP dla modułu równoważenia obciążenia. Nieklastrowana pamięć podręczna jest traktowana jako jedna fragmentu.
> 
> 

Po utworzeniu pamięci podręcznej można wyświetlić konfigurację sieci wirtualnej, klikając pozycję **Virtual Network** w **menu zasób**.

![Sieć wirtualna][redis-cache-vnet-info]

Aby nawiązać połączenie z usługą Azure cache for Redis w przypadku korzystania z sieci wirtualnej, należy określić nazwę hosta pamięci podręcznej w parametrach połączenia, jak pokazano w następującym przykładzie:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Usługa Azure cache for Redis — często zadawane pytania
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania Redis w pamięci podręcznej platformy Azure.

* Jak często występują problemy z błędami konfiguracji w usłudze Azure cache dla Redis i sieci wirtualnych?
* [Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Dlaczego podczas próby nawiązania połączenia z moją pamięcią podręczną platformy Azure dla Redis w sieci wirtualnej jest wyświetlany komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?
* [Czy mogę używać sieci wirtualnych z pamięcią standardowa lub podstawową?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Dlaczego tworzenie pamięci podręcznej platformy Azure dla usługi Redis kończy się niepowodzeniem w niektórych podsieciach, ale nie w innych?
* [Jakie są wymagania dotyczące przestrzeni adresowej podsieci?](#what-are-the-subnet-address-space-requirements)
* [Czy wszystkie funkcje pamięci podręcznej działają w przypadku hostowania pamięci podręcznej w sieci wirtualnej?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Jak często występują problemy z błędami konfiguracji w usłudze Azure cache dla Redis i sieci wirtualnych?
Gdy pamięć podręczna platformy Azure dla usługi Redis jest hostowana w sieci wirtualnej, używane są porty w poniższych tabelach. 

>[!IMPORTANT]
>Jeśli porty w poniższych tabelach są blokowane, pamięć podręczna może nie działać poprawnie. Jeśli co najmniej jeden z tych portów jest zablokowany, jest to najbardziej typowy problem z konfiguracją w przypadku korzystania z usługi Azure cache for Redis w sieci wirtualnej.
> 
> 

- [Wymagania dotyczące portów wychodzących](#outbound-port-requirements)
- [Wymagania dotyczące portów przychodzących](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących

Istnieją dziewięć wymagań portu wychodzącego. Żądania wychodzące w tych zakresach są wychodzące do innych usług niezbędnych do funkcjonowania pamięci podręcznej lub wewnętrznej podsieci Redis na potrzeby komunikacji między węzłami. W przypadku replikacji geograficznej istnieją dodatkowe wymagania wychodzące dotyczące komunikacji między podsieciami podstawowej i pamięci podręcznej repliki.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Outbound |TCP |Redis zależności w usłudze Azure Storage/PKI (Internet) | (Podsieć Redis) |* |
| 443 | Outbound | TCP | Redis zależność od Azure Key Vault | (Podsieć Redis) | AzureKeyVault <sup>1</sup> |
| 53 |Outbound |TCP/UDP |Redis zależności w systemie DNS (Internet/Sieć wirtualna) | (Podsieć Redis) | 168.63.129.16 i 169.254.169.254 <sup>2</sup> oraz dowolny niestandardowy serwer DNS dla podsieci <sup>3</sup> |
| 8443 |Outbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 10221-10231 |Outbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 20226 |Outbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 13000-13999 |Outbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 15000-15999 |Outbound |TCP |Komunikacja wewnętrzna dla Redis i Geo-Replication | (Podsieć Redis) |(Podsieć Redis) (Podsieć równorzędna repliki geograficznej) |
| 6379-6380 |Outbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> możesz użyć znacznika usługi "AzureKeyVault" z grupami zabezpieczeń sieci Menedżer zasobów.

<sup>2</sup> te adresy IP należące do firmy Microsoft są używane do ADRESOWANIA maszyny wirtualnej hosta, która obsługuje Azure DNS.

<sup>3</sup> nie jest wymagana w przypadku podsieci bez NIESTANDARDOWEGO serwera DNS lub nowszych pamięci podręcznych Redis, które ignorują niestandardowy serwer DNS.

#### <a name="geo-replication-peer-port-requirements"></a>Wymagania dotyczące portów równorzędnych replikacji geograficznej

W przypadku używania georeplikacji między pamięciami podręcznymi w sieciach wirtualnych platformy Azure należy pamiętać, że zalecaną konfiguracją jest Odblokowanie portów 15000-15999 dla całej podsieci zarówno w przypadku ruchu przychodzącego, jak i wychodzącego do obu pamięci podręcznych, dzięki czemu wszystkie składniki repliki w podsieci mogą komunikować się bezpośrednio ze sobą nawet w przypadku przyszłej geograficznej pracy w trybie failover.

#### <a name="inbound-port-requirements"></a>Wymagania dotyczące portów przychodzących

Istnieją osiem wymagań zakresu portów przychodzących. Żądania przychodzące w tych zakresach są przychodzące z innych usług hostowanych w tej samej sieci wirtualnej lub wewnętrznych dla komunikacji podsieci Redis.

| Porty | Kierunek | Protokół transportu | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inbound |TCP |Komunikacja z klientem do Redis, równoważenie obciążenia platformy Azure | (Podsieć Redis) | (Podsieć Redis), Virtual Network, Azure Load Balancer <sup>1</sup> |
| 8443 |Inbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 8500 |Inbound |TCP/UDP |Równoważenie obciążenia na platformie Azure | (Podsieć Redis) |Azure Load Balancer |
| 10221-10231 |Inbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis), Azure Load Balancer |
| 13000-13999 |Inbound |TCP |Komunikacja klienta z klastrami Redis, usługa równoważenia obciążenia platformy Azure | (Podsieć Redis) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Inbound |TCP |Komunikacja z klientem do klastrów Redis, równoważenia obciążenia platformy Azure i Geo-Replication | (Podsieć Redis) |Virtual Network, Azure Load Balancer, (podsieć równorzędna z repliką geograficzną) |
| 16001 |Inbound |TCP/UDP |Równoważenie obciążenia na platformie Azure | (Podsieć Redis) |Azure Load Balancer |
| 20226 |Inbound |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> do tworzenia reguł sieciowej grupy zabezpieczeń można użyć znacznika usługi "AzureLoadBalancer Menedżer zasobów" (lub "AZURE_LOADBALANCER" dla klasycznego).

#### <a name="additional-vnet-network-connectivity-requirements"></a>Dodatkowe wymagania dotyczące łączności sieci wirtualnej

Istnieją wymagania dotyczące łączności sieciowej dla usługi Azure cache for Redis, które mogą nie być początkowo spełnione w sieci wirtualnej. Usługa Azure cache for Redis wymaga, aby wszystkie następujące elementy funkcjonowały prawidłowo, gdy są używane w sieci wirtualnej.

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure Storage w świecie. Obejmuje to punkty końcowe znajdujące się w tym samym regionie co usługa Azure cache for Redis oraz punkty końcowe magazynu znajdujące się w **innych** regionach świadczenia usługi Azure. Punkty końcowe usługi Azure Storage są rozwiązywane w następujących domenach DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *Queue.Core.Windows.NET*i *File.Core.Windows.NET*. 
* Wychodząca łączność sieciowa z *OCSP.msocsp.com*, *mscrl.Microsoft.com*i *CRL.Microsoft.com*. Ta łączność jest wymagana do obsługi funkcji TLS/SSL.
* Konfiguracja systemu DNS dla sieci wirtualnej musi być w stanie rozwiązać wszystkie punkty końcowe i domeny wymienione w poprzednich punktach. Te wymagania dotyczące usługi DNS można spełnić, upewniając się, że dla sieci wirtualnej jest skonfigurowana i utrzymywana prawidłowa infrastruktura DNS.
* Wychodząca łączność sieciowa z następującymi punktami końcowymi monitorowania platformy Azure, które są rozpoznawane w następujących domenach DNS: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?

>[!IMPORTANT]
>W przypadku nawiązywania połączenia z usługą Azure cache for Redis, która jest hostowana w sieci wirtualnej, klienci pamięci podręcznej muszą znajdować się w tej samej sieci wirtualnej lub w sieci wirtualnej z włączoną obsługą sieci wirtualnych w tym samym regionie świadczenia usługi Azure. Globalne Wirtualne sieci równorzędne nie są obecnie obsługiwane. Dotyczy to wszystkich aplikacji testowych lub narzędzi ping diagnostycznych. Bez względu na to, gdzie jest hostowana aplikacja kliencka, grupy zabezpieczeń sieci muszą być skonfigurowane tak, aby ruch sieciowy klienta mógł dotrzeć do wystąpienia Redis.
>
>

Po skonfigurowaniu wymagań dotyczących portów zgodnie z opisem w poprzedniej sekcji można sprawdzić, czy pamięć podręczna działa, wykonując następujące kroki.

- [Uruchom ponownie](cache-administration.md#reboot) wszystkie węzły pamięci podręcznej. Jeśli nie można uzyskać dostępu do wszystkich wymaganych zależności pamięci podręcznej (zgodnie z opisem w [wymaganiach dotyczących portów przychodzących](cache-how-to-premium-vnet.md#inbound-port-requirements) i [wymagań dotyczących portów wychodzących](cache-how-to-premium-vnet.md#outbound-port-requirements)), pamięć podręczna nie będzie mogła zostać pomyślnie ponownie uruchomiona.
- Po ponownym uruchomieniu węzłów pamięci podręcznej (zgodnie z informacjami o stanie pamięci podręcznej w Azure Portal) można wykonać następujące testy:
  - Wyślij polecenie ping do punktu końcowego pamięci podręcznej (przy użyciu portu 6380) z komputera, który znajduje się w tej samej sieci wirtualnej co pamięć podręczna, przy użyciu [tcping](https://www.elifulkerson.com/projects/tcping.php). Na przykład:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Jeśli `tcping` Narzędzie zgłosi, że port jest otwarty, pamięć podręczna jest dostępna do połączenia z klientów w sieci wirtualnej.

  - Innym sposobem na przetestowanie jest utworzenie klienta testowej pamięci podręcznej (który może być prostą aplikacją konsolową przy użyciu StackExchange. Redis), który łączy się z pamięcią podręczną i dodaje i pobiera niektóre elementy z pamięci podręcznej. Zainstaluj przykładową aplikację kliencką na maszynie wirtualnej znajdującej się w tej samej sieci wirtualnej, w której znajduje się pamięć podręczna, i uruchom ją w celu sprawdzenia łączności z pamięcią podręczną.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Dlaczego podczas próby nawiązania połączenia z moją pamięcią podręczną platformy Azure dla Redis w sieci wirtualnej jest wyświetlany komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?

Podczas próby nawiązania połączenia z pamięcią podręczną platformy Azure dla Redis w sieci wirtualnej zostanie wyświetlony następujący błąd sprawdzania poprawności certyfikatu:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Przyczyną może być nawiązanie połączenia z hostem za pomocą adresu IP. Zalecamy używanie nazwy hosta. Innymi słowy, użyj następujących czynności:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Należy unikać używania adresu IP podobnego do następujących parametrów połączenia:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Jeśli nie można rozpoznać nazwy DNS, niektóre biblioteki klienta obejmują opcje konfiguracji, takie jak `sslHost` dostarczone przez klienta stackexchange. Redis. Pozwala to zastąpić nazwę hosta używaną do sprawdzania poprawności certyfikatu. Na przykład:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Czy mogę używać sieci wirtualnych z pamięcią standardowa lub podstawową?
Sieci wirtualnych można używać tylko z pamięcią podręczną w warstwie Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Dlaczego tworzenie pamięci podręcznej platformy Azure dla usługi Redis kończy się niepowodzeniem w niektórych podsieciach, ale nie w innych?
W przypadku wdrażania pamięci podręcznej platformy Azure dla Redis w sieci wirtualnej Menedżer zasobów, pamięć podręczna musi znajdować się w dedykowanej podsieci, która nie zawiera innego typu zasobu. Jeśli podjęto próbę wdrożenia pamięci podręcznej platformy Azure dla Redis w podsieci sieci wirtualnej Menedżer zasobów zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem. Aby można było utworzyć nową pamięć podręczną platformy Azure dla usługi Redis, należy usunąć istniejące zasoby w podsieci.

Wiele typów zasobów można wdrożyć w klasycznej sieci wirtualnej, o ile masz wystarczającą liczbę dostępnych adresów IP.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jakie są wymagania dotyczące przestrzeni adresowej podsieci?
Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, każde wystąpienie Redis w podsieci używa dwóch adresów IP na fragmentu i jednego dodatkowego adresu IP dla modułu równoważenia obciążenia. Nieklastrowana pamięć podręczna jest traktowana jako jedna fragmentu.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Czy wszystkie funkcje pamięci podręcznej działają w przypadku hostowania pamięci podręcznej w sieci wirtualnej?
Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do pamięci podręcznej. W związku z tym następujące funkcje zarządzania pamięcią podręczną nie działają w tym momencie.

* Konsola Redis — ponieważ konsola Redis działa w przeglądarce lokalnej, która jest poza siecią wirtualną, nie może nawiązać połączenia z pamięcią podręczną.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Używanie ExpressRoute z usługą Azure cache dla Redis

Klienci mogą połączyć obwód [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z infrastrukturą sieci wirtualnej, rozszerzając w ten sposób sieć lokalną na platformę Azure. 

Domyślnie nowo utworzony obwód ExpressRoute nie wykonuje wymuszonego tunelowania (anons trasy domyślnej, 0.0.0.0/0) w sieci wirtualnej. W efekcie wychodzące połączenie z Internetem jest dozwolone bezpośrednio z sieci wirtualnej, a aplikacje klienckie mogą łączyć się z innymi punktami końcowymi platformy Azure, w tym z usługą Azure cache for Redis.

Jednak typową konfiguracją klienta jest korzystanie z wymuszonego tunelowania (anonsowanie trasy domyślnej), które wymusza ruch wychodzący z Internetu zamiast przepływu lokalnego. Ten przepływ ruchu zrywa łączność z usługą Azure cache for Redis, jeśli ruch wychodzący jest następnie blokowany lokalnie w taki sposób, aby wystąpienie usługi Azure cache for Redis nie mogło komunikować się ze swoimi zależnościami.

Rozwiązanie polega na zdefiniowaniu co najmniej jednej trasy zdefiniowanej przez użytkownika (UDR) w podsieci zawierającej pamięć podręczną platformy Azure dla Redis. UDR definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe, zaleca się użycie następującej konfiguracji:

* Konfiguracja ExpressRoute anonsuje 0.0.0.0/0, a domyślnie tuneluje cały ruch wychodzący w środowisku lokalnym.
* UDR zastosowana do podsieci zawierającej pamięć podręczną platformy Azure dla Redis definiuje 0.0.0.0/0 z działającą trasą dla ruchu TCP/IP do publicznego Internetu; na przykład poprzez ustawienie typu następnego przeskoku na "Internet".

Połączony efekt tych kroków polega na tym, że poziom podsieci UDR ma pierwszeństwo przed wymuszonym tunelowaniem ExpressRoute, dzięki czemu umożliwia wychodzący dostęp do Internetu z pamięci podręcznej platformy Azure dla Redis.

Nawiązywanie połączenia z usługą Azure cache for Redis z aplikacji lokalnej przy użyciu ExpressRoute nie jest typowym scenariuszem użycia ze względu na wydajność (w celu uzyskania najlepszej wydajności pamięci podręcznej platformy Azure dla klientów Redis powinna znajdować się w tym samym regionie co pamięć podręczna platformy Azure dla Redis).

>[!IMPORTANT] 
>Trasy zdefiniowane w UDR **muszą** być wystarczająco odpowiednie, aby mieć pierwszeństwo przed wszelkimi trasami anonsowanymi przez konfigurację ExpressRoute. W poniższym przykładzie użyto szerokiego zakresu adresów 0.0.0.0/0, ponieważ może to spowodować przypadkowe zastąpienie anonsów tras przy użyciu bardziej szczegółowych zakresów adresów.

>[!WARNING]  
>Usługa Azure cache for Redis nie jest obsługiwana w przypadku konfiguracji ExpressRoute, które **niepoprawnie anonsują trasy z publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej**. Konfiguracje ExpressRoute, które mają skonfigurowaną publiczną komunikację równorzędną, odbierają anonse tras od firmy Microsoft w celu uzyskania dużego zestawu Microsoft Azure zakresów adresów IP. Jeśli te zakresy adresów są nieprawidłowo anonsowane krzyżowo w ścieżce prywatnej komunikacji równorzędnej, wynikiem jest to, że wszystkie pakiety sieciowe wychodzące z podsieci wystąpienia usługi Azure cache for Redis są nieprawidłowo wymuszane przez tunelowanie do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieci powoduje uszkodzenie pamięci podręcznej platformy Azure dla Redis. Rozwiązaniem tego problemu jest zatrzymanie tras krzyżowego anonsowania od publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.


Ogólne informacje dotyczące tras zdefiniowanych przez użytkownika są dostępne w tym [omówieniu](../virtual-network/virtual-networks-udr-overview.md).

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [Omówienie techniczne ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
