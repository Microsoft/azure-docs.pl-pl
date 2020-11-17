---
title: Funkcje usługi Azure Firewall
description: Dowiedz się więcej o funkcjach zapory platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
ms.openlocfilehash: 6c71d9325ff7c1c5d2e7eff03a587a28335efd72
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652148"
---
# <a name="azure-firewall-features"></a>Funkcje usługi Azure Firewall

[Zapora systemu Azure](overview.md) to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.

![Omówienie zapory](media/overview/firewall-threat.png)

Zapora platformy Azure obejmuje następujące funkcje:

- [Wbudowana wysoka dostępność](#built-in-high-availability)
- [Strefy dostępności](#availability-zones)
- [Nieograniczona skalowalność chmury](#unrestricted-cloud-scalability)
- [Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji](#application-fqdn-filtering-rules)
- [Reguły filtrowania ruchu sieciowego](#network-traffic-filtering-rules)
- [Tagi w pełni kwalifikowanych nazw domen](#fqdn-tags)
- [Tagi usługi](#service-tags)
- [Analiza zagrożeń](#threat-intelligence)
- [Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego](#outbound-snat-support)
- [Obsługa technologii DNAT dla ruchu przychodzącego](#inbound-dnat-support)
- [Wiele publicznych adresów IP](#multiple-public-ip-addresses)
- [Rejestrowanie Azure Monitor](#azure-monitor-logging)
- [Wymuszone tunelowanie](#forced-tunneling)
- [Certyfikaty](#certifications)

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowana, więc nie są wymagane żadne dodatkowe moduły równoważenia obciążenia i nie trzeba konfigurować żadnych usług.

## <a name="availability-zones"></a>Strefy dostępności

Zaporę platformy Azure można skonfigurować podczas wdrażania w celu rozdzielenia wielu Strefy dostępności w celu zwiększenia dostępności. W przypadku Strefy dostępności czas dostępności wzrosną do 99,99% czasu. Aby uzyskać więcej informacji, zobacz Umowa dotycząca poziomu usług zapory platformy Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Umowa SLA na czas działania na 99,99% jest oferowana w przypadku wybrania co najmniej dwóch Strefy dostępności.

Można także skojarzyć zaporę platformy Azure z określoną strefą tylko z przyczyn bliskości przy użyciu standardowej umowy SLA usługi 99,95%.

Dla zapory wdrożonej w strefie dostępności nie ma dodatkowych kosztów. Istnieją jednak dodatkowe koszty dla przychodzących i wychodzących transferów danych skojarzonych z Strefy dostępności. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

Strefy dostępności zapory platformy Azure są dostępne w regionach, które obsługują Strefy dostępności. Aby uzyskać więcej informacji, zobacz [regiony obsługujące strefy dostępności na platformie Azure](../availability-zones/az-region.md)

> [!NOTE]
> Strefy dostępności można skonfigurować tylko podczas wdrażania. Nie można skonfigurować istniejącej zapory do dołączania Strefy dostępności.

Aby uzyskać więcej informacji na temat Strefy dostępności, zobacz [regiony i strefy dostępności na platformie Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Skalowalność w chmurze bez ograniczeń

Usługa Azure Firewall umożliwia skalowanie bez ograniczeń, odpowiednio do zmieniających się przepływów ruchu sieciowego — nie trzeba więc dostosowywać budżetu do okresów szczytowego ruchu.

## <a name="application-fqdn-filtering-rules"></a>Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji

Można ograniczyć ruch wychodzący HTTP/S lub ruch usługi Azure SQL do określonej listy w pełni kwalifikowanych nazw domen (FQDN), w tym symboli wieloznacznych. Ta funkcja nie wymaga zakończenia protokołu TLS.

## <a name="network-traffic-filtering-rules"></a>Reguły filtrowania ruchu sieciowego

Można centralnie *utworzyć reguły* *filtrowania sieci dla* źródła i docelowego adresu IP, portu i protokołu. Usługa Azure Firewall jest w pełni stanowa, więc możesz rozróżniać autentyczne pakiety w ramach różnych typów połączeń. Reguły są wymuszane i rejestrowane w wielu subskrypcjach i sieciach wirtualnych.

## <a name="fqdn-tags"></a>Tagi w pełni kwalifikowanych nazw domen

[Tagi FQDN](fqdn-tags.md) ułatwiają zapewnienie dobrze znanego ruchu sieciowego usługi platformy Azure przez zaporę. Załóżmy na przykład, że chcesz zezwolić na ruch sieciowy z witryny Windows Update przez zaporę. Tworzysz regułę aplikacji i dołączasz tag „Windows Update”. Teraz ruch sieciowy z witryny Windows Update może przechodzić przez zaporę.

## <a name="service-tags"></a>Tagi usługi

[Tag usługi](service-tags.md) reprezentuje grupę prefiksów adresów IP, która pomaga zminimalizować złożoność tworzenia reguł zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić, które adresy IP znajdują się w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

## <a name="threat-intelligence"></a>Analiza zagrożeń

Filtrowanie na podstawie [analizy zagrożeń](threat-intel.md)może być włączone, aby zapora mogła zgłaszać i odrzucać ruch z/do znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft.

## <a name="outbound-snat-support"></a>Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego

Wszystkie adresy IP wychodzącego ruchu sieciowego są tłumaczone na publiczny adres IP usługi Azure Firewall (translacja adresów sieciowych źródła, SNAT). Możesz zidentyfikować ruch pochodzący z sieci wirtualnej do zdalnych internetowych miejsc docelowych i zezwalać na niego. Zapora platformy Azure nie ma protokołu IPSec, gdy docelowy adres IP jest prywatnym zakresem adresów IP na [organizację IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Jeśli Twoja organizacja używa zakresu publicznego adresu IP dla sieci prywatnych, Zapora platformy Azure przywróci ruch do jednego z prywatnych adresów IP zapory w AzureFirewallSubnet. Zaporę platformy Azure można skonfigurować w taki sposób, aby **nie** było możliwe przechodzenie do publicznego zakresu adresów IP. Aby uzyskać więcej informacji, zobacz [zakresy prywatnych adresów IP zapory systemu Azure](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Obsługa technologii DNAT dla ruchu przychodzącego

Ruch przychodzący w sieci Internet do publicznego adresu IP zapory jest tłumaczony (translacja adresów sieciowych) i filtrowany na prywatne adresy IP w sieciach wirtualnych.

## <a name="multiple-public-ip-addresses"></a>Wiele publicznych adresów IP

Za pomocą zapory można skojarzyć [wiele publicznych adresów IP](deploy-multi-public-ip-powershell.md) (do 250).

Dzięki temu można wykonać następujące scenariusze:

- **DNAT** — wiele standardowych wystąpień portów można przetłumaczyć na serwery zaplecza. Jeśli na przykład masz dwa publiczne adresy IP, możesz wykonać translację portu TCP 3389 (RDP) dla obu adresów IP.
- Reportcy **adresów sieciowych** — dodatkowe porty są dostępne dla wychodzących połączeń z reportem adresów sieciowych, co zmniejsza prawdopodobieństwo wyczerpania portów dla tego elementu. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP, który ma być używany w połączeniu. Jeśli w sieci występuje jakiekolwiek filtrowanie w kierunku do klienta, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą. Rozważ użycie [prefiksu publicznego adresu IP](../virtual-network/public-ip-address-prefix.md) , aby uprościć tę konfigurację.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

Wszystkie zdarzenia są zintegrowane z usługą Azure Monitor, co umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum Event Hub lub wysyłanie ich do dzienników usługi Azure Monitor. Przykłady dzienników Azure Monitor można znaleźć w temacie [Azure monitor Logs dla zapory platformy Azure](log-analytics-samples.md).

Aby uzyskać więcej informacji, zobacz [Samouczek: monitorowanie dzienników i metryk zapory platformy Azure](./firewall-diagnostics.md). 

Skoroszyt zapory platformy Azure zapewnia elastyczną kanwę do analizy danych w zaporze platformy Azure. Można go użyć do tworzenia rozbudowanych raportów wizualnych w Azure Portal. Aby uzyskać więcej informacji, zobacz [monitorowanie dzienników przy użyciu skoroszytu zapory platformy Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Wymuszone tunelowanie

Zaporę platformy Azure można skonfigurować tak, aby rozsyłać cały ruch związany z Internetem do określonego następnego przeskoku zamiast bezpośrednio do Internetu. Na przykład lokalna Zapora brzegowa lub inne wirtualne urządzenie sieciowe (urządzenie WUS) mogą przetwarzać ruch sieciowy przed przekazaniem go do Internetu. Aby uzyskać więcej informacji, zobacz [tunelowanie wymuszone przez zaporę platformy Azure](forced-tunneling.md).

## <a name="certifications"></a>Certyfikaty

Zapora platformy Azure to branżowa karta płatnicza (PCI), kontrolki organizacji usług (SOC), Międzynarodowa Organizacja Normalizacyjna (ISO) i ICSA Labs. Aby uzyskać więcej informacji, zobacz [certyfikaty zgodności zapory platformy Azure](compliance-certifications.md).

## <a name="next-steps"></a>Następne kroki

- [Logika przetwarzania reguł usługi Azure Firewall](rule-processing.md)