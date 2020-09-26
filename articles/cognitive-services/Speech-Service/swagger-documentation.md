---
title: Dokumentacja programu Swagger — usługa mowy
titleSuffix: Azure Cognitive Services
description: Za pomocą dokumentacji struktury Swagger można automatycznie generować zestawy SDK dla wielu języków programowania. Wszystkie operacje w naszej usłudze są obsługiwane przez strukturę Swagger
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e42d9713d1d61bb681c0ee51e46cc52eb0bcc807
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91363192"
---
# <a name="swagger-documentation"></a>Dokumentacja platformy Swagger

Usługa mowy oferuje specyfikację struktury Swagger do współpracy z kilku interfejsów API REST używanych do importowania danych, tworzenia modeli, dokładności modeli testów, tworzenia niestandardowych punktów końcowych, kolejkowania transkrypcji partii i zarządzania subskrypcjami. Większość operacji dostępnych za pośrednictwem portalu Custom Speech można wykonać programowo przy użyciu tych interfejsów API.

> [!NOTE]
> Operacje zamiany mowy na tekst i zamiany tekstu na mowę są obsługiwane jako interfejsy API REST, które są opisane w specyfikacji struktury Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generowanie kodu na podstawie specyfikacji struktury Swagger

[Specyfikacja struktury Swagger](https://cris.ai/swagger/ui/index) zawiera opcje, które umożliwiają szybkie przetestowanie różnych ścieżek. Jednak czasami trzeba wygenerować kod dla wszystkich ścieżek, tworząc pojedynczą bibliotekę wywołań, na których można oprzeć przyszłe rozwiązania. Przyjrzyjmy się procesowi, aby wygenerować bibliotekę języka Python.

Musisz ustawić strukturę Swagger na ten sam region, w którym znajduje się subskrypcja usługi mowy. Możesz potwierdzić swój region w Azure Portal w ramach zasobu usługi mowy. Aby uzyskać pełną listę obsługiwanych regionów, zobacz [regiony](regions.md).

1. Przejdź do strony https://editor.swagger.io
2. Kliknij pozycję **plik**, a następnie kliknij pozycję **Importuj** .
3. Wprowadź adres URL programu Swagger, w tym Region subskrypcji usługi mowy `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kliknij pozycję **Generuj klienta** i wybierz pozycję Python
5. Zapisz bibliotekę kliencką

Możesz użyć biblioteki języka Python wygenerowanej w [przykładach usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [REST (Swagger): Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

* [Przykłady usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).
* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
