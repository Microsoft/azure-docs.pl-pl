---
title: 'Omówienie: co to jest Azure Time Series Insights Gen2? — Azure Time Series Insights Gen2 | Microsoft Docs'
description: Dowiedz się więcej o zmianach, ulepszeniach i funkcjach w Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 4ff26289962aa06f9638644e28da23b646554281
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865319"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Co to jest usługa Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 to otwarta i skalowalna, end-to-end usługa analizy IoT oferująca najlepsze w swojej klasie środowisko użytkownika i rozbudowane interfejsy API, które integrują jej zaawansowane możliwości z istniejącym przepływem pracy lub aplikacją.

Umożliwia ona zbieranie, przetwarzanie, przechowywanie i wizualizowanie danych w skali Internet rzeczy (IoT) — dane wysoce kontekstowe i zoptymalizowane pod kątem szeregów czasu.

Azure Time Series Insights Gen2 jest przeznaczony do eksplorowania danych ad hoc i analizy operacyjnej, co pozwala odkrywać ukryte trendy, wykrywać anomalie i przeprowadzać analizę głównych przyczyn. Jest to otwarta i elastyczna oferta, która spełnia szerokie potrzeby przemysłowych wdrożeń IoT.

## <a name="video"></a>Wideo

Dowiedz się więcej o Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definicja danych IoT

Przemysłowe dane IoT w organizacjach intensywnie obciążających zasobami często nie mają spójności strukturalnej ze względu na zróżnicowany charakter urządzeń i czujników w środowisku przemysłowym. Dane z tych strumieni charakteryzują się znacznymi lukami, czasami uszkodzonymi komunikatami i fałszywymi odczytami. Dane IoT często mają znaczenie w kontekście dodatkowych danych wejściowych które pochodzą z zewnętrznych źródeł, takich jak crm lub ERP, które dodają kontekst dla przepływów pracy na wszystkich urządzeniach. Dane wejściowe ze źródeł danych innych firm, takie jak dane o pogodzie, mogą pomóc w rozszerzonym strumieniu telemetrii w danej instalacji.

Wszystko to oznacza, że tylko część danych jest używana do celów operacyjnych i biznesowych, a analiza wymaga kontekstu. Dane przemysłowe są często analizowane w bardziej szczegółowej analizie w dłuższym okresie, aby zrozumieć i skorelować trendy. Przekształcanie zbieranych danych IoT na szczegółowe informacje umożliwiające podejmowanie działań wymaga następujących elementów:

* przetwarzanie danych w celu oczyszczenia, przefiltrowania, interpolacji, przekształcenia i przygotowania danych do analizy;
* Struktura do nawigowania po danych i ich rozumienia, czyli normalizacji i kontekstowej normalizacji danych.
* Ekonomiczne przechowywanie przez długi lub nieskończony czas przechowywania przetworzonych (lub pochodnych) danych i danych pierwotnych.

Takie dane zapewniają spójne, kompleksowe, aktualne i poprawne informacje na potrzeby analizy biznesowej i raportowania.

Na poniższej ilustracji przedstawiono typowy przepływ danych IoT.

[![Przepływ danych IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 dla przemysłowego IoT

Krajobraz IoT jest zróżnicowany i obejmuje klientów obejmujących wiele różnych segmentów branży, w tym produkcja, branża samochodowy, energetyka, media, inteligentne budynki i konsulting. Na tym szerokim zakresie przemysłowego rynku IoT rozwiązania natywne dla chmury, które zapewniają kompleksową analizę danych IoT na dużą skalę, wciąż ewoluują.

Usługa Azure Time Series Insights Gen2 odpowiada na tę potrzebę rynku, zapewniając gotowe do użytku, end-to-end rozwiązanie do analizy IoT z rozbudowanym modelem semantyki na potrzeby kontekstowego przetwarzania danych szeregów czasu, szczegółowych informacji opartych na zasobach i najlepszego w swojej klasie środowisko użytkownika do odnajdywania, popularnego, wykrywania anomalii i analizy operacyjnej.

Bogata platforma analizy operacyjnej w połączeniu z naszymi interaktywnymi możliwościami eksploracji danych umożliwia korzystanie z usługi Azure Time Series Insights Gen2 w celu uzyskania większej wartości z danych zebranych z zasobów IoT. Oferta Gen2 obsługuje:

* Wielowarstwowe rozwiązanie magazynu z obsługą analizy gorącej i zimnej zapewnia klientom możliwość rozsyłania danych między ciepłymi i zimnymi na potrzeby interaktywnej analizy na ciepłych danych, a także analizy operacyjnej z kilkudziesięciu lat danych historycznych.

  * Wysoce interaktywne rozwiązanie analizy ciepłej do wykonywania częstych i dużej liczby zapytań w krótszym okresie danych
  * Skalowalny, zoptymalizowany pod kątem wydajności i kosztów szereg czasowy data lake oparty na usłudze Azure Storage, dzięki czemu klienci mogą trendować dane szeregów czasu z wielu lat w ciągu kilku sekund.

* Obsługa modelu semantycznego, która opisuje domenę i metadane skojarzone z pochodnymi i nieprzetworzonymi sygnałami z zasobów i urządzeń.

* Elastyczna platforma do analizy do przechowywania historycznych danych szeregów czasu na koncie usługi Azure Storage należącym do klienta, dzięki czemu klienci mogą mieć własność swoich danych IoT. Dane są przechowywane w formacie open source Apache Parquet, który umożliwia łączność i połączenia między różnymi scenariuszami danych, w tym analizą predykcyjną, uczeniem maszynowym i innymi obliczeniami niestandardowymi, które są wykonywane przy użyciu znanych technologii, takich jak Spark i Databricks.

* Rozbudowane funkcje analityczne z rozszerzonymi interfejsami API zapytań i interfejsem użytkownika, które łączy szczegółowe informacje o danych oparte na zasobach z rozbudowaną analizą danych ad hoc z obsługą interpolacji, funkcji skalarnych i agregowanych, zmiennych kategorii, wykresów punktowych i sygnałów szeregów czasu zmiany czasu w celu szczegółowej analizy.

* Platforma klasy korporacyjnej do obsługi potrzeb klientów korporacyjnych w zakresie skalowania, wydajności, bezpieczeństwa i niezawodności.

* Rozszerzalność i obsługa integracji w ramach analiz typu end-to-end. Azure Time Series Insights Gen2 zapewnia rozszerzalne platformy analityczne dla różnych scenariuszy danych. Power BI connector umożliwia klientom korzystanie z zapytań, które robią w u Azure Time Series Insights Gen2, bezpośrednio do usługi Power BI w celu uzyskania ujednoliconego widoku analizy biznesowej i analizy szeregów czasowych w jednym okienku.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu.

  [![Najważniejsze możliwości](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Usługa Azure Time Series Insights Gen2 oferuje skalowalny model cen z płatnością zgodnie z potrzebami na potrzeby przetwarzania danych, magazynowania (danych i metadanych) oraz wykonywania zapytań, dzięki czemu klienci mogą dostosować użycie do swoich potrzeb biznesowych.

Wraz z wprowadzeniem tych kluczowych możliwości przemysłowego IoT Azure Time Series Insights Gen2 zapewnia również następujące kluczowe korzyści:  

| Możliwość | Korzyść |
| ---| ---|
| Wielowarstwowy magazyn danych szeregów czasu w skali IoT | Za pomocą udostępnionego potoku przetwarzania danych w celu pozyskania danych można pozyskać dane zarówno do ciepłych, jak i zimnych magazynów. Używaj ciepłego magazynu do interaktywnych zapytań i zimnego magazynu do przechowywania dużych ilości danych. Aby dowiedzieć się więcej na temat sposobu korzystania z wysokiej wydajności zapytań opartych na zasobach, zobacz [zapytania](./concepts-query-overview.md). |
| Model szeregu czasowego na potrzeby określania kontekstu nieprzetworzonych danych telemetrycznych i uzyskiwania szczegółowych danych opartych na zasobach | Za pomocą modelu szeregów czasowych można tworzyć wystąpienia, hierarchie, typy i zmienne dla danych szeregów czasu. Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [Time Series Model (Model szeregów czasowych).](./concepts-model-overview.md)  |
| Bezproblemowa i ciągła integracja z innymi rozwiązaniami do obsługi danych | Dane w zimnym Azure Time Series Insights Gen2 są [przechowywane w](./concepts-storage.md) plikach open source Apache Parquet. Umożliwia to integrację danych z innymi rozwiązaniami danych firmy 1 lub 3 dla scenariuszy, które obejmują analizę biznesową, zaawansowane uczenie maszynowe i analizę predykcyjną. |
| Eksploracja danych w czasie niemal rzeczywistym | Środowisko [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) udostępnia wizualizację dla wszystkich danych przesyłanych strumieniowo za pośrednictwem potoku pozyskiwania. Po połączeniu źródła zdarzeń można wyświetlać i eksplorować dane zdarzeń oraz badać je. W ten sposób można sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami. Można również monitorować kondycję, wydajność i ogólną efektywność zasobu IoT. |
| Rozszerzalność i integracja | Integracja łącznika Power BI jest dostępna bezpośrednio w interfejsie użytkownika  programu Time Series Explorer za pośrednictwem opcji Eksportuj, co umożliwia klientom eksportowanie zapytań szeregów czasu tworzyć w naszym interfejsie użytkownika bezpośrednio do pulpitu usługi Power BI i wyświetlanie wykresów szeregów czasu wraz z innymi analizami analizy biznesowej. Otwiera to drzwi do nowej klasy scenariuszy dla przedsiębiorstw przemysłowego IoT, które zainwestowały w usługę Power BI, zapewniając jedną szybę przez analizę z różnych źródeł danych, w tym szeregów czasu IoT. |
| Aplikacje niestandardowe wbudowane na platformie Azure Time Series Insights Gen2 | Azure Time Series Insights Gen2 obsługuje zestaw [JavaScript SDK.](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) Ten zestaw SDK zapewnia zaawansowane kontrolki i uproszczony dostęp do zapytań. Użyj zestawu SDK, aby tworzyć niestandardowe aplikacje IoT na Azure Time Series Insights Gen2 zgodnie z potrzebami biznesowymi. Interfejsów API zapytań Azure Time Series Insights [Gen2](./concepts-query-overview.md) można również używać bezpośrednio do kierowania danych do niestandardowych aplikacji IoT. |

## <a name="region-availability"></a>Dostępność w danym regionie

Odwiedź stronę [Produkty globalnej infrastruktury platformy Azure według regionów,](https://azure.microsoft.com/global-infrastructure/services/?products=time-series-insights) aby dowiedzieć się, Azure Time Series Insights są dostępne.

Azure Time Series Insights znajduje się w jednym regionie. Jednak dane klientów są przechowywane/przetwarzane poza regionem, w którym klient wdraża wystąpienie usługi, z wyjątkiem następujących regionów, w których dane nie są replikowane poza regionem:

* Azja Wschodnia
* Southeast Asia
*   Australia Południowo-Wschodnia
*   West Europe
*   Południowe Zjednoczone Królestwo
*   Zachodnie stany USA
*   Francja Środkowa
*   Kanada Środkowa

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Przewodnik Szybki start](./quickstart-explore-tsi.md)

Dowiedz się więcej na temat przypadków użycia:

> [!div class="nextstepaction"]
> [Azure Time Series Insights użycia 2. generacji](./overview-use-cases.md)
