---
title: Modele AI usługi Azure Percept
description: Dowiedz się więcej o modelach AI dostępnych do tworzenia prototypów i wdrażania
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: e31a696dfb443c20566f13f5a228eefac7af5ecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595927"
---
# <a name="azure-percept-ai-models"></a>Modele AI usługi Azure Percept

Usługa Azure Percept umożliwia tworzenie i wdrażanie modeli AI bezpośrednio na platformie Azure Percept DK z [platformy Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Wdrożenie modelu wykorzystuje [IoT Hub platformy Azure](https://azure.microsoft.com/services/iot-hub/) i [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Przykładowe modele AI

Usługa Azure Percept Studio zawiera przykładowe modele dla następujących aplikacji:

- Wykrywanie osób
- wykrywanie pojazdu
- ogólne wykrywanie obiektów
- Produkty — wykrywanie na półce

W przypadku modeli wstępnie przeszkolonych nie jest wymagane tworzenie kodu ani zbieranie danych szkoleniowych. Po prostu Wdróż żądany model na platformie Azure Percept DK z portalu i Otwórz strumień wideo Devkit, aby zobaczyć model inferencing w działaniu. Dostęp do telemetrii inferencing modelu można także uzyskać za pomocą narzędzia [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="reference-solutions"></a>Rozwiązania referencyjne

Dostępne są również [rozwiązania do zliczania użytkowników](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) . To rozwiązanie referencyjne jest aplikacją AI Open Source, która zapewnia programom opartym na krawędziach zliczanie z użyciem zdarzeń wejścia/wyjścia strefy zdefiniowanej przez użytkownika. Dane wyjściowe wideo i AI z lokalnego urządzenia brzegowego są egressed do [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/)przy użyciu interfejsu użytkownika działającego jako witryna sieci Web platformy Azure. Inferencing AI jest dostarczany przez model AI open source do wykrywania osób.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Wstępnie skompilowane rozwiązanie do analizy przestrzennej.":::

## <a name="custom-no-code-solutions"></a>Niestandardowe rozwiązania bez kodu

Za pomocą usługi Azure Percept Studio można opracowywać niestandardowe rozwiązania do [obsługi](./tutorial-nocode-vision.md) i mowy, bez konieczności kodowania.

W przypadku rozwiązań niestandardowych można korzystać zarówno z wykrywania obiektów, jak i klasyfikacji modeli AI. Po prostu Przekaż i Oznacz obrazy szkoleniowe, które można wykonać bezpośrednio z modelem "Percept" platformy Azure, w razie potrzeby. W [Custom Vision](https://www.customvision.ai/), które są częścią [Cognitive Services platformy Azure](https://azure.microsoft.com/services/cognitive-services/#overview), można łatwo przeprowadzić szkolenie i ocenę modeli.

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

W przypadku niestandardowych rozwiązań mowy szablony asystenta głosowego są obecnie dostępne dla następujących aplikacji:

- Hotelarstwo: pomieszczenie hotelowe wyposażone w urządzenia inteligentne sterowane głosowo.
- Opieka zdrowotna: placówka opiekowa wyposażona w urządzenia inteligentne sterowane głosowo.
- Spis: centrum spisu wyposażone w urządzenia inteligentne sterowane głosem.
- Motoryzacyjne: centrum motoryzacyjne wyposażone w urządzenia inteligentne sterowane głosem.

Wstępnie skompilowane słowa kluczowe i polecenia asystenta głosowego są dostępne bezpośrednio w portalu. Niestandardowe słowa kluczowe i polecenia można tworzyć i przeszkolone w programie [Speech Studio](https://speech.microsoft.com/), który jest również częścią usługi Azure Cognitive Services.

## <a name="advanced-development"></a>Opracowywanie zaawansowane

Odwiedź witrynę [Azure PERCEPT DK Advanced Development](https://github.com/microsoft/azure-percept-advanced-development) w serwisie GitHub, aby uzyskać aktualne wskazówki, samouczki i przykłady dla następujących elementów:

* Przełączanie niestandardowego modelu AI na urządzenie
* Aktualizowanie modelu, który jest już obsługiwany przez uczenie transferu
* I inne informacje
