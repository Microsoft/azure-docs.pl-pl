---
title: Użycie punktu końcowego przewidywania do programowego testowania obrazów przy użyciu klasyfikatora — Custom Vision
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak programowo testować obrazy za pomocą interfejsu API przy użyciu klasyfikatora usługi Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934756"
---
# <a name="use-your-model-with-the-prediction-api"></a>Korzystanie z modelu za pomocą interfejsu API przewidywania

Po nauczeniu modelu możesz programowo przetestować obrazy przez przesłanie ich do punktu końcowego interfejsu API przewidywania.

> [!NOTE]
> W tym dokumencie przedstawiono przesyłanie obrazu do interfejsu API przewidywania przy użyciu języka C#. Aby uzyskać więcej informacji i przykładów, zobacz [Dokumentacja interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publikowanie przeszkolonej iteracji

Na stronie [Custom Vision ](https://customvision.ai) wybierz swój projekt, a następnie kartę __Wydajność__.

Aby przesłać obrazy do interfejsu API przewidywania, najpierw musisz opublikować iterację do prognozowania, którą można wykonać, wybierając pozycję __Publikuj__ i podając nazwę opublikowanej iteracji. Dzięki temu Twój model będzie dostępny dla interfejsu API przewidywania zasobów Custom Vision platformy Azure.

![Zostanie wyświetlona karta wydajność z czerwonym prostokątem otaczającym przycisk Publikuj.](./media/use-prediction-api/unpublished-iteration.png)

Po pomyślnym opublikowaniu modelu zobaczysz etykietę "Opublikowano" obok iteracji na pasku bocznym po lewej stronie, a jej nazwa zostanie wyświetlona w opisie iteracji.

![Zostanie wyświetlona karta wydajność z czerwonym prostokątem otaczającym opublikowaną etykietę i nazwę opublikowanej iteracji.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Pobieranie adresu URL i klucza predykcyjnego

Po opublikowaniu modelu możesz pobrać wymagane informacje, wybierając pozycję __adres URL przewidywania__. Spowoduje to otwarcie okna dialogowego z informacjami dotyczącymi korzystania z interfejsu API przewidywania, w tym __adres URL przewidywania__ i __klucz predykcyjny__.

![Karta wydajność jest pokazywana z czerwonym prostokątem otaczającym przycisk prognozowanego adresu URL.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Karta wydajność jest pokazywana z czerwonym prostokątem otaczającym wartość adresu URL przewidywania przy użyciu pliku obrazu i wartości Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


W tym przewodniku użyjesz lokalnego obrazu, więc Skopiuj adres URL w obszarze **Jeśli masz plik obrazu** do lokalizacji tymczasowej. Skopiuj również odpowiednią wartość __klucza przewidywania__ .

## <a name="create-the-application"></a>Tworzenie aplikacji

1. W programie Visual Studio Utwórz nową aplikację konsolową w języku C#.

1. Użyj poniższego kodu jako treści pliku __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Zmień następujące informacje:
   * Ustaw `namespace` pole na nazwę projektu.
   * Zastąp symbol zastępczy `<Your prediction key>` wcześniej pobraną wartością klucza.
   * Zamień symbol zastępczy na `<Your prediction URL>` pobrany wcześniej adres URL.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Po uruchomieniu aplikacji zostanie wyświetlony monit o wprowadzenie ścieżki do pliku obrazu w konsoli programu. Obraz jest następnie przesyłany do usługi przewidywania API, a wyniki przewidywania są zwracane jako ciąg w formacie JSON. Poniżej przedstawiono przykładową odpowiedź.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób przesyłania obrazów do niestandardowego klasyfikatora i detektora obrazu oraz do programistycznego odbierania odpowiedzi przy użyciu zestawu C# SDK. Następnie dowiesz się, jak ukończyć kompleksowe scenariusze w języku C#, lub Rozpocznij korzystanie z innego zestawu SDK języka.

* [Szybki Start: Custom Vision SDK](quickstarts/image-classification.md)
