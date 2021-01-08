---
title: Wybieranie rozwiązania do przetwarzania w czasie rzeczywistym i przesyłania strumieniowego na platformie Azure
description: Dowiedz się, jak wybrać odpowiednią technologię analizy i przetwarzania strumieniowego w czasie rzeczywistym w celu skompilowania aplikacji na platformie Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019061"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Wybierz technologię analizy i przetwarzania strumieniowego w czasie rzeczywistym na platformie Azure

Istnieje kilka usług dostępnych do analizy w czasie rzeczywistym i przetwarzania strumieniowego na platformie Azure. Ten artykuł zawiera informacje, które należy podjąć, aby zdecydować, która technologia najlepiej pasuje do Twojej aplikacji.

## <a name="when-to-use-azure-stream-analytics"></a>Kiedy używać Azure Stream Analytics

Azure Stream Analytics to zalecana usługa dla usługi Stream Analytics na platformie Azure. Jest to przeznaczone dla szerokiego zakresu scenariuszy, które obejmują, ale nie są ograniczone do:

* Pulpity nawigacyjne do wizualizacji danych
* [Alerty](stream-analytics-set-up-alerts.md) w czasie rzeczywistym z wzorców danych czasowych i przestrzennych lub anomalii
* Ekstrakcja, transformacja, ładowanie (ETL)
* [Wzorzec określania źródła zdarzeń](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Dodanie Azure Stream Analytics zadania do aplikacji jest najszybszym sposobem na rozpoczęcie analizy strumieniowej i uruchamiania jej na platformie Azure przy użyciu języka SQL, który już znasz. Azure Stream Analytics to usługa zadań, dlatego nie trzeba poświęcać czasu na zarządzanie klastrami i nie trzeba martwić się o przestoje z 99,9% umowy SLA na poziomie zadania. Rozliczanie odbywa się również na poziomie zadania, co zmniejsza koszty uruchamiania (jedna Jednostka przesyłania strumieniowego), ale skalowalne (do 192 jednostek przesyłania strumieniowego). Jest to znacznie tańsze, aby uruchamiać kilka Stream Analyticsych zadań niż w przypadku uruchamiania i konserwowania klastra.

Azure Stream Analytics ma wbudowane środowisko pracy. Można od razu korzystać z następujących funkcji bez żadnej dodatkowej konfiguracji:

* Wbudowane operatory danych czasowych, takie jak [agregacje okienkowe](stream-analytics-window-functions.md), sprzężenia czasowe i funkcje analityczne danych czasowych.
* Natywne karty [danych wejściowych](stream-analytics-add-inputs.md) i [wyjściowych](stream-analytics-define-outputs.md) platformy Azure
* Obsługa wolnych zmian [danych referencyjnych](stream-analytics-use-reference-data.md) (nazywanych również tabelami odnośników), w tym dołączanie do danych referencyjnych geoprzestrzennych.
* Zintegrowane rozwiązania, takie jak [wykrywanie anomalii](stream-analytics-machine-learning-anomaly-detection.md)
* Wiele okien czasu w tym samym zapytaniu
* Możliwość redagowania wielu operatorów czasowych w dowolnej kolejności.
* W obszarze 100-ms-end-to-end opóźnienia od wejścia do Event Hubs, do danych wyjściowych w Event Hubs, w tym opóźnienia sieci z i do Event Hubs, z trwałą wysoką przepustowością

## <a name="when-to-use-other-technologies"></a>Kiedy używać innych technologii

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Chcesz napisać UDF, uda i niestandardowe deserializacji w języku innym niż JavaScript lub C #

Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika (UDF) lub zagregowane wartości zdefiniowane przez użytkownika (UDA) w języku JavaScript dla zadań w chmurze i języka C# dla IoT Edge zadań. W języku C# są również obsługiwane deserializacji zdefiniowane przez użytkownika. Jeśli chcesz zaimplementować Deserializator, UDF lub UDA w innych językach, takich jak Java lub Python, możesz użyć przesyłania strumieniowego platformy Spark. Możesz również uruchomić Event Hubs **klasy eventprocessorhost** na własnych maszynach wirtualnych, aby wykonać dowolne przetwarzanie przesyłania strumieniowego.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Twoje rozwiązanie jest w środowisku wielochmurowym lub lokalnym

Azure Stream Analytics jest technologią firmową firmy Microsoft i jest dostępna tylko na platformie Azure. Jeśli potrzebujesz rozwiązania przenośnego między chmurami lub lokalnymi, rozważ użycie technologii typu open source, takich jak platforma Spark lub burza.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zadania Stream Analytics przy użyciu Azure Portal](stream-analytics-quick-create-portal.md)
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Tworzenie zadania Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Tworzenie zadania Stream Analytics przy użyciu Visual Studio Code](quick-create-visual-studio-code.md)
