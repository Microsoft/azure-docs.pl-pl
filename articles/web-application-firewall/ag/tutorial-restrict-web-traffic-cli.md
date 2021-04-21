---
title: Włączanie Web Application Firewall — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak ograniczyć ruch internetowy za pomocą Web Application Firewall w bramie aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/29/2021
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 390fdd4d9e9d0bc62589484ab0c4ba7468bcaf4b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773103"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Włączanie Web Application Firewall przy użyciu interfejsu wiersza polecenia platformy Azure

Ruch w bramie aplikacji można ograniczyć za pomocą [Web Application Firewall](ag-overview.md) (WAF). Zapora aplikacji internetowych chroni Twoje aplikacje, używając reguł [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Reguły te obejmują ochronę przed atakami, takimi jak iniekcja kodu SQL, działanie skryptów między witrynami i porywanie sesji.

W tym artykule omówiono sposób wykonywania następujących zadań:

 * Konfigurowanie sieci
 * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
 * Tworzenie zestawu skalowania maszyn wirtualnych
 * Tworzenie konta magazynu i konfigurowanie diagnostyki

![Web Application Firewall przykład](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Jeśli wolisz, możesz wykonać tę procedurę przy użyciu [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów platformy Azure o nazwie *myResourceGroupAG* za pomocą polecenia [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Sieci wirtualne i podsieci umożliwiają nawiązywanie łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami. Utwórz sieć wirtualną *o nazwie myVNet* i podsieć *o nazwie myAGSubnet.* Następnie utwórz publiczny adres IP o *nazwie myAGPublicIPAddress.*

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Tworzenie bramy aplikacji przy użyciu zapory aplikacji internetowej

Można użyć polecenia [az network application-gateway create](/cli/azure/network/application-gateway) w celu utworzenia bramy aplikacji o nazwie *myAppGateway*. Podczas tworzenia bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure należy podać informacje o konfiguracji, takie jak pojemność, jednostka SKU i ustawienia protokołu HTTP. Brama aplikacji jest przypisana do *podsieci myAGSubnet* i *myAGPublicIPAddress.*

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Tworzenie bramy aplikacji może potrwać kilka minut. Po utworzeniu bramy aplikacji możesz zauważyć następujące nowe funkcje:

- *appGatewayBackendPool* — brama aplikacji musi mieć co najmniej jedną pulę adresów zaplecza.
- *appGatewayBackendHttpSettings* — określa, że port 80 i protokół HTTP są używane do komunikacji.
- *appGatewayHttpListener* — domyślny odbiornik skojarzony z pulą *appGatewayBackendPool*.
- *appGatewayFrontendIP* — przypisuje adres *myAGPublicIPAddress* do odbiornika *appGatewayHttpListener*.
- *rule1* — domyślna reguła routingu skojarzona z odbiornikiem *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, który zapewnia dwa serwery dla puli zaplecza w bramie aplikacji. Maszyny wirtualne w zestawie skalowania są kojarzone z podsiecią *myBackendSubnet*. Aby utworzyć zestaw skalowania, możesz użyć polecenia [az vmss create](/cli/azure/vmss#az_vmss_create).

Przed uruchomieniem tych wartości zastąp wartości \<username> \<password> i .

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username <username> \
  --admin-password <password> \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalowanie serwera NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tworzenie konta magazynu i konfigurowanie diagnostyki

W tym artykule brama aplikacji używa konta magazynu do przechowywania danych na potrzeby wykrywania i zapobiegania. Do rejestrowania danych można też użyć dzienników usługi Azure Monitor lub usługi Event Hub. 

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz konto magazynu o nazwie *myagstore1* za pomocą polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>Konfigurowanie diagnostyki

Skonfiguruj diagnostykę do rejestrowania danych w dziennikach ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog i ApplicationGatewayFirewallLog. Zastąp `<subscriptionId>` wartość identyfikatorem subskrypcji, a następnie skonfiguruj diagnostykę za pomocą narzędzia [az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testowanie podstawowego adresu URL w bramie aplikacji](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie reguł zapory aplikacji internetowej](application-gateway-customize-waf-rules-portal.md)
