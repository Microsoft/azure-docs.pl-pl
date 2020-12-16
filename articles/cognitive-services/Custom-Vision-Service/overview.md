---
title: Co to jest usługa Custom Vision?
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą usługi Azure Custom Vision utworzyć niestandardowe klasyfikatory obrazów AI i wykrywacze obiektów w chmurze platformy Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Rozpoznawanie obrazu, identyfikator obrazu, aplikacja do rozpoznawania obrazów, niestandardowa wizja
ms.openlocfilehash: 7274fed961c4bbac8841e1c741f54031614cea5c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562994"
---
# <a name="what-is-custom-vision"></a>Co to jest usługa Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision to usługa rozpoznawania obrazów, która umożliwia tworzenie, wdrażanie i ulepszanie własnych identyfikatorów obrazów. Identyfikator obrazu stosuje etykiety (reprezentujące klasy lub obiekty) do obrazów, zgodnie z ich cechami wizualnymi. W przeciwieństwie do usługi [Przetwarzanie obrazów](../computer-vision/overview.md) , Custom Vision umożliwia określenie etykiet i uczenie modeli niestandardowych w celu ich wykrycia.

## <a name="what-it-does"></a>Wyniki działania

Usługa Custom Vision używa algorytmu uczenia maszynowego do analizowania obrazów. Ty, deweloper, przesyłaj grupy obrazów, które nie mają cech. Etykiety obrazów należy określić samodzielnie podczas przesyłania. Następnie algorytm pociąga za sobą dane i oblicza własną dokładność przez przetestowanie na tych samych obrazach. Po przeszkoleniu algorytmu można testować, przeszkolić i ostatecznie używać go w aplikacji rozpoznawania obrazu do klasyfikowania nowych obrazów. Możesz także wyeksportować sam model do użytku w trybie offline.

### <a name="classification-and-object-detection"></a>Klasyfikacja i wykrywanie obiektów

Funkcje usługi Custom Vision można podzielić na dwie kategorie. **Klasyfikacja obrazów** stosuje jedną lub więcej etykiet do obrazu. **Wykrywanie obiektów** jest podobne, ale zwraca także współrzędne na obrazie, których dotyczą zastosowane etykiety.

### <a name="optimization"></a>Optymalizacja

Usługa Custom Vision jest zoptymalizowana pod kątem szybkiego rozpoznawania głównych różnic między obrazami, dzięki czemu możesz zacząć prototypować model za pomocą niewielkiej ilości danych. 50 obrazów na etykietę to zazwyczaj dobry początek. Jednak usługa nie jest optymalna do wykrywania delikatnych różnic w obrazach (na przykład w przypadku wykrywania drobnych pęknięć lub zwiększania wcięcia w scenariuszach zapewniania jakości).

Ponadto możesz wybrać spośród kilku różnych typów algorytmu usługi Custom Vision, które są zoptymalizowane pod kątem obrazów z określonymi materiałami &mdash;, na przykład elementami krajobrazu lub produktami sprzedaży detalicznej. Aby uzyskać więcej informacji, zapoznaj się z przewodnikiem [Kompilowanie klasyfikatora](getting-started-build-a-classifier.md) lub [Tworzenie prowadnic detektora obiektów](get-started-build-detector.md) .

## <a name="what-it-includes"></a>Co zawiera

Custom Vision Service jest dostępny jako zestaw natywnych zestawów SDK, a także za pośrednictwem interfejsu opartego na sieci Web w [witrynie sieci web Custom Vision](https://customvision.ai/). Możesz tworzyć, testować i trenować model za pośrednictwem jednego z tych dwóch interfejsów lub obu jednocześnie.

![Custom Vision witrynę sieci Web w oknie przeglądarki Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Custom Vision powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem dotyczącym [kompilowania klasyfikatora](getting-started-build-a-classifier.md) , aby rozpocząć korzystanie z Custom Vision w portalu internetowym, lub wykonaj instrukcje przewodnika [Szybki Start dla biblioteki klienta](quickstarts/image-classification.md) , aby zaimplementować podstawowe scenariusze w kodzie.