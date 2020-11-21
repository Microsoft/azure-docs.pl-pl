---
title: 'Przegląd: co to jest Azure Time Series Insights? -Azure Time Series Insights | Microsoft Docs'
description: Wprowadzenie do usługi Azure Time Series Insights — nowej usługi do analizy danych szeregów czasowych i obsługi rozwiązań IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: overview
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: cebe0e018b4483a9de1771fb3171effbbd5ca59c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020065"
---
# <a name="what-is-azure-time-series-insights-gen1"></a>Co to jest Azure Time Series Insights Gen1?

> [!CAUTION]
> To jest artykuł Gen1.

Azure Time Series Insights jest zbudowana do przechowywania, wizualizowania i wykonywania zapytań dotyczących dużych ilości danych szeregów czasowych, takich jak te wygenerowane przez urządzenia IoT. Jeśli chcesz przechowywać dane szeregów czasowych, zarządzać nimi i wizualizować je w chmurze, Azure Time Series Insights prawdopodobnie są dla Ciebie odpowiednie.

[![Schemat blokowy Azure Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Azure Time Series Insights ma cztery kluczowe zadania:

- Jest ona w pełni zintegrowana z bramami w chmurze, takimi jak Azure IoT Hub i Azure Event Hubs. Łatwo łączy się z takimi źródłami zdarzeń i analizuje kod JSON z komunikatów i struktur z danymi w postaci czytelnych wierszy i kolumn. Łączy metadane z telemetrią i indeksuje dane w magazynie kolumnowym.
- Azure Time Series Insights zarządza magazynem danych. Aby mieć pewność, że dane są zawsze łatwo dostępne, przechowuje dane w pamięci i dysków SSD przez maksymalnie 400 dni. Możesz interaktywnie wysyłać zapytania o miliardy zdarzeń w ciągu kilku sekund — na żądanie.
- Azure Time Series Insights zapewnia wbudowaną wizualizację za pomocą Eksploratora Azure Time Series Insights.
- Azure Time Series Insights udostępnia usługę zapytań, zarówno w Eksploratorze Azure Time Series Insights, jak i za pomocą interfejsów API, które można łatwo zintegrować, aby osadzić dane szeregów czasowych w aplikacjach niestandardowych.

W przypadku tworzenia aplikacji do użytku wewnętrznego lub dla klientów zewnętrznych można użyć Azure Time Series Insights jako zaplecza. Można jej użyć do indeksowania, przechowywania i agregowania danych szeregów czasowych. Aby utworzyć wizualizację niestandardową i środowisko użytkownika na górze, użyj [zestawu SDK klienta](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Azure Time Series Insights jest również wyposażony w kilka [interfejsów API zapytań](./concepts-json-flattening-escaping-rules.md) , aby umożliwić te niestandardowe scenariusze.

Dane szeregów czasowych przedstawiają, jak zasób lub proces zmienia się wraz z upływem czasu. Dane szeregów czasowych są indeksowane przez sygnatury czasowe, a czas jest najbardziej znaczącą osią, na której zorganizowane są takie dane. Dane szeregów czasowych są zwykle docierane w kolejności sekwencyjnej, więc jest traktowana jako INSERT, a nie aktualizacja bazy danych.

Może być wyzwaniem do przechowywania, indeksowania, wykonywania zapytań, analizowania i wizualizowania danych szeregów czasowych w dużych woluminach.
Azure Time Series Insights przechwytuje i zapisuje każde nowe zdarzenie jako wiersz, a zmiana jest efektywnie mierzona w czasie. W związku z tym możesz odszukać szczegółowe informacje od przeszłości, aby ułatwić prognozowanie przyszłych zmian.

## <a name="video"></a>Wideo

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Dowiedz się więcej o Azure Time Series Insights, opartej na chmurze platformie IoT Analytics.</br>

[![PLIKÓW](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Podstawowe scenariusze

- Przechowuj dane szeregów czasowych w sposób skalowalny.

   Na swoim rdzeńu Azure Time Series Insights ma bazę danych zaprojektowaną z uwzględnieniem danych szeregów czasowych. Ponieważ jest skalowalna i w pełni zarządzana, Azure Time Series Insights obsługuje zadania przechowywania i zarządzania zdarzeniami.

- Eksploruj dane niemal w czasie rzeczywistym.

   Azure Time Series Insights udostępnia Eksploratora, który wizualizuje wszystkie dane przesyłane strumieniowo do środowiska. Wkrótce po nawiązaniu połączenia ze źródłem zdarzeń można wyświetlać, eksplorować i badać dane zdarzeń w Azure Time Series Insights. Dane pomagają sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami, oraz aby monitorować zasoby IoT pod kątem kondycji, produktywności i ogólnej skuteczności.

- Wykonaj analizę głównych przyczyn i Wykryj anomalie.

   Azure Time Series Insights zawiera narzędzia, takie jak wzorce i widoki perspektywy, do przeprowadzania i zapisywania analizy głównej przyczyny wieloetapowej. Azure Time Series Insights działa również z usługami alertów, takimi jak Azure Stream Analytics, aby można było wyświetlać alerty i wykryte anomalie niemal w czasie rzeczywistym w Eksploratorze Azure Time Series Insights.

- Uzyskaj globalny wgląd w dane szeregów czasowych, które przesyła strumieniowo z różnych lokalizacji na potrzeby porównania wielu zasobów lub lokacji.

   Można połączyć wiele źródeł zdarzeń ze środowiskiem Azure Time Series Insights. W ten sposób można wyświetlać dane przesyłane strumieniowo z wielu różnych lokalizacji w czasie niemal rzeczywistym. Użytkownicy mogą korzystać z tej widoczności, aby współużytkować dane ze liderami biznesowymi. Mogą współpracować z ekspertami domeny, którzy mogą stosować swoje doświadczenia, aby pomóc w rozwiązywaniu problemów, stosowaniu najlepszych rozwiązań i udostępnianiu informacji.

- Kompiluj aplikację klienta na Azure Time Series Insights.

   Azure Time Series Insights uwidacznia interfejsy API zapytania REST, których można użyć do kompilowania aplikacji korzystających z danych szeregów czasowych.

## <a name="capabilities"></a>Możliwości

- **Szybko Rozpocznij pracę**: Azure Time Series Insights nie wymaga przygotowania do przedniego danych, dzięki czemu możesz szybko połączyć się z milionami zdarzeń w centrum IoT Hub lub centrum zdarzeń. Po nawiązaniu połączenia możesz wizualizować dane czujników i korzystać z nich, aby szybko weryfikować Twoje rozwiązania IoT. Możesz korzystać z danych bez pisania kodu i nie musisz uczyć się nowego języka. Azure Time Series Insights udostępnia szczegółową, niezależną powierzchnię zapytania dla zaawansowanych użytkowników, a także Eksplorowanie punktów i klikanie.

- **Szczegółowe informacje** w czasie rzeczywistym: Azure Time Series Insights mogą pozyskiwanie milionów zdarzeń czujników dziennie, z opóźnieniem jednej minuty. Azure Time Series Insights pomaga uzyskać wgląd w dane czujników. Korzystaj z nich, aby wypróbować trendy i anomalie, przeprowadzać analizy głównych przyczyn i uniknąć kosztownych przestojów. Wzajemna korelacja między danymi w czasie rzeczywistym i historycznymi ułatwia znalezienie ukrytych trendów w danych.

- **Tworzenie niestandardowych rozwiązań**: Osadź Azure Time Series Insights dane w istniejących aplikacjach. Można również tworzyć nowe niestandardowe rozwiązania przy użyciu Azure Time Series Insights interfejsów API REST. Utwórz widoki spersonalizowane, które można udostępniać innym, aby umożliwić im eksplorowanie Twoich szczegółowych danych.

- **Skalowalność**: Azure Time Series Insights jest zaprojektowana do obsługi IoT na dużą skalę. Umożliwia pozyskiwanie od 1 miliona do 100 milionów zdarzeń dziennie, które domyślnie są przechowywane przez 31 dni. Możesz wizualizować i analizować strumienie danych na żywo niemal w czasie rzeczywistym, obok danych historycznych.

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć, wykonaj następujące kroki.

1. Zainicjuj obsługę środowiska Azure Time Series Insights w Azure Portal.
1. Nawiąż połączenie ze źródłem zdarzenia, takim jak IoT Hub lub centrum zdarzeń.
1. Przekaż dane referencyjne. To nie jest dodatkowa usługa.
1. Przejrzyj i Wyświetl dane w kilka minut za pomocą Eksploratora Azure Time Series Insights.

## <a name="explorer"></a>Eksplorator

Wyświetlaj, analizuj i odnajduj trendy w danych przy użyciu Eksploratora Azure Time Series Insights.

![Eksplorator Azure Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Dowiedz się, jak używać [eksploratora Azure Time Series Insights](time-series-insights-explorer.md) i rysować szczegółowe dane na podstawie danych.

## <a name="next-steps"></a>Następne kroki

- Eksploruj Azure Time Series Insights [środowiska demonstracyjnego](./time-series-quickstart.md)Gen1.

- Dowiedz się więcej o [tym, jak planować środowisko Azure Time Series Insights](time-series-insights-environment-planning.md) .