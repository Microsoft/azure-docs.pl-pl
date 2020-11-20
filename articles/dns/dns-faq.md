---
title: Często zadawane pytania — Azure DNS
description: W tym artykule zapoznaj się z często zadawanymi pytaniami dotyczącymi Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 2f7e4eadc25028db4668db8d245803c7ddba8688
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968251"
---
# <a name="azure-dns-faq"></a>Azure DNS często zadawane pytania

## <a name="about-azure-dns"></a>Informacje o Azure DNS

### <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

System nazw domen (DNS) tłumaczy lub rozpoznaje nazwę witryny sieci Web lub usługi na adres IP. Usługa Azure DNS jest usługą hostingu dla domen DNS. Zapewnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Domeny DNS w Azure DNS są hostowane w globalnej sieci Azure serwerów nazw DNS. Ten system używa sieci emisji dowolnej, aby każde zapytanie DNS zostało odebrane przez najbliższy dostępny serwer DNS. Azure DNS zapewnia szybką wydajność i wysoką dostępność dla domeny.

Azure DNS jest oparty na Azure Resource Manager. Azure DNS korzyści z funkcji Menedżer zasobów, takich jak kontrola dostępu oparta na rolach na platformie Azure, dzienniki inspekcji i blokowanie zasobów. Można zarządzać domenami i rekordami za pomocą Azure Portal, Azure PowerShell poleceń cmdlet i międzyplatformowego interfejsu wiersza polecenia platformy Azure. Aplikacje wymagające automatycznego zarządzania systemem DNS można zintegrować z usługą za pośrednictwem interfejsu API REST i zestawów SDK.

### <a name="how-much-does-azure-dns-cost"></a>Ile kosztuje Azure DNS?

Model rozliczeń Azure DNS jest oparty na liczbie stref DNS hostowanych w Azure DNS. Jest on również oparty na liczbie otrzymywanych zapytań DNS. Rabaty są podawane na podstawie użycia.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaki jest poziom umowy SLA dla usługi Azure DNS?

Platforma Azure gwarantuje, że prawidłowe żądania DNS odbierają odpowiedź od co najmniej jednego Azure DNS nazwy serwera 100% czasu.

