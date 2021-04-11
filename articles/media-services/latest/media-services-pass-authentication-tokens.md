---
title: Przekaż tokeny uwierzytelniania do Media Services v3 | Microsoft Docs
description: Dowiedz się, jak wysyłać tokeny uwierzytelniania z klienta do usługi dostarczania kluczy Media Services v3
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572545"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Przekaż tokeny do usługi dostarczania kluczy Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Klienci często pytają, jak gracz może przekazać tokeny do usługi dostarczania kluczy Azure Media Services w celu weryfikacji, aby odtwarzacz mógł uzyskać klucz. Media Services obsługuje formaty Simple Web token (SWT) i token sieci Web JSON (JWT). Uwierzytelnianie tokenu jest stosowane do dowolnego typu klucza, bez względu na to, czy w systemie jest używane szyfrowanie oparte na standardowym szyfrowaniu lub Advanced Encryption Standard (AES).

 W zależności od odtwarzacza i platformy docelowej można przekazać token z odtwarzaczem w następujący sposób:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Przekaż token za pośrednictwem nagłówka autoryzacji HTTP

> [!NOTE]
> Oczekiwano prefiksu "Bearer" zgodnie ze specyfikacją OAuth 2,0. Aby ustawić źródło wideo, wybierz **AES (token JWT)** lub **AES (SWT token)**. Token jest przesyłany za pośrednictwem nagłówka autoryzacji.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Przekaż token poprzez dodanie parametru zapytania adresu URL z "token = tokenvalue".

> [!NOTE]
> Nie oczekiwano prefiksu "Bearer". Ponieważ token jest wysyłany za pomocą adresu URL, należy chronił ciąg tokenu. Oto przykładowy kod w języku C#, który pokazuje, jak to zrobić:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Przekaż token za pomocą pola CustomData

Ta opcja jest używana tylko w przypadku pozyskiwania licencji PlayReady przy użyciu pola CustomData wyzwania pozyskiwania licencji PlayReady. W takim przypadku token musi znajdować się wewnątrz dokumentu XML, zgodnie z opisem w tym miejscu:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Umieść token uwierzytelniania w elemencie token.
