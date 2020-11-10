---
title: Zapora platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące zapory platformy Azure. Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 8b94b71993285a61042be3c6cd9e4708315fab9f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413007"
---
# <a name="azure-firewall-faq"></a>Zapora platformy Azure — często zadawane pytania

## <a name="what-is-azure-firewall"></a>Co to jest usługa Azure Firewall?

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa Zapora jako usługa z wbudowaną wysoką dostępnością i nieograniczoną skalowalnością chmury. Możesz centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w subskrypcjach i sieciach wirtualnych.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Jakie możliwości są obsługiwane w zaporze platformy Azure?

Aby dowiedzieć się więcej o funkcjach zapory platformy Azure, zobacz [funkcje zapory platformy Azure](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Jaki jest typowy model wdrażania dla zapory platformy Azure?

Usługę Azure Firewall można wdrożyć w dowolnej sieci wirtualnej, ale klienci zazwyczaj wdrażają ją w centralnej sieci wirtualnej i łączą z nią równorzędnie inne sieci wirtualne w modelu piasty i szprych. Następnie można ustawić trasy domyślnej z równorzędnych sieci wirtualnych w taki sposób, aby wskazywały tę centralną sieć wirtualną zapory. Globalna komunikacja równorzędna sieci wirtualnej jest obsługiwana, ale nie jest zalecana ze względu na potencjalne problemy z wydajnością i opóźnieniami w różnych regionach. Aby uzyskać najlepszą wydajność, wdróż jedną zaporę na region.

Zaletą tego modelu jest możliwość scentralizowanej kontroli nad wieloma sieciami wirtualnymi szprychy w różnych subskrypcjach. Są również naliczane oszczędności, ponieważ nie trzeba oddzielnie wdrażać zapory w każdej sieci wirtualnej. Oszczędności kosztów należy mierzyć w porównaniu z kosztami powiązanej komunikacji równorzędnej na podstawie wzorców ruchu klienta.

## <a name="how-can-i-install-the-azure-firewall"></a>Jak zainstalować zaporę platformy Azure?

Zaporę platformy Azure można skonfigurować przy użyciu Azure Portal, programu PowerShell, interfejsu API REST lub przy użyciu szablonów. Zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal,](tutorial-firewall-deploy-portal.md) Aby uzyskać instrukcje krok po kroku.

## <a name="what-are-some-azure-firewall-concepts"></a>Jakie są pojęcia związane z zaporą platformy Azure?

Zapora platformy Azure obsługuje reguły i kolekcje reguł. Kolekcja reguł jest zestawem reguł, które mają takie samo zamówienie i priorytet. Kolekcje reguł są wykonywane w kolejności ich priorytetu. Kolekcje reguł sieciowych są wyższym priorytetem niż kolekcje reguł aplikacji i wszystkie reguły są przerywane.

Istnieją trzy typy kolekcji reguł:

* *Reguły aplikacji* : Skonfiguruj w pełni kwalifikowane nazwy domen (FQDN), do których można uzyskać dostęp z podsieci.
* *Reguły sieci* : Skonfiguruj reguły, które zawierają adresy źródłowe, protokoły, porty docelowe i adresy docelowe.
* *Reguły NAT* : Konfigurowanie reguł DNAT w celu zezwalania na przychodzące połączenia internetowe.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Czy Zapora platformy Azure obsługuje filtrowanie ruchu przychodzącego?

