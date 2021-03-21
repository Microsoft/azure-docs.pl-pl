---
title: Przekierowywanie ruchu zewnętrznego przy użyciu interfejsu wiersza polecenia platformy Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji, która przekierowuje zewnętrzny ruch internetowy do odpowiedniej puli przy użyciu interfejsu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: 838c2dc887790bb12b390261d94748595232d8b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565861"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Tworzenie bramy aplikacji z przekierowaniami zewnętrznymi przy użyciu interfejsu wiersza polecenia platformy Azure

Przy użyciu interfejsu wiersza polecenia platformy Azure można skonfigurować [przekierowywanie ruchu internetowego](multiple-site-overview.md) podczas tworzenia [bramy aplikacji](overview.md). W tym samouczku skonfigurujesz odbiornik i regułę, która przekierowuje ruch internetowy, który dociera do bramy aplikacji do lokacji zewnętrznej.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Konfigurowanie sieci
* Tworzenie reguły odbiornika i przekierowywania
* Tworzenie bramy aplikacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupAG* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz sieć wirtualną o nazwie *myVNet* i podsieć o nazwie *myAGSubnet* przy użyciu polecenia [az network vnet create](/cli/azure/network/vnet). Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [az network public-ip create](/cli/azure/network/public-ip). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Można użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway) w celu utworzenia bramy aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisywana do wcześniej utworzonej podsieci *myAGSubnet* i adresu *myPublicIPAddress*. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodaj konfigurację przekierowania, która wysyła ruch *z \. consoto.org www* do odbiornika dla *sieci Web \. contoso.com* do bramy aplikacji za pomocą polecenia [AZ Network Application-Gateway redirect-config Create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Dodawanie odbiornika i reguły routingu

Odbiornik jest wymagany, aby umożliwić bramie aplikacji odpowiednie kierowanie ruchu sieciowego. Utwórz odbiornik za pomocą polecenia [AZ Network Application-Gateway HTTP-Listener Create](/cli/azure/network/application-gateway) z portem frontonu utworzonym za pomocą polecenia [AZ Network Application-Gateway fronton-port Create](/cli/azure/network/application-gateway). Dla odbiornika należy określić, gdzie ma być wysyłany ruch przychodzący. Utwórz podstawową regułę o nazwie *redirectRule* przy użyciu polecenia [AZ Network Application-Gateway Rule Create](/cli/azure/network/application-gateway).

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, możesz użyć polecenia [az network public-ip show](/cli/azure/network/public-ip). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

W przeglądarce powinny być widoczne *Bing.com* .

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bramy aplikacji z przekierowaniami wewnętrznymi przy użyciu interfejsu wiersza polecenia platformy Azure](redirect-internal-site-cli.md)
