---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST platformy Azure i języka Java'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i języka Java do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fbe62cf00422710e18a6b112adc08f19ea03177b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858357"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST i języka Java

W tym przewodniku szybki start użyjesz interfejsu API REST platformy Azure dla języka Java w celu wykrywania twarzy na obrazie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Wybrane środowisko IDE Java.

## <a name="create-the-java-project"></a>Tworzenie projektu języka Java

1. Utwórz nową aplikację Java w wierszu polecenia w środowisku IDE i Dodaj klasę **główną** przy użyciu metody **Main** .
1. Zaimportuj poniższe biblioteki do projektu języka Java. Jeśli używasz programu Maven, współrzędne programu Maven są udostępniane w każdej bibliotece.
   - [Klient Apache HTTP](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.6)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [Biblioteka JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Rejestrowanie Apache Commons Attribution](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (Commons Attribution-logging: Commons Attribution-logging: 1.1.2)

## <a name="add-face-detection-code"></a>Dodawanie kodu służącego do wykrywania twarzy

Otwórz główną klasę projektu. W tym pliku dodasz kod wymagany do ładowania obrazów i wykrywania twarzy.

### <a name="import-packages"></a>Importowanie pakietów

Dodaj poniższe instrukcje `import` na początku pliku.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Dodawanie podstawowych pól

Zastąp klasę **Main** poniższym kodem. Te dane służą do określania sposobu nawiązywania połączenia z usługą rozpoznawania twarzy i lokalizacji, z której można pobrać dane wejściowe. Musisz zaktualizować `subscriptionKey` pole przy użyciu wartości klucza subskrypcji i zmienić `uriBase` ciąg tak, aby zawierał prawidłowy ciąg punktu końcowego. Możesz również ustawić wartość `imageWithFaces` na ścieżkę, która wskazuje na inny plik obrazu.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Pole `faceAttributes` to po prostu lista atrybutów określonych typów. Będzie ono określać informacje dotyczące wykrytych twarzy, które mają zostać pobrane.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Wywoływanie interfejsu API REST wykrywania twarzy

Dodaj metodę **Main** przy użyciu następującego kodu. Tworzy on wywołanie REST do interfejsu API rozpoznawania twarzy w celu wykrycia informacji o twarzy na obrazie zdalnym (ciąg `faceAttributes` określa, które atrybuty dotyczące twarzy mają zostać pobrane). Następnie zapisuje dane wyjściowe do ciągu JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

Bezpośrednio poniżej powyższego kodu dodaj następujący blok, który służy do konwertowania zwróconych danych JSON do bardziej czytelnego formatu przed wyświetleniem ich w konsoli. Na koniec Zamknij blok try-catch, Metoda **Main** i **Main** Class.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Uruchamianie aplikacji

Skompiluj kod i uruchom go. Odpowiedź oznaczająca powodzenie będzie zawierać dane dotyczące rozpoznawania twarzy w czytelnym formacie JSON w oknie konsoli. Na przykład:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Wyodrębnij atrybuty kroju
 
Aby wyodrębnić atrybuty kroju, użyj modelu wykrywania 1 i Dodaj `returnFaceAttributes` parametr zapytania.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

Odpowiedź zawiera teraz atrybuty czcionki. Na przykład:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono prostą aplikację konsolową języka Java, która używa wywołań REST do interfejs API rozpoznawania twarzy platformy Azure, aby wykrywać twarze w obrazie i zwracać ich atrybuty. Następnie zapoznaj się z dokumentacją referencyjną interfejsu API rozpoznawania twarzy, aby dowiedzieć się więcej na temat obsługiwanych scenariuszy.

> [!div class="nextstepaction"]
> [Interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
