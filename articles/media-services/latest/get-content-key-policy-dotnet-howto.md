---
title: Pobieranie klucza podpisywania z zasad przy użyciu platformy .NET w wersji 3 Azure Media Services
description: W tym temacie pokazano, jak uzyskać klucz podpisywania z istniejących zasad przy użyciu zestawu .NET SDK Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80065962"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Pobieranie klucza podpisywania z istniejących zasad

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API V3 nie zwracają wpisów tajnych ani poświadczeń dla operacji **Get** lub **list** . Zobacz szczegółowe wyjaśnienie tutaj: Aby uzyskać więcej informacji, zobacz [konta RBAC i Media Services](rbac-overview.md)

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

[Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md) 
