---
title: Kontrola dostępu oparta na rolach dla kont Media Services — platforma Azure | Microsoft Docs
description: W tym artykule omówiono kontrolę dostępu opartą na rolach (RBAC) dla kont Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66236914"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Kontrola dostępu oparta na rolach (RBAC) dla kont Media Services

Obecnie Azure Media Services nie definiuje żadnych ról niestandardowych specyficznych dla usługi. Aby uzyskać pełny dostęp do konta Media Services, klienci mogą używać wbudowanych ról **właściciela** lub **współautora**. Główną różnicą między tymi rolami są: **właściciel** może kontrolować, kto ma dostęp do zasobu i **współautor** nie może. Można również użyć wbudowanej roli **czytnika** , ale użytkownik lub aplikacja będzie mieć dostęp tylko do odczytu do interfejsów API Media Services. 

## <a name="design-principles"></a>Zasady projektowania

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API V3 nie zwracają wpisów tajnych ani poświadczeń dla operacji **Get** lub **list** . Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Użytkownik musi wywołać oddzielną metodę akcji, aby uzyskać wpisy tajne lub poświadczenia. Rola **czytnika** nie może wywoływać operacji takich jak Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Posiadanie oddzielnych akcji umożliwia ustawienie bardziej szczegółowych uprawnień zabezpieczeń RBAC w roli niestandardowej w razie potrzeby.

Aby wyświetlić listę obsługiwanych Media Services operacji, wykonaj następujące czynności:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[Wbudowany opis ról](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) zawiera informacje o tym, co przydaje rola. 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Role klasycznego administratora subskrypcji, role kontroli na podstawie ról (RBAC) platformy Azure i role administratora usługi Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Co to jest RBAC dla zasobów platformy Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Zarządzanie dostępem przy użyciu RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operacje dostawcy zasobów Media Services](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Następne kroki

- [Programowanie przy użyciu interfejsów API Media Services v3](media-services-apis-overview.md)
- [Pobieranie zasad klucza zawartości przy użyciu Media Services .NET](get-content-key-policy-dotnet-howto.md)
