---
title: Tworzenie ContentKeys przy użyciu platformy .NET
description: W tym artykule przedstawiono sposób tworzenia kluczy zawartości przy użyciu platformy .NET. Te klucze zapewniają bezpieczny dostęp do zasobów.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 05bf928490e94f43b755e1958213899e9e1e98e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014181"
---
# <a name="create-contentkeys-with-net"></a>Tworzenie ContentKeys przy użyciu platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services umożliwia tworzenie i dostarczanie zaszyfrowanych zasobów. **ContentKey** zapewnia bezpieczny dostęp do **zasobu** s. 

Podczas tworzenia nowego zasobu (na przykład przed [przekazaniem plików](media-services-dotnet-upload-files.md)) można określić następujące opcje szyfrowania: **StorageEncrypted**, **CommonEncryptionProtected** lub **EnvelopeEncryptionProtected**. 

Po dostarczeniu zasobów klientom można [skonfigurować dla zasobów do dynamicznego szyfrowania](media-services-dotnet-configure-asset-delivery-policy.md) przy użyciu jednego z następujących dwóch szyfrowania: **DynamicEnvelopeEncryption** lub **DynamicCommonEncryption**.

Zaszyfrowane zasoby muszą być skojarzone z **ContentKey** s. W tym artykule opisano sposób tworzenia klucza zawartości.

> [!NOTE]
> Podczas tworzenia nowego elementu zawartości **StorageEncrypted** przy Media Services użyciu zestawu SDK platformy .NET **ContentKey** jest automatycznie tworzony i połączony z elementem zawartości.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Jedną z wartości, które należy ustawić podczas tworzenia klucza zawartości, jest typ klucza zawartości. Wybierz jedną z następujących wartości. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Utwórz ContentKey typu koperty
Poniższy fragment kodu tworzy klucz zawartości typu szyfrowania koperty. Następnie kojarzy klucz z określonym elementem zawartości.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Utwórz ContentKey typu wspólnego
Poniższy fragment kodu tworzy klucz zawartości wspólnego typu szyfrowania. Następnie kojarzy klucz z określonym elementem zawartości.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

