---
title: Definicja schematu aplikacji
description: Aplikacja LUIS jest reprezentowana w `.json` lub `.lu` i zawiera wszystkie intencje, jednostki, przykład wyrażenia długości, funkcje i ustawienia.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684356"
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
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

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