Zapora platformy Azure obsługuje filtrowanie przychodzące i wychodzące. Ochrona ruchu przychodzącego jest zwykle używana dla protokołów innych niż HTTP/S. Na przykład protokoły RDP, SSH i FTP. Aby zapewnić najlepszą ochronę ruchu przychodzącego HTTP/S, należy użyć zapory aplikacji sieci Web, takiej jak [Zapora aplikacji sieci Web platformy Azure (WAF)](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Które usługi rejestrowania i analizy są obsługiwane przez zaporę systemu Azure?

Zapora platformy Azure jest zintegrowana z Azure Monitor do przeglądania i analizowania dzienników zapory. Dzienniki mogą być wysyłane do Log Analytics, usługi Azure Storage lub Event Hubs. Można je analizować w Log Analytics lub przy użyciu różnych narzędzi, takich jak program Excel i Power BI. Aby uzyskać więcej informacji, zobacz [Samouczek: monitorowanie dzienników zapory platformy Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Jak Zapora platformy Azure różni się od istniejących usług, takich jak urządzeń WUS w portalu Marketplace?

Zapora systemu Azure to podstawowa usługa zapory, która może zająć się pewnymi scenariuszami klientów. Oczekujesz, że będziesz mieć kombinację urządzeń WUS innych firm i zapory platformy Azure. Lepsza współpraca jest priorytetem podstawowym.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Jaka jest różnica między Application Gateway WAF i zaporą platformy Azure?

Zapora aplikacji sieci Web (WAF) to funkcja Application Gateway, która zapewnia scentralizowaną ochronę ruchu przychodzącego aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. Zapora platformy Azure zapewnia ochronę ruchu przychodzącego dla protokołów innych niż HTTP/S (na przykład RDP, SSH, FTP), wychodzącej ochrony na poziomie sieci dla wszystkich portów i protokołów oraz ochrony na poziomie aplikacji dla wychodzącego protokołu HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Czym różnią się sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i Zapora platformy Azure?

Usługa Zapora platformy Azure uzupełnia funkcjonalność sieciowych grup zabezpieczeń. Wspólnie zapewniają lepsze zabezpieczenia sieci "Ochrona przed bardziej szczegółowymi". Sieciowe grupy zabezpieczeń zapewniają filtrowanie ruchu warstwy sieci rozproszonej, aby ograniczyć ruch do zasobów w ramach sieci wirtualnych w każdej subskrypcji. Zapora systemu Azure to w pełni stanowa, scentralizowana Zapora sieciowa jako usługa, która zapewnia ochronę na poziomie sieci i aplikacji w różnych subskrypcjach i sieciach wirtualnych.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Czy sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są obsługiwane przez AzureFirewallSubnet?

Zapora systemu Azure to zarządzana usługa z wieloma warstwami ochrony, obejmująca ochronę platformy przy użyciu sieciowych grup zabezpieczeń na poziomie karty sieciowej (niewidoczny).  Sieciowych grup zabezpieczeń poziomu podsieci nie są wymagane w AzureFirewallSubnet i są wyłączone w celu zapewnienia braku przerw w świadczeniu usług.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Jak mogę skonfigurować zaporę platformy Azure z punktami końcowymi usługi?

Aby zapewnić bezpieczny dostęp do usług PaaS Services, zalecamy korzystanie z punktów końcowych usługi. Możesz włączyć punkty końcowe usługi w podsieci zapory platformy Azure i wyłączyć je w sieci wirtualnej połączone szprych. Dzięki temu można korzystać z obu funkcji: zabezpieczeń punktu końcowego usługi i centralnego rejestrowania dla całego ruchu.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Jakie są ceny zapory systemu Azure?

Zobacz [Cennik usługi Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Jak zatrzymać i uruchomić zaporę platformy Azure?

Można użyć Azure PowerShell *alokacji* i *alokacji* metod.

Na przykład:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Musisz ponownie przydzielić zaporę i publiczny adres IP do oryginalnej grupy zasobów i subskrypcji.

## <a name="what-are-the-known-service-limits"></a>Jakie są znane limity usługi?

Aby uzyskać ograniczenia dotyczące usługi Zapora platformy Azure, zobacz [limity dotyczące subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Czy Zapora platformy Azure w sieci wirtualnej centrum przekazuje i filtruje ruch sieciowy między dwiema sieciami wirtualnymi szprych?

Tak, możesz użyć zapory platformy Azure w sieci wirtualnej Hub do kierowania i filtrowania ruchu między dwiema sieciami wirtualnymi szprych. Podsieci w każdej z sieci wirtualnych szprych muszą mieć UDR wskazujące na zaporę platformy Azure jako bramę domyślną, aby ten scenariusz działał poprawnie.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Czy Zapora platformy Azure przekazuje i filtruje ruch sieciowy między podsieciami w tej samej sieci wirtualnej lub równorzędnej sieci wirtualnej?

Tak. Jednak skonfigurowanie UDR do przekierowywania ruchu między podsieciami w tej samej wirtualnej wymaga dodatkowej uwagi. Użycie zakresu adresów sieci wirtualnej jako prefiksu docelowego dla UDR jest wystarczające, ale również kieruje cały ruch z jednej maszyny do innej maszyny w tej samej podsieci za pośrednictwem wystąpienia zapory platformy Azure. Aby tego uniknąć, należy uwzględnić trasę dla podsieci w UDR z typem następnego przeskoku sieci **wirtualnej**. Zarządzanie tymi trasami może być uciążliwe i podatne na błędy. Zalecaną metodą segmentacji sieci wewnętrznej jest użycie sieciowych grup zabezpieczeń, które nie wymagają UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Czy ruch wychodzący między sieciami prywatnymi jest zaporą platformy Azure?

Zapora platformy Azure nie ma protokołu IPSec, gdy docelowy adres IP jest prywatnym zakresem adresów IP na [organizację IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure SNATs ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Zaporę platformy Azure można skonfigurować w taki sposób, aby **nie** było możliwe przechodzenie do publicznego zakresu adresów IP. Aby uzyskać więcej informacji, zobacz [zakresy prywatnych adresów IP zapory systemu Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Czy jest wymuszane tunelowanie/łączenie do obsługiwanego sieciowego urządzenia wirtualnego?

Wymuszone tunelowanie jest obsługiwane podczas tworzenia nowej zapory. Nie można skonfigurować istniejącej zapory do wymuszonego tunelowania. Aby uzyskać więcej informacji, zobacz [tunelowanie wymuszone przez zaporę platformy Azure](forced-tunneling.md).

Usługa Azure Firewall musi mieć bezpośrednie połączenie z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić ten element przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** w celu utrzymania bezpośredniej łączności z Internetem.

Jeśli konfiguracja wymaga wymuszonego tunelowania do sieci lokalnej i można określić docelowe prefiksy adresów IP dla miejsc docelowych Internetu, można skonfigurować te zakresy przy użyciu sieci lokalnej jako następnego skoku za pośrednictwem trasy zdefiniowanej przez użytkownika w AzureFirewallSubnet. Lub można użyć protokołu BGP, aby zdefiniować te trasy.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Czy istnieją jakieś ograniczenia grupy zasobów zapory?

Tak. Wszystkie zapory, Sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej grupie zasobów.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Czy podczas konfigurowania DNAT dla ruchu przychodzącego sieci internetowego należy również skonfigurować odpowiednią regułę sieci, aby zezwolić na ten ruch?

Nie. Reguły translatora adresów sieciowych niejawnie Dodaj odpowiednią regułę sieci, aby zezwolić na ruch przetłumaczony. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Jak działają symbole wieloznaczne w nazwie FQDN docelowej reguły aplikacji?

Obecnie symboli wieloznacznych można używać tylko po lewej stronie nazwy FQDN. Na przykład * *_. contoso.com_* i * *_contoso.com_*.

Skonfigurowanie * *_. contoso.com_* umożliwia *anyvalue*. contoso.com, ale nie contoso.com (wierzchołk domeny). Jeśli chcesz zezwolić na wierzchołk domeny, musisz jawnie skonfigurować go jako docelową nazwę FQDN.

## <a name="what-does-provisioning-state-failed-mean"></a>Co to jest *stan aprowizacji: niepowodzenie* ?

Po każdym zastosowaniu zmiany konfiguracji usługa Azure Firewall próbuje zaktualizować wszystkie swoje bazowe wystąpienia zaplecza. W rzadkich przypadkach nie można zaktualizować jednego z tych wystąpień zaplecza przy użyciu nowej konfiguracji, a proces aktualizacji zostanie zatrzymany z nieprawidłowym stanem aprowizacji. Usługa Azure Firewall nadal działa, ale zastosowana konfiguracja może być w niespójnym stanie, w którym niektóre wystąpienia mają poprzednią konfigurację, a inne mają zaktualizowany zestaw reguł. W takim przypadku spróbuj zaktualizować konfigurację jeszcze raz, dopóki operacja się nie powiedzie, a Zapora *zakończyła się pomyślnie* .

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Jak Zapora platformy Azure obsługuje planowaną konserwację i nieplanowane błędy?
Zapora platformy Azure składa się z kilku węzłów zaplecza w konfiguracji aktywne-aktywne.  W przypadku każdej planowanej konserwacji mamy do bezpiecznego aktualizowania węzłów opróżnianie logiki.  Aktualizacje są planowane w godzinach poza godzinami pracy dla każdego regionu platformy Azure w celu dodatkowego ograniczenia ryzyka zakłócenia.  W przypadku nieplanowanych problemów tworzymy nowy węzeł, który zastąpi węzeł zakończony niepowodzeniem.  Połączenie z nowym węzłem jest zwykle ponownie nawiązane w ciągu 10 sekund od momentu awarii.

## <a name="how-does-connection-draining-work"></a>Jak działa opróżnianie połączenia?

W przypadku każdej planowanej konserwacji, logika opróżniania połączenia bezpiecznie aktualizuje węzły zaplecza. Zapora platformy Azure czeka 90 sekund na zamknięcie istniejących połączeń. W razie potrzeby klienci mogą automatycznie ponownie ustanowić łączność z innym węzłem zaplecza.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Czy istnieje limit znaków nazwy zapory?

Tak. Dla nazwy zapory istnieje limit znaków 50.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Dlaczego Zapora platformy Azure potrzebuje rozmiaru podsieci/26?

Zapora platformy Azure musi obsługiwać więcej wystąpień maszyn wirtualnych w miarę skalowania. Przestrzeń adresowa (/26) gwarantuje, że Zapora ma wystarczającą liczbę adresów IP, aby obsłużyć skalowanie.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Czy rozmiar podsieci zapory musi ulec zmianie w miarę skalowania usługi?

Nie. Zapora platformy Azure nie wymaga podsieci większej niż/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Jak zwiększyć przepływność zapory?

Początkowa przepustowość zapory platformy Azure to 2,5 – 3 GB/s i jest skalowana do 30 GB/s. Skaluje się automatycznie w zależności od użycia procesora CPU i przepływności.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Jak długo trwa skalowanie w poziomie zapory platformy Azure?

Usługa Azure firewall stopniowo skaluje się, gdy średnia przepustowość lub użycie procesora CPU wynosi 60%. Domyślna maksymalna przepływność wdrożenia wynosi około 2,5-3 GB/s i zaczyna skalowanie w poziomie, gdy osiągnie 60% tej liczby. Skalowanie w poziomie trwa od 5 do siedmiu minut. 

Podczas testowania wydajności upewnij się, że testujesz przez co najmniej 10 do 15 minut, i Rozpocznij nowe połączenia, aby korzystać z nowo utworzonych węzłów zapory.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Czy Zapora platformy Azure domyślnie zezwala na dostęp do Active Directory?

Nie. Zapora systemu Azure domyślnie blokuje dostęp Active Directory. Aby zezwolić na dostęp, skonfiguruj tag usługi usługi azureactivedirectory. Aby uzyskać więcej informacji, zobacz [Tagi usługi Zapora platformy Azure](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Czy można wykluczyć nazwę FQDN lub adres IP z filtrowania opartego na analizie zagrożeń zapory platformy Azure?

Tak, możesz użyć Azure PowerShell, aby to zrobić:

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Dlaczego funkcja ping protokołu TCP i podobne narzędzia pomyślnie nawiązują połączenie z docelową nazwą FQDN, nawet jeśli żadna Reguła zapory platformy Azure nie zezwala na ten ruch?

Polecenie ping protokołu TCP nie nawiązuje połączenia z docelową nazwą FQDN. Dzieje się tak, ponieważ przezroczysty serwer proxy zapory platformy Azure nasłuchuje na porcie 80/443 dla ruchu wychodzącego. Polecenie ping protokołu TCP nawiązuje połączenie z zaporą, która następnie odrzuca pakiet. Takie zachowanie nie ma żadnego wpływu na zabezpieczenia. Jednak aby uniknąć nieporozumień, badamy potencjalne zmiany w tym zachowaniu.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Czy istnieją limity liczby adresów IP obsługiwanych przez grupy adresów IP?

Tak. Aby uzyskać więcej informacji, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Czy mogę przenieść grupę adresów IP do innej grupy zasobów?

Nie, przeniesienie grupy adresów IP do innej grupy zasobów nie jest obecnie obsługiwane.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Jaki jest limit czasu bezczynności TCP dla zapory platformy Azure?

Standardowe zachowanie zapory sieciowej polega na zapewnieniu aktywności połączeń TCP i niezwłocznego ich zamknięcia w przypadku braku aktywności. Limit czasu bezczynności protokołu TCP zapory platformy Azure to cztery minuty. Tego ustawienia nie można skonfigurować. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana. Typowym zastosowaniem jest utrzymywanie aktywności protokołu TCP. Ta metoda utrzymuje, że połączenie jest aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [przykłady dla platformy .NET](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Czy mogę wdrożyć zaporę platformy Azure bez publicznego adresu IP?

Nie. obecnie należy wdrożyć zaporę platformy Azure z publicznym adresem IP.

## <a name="where-does-azure-firewall-store-customer-data"></a>Gdzie usługa Azure firewall ma przechowywać dane klienta?

Zapora platformy Azure nie przenosi ani nie zapisuje danych klienta z regionu, w którym został wdrożony.
