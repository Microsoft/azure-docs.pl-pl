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
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097307"
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

**_

## <a name="next-steps"></a>Następne kroki

_ [Jak używać rozpoznawania jednostek nazwanych w analiza tekstu](how-tos/text-analytics-how-to-entity-linking.md)
