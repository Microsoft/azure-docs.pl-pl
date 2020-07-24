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
ms.openlocfilehash: 1872d0dd56a0459988e65852340c36493ee53f9f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132249"
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

[Wbudowany opis ról](../../role-based-access-control/built-in-roles.md) zawiera informacje o tym, co przydaje rola. 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Role klasycznego administratora subskrypcji, role kontroli na podstawie ról (RBAC) platformy Azure i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Zarządzanie dostępem przy użyciu RBAC](../../role-based-access-control/role-assignments-rest.md)
- [Operacje dostawcy zasobów Media Services](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Następne kroki

- [Programowanie przy użyciu interfejsów API Media Services v3](media-services-apis-overview.md)
- [Pobieranie zasad klucza zawartości przy użyciu Media Services .NET](get-content-key-policy-dotnet-howto.md)
