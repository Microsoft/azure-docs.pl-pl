---
title: 'Przegląd: co to jest wersja zapoznawcza Azure Time Series Insights? -Azure Time Series Insights | Microsoft Docs'
description: Informacje na temat zmian, ulepszeń i funkcji dostępnych w wersji zapoznawczej Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 73244a635bbf14efcf33f1b978db14e9e2589581
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81271067"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Co to jest wersja zapoznawcza usługi Azure Time Series Insights?

Wersja zapoznawcza Azure Time Series Insights to kompleksowa oferta typu "platforma jako usługa" (PaaS). Można jej używać do zbierania, przetwarzania, przechowywania, analizowania i wykonywania zapytań dotyczących danych w skali Internet rzeczy (IoT) — danych, które są wysoce zastrzeżonych i zoptymalizowane pod kątem szeregów czasowych. 

Time Series Insights jest przeznaczony do eksploracji danych ad hoc i analizy operacyjnej. Jest to rozszerzalna i dostosowana oferta usług, która spełnia szeroką gamę potrzeb przemysłowych wdrożeń IoT.

## <a name="video"></a>Film wideo

Dowiedz się więcej o Azure Time Series Insights wersji zapoznawczej.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definicja danych IoT

Przemysłowe dane IoT w organizacjach intensywnie korzystających z zasobów często nie posiadają spójności strukturalnej ze względu na zróżnicowany charakter urządzeń i czujników w ustawieniach przemysłowych. Dane z tych strumieni są scharakteryzowane przez znaczące przerwy, a czasami uszkodzone komunikaty i fałszywe odczyty. Dane IoT są często zrozumiałe w kontekście dodatkowych danych wejściowych, które pochodzą z pierwszych stron lub innych źródeł, takich jak CRM lub ERP, które dodają kontekst dla kompleksowych przepływów pracy. Wejścia ze źródeł danych innych firm, takie jak dane pogodowe, mogą pomóc w rozszerzeniu strumieni telemetrii w danej instalacji. 

Wszystko to oznacza, że tylko ułamek danych jest używany do celów operacyjnych i firmowych, a analiza wymaga contextualization. Dane przemysłowe są często historyczne w celu dokładnej analizy w dłuższym okresie, aby zrozumieć i skorelować trendy. Przekształcanie zbieranych danych IoT na szczegółowe informacje umożliwiające podejmowanie działań wymaga następujących elementów: 

* przetwarzanie danych w celu oczyszczenia, przefiltrowania, interpolacji, przekształcenia i przygotowania danych do analizy;
* Struktura umożliwiająca nawigowanie i zrozumienie danych, czyli Aby znormalizować i contextualize dane.
* Oszczędny magazyn na potrzeby długotrwałego lub nieskończonego przechowywania przetworzonych (lub pochodnych) danych i danych pierwotnych.

Takie dane zapewniają spójne, wszechstronne, aktualne i prawidłowe informacje dotyczące analizy biznesowej i raportowania.

Na poniższej ilustracji przedstawiono typowy przepływ danych IoT.

