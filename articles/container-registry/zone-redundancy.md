---
title: Rejestr strefowo nadmiarowy w celu wysokiej dostępności
description: Dowiedz się więcej o włączaniu nadmiarowości stref w Azure Container Registry. Utwórz rejestr kontenerów lub replikację w strefie dostępności platformy Azure. Nadmiarowość strefowa jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479019"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Włączanie nadmiarowości strefy w Azure Container Registry w celu zapewnienia odporności i wysokiej dostępności

Oprócz [replikacji](container-registry-geo-replication.md)geograficznej , która replikuje dane rejestru w co najmniej jednym regionie platformy Azure w celu zapewnienia dostępności i zmniejszenia opóźnienia operacji regionalnych, usługa Azure Container Registry opcjonalną nadmiarowość *stref.* [Nadmiarowość strefy](../availability-zones/az-overview.md#availability-zones) zapewnia odporność i wysoką dostępność zasobu rejestru lub replikacji (repliki) w określonym regionie.

W tym artykule pokazano, jak skonfigurować strefowo nadmiarowy rejestr kontenerów lub replikę przy użyciu interfejsu wiersza polecenia platformy Azure, Azure Portal lub Azure Resource Manager szablonu. 

Nadmiarowość strefowa to **funkcja w wersji zapoznawczej** warstwy usługi rejestru kontenerów w warstwie Premium. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Obecnie obsługiwane w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Japonia Wschodnia.
* Konwersje regionów do stref dostępności nie są obecnie obsługiwane. Aby włączyć obsługę strefy dostępności w regionie, rejestr musi zostać utworzony w żądanym regionie z włączoną obsługą strefy dostępności lub należy dodać replikowany region z włączoną obsługą strefy dostępności.
* Nadmiarowości strefy nie można wyłączyć w regionie.
* [zadania usługi ACR](container-registry-tasks-overview.md) nie obsługuje jeszcze stref dostępności.

## <a name="about-zone-redundancy"></a>Informacje o nadmiarowości strefy

Użyj stref [dostępności platformy Azure,](../availability-zones/az-overview.md) aby utworzyć odporny i wysokiej dostępności rejestr kontenerów platformy Azure w regionie świadczenia usługi Azure. Na przykład organizacje mogą skonfigurować strefowo nadmiarowy rejestr kontenerów platformy Azure z innymi obsługiwanymi zasobami platformy [Azure](../availability-zones/az-region.md) w celu spełnienia wymagań dotyczących rezydencji danych lub innych wymagań dotyczących zgodności, zapewniając jednocześnie wysoką dostępność w obrębie regionu.

Azure Container Registry obsługuje również [replikację](container-registry-geo-replication.md)geograficzną , która replikuje usługę w wielu regionach, umożliwiając nadmiarowość i lokalność zasobów obliczeniowych w innych lokalizacjach. Kombinacja stref dostępności w celu zapewnienia nadmiarowości w obrębie regionu i replikacji geograficznej w wielu regionach zwiększa niezawodność i wydajność rejestru.

Strefy dostępności to unikatowe lokalizacje fizyczne w regionie świadczenia usługi Azure. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Każda strefa ma co najmniej jedno centrum danych wyposażone w niezależne zasilanie, chłodzenie i sieć. W przypadku skonfigurowania nadmiarowości strefy rejestr (lub replika rejestru w innym regionie) jest replikowany we wszystkich strefach dostępności w regionie, co zapewnia jego dostępność w przypadku awarii centrum danych.

## <a name="create-a-zone-redundant-registry---cli"></a>Tworzenie rejestru strefowo nadmiarowego — interfejs wiersza polecenia

Aby włączyć nadmiarowość stref przy użyciu interfejsu wiersza polecenia platformy Azure, potrzebny jest interfejs wiersza polecenia platformy Azure w wersji 2.17.0 lub nowszej albo Azure Cloud Shell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom [polecenie az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów dla rejestru.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Tworzenie rejestru z włączoną strefą

Uruchom polecenie [az acr create,](/cli/azure/acr#az_acr_create) aby utworzyć rejestr strefowo nadmiarowy w warstwie usługi Premium. Wybierz region, który [obsługuje strefy dostępności dla](../availability-zones/az-region.md) Azure Container Registry. W poniższym przykładzie nadmiarowość strefy jest włączona w *regionie eastus.* Zobacz pomoc `az acr create` polecenia, aby uzyskać więcej opcji rejestru.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

W danych wyjściowych polecenia `zoneRedundancy` zanotuj właściwość rejestru. Po włączeniu rejestr jest strefowo nadmiarowy:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Tworzenie replikacji strefowo nadmiarowej

Uruchom [polecenie az acr replication create,](/cli/azure/acr/replication#az_acr_replication_create) aby utworzyć strefowo [](../availability-zones/az-region.md) nadmiarową replikę rejestru w regionie, który obsługuje strefy dostępności dla Azure Container Registry, takich *jak westus2.* 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
W danych wyjściowych polecenia `zoneRedundancy` zanotuj właściwość repliki. Po włączeniu replika jest strefowo nadmiarowa:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Tworzenie rejestru strefowo nadmiarowego — portal

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. Wybierz **pozycję Utwórz zasób**  >  **Kontenery**  >  **Container Registry**.
1. Na karcie **Podstawowe** wybierz lub utwórz grupę zasobów, a następnie wprowadź unikatową nazwę rejestru. 
1. W **obszarze** Lokalizacja wybierz region, który obsługuje nadmiarowość stref dla Azure Container Registry, na przykład *Wschodnie usa.*
1. W **sku** wybierz pozycję **Premium.**
1. W **strefie dostępności** wybierz pozycję **Włączone.**
1. Opcjonalnie skonfiguruj dodatkowe ustawienia rejestru, a następnie wybierz pozycję **Przejrzyj i utwórz.**
1. Wybierz **pozycję Utwórz,** aby wdrożyć wystąpienie rejestru.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Włączanie nadmiarowości strefy w Azure Portal":::

Aby utworzyć replikację strefowo nadmiarową:

1. Przejdź do rejestru kontenerów w warstwie Premium i wybierz **pozycję Replikacje.**
1. Na wyświetlonej mapie wybierz zielony sześciokąt w regionie, który obsługuje nadmiarowość stref dla Azure Container Registry, na przykład Zachodnie stany **USA 2.** Możesz też **wybrać pozycję + Dodaj**.
1. W **oknie Tworzenie replikacji** potwierdź **lokalizację**. W **strefie dostępności** wybierz **pozycję Włączone,** a następnie wybierz pozycję **Utwórz.**

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Włączanie replikacji strefowo nadmiarowej w Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Tworzenie rejestru strefowo nadmiarowego — szablon

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom [polecenie az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów dla rejestru w regionie, który obsługuje strefy dostępności dla Azure Container Registry, takich jak *eastus*. [](../availability-zones/az-region.md) Ten region jest używany przez szablon do ustawienia lokalizacji rejestru.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Wdrożenie szablonu 

Za pomocą poniższego szablonu Resource Manager można utworzyć strefowo nadmiarowy rejestr z replikacją geograficzną. Szablon domyślnie włącza nadmiarowość stref w rejestrze i repliki regionalnej. 

Skopiuj następującą zawartość do nowego pliku i zapisz ją przy użyciu nazwy pliku, takiej jak `registryZone.json` .

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

Uruchom następujące polecenie [az deployment group create,](/cli/azure/group/deployment#az_group_deployment_create) aby utworzyć rejestr przy użyciu poprzedniego pliku szablonu. Tam, gdzie jest to wskazane, podaj:

* unikatową nazwę rejestru lub wdrożysz szablon bez parametrów, co spowoduje utworzenie unikatowej nazwy.
* lokalizacja repliki, która obsługuje strefy dostępności, na przykład *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

W danych wyjściowych polecenia `zoneRedundancy` zanotuj właściwość rejestru i repliki. Po włączeniu każdy zasób jest strefowo nadmiarowy:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [regionach, które obsługują strefy dostępności.](../availability-zones/az-region.md)
* Dowiedz się więcej o budowania [niezawodności na platformie](/azure/architecture/framework/resiliency/overview) Azure.
