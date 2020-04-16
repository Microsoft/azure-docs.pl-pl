---
title: Wdrażanie aplikacji sieci szkieletowej usług z przypisanym do systemu mi
description: W tym artykule pokazano, jak przypisać tożsamość zarządzaną przypisaną do systemu do aplikacji sieci szkieletowej usług Azure
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415655"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Wdrażanie aplikacji sieci szkieletowej usług z tożsamością zarządzaną przypisaną do systemu

Aby uzyskać dostęp do funkcji tożsamości zarządzanej dla aplikacji sieci szkieletowej usług Azure, należy najpierw włączyć usługę tokenu tożsamości zarządzanej w klastrze. Ta usługa jest odpowiedzialna za uwierzytelnianie aplikacji sieci szkieletowej usług przy użyciu ich tożsamości zarządzanych i uzyskiwanie tokenów dostępu w ich imieniu. Po włączeniu usługi można ją wyświetlić w Eksploratorze sieci szkieletowej usług w sekcji **System** w lewym okienku, działającej pod nazwą **sieci szkieletowej:/System/ManagedIdentityTokenService** obok innych usług systemowych.

> [!NOTE] 
> Wdrażanie aplikacji sieci szkieletowej usług z zarządzanymi `"2019-06-01-preview"`tożsamościami są obsługiwane począwszy od wersji interfejsu API . Można również użyć tej samej wersji interfejsu API dla typu aplikacji, wersji typu aplikacji i zasobów usługi. Minimalny obsługiwany środowisko uruchomieniowe sieci szkieletowej usług wynosi 6,5 CU2. W additoin, środowisko kompilacji / pakiet powinien mieć SF .Net SDK w CU2 lub wyższe

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

### <a name="application-template"></a>Szablon aplikacji

Aby włączyć aplikację z tożsamością zarządzaną przypisaną do systemu, dodaj właściwość **tożsamości** do zasobu aplikacji, z **typem systemAssigned,** jak pokazano w poniższym przykładzie:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Ta właściwość deklaruje (do usługi Azure Resource Manager i dostawców zasobów sieci szkieletowej zarządzanych`system assigned`i usług, odpowiednio, że ten zasób ma niejawną ( ) tożsamość zarządzana.

### <a name="application-and-service-package"></a>Pakiet aplikacji i usług

1. Zaktualizuj manifest aplikacji, aby dodać element **ManagedIdentity** w sekcji **Wiele głównie osób,** zawierający pojedynczy wpis, jak pokazano poniżej:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    To mapuje tożsamość przypisaną do aplikacji jako zasób do przyjaznej nazwy, do dalszego przypisania do usług składających się na aplikację. 

2. W **ServiceManifestImport** sekcji odpowiadającej usługi, która jest przypisana tożsamości zarządzanej, dodać **IdentityBindingPolicy** element, jak wskazano poniżej:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Ten element przypisuje tożsamość aplikacji do usługi; bez tego przypisania usługa nie będzie mogła uzyskać dostępu do tożsamości aplikacji. We powyższym urywek `SystemAssigned` tożsamość (która jest zastrzeżonym słowem kluczowym) jest mapowana `WebAdmin`na definicję usługi pod przyjazną nazwą .

3. Zaktualizuj manifest usługi, aby dodać element **ManagedIdentity** wewnątrz `ServiceIdentityRef` sekcji **Zasoby** o nazwie pasującej do wartości ustawienia z `IdentityBindingPolicy` definicji w manifeście aplikacji:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Jest to równoważne mapowanie tożsamości do usługi, jak opisano powyżej, ale z punktu widzenia definicji usługi. Tożsamość odwołuje się tutaj przez`WebAdmin`jego przyjazną nazwę ( ), zgodnie z zadeklarowaną w manifeście aplikacji.

## <a name="next-steps"></a>Następne kroki
* Przejrzyj [obsługę tożsamości zarządzanej](./concepts-managed-identity.md) w sieci szkieletowej usług Azure
* [Wdrażanie nowego](./configure-new-azure-service-fabric-enable-managed-identity.md) Klaster sieci szkieletowej usług Azure z obsługą tożsamości zarządzanych 
* [Włączanie tożsamości zarządzanej](./configure-existing-cluster-enable-managed-identity-token-service.md) w istniejącym klastrze sieci szkieletowej usług Azure
* Wykorzystanie tożsamości zarządzanej aplikacji sieci szkieletowej [usług z kodu źródłowego](./how-to-managed-identity-service-fabric-app-code.md)
* [Wdrażanie aplikacji sieci szkieletowej usług Azure z tożsamością zarządzaną przypisaną przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udzielanie dostępu aplikacji sieci szkieletowej usługi Azure do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
