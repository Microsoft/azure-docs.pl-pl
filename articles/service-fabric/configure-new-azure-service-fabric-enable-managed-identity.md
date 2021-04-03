---
title: Konfigurowanie obsługi tożsamości zarządzanej dla nowego klastra Service Fabric
description: Poniżej przedstawiono sposób włączania obsługi tożsamości zarządzanych w nowym klastrze usługi Azure Service Fabric
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574670"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurowanie obsługi tożsamości zarządzanej dla nowego klastra Service Fabric

Aby używać [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w aplikacjach Service Fabric, należy najpierw włączyć *usługę tokenu tożsamości zarządzanej* w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji Service Fabric przy użyciu ich tożsamości zarządzanych i uzyskiwania tokenów dostępu w ich imieniu. Po włączeniu usługi zobaczysz ją w Service Fabric Explorer w sekcji **system** w okienku po lewej stronie, działając w obszarze Nazwa **sieci szkieletowej:/system/ManagedIdentityTokenService** obok innych usług systemowych.

> [!NOTE]
> Aby włączyć **usługę zarządzanego tokenu tożsamości**, wymagana jest Service Fabric środowiska uruchomieniowego w wersji 6.5.658.9590 lub nowszej.  

## <a name="enable-the-managed-identity-token-service"></a>Włącz usługę tokenu zarządzanej tożsamości

Aby włączyć usługę zarządzanych tokenów tożsamości podczas tworzenia klastra, Dodaj następujący fragment kodu do szablonu Azure Resource Manager klastra:

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

## <a name="errors"></a>błędy

Jeśli wdrożenie nie powiedzie się, oznacza to, że klaster nie znajduje się w wymaganej wersji Service Fabric (Minimalna obsługiwana wersja środowiska uruchomieniowego to 6,5 ZASTOSUJESZ pakietu CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Powiązane artykuły

* Przejrzyj [obsługę tożsamości zarządzanych](./concepts-managed-identity.md) w usłudze Azure Service Fabric

* [Włączanie obsługi tożsamości zarządzanych w istniejącym klastrze Service Fabric platformy Azure](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Korzystanie z zarządzanej tożsamości aplikacji Service Fabric z poziomu kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)