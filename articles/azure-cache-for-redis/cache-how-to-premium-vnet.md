---
title: Konfigurowanie usługi Virtual Network — warstwa Premium pamięć podręczna Azure dla wystąpienia Redis
description: Dowiedz się, jak utworzyć i zarządzać obsługą sieci wirtualnej dla usługi Azure cache w warstwie Premium dla wystąpienia Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375276"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Konfigurowanie obsługi sieci wirtualnej dla wystąpienia usługi Redis w warstwie Premium

Wdrożenie [usługi Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) zapewnia ulepszone zabezpieczenia i izolację oraz podsieci, zasady kontroli dostępu i inne funkcje, aby dodatkowo ograniczyć dostęp. Gdy usługa Azure cache for Redis jest skonfigurowana przy użyciu sieci wirtualnej, nie jest ona publicznie dostępna i można uzyskać do niej dostęp tylko z maszyn wirtualnych i aplikacji w sieci wirtualnej. W tym artykule opisano sposób konfigurowania obsługi sieci wirtualnej dla wystąpienia usługi Azure cache w warstwie Premium dla Redis.

> [!NOTE]
> Usługa Azure cache for Redis obsługuje zarówno klasyczny model wdrażania, jak i Azure Resource Manager sieci wirtualne.
> 

## <a name="set-up-virtual-network-support"></a>Konfigurowanie obsługi sieci wirtualnej

Obsługa sieci wirtualnej jest konfigurowana w okienku **nowej usługi Azure cache for Redis** podczas tworzenia pamięci podręcznej.

1. Aby utworzyć pamięć podręczną w warstwie Premium, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. Oprócz tworzenia pamięci podręcznych w Azure Portal można je również utworzyć za pomocą szablonów Menedżer zasobów, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat tworzenia wystąpienia usługi Azure cache for Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Zrzut ekranu przedstawiający tworzenie zasobu.":::
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych**. Następnie wybierz pozycję **pamięć podręczna platformy Azure dla Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Zrzut ekranu pokazujący wybór usługi Azure cache for Redis.":::

1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia nowej pamięci podręcznej warstwy Premium.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, które zawierają tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć literą lub cyfrą i nie może zawierać kolejnych łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . Redis.cache.Windows.NET*. |
   | **Subskrypcja** | Wybierz swoją subskrypcję z listy rozwijanej. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. |
   | **Grupa zasobów** | Wybierz grupę zasobów z listy rozwijanej lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. |
   | **Lokalizacja** | Z listy rozwijanej wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Typ pamięci podręcznej** |Wybierz pamięć podręczną warstwy Premium z listy rozwijanej, aby skonfigurować funkcje warstwy Premium. Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure cache for Redis](cache-overview.md). |

1. Wybierz kartę **Sieć** lub przycisk **sieci** w dolnej części strony.

