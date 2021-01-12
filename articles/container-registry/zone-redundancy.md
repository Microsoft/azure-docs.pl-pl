---
title: Strefowo nadmiarowy rejestr w celu zapewnienia wysokiej dostępności
description: Dowiedz się więcej na temat włączania nadmiarowości stref w Azure Container Registry. Utwórz rejestr kontenerów lub replikację w strefie dostępności platformy Azure. Nadmiarowość stref jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 8c03b2bb093f8d0fa70ff5132f7448ce86e8779d
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127360"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Włącz nadmiarowość stref w Azure Container Registry na potrzeby odporności i wysokiej dostępności

Oprócz [replikacji geograficznej](container-registry-geo-replication.md), która replikuje dane rejestru w jednym lub wielu regionach platformy Azure w celu zapewnienia dostępności i skrócenia opóźnień dla operacji regionalnych, Azure Container Registry obsługuje opcjonalną *nadmiarowość stref*. [Nadmiarowość stref](../availability-zones/az-overview.md#availability-zones) zapewnia odporność i wysoką dostępność dla rejestru lub zasobu replikacji (Replica) w określonym regionie.

W tym artykule pokazano, jak skonfigurować strefowo nadmiarowy rejestr kontenerów lub replikę, korzystając z szablonu interfejsu wiersza polecenia platformy Azure, Azure Portal lub Azure Resource Manager. 

Nadmiarowość stref to funkcja w **wersji zapoznawczej** warstwy usługi kontenera Premium Registry. Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie obsługiwane w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2 i zachodnie stany USA 2.
* Konwersje regionów do stref dostępności nie są obecnie obsługiwane. Aby włączyć obsługę strefy dostępności w regionie, należy utworzyć rejestr w żądanym regionie, w którym włączono obsługę strefy dostępności, lub należy dodać replikowany region z włączoną obsługą strefy dostępności.
* Nie można wyłączyć nadmiarowości strefy w regionie.
* [Zadania ACR](container-registry-tasks-overview.md) jeszcze nie obsługują stref dostępności.

## <a name="about-zone-redundancy"></a>Informacje o nadmiarowości strefy

Za pomocą [stref dostępności](../availability-zones/az-overview.md) platformy Azure można tworzyć odporne i wysokiej dostępności rejestr kontenerów platformy Azure w regionie świadczenia usługi Azure. Na przykład organizacje mogą skonfigurować strefowo nadmiarowy rejestr kontenerów platformy Azure z innymi [obsługiwanymi zasobami platformy Azure](../availability-zones/az-region.md) , aby zaspokoić dane lub inne wymagania dotyczące zgodności, jednocześnie zapewniając wysoką dostępność w regionie.

Azure Container Registry obsługuje również [replikację geograficzną](container-registry-geo-replication.md), która replikuje usługę w wielu regionach, co zapewnia nadmiarowość i lokalizację zasobów obliczeniowych w innych lokalizacjach. Połączenie stref dostępności w celu zapewnienia nadmiarowości w obrębie regionu i replikacji geograficznej w wielu regionach zwiększa niezawodność i wydajność rejestru.

Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Każda strefa ma co najmniej jedno centrum danych z niezależną mocą, chłodzeniem i siecią. Po skonfigurowaniu nadmiarowości strefy Rejestr (lub replikę rejestru w innym regionie) jest replikowana we wszystkich strefach dostępności w regionie, zachowując dostępność w przypadku awarii centrów danych.

## <a name="create-a-zone-redundant-registry---cli"></a>Tworzenie Strefowo nadmiarowego rejestru — interfejs wiersza polecenia

Aby włączyć nadmiarowość strefy przy użyciu interfejsu wiersza polecenia platformy Azure, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.17.0 lub nowszej albo Azure Cloud Shell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom polecenie [AZ Group Create](/cli/az/group#az_group_create) , aby utworzyć grupę zasobów dla rejestru.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Utwórz rejestr obsługujący strefę

Uruchom polecenie [AZ ACR Create](/cli/az/acr#az_acr_create) , aby utworzyć strefowo nadmiarowy rejestr w warstwie usługi Premium. Wybierz region, który [obsługuje strefy dostępności](../availability-zones/az-region.md) dla Azure Container Registry. W poniższym przykładzie jest włączona nadmiarowość strefy w regionie *wschodnim* . Zobacz `az acr create` Pomoc polecenia, aby uzyskać więcej opcji rejestru.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

W danych wyjściowych polecenia Zanotuj `zoneRedundancy` Właściwość rejestru. Gdy ta funkcja jest włączona, rejestr jest nadmiarowy strefy:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Utwórz strefowo nadmiarową replikację

Uruchom polecenie [AZ ACR Replication Create](/cli/az/acr/replication#az_acr_replication_create) , aby utworzyć strefowo nadmiarową replikę rejestru w regionie, który [obsługuje strefy dostępności](../availability-zones/az-region.md) dla Azure Container Registry, na przykład *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
W danych wyjściowych polecenia Zanotuj `zoneRedundancy` Właściwość repliki. Gdy ta funkcja jest włączona, replika jest strefowo nadmiarowa:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Tworzenie strefy — nadmiarowy rejestr — Portal

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. Wybierz pozycję **Utwórz**  >  **kontenery** zasobów  >  **Container Registry**.
1. Na karcie **podstawy** wybierz lub Utwórz grupę zasobów, a następnie wprowadź unikatową nazwę rejestru. 
1. W obszarze **Lokalizacja** wybierz region obsługujący nadmiarowość strefy dla Azure Container Registry, na przykład *Wschodnie stany USA*.
1. W obszarze **jednostka SKU** wybierz pozycję **Premium**.
1. W obszarze **strefy dostępności** wybierz pozycję **włączone**.
1. Opcjonalnie skonfiguruj dodatkowe ustawienia rejestru, a następnie wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz pozycję **Utwórz** , aby wdrożyć wystąpienie rejestru.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Włącz nadmiarowość stref w Azure Portal":::

Aby utworzyć strefowo nadmiarową replikację:

1. Przejdź do rejestru kontenerów warstwy Premium i wybierz pozycję **replikacje**.
1. Na wyświetlonej mapie wybierz zielony sześciokąt w regionie, który obsługuje nadmiarowość strefy dla Azure Container Registry, na przykład **zachodnie stany USA 2**. Lub wybierz pozycję **+ Dodaj**.
1. W oknie **Tworzenie replikacji** Potwierdź **lokalizację**. W obszarze **strefy dostępności** wybierz pozycję **włączone**, a następnie wybierz pozycję **Utwórz**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Włącz replikację nadmiarową strefy w Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Tworzenie strefy — nadmiarowy rejestr — szablon

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom polecenie [AZ Group Create](/cli/az/group#az_group_create) , aby utworzyć grupę zasobów dla rejestru w regionie, który [obsługuje strefy dostępności](../availability-zones/az-region.md) dla Azure Container Registry, takich jak *Wschodnie*. Ten region jest używany przez szablon do ustawiania lokalizacji w rejestrze.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Wdrożenie szablonu 

Przy użyciu następującego szablonu Menedżer zasobów można utworzyć strefowo nadmiarowy rejestr z replikacją geograficzną. Szablon domyślnie włącza nadmiarowość stref w rejestrze i replikę regionalną. 

Skopiuj poniższą zawartość do nowego pliku i Zapisz go przy użyciu nazwy pliku, takiej jak `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Uruchom następujące polecenie [AZ Deployment Group Create](/cli/az/deployment#az_group_deployment_create) , aby utworzyć rejestr przy użyciu poprzedniego pliku szablonu. Gdzie to wskazane, podaj:

* Unikatowa nazwa rejestru lub wdrożenie szablonu bez parametrów i spowoduje utworzenie unikatowej nazwy
* Lokalizacja repliki obsługującej strefy dostępności, na przykład *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

W danych wyjściowych polecenia Zanotuj `zoneRedundancy` Właściwość rejestru i repliki. Gdy ta funkcja jest włączona, każdy zasób jest nadmiarowy strefy:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [regionach, które obsługują strefy dostępności](../availability-zones/az-region.md).
* Dowiedz się więcej o tworzeniu [niezawodności](/azure/architecture/framework/resiliency/overview) na platformie Azure.