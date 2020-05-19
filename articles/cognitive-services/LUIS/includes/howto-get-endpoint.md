---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 2a98e2a97a9154d9e256a4662bb292896c6d1e77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588796"
---
W sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) Skopiuj przykładowy adres URL **zapytania** i wklej go do nowej karty przeglądarki.

Adres URL punktu końcowego wygląda tak, jak w następującym formacie, przy użyciu własnej niestandardowej domeny podrzędnej, identyfikatora aplikacji i klucza punktu końcowego zastępującego identyfikator aplikacji i IDENTYFIKATORem klucza:

```console
https://YOUR-CUSTOM-SUBDMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```