1. Na karcie **Sieć** wybierz pozycję **sieci wirtualne** jako metodę łączności. Aby użyć nowej sieci wirtualnej, utwórz ją najpierw, wykonując kroki opisane w temacie [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) lub [Tworzenie sieci wirtualnej (klasycznej) przy użyciu Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Następnie wróć do **nowego okienka usługi Azure cache for Redis** , aby utworzyć i skonfigurować pamięć podręczną warstwy Premium.

   > [!IMPORTANT]
   > W przypadku wdrażania usługi Azure cache for Redis w sieci wirtualnej Menedżer zasobów pamięć podręczna musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych innych zasobów z wyjątkiem usługi Azure cache for Redis Instances. Jeśli próbujesz wdrożyć wystąpienie usługi Azure cache for Redis w podsieci sieci wirtualnej Menedżer zasobów zawierającej inne zasoby, wdrożenie zakończy się niepowodzeniem.
   > 
   > 

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sieć wirtualna** | Z listy rozwijanej wybierz sieć wirtualną. | Wybierz sieć wirtualną znajdującą się w tej samej subskrypcji i lokalizacji co pamięć podręczna. |
   | **Podsieć** | Wybierz podsieć z listy rozwijanej. | Zakres adresów podsieci powinien być w notacji CIDR (na przykład 192.168.1.0/24). Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej. |
   | **Statyczny adres IP** | Obowiązkowe Wprowadź statyczny adres IP. | Jeśli nie określisz statycznego adresu IP, adres IP zostanie wybrany automatycznie. |

   > [!IMPORTANT]
   > Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, każde wystąpienie usługi Azure cache for Redis w podsieci używa dwóch adresów IP na fragmentu i jednego dodatkowego adresu IP dla modułu równoważenia obciążenia. Nieklastrowana pamięć podręczna jest uważana za jedną fragmentu.
   > 

1. Wybierz kartę **Dalej: Zaawansowane** lub wybierz przycisk **Dalej: Zaawansowane** w dolnej części strony.

1. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej w warstwie Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowanie i trwałość danych.

1. Wybierz kartę **następne: Tagi** lub wybierz przycisk **Dalej: Tagi** w dolnej części strony.

1. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób.

1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do karty **Recenzja + tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Po wyświetleniu komunikatu o pomyślnym **sprawdzeniu poprawności** , wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. Po utworzeniu pamięci podręcznej można wyświetlić konfigurację sieci wirtualnej, wybierając pozycję **Virtual Network** z menu **zasób** .

![Sieć wirtualna][redis-cache-vnet-info]

Aby połączyć się z wystąpieniem usługi Azure cache for Redis w przypadku korzystania z sieci wirtualnej, należy określić nazwę hosta pamięci podręcznej w parametrach połączenia, jak pokazano w następującym przykładzie:

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

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Usługa Azure cache for Redis — często zadawane pytania

Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące skalowania usługi Azure cache for Redis.

* Jak często występują problemy z błędami konfiguracji w usłudze Azure cache dla Redis i sieci wirtualnych?
* [Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Podczas próby nawiązania połączenia z usługą Azure cache for Redis w sieci wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?
* [Czy można używać sieci wirtualnych ze standardową lub podstawową pamięcią podręczną?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Dlaczego tworzenie pamięci podręcznej platformy Azure dla wystąpienia Redis kończy się niepowodzeniem w niektórych podsieciach, ale nie w innych?
* [Jakie są wymagania dotyczące przestrzeni adresowej podsieci?](#what-are-the-subnet-address-space-requirements)
* [Czy wszystkie funkcje pamięci podręcznej działają, gdy pamięć podręczna jest hostowana w sieci wirtualnej?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Jak często występują problemy z błędami konfiguracji w usłudze Azure cache dla Redis i sieci wirtualnych?

Gdy usługa Azure cache for Redis jest hostowana w sieci wirtualnej, używane są porty w poniższych tabelach.

>[!IMPORTANT]
>Jeśli porty w poniższych tabelach są blokowane, pamięć podręczna może nie działać poprawnie. Jeśli co najmniej jeden z tych portów jest zablokowany, jest to najbardziej typowy problem z konfiguracją w przypadku korzystania z usługi Azure cache for Redis w sieci wirtualnej.
> 

- [Wymagania dotyczące portów wychodzących](#outbound-port-requirements)
- [Wymagania dotyczące portów przychodzących](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących

Istnieją dziewięć wymagań portu wychodzącego. Żądania wychodzące w tych zakresach są wychodzące do innych usług niezbędnych do funkcjonowania pamięci podręcznej lub wewnętrznej podsieci Redis na potrzeby komunikacji między węzłami. W przypadku replikacji geograficznej istnieją dodatkowe wymagania wychodzące dotyczące komunikacji między podsieciami podstawowej i pamięci podręcznej repliki.

| Porty | Kierunek | Protokół transportowy | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Wychodzący |TCP |Redis zależności w usłudze Azure Storage/PKI (Internet) | (Podsieć Redis) |* <sup>czwart</sup> |
| 443 | Wychodzący | TCP | Redis zależność od Azure Key Vault i Azure Monitor | (Podsieć Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Wychodzący |TCP/UDP |Redis zależności w systemie DNS (Internet/Sieć wirtualna) | (Podsieć Redis) | 168.63.129.16 i 169.254.169.254 <sup>2</sup> oraz dowolny niestandardowy serwer DNS dla podsieci <sup>3</sup> |
| 8443 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 10221-10231 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) | (Podsieć Redis) |
| 20226 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 13000-13999 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 15000-15999 |Wychodzący |TCP |Komunikacja wewnętrzna Redis i replikacja geograficzna | (Podsieć Redis) |(Podsieć Redis) (Podsieć równorzędna repliki geograficznej) |
| 6379-6380 |Wychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> można użyć tagów usług AzureKeyVault i AzureMonitor z Menedżer zasobów sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń).

<sup>2</sup> te adresy IP należące do firmy Microsoft są używane do ADRESOWANIA maszyny wirtualnej hosta, która obsługuje Azure DNS.

<sup>3</sup> te informacje nie są używane w przypadku podsieci bez niestandardowych serwerów DNS lub nowszych pamięci podręcznych Redis, które ignorują niestandardowy system DNS.

<sup>4</sup> Aby uzyskać więcej informacji, zobacz [dodatkowe wymagania dotyczące łączności z siecią wirtualną](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Wymagania dotyczące portów równorzędnych replikacji geograficznej

Jeśli używasz replikacji geograficznej między pamięciami podręcznymi w sieciach wirtualnych platformy Azure, Odblokuj porty 15000-15999 dla całej podsieci w kierunku przychodzącym *i* wychodzącym do obu pamięci podręcznych. W przypadku tej konfiguracji wszystkie składniki repliki w podsieci mogą komunikować się bezpośrednio ze sobą nawet wtedy, gdy jest to przyszłe geograficznie tryb failover.

#### <a name="inbound-port-requirements"></a>Wymagania dotyczące portów przychodzących

Istnieją osiem wymagań zakresu portów przychodzących. Żądania przychodzące w tych zakresach są przychodzące z innych usług hostowanych w tej samej sieci wirtualnej lub wewnętrznych dla komunikacji z podsiecią Redis.

| Porty | Kierunek | Protokół transportowy | Przeznaczenie | Lokalny adres IP | Zdalny adres IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Przychodzący |TCP |Komunikacja z klientem do Redis, równoważenie obciążenia platformy Azure | (Podsieć Redis) | (Podsieć Redis), (podsieć klienta), AzureLoadBalancer <sup>1</sup> |
| 8443 |Przychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |
| 8500 |Przychodzący |TCP/UDP |Równoważenie obciążenia na platformie Azure | (Podsieć Redis) | AzureLoadBalancer |
| 10221-10231 |Przychodzący |TCP |Komunikacja z klientami z klastrami Redis, wewnętrzna komunikacja dla Redis | (Podsieć Redis) |(Podsieć Redis), AzureLoadBalancer, (podsieć klienta) |
| 13000-13999 |Przychodzący |TCP |Komunikacja klienta z klastrami Redis, usługa równoważenia obciążenia platformy Azure | (Podsieć Redis) | (Podsieć Redis), (podsieć klienta), AzureLoadBalancer |
| 15000-15999 |Przychodzący |TCP |Komunikacja z klientem do klastrów Redis, równoważenia obciążenia platformy Azure i replikacji geograficznej | (Podsieć Redis) | (Podsieć Redis), (podsieć klienta), AzureLoadBalancer, (podsieć równorzędna z repliką geograficzną) |
| 16001 |Przychodzący |TCP/UDP |Równoważenie obciążenia na platformie Azure | (Podsieć Redis) | AzureLoadBalancer |
| 20226 |Przychodzący |TCP |Komunikacja wewnętrzna dla Redis | (Podsieć Redis) |(Podsieć Redis) |

<sup>1</sup> możesz użyć AzureLoadBalancer znacznika usługi dla Menedżer zasobów lub AZURE_LOADBALANCER dla klasycznego modelu wdrażania do tworzenia reguł sieciowej grupy zabezpieczeń.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Dodatkowe wymagania dotyczące łączności z siecią wirtualną

Istnieją wymagania dotyczące łączności sieciowej dla usługi Azure cache for Redis, które mogą nie być początkowo spełnione w sieci wirtualnej. Usługa Azure cache for Redis wymaga, aby wszystkie następujące elementy funkcjonowały prawidłowo, gdy są używane w sieci wirtualnej:

* Wychodząca łączność sieciowa z punktami końcowymi usługi Azure Storage w świecie. Punkty końcowe znajdujące się w tym samym regionie co pamięć podręczna platformy Azure dla wystąpienia Redis i punkty końcowe magazynu znajdujące się w *innych* regionach platformy Azure są uwzględniane. Punkty końcowe usługi Azure Storage są rozwiązywane w następujących domenach DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *Queue.Core.Windows.NET* i *File.Core.Windows.NET*.
* Wychodząca łączność sieciowa z *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* i *CRL.Microsoft.com*. Ta łączność jest wymagana do obsługi funkcji TLS/SSL.
* Konfiguracja systemu DNS dla sieci wirtualnej musi być w stanie rozwiązać wszystkie punkty końcowe i domeny wymienione w poprzednich punktach. Te wymagania dotyczące usługi DNS można spełnić, upewniając się, że dla sieci wirtualnej jest skonfigurowana i utrzymywana prawidłowa infrastruktura DNS.
* Wychodząca łączność sieciowa z następującymi Azure Monitor punktami końcowymi, które są rozpoznawane w następujących domenach DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.NET*, *North-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Black.azglobal.Metrics.nsatc.NET*, *shoebox2-Red.shoebox2.Metrics.nsatc.NET*, *East-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Red.azglobal.Metrics.nsatc.NET*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-Red.prod.microsoftmetrics.com* i *shoebox3-Black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Jak sprawdzić, czy moja pamięć podręczna działa w sieci wirtualnej?

>[!IMPORTANT]
>Po nawiązaniu połączenia z usługą Azure cache for Redis, która jest hostowana w sieci wirtualnej, klienci pamięci podręcznej muszą znajdować się w tej samej sieci wirtualnej lub w sieci wirtualnej z włączonymi równorzędnymi sieciami wirtualnymi w tym samym regionie świadczenia usługi Azure. Globalne wirtualne sieci równorzędne nie są obecnie obsługiwane. To wymaganie dotyczy wszystkich aplikacji testowych lub narzędzi ping diagnostycznych. Bez względu na to, gdzie jest hostowana aplikacja kliencka, sieciowych grup zabezpieczeń lub inne warstwy sieciowe muszą być skonfigurowane tak, aby ruch sieciowy klienta mógł dotrzeć do usługi Azure cache for Redis instance.
>

Po skonfigurowaniu wymagań dotyczących portów zgodnie z opisem w poprzedniej sekcji można sprawdzić, czy pamięć podręczna działa, wykonując następujące czynności:

- [Uruchom ponownie](cache-administration.md#reboot) wszystkie węzły pamięci podręcznej. Jeśli nie można osiągnąć wszystkich wymaganych zależności pamięci podręcznej, jak opisano w [wymaganiach dotyczących portów przychodzących](cache-how-to-premium-vnet.md#inbound-port-requirements) i [wymaganiach dotyczących portów wychodzących](cache-how-to-premium-vnet.md#outbound-port-requirements), pamięć podręczna nie będzie mogła zostać pomyślnie uruchomiona ponownie.
- Po ponownym uruchomieniu węzłów pamięci podręcznej zgodnie z informacjami o stanie pamięci podręcznej w Azure Portal można wykonać następujące testy:
  - Wyślij polecenie ping do punktu końcowego pamięci podręcznej przy użyciu portu 6380 z komputera, który znajduje się w tej samej sieci wirtualnej co pamięć podręczna, przy użyciu [tcping](https://www.elifulkerson.com/projects/tcping.php). Na przykład:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Jeśli `tcping` Narzędzie zgłasza, że port jest otwarty, pamięć podręczna jest dostępna do połączenia z klientami w sieci wirtualnej.

  - Innym sposobem na przetestowanie jest utworzenie klienta testowej pamięci podręcznej (który może być prostą aplikacją konsolową przy użyciu StackExchange. Redis), który łączy się z pamięcią podręczną i dodaje i pobiera niektóre elementy z pamięci podręcznej. Zainstaluj przykładową aplikację kliencką na maszynie wirtualnej znajdującej się w tej samej sieci wirtualnej co pamięć podręczna. Następnie należy uruchomić go w celu sprawdzenia łączności z pamięcią podręczną.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Podczas próby nawiązania połączenia z usługą Azure cache for Redis w sieci wirtualnej, Dlaczego otrzymuję komunikat o błędzie informujący, że certyfikat zdalny jest nieprawidłowy?

Podczas próby nawiązania połączenia z usługą Azure cache for Redis w sieci wirtualnej zostanie wyświetlony następujący błąd sprawdzania poprawności certyfikatu:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Przyczyną może być nawiązanie połączenia z hostem za pomocą adresu IP. Zalecamy użycie nazwy hosta. Innymi słowy, użyj następującego ciągu:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Należy unikać używania adresu IP podobnego do następujących parametrów połączenia:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Jeśli nie można rozpoznać nazwy DNS, niektóre biblioteki klienta obejmują opcje konfiguracji, takie jak `sslHost` , dostarczone przez klienta stackexchange. Redis. Ta opcja umożliwia przesłonięcie nazwy hosta używanej do sprawdzania poprawności certyfikatu. Na przykład:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Czy można używać sieci wirtualnych ze standardową lub podstawową pamięcią podręczną?

Sieci wirtualnych można używać tylko z pamięcią podręczną warstwy Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Dlaczego tworzenie pamięci podręcznej platformy Azure dla wystąpienia Redis kończy się niepowodzeniem w niektórych podsieciach, ale nie w innych?

W przypadku wdrażania wystąpienia usługi Azure cache for Redis w sieci wirtualnej pamięć podręczna musi znajdować się w dedykowanej podsieci, która nie zawiera innego typu zasobu. Jeśli podjęto próbę wdrożenia wystąpienia usługi Azure cache for Redis w podsieci sieci wirtualnej Menedżer zasobów zawierającej inne zasoby, takie jak wystąpienia Application Gateway platformy Azure i wychodzące NAT, wdrożenie zazwyczaj zakończy się niepowodzeniem. Należy usunąć istniejące zasoby innych typów, aby można było utworzyć nowe wystąpienie usługi Azure cache for Redis.

Musisz również mieć wystarczającą liczbę adresów IP w podsieci.

### <a name="what-are-the-subnet-address-space-requirements"></a>Jakie są wymagania dotyczące przestrzeni adresowej podsieci?

Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure, w każdej pamięci podręcznej platformy Azure dla wystąpienia Redis w podsieci są używane dwa adresy IP na fragmentu klastra, a także dodatkowe adresy IP dla dodatkowych replik (jeśli istnieją). Jeden dodatkowy adres IP jest używany dla usługi równoważenia obciążenia. Nieklastrowana pamięć podręczna jest uważana za jedną fragmentu.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Czy wszystkie funkcje pamięci podręcznej działają, gdy pamięć podręczna jest hostowana w sieci wirtualnej?

Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do pamięci podręcznej. W związku z tym następujące funkcje zarządzania pamięcią podręczną nie działają w tym momencie:

* **Konsola Redis**: ponieważ konsola Redis działa w przeglądarce lokalnej, która zwykle znajduje się na komputerze dewelopera, który nie jest połączony z siecią wirtualną, nie może nawiązać połączenia z pamięcią podręczną.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Używanie ExpressRoute z usługą Azure cache dla Redis

Klienci mogą łączyć obwód [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z infrastrukturą sieci wirtualnej. W ten sposób rozszerzy one sieć lokalną na platformę Azure.

Domyślnie nowo utworzony obwód ExpressRoute nie wykonuje wymuszonego tunelowania (anons trasy domyślnej, 0.0.0.0/0) w sieci wirtualnej. W związku z tym wychodzące połączenie z Internetem jest dozwolone bezpośrednio z sieci wirtualnej. Aplikacje klienckie mogą łączyć się z innymi punktami końcowymi platformy Azure, które obejmują wystąpienie usługi Azure cache for Redis.

Typową konfiguracją klienta jest użycie wymuszonego tunelowania (anonsowanie trasy domyślnej), które wymusza ruch wychodzący z Internetu zamiast przepływu lokalnego. Ten przepływ ruchu zrywa łączność z usługą Azure cache for Redis, jeśli ruch wychodzący jest następnie blokowany lokalnie w taki sposób, aby wystąpienie usługi Azure cache for Redis nie mogło komunikować się ze swoimi zależnościami.

Rozwiązanie polega na zdefiniowaniu co najmniej jednej trasy zdefiniowanej przez użytkownika (UDR) w podsieci zawierającej wystąpienie usługi Azure cache for Redis. UDR definiuje trasy specyficzne dla podsieci, które będą honorowane zamiast trasy domyślnej.

Jeśli to możliwe, należy użyć następującej konfiguracji:

* Konfiguracja ExpressRoute anonsuje 0.0.0.0/0 i domyślnie wymusi tuneluje cały ruch wychodzący w środowisku lokalnym.
* UDR zastosowana do podsieci, która zawiera pamięć podręczną platformy Azure dla wystąpienia Redis definiuje 0.0.0.0/0 z działającą trasą dla ruchu TCP/IP do publicznego Internetu. Na przykład ustawia typ następnego przeskoku na *Internet*.

Połączony efekt tych kroków polega na tym, że UDR poziomu podsieci ma pierwszeństwo przed wymuszonym tunelowaniem ExpressRoute, co zapewnia wychodzący dostęp do Internetu z poziomu pamięci podręcznej platformy Azure dla wystąpienia Redis.

Nawiązywanie połączenia z usługą Azure cache for Redis z aplikacji lokalnej przy użyciu ExpressRoute nie jest typowym scenariuszem użycia ze względu na wydajność. Aby uzyskać najlepszą wydajność, pamięć podręczna systemu Azure dla klientów Redis powinna znajdować się w tym samym regionie co pamięć podręczna platformy Azure dla wystąpienia Redis.

>[!IMPORTANT]
>Trasy zdefiniowane w UDR *muszą* być wystarczająco odpowiednie, aby mieć pierwszeństwo przed wszelkimi trasami anonsowanymi przez konfigurację ExpressRoute. W poniższym przykładzie użyto szerokiego zakresu adresów 0.0.0.0/0 i w związku z tym może zostać przypadkowo zastąpiony przez anonse trasy korzystające z bardziej szczegółowych zakresów adresów.

>[!WARNING]
>Usługa Azure cache for Redis nie jest obsługiwana w przypadku konfiguracji ExpressRoute, które *niepoprawnie anonsują trasy z publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej*. Konfiguracje ExpressRoute, które mają skonfigurowaną publiczną komunikację równorzędną, odbierają Anonsy tras od firmy Microsoft w ramach dużego zestawu Microsoft Azure zakresów adresów IP. Jeśli te zakresy adresów są nieprawidłowo anonsowane krzyżowo w ścieżce prywatnej komunikacji równorzędnej, wynikiem jest to, że wszystkie pakiety sieciowe wychodzące z podsieci wystąpienia usługi Azure cache for Redis są nieprawidłowo wymuszane przez tunelowanie do lokalnej infrastruktury sieciowej klienta. Ten przepływ sieci powoduje uszkodzenie pamięci podręcznej platformy Azure dla Redis. Rozwiązaniem tego problemu jest zatrzymanie tras krzyżowego anonsowania od publicznej ścieżki komunikacji równorzędnej do ścieżki prywatnej komunikacji równorzędnej.

Informacje ogólne na temat UDR są dostępne w obszarze [routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [Omówienie techniczne ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
