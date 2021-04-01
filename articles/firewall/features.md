---
title: Funkcje usługi Azure Firewall
description: Dowiedz się więcej o funkcjach zapory platformy Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: victorh
ms.openlocfilehash: 21bb1856409b7fbea1eeffb8b3769dd63119da50
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612851"
---
# <a name="azure-firewall-features"></a>Funkcje usługi Azure Firewall

[Zapora systemu Azure](overview.md) to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.

![Omówienie zapory](media/overview/firewall-threat.png)

Zapora platformy Azure obejmuje następujące funkcje:

- Wbudowana wysoka dostępność
- Strefy dostępności
- Skalowalność w chmurze bez ograniczeń
- Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji
- Reguły filtrowania ruchu sieciowego
- Tagi w pełni kwalifikowanych nazw domen
- Tagi usługi
- Analiza zagrożeń
- Obsługa translacji adresów sieciowych źródła (SNAT) dla ruchu wychodzącego
- Obsługa technologii DNAT dla ruchu przychodzącego
- Wiele publicznych adresów IP
- Rejestrowanie w usłudze Azure Monitor
- Wymuszone tunelowanie
- Kategorie sieci Web (wersja zapoznawcza)
- Certyfikaty

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność

Wysoka dostępność jest wbudowana, więc nie są wymagane żadne dodatkowe moduły równoważenia obciążenia i nie trzeba już konfigurować.

## <a name="availability-zones"></a>Strefy dostępności

Zaporę platformy Azure można skonfigurować podczas wdrażania w celu rozdzielenia wielu Strefy dostępności w celu zwiększenia dostępności. W przypadku Strefy dostępności czas dostępności wzrosną do 99,99% czasu. Aby uzyskać więcej informacji, zobacz Umowa dotycząca poziomu usług zapory platformy Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Umowa SLA na czas działania na 99,99% jest oferowana w przypadku wybrania co najmniej dwóch Strefy dostępności.

Można także skojarzyć zaporę platformy Azure z określoną strefą tylko z przyczyn bliskości przy użyciu standardowej umowy SLA usługi 99,95%.

Dla zapory wdrożonej w strefie dostępności nie ma dodatkowych kosztów. Istnieją jednak koszty dla przychodzących i wychodzących transferów danych skojarzonych z Strefy dostępności. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

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
- Dla wychodzących **połączeń z reportem równorzędnym** są dostępne dodatkowe porty, co zmniejsza prawdopodobieństwo wyczerpania portów przez przystawkę. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP, który ma być używany w połączeniu. Jeśli w sieci występuje jakiekolwiek filtrowanie w kierunku do klienta, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą. Rozważ użycie [prefiksu publicznego adresu IP](../virtual-network/public-ip-address-prefix.md) , aby uprościć tę konfigurację.

## <a name="azure-monitor-logging"></a>Rejestrowanie w usłudze Azure Monitor

Wszystkie zdarzenia są zintegrowane z usługą Azure Monitor, co umożliwia archiwizowanie dzienników na koncie magazynu, przesyłanie strumieniowe zdarzeń do centrum Event Hub lub wysyłanie ich do dzienników usługi Azure Monitor. Przykłady dzienników Azure Monitor można znaleźć w temacie [Azure monitor Logs dla zapory platformy Azure](./firewall-workbook.md).

Aby uzyskać więcej informacji, zobacz [Samouczek: monitorowanie dzienników i metryk zapory platformy Azure](./firewall-diagnostics.md). 

Skoroszyt zapory platformy Azure zapewnia elastyczną kanwę do analizy danych w zaporze platformy Azure. Można go użyć do tworzenia rozbudowanych raportów wizualnych w Azure Portal. Aby uzyskać więcej informacji, zobacz [monitorowanie dzienników przy użyciu skoroszytu zapory platformy Azure](firewall-workbook.md).

## <a name="forced-tunneling"></a>Wymuszone tunelowanie

Zaporę platformy Azure można skonfigurować tak, aby rozsyłać cały ruch związany z Internetem do określonego następnego przeskoku zamiast bezpośrednio do Internetu. Na przykład lokalna Zapora brzegowa lub inne wirtualne urządzenie sieciowe (urządzenie WUS) mogą przetwarzać ruch sieciowy przed przekazaniem go do Internetu. Aby uzyskać więcej informacji, zobacz [tunelowanie wymuszone przez zaporę platformy Azure](forced-tunneling.md).

## <a name="web-categories-preview"></a>Kategorie sieci Web (wersja zapoznawcza)

Kategorie sieci Web umożliwiają administratorom Zezwalanie na dostęp użytkowników do kategorii witryn sieci Web, takich jak witryny typu hazard, witryny internetowe mediów społecznościowych i inne osoby. Kategorie sieci Web są zawarte w standardzie zapory platformy Azure, ale bardziej dostrojone w wersji zapoznawczej usługi Azure firewall Premium. W przeciwieństwie do kategorii sieci Web w standardowej jednostce SKU, która jest zgodna z kategorią opartą na nazwie FQDN, jednostka SKU Premium dopasowuje kategorię zgodnie z całym adresem URL dla ruchu HTTP i HTTPS. Aby uzyskać więcej informacji na temat usługi Azure firewall Premium w wersji zapoznawczej, zobacz [funkcje usługi Azure firewall Premium Preview](premium-features.md).

Na przykład jeśli Zapora platformy Azure przechwytuje żądanie HTTPS dla `www.google.com/news` , oczekiwana jest następująca Kategoryzacja: 

- Zapora standardowa — zostanie zbadana tylko część nazwy FQDN, więc `www.google.com` zostanie ona skategoryzowana jako *aparat wyszukiwania*. 

- Zapora Premium — zostanie sprawdzony pełny adres URL, więc `www.google.com/news` zostanie on skategoryzowany jako *wiadomości*.

Kategorie są zorganizowane na podstawie ważności **odpowiedzialności**, **dużej przepustowości**, **użycia w firmie**, **utraty produktywności**, **ogólnego żeglowania** i bez **kategorii**.

### <a name="categorization-change"></a>Zmiana kategoryzacji

Możesz zażądać zmiany kategoryzacji, jeśli:

 - należy wziąć pod uwagę, że nazwa FQDN lub adres URL powinny znajdować się w innej kategorii 
 
lub 

- masz sugerowaną kategorię dla nazwy FQDN lub adresu URL bez kategorii

Witamy w przesłaniu żądania o [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .

### <a name="category-exceptions"></a>Wyjątki kategorii

Można utworzyć wyjątki dla reguł kategorii sieci Web. Utwórz oddzielną kolekcję reguł Zezwól lub Odmów z wyższym priorytetem w grupie kolekcji reguł. Można na przykład skonfigurować kolekcję reguł, która umożliwia korzystanie `www.linkedin.com` z priorytetu 100 z kolekcją reguł, która odmówi **sieci społecznościowych** z priorytetem 200. Spowoduje to utworzenie wyjątku dla wstępnie zdefiniowanej kategorii sieci Web **Sieć społecznościowa** .



## <a name="certifications"></a>Certyfikaty

Zapora platformy Azure to branżowa karta płatnicza (PCI), kontrolki organizacji usług (SOC), Międzynarodowa Organizacja Normalizacyjna (ISO) (ISO) i ICSA Labs. Aby uzyskać więcej informacji, zobacz [certyfikaty zgodności zapory platformy Azure](compliance-certifications.md).

## <a name="next-steps"></a>Następne kroki

- [Funkcje usługi Azure firewall Premium w wersji zapoznawczej](premium-features.md)