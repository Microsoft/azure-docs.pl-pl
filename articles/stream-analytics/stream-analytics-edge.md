---
title: Azure Stream Analytics na urządzeniach IoT Edge
description: Twórz zadania brzegowe w Azure Stream Analytics i wdrażaj je na urządzeniach z Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 65f1ad93bf711f7f7efe95c38619390dde527dd0
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827244"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na urządzeniach IoT Edge
 
Azure Stream Analytics na urządzeniach IoT Edge umożliwia deweloperom wdrażanie analitycznej analizy w czasie rzeczywistym bliżej urządzeń IoT, dzięki czemu mogą odblokować pełną wartość danych generowanych przez urządzenia. Usługę Azure Stream Analytics zaprojektowano pod kątem małych opóźnień, odporności, wydajnego wykorzystywania przepustowości i zgodności. Przedsiębiorstwa mogą wdrażać logikę kontroli blisko operacji przemysłowych i uzupełniać analizę danych Big Data wykonywanej w chmurze.

Azure Stream Analytics na urządzeniach IoT Edge działa w ramach struktury [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) . Po utworzeniu zadania w Stream Analytics można wdrożyć go i zarządzać nim przy użyciu IoT Hub.

## <a name="common-scenarios"></a>Typowe scenariusze

W tej sekcji opisano typowe scenariusze Stream Analytics w IoT Edge. Na poniższym diagramie przedstawiono przepływ danych między urządzeniami IoT i chmurą platformy Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagram wysokiego poziomu IoT Edge":::

### <a name="low-latency-command-and-control"></a>Polecenie i kontrola o małym opóźnieniu

Systemy bezpieczeństwa produkcji muszą reagować na dane operacyjne przy bardzo małych opóźnieniach. Za pomocą Stream Analytics na IoT Edge można analizować dane czujników niemal w czasie rzeczywistym i wydawać polecenia w przypadku wykrycia anomalii w celu zatrzymania alertów komputera lub wyzwalacza.

### <a name="limited-connectivity-to-the-cloud"></a>Ograniczona łączność z chmurą

Systemy o znaczeniu krytycznym, takie jak zdalny sprzęt górniczy, połączone statki lub przechodzenie na przybrzeżne, muszą analizować i reagować na dane nawet wtedy, gdy łączność z chmurą jest nieprzerwana. Dzięki Stream Analytics logika przesyłania strumieniowego działa niezależnie od łączności sieciowej i można wybrać operacje wysyłane do chmury w celu dalszej obróbki lub magazynu.

### <a name="limited-bandwidth"></a>Ograniczona przepustowość

Ilość danych wytworzonych przez aparaty Jet lub połączone samochody mogą być tak duże, że dane muszą być przefiltrowane lub wstępnie przetworzone przed wysłaniem ich do chmury. Za pomocą Stream Analytics można filtrować lub agregować dane, które muszą być wysyłane do chmury.

### <a name="compliance"></a>Zgodność

Zgodność z przepisami może wymagać, aby pewne dane były lokalnie anonimowe lub zagregowane przed wysłaniem ich do chmury.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Zadania brzegowe w Azure Stream Analytics

Zadania brzegowe Stream Analytics uruchamiane w kontenerach wdrożonych na [urządzeniach Azure IoT Edge](../iot-edge/about-iot-edge.md). Zadania brzegowe składają się z dwóch części:

* Część w chmurze, która jest odpowiedzialna za definicję zadania: Użytkownicy definiują dane wejściowe, wyjściowe, zapytania i inne ustawienia, takie jak zdarzenia poza kolejnością, w chmurze.

* Moduł uruchomiony na urządzeniach IoT. Moduł zawiera aparat Stream Analytics i odbierze definicję zadania z chmury. 

Stream Analytics używa IoT Hub do wdrażania zadań brzegowych na urządzeniach. Aby uzyskać więcej informacji, zobacz [IoT Edge Deployment](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Zadanie Azure Stream Analytics Edge":::

## <a name="edge-job-limitations"></a>Ograniczenia zadania brzegowego

Celem jest zaplanowanie między zadaniami IoT Edge i zadaniami w chmurze. Większość funkcji języka zapytań SQL jest obsługiwanych zarówno w przypadku krawędzi, jak i chmury. Jednak następujące funkcje nie są obsługiwane w przypadku zadań brzegowych:
* Funkcje zdefiniowane przez użytkownika (UDF) w języku JavaScript. System UDF jest dostępny w [języku C# dla zadań IoT Edge](./stream-analytics-edge-csharp-udf.md) (wersja zapoznawcza).
* Agregacje zdefiniowane przez użytkownika (UDA).
* Funkcje usługi Azure ML.
* Format AVRO dla operacji wejścia/wyjścia. W tej chwili obsługiwane są tylko woluminy CSV i JSON.
* Następujące operatory SQL:
    * PODZIEL NA PARTYCJE
    * GetMetadataPropertyValue
* Zasady późnego przybycia

### <a name="runtime-and-hardware-requirements"></a>Wymagania dotyczące środowiska uruchomieniowego i sprzętu
Aby uruchamiać Stream Analytics na IoT Edge, potrzebne są urządzenia, które mogą uruchamiać [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics i Azure IoT Edge używają kontenerów **platformy Docker** , aby zapewnić przenośne rozwiązanie działające w wielu systemach operacyjnych hosta (Windows, Linux).

Stream Analytics na IoT Edge są udostępniane jako obrazy systemu Windows i Linux, uruchomione na architekturze x86-64 lub ARM (Advanced RISC Machines). 


## <a name="input-and-output"></a>Dane wejściowe i wyjściowe

Stream Analytics zadania brzegowe mogą pobierać dane wejściowe i wyjściowe z innych modułów uruchomionych na IoT Edge urządzeniach. Aby nawiązać połączenie z i do określonych modułów, można ustawić konfigurację routingu w czasie wdrażania. Więcej informacji znajduje się w [dokumentacji dotyczącej kompozycji modułu IoT Edge](../iot-edge/module-composition.md).

W przypadku danych wejściowych i wyjściowych obsługiwane są formaty CSV i JSON.

Dla każdego strumienia danych wejściowych i wyjściowych utworzonego w ramach zadania Stream Analytics zostanie utworzony odpowiedni punkt końcowy dla wdrożonego modułu. Te punkty końcowe mogą być używane w trasach wdrożenia.

Obsługiwane typy wejściowe strumienia to:
* Centrum brzegowe
* Centrum zdarzeń
* Usługa IoT Hub

Obsługiwane typy danych wyjściowych strumienia to:
* Centrum brzegowe
* Baza danych SQL
* Centrum zdarzeń
* Blob Storage/ADLS Gen2

Wejście odwołania obsługuje typ pliku referencyjnego. Inne dane wyjściowe można osiągnąć przy użyciu zadania w chmurze podrzędnego. Na przykład zadanie Stream Analytics hostowane w programie Edge wysyła dane wyjściowe do centrum brzegowego, a następnie wysyła dane wyjściowe do IoT Hub. Możesz użyć drugiego zadania Azure Stream Analytics hostowanego w chmurze z danymi wejściowymi z IoT Hub i danych wyjściowych do Power BI lub innego typu wyjściowego.

## <a name="license-and-third-party-notices"></a>Licencja i uwagi innych firm
* [Azure Stream Analytics na urządzeniach IoT Edge licencji](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Powiadomienie innych firm dotyczące Azure Stream Analytics na urządzeniach IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informacje o obrazie modułu Azure Stream Analytics 

Informacje o tej wersji zostały ostatnio zaktualizowane w dniu 2020-09-21:

- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platformach
      - Architektura: amd64
      - system operacyjny: Linux
 
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platformach
      - Architektura: ARM
      - system operacyjny: Linux
 
- Obraz: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - obraz podstawowy: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platformach
      - Architektura: arm64
      - system operacyjny: Linux
      
      
## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, wypróbuj&stronie pytań i odpowiedzi [dla Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Więcej informacji na temat Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Stream Analytics w samouczku IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Opracowywanie zadań Stream Analytics Edge przy użyciu narzędzi Visual Studio Tools](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementowanie ciągłej integracji/ciągłego dostarczania Stream Analytics przy użyciu interfejsów API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
