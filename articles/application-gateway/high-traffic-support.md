---
title: Application Gateway obsługa dużej ilości ruchu sieciowego
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania Application Gateway platformy Azure w ramach obsługi dużych scenariuszy ilości ruchu sieciowego.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250252"
---
# <a name="application-gateway-high-traffic-support"></a>Obsługa dużego natężenia ruchu usługi Application Gateway

>[!NOTE]
> W tym artykule opisano kilka zalecanych wytycznych, które ułatwiają konfigurowanie Application Gateway do obsługi dodatkowego ruchu z powodu dużej ilości ruchu sieciowego, który może wystąpić z powodu kryzysu COVID-19.

Za pomocą Application Gateway za pomocą zapory aplikacji sieci Web (WAF) można skalowalne i bezpieczne zarządzanie ruchem do aplikacji sieci Web.

Poniższe sugestie pomogą Ci w konfigurowaniu Application Gateway przy użyciu WAF do obsługi dodatkowego ruchu sieciowego.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Korzystanie z jednostki SKU w wersji 2 w wersji 1 na potrzeby możliwości skalowania automatycznego i korzyści z wydajności
Jednostka SKU v2 oferuje Skalowanie automatyczne, aby zapewnić, że Application Gateway można skalować w górę w miarę wzrostu ruchu. Oferuje również inne korzyści wynikające z wydajności, takie jak pięciokrotną lepsza wydajność odciążania TLS, szybszy czas wdrażania i aktualizacji, nadmiarowość stref i wiele więcej w porównaniu do wersji 1. Aby uzyskać więcej informacji, zobacz nasze [dokumenty w wersji 2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ustaw maksymalną dozwoloną liczbę wystąpień (125)
 
Przy założeniu, że masz jednostkę SKU Application Gateway v2, ustawienie maksymalnej liczby wystąpień na maksymalną możliwą wartość 125 pozwala na skalowanie w poziomie Application Gateway w razie potrzeby. Dzięki temu można obsługiwać możliwy wzrost ruchu do aplikacji. Opłata zostanie naliczona tylko za używane jednostki pojemności.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Ustawianie minimalnej liczby wystąpień na podstawie średniego użycia CU

Przy założeniu, że masz jednostkę SKU Application Gateway v2, Skalowanie automatyczne trwa od sześciu do siedmiu minut. Przy większej liczbie minimalnych wystąpień Application Gateway może lepiej obsłużyć ruch w przypadku zwiększenia obciążenia, ponieważ skok ruchu nie wymaga operacji skalowania automatycznego.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Zgłoś alert, jeśli określona Metryka przekracza 75% średniej wartości użycia. 
Szczegółowe wyjaśnienie naszych metryk i innych przewodników można znaleźć w [dokumentacji metryk Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) . 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Przykład: Konfigurowanie alertu na 75% średniego użycia CU

W tym przykładzie pokazano, jak za pomocą Azure Portal skonfigurować alert w przypadku osiągnięcia 75% średniej wartości użycia. 
1. Przejdź do **Application Gateway**.
2. Na panelu po lewej stronie wybierz pozycję **metryki** na karcie **monitorowanie** . 
3. Dodaj metrykę dla **średniej bieżącej jednostki obliczeniowej**. 
![Konfigurowanie metryki WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Jeśli określono minimalną liczbę wystąpień jako średniego użycia CU, przejdź dalej i Ustaw Alert, gdy zostanie użytych 75% minimalnej liczby wystąpień. Na przykład jeśli średnie użycie wynosi 10 jednostek, Ustaw Alert na 7,5. Spowoduje to wygenerowanie alertów, jeśli użycie zwiększy się i przekroczy czas odpowiedzi. Możesz podnieść wartość minimalną, jeśli uważasz, że ten ruch będzie w stanie wzrosnąć. 
![Konfigurowanie alertu WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Można ustawić alert, który ma być wykonywany przy niższej lub wyższej wartości procentowej użycia, w zależności od tego, jak poufne jest prawdopodobieństwo potencjalnego obciążenia.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Konfigurowanie WAF z użyciem geofiltrów i ochrony bot w celu zatrzymywania ataków
Jeśli potrzebujesz dodatkowej warstwy zabezpieczeń przed aplikacją, użyj Application Gateway jednostki SKU WAF_v2 do obsługi funkcji WAF. Jednostkę SKU v2 można skonfigurować tak, aby zezwalała na dostęp tylko do aplikacji z danego kraju/regionu lub krajów/regionów. Można skonfigurować regułę niestandardową WAF, aby jawnie zezwalać lub blokować ruch na podstawie geolokalizacji. Aby uzyskać więcej informacji, zobacz Opis [geofiltrowaniu reguł niestandardowych](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) i [sposób konfigurowania reguł niestandardowych w Application Gateway WAF_v2 jednostki SKU przy użyciu programu PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Włącz ochronę bot, aby blokować znane złe botów. Powinno to zmniejszyć ilość ruchu przychodzącego do aplikacji. Aby uzyskać więcej informacji, zobacz [bot Protection with Set up](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Włącz diagnostykę na Application Gateway i WAF

Dzienniki diagnostyczne umożliwiają wyświetlanie dzienników zapory, dzienników wydajności i dzienników dostępu. Za pomocą tych dzienników na platformie Azure można zarządzać bramami aplikacji i rozwiązywać problemy. Aby uzyskać więcej informacji, zobacz naszą [dokumentację diagnostyki](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurowanie zasad protokołu TLS dla dodatkowych zabezpieczeń
Upewnij się, że używasz najnowszej wersji zasad protokołu TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Wymusza to użycie protokołu TLS 1,2 i silniejszych szyfrów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wersji zasad protokołu TLS i mechanizmów szyfrowania za pomocą programu PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