[![Przepływ danych IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Usługa Azure Time Series Insights dla przemysłowego Internetu rzeczy

Środowisko IoT jest zróżnicowane dla klientów obejmujących różne segmenty branżowe, takie jak produkcja, samochodowa, energia, narzędzia, inteligentne budynki i doradztwo. W ramach tego szerokiego zakresu przemysłowego IoT, natywne rozwiązania w chmurze, które zapewniają kompleksową analizę na dużą skalę danych IoT, są nadal rozwijane. 

Azure Time Series Insights rozwiązać ten problem na rynku, dostarczając gotowe, kompleksowe rozwiązanie IoT Analytics z rozbudowanym modelem semantycznym do contextualization danych szeregów czasowych, analiz opartych na zasobach i najlepszych w swojej klasie środowisku użytkownika na potrzeby odnajdywania, trendów, wykrywania anomalii i analizy operacyjnej. 

Rozbudowana platforma analityczna działająca w połączeniu z naszymi interaktywnymi możliwościami eksplorowania danych, można użyć Time Series Insights, aby uzyskać więcej wartości z danych zebranych z zasobów IoT. Oferta wersji zapoznawczej zapewnia następujące składniki: 

* Wielowarstwowe rozwiązanie magazynu z obsługą wysokiej i zimnej analizy zapewnia klientom możliwość przesyłania danych między ciepłą i zimną analizą interaktywną, a także analizą operacyjną w przypadku danych historycznych. 

    *    Wysoce interaktywne rozwiązanie do analizy ciepłej do częstego wykonywania i wiele zapytań w ciągu krótszych okresów 
    *    Skalowalne, wydajne i zoptymalizowane pod kątem kosztów usługi Data Lake w oparciu o usługę Azure Storage, dzięki czemu klienci mogą trendować dane szeregów czasowych w ciągu kilku sekund. 

* Obsługa modelu semantycznego opisującego domenę i metadane skojarzone z sygnałami pochodnymi i nieprzetworzonymi z zasobów i urządzeń.

* Elastyczna platforma analityczna umożliwiająca przechowywanie historycznych danych szeregów czasowych na koncie usługi Azure Storage należącym do klienta, dzięki czemu klienci mogą mieć własność swoich danych IoT. Dane są przechowywane w formacie Apache Parquet typu open source, który umożliwia łączność i międzyoperacyjność w różnych scenariuszach danych, w tym analizy predykcyjnej, uczenie maszynowe i inne niestandardowe obliczenia wykonywane przy użyciu znanych technologii, takich jak Spark, datakosteks i Jupyter.

* Zaawansowana analiza przy użyciu ulepszonych interfejsów API zapytań i środowiska użytkownika, które łączą dane oparte na zasobach dzięki bogatej analizie danych ad hoc z obsługą interpolacji, skalarnych i agregujących funkcji, zmiennych kategorii, wykresów punktowych i przesunięciem czasu szeregów czasowych dla dokładnej analizy.

*    Platforma klasy korporacyjnej umożliwiająca obsługę skalowalności, wydajności, zabezpieczeń i niezawodności potrzeb naszych klientów IoT w przedsiębiorstwie.

* Obsługa rozszerzalności i integracji na potrzeby kompleksowej analizy. Time Series Insights zapewnia rozszerzalną platformę analityczną dla różnych scenariuszy danych. Łącznik Time Series Insights Power BI umożliwia klientom wykonywanie zapytań, które wykonują w Time Series Insights bezpośrednio do Power BI, aby uzyskać ujednolicony wgląd w analizy analizy biznesowej i szeregów czasowych w jednym okienku Glass.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu.

  [![Najważniejsze możliwości](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights udostępnia skalowalny model cenowy z płatnością zgodnie z rzeczywistym użyciem na potrzeby przetwarzania danych, magazynowania (danych i metadanych) oraz wykonywania zapytań, dzięki czemu klienci mogą dostosowywać ich użycie do potrzeb firmy. 
 
Wraz z wprowadzeniem tych kluczowych funkcji IoT rzeczy Time Series Insights również zapewnia następujące korzyści.  

| | |
| ---| ---|
| Magazyn wielowarstwowy dla danych szeregów czasowych w skali IoT | Za pomocą potoku przetwarzania danych udostępnionych do pozyskiwania danych można pozyskać dane zarówno do magazynu ciepłego, jak i chłodni. Korzystaj ze sklepu ciepłego dla interaktywnych zapytań i chłodnego magazynu do przechowywania dużych ilości danych. Aby dowiedzieć się więcej o tym, jak korzystać z wysoce wydajnych zapytań opartych na zasobach, zobacz [zapytania](./time-series-insights-update-tsq.md). |
| Model szeregu czasowego na potrzeby określania kontekstu nieprzetworzonych danych telemetrycznych i uzyskiwania szczegółowych danych opartych na zasobach | Możesz użyć modelu szeregów czasowych, aby utworzyć wystąpienia, hierarchie, typy i zmienne dla danych szeregów czasowych. Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).  |
| Bezproblemowa i ciągła integracja z innymi rozwiązaniami do obsługi danych | Dane znajdujące się w Time Series Insights chłodny magazyn są [przechowywane](./time-series-insights-update-storage-ingress.md) w plikach Apache Parquet. Pozwala to na integrację danych z innymi rozwiązaniami dotyczącymi danych, 1 lub trzecimi, w przypadku scenariuszy obejmujących analizę biznesową, zaawansowaną Uczenie maszynowe i analizę predykcyjną. |
| Eksploracja danych w czasie niemal rzeczywistym | Środowisko użytkownika [eksploratora usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md) udostępnia wizualizacje dla wszystkich danych przesyłanych strumieniowo za pośrednictwem potoku pozyskiwania. Po nawiązaniu połączenia ze źródłem zdarzeń można wyświetlać, eksplorować i wysyłać zapytania dotyczące danych zdarzeń. W ten sposób można sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami. Można również monitorować kondycję, wydajność i ogólną efektywność zasobu IoT. | 
| Rozszerzalność i integracja | Integracja łącznika Power BI Azure Time Series Insights jest dostępna bezpośrednio w środowisku użytkownika Eksploratora szeregów czasowych za pomocą opcji **eksportu** , umożliwiając klientom eksportowanie zapytań szeregów czasowych tworzonych w środowisku użytkownika bezpośrednio na pulpit Power BI i wyświetlanie ich wykresów szeregów czasowych wraz z inną ANALIZĄ analizy biznesowej. Spowoduje to otwarcie drzwi do nowej klasy scenariuszy dla firmowych przedsiębiorstw IoT, które zainwestowali w Power BI przez zapewnienie pojedynczego okienka Glass przez analizę z różnych źródeł danych, w tym szeregów czasowych IoT. | 
| Aplikacje niestandardowe skompilowane na platformie Time Series Insights | Usługa Time Series Insights obsługuje [zestaw SDK języka JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Ten zestaw SDK zapewnia zaawansowane kontrolki i uproszczony dostęp do zapytań. Użyj zestawu SDK do kompilowania niestandardowych aplikacji IoT na Time Series Insights, aby odpowiadały potrzebom biznesowym. Można również bezpośrednio korzystać z [interfejsów API zapytań](./time-series-insights-update-tsq.md) usługi Time Series Insights, aby kierować dane do niestandardowych aplikacji IoT. |

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z usługą Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Przewodnik Szybki start](./time-series-insights-update-quickstart.md)

Dowiedz się więcej na temat przypadków użycia:

> [!div class="nextstepaction"]
> [Przypadki użycia usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-use-cases.md)