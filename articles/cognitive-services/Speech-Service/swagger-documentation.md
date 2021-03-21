---
title: Dokumentacja programu Swagger — usługa mowy
titleSuffix: Azure Cognitive Services
description: Za pomocą dokumentacji struktury Swagger można automatycznie generować zestawy SDK dla wielu języków programowania. Wszystkie operacje w naszej usłudze są obsługiwane przez strukturę Swagger
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636272"
---
# <a name="swagger-documentation"></a>Dokumentacja platformy Swagger

Usługa Speech Service oferuje specyfikację Swagger do współpracy z kilku interfejsów API REST używanych do importowania danych, tworzenia modeli, dokładności modeli testów, tworzenia niestandardowych punktów końcowych, kolejkowania transkrypcji partii i zarządzania subskrypcjami. Większość operacji dostępnych za pośrednictwem [obszaru Custom Speech w programie Speech Studio](https://aka.ms/customspeech) można wykonać programowo przy użyciu tych interfejsów API.

> [!NOTE]
> Usługa Speech zawiera kilka interfejsów API REST na potrzeby [zamiany mowy na tekst](rest-speech-to-text.md) i [zamiany tekstu na mowę](rest-text-to-speech.md).  
>
> Tylko [Zamiana mowy na tekst interfejsy API REST v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) i v 2.0 są udokumentowane w specyfikacji struktury Swagger. Zapoznaj się z dokumentami wymienionymi w poprzednim akapicie, aby uzyskać informacje na temat wszystkich innych interfejsów API REST usługi Speech Services.

## <a name="generating-code-from-the-swagger-specification"></a>Generowanie kodu na podstawie specyfikacji struktury Swagger

[Specyfikacja struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) zawiera opcje, które umożliwiają szybkie przetestowanie różnych ścieżek. Jednak czasami trzeba wygenerować kod dla wszystkich ścieżek, tworząc pojedynczą bibliotekę wywołań, na których można oprzeć przyszłe rozwiązania. Przyjrzyjmy się procesowi, aby wygenerować bibliotekę języka Python.

Musisz ustawić strukturę Swagger w regionie Twojego zasobu mowy. Region można potwierdzić w części **Przegląd** ustawień zasobów mowy w Azure Portal. Pełna lista obsługiwanych regionów jest dostępna [tutaj](regions.md#speech-to-text).

1. W przeglądarce przejdź do specyfikacji Swagger dla [regionu](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Na tej stronie kliknij pozycję **definicja interfejsu API**, a następnie kliknij pozycję **Swagger**. Skopiuj adres URL wyświetlanej strony.
1. W nowej przeglądarce przejdź do [https://editor.swagger.io](https://editor.swagger.io)
1. Kliknij pozycję **plik**, kliknij pozycję **Importuj adres URL**, wklej adres URL, a następnie kliknij przycisk **OK**.
1. Kliknij przycisk **Generuj klienta** i wybierz język **Python**. Biblioteka klienta pobiera do komputera w `.zip` pliku.
1. Wyodrębnij wszystko z pobierania. Możesz użyć, `tar -xf` Aby wyodrębnić wszystko.
1. Zainstaluj wyodrębniony moduł w środowisku języka Python:  
      `pip install path/to/package/python-client`
1. Zainstalowany pakiet ma nazwę `swagger_client` . Sprawdź, czy instalacja zadziałała:  
       `python -c "import swagger_client"`

Możesz użyć biblioteki języka Python wygenerowanej w [przykładach usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Dokumenty referencyjne

* [Swagger: zamiana mowy na tekst interfejsu API REST v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md)

## <a name="next-steps"></a>Następne kroki

* [Przykłady usługi mowy w witrynie GitHub](https://aka.ms/csspeech/samples).
* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
