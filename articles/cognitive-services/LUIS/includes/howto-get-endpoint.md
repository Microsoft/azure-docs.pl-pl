---
title: dołączanie pliku
description: dołączanie pliku
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959045"
---
W sekcji **Zarządzanie** (menu w prawym górnym rogu) na stronie **zasoby platformy Azure** (menu po lewej) Skopiuj przykładowy adres URL **zapytania** i wklej go do nowej karty przeglądarki.

Adres URL punktu końcowego wygląda tak, jak w następującym formacie, przy użyciu własnej niestandardowej domeny podrzędnej, identyfikatora aplikacji i klucza punktu końcowego zastępującego identyfikator aplikacji i IDENTYFIKATORem klucza:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```