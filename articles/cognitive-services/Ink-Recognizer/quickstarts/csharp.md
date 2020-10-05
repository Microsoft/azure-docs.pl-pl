---
title: 'Szybki Start: Rozpoznawanie cyfrowego atramentu za pomocą interfejsu API REST aparatu rozpoznawania atramentu i języka C #'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przedstawia sposób korzystania z interfejsu API rozpoznawania pisma odręcznego i języka C# w celu rozpoczęcia rozpoznawania cyfrowych pociągnięć odręcznych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a4bb02f11c7a9a75ddc96e0ee8e9f4b868f8ade5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051597"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Szybki Start: Rozpoznawanie cyfrowego atramentu za pomocą interfejsu API REST aparatu rozpoznawania atramentu i języka C #

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wysyłanie pociągnięć cyfrowego atramentu do interfejsu API rozpoznawania atramentu. Ta aplikacja w języku C# wysyła żądanie interfejsu API zawierające dane pociągnięcia odręcznego w formacie JSON i pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji cyfrowego odręcznego. Ten przewodnik Szybki Start wysyła dane pociągnięć odręcznych dla poniższego przykładu napisanego ręcznie z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Aby zainstalować Newtonsoft.Jsna jako pakiet NuGet w programie Visual Studio:
        1. Kliknij prawym przyciskiem myszy **Menedżera rozwiązań**
        2. Kliknij pozycję **Zarządzaj pakietami NuGet...**
        3. Wyszukaj `Newtonsoft.Json` i zainstaluj pakiet
- Jeśli używasz systemu Linux/MacOS, ta aplikacja może zostać uruchomiona przy użyciu narzędzia [mono](https://www.mono-project.com/).

- Przykładowe dane pociągnięcia farbą dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania atramentu

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i Dodaj następujące pakiety. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego oraz przykładowy plik JSON. Punkt końcowy zostanie później połączony z `inkRecognitionUrl` programem w celu uzyskania dostępu do interfejsu API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję asynchroniczną o nazwie `Request` , która przyjmuje utworzone powyżej zmienne.

2. Ustaw informacje o protokole i nagłówku zabezpieczeń klienta przy użyciu `HttpClient` obiektu. Pamiętaj, aby dodać klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka. Następnie Utwórz `StringContent` obiekt dla żądania.
 
3. Wyślij żądanie przy użyciu `PutAsync()` . Jeśli żądanie zakończy się pomyślnie, zwróć odpowiedź.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Wyślij żądanie rozpoznawania pisma odręcznego

1. Utwórz nową funkcję o nazwie `recognizeInk()` . Utwórz żądanie i wyślij je, wywołując `Request()` funkcję z punktem końcowym, kluczem subskrypcji, adresem URL dla interfejsu API i danymi pociągnięcia cyfrowego atramentu.

2. Deserializacji obiektu JSON i Zapisz go w konsoli. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Ładowanie danych cyfrowego atramentu

Utwórz funkcję o nazwie, `LoadJson()` Aby załadować plik JSON danych pisma odręcznego. Użyj `StreamReader` i, `JsonTextReader` Aby utworzyć `JObject` i zwrócić.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Wyślij żądanie interfejsu API

1. W głównej metodzie aplikacji Załaduj dane JSON przy użyciu funkcji utworzonej powyżej. 

2. Wywołaj `recognizeInk()` utworzoną powyżej funkcję. Użyj, `System.Console.ReadKey()` Aby zachować otwarte okno konsoli po uruchomieniu aplikacji.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację i Wyświetl odpowiedź

Uruchom aplikację. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź na kod JSON można również znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w aplikacji do cyfrowego odkróla, zapoznaj się z następującymi przykładowymi aplikacjami w witrynie GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
