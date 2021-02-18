---
title: Application Gateway obsługa dużej ilości ruchu sieciowego
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania Application Gateway platformy Azure w ramach obsługi dużych scenariuszy ilości ruchu sieciowego.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586117"
---
# <a name="application-gateway-high-traffic-support"></a>Obsługa dużego natężenia ruchu usługi Application Gateway

>[!NOTE]
> W tym artykule opisano kilka sugerowanych wytycznych, które ułatwiają konfigurowanie Application Gateway do obsługi dodatkowego ruchu sieciowego w przypadku wystąpienia dużej ilości ruchu. Progi alertów są wyłącznie sugestiami i ogólnym charakterem. Użytkownicy mogą określić progi alertów na podstawie obciążenia i oczekiwań użycia.

Za pomocą Application Gateway za pomocą zapory aplikacji sieci Web (WAF) można skalowalne i bezpieczne zarządzanie ruchem do aplikacji sieci Web.

Ważne jest, aby skalować Application Gateway zgodnie z ruchem i z bitowym buforem, dzięki czemu można przystąpić do dowolnych lub większej ilości ruchu i zminimalizować wpływ, jaki może on znajdować się w funkcji QoS. Poniższe sugestie pomogą Ci w konfigurowaniu Application Gateway przy użyciu WAF do obsługi dodatkowego ruchu sieciowego.

Zapoznaj się z [dokumentacją metryk](./application-gateway-metrics.md) , aby zapoznać się z pełną listą metryk oferowanych przez Application Gateway. Zapoznaj się z tematem [Wizualizacja metryk](./application-gateway-metrics.md#metrics-visualization) w Azure Portal i [dokumentacji usługi Azure monitor](../azure-monitor/alerts/alerts-metric.md) dotyczącej sposobu ustawiania alertów dotyczących metryk.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Skalowanie dla jednostki SKU Application Gateway V1 (jednostka SKU standardowa/WAF)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Ustawianie liczby wystąpień na podstawie szczytowego użycia procesora CPU
Jeśli używasz bramy SKU w wersji 1, będziesz mieć możliwość ustawienia Application Gateway do 32 wystąpień do skalowania. Sprawdź użycie procesora przez Application Gateway w ciągu ostatnich miesięcy w przypadku dowolnych przekroczeń powyżej 80%, dostępne jako metryki do monitorowania. Zalecane jest, aby ustawić liczbę wystąpień zgodnie z szczytowym użyciem i z 10% do 20% dodatkowego buforu w celu uwzględnienia dowolnych danych.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Metryki użycia procesora CPU w wersji 1" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Korzystanie z jednostki SKU w wersji 2 w wersji 1 na potrzeby możliwości skalowania automatycznego i korzyści z wydajności
Jednostka SKU v2 oferuje Skalowanie automatyczne, aby zapewnić, że Application Gateway można skalować w górę w miarę wzrostu ruchu. Oferuje również inne korzyści wynikające z wydajności, takie jak pięciokrotną lepsza wydajność odciążania TLS, szybszy czas wdrażania i aktualizacji, nadmiarowość stref i wiele więcej w porównaniu do wersji 1. Więcej informacji można znaleźć w dokumentacji dotyczącej [wersji 2](./application-gateway-autoscaling-zone-redundant.md) i zapoznać się z [dokumentacją dotyczącą migracji](./migrate-v1-v2.md) z wersji 1 do wersji 2, aby dowiedzieć się, jak migrować istniejące bramy SKU V1 do wersji 2 jednostki SKU. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Skalowanie automatyczne dla jednostki SKU Application Gateway v2 (jednostka SKU Standard_v2/WAF_v2)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ustaw maksymalną dozwoloną liczbę wystąpień (125)
 
W przypadku jednostki SKU Application Gateway v2 ustawienie maksymalnej liczby wystąpień na maksymalną możliwą wartość 125 pozwala na skalowanie w poziomie Application Gateway w miarę potrzeby. Dzięki temu można obsługiwać możliwy wzrost ruchu do aplikacji. Opłata zostanie naliczona tylko za używane jednostki pojemności. 

Upewnij się, że w podsieci Sprawdź rozmiar podsieci i dostępną liczbę adresów IP i ustaw maksymalną liczbę wystąpień na podstawie tej wartości. Jeśli podsieć nie ma wystarczającej ilości miejsca do zaspokojenia, należy ponownie utworzyć bramę w tej samej lub innej podsieci, która ma wystarczającą pojemność. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="Konfiguracja automatycznego skalowania w wersji 2" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Ustawianie minimalnej liczby wystąpień na podstawie średniego użycia jednostek obliczeniowych

W przypadku jednostki SKU Application Gateway v2 Skalowanie automatyczne trwa od sześciu do siedmiu minut w celu skalowania w poziomie i aprowizacji dodatkowego zestawu wystąpień gotowego do obsługi ruchu. Do momentu, gdy w ruchu występują krótkie skoki, istniejące wystąpienia bramy mogą być pod obciążeniem, co może spowodować nieoczekiwane opóźnienie lub utratę ruchu. 

Zalecane jest, aby ustawić minimalną liczbę wystąpień na optymalny poziom. Na przykład jeśli wymagane są 50 wystąpień do obsługi ruchu w szczytowym obciążeniu, ustawienie minimum od 25 do 30 jest dobrym pomysłem, a nie na <10, tak że nawet w przypadku krótkich obciążeń ruchem Application Gateway będzie można je obsłużyć i zapewnić wystarczającą ilość czasu na reagowanie i skuteczne skalowanie.

Sprawdź metrykę jednostki obliczeniowej przez ostatni miesiąc. Metryka jednostki obliczeniowej to reprezentacja użycia procesora CPU przez bramę i oparta na szczytowym użyciu podzielonym przez 10, można ustawić minimalną wymaganą liczbę wystąpień. Należy zauważyć, że 1 wystąpienie bramy aplikacji może obsłużyć co najmniej 10 jednostek obliczeniowych

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="Metryki jednostek obliczeniowych w wersji 2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Skalowanie ręczne dla jednostki SKU Application Gateway v2 (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Ustawianie liczby wystąpień na podstawie szczytowego użycia jednostek obliczeniowych 

W przeciwieństwie do skalowania automatycznego, w ręcznym skalowaniu należy ręcznie ustawić liczbę wystąpień bramy aplikacji na podstawie wymagań dotyczących ruchu sieciowego. Zalecane jest, aby ustawić liczbę wystąpień zgodnie z szczytowym użyciem i z 10% do 20% dodatkowego buforu w celu uwzględnienia dowolnych danych. Na przykład, jeśli ruch wymaga 50 wystąpień w szczycie, należy zastanowić 55 do 60 wystąpień w celu obsługi nieoczekiwanych skoków ruchu, które mogą wystąpić.

Sprawdź metrykę jednostki obliczeniowej przez ostatni miesiąc. Metryka jednostki obliczeniowej to reprezentacja użycia procesora CPU przez bramę i oparta na szczytowym użyciu podzielonym przez 10, można ustawić wymaganą liczbę wystąpień, ponieważ 1 wystąpienie bramy aplikacji może obsłużyć co najmniej 10 jednostek obliczeniowych

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Aby otrzymywać powiadomienia o wszelkich anomaliach dotyczących ruchu lub wykorzystania, można skonfigurować alerty dla określonych metryk. Zobacz [dokumentację metryk](./application-gateway-metrics.md) , aby zapoznać się z pełną listą metryk oferowanych przez Application Gateway. Zapoznaj się z tematem [Wizualizacja metryk](./application-gateway-metrics.md#metrics-visualization) w Azure Portal i [dokumentacji usługi Azure monitor](../azure-monitor/alerts/alerts-metric.md) dotyczącej sposobu ustawiania alertów dotyczących metryk.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Alerty dla jednostki SKU Application Gateway V1 (standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Zgłoś alert, jeśli średnie użycie procesora CPU przekroczy 80%

W normalnych warunkach użycie procesora nie powinno regularnie przekraczać 90%, ponieważ może to powodować opóźnienie w witrynach internetowych hostowanych za bramą aplikacji i zakłócać środowisko klienta. Można pośrednio kontrolować lub zwiększać wykorzystanie procesora przez modyfikację konfiguracji Application Gateway przez zwiększenie liczby wystąpień lub przechodzenie do większego rozmiaru jednostki SKU lub wykonanie obu tych czynności. Ustaw Alert, jeśli Metryka użycia procesora CPU spadnie powyżej 80% średniej.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Zgłoś alert w przypadku wartości progowej przekroczenia liczby hostów w złej kondycji

Ta Metryka wskazuje liczbę serwerów zaplecza, których Brama aplikacji nie może pomyślnie sondować. Spowoduje to wychwycenie problemów, w których wystąpienia bramy aplikacji nie mogą nawiązać połączenia z zapleczem. Zgłoś alert, jeśli liczba przekracza 20% pojemności zaplecza. Na przykład Jeśli obecnie masz 30 serwerów zaplecza w puli zaplecza, ustaw alert, jeśli liczba hostów w złej kondycji spadnie powyżej 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Zgłoś alert w przypadku wartości progowej przekroczenia stanu odpowiedzi (4xx, 5xx) 

Utwórz alert, gdy Application Gateway stanie odpowiedzi to 4xx lub 5xx. Z powodu przejściowych problemów może wystąpić okazjonalna odpowiedź 4xx lub 5xx. Aby określić próg statyczny lub użyć progu dynamicznego dla alertu, należy obserwować bramę w środowisku produkcyjnym.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Zgłoś alert w przypadku przekroczenia progu żądań zakończonych niepowodzeniem 

Utwórz alert w przypadku niepowodzenia wartość progowa przekroczenia przez żądania. Aby określić próg statyczny lub użyć progu dynamicznego dla alertu, należy obserwować bramę w środowisku produkcyjnym.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Przykład: Konfigurowanie alertu dla ponad 100 żądań zakończonych niepowodzeniem w ciągu ostatnich 5 minut

W tym przykładzie pokazano, jak za pomocą Azure Portal skonfigurować alert, jeśli liczba żądań zakończonych niepowodzeniem w ciągu ostatnich 5 minut jest większa niż 100.
1. Przejdź do **Application Gateway**.
2. Na panelu po lewej stronie wybierz pozycję **metryki** na karcie **monitorowanie** . 
3. Dodaj metrykę dla **żądań zakończonych niepowodzeniem**.
4. Kliknij pozycję **Nowa reguła alertu** i zdefiniuj swój warunek i akcje
5. Kliknij pozycję **Utwórz regułę alertu** , aby utworzyć i włączyć alert

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="2. tworzenie alertów" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Alerty dla jednostki SKU Application Gateway v2 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Zgłoś alert, jeśli wykorzystanie jednostek obliczeniowych przekroczy 75% średniego użycia 

Jednostka obliczeniowa to miara wykorzystania obliczeń Application Gateway. Sprawdź średnie użycie jednostek obliczeniowych w ciągu ostatnich miesięcy i Ustaw Alert, jeśli przekroczy on 75% czasu. Na przykład jeśli średnie użycie ma 10 jednostek obliczeniowych, Ustaw Alert na 7,5. Spowoduje to wygenerowanie alertów, jeśli użycie zwiększy się i przekroczy czas odpowiedzi. Możesz podnieść wartość minimalną, jeśli uważasz, że ten ruch będzie w stanie wzrosnąć. Postępuj zgodnie z powyższymi sugestiami skalowania w celu skalowania w poziomie.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Przykład: Konfigurowanie alertu na 75% średniego użycia CU

W tym przykładzie pokazano, jak za pomocą Azure Portal skonfigurować alert w przypadku osiągnięcia 75% średniej wartości użycia. 
1. Przejdź do **Application Gateway**.
2. Na panelu po lewej stronie wybierz pozycję **metryki** na karcie **monitorowanie** . 
3. Dodaj metrykę dla **średniej bieżącej jednostki obliczeniowej**. 
4. Jeśli określono minimalną liczbę wystąpień jako średniego użycia CU, przejdź dalej i Ustaw Alert, gdy zostanie użytych 75% minimalnej liczby wystąpień. Na przykład jeśli średnie użycie wynosi 10 jednostek, Ustaw Alert na 7,5. Spowoduje to wygenerowanie alertów, jeśli użycie zwiększy się i przekroczy czas odpowiedzi. Możesz podnieść wartość minimalną, jeśli uważasz, że ten ruch będzie w stanie wzrosnąć. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="Alerty jednostek obliczeniowych w wersji 2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> Można ustawić alert, który ma być wykonywany przy niższej lub wyższej wartości procentowej użycia, w zależności od tego, jak poufne jest prawdopodobieństwo potencjalnego obciążenia.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Zgłoś alert, jeśli wykorzystanie jednostki pojemności przekracza 75% szczytowego użycia 

Jednostki pojemności reprezentują ogólne wykorzystanie bramy w zakresie przepływności, obliczeń i liczby połączeń. Sprawdź maksymalne użycie jednostek pojemności w ciągu ostatniego miesiąca i Ustaw Alert, jeśli przekroczy on 75% czasu. Jeśli na przykład maksymalne użycie ma 100 jednostek wydajności, należy ustawić alert dla 75 CUs. Postępuj zgodnie z powyższymi dwiema sugestiami w celu skalowania w poziomie, w razie potrzeby.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Zgłoś alert w przypadku wartości progowej przekroczenia liczby hostów w złej kondycji 

Ta Metryka wskazuje liczbę serwerów zaplecza, których Brama aplikacji nie może pomyślnie sondować. Spowoduje to wychwycenie problemów, w których wystąpienia bramy aplikacji nie mogą nawiązać połączenia z zapleczem. Zgłoś alert, jeśli liczba przekracza 20% pojemności zaplecza. Na przykład Jeśli obecnie masz 30 serwerów zaplecza w puli zaplecza, ustaw alert, jeśli liczba hostów w złej kondycji spadnie powyżej 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Zgłoś alert w przypadku wartości progowej przekroczenia stanu odpowiedzi (4xx, 5xx) 

Utwórz alert, gdy Application Gateway stanie odpowiedzi to 4xx lub 5xx. Z powodu przejściowych problemów może wystąpić okazjonalna odpowiedź 4xx lub 5xx. Aby określić próg statyczny lub użyć progu dynamicznego dla alertu, należy obserwować bramę w środowisku produkcyjnym.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Zgłoś alert w przypadku przekroczenia progu żądań zakończonych niepowodzeniem 

Utwórz alert w przypadku niepowodzenia wartość progowa przekroczenia przez żądania. Aby określić próg statyczny lub użyć progu dynamicznego dla alertu, należy obserwować bramę w środowisku produkcyjnym.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Zgłoś alert w przypadku wartości progowej przekroczenia limitu czasu odpowiedzi ostatniego bajtu 

Ta Metryka wskazuje przedział czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi. Utwórz alert, jeśli opóźnienie odpowiedzi wewnętrznej bazy danych jest większe niż zwykle określony próg. Na przykład ustaw, aby otrzymywać alerty, gdy opóźnienie odpowiedzi wewnętrznej bazy danych zwiększa się o więcej niż 30% od wartości zwykłej.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Zgłoś alert, jeśli Application Gateway próg całkowitego przekroczenia czasu

Jest to interwał od momentu odebrania przez Application Gateway pierwszego bajtu żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi do klienta. Należy utworzyć alert, jeśli opóźnienie odpowiedzi wewnętrznej bazy danych jest większe niż zwykle określony próg. Na przykład można ustawić, aby otrzymywać alerty, gdy łączny czas opóźnienia rośnie o ponad 30% od wartości zwykłej.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Konfigurowanie WAF z filtrowaniem geograficznym i ochroną bot w celu zatrzymywania ataków
Jeśli potrzebujesz dodatkowej warstwy zabezpieczeń przed aplikacją, użyj Application Gateway jednostki SKU WAF_v2 do obsługi funkcji WAF. Jednostkę SKU v2 można skonfigurować tak, aby zezwalała na dostęp tylko do aplikacji z danego kraju/regionu lub krajów/regionów. Można skonfigurować regułę niestandardową WAF, aby jawnie zezwalać lub blokować ruch na podstawie lokalizacji geograficznej. Więcej informacji znajduje się w sekcji [filtrowanie geograficzne reguł niestandardowych](../web-application-firewall/ag/geomatch-custom-rules.md) i [Konfigurowanie reguł niestandardowych w Application Gateway WAF_v2 jednostki SKU przy użyciu programu PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Włącz ochronę bot, aby blokować znane złe botów. Powinno to zmniejszyć ilość ruchu przychodzącego do aplikacji. Aby uzyskać więcej informacji, zobacz [bot Protection with Set up](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Włącz diagnostykę na Application Gateway i WAF

Dzienniki diagnostyczne umożliwiają wyświetlanie dzienników zapory, dzienników wydajności i dzienników dostępu. Za pomocą tych dzienników na platformie Azure można zarządzać bramami aplikacji i rozwiązywać problemy. Aby uzyskać więcej informacji, zobacz naszą [dokumentację diagnostyki](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurowanie zasad protokołu TLS dla dodatkowych zabezpieczeń
Upewnij się, że używasz najnowszej wersji zasad protokołu TLS ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)). Wymusza to użycie protokołu TLS 1,2 i silniejszych szyfrów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wersji zasad protokołu TLS i mechanizmów szyfrowania za pomocą programu PowerShell](./application-gateway-configure-ssl-policy-powershell.md).