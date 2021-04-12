---
title: Bezpieczny ruch kierowany do prywatnych punktów końcowych w wirtualnej sieci WAN platformy Azure
description: Dowiedz się, jak używać reguł sieci i reguł aplikacji w celu zabezpieczania ruchu kierowanego do prywatnych punktów końcowych w usłudze Azure Virtual Network
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259357"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Bezpieczny ruch kierowany do prywatnych punktów końcowych w wirtualnej sieci WAN platformy Azure

> [!NOTE]
> Ten artykuł ma zastosowanie tylko do zabezpieczonego koncentratora wirtualnego. Aby przeprowadzić inspekcję ruchu przeznaczonego dla prywatnych punktów końcowych przy użyciu zapory platformy Azure w sieci wirtualnej centrum, zobacz [Używanie zapory platformy Azure do sprawdzania ruchu kierowanego do prywatnego punktu końcowego](../private-link/inspect-traffic-with-azure-firewall.md).

[Prywatny punkt końcowy platformy Azure](../private-link/private-endpoint-overview.md) to podstawowy blok konstrukcyjny dla [prywatnego linku platformy Azure](../private-link/private-link-overview.md). Prywatne punkty końcowe umożliwiają zasobom platformy Azure wdrożonym w sieci wirtualnej komunikowanie się z prywatnymi zasobami linków.

Prywatne punkty końcowe umożliwiają dostęp do zasobów do usługi link prywatny wdrożonej w sieci wirtualnej. Dostęp do prywatnego punktu końcowego za pośrednictwem komunikacji równorzędnej sieci wirtualnej i lokalnych połączeń sieciowych zwiększają łączność.

Może być konieczne przefiltrowanie ruchu klientów lokalnie lub na platformie Azure przeznaczonych dla usług udostępnianych za pośrednictwem prywatnych punktów końcowych w sieci wirtualnej połączonej z siecią WAN. Ten artykuł przeprowadzi Cię przez to zadanie przy użyciu [zabezpieczonego koncentratora wirtualnego](../firewall-manager/secured-virtual-hub.md) z [zaporą platformy Azure](../firewall/overview.md) jako dostawcy zabezpieczeń.

Zapora platformy Azure filtruje ruch przy użyciu dowolnej z następujących metod:

