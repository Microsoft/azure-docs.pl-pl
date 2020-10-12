---
title: Przestarzałe umiejętności poznawcze
titleSuffix: Azure Cognitive Search
description: Ta strona zawiera listę umiejętności poznawczych, które są uważane za przestarzałe i nie będzie obsługiwana w najbliższej przyszłości na platformie Azure Wyszukiwanie poznawcze umiejętności.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936099"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Przestarzałe umiejętności poznawcze na platformie Azure Wyszukiwanie poznawcze

W tym dokumencie opisano umiejętności poznawcze, które są uważane za przestarzałe. Skorzystaj z poniższej wskazówki dotyczącej zawartości:

* Nazwa kwalifikacji: Nazwa umiejętności, która będzie przestarzała, jest mapowana na @odata.type atrybut.
* Ostatnia dostępna wersja interfejsu API: Ostatnia wersja usługi Azure Wyszukiwanie poznawcze publicznego interfejsu API, za pomocą której można utworzyć/zaktualizować umiejętności zawierające odpowiednie przestarzałe umiejętności.
* Koniec wsparcia: ostatni dzień, po którym odpowiednie kwalifikacje są uznawane za nieobsługiwane. Wcześniej utworzona umiejętności powinna nadal działać, ale użytkownicy są zalecani do migracji od przestarzałej umiejętności.
* Zalecenia: ścieżka migracji do przodu w celu korzystania z obsługiwanej umiejętności. Użytkownicy powinni postępować zgodnie z zaleceniami, aby nadal otrzymywać pomoc techniczną.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. umiejętności. Text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Ostatnia dostępna wersja interfejsu API

2017-11-11-Preview

### <a name="end-of-support"></a>Koniec wsparcia

15 lutego 2019 r.

### <a name="recommendations"></a>Zalecenia 

Zamiast tego użyj [Microsoft. umiejętności. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Zapewnia większość funkcji NamedEntityRecognitionSkill o wyższej jakości. Zawiera także bogatsze informacje w złożonych polach danych wyjściowych.

Aby przeprowadzić migrację do [umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), należy wykonać co najmniej jedną z następujących zmian w definicji umiejętności. Definicję umiejętności można zaktualizować za pomocą [interfejsu API aktualizacji zestawu umiejętności](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Obecnie wynik zaufania nie jest obsługiwany. Ten `minimumPrecision` parametr istnieje `EntityRecognitionSkill` do użytku w przyszłości i w celu zapewnienia zgodności z poprzednimi wersjami.

1. *(Wymagane)* Zmień wartość `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` na `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Opcjonalnie)* Jeśli używasz `entities` danych wyjściowych, użyj `namedEntities` złożonego danych wyjściowych kolekcji z `EntityRecognitionSkill` zamiast tego. Można użyć `targetName` w definicji umiejętności, aby zamapować ją na adnotację o nazwie `entities` .

3. *(Opcjonalnie)* Jeśli nie określisz jawnie `categories` , `EntityRecognitionSkill` może zwracać różne typy kategorii poza tymi, które były obsługiwane przez `NamedEntityRecognitionSkill` . Jeśli takie zachowanie jest niepożądane, upewnij się, że parametr został jawnie ustawiony `categories` na `["Person", "Location", "Organization"]` .

    _Przykładowe definicje migracji_

    * Prosta migracja

        _Umożliwić Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _Otrzyma Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Nieznacznie skomplikowana migracja

        _Umożliwić Definicja umiejętności NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _Otrzyma Definicja umiejętności EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md)