---
title: Konfigurowanie obsługi tożsamości zarządzanej dla nowego klastra sieci szkieletowej usług
description: Poniżej opisano, jak włączyć obsługę tożsamości zarządzanych w nowym klastrze sieci szkieletowej usług Azure
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415666"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurowanie obsługi tożsamości zarządzanej dla nowego klastra sieci szkieletowej usług

Aby użyć [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w aplikacjach sieci szkieletowej usług, należy najpierw włączyć usługę *tokenu tożsamości zarządzanej* w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji sieci szkieletowej usług przy użyciu ich tożsamości zarządzanych i uzyskiwanie tokenów dostępu w ich imieniu. Po włączeniu usługi można ją wyświetlić w Eksploratorze sieci szkieletowej usług w sekcji **System** w lewym okienku, działającej pod nazwą **sieci szkieletowej:/System/ManagedIdentityTokenService** obok innych usług systemowych.

> [!NOTE]
> Aby włączyć **usługę Token Tokenu Tożsamości Zarządzanej,** wymagana jest wersja wykonawcza sieci szkieletowej usług w wersji 6.5.658.9590 lub nowszej.  

## <a name="enable-the-managed-identity-token-service"></a>Włączanie usługi tokenu tożsamości zarządzanej

Aby włączyć usługę tokenu tożsamości zarządzanej w czasie tworzenia klastra, dodaj następujący fragment kodu do szablonu usługi Azure Resource Manager klastra:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Jeśli wdrożenie nie powiedzie się z tym komunikatem, oznacza to, że klaster nie jest w wymaganej wersji sieci szkieletowej usług (minimalne obsługiwane środowisko uruchomieniowe to 6.5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Powiązane artykuły

* Przejrzyj [obsługę tożsamości zarządzanej](./concepts-managed-identity.md) w sieci szkieletowej usług Azure

* [Włączanie obsługi tożsamości zarządzanych w istniejącym klastrze sieci szkieletowej usług Azure](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Wykorzystaj zarządzaną tożsamość aplikacji sieci szkieletowej usług z kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Udzielanie dostępu aplikacji sieci szkieletowej usługi Azure do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)