---
title: dołączanie pliku
description: dołączanie pliku
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375145"
---
W sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) Skopiuj przykładowy adres URL **zapytania** i wklej go do nowej karty przeglądarki.

Adres URL punktu końcowego wygląda tak, jak w następującym formacie, przy użyciu własnej niestandardowej domeny podrzędnej, identyfikatora aplikacji i klucza punktu końcowego zastępującego identyfikator aplikacji i IDENTYFIKATORem klucza:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```