---
title: Interfejs API REST — Szybki Start
description: Korzystaj z interfejsu API REST twarzy z zwinięciem, aby wykrywać twarze, znaleźć podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: be942f73ee0a3d5a8850141c937754bad330db90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912184"
---
Wprowadzenie do rozpoznawania twarzy przy użyciu interfejsu API REST twarzy. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj interfejsu API REST do:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)

> [!NOTE]
> Ten przewodnik Szybki Start używa poleceń zawieszania do wywołania interfejsu API REST. Interfejs API REST można również wywołać przy użyciu języka programowania. Zobacz przykłady w witrynie GitHub, aby zapoznać się z przykładami w [językach C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)i [go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.


## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Użyjesz polecenia, takiego jak następujące, aby wywołać interfejs API rozpoznawania twarzy i pobrać dane atrybutów kroju z obrazu. Najpierw skopiuj kod do edytora tekstów &mdash; konieczne będzie wprowadzenie zmian w niektórych częściach polecenia przed jego uruchomieniem.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Wprowadź następujące zmiany:
1. Przypisz `Ocp-Apim-Subscription-Key` do poprawnego klucza subskrypcji czołowej.
1. Zmień pierwszą część adresu URL zapytania, aby pasowała do punktu końcowego, który odpowiada kluczowi subskrypcji.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Opcjonalnie można zmienić adres URL w treści żądania, aby wskazać inny obraz.

Po dokonaniu zmian otwórz wiersz polecenia i wprowadź nowe polecenie. 

### <a name="examine-the-results"></a>Sprawdzanie wyników

Informacje o twarzy powinny zostać wyświetlone jako dane JSON w oknie konsoli. Na przykład:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Pobieranie atrybutów kroju
 
Aby wyodrębnić atrybuty kroju, Wywołaj interfejs API wykrywania ponownie, ale ustaw wartość `detectionModel` `detection_01` . Dodaj `returnFaceAttributes` również parametr zapytania. Polecenie powinno teraz wyglądać podobnie do poniższego. Tak jak wcześniej, Wstaw klucz subskrypcji i punkt końcowy Twojej usługi.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Sprawdzanie wyników

Zwrócone informacje o przedstronie są teraz uwzględnione w atrybutach czcionki. Na przykład:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Ta operacja wymaga pojedynczej wykrytej twarzy (źródła) i wyszukuje w zestawie inne twarze (element docelowy), aby znaleźć dopasowania (Wyszukiwanie twarzy według obrazu). Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw należy wykryć twarze w obrazach, zanim będzie można je porównać. Uruchom to polecenie, jak w sekcji [Wykryj twarze](#detect-faces-in-an-image) . Ta metoda wykrywania jest zoptymalizowana pod kątem operacji porównania. Nie wyodrębnimy szczegółowych atrybutów kroju, takich jak w powyższej sekcji, i używa innego modelu wykrywania.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Znajdź `"faceId"` wartość w odpowiedzi JSON i Zapisz ją w tymczasowej lokalizacji. Następnie ponownie wywołaj powyższe polecenie dla innych adresów URL obrazów i Zapisz ich identyfikatory. Te identyfikatory będą używane jako Grupa docelowa powierzchni, z których można znaleźć podobną twarz.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Na koniec Wykryj funkcję pojedynczego źródła, która będzie używana do dopasowywania i Zapisz jego identyfikator. Zachowaj ten identyfikator niezależnie od innych.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Znajdź dopasowania

Skopiuj następujące polecenie do edytora tekstu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Następnie wprowadź następujące zmiany:
1. Przypisz `Ocp-Apim-Subscription-Key` do poprawnego klucza subskrypcji czołowej.
1. Zmień pierwszą część adresu URL zapytania, aby pasowała do punktu końcowego, który odpowiada kluczowi subskrypcji.

Użyj następującej zawartości JSON dla `body` wartości:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Użyj identyfikatora źródła danych dla `"faceId"` .
1. Wklej inne identyfikatory kroju jako warunki w `"faceIds"` tablicy.

### <a name="examine-the-results"></a>Sprawdzanie wyników

Otrzymasz odpowiedź JSON, która wyświetla listę identyfikatorów twarzy, które pasują do powierzchni zapytania.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać interfejsu API REST twarzy do wykonywania podstawowych zadań rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Informacje interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)