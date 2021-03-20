---
title: Definicja schematu aplikacji
description: Aplikacja LUIS jest reprezentowana w `.json` lub `.lu` i zawiera wszystkie intencje, jednostki, przykład wyrażenia długości, funkcje i ustawienia.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327327"
---
# <a name="app-schema-definition"></a>Definicja schematu aplikacji

Aplikacja LUIS jest reprezentowana w `.json` lub `.lu` i zawiera wszystkie intencje, jednostki, przykład wyrażenia długości, funkcje i ustawienia.

## <a name="format"></a>Format

Podczas importowania i eksportowania aplikacji wybierz opcję `.json` lub `.lu` .

|Format|Informacje|
|--|--|
|`.json`| Standardowy format programowania|
|`.lu`|Obsługiwane przez [narzędzia bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)platformy bot Framework.|

## <a name="version-7x"></a>Wersja 7. x

* Przechodzenie do wersji 7. x jednostki są reprezentowane jako zagnieżdżone jednostki uczenia maszynowego.
* Obsługa tworzenia zagnieżdżonych jednostek uczenia maszynowego z `enableNestedChildren` właściwością następujących interfejsów API tworzenia:
    * [Dodawanie etykiety](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Dodaj etykietę partii](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Przejrzyj etykiety](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Sugeruj zapytania punktu końcowego dla jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Sugeruj zapytania punktów końcowych dla intencji](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

|  — element                  | Komentarz                              |
|--------------------------|--------------------------------------|
| "hierarchie": [],     | Przestarzałe, użyj [jednostek uczenia maszynowego](luis-concept-entity-types.md).   |
| "kompozyty": [],        | Przestarzałe, użyj [jednostek uczenia maszynowego](luis-concept-entity-types.md). Odwołanie do [jednostki złożonej](reference-entity-composite.md) . |
| "closedLists": [],       | [Lista](reference-entity-list.md) odwołań do jednostek, używana głównie jako funkcje do jednostek.    |
| "versionId": "0,1",      | Wersja aplikacji LUIS.|
| "name": "example-App",   | Nazwa aplikacji LUIS. |
| "DESC": "",              | Opcjonalny opis aplikacji LUIS.  |
| "Kultura": "en-us",      | [Język](luis-language-support.md) aplikacji ma wpływ na podstawowe funkcje, takie jak wstępnie zbudowane jednostki, uczenie maszynowe i tokenizatora.  |
| "tokenizerVersion": "1.0.0", | [Tokenizatora](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Jednostka Pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Jednostka wyrażenia regularnego](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Listy fraz (funkcja)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Przestarzałe, użyj [jednostek uczenia maszynowego](luis-concept-entity-types.md). |
| "wzorce": [],          |  [Wzorce poprawiają dokładność przewidywania](luis-concept-patterns.md) ze [składnią wzorca](reference-pattern-syntax.md)   |
| "Ustawienia": []           | [Ustawienia aplikacji](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Wersja 6. x

* Przechodzenie do wersji 6. x, użyj nowej [jednostki uczenia maszynowego](reference-entity-machine-learned-entity.md) do reprezentowania jednostek.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Następne kroki

* Migrowanie do [interfejsów API tworzenia v3](luis-migration-authoring-entities.md)