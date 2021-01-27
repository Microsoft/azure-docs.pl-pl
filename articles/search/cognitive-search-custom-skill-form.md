---
title: Niestandardowa umiejętność rozpoznawania formularzy (C#)
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć niestandardową umiejętność aparatu rozpoznawania formularzy przy użyciu języka C# i programu Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880139"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Przykład: Utwórz niestandardową umiejętność aparatu rozpoznawania formularzy

Na tym przykładzie usługi Azure Wyszukiwanie poznawcze zestawu umiejętności dowiesz się, jak utworzyć niestandardową umiejętność aparatu rozpoznawania formularzy przy użyciu języka C# i programu Visual Studio. Aparat rozpoznawania formularzy analizuje dokumenty i wyodrębnia pary klucz/wartość oraz dane tabeli. Przez otokę aparatu rozpoznawania formularzy do [niestandardowego interfejsu umiejętności](cognitive-search-custom-skill-interface.md)można dodać tę funkcję jako krok do kompleksowego potoku wzbogacania. Potok może następnie załadować dokumenty i wykonać inne przekształcenia.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (dowolna wersja).
- Co najmniej pięć formularzy tego samego typu. Możesz użyć przykładowych danych dostarczanych z tym przewodnikiem.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Szkolenie modelu

Przed rozpoczęciem korzystania z tej umiejętności należy przeprowadzić uczenie modelu aparatu rozpoznawania formularzy przy użyciu formularzy wejściowych. Postępuj zgodnie z [przewodnikiem Szybki Start](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) , aby dowiedzieć się, jak szkolić model. Możesz użyć przykładowych formularzy dostarczonych w tym przewodniku szybki start lub można użyć własnych danych. Po przeszkoleniu modelu skopiuj jego wartość identyfikatora do bezpiecznej lokalizacji.

## <a name="set-up-the-custom-skill"></a>Skonfiguruj niestandardową umiejętność

W tym samouczku jest stosowany projekt [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) w repozytorium GitHub [umiejętności zarządzania](https://github.com/Azure-Samples/azure-search-power-skills) w witrynie Azure Search. Sklonuj to repozytorium na komputerze lokalnym i przejdź do **elementu Vision/AnalyzeForm/** , aby uzyskać dostęp do projektu. Następnie otwórz _AnalyzeForm. csproj_ w programie Visual Studio. Ten projekt tworzy zasób funkcji platformy Azure, który spełnia [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md) i może służyć do wzbogacania wyszukiwanie poznawcze platformy Azure. Przyjmuje dokumenty formularza jako dane wejściowe i wyprowadza (jako tekst) pary klucz/wartość, które określisz.

Najpierw Dodaj zmienne środowiskowe na poziomie projektu. Znajdź projekt **AnalyzeForm** w lewym okienku, kliknij go prawym przyciskiem myszy, a następnie wybierz polecenie **Właściwości**. W oknie **Właściwości** kliknij kartę **debugowanie** , a następnie znajdź pole **zmienne środowiskowe** . Kliknij przycisk **Dodaj** , aby dodać następujące zmienne:
* `FORMS_RECOGNIZER_ENDPOINT_URL` wartość ustawiona na adres URL punktu końcowego.
* `FORMS_RECOGNIZER_API_KEY` wartość ustawiona na klucz subskrypcji.
* `FORMS_RECOGNIZER_MODEL_ID` z wartością ustawioną na identyfikator przeszkolonego modelu.
* `FORMS_RECOGNIZER_RETRY_DELAY` wartość ustawiona na 1000. Ta wartość to czas (w milisekundach) oczekiwania programu przed ponowieniem próby wykonania zapytania.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` wartość ustawiona na 100. Ta wartość jest tym, ile razy program wyśle zapytanie do usługi podczas próby uzyskania pomyślnej odpowiedzi.

Następnie otwórz _AnalyzeForm.cs_ i Znajdź `fieldMappings` zmienną, która odwołuje się *field-mappings.js* pliku. Ten plik (oraz zmienna odwołująca się do niego) definiuje listę kluczy, które mają zostać wyodrębnione z formularzy, oraz etykietę niestandardową dla każdego klucza. Na przykład wartość `{ "Address:", "address" }, { "Invoice For:", "recipient" }` oznacza, że skrypt będzie zapisywał tylko wartości dla wykrytych `Address:` i `Invoice For:` pól, a następnie oznaczy te wartości odpowiednio znakami `"address"` i `"recipient"` .

Na koniec Zwróć uwagę na `contentType` zmienną. Ten skrypt uruchamia dany model aparatu rozpoznawania formularzy w dokumentach zdalnych, do których odwołuje się adres URL, więc typ zawartości to `application/json` . Jeśli chcesz analizować pliki lokalne przez uwzględnienie ich strumieni bajtów w żądaniach HTTP, należy zmienić na `contentType` odpowiedni [Typ MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) dla pliku.

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji z programu Visual Studio

Po edytowaniu projektu, Zapisz go i ustaw projekt **AnalyzeForm** jako projekt startowy w programie Visual Studio (jeśli nie został jeszcze ustawiony). Naciśnij klawisz **F5** , aby uruchomić funkcję w środowisku lokalnym. Użyj usługi REST, takiej jak [Poster](https://www.postman.com/) , aby wywołać funkcję.

### <a name="http-request"></a>Żądanie HTTP

Należy wykonać następujące żądanie wywołania funkcji.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Treść żądania

Rozpocznij od szablonu treści żądania poniżej.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

W tym miejscu należy podać adres URL formularza, który ma ten sam typ co przeszkolone formularze. Do celów testowych możesz użyć jednego z Twoich formularzy szkoleniowych. Jeśli korzystasz z tego przewodnika Szybki Start, Twoje formularze będą znajdować się na koncie usługi Azure Blob Storage. Otwórz Eksplorator usługi Azure Storage, zlokalizuj plik formularza, kliknij go prawym przyciskiem myszy, a następnie wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**. Następne okno dialogowe udostępnia adres URL i token sygnatury dostępu współdzielonego. Wprowadź te ciągi odpowiednio w `"formUrl"` `"formSasToken"` polach i treści żądania.

> [!div class="mx-imgBorder"]
> ![Eksplorator usługi Azure Storage; wybrano dokument PDF](media/cognitive-search-skill-form/form-sas.png)

Jeśli chcesz analizować dokument zdalny, który nie znajduje się w usłudze Azure Blob Storage, wklej jego adres URL w `"formUrl"` polu i pozostaw `"formSasToken"` pole puste.

> [!NOTE]
> Gdy umiejętność jest zintegrowana z zestawu umiejętności, adres URL i token będą udostępniane przez Wyszukiwanie poznawcze.

### <a name="response"></a>Reakcja

Powinna zostać wyświetlona odpowiedź podobna do poniższego przykładu:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikowanie funkcji na platformie Azure

Gdy zachowanie funkcji jest zadowalające, można je opublikować.

1. W **Eksplorator rozwiązań** w programie Visual Studio kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Publikuj**. Wybierz pozycję **Utwórz nową**  >  **publikację**.
1. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto....**
1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Określ unikatową nazwę usługi App Service, subskrypcję platformy Azure, grupę zasobów, plan hostingu i konto magazynu, którego chcesz użyć. Jeśli jeszcze tego nie zrobiono, możesz utworzyć nową grupę zasobów, nowy plan hostingu i nowe konto magazynu. Po zakończeniu wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrażania Zwróć uwagę na adres URL witryny. Ten adres URL jest adresem aplikacji funkcji na platformie Azure. Zapisz ją w lokalizacji tymczasowej.
1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów, a `AnalyzeForm` następnie wyszukaj opublikowaną funkcję. W sekcji **Zarządzanie** powinny zostać wyświetlone klucze hosta. Skopiuj *domyślny* klucz hosta i Zapisz go w lokalizacji tymczasowej.

## <a name="connect-to-your-pipeline"></a>Nawiązywanie połączenia z potokiem

Aby skorzystać z tej umiejętności w potoku Wyszukiwanie poznawcze, musisz dodać definicję umiejętności do zestawu umiejętności. Poniższy blok JSON jest przykładową definicją umiejętności (należy zaktualizować dane wejściowe i wyjściowe, aby odzwierciedlały określony scenariusz i środowisko zestawu umiejętności). Zamień `AzureFunctionEndpointUrl` na adres URL funkcji i Zamień `AzureFunctionDefaultHostKey` na swój klucz hosta.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku utworzono niestandardową umiejętność z usługi rozpoznawania formularzy platformy Azure. Aby dowiedzieć się więcej o umiejętnościach niestandardowych, zobacz następujące zasoby. 

* [Azure Search umiejętności dotyczące oszczędzania mocy: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
* [Dodaj niestandardową umiejętność do potoku wzbogacania AI](cognitive-search-custom-skill-interface.md)
* [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
* [Utwórz zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset)
* [Mapuj wzbogacone pola](cognitive-search-output-field-mapping.md)