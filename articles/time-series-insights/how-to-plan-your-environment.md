---
title: Zaplanuj środowisko Gen2 — Azure Time Series Insights | Microsoft Docs
description: Najlepsze rozwiązania dotyczące konfigurowania, planowania i wdrażania środowiska Azure Time Series Insights Gen2 oraz zarządzania nim.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016789"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planowanie środowiska Azure Time Series Insights Gen2

W tym artykule opisano najlepsze rozwiązania dotyczące szybkiego planowania i rozpoczynania pracy przy użyciu Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Najlepsze rozwiązania dotyczące planowania i przygotowywania

Najlepsze rozwiązania związane z planowaniem i przygotowywaniem środowiska są opisane w następujących artykułach:

* Co otrzymujesz w przypadku aprowizacji [środowiska Azure Time Series Insights Gen2](#the-gen2-environment).
* Jakie [są identyfikatory szeregów czasowych i właściwości sygnatur czasowych](#configure-time-series-ids-and-timestamp-properties).
* Nowy [model szeregów czasowych](#understand-the-time-series-model)i sposób tworzenia własnych.
* Jak [efektywnie wysyłać zdarzenia w formacie JSON](#shape-your-events).
* Azure Time Series Insights [Opcje odzyskiwania po awarii firmy](#business-disaster-recovery).

Azure Time Series Insights wykorzystuje model biznesowy z płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat opłat i pojemności, Przeczytaj [Azure Time Series Insights Cennik](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>Środowisko Gen2

Po udostępnieniu środowiska Azure Time Series Insights Gen2 należy utworzyć dwa zasoby platformy Azure:

* Azure Time Series Insights środowisko Gen2
* Konto usługi Azure Storage

W ramach procesu inicjowania obsługi administracyjnej należy określić, czy ma zostać włączona obsługa magazynu w postaci ciepłej. Magazyn ciepły zapewnia obsługę zapytań warstwowych. Po włączeniu należy określić okres przechowywania w zakresie od 7 do 30 dni. Zapytania wykonywane w ramach okresu przechowywania ciepłego magazynu zwykle zapewniają krótszy czas odpowiedzi. Gdy zapytanie obejmuje okres przechowywania w sieci ciepłej, jest ono obsługiwane w chłodnym magazynie.

Zapytania dotyczące sklepu w sklepie są bezpłatne, podczas gdy zapytania dotyczące magazynu zimnego są ponoszone. Ważne jest, aby zrozumieć wzorce zapytań i odpowiednio zaplanować konfigurację magazynu ciepłego. Zalecamy, aby interakcyjna analiza najnowszych danych znajdował się w magazynie ciepłym i analizie wzorców oraz długoterminowe trendy znajdowały się na zimno.

> [!NOTE]
> Aby dowiedzieć się więcej na temat wykonywania zapytań dotyczących danych ciepłych, Przeczytaj [Informacje o interfejsie API](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Aby rozpocząć, potrzebne są trzy dodatkowe elementy:

* [Model szeregów czasowych](./concepts-model-overview.md)
* [Źródło zdarzenia połączone z Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Zdarzenia przepływające do źródła zdarzeń](./time-series-insights-send-events.md) , które są zamapowane na model i są w prawidłowym formacie JSON

## <a name="review-azure-time-series-insights-gen2-limits"></a>Przegląd Azure Time Series Insights limitów Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurowanie identyfikatorów szeregów czasowych i właściwości sygnatur czasowych

Aby utworzyć nowe środowisko Azure Time Series Insights, wybierz identyfikator szeregów czasowych. Wykonanie tej czynności jako partycji logicznej dla danych. Upewnij się, że identyfikatory szeregów czasowych są gotowe.

> [!IMPORTANT]
> Identyfikatorów szeregów czasowych *nie można zmienić później*. Sprawdź każdy z nich przed ostatecznym zaznaczeniem i pierwszym użyciem.

Można wybrać maksymalnie trzy klucze, aby jednoznacznie odróżnić zasoby. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania identyfikatora szeregów czasowych](./how-to-select-tsid.md) i [reguł](concepts-json-flattening-escaping-rules.md)pozyskiwania.

Właściwość **sygnatury czasowej** jest również ważna. Tę właściwość można wyznaczyć podczas dodawania źródeł zdarzeń. Każde źródło zdarzenia ma opcjonalną właściwość sygnatury czasowej, która jest używana do śledzenia źródeł zdarzeń w czasie. W wartościach sygnatury czasowej jest uwzględniana wielkość liter i muszą one być sformatowane do poszczególnych specyfikacji każdego źródła zdarzenia.

Gdy pole pozostanie puste, czas, w którym zdarzenie zostało w kolejce do IoT Hub lub centrum zdarzeń, jest używany jako sygnatura czasowa zdarzenia. Ogólnie rzecz biorąc, użytkownicy powinni dostosowywać Właściwość sygnatur czasowych i korzystać z czasu, gdy czujnik lub tag wygenerował odczyt, a nie czas podłączany do centrum. Aby uzyskać więcej informacji i zapoznać się z przesunięciami strefy czasowej odczytywanie [sygnatury czasowej źródła zdarzenia](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp).

## <a name="understand-the-time-series-model"></a>Omówienie modelu szeregów czasowych

Teraz można skonfigurować model szeregów czasowych środowiska Azure Time Series Insights. Nowy model ułatwia znajdowanie i analizowanie danych IoT. Umożliwia ona nadzorowanie, konserwację i wzbogacanie danych szeregów czasowych i pomaga przygotować zestawy danych gotowe do użycia przez klientów. Model używa identyfikatorów szeregów czasowych, które mapują do wystąpienia, które kojarzy unikatowy zasób ze zmiennymi, znanymi jako typy i hierarchie. Przeczytaj temat Omówienie [modelu szeregów czasowych](./concepts-model-overview.md) , aby dowiedzieć się więcej.

Model jest dynamiczny, więc można go skompilować w dowolnym momencie. Aby szybko rozpocząć pracę, skompiluj ją i przekaż przed wypchnięciem danych do Azure Time Series Insights. Aby skompilować model, przeczytaj artykuł [Korzystanie z modelu szeregów czasowych](./concepts-model-overview.md).

W przypadku wielu klientów model szeregów czasowych jest mapowany na istniejący model zasobów lub system ERP. Jeśli nie masz istniejącego modelu, [zapewnione](https://github.com/Microsoft/tsiclient) zostanie wbudowane środowisko użytkownika umożliwiające szybkie rozpoczęcie pracy. Aby Envision, jak może pomóc model, zobacz [przykładowe środowisko demonstracyjne](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Kształtowanie zdarzeń

Możesz sprawdzić sposób wysyłania zdarzeń do Azure Time Series Insights. W idealnym przypadku zdarzenia są bardziej znormalizowane i wydajne.

Dobrym regułą dla elementu kciuk:

* Przechowuj metadane w modelu szeregów czasowych.
* Upewnij się, że tryb szeregów czasowych, pola wystąpień i zdarzenia zawierają tylko niezbędne informacje, takie jak identyfikator szeregów czasowych lub właściwość sygnatury czasowej.

Aby uzyskać więcej informacji i zrozumieć, jak zdarzenia mają być spłaszczone i przechowywane, Przeczytaj [reguły spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [Azure Advisor](../advisor/advisor-overview.md) , aby zaplanować opcje konfiguracji odzyskiwania firmy.
* Przejrzyj [Azure Advisor](../advisor/advisor-overview.md) , aby zaplanować opcje konfiguracji odzyskiwania firmy.
* Przeczytaj więcej na temat pozyskiwania [danych](./concepts-ingestion-overview.md) w Azure Time Series Insights Gen2.
* Zapoznaj się z artykułem dotyczącym [magazynu danych](./concepts-storage.md) w Azure Time Series Insights Gen2.
* Dowiedz się więcej na temat [modelowania danych](./concepts-model-overview.md) w Azure Time Series Insights Gen2.