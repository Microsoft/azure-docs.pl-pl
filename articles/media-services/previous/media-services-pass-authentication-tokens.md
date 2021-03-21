---
title: Przekaż tokeny uwierzytelniania do Azure Media Services | Microsoft Docs
description: Dowiedz się, jak wysyłać tokeny uwierzytelniania z klienta do usługi dostarczania kluczy Azure Media Services
services: media-services
keywords: Ochrona zawartości, DRM, uwierzytelnianie tokenu
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4daec8ef5f2cc1da3a211113e7236dfd22803d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008249"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Dowiedz się, jak klienci przekazują tokeny do usługi dostarczania kluczy Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Klienci często pytają, jak gracz może przekazać tokeny do usługi dostarczania kluczy Azure Media Services w celu weryfikacji, aby odtwarzacz mógł uzyskać klucz. Media Services obsługuje formaty Simple Web token (SWT) i token sieci Web JSON (JWT). Uwierzytelnianie tokenu jest stosowane do dowolnego typu klucza, bez względu na to, czy w systemie jest używane szyfrowanie oparte na standardowym szyfrowaniu lub Advanced Encryption Standard (AES).

 W zależności od odtwarzacza i platformy docelowej można przekazać token z odtwarzaczem w następujący sposób:

- Za pośrednictwem nagłówka autoryzacji HTTP.
    > [!NOTE]
    > Oczekiwano prefiksu "Bearer" zgodnie ze specyfikacją OAuth 2,0. Przykładowy odtwarzacz z konfiguracją tokenu jest hostowany na [stronie demonstracyjnej](https://ampdemo.azureedge.net/)Azure Media Player. Aby ustawić źródło wideo, wybierz **AES (token JWT)** lub **AES (SWT token)**. Token jest przesyłany za pośrednictwem nagłówka autoryzacji.

- Za pomocą dodania parametru zapytania URL z "token = tokenvalue".  
    > [!NOTE]
    > Nie oczekiwano prefiksu "Bearer". Ponieważ token jest wysyłany za pomocą adresu URL, należy chronił ciąg tokenu. Oto przykładowy kod w języku C#, który pokazuje, jak to zrobić:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Za pomocą pola CustomData.
Ta opcja jest używana tylko w przypadku pozyskiwania licencji PlayReady przy użyciu pola CustomData wyzwania pozyskiwania licencji PlayReady. W takim przypadku token musi znajdować się wewnątrz dokumentu XML, zgodnie z opisem w tym miejscu:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Umieść token uwierzytelniania w elemencie token.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
