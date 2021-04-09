---
title: Uzyskiwanie klucza podpisywania z poziomu platformy .NET dla zasad
description: W tym temacie pokazano, jak uzyskać klucz podpisywania z istniejących zasad przy użyciu zestawu .NET SDK Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: df17eddd9cb4f05eb42ac517691bbf71bf72040f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960184"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Pobieranie klucza podpisywania z istniejących zasad

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API V3 nie zwracają wpisów tajnych ani poświadczeń dla operacji **Get** lub **list** . Zobacz szczegółowe wyjaśnienie tutaj: Aby uzyskać więcej informacji, zobacz [konta usług Azure RBAC i Media Services](rbac-overview.md)

W przykładzie w tym artykule pokazano, jak za pomocą programu .NET pobrać klucz podpisywania z istniejących zasad. 
 
## <a name="download"></a>Pobierz 

Sklonuj repozytorium GitHub zawierające pełny przykład platformy .NET na maszynie przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Przykład ContentKeyPolicy z wpisami tajnymi znajduje się w folderze [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .

## <a name="get-contentkeypolicy-with-secrets"></a>Pobierz ContentKeyPolicy z kluczami tajnymi 

Aby przejść do klucza, użyj **GetPolicyPropertiesWithSecretsAsync**, jak pokazano w poniższym przykładzie.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Następne kroki

[Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](architecture-design-multi-drm-system.md) 