Aby uzyskać więcej informacji, zobacz [stronę umów SLA Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co to jest strefa DNS? Czy jest taka sama jak domena DNS? 

Domena jest unikatową nazwą w systemie nazw domen. Przykładowa domena to contoso.com.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład domena contoso.com może zawierać kilka rekordów DNS. Rekordy mogą zawierać mail.contoso.com dla serwera poczty i sieci Web \. contoso.com dla witryny internetowej. Te rekordy są hostowane w strefie DNS contoso.com.

Nazwa domeny jest *tylko nazwą*. Strefa DNS jest zasobem danych zawierającym rekordy DNS dla nazwy domeny. Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Zapewnia również serwerom nazw DNS odpowiedzi na zapytania DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Czy muszę kupić nazwę domeny DNS, aby użyć Azure DNS? 

Niekoniecznie.

Nie trzeba kupować domeny w celu hostowania strefy DNS w Azure DNS. Strefę DNS można utworzyć w dowolnym momencie bez właściciela nazwy domeny. Zapytania DNS dla tej strefy są rozwiązywane tylko wtedy, gdy są kierowane do Azure DNS serwerów nazw przypisanych do strefy.

Aby połączyć strefę DNS z globalną hierarchią DNS, należy zakupić nazwę domeny. Następnie zapytania DNS z dowolnego miejsca na świecie znajdą strefę DNS i odpowiadają za pomocą rekordów DNS.

## <a name="azure-dns-features"></a>Funkcje Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Czy istnieją ograniczenia w przypadku używania rekordów aliasów dla wierzchołka nazwy domeny z Traffic Manager?

Tak. Statyczne publiczne adresy IP należy używać z usługą Azure Traffic Manager. Skonfiguruj docelowy **zewnętrzny punkt końcowy** przy użyciu statycznego adresu IP. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Czy Azure DNS obsługuje routing ruchu opartego na systemie DNS lub tryb failover punktu końcowego?

Routing ruchu oparty na systemie DNS i tryb failover punktu końcowego są udostępniane przez Traffic Manager. Traffic Manager to oddzielna usługa platformy Azure, która może być używana z Azure DNS. Aby uzyskać więcej informacji, zobacz [omówienie Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS obsługuje tylko hostowanie statycznych domen DNS, gdzie każde zapytanie DNS dla danego rekordu DNS zawsze otrzymuje tę samą odpowiedź DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Czy Azure DNS obsługiwać rejestrację nazw domen?

Nie. Azure DNS nie obsługuje obecnie opcji kupowania nazw domen. Aby kupić domeny, należy użyć rejestratora nazw domen innej firmy. Rejestrator zazwyczaj obciąża małą roczną opłatą. Domeny mogą być hostowane w Azure DNS na potrzeby zarządzania rekordami DNS. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](dns-domain-delegation.md).

Funkcja kupowania nazw domen jest śledzona w zaległości platformy Azure. Skorzystaj z witryny opinii, aby [zarejestrować obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Czy Azure DNS obsługuje rozszerzenia DNSSEC?

Nie. Azure DNS nie obsługuje obecnie rozszerzeń zabezpieczeń systemu nazw domen (DNSSEC).

Funkcja DNSSEC jest śledzona w zaległości Azure DNS. Skorzystaj z witryny opinii, aby [zarejestrować obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Czy Azure DNS obsługuje transfery stref (AXFR/IXFR)?

Nie. Azure DNS nie obsługuje obecnie transferów stref. Strefy DNS można [zaimportować do Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure](dns-import-export.md). Rekordy DNS są zarządzane za pośrednictwem [portalu zarządzania Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](/powershell/module/az.dns), [zestawu SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md)lub [Narzędzia interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

Funkcja transferu strefy jest śledzona w zaległości Azure DNS. Skorzystaj z witryny opinii, aby [zarejestrować obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Czy Azure DNS obsługuje przekierowania adresów URL?

Nie. Usługi przekierowywania adresów URL nie są usługą DNS. Działają one na poziomie HTTP, a nie na poziomie systemu DNS. Niektórzy dostawcy DNS tworzą usługę przekierowywania adresów URL w ramach ich ogólnej oferty. Ta usługa nie jest obecnie obsługiwana przez Azure DNS.

Funkcja przekierowywania adresów URL jest śledzona w zaległości Azure DNS. Skorzystaj z witryny opinii, aby [zarejestrować obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Czy Azure DNS obsługuje rozszerzone kodowanie ASCII (8-bitowe) ustawione dla zestawów rekordów TXT?

Tak. Azure DNS obsługuje rozszerzony zestaw kodowania ASCII dla zestawów rekordów TXT. Należy jednak użyć najnowszej wersji interfejsów API REST platformy Azure, zestawów SDK, programu PowerShell i interfejsu wiersza polecenia. Wersje starsze niż 1 października 2017 lub zestaw SDK 2,1 nie obsługują rozszerzonego zestawu ASCII. 

Można na przykład podać ciąg jako wartość rekordu TXT, który ma rozszerzony znak ASCII \ 128. Przykładem jest "abcd\128efgh." Azure DNS używa wartości Byte tego znaku, czyli 128 w wewnętrznej reprezentacji. W momencie rozpoznawania nazw DNS ta wartość bajtu jest zwracana w odpowiedzi. Należy również zauważyć, że "ABC" i "\ 097 \ 098 \ 099" są zamienne, o ile dotyczy to rozwiązania. 

Stosujemy reguły ucieczki pliku strefy [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) dla rekordów TXT. Na przykład `\` teraz wyprowadza wszystkie informacje na temat specyfikacji RFC. Jeśli określisz `A\B` jako wartość rekordu TXT, zostanie ona reprezentowana i rozwiązany jako tylko `AB` . Jeśli na pewno chcesz, aby rekord TXT miał swoją `A\B` rozdzielczość, należy ponownie wprowadzić zmiany `\` . Na przykład określ `A\\B` .

Ta obsługa jest obecnie niedostępna dla rekordów TXT utworzonych na podstawie Azure Portal.

## <a name="alias-records"></a>Rekordy aliasu

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Co to są scenariusze, w których rekordy aliasów są przydatne?

Zobacz sekcję scenariusze w [Azure DNS Omówienie rekordów aliasów](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jakie typy rekordów są obsługiwane w przypadku zestawów rekordów aliasów?

Zestawy rekordów aliasów są obsługiwane dla następujących typów rekordów w strefie Azure DNS:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jakie zasoby są obsługiwane jako elementy docelowe dla zestawów rekordów aliasu?

- **Wskaż zasób publicznego adresu IP z zestawu rekordów A/AAAA systemu DNS.** Można utworzyć zestaw rekordów A/AAAA i ustawić dla niego rekord aliasu, aby wskazywał na zasób publicznego adresu IP.
- **Wskaż profil Traffic Manager z zestawu rekordów A/AAAA/CNAME systemu DNS.** Możesz wskazać rekord CNAME Traffic Manager profilu z zestawu rekordów CNAME systemu DNS. Przykładem jest contoso.trafficmanager.net. Teraz można również wskazać profil Traffic Manager, który ma zewnętrzne punkty końcowe z rekordu A lub AAAA zestawu rekordów w strefie DNS.
- **Wskaż punkt końcowy usługi Azure Content Delivery Network (CDN)**. Jest to przydatne w przypadku tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i Azure CDN.
- **Wskaż inny rekord DNS ustawiony w ramach tej samej strefy.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. To rozwiązanie jest przydatne, jeśli chcesz, aby niektóre rekordy miały aliasy i niektóre inne niż aliasy.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Czy można tworzyć i aktualizować rekordy aliasów z Azure Portal?

Tak. Można tworzyć rekordy aliasów i zarządzać nimi w Azure Portal wraz z interfejsami API REST platformy Azure, programem PowerShell, interfejsem wiersza polecenia i zestawami SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Czy alias zostanie zarejestrowana w celu zapewnienia, że mój zestaw rekordów DNS jest usuwany po usunięciu źródłowego publicznego adresu IP?

Tak. Ta funkcja jest jedną z podstawowych możliwości rekordów aliasów. Pozwala to uniknąć potencjalnego przestoju dla użytkowników aplikacji.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Czy alias rejestruje pomoc, aby upewnić się, że mój zestaw rekordów DNS został zaktualizowany do poprawnego adresu IP, gdy źródłowy publiczny adres IP ulegnie zmianie?

Tak. Ta funkcja jest jedną z podstawowych możliwości rekordów aliasów. Pozwala to uniknąć potencjalnych zagrożeń związanych z awarią lub zabezpieczeniami aplikacji.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Czy istnieją ograniczenia w przypadku używania zestawów rekordów aliasów dla rekordów A lub AAAA do wskazywania Traffic Manager?

Tak. Aby wskazać profil Traffic Manager jako alias z zestawu rekordów A lub AAAA, profil Traffic Manager musi używać tylko zewnętrznych punktów końcowych. Podczas tworzenia zewnętrznych punktów końcowych w Traffic Manager Podaj rzeczywiste adresy IP punktów końcowych.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Czy istnieje dodatkowa opłata za korzystanie z rekordów aliasów?

Rekordy aliasów są kwalifikacją dla prawidłowego zestawu rekordów DNS. Nie ma dodatkowych opłat za rekordy aliasów.

## <a name="use-azure-dns"></a>Użyj Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Czy mogę współhostować domenę przy użyciu Azure DNS i innego dostawcy DNS?

Tak. Azure DNS obsługuje domeny współhostingu z innymi usługami DNS.

Aby skonfigurować współhosting, należy zmodyfikować rekordy NS dla domeny w taki sposób, aby wskazywały serwery nazw obu dostawców. Rekordy serwera nazw (NS) określają, którzy dostawcy odbierają zapytania DNS dla domeny. Te rekordy NS można modyfikować w Azure DNS, w drugim dostawcy i w strefie nadrzędnej. Strefa nadrzędna jest zazwyczaj konfigurowana za pośrednictwem rejestratora nazw domen. Aby uzyskać więcej informacji na temat delegowania usługi DNS, zobacz [delegowanie domeny DNS](dns-domain-delegation.md).

Upewnij się również, że rekordy DNS dla domeny są zsynchronizowane między obydwoma dostawcami DNS. Azure DNS nie obsługuje obecnie transferów stref DNS. Rekordy DNS należy synchronizować przy użyciu [portalu zarządzania Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](/rest/api/dns/), [zestawu SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md)lub [Narzędzia interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Czy muszę delegować domenę do wszystkich czterech Azure DNS serwerów nazw?

Tak. Azure DNS przypisuje cztery serwery nazw do każdej strefy DNS. To rozwiązanie służy do izolacji błędów i zwiększonej odporności. Aby zakwalifikować się do umowy SLA Azure DNS, należy delegować domenę do wszystkich czterech serwerów nazw.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jakie są limity użycia Azure DNS?

W przypadku korzystania z Azure DNS są stosowane następujące domyślne limity.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Czy mogę przenieść strefę Azure DNS między grupami zasobów lub między subskrypcjami?

Tak. Strefy DNS można przenosić między grupami zasobów lub między subskrypcjami.

Podczas przenoszenia strefy DNS nie ma wpływu na zapytania DNS. Serwery nazw przypisane do strefy pozostają takie same. Zapytania DNS są przetwarzane w normalny sposób.

Aby uzyskać więcej informacji i instrukcje dotyczące przenoszenia stref DNS, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak długo trwa wprowadzanie zmian DNS?

Nowe strefy DNS i rekordy DNS zwykle pojawiają się na serwerach nazw Azure DNS szybko. Czas to kilka sekund.

Zmiany w istniejących rekordach DNS mogą trwać nieco dłużej. Zazwyczaj są one wyświetlane na serwerach nazw Azure DNS w ciągu 60 sekund. Buforowanie DNS przez klientów DNS i cykliczne resolvery DNS poza Azure DNS może mieć wpływ na chronometraż. Aby kontrolować ten czas trwania pamięci podręcznej, należy użyć właściwości czasu wygaśnięcia (TTL) każdego zestawu rekordów.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak chronić strefy DNS przed przypadkowym usunięciem?

Azure DNS jest zarządzany przy użyciu Azure Resource Manager. Azure DNS korzyści z funkcji kontroli dostępu zapewnianych przez Azure Resource Manager. Za pomocą kontroli dostępu opartej na rolach platformy Azure można kontrolować, którzy użytkownicy mają dostęp do odczytu lub zapisu do stref i zestawów rekordów DNS. Blokady zasobów uniemożliwiają przypadkowe modyfikację lub usunięcie stref DNS i zestawów rekordów.

Aby uzyskać więcej informacji, zobacz [Ochrona stref i rekordów DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak mogę skonfigurować rekordy SPF w Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Czy Azure DNS serwery nazw rozwiązują protokół IPv6? 

Tak. Serwery nazw Azure DNS są podwójnym stosem. Podwójny stos oznacza, że mają adresy IPv4 i IPv6. Aby znaleźć adres IPv6 Azure DNS serwerów nazw przypisanych do strefy DNS, użyj narzędzia takiego jak nslookup. Może to być na przykład `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Jak mogę skonfigurować IDN w Azure DNS?

Międzynarodowe nazwy domen (IDN) kodują każdą nazwę DNS przy użyciu [formacie Punycode](https://en.wikipedia.org/wiki/Punycode). Zapytania DNS są tworzone przy użyciu tych nazw zakodowanych formacie Punycode.

Aby skonfigurować IDN w Azure DNS, przekonwertuj nazwę strefy lub nazwę zestawu rekordów na formacie Punycode. Azure DNS nie obsługuje obecnie wbudowanej konwersji na lub z formacie Punycode.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o Azure DNS](dns-overview.md).

- [Dowiedz się więcej na temat używania Azure DNS w przypadku domen prywatnych](private-dns-overview.md).

- [Dowiedz się więcej o strefach i rekordach DNS](dns-zones-records.md).

- [Rozpocznij pracę z Azure DNS](dns-getstarted-portal.md).