---
title: 'Szybki Start: uzyskiwanie szczegółowych informacji o obrazach przy użyciu interfejsu API REST i języka C# wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazywać obraz przy użyciu interfejs API wyszukiwania wizualnego Bing i języka C#, a następnie uzyskiwać szczegółowe informacje o obrazie.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0f908863b16b892e0978964a549b20bd9393fbae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91277126"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Szybki Start: uzyskiwanie informacji o obrazie przy użyciu interfejsu API REST wyszukiwanie wizualne Bing i języka C #

Ten przewodnik Szybki Start przedstawia sposób przekazywania obrazu do interfejs API wyszukiwania wizualnego Bing i wyświetlania szczegółowych informacji, które zwraca.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [JSON.NET Framework](https://www.newtonsoft.com/json), dostępny jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu narzędzia [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli o nazwie BingSearchApisQuickStart. Dodaj następujące przestrzenie nazw do głównego pliku kodu:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Dodaj zmienne dla klucza subskrypcji, punktu końcowego i ścieżki do obrazu, który chcesz przekazać. Dla `uriBase` wartości można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Utwórz metodę o nazwie, `GetImageFileName()` Aby uzyskać ścieżkę do obrazu.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Utwórz metodę, aby pobrać dane binarne obrazu.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Tworzenie danych formularza

1. Aby przekazać lokalny obraz, najpierw Skompiluj dane formularza w celu wysłania do interfejsu API. Dane formularza zawierają `Content-Disposition` nagłówek, `name` parametr ustawiony na "Image" i `filename` parametr ustawiony na nazwę pliku obrazu. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, to 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. Dodaj ciągi graniczne, aby sformatować dane formularza POST. Ciągi graniczne określają początkową, końcową i znak nowego wiersza dla danych.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. Użyj następujących zmiennych, aby dodać parametry do danych formularza:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. Utwórz funkcję o nazwie, `BuildFormDataStart()` Aby utworzyć początek danych formularza przy użyciu ciągów granic i ścieżki obrazu.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. Utwórz funkcję o nazwie, `BuildFormDataEnd()` Aby utworzyć koniec danych formularza przy użyciu ciągów granicznych.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Wywoływanie interfejsu API wyszukiwania wizualnego Bing

1. Utwórz funkcję wywołującą punkt końcowy wyszukiwanie wizualne Bing i zwracają odpowiedź JSON. Funkcja przyjmuje początek i koniec danych formularza, tablicę bajtową zawierającą dane obrazu i `contentType` wartość.

2. Użyj żądania `WebRequest` do przechowywania identyfikatora URI, wartości contentType i nagłówków.  

3. Użyj `request.GetRequestStream()` , aby napisać dane formularzy i obrazów, a następnie uzyskać odpowiedzi. Funkcja powinna wyglądać podobnie do następującego kodu:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Tworzenie metody głównej

1. W `Main()` metodzie aplikacji Pobierz nazwę pliku i dane binarne obrazu.

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Skonfiguruj treść wpisu, modyfikując jego granicę. Następnie należy wywołać `BuildFormDataStart()` i `BuildFormDataEnd()` utworzyć dane formularza.

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Utwórz `ContentType` wartość przez formatowanie `CONTENT_TYPE_HEADER_PARAMS` i granicę danych formularza.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Pobierz odpowiedź interfejsu API, wywołując `BingImageSearch()` , a następnie drukując odpowiedź.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Korzystanie z obiektu HttpClient

Jeśli używasz `HttpClient` , możesz użyć `MultipartFormDataContent` klasy do skompilowania danych formularza. Skorzystaj z następujących sekcji kodu, aby zastąpić odpowiednie metody w poprzednim przykładzie:

1. Zastąp metodę `Main()` poniższym kodem:

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. Zastąp metodę `BingImageSearch()` poniższym kodem:

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web](../tutorial-bing-visual-search-single-page-app.md)
