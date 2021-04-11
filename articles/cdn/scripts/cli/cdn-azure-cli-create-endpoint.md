---
title: Tworzenie profilu i punktu końcowego usługi Azure Content Delivery Network (CDN) przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przykładowe skrypty interfejsu wiersza polecenia platformy Azure do tworzenia profilu Azure CDN, punktu końcowego, grupy pochodzenia, pochodzenia i domeny niestandardowej.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726871"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Tworzenie profilu Azure CDN i punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Jako alternatywę dla Azure Portal można użyć tych przykładowych skryptów interfejsu wiersza polecenia platformy Azure do zarządzania następującymi operacjami usługi CDN:

- Utwórz profil usługi CDN.
- Tworzenie punktu końcowego usługi CDN.
- Utwórz grupę pierwotną usługi CDN i ustaw ją jako grupę domyślną.
- Utwórz źródło sieci CDN.
- Utwórz domenę niestandardową i Włącz protokół HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Przykładowe skrypty

Jeśli nie masz jeszcze grupy zasobów dla Twojego profilu CDN, utwórz ją za pomocą polecenia `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Następujący skrypt interfejsu wiersza polecenia platformy Azure tworzy profil sieci CDN i punkt końcowy usługi CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Następujący skrypt interfejsu wiersza polecenia platformy Azure tworzy grupę pochodzenia sieci CDN, ustawia domyślną grupę pierwotną dla punktu końcowego i tworzy nowe źródło:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Poniższy skrypt interfejsu wiersza polecenia platformy Azure tworzy domenę niestandardową usługi CDN i włącza protokół HTTPS. Aby można było skojarzyć domenę niestandardową z punktem końcowym Azure CDN, należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) z Azure DNS lub dostawcą DNS, aby wskazać punkt końcowy usługi CDN. Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu wykonywania przykładowych skryptów Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Poleceń interfejsu wiersza polecenia platformy Azure używanych w tym artykule

- [AZ CDN Endpoint Create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [AZ CDN Endpoint Update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [AZ CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [AZ CDN Origin-Group Create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [AZ CDN profile Create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ CDN Custom-Domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [AZ CDN Custom-Domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
