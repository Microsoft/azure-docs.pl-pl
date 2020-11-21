---
title: 'Przegląd: co to jest Azure Time Series Insights Gen2? -Azure Time Series Insights Gen2 | Microsoft Docs'
description: Dowiedz się więcej na temat zmian, ulepszeń i funkcji w Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 8fdbb42d06f26849d14e093c340c6d2457ee6ec9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020099"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Co to jest Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 to otwarta i skalowalna kompleksowa usługa analizy IoT, która oferuje najlepsze w swojej klasie środowiska użytkownika i rozbudowane interfejsy API, aby zintegrować swoje zaawansowane możliwości z istniejącym przepływem pracy lub aplikacją.

Można jej używać do zbierania, przetwarzania, przechowywania, wykonywania zapytań i wizualizacji danych w skali Internet rzeczy (IoT) — danych, które są wysoce zastrzeżonych i zoptymalizowane pod kątem szeregów czasowych.

Azure Time Series Insights Gen2 jest przeznaczony do eksploracji danych ad hoc i analizy operacyjnej, co umożliwia odkrywanie ukrytych trendów, anomalii wykrywania trendów i przeprowadzanie analiz głównych przyczyn. Jest to otwarta i elastyczna oferta, która spełnia szeroką gamę potrzeb przemysłowych wdrożeń IoT.

## <a name="video"></a>Wideo

Dowiedz się więcej o Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definicja danych IoT

Przemysłowe dane IoT w organizacjach intensywnie korzystających z zasobów często nie posiadają spójności strukturalnej ze względu na zróżnicowany charakter urządzeń i czujników w ustawieniach przemysłowych. Dane z tych strumieni są scharakteryzowane przez znaczące przerwy, a czasami uszkodzone komunikaty i fałszywe odczyty. Dane IoT są często zrozumiałe w kontekście dodatkowych danych wejściowych, które pochodzą z pierwszych stron lub innych źródeł, takich jak CRM lub ERP, które dodają kontekst dla kompleksowych przepływów pracy. Wejścia ze źródeł danych innych firm, takie jak dane pogodowe, mogą pomóc w rozszerzeniu strumieni telemetrii w danej instalacji.

Wszystko to oznacza, że tylko ułamek danych jest używany do celów operacyjnych i firmowych, a analiza wymaga contextualization. Dane przemysłowe są często historyczne w celu dokładnej analizy w dłuższym okresie, aby zrozumieć i skorelować trendy. Przekształcanie zbieranych danych IoT na szczegółowe informacje umożliwiające podejmowanie działań wymaga następujących elementów:

* przetwarzanie danych w celu oczyszczenia, przefiltrowania, interpolacji, przekształcenia i przygotowania danych do analizy;
* Struktura umożliwiająca nawigowanie i zrozumienie danych, czyli Aby znormalizować i contextualize dane.
* Oszczędny magazyn na potrzeby długotrwałego lub nieskończonego przechowywania przetworzonych (lub pochodnych) danych i danych pierwotnych.

Takie dane zapewniają spójne, wszechstronne, aktualne i prawidłowe informacje dotyczące analizy biznesowej i raportowania.

Na poniższej ilustracji przedstawiono typowy przepływ danych IoT.

[![Przepływ danych IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 dla rzeczy przemysłowych

Środowisko IoT jest zróżnicowane dla klientów obejmujących różne segmenty branżowe, takie jak produkcja, samochodowa, energia, narzędzia, inteligentne budynki i doradztwo. W ramach tego szerokiego zakresu przemysłowego IoT, natywne rozwiązania w chmurze, które zapewniają kompleksową analizę na dużą skalę danych IoT, są nadal rozwijane.

Azure Time Series Insights Gen2 na to, że to wymaganie na rynku, zapewniając gotowe, kompleksowe rozwiązanie do analizy IoT z rozbudowanym modelem semantycznym dla contextualization danych szeregów czasowych, szczegółowych informacji opartych na zasobach i najlepszych w swojej klasie środowisku użytkownika do odnajdywania, tworzenia trendów, wykrywania anomalii i analizy operacyjnej.

Rozbudowana platforma analityczna działająca w połączeniu z naszymi interaktywnymi możliwościami eksplorowania danych, można użyć Azure Time Series Insights Gen2, aby uzyskać więcej wartości z danych zebranych z zasobów IoT. Oferta Gen2 obsługuje:

* Wielowarstwowe rozwiązanie magazynu z obsługą wysokiej i zimnej analizy zapewnia klientom możliwość przesyłania danych między ciepłą i zimną analizą interaktywną, a także analizą operacyjną w przypadku danych historycznych.

  * Wysoce interaktywne rozwiązanie do analizy ciepłej do częstego wykonywania i wiele zapytań w ciągu krótszych okresów
  * Skalowalne, wydajne i zoptymalizowane pod kątem kosztów usługi Data Lake w oparciu o usługę Azure Storage, dzięki czemu klienci mogą trendować dane szeregów czasowych w ciągu kilku sekund.

* Obsługa modelu semantycznego opisującego domenę i metadane skojarzone z sygnałami pochodnymi i nieprzetworzonymi z zasobów i urządzeń.

* Elastyczna platforma analityczna umożliwiająca przechowywanie historycznych danych szeregów czasowych na koncie usługi Azure Storage należącym do klienta, dzięki czemu klienci mogą mieć własność swoich danych IoT. Dane są przechowywane w formacie Apache Parquet typu "open source", który umożliwia łączność i współdziałanie w różnych scenariuszach danych, takich jak Analiza predykcyjna, uczenie maszynowe i inne niestandardowe obliczenia wykonywane przy użyciu znanych technologii, takich jak Spark i datakostki.

* Zaawansowana analiza przy użyciu ulepszonych interfejsów API zapytań i środowiska użytkownika, które łączą dane oparte na zasobach dzięki bogatej analizie danych ad hoc z obsługą interpolacji, skalarnych i agregujących funkcji, zmiennych kategorii, wykresów punktowych i przesunięciem czasu szeregów czasowych dla dokładnej analizy.

* Platforma klasy korporacyjnej umożliwiająca obsługę skalowalności, wydajności, zabezpieczeń i niezawodności potrzeb naszych klientów IoT w przedsiębiorstwie.

* Obsługa rozszerzalności i integracji na potrzeby kompleksowej analizy. Azure Time Series Insights Gen2 zapewnia rozszerzalną platformę analityczną dla różnych scenariuszy danych. Łącznik Power BI umożliwia klientom wykonywanie zapytań, które wykonują w Azure Time Series Insights Gen2 bezpośrednio w Power BI, aby uzyskać ujednolicony wgląd w analizy analizy biznesowej i szeregów czasowych w jednym okienku Glass.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu.

  [![Najważniejsze możliwości](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 zapewnia skalowalny model cenowy z płatnością zgodnie z rzeczywistym użyciem na potrzeby przetwarzania danych, magazynowania (danych i metadanych) oraz wykonywania zapytań, dzięki czemu klienci mogą dostosowywać ich użycie do potrzeb firmy.

Wraz z wprowadzeniem tych podstawowych funkcji IoT rzeczy Azure Time Series Insights Gen2 również oferuje następujące kluczowe korzyści:  

| Możliwość | Korzyść |
| ---| ---|
| Magazyn wielowarstwowy dla danych szeregów czasowych w skali IoT | Za pomocą potoku przetwarzania danych udostępnionych do pozyskiwania danych można pozyskać dane zarówno do magazynu ciepłego, jak i chłodni. Korzystaj ze sklepu ciepłego dla interaktywnych zapytań i chłodnego magazynu do przechowywania dużych ilości danych. Aby dowiedzieć się więcej o tym, jak korzystać z wysoce wydajnych zapytań opartych na zasobach, zobacz [zapytania](./concepts-query-overview.md). |
| Model szeregu czasowego na potrzeby określania kontekstu nieprzetworzonych danych telemetrycznych i uzyskiwania szczegółowych danych opartych na zasobach | Możesz użyć modelu szeregów czasowych, aby utworzyć wystąpienia, hierarchie, typy i zmienne dla danych szeregów czasowych. Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [model szeregów czasowych](./concepts-model-overview.md).  |
| Bezproblemowa i ciągła integracja z innymi rozwiązaniami do obsługi danych | Dane znajdujące się w Azure Time Series Insights Gen2 zimny magazyn są [przechowywane](./concepts-storage.md) w plikach programu Apache Parquet Open Source. Pozwala to na integrację danych z innymi rozwiązaniami dotyczącymi danych, 1 lub trzecimi, w przypadku scenariuszy obejmujących analizę biznesową, zaawansowaną Uczenie maszynowe i analizę predykcyjną. |
| Eksploracja danych w czasie niemal rzeczywistym | Środowisko użytkownika programu [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md) zapewnia wizualizację wszystkich danych przesyłanych za pomocą potoku pozyskiwania. Po nawiązaniu połączenia ze źródłem zdarzeń można wyświetlać, eksplorować i wysyłać zapytania dotyczące danych zdarzeń. W ten sposób można sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami. Można również monitorować kondycję, wydajność i ogólną efektywność zasobu IoT. |
| Rozszerzalność i integracja | Integracja łącznika Power BI jest dostępna bezpośrednio w środowisku użytkownika Eksploratora szeregów czasowych za pomocą opcji **eksportu** , umożliwiając klientom eksportowanie zapytań szeregów czasowych tworzonych w środowisku użytkownika bezpośrednio na pulpit Power BI i wyświetlanie ich wykresów szeregów czasowych wraz z inną ANALIZĄ analizy biznesowej. Spowoduje to otwarcie drzwi do nowej klasy scenariuszy dla firmowych przedsiębiorstw IoT, które zainwestowali w Power BI przez zapewnienie pojedynczego okienka Glass przez analizę z różnych źródeł danych, w tym szeregów czasowych IoT. |
| Aplikacje niestandardowe skompilowane na platformie Azure Time Series Insights Gen2 | Azure Time Series Insights Gen2 obsługuje [zestaw JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Ten zestaw SDK zapewnia zaawansowane kontrolki i uproszczony dostęp do zapytań. Użyj zestawu SDK do kompilowania niestandardowych aplikacji IoT na Azure Time Series Insights Gen2, aby odpowiadały potrzebom biznesowym. Możesz również użyć Azure Time Series Insights [interfejsów API zapytań](./concepts-query-overview.md) Gen2 bezpośrednio, aby dyskować dane w niestandardowych aplikacjach IoT. |

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Przewodnik Szybki start](./quickstart-explore-tsi.md)

Dowiedz się więcej na temat przypadków użycia:

> [!div class="nextstepaction"]
> [Azure Time Series Insights przypadki użycia Gen2](./overview-use-cases.md)