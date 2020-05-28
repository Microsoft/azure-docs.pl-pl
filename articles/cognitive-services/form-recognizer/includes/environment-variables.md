---
title: Zmienne środowiskowe
description: Ustawianie zmiennych środowiskowych
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 8bcce81808530c124b73b3527609a08f869b99c4
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997609"
---
Przy użyciu klucza i punktu końcowego z utworzonego zasobu Utwórz dwa zmienne środowiskowe do uwierzytelnienia:

* `FORM_RECOGNIZER_KEY`— Klucz zasobu do uwierzytelniania żądań.
* `FORM_RECOGNIZER_ENDPOINT`— Punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie wyglądać następująco: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

>[!NOTE]
> Punkty końcowe dla zasobów nieprzeznaczonych dla wersji próbnej utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Aby ustawić zmienne środowiskowe w systemie operacyjnym, należy wykonać poniższe instrukcje.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Po dodaniu zmiennych środowiskowych Zamknij i ponownie otwórz okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

Edytuj `.bash_profile` i Dodaj zmienną środowiskową:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennych środowiskowych Uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zaczęły obowiązywać.
***