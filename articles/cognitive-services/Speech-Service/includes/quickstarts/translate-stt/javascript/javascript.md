---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: ad3e005f65b1660bd843bf2819f41eddbed44e9c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035892"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>Tworzenie nowego folderu witryny internetowej

Utwórz nowy, pusty folder. W przypadku, gdy chcesz hostować przykład na serwerze internetowym, upewnij się, że serwer internetowy ma dostęp do tego folderu.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Rozpakowywanie zestawu SDK usługi Mowa dla języka JavaScript do tego folderu

Pobierz zestaw SDK usługi Mowa jako [pakiet zip](https://aka.ms/csspeech/jsbrowserpackage) i rozpakuj go do nowo utworzonego folderu. Powoduje to rozpakowanie dwóch plików: `microsoft.cognitiveservices.speech.sdk.bundle.js` i `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Ten drugi plik jest opcjonalny i przydaje się podczas debugowania do kodu zestawu SDK.

## <a name="create-an-indexhtml-page"></a>Tworzenie strony index.html

Utwórz w folderze nowy plik o nazwie `index.html`, a następnie otwórz ten plik za pomocą edytora tekstów.

1. Utwórz następujący szkielet kodu HTML:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-translate-stt.html)]

## <a name="create-the-token-source-optional"></a>Tworzenie źródła tokenu (opcjonalnie)

W przypadku, gdy chcesz udostępnić stronę internetową na serwerze internetowym, możesz opcjonalnie podać źródło tokenu dla swojej aplikacji pokazowej.
W ten sposób Twój klucz subskrypcji nigdy nie opuści serwera, ale jednocześnie umożliwi użytkownikom korzystanie z możliwości funkcji rozpoznawania mowy bez wprowadzania przez nich kodu autoryzacji.

Utwórz nowy plik o nazwie `token.php`. W tym przykładzie przyjęto założenie, że serwer sieci Web obsługuje język skryptów PHP z włączonym zwinięciem. Wprowadź następujący kod:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Tokeny autoryzacji mają tylko ograniczony okres istnienia.
> Ten uproszczony przykład nie pokazuje, jak automatycznie odświeżać tokeny autoryzacji. Jako użytkownik możesz ręcznie ponownie załadować stronę lub nacisnąć klawisz F5, aby odświeżyć.

## <a name="build-and-run-the-sample-locally"></a>Lokalne kompilowanie i uruchamianie przykładu

Aby uruchomić aplikację, kliknij dwukrotnie plik index.html lub otwórz plik index.html za pomocą swojej ulubionej przeglądarki internetowej. Udostępnimy prosty graficzny interfejs użytkownika, który umożliwi wprowadzanie klucza subskrypcji i [regionu](../../../../regions.md) oraz wyzwalanie transkrypcji tekstu wejściowego.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Kompilowanie i uruchamianie przykładu za pomocą serwera internetowego

Aby uruchomić aplikację, Otwórz ulubioną przeglądarkę internetową i wskaż jej publiczny adres URL, na którym znajduje się folder, wprowadź swój [region](../../../../regions.md)i Wyzwól transkrypcję tekstu wejściowego. Jeśli skonfigurowano tę funkcję, pobierze ona token ze źródła tokenu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]