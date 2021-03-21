---
title: Konfigurowanie obsługi tożsamości zarządzanej w istniejącym klastrze Service Fabric
description: Poniżej przedstawiono sposób włączania obsługi tożsamości zarządzanych w istniejącym klastrze Service Fabric platformy Azure
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790043"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Konfigurowanie obsługi tożsamości zarządzanej w istniejącym klastrze Service Fabric

Aby używać [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w aplikacjach Service Fabric, należy najpierw włączyć *usługę tokenu tożsamości zarządzanej* w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji Service Fabric przy użyciu ich tożsamości zarządzanych i uzyskiwania tokenów dostępu w ich imieniu. Po włączeniu usługi zobaczysz ją w Service Fabric Explorer w sekcji **system** w okienku po lewej stronie, działając w obszarze Nazwa **sieci szkieletowej:/system/ManagedIdentityTokenService**.

> [!NOTE]
> Aby włączyć **usługę zarządzanego tokenu tożsamości**, wymagana jest Service Fabric środowiska uruchomieniowego w wersji 6.5.658.9590 lub nowszej.  
>
> Service Fabric wersję klastra można znaleźć z poziomu Azure Portal, otwierając zasób klastra i sprawdzając Właściwość **wersja Service Fabric** w sekcji **podstawowe** informacje.
>
> Jeśli klaster działa w trybie uaktualniania **ręcznego** , należy najpierw uaktualnić go do 6.5.658.9590 lub nowszego.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Włączanie *usługi zarządzanych tokenów tożsamości* w istniejącym klastrze

Aby włączyć usługę tokenu tożsamości zarządzanej w istniejącym klastrze, należy zainicjować uaktualnienie klastra, określając dwie zmiany: (1) włączenie usługi tokenu tożsamości zarządzanej oraz (2) żądanie ponownego uruchomienia każdego węzła. Najpierw Dodaj następujący fragment kodu dotyczący szablonu Azure Resource Manager klastra:

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

Aby zmiany zaczęły obowiązywać, należy również zmienić zasady uaktualniania, aby określić wymuszenie ponownego uruchomienia Service Fabric środowiska uruchomieniowego na każdym węźle w miarę postępu uaktualniania przez klaster. To ponowne uruchomienie zapewnia, że nowo włączona usługa systemu zostanie uruchomiona i uruchomiona w każdym węźle. W poniższym fragmencie kodu `forceRestart` jest to podstawowe ustawienie umożliwiające ponowne uruchomienie. W przypadku pozostałych parametrów należy użyć wartości opisanych poniżej lub użyć istniejących wartości niestandardowych już określonych dla zasobu klastra. Ustawienia niestandardowe dla zasad uaktualniania sieci szkieletowej ("upgradeDescription") można wyświetlić w witrynie Azure Portal, wybierając opcję "Ustawienia sieci szkieletowej" w zasobie Service Fabric lub resources.azure.com. Domyślne opcje dla zasad uaktualniania ("upgradeDescription") nie są widoczne w programie PowerShell lub resources.azure.com. Aby uzyskać dodatkowe informacje, zobacz [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) .  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Po pomyślnym zakończeniu uaktualniania nie zapomnij wycofać tego `forceRestart` Ustawienia, aby zminimalizować wpływ kolejnych uaktualnień. 

## <a name="errors-and-troubleshooting"></a>Błędy i rozwiązywanie problemów

Jeśli wdrożenie zakończy się niepowodzeniem z następującym komunikatem, oznacza to, że klaster nie jest uruchomiony na wystarczająco wysokiej wersji Service Fabric:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Korzystanie z zarządzanej tożsamości aplikacji Service Fabric z poziomu kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
