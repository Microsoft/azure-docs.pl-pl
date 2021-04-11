---
title: Kopiowanie i przenoszenie Custom Vision projektów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsów API ExportProject i ImportProject do kopiowania i przenoszenia projektów Custom Vision.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 1def60843df55a80e8b7d28d7eb61e262a560111
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044159"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Kopiowanie i przenoszenie projektów Custom Vision

Po utworzeniu i przeszkoleniu projektu Custom Vision warto skopiować projekt do innego zasobu. Na przykład możesz chcieć przenieść projekt z programowania do środowiska produkcyjnego lub utworzyć kopię zapasową projektu na koncie w innym regionie świadczenia usługi Azure, aby zwiększyć bezpieczeństwo danych.

Interfejsy API **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** i **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** umożliwiają ten scenariusz, umożliwiając kopiowanie projektów z jednego konta Custom Vision do innych. W tym przewodniku pokazano, jak używać tych interfejsów API REST z zwinięciem. Możesz również użyć usługi żądania HTTP, takiej jak program Poster, aby wydać żądania.

## <a name="business-scenarios"></a>Scenariusze biznesowe

Jeśli Twoja aplikacja lub firma zależy od użycia projektu Custom Vision, zalecamy skopiowanie modelu na inne konto Custom Vision w innym regionie. Jeśli wystąpi awaria regionalna, można uzyskać dostęp do projektu w regionie, w którym został skopiowany.

##  <a name="prerequisites"></a>Wymagania wstępne

- Dwa zasoby Custom Vision platformy Azure. Jeśli ich nie masz, przejdź do Azure Portal i [Utwórz nowy zasób Custom Vision](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Klucze szkoleniowe i adresy URL punktów końcowych zasobów Custom Vision. Te wartości można znaleźć na karcie **Przegląd** zasobu na Azure Portal.
- Utworzono projekt Custom Vision. Zobacz [Kompilowanie klasyfikatora,](./getting-started-build-a-classifier.md) Aby uzyskać instrukcje, jak to zrobić.
* Program [PowerShell w wersji 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)lub podobne narzędzie wiersza polecenia.

## <a name="process-overview"></a>Przegląd procesu

Proces kopiowania projektu składa się z następujących kroków:

1. Najpierw należy uzyskać identyfikator projektu na koncie źródłowym, które chcesz skopiować.
1. Następnie należy wywołać interfejs API **ExportProject** przy użyciu identyfikatora projektu i klucza szkoleniowego konta źródłowego. Otrzymasz tymczasowy ciąg tokenu.
1. Następnie należy wywołać interfejs API **ImportProject** przy użyciu ciągu tokenu i klucza szkoleniowego konta docelowego. Projekt zostanie następnie wyświetlony na liście w ramach konta docelowego.

## <a name="get-the-project-id"></a>Pobierz identyfikator projektu

Najpierw Wywołaj metodę **[Getprojects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** , aby wyświetlić listę istniejących projektów Custom Vision i ich identyfikatorów. Użyj klucza szkoleniowego i punktu końcowego konta źródłowego.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Otrzymasz odpowiedź na `200\OK` listę projektów i ich metadanych w treści. `"id"`Wartość jest ciągiem do skopiowania dla następnych kroków.

```json
[
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "name": "string",
    "description": "string",
    "settings": {
      "domainId": "00000000-0000-0000-0000-000000000000",
      "classificationType": "Multiclass",
      "targetExportPlatforms": [
        "CoreML"
      ],
      "useNegativeSet": true,
      "detectionParameters": "string",
      "imageProcessingSettings": {
        "augmentationMethods": {}
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Eksportowanie projektu

Wywołaj **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** przy użyciu identyfikatora projektu oraz źródłowego klucza szkoleniowego i punktu końcowego.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Otrzymasz `200/OK` odpowiedź zawierającą metadane dotyczące wyeksportowanego projektu i ciągu odwołania `"token"` . Skopiuj wartość tokenu.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importowanie projektu

Wywołaj **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** przy użyciu docelowego klucza szkoleniowego i punktu końcowego wraz z tokenem odwołania. Możesz również nadać projektowi nazwę w nowym koncie.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Otrzymasz odpowiedź dotyczącą `200/OK` metadanych o nowo zaimportowanym projekcie.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób kopiowania i przenoszenia projektu między zasobami Custom Vision. Następnie zapoznaj się z dokumentacją dotyczącą interfejsów API, aby zobaczyć, co jeszcze można zrobić z Custom Vision.
* [Dokumentacja interfejsu API REST](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)