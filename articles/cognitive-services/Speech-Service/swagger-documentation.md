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
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665688"
---
# <a name="swagger-documentation"></a>Dokumentacja platformy Swagger

Usługa mowy oferuje specyfikację struktury Swagger do współpracy z kilku interfejsów API REST używanych do importowania danych, tworzenia modeli, dokładności modeli testów, tworzenia niestandardowych punktów końcowych, kolejkowania transkrypcji partii i zarządzania subskrypcjami. Większość operacji dostępnych za pośrednictwem portalu Custom Speech można wykonać programowo przy użyciu tych interfejsów API.

> [!NOTE]
> Operacje zamiany mowy na tekst i zamiany tekstu na mowę są obsługiwane jako interfejsy API REST, które są opisane w specyfikacji struktury Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generowanie kodu na podstawie specyfikacji struktury Swagger

[Specyfikacja struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) zawiera opcje, które umożliwiają szybkie przetestowanie różnych ścieżek. Jednak czasami trzeba wygenerować kod dla wszystkich ścieżek, tworząc pojedynczą bibliotekę wywołań, na których można oprzeć przyszłe rozwiązania. Przyjrzyjmy się procesowi, aby wygenerować bibliotekę języka Python.

Musisz ustawić strukturę Swagger na ten sam region, w którym znajduje się subskrypcja usługi mowy. Możesz potwierdzić swój region w Azure Portal w ramach zasobu usługi mowy. Aby uzyskać pełną listę obsługiwanych regionów, zobacz [regiony](regions.md).

1. W przeglądarce przejdź do specyfikacji Swagger dla regionu:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Na tej stronie kliknij pozycję **definicja interfejsu API**, a następnie kliknij pozycję **Swagger**. Skopiuj adres URL wyświetlanej strony.
1. W nowej przeglądarce przejdź do https://editor.swagger.io
1. Kliknij pozycję **plik**, kliknij pozycję **Importuj adres URL**, wklej adres URL, a następnie kliknij przycisk **OK**.
1. Kliknij przycisk **Generuj klienta** i wybierz język **Python**. Biblioteka klienta pobiera do komputera w `.zip` pliku.
1. Wyodrębnij wszystko z pobierania. Możesz użyć, `tar -xf` Aby wyodrębnić wszystko.
1. Zainstaluj wyodrębniony moduł w środowisku języka Python:  
       `pip install path/to/package/python-client`
1. Zainstalowany pakiet ma nazwę `swagger_client` . Sprawdź, czy instalacja zadziałała:  
       `python -c "import swagger_client"`

Możesz użyć biblioteki języka Python wygenerowanej w [przykładach usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [REST (Swagger): Transkrypcja i dostosowywanie partii](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

* [Przykłady usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).
* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
