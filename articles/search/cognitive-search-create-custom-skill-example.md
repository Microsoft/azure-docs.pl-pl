---
title: Niestandardowy przykład umiejętności przy użyciu interfejs API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Search
description: Demonstruje użycie usługi wyszukiwanie jednostek Bing w niestandardowej umiejętności zamapowanej na potok indeksowania wzbogaconej AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020188"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Przykład: Tworzenie niestandardowej umiejętności przy użyciu interfejs API wyszukiwania jednostek Bing

W tym przykładzie dowiesz się, jak utworzyć niestandardową umiejętność interfejsu API sieci Web. Ta umiejętność akceptuje lokalizacje, publiczne dane i organizacje oraz zwraca do nich opisy. W przykładzie jest stosowana [Funkcja platformy Azure](https://azure.microsoft.com/services/functions/) , która umożliwia zawinięcie [interfejs API wyszukiwania jednostek Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) tak, aby implementuje niestandardowy interfejs umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z artykułem dotyczącym [niestandardowego interfejsu umiejętności](cognitive-search-custom-skill-interface.md) , jeśli nie znasz interfejsu wejścia/wyjścia, który powinien być zaimplementowany przez umiejętność niestandardową.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) lub nowszy, w tym obciążenie Programowanie na platformie Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Mimo że w tym przykładzie używa się funkcji platformy Azure do hostowania internetowego interfejsu API, nie jest to wymagane.  Tak długo, jak spełniasz [wymagania dotyczące interfejsu dla umiejętności poznawczej](cognitive-search-custom-skill-interface.md), podejmowane podejście jest nieistotne. Azure Functions jednak ułatwić tworzenie niestandardowych umiejętności.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. W programie Visual Studio wybierz pozycję **Nowy**  >  **projekt** z menu plik.

1. W oknie dialogowym Nowy projekt wybierz pozycję **zainstalowane**, rozwiń węzeł **Visual C#**  >  **Cloud**, wybierz **Azure Functions**, wpisz nazwę projektu i wybierz **przycisk OK**. Nazwa aplikacji funkcji musi być prawidłowa jako przestrzeń nazw języka C#, dlatego nie należy używać podkreśleń, łączników ani żadnych innych znaków innych niż alfanumeryczne.

1. Wybierz **Azure Functions v2 (.NET Core)**. Można to również zrobić z wersją 1, ale kod zapisany poniżej jest oparty na szablonie v2.

1. Wybierz typ, który ma być **wyzwalaczem http**

1. W przypadku konta magazynu możesz wybrać opcję **Brak**, ponieważ nie będzie potrzebny żaden magazyn dla tej funkcji.

1. Wybierz **przycisk OK** , aby utworzyć projekt funkcji i funkcję wyzwalaną przez protokół http.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modyfikowanie kodu w celu wywołania usługi wyszukiwanie jednostek Bing

Program Visual Studio tworzy projekt, a w nim klasę zawierającą standardowy kod dla wybranego typu funkcji. Atrybut *FunctionName* metody ustawia nazwę funkcji. Atrybut *HttpTrigger* określa, że funkcja jest wyzwalana przez żądanie HTTP.

Teraz Zastąp całą zawartość pliku *Function1.cs* następującym kodem:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Upewnij się, że na *key* `key` podstawie klucza, który ma być używany podczas rejestrowania się w celu korzystania z interfejsu API wyszukiwania jednostek Bing, wprowadzono własną wartość klucza.

Ten przykład zawiera wszystkie niezbędne kody w pojedynczym pliku dla wygody. Możesz znaleźć nieco bardziej strukturalną wersję tej samej umiejętności w [repozytorium umiejętności oszczędzania](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Oczywiście można zmienić nazwę pliku z `Function1.cs` na `BingEntitySearch.cs` .

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji z programu Visual Studio

Naciśnij klawisz **F5** , aby uruchomić program i testować zachowania funkcji. W takim przypadku użyjemy poniższej funkcji, aby wyszukać dwie jednostki. Użyj elementu Poster lub programu Fiddler, aby wystawić wywołanie podobne do pokazanego poniżej:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Reakcja
Powinna zostać wyświetlona odpowiedź podobna do poniższego przykładu:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikowanie funkcji na platformie Azure

Gdy zachowanie funkcji jest zadowalające, można je opublikować.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. Wybierz pozycję **Utwórz nową**  >  **publikację**.

1. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto....**

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Zostanie wyświetlony monit o podanie unikatowej nazwy usługi App Service, subskrypcji platformy Azure, grupy zasobów, planu hostingu oraz konta magazynu, którego chcesz użyć. Jeśli jeszcze tego nie zrobiono, możesz utworzyć nową grupę zasobów, nowy plan hostingu i konto magazynu. Po zakończeniu wybierz pozycję **Utwórz** .

1. Po zakończeniu wdrażania Zwróć uwagę na adres URL witryny. Jest to adres aplikacji funkcji na platformie Azure. 

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a `EntitySearch` następnie wyszukaj opublikowaną funkcję. W sekcji **Zarządzanie** powinny zostać wyświetlone klucze hosta. Wybierz ikonę **kopiowania** dla *domyślnego* klucza hosta.  

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy masz domyślny klucz hosta, przetestuj swoją funkcję w następujący sposób:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Ten przykład powinien dawać ten sam wynik, który został wcześniej wyświetlony podczas uruchamiania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Nawiązywanie połączenia z potokiem
Teraz, gdy masz nową niestandardową umiejętność, możesz dodać ją do swojej zestawu umiejętności. W poniższym przykładzie pokazano, jak wywoływać umiejętność dodawania opisów do organizacji w dokumencie (można ją rozszerzyć, aby działała także w lokalizacjach i osobach). Zamień `[your-entity-search-app-name]` na nazwę aplikacji.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

W tym miejscu Zliczamy wbudowaną [umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md) , która ma być obecna w zestawu umiejętności, i aby wzbogacić dokument z listą organizacji. Poniżej przedstawiono informacje o konfiguracji umiejętności wyodrębniania jednostek, które byłyby wystarczające do generowania potrzebnych danych:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Następne kroki
Gratulacje! Utworzono swoją pierwszą umiejętność niestandardową. Teraz można użyć tego samego wzorca, aby dodać własną funkcję niestandardową. Kliknij poniższe linki, aby dowiedzieć się więcej.

+ [Umiejętności dotyczące oszczędzania mocy: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Dodaj niestandardową umiejętność do potoku wzbogacania AI](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)