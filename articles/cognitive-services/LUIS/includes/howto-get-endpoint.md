---
title: plik dołączania
description: plik dołączania
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545612"
---
W sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) Skopiuj przykładowy adres URL **zapytania** i wklej go do nowej karty przeglądarki.

Adres URL punktu końcowego wygląda tak, jak w następującym formacie, przy użyciu własnej niestandardowej domeny podrzędnej, identyfikatora aplikacji i klucza punktu końcowego zastępującego identyfikator aplikacji i IDENTYFIKATORem klucza:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```