* [Nazwa FQDN w regułach sieciowych](../firewall/fqdn-filtering-network-rules.md) dla protokołów TCP i UDP
* [Nazwa FQDN w regułach aplikacji](../firewall/features.md#application-fqdn-filtering-rules) dla protokołów HTTP, https i MSSQL.
* Źródłowe i docelowe adresy IP, porty i protokoły przy użyciu [reguł sieci](../firewall/features.md#network-traffic-filtering-rules)

Użyj reguł aplikacji za pośrednictwem reguł sieci, aby sprawdzić ruch przeznaczony dla prywatnych punktów końcowych.
Bezpiecznym koncentratorem wirtualnym zarządza firma Microsoft i nie można go połączyć ze [strefą prywatna strefa DNS](../dns/private-dns-privatednszone.md). Jest to wymagane, aby rozpoznać nazwę FQDN [zasobu linku prywatnego](../private-link/private-endpoint-overview.md#private-link-resource) do odpowiedniego prywatnego adresu IP punktu końcowego.

Filtrowanie nazwy FQDN SQL jest obsługiwane tylko w [trybie proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Tryb *serwera proxy* może powodować większe opóźnienia w porównaniu do *przekierowania*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, który jest domyślnym ustawieniem dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu nazwy FQDN w regułach sieci zapory.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrowanie ruchu przy użyciu nazwy FQDN w regułach sieci i aplikacji

Poniższe kroki umożliwiają zaporze platformy Azure Filtrowanie ruchu przy użyciu nazwy FQDN w regułach sieci i aplikacji:

1. Wdróż maszynę wirtualną usługi [przesyłania dalej DNS](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) w sieci wirtualnej połączonej z bezpiecznym koncentratorem wirtualnym i połączoną z prywatna strefa DNS strefami obsługującymi typy rekordów dla prywatnych punktów końcowych.

2. Skonfiguruj [niestandardowe ustawienia DNS](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) , aby wskazywały adres IP maszyny wirtualnej usługi przesyłania dalej DNS i włączyć serwer proxy DNS w zasadach zapory skojarzonych z zaporą platformy Azure wdrożoną w bezpiecznym koncentratorze wirtualnym.

3. Skonfiguruj [niestandardowe serwery DNS](../virtual-network/manage-virtual-network.md#change-dns-servers) dla sieci wirtualnych podłączonych do zabezpieczonego koncentratora wirtualnego w taki sposób, aby wskazywały prywatny adres IP skojarzony z zaporą platformy Azure wdrożoną w bezpiecznym koncentratorze wirtualnym.

4. Skonfiguruj lokalne serwery DNS, aby przekazywać zapytania DNS dotyczące prywatnych punktów końcowych publicznych stref DNS do prywatnego adresu IP skojarzonego z zaporą platformy Azure wdrożoną w bezpiecznym koncentratorze wirtualnym.

5. Skonfiguruj [regułę aplikacji](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) lub [regułę sieci](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) w zależności od potrzeb w zasadach zapory skojarzonych z zaporą platformy Azure wdrożoną w zabezpieczonym centrum wirtualnym z nazwą FQDN *typu docelowego* i publicznym prywatnym nazwą FQDN zasobu linku prywatnego jako *miejsce docelowe*.

6. Przejdź do *bezpiecznych koncentratorów wirtualnych* w zasadach zapory skojarzonych z zaporą platformy Azure wdrożonej w zabezpieczonym centrum wirtualnym i wybierz zabezpieczone centrum wirtualne, w którym zostaną skonfigurowane Filtrowanie ruchu przeznaczone dla prywatnych punktów końcowych.

7. Przejdź do pozycji **Konfiguracja zabezpieczeń**, a następnie wybierz pozycję **Wyślij za pośrednictwem zapory platformy Azure** w obszarze **ruch prywatny**.

8. Wybierz pozycję **prywatne prefiksy ruchu** , aby edytować prefiksy CIDR, które będą sprawdzane za pośrednictwem zapory platformy Azure w zabezpieczonym koncentratorze wirtualnym, i Dodaj jeden prefiks/32 dla każdego prywatnego punktu końcowego w następujący sposób:

   > [!IMPORTANT]
   > Jeśli prefiksy/32 nie są skonfigurowane, ruch kierowany do prywatnych punktów końcowych spowoduje pominięcie zapory platformy Azure.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Konfiguracja zabezpieczeń Menedżera zapory" border="true":::

Te czynności działają tylko wtedy, gdy klienci i prywatne punkty końcowe są wdrażane w różnych sieciach wirtualnych podłączonych do tego samego zabezpieczonego koncentratora wirtualnego i dla lokalnych klientów. Jeśli klienci i prywatne punkty końcowe są wdrożone w tej samej sieci wirtualnej, muszą zostać utworzone UDR z/32 tras dla prywatnych punktów końcowych. Skonfiguruj te trasy z ustawionym **typem następnego przeskoku**  na **urządzenie wirtualne** i **adres następnego skoku** ustawione na prywatny adres IP zapory platformy Azure wdrożonej w bezpiecznym koncentratorze wirtualnym. **Propaguj trasy bramy** muszą mieć ustawioną **wartość tak**.

Na poniższym diagramie przedstawiono przepływy ruchu DNS i danych dla różnych klientów w celu połączenia z prywatnym punktem końcowym wdrożonym w wirtualnej sieci WAN platformy Azure:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Przepływy ruchu" border="true":::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Główne problemy, które mogą wystąpić podczas próby filtrowania ruchu przeznaczonego dla prywatnych punktów końcowych za pośrednictwem zabezpieczonego koncentratora wirtualnego:

- Klienci nie mogą połączyć się z prywatnymi punktami końcowymi.

- Zapora platformy Azure jest pomijana. Ten objaw może być zweryfikowany przez brak wpisów dziennika reguł sieci lub aplikacji w zaporze platformy Azure.

W większości przypadków te problemy są spowodowane przez jeden z następujących problemów:

- Nieprawidłowe rozpoznawanie nazw DNS

- Niepoprawna konfiguracja routingu

### <a name="incorrect-dns-name-resolution"></a>Nieprawidłowe rozpoznawanie nazw DNS

1. Sprawdź, czy serwery DNS sieci wirtualnej są ustawione na *niestandardowe* , a adres IP to prywatny adres IP zapory platformy Azure w zabezpieczonym koncentratorze wirtualnym.

   Interfejs wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Sprawdź, czy klienci w tej samej sieci wirtualnej, co maszyna wirtualna usług przesyłania dalej DNS, może rozpoznać publiczną nazwę FQDN prywatnego punktu końcowego na odpowiadającym mu prywatnym adresie IP, bezpośrednio badając maszynę wirtualną skonfigurowaną jako usługę przesyłania dalej DNS.

   W systemie Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Inspekcja wpisów dziennika zapory platformy Azure *AzureFirewallDNSProxy* i sprawdzanie poprawności może odbierać i rozwiązywać zapytania DNS od klientów.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Sprawdź, czy *serwer proxy usługi DNS* został włączony i czy *niestandardowy* serwer DNS wskazujący adres IP maszyny wirtualnej usługi przesyłania dalej DNS został skonfigurowany w zasadach zapory skojarzonych z zaporą platformy Azure w zabezpieczonym koncentratorze wirtualnym.

   Interfejs wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Niepoprawna konfiguracja routingu

1. Sprawdź *konfigurację zabezpieczeń* w zasadach zapory skojarzonych z zaporą platformy Azure wdrożoną w bezpiecznym koncentratorze wirtualnym. Upewnij się, że w kolumnie **ruch prywatny** jest wyświetlana wartość **zabezpieczony przez zaporę platformy Azure** dla wszystkich połączeń sieci wirtualnej i gałęzi, dla których chcesz filtrować ruch.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Ruch prywatny zabezpieczony przez zaporę platformy Azure" border="true":::

2. Sprawdź **konfigurację zabezpieczeń** w zasadach zapory skojarzonych z zaporą platformy Azure wdrożoną w bezpiecznym koncentratorze wirtualnym. Upewnij się, że istnieje wpis/32 dla każdego prywatnego adresu IP prywatnego punktu końcowego, dla którego chcesz filtrować ruch dla **prywatnych prefiksów ruchu**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Konfiguracja zabezpieczeń Menedżera zapory — prefiksy ruchu prywatnego" border="true":::

3. W bezpiecznym koncentratorze wirtualnym w obszarze wirtualna sieć WAN Sprawdź efektywne trasy dla tabel tras skojarzonych z sieciami wirtualnymi i gałęziami, dla których chcesz filtrować ruch. Upewnij się, 32 że istnieją wpisy dla każdego prywatnego prywatnego adresu IP, dla których chcesz filtrować ruch.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Efektywne trasy z bezpiecznym koncentratorem wirtualnym" border="true":::

4. Sprawdź obowiązujące trasy na kartach sieciowych dołączonych do maszyn wirtualnych wdrożonych w sieciach wirtualnych, dla których chcesz filtrować ruch. Upewnij się, 32 że istnieją wpisy dla każdego prywatnego prywatnego adresu IP, dla których chcesz filtrować ruch.
 
   Interfejs wiersza polecenia platformy Azure:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Sprawdź tabele routingu lokalnych urządzeń routingu. Upewnij się, że znasz przestrzenie adresowe sieci wirtualnych, w których są wdrożone prywatne punkty końcowe.

   Wirtualna sieć WAN platformy Azure nie anonsuje prefiksów skonfigurowanych w ramach **prywatnych prefiksów ruchu** w **konfiguracji zabezpieczeń** zasad zapory do lokalnego. Oczekuje się, że wpisy/32 nie będą wyświetlane w tabelach routingu lokalnych urządzeń routingu.

6. Sprawdź dzienniki zapory platformy Azure **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule** . Upewnij się, że ruch kierowany do prywatnych punktów końcowych jest rejestrowany.

   Wpisy dziennika **AzureFirewallNetworkRule** nie zawierają informacji o nazwie FQDN. Filtruj według adresu IP i portu podczas sprawdzania reguł sieci.

   Podczas filtrowania ruchu przeznaczonego dla [Azure Files](../storage/files/storage-files-introduction.md) prywatnych punktów końcowych wpisy dziennika **AzureFirewallNetworkRule** będą generowane tylko wtedy, gdy klient zainstaluje lub nawiąże połączenie z udziałem plików. Zapora platformy Azure nie będzie generować dzienników dla operacji [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) dla plików w udziale plików. Wynika to z faktu, że operacje CRUD są wykonywane za pośrednictwem stałego kanału TCP otwartego, gdy klient łączy się lub instaluje w udziale plików.

   Przykład zapytania dziennika reguły aplikacji:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Następne kroki

- [Używanie zapory platformy Azure do inspekcji ruchu kierowanego do prywatnego punktu końcowego](../private-link/inspect-traffic-with-azure-firewall.md)
