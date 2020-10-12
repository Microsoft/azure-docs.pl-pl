---
title: Przykład niestandardowego umiejętności (Python)
titleSuffix: Azure Cognitive Search
description: W przypadku deweloperów języka Python Poznaj narzędzia i techniki umożliwiające tworzenie niestandardowych umiejętności przy użyciu Azure Functions i programu Visual Studio. Niestandardowe umiejętności zawierają modele zdefiniowane przez użytkownika lub logikę, które można dodać do potoku indeksowania z ulepszonymi systemami AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936133"
---
# <a name="example-create-a-custom-skill-using-python"></a>Przykład: Tworzenie niestandardowej umiejętności przy użyciu języka Python

Na tym przykładzie usługi Azure Wyszukiwanie poznawcze zestawu umiejętności dowiesz się, jak utworzyć niestandardową umiejętność interfejsu API sieci Web przy użyciu języka Python i Visual Studio Code. W przykładzie jest stosowana [Funkcja platformy Azure](https://azure.microsoft.com/services/functions/) , która implementuje [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md).

Niestandardowa umiejętność jest prosta poprzez projektowanie (łączy dwa ciągi), dzięki czemu można skupić się na narzędziach i technologiach używanych do niestandardowego tworzenia umiejętności w języku Python. Po pomyślnym przeprowadzeniu prostej umiejętności można rozgałęziać z bardziej złożonymi scenariuszami.

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z [niestandardowym interfejsem umiejętności](cognitive-search-custom-skill-interface.md) w celu wprowadzenia do interfejsu wejścia/wyjścia, który powinien być zaimplementowany przez umiejętność niestandardową.

+ Skonfiguruj swoje środowisko. [Ten samouczek jest kompleksowy](/azure/python/tutorial-vs-code-serverless-python-01) , aby skonfigurować funkcję platformy Azure bezserwerową przy użyciu rozszerzeń Visual Studio Code i Python. Samouczek przeprowadzi Cię przez proces instalacji następujących narzędzi i składników: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Rozszerzenie języka Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

W tym przykładzie została użyta funkcja platformy Azure do zademonstrowania koncepcji hostingu internetowego interfejsu API, ale możliwe jest inne podejście. Tak długo, jak spełniasz [wymagania dotyczące interfejsu dla umiejętności poznawczej](cognitive-search-custom-skill-interface.md), podejmowane podejście jest nieistotne. Azure Functions jednak ułatwić tworzenie niestandardowych umiejętności.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Szablon projektu usługi Azure Functions w programie Visual Studio Code umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji w jednostki logiczne, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi.

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create new project...` .

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. Z tego powodu nie należy wybierać folderu projektu, który jest częścią obszaru roboczego.

1. Wybierz język projektu aplikacji funkcji. Na potrzeby tego samouczka wybierz pozycję **Python**.
1. Wybierz wersję języka Python (wersja 3.7.5 jest obsługiwana przez Azure Functions)
1. Wybierz szablon dla pierwszej funkcji projektu. Wybierz **wyzwalacz http** , aby utworzyć funkcję wyzwalaną przez protokół HTTP w nowej aplikacji funkcji.
1. Podaj nazwę funkcji. W tym przypadku użyjemy **złączer** 
1. Wybierz opcję **Funkcja** jako poziom autoryzacji. Oznacza to, że dostarczamy [klucz funkcji](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) do wywołania punktu końcowego http funkcji. 
1. Wybierz, w jaki sposób chcesz otworzyć projekt. W tym kroku wybierz pozycję **Dodaj do obszaru roboczego** , aby utworzyć aplikację funkcji w bieżącym obszarze roboczym.

Program Visual Studio Code utworzy projekt aplikacji funkcji w nowym obszarze roboczym. Ten projekt zawiera pliki konfiguracyjne [host.json](../azure-functions/functions-host-json.md) i [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), a także wszystkie specyficzne dla języka pliki projektu. 

Nowa funkcja wyzwalana przez protokół HTTP jest również tworzona w folderze **złączer** projektu aplikacji funkcji. W tym miejscu będzie istnieć plik o nazwie " \_ \_ init__. pr" z następującą zawartością:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Teraz Zmodyfikujmy ten kod, aby obserwować [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md). Zmodyfikuj kod o następującej zawartości:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Metoda **transform_value** wykonuje operację na pojedynczym rekordzie. Możesz zmodyfikować metodę w celu spełnienia określonych wymagań. Pamiętaj, aby wykonać wszelkie niezbędne walidacje danych wejściowych i zwrócić wszelkie błędy i ostrzeżenia generowane, jeśli nie można ukończyć operacji dla rekordu.

### <a name="debug-your-code-locally"></a>Lokalne debugowanie kodu

Visual Studio Code ułatwia debugowanie kodu. Naciśnij klawisz F5 lub przejdź do menu **Debuguj** , a następnie wybierz **Rozpocznij debugowanie**.

Możesz ustawić wszystkie punkty przerwania w kodzie, naciskając klawisz F9 w wierszu zainteresowania.

Po rozpoczęciu debugowania funkcja zostanie uruchomiona lokalnie. Aby wysłać żądanie do hosta lokalnego, można użyć narzędzia, takiego jak Poster lub programu Fiddler. Zanotuj lokalizację lokalnego punktu końcowego w oknie terminalu. 

## <a name="publish-your-function"></a>Publikowanie funkcji

Gdy zachowanie funkcji jest zadowalające, można je opublikować.

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz pozycję **Wdróż do aplikacja funkcji...**. 

1. Wybierz subskrypcję platformy Azure, w której chcesz wdrożyć aplikację.

1. Wybierz pozycję **+ Utwórz nowe aplikacja funkcji na platformie Azure**

1. Wprowadź globalnie unikatową nazwę aplikacji funkcji.

1. Wybierz wersję języka Python (dla tej funkcji działa język Python 3.7. x).

1. Wybierz lokalizację nowego zasobu (na przykład zachodnie stany USA 2).

W tym momencie niezbędne zasoby zostaną utworzone w ramach subskrypcji platformy Azure w celu hostowania nowej funkcji platformy Azure na platformie Azure. Zaczekaj na zakończenie wdrażania. W oknie danych wyjściowych zostanie wyświetlony stan procesu wdrożenia.

1. W [Azure Portal](https://portal.azure.com)przejdź do **wszystkich zasobów** i Wyszukaj funkcję opublikowaną przez nazwę. Jeśli została wybrana opcja **złączer**, wybierz zasób.

1. Kliknij przycisk **</> Pobierz adres URL funkcji** . Umożliwi to skopiowanie adresu URL w celu wywołania funkcji.

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy masz domyślny klucz hosta, przetestuj swoją funkcję w następujący sposób:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Ten przykład powinien dawać ten sam wynik, który został wcześniej wyświetlony podczas uruchamiania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Nawiązywanie połączenia z potokiem

Teraz, gdy masz nową niestandardową umiejętność, możesz dodać ją do swojej zestawu umiejętności. W poniższym przykładzie pokazano, jak wywołać umiejętność łączenia tytułu i autora dokumentu w jedno pole, które wywołuje merged_title_author. Zamień na `[your-function-url-here]` adres URL nowej funkcji platformy Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki
Gratulacje! Utworzono swoją pierwszą umiejętność niestandardową. Teraz można użyć tego samego wzorca, aby dodać własną funkcję niestandardową. Kliknij poniższe linki, aby dowiedzieć się więcej.

+ [Umiejętności dotyczące oszczędzania mocy: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Dodaj niestandardową umiejętność do potoku wzbogacania AI](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)