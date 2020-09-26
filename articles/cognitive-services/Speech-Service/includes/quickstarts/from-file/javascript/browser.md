---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 1f0748ba69c8617663ee867d054758e7a359b48f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377586"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi Azure Speech <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>Dodawanie elementów interfejsu użytkownika

Teraz dodamy podstawowy interfejs użytkownika dla pól wejściowych, odwołując się do języka JavaScript zestawu Speech SDK i połączysz Token autoryzacji, jeśli jest dostępny.

```html  
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">File</td>
        <td><input type="file" id="filePicker" accept=".wav" style="display:none" /></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startRecognizeOnceAsyncButton">Start recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Results</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

   <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
          var token = JSON.parse(atob(this.responseText.split(".")[1]));
          serviceRegion.value = token.region;
          authorizationToken = this.responseText;
          subscriptionKey.disabled = true;
          subscriptionKey.value = "using authorization token (hit F5 to refresh)";
          console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>
  
  <script>
    // status fields and start button in UI
    var phraseDiv;
    var startRecognizeOnceAsyncButton;

    // subscription key and region for speech services.
    var subscriptionKey, serviceRegion;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;
    var filePicker;
    var audioFile;

    document.addEventListener("DOMContentLoaded", function () {
      startRecognizeOnceAsyncButton = document.getElementById("startRecognizeOnceAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      phraseDiv = document.getElementById("phraseDiv");
      filePicker = document.getElementById('filePicker');
      
      filePicker.addEventListener("change", function () {
                audioFile = filePicker.files[0];
            });

      startRecognizeOnceAsyncButton.addEventListener("click", function () {
        startRecognizeOnceAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";

      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startRecognizeOnceAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
            RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `SpeechRecognizer` obiekt, należy utworzyć konfigurację korzystającą z klucza subskrypcji i regionu subskrypcji. Wstaw ten kod w `startRecognizeOnceAsyncButton.addEventListener()` metodzie.

> [!NOTE]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Tworzenie konfiguracji audio

Teraz musisz utworzyć `AudioConfig` obiekt, który wskazuje na plik audio. Wstaw ten kod w `startRecognizeOnceAsyncButton.addEventListener()` metodzie bezpośrednio poniżej konfiguracji mowy.

```JavaScript
        var audioConfig  = SpeechSDK.AudioConfig.fromWavFileInput(audioFile);
```

## <a name="initialize-a-speechrecognizer"></a>Inicjowanie elementu SpeechRecognizer

Teraz Utwórzmy `SpeechRecognizer` Obiekt przy użyciu `SpeechConfig` `AudioConfig` utworzonych wcześniej obiektów i. Wstaw ten kod w `startRecognizeOnceAsyncButton.addEventListener()` metodzie.

```JavaScript
        recognizer = new SpeechSDK.SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-a-phrase"></a>Rozpoznawanie frazy

Z `SpeechRecognizer` obiektu jest wywoływana `recognizeOnceAsync()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

```JavaScript
recognizer.recognizeOnceAsync(
          function (result) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += result.text;
            window.console.log(result);

            recognizer.close();
            recognizer = undefined;
          },
          function (err) {
            startRecognizeOnceAsyncButton.disabled = false;
            phraseDiv.innerHTML += err;
            window.console.log(err);

            recognizer.close();
            recognizer = undefined;
          });
```

## <a name="check-your-code"></a>Sprawdź swój kod

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-from-file.html)]

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

Aby uruchomić aplikację, kliknij dwukrotnie plik index.html lub otwórz plik index.html za pomocą swojej ulubionej przeglądarki internetowej. Będzie on prezentował prosty graficzny interfejs użytkownika umożliwiający wprowadzenie klucza subskrypcji i [regionu](../../../../regions.md) oraz wyzwolenie rozpoznawanie przy użyciu mikrofonu.

> [!NOTE]
> Ta metoda nie działa w przeglądarce Safari.
> W przeglądarce Safari Przykładowa strona sieci Web musi być hostowana na serwerze sieci Web. Przeglądarka Safari nie zezwala na korzystanie z mikrofonu w witrynach sieci Web ładowanych z pliku lokalnego.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Kompilowanie i uruchamianie przykładu za pomocą serwera internetowego

Aby uruchomić aplikację, otwórz przeglądarkę internetową i wskaż w niej publiczny adres URL, pod którym hostowany jest folder, wprowadź swój [region](../../../../regions.md) i wyzwól rozpoznawanie za pomocą mikrofonu. Jeśli skonfigurowano tę funkcję, pobierze ona token ze źródła tokenu.


## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]