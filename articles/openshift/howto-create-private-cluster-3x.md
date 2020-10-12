---
title: Tworzenie prywatnego klastra przy użyciu systemu Azure Red Hat OpenShift 3,11 | Microsoft Docs
description: Tworzenie prywatnego klastra przy użyciu usługi Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: ARO, OpenShift, prywatny klaster, Red Hat
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727648"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Tworzenie prywatnego klastra przy użyciu usługi Azure Red Hat OpenShift 3,11

Klastry prywatne zapewniają następujące korzyści:

* Klastry prywatne nie ujawniają składników płaszczyzny kontroli klastra (takich jak serwery interfejsu API) na publicznym adresie IP.
* Sieć wirtualna klastra prywatnego jest konfigurowana przez klientów, co pozwala na skonfigurowanie sieci w taki sposób, aby zezwalała na komunikację równorzędną z innymi sieciami wirtualnymi, w tym w środowiskach ExpressRoute. Możesz również skonfigurować niestandardowy serwer DNS w sieci wirtualnej, aby zintegrować z usługami wewnętrznymi.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Pola w poniższym fragmencie kodu konfiguracji są nowe i muszą być dołączone do konfiguracji klastra. `managementSubnetCidr` Program musi znajdować się w sieci wirtualnej klastra i jest używany przez platformę Azure do zarządzania klastrem.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Klaster prywatny można wdrożyć za pomocą przykładowych skryptów przedstawionych poniżej. Po wdrożeniu klastra wykonaj `cluster get` polecenie i sprawdź `properties.FQDN` Właściwość, aby określić prywatny adres IP serwera interfejsu API OpenShift.

Sieć wirtualna klastra zostanie utworzona z uprawnieniami, aby można było ją zmodyfikować. Następnie można skonfigurować sieć w celu uzyskania dostępu do sieci wirtualnej (ExpressRoute, sieci VPN, komunikacji równorzędnej sieci wirtualnej) zgodnie z potrzebami.

Jeśli zmienisz serwery nazw DNS w sieci wirtualnej klastra, musisz wydać aktualizację w klastrze z `properties.RefreshCluster` ustawioną właściwością `true` tak, aby maszyny wirtualne mogły być odtwarzane z obrazu. Ta aktualizacja umożliwi im pobranie nowych serwery nazw.

## <a name="sample-configuration-scripts"></a>Przykładowe skrypty konfiguracyjne

Użyj przykładowych skryptów w tej sekcji, aby skonfigurować i wdrożyć swój prywatny klaster.

### <a name="environment"></a>Środowisko

Wypełnij zmienne środowiskowe poniżej, używając własnych wartości.

> [!NOTE]
> Lokalizacja musi być ustawiona na wartość `eastus2` , ponieważ jest ona obecnie jedyną obsługiwaną lokalizacją dla klastrów prywatnych.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.jsna

Korzystając ze zmiennych środowiskowych zdefiniowanych powyżej, poniżej przedstawiono przykładową konfigurację klastra z włączonym klastrem prywatnym.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Wdrażanie klastra prywatnego

Po skonfigurowaniu klastra prywatnego z przykładowymi skryptami należy uruchomić następujące polecenie, aby wdrożyć swój prywatny klaster.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uzyskać dostęp do konsoli OpenShift, zobacz [Przewodnik po konsoli sieci Web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
