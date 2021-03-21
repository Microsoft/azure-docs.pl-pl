---
title: Kategorie obsługiwane w przypadku rozpoznawania jednostek nazwanych
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o obsługiwanych kategoriach jednostek w interfejs API analizy tekstu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599335"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Obsługiwane kategorie jednostek w interfejs API analizy tekstu v3

Skorzystaj z tego artykułu, aby znaleźć kategorie jednostek, które mogą być zwracane przez [nazwanego rozpoznawania jednostek](how-tos/text-analytics-how-to-entity-linking.md) (ner). NER uruchamia model predykcyjny do identyfikowania i kategoryzowania nazwanych jednostek z dokumentu wejściowego.

Dostępna jest również wersja zapoznawcza NER v 3.1, która obejmuje możliwość wykrywania osobistych ( `PII` ) i informacji o kondycji ( `PHI` ). Ponadto kliknij kartę **kondycja** , aby wyświetlić listę obsługiwanych kategorii w analiza tekstu na potrzeby kondycji. 

Listę typów zwracanych przez wersję 2,1 można znaleźć w [przewodniku migracji](migration-guide.md?tabs=named-entity-recognition)

## <a name="entity-categories"></a>Kategorie jednostek

#### <a name="general"></a>[Ogólne](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[DANE](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Służba zdrowia](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Następne kroki

* [Jak używać rozpoznawania jednostek nazwanych w analiza tekstu](how-tos/text-analytics-how-to-entity-linking.md)
