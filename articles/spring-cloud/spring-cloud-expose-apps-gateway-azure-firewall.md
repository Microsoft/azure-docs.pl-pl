---
title: Uwidacznianie aplikacji w Internecie przy użyciu Application Gateway i zapory platformy Azure
description: Jak uwidocznić aplikacje w Internecie przy użyciu Application Gateway i zapory platformy Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738722"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Uwidacznianie aplikacji w Internecie przy użyciu Application Gateway i zapory platformy Azure

W tym dokumencie wyjaśniono, jak udostępniać aplikacje w Internecie przy użyciu Application Gateway i zapory platformy Azure. Gdy w sieci wirtualnej wdrożono wystąpienie usługi Azure wiosennej w chmurze, aplikacje w wystąpieniu usługi są dostępne tylko w sieci prywatnej. Aby udostępnić aplikacje w Internecie, musisz przeprowadzić integrację z **usługą azure Application Gateway** i opcjonalnie za pomocą **zapory platformy Azure**.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definiowanie zmiennych

Zdefiniuj zmienne dla grupy zasobów i sieci wirtualnej, która została utworzona jako skierowana do [wdrożenia Azure wiosennej chmury w usłudze Azure Virtual Network (iniekcja sieci wirtualnej)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Dostosuj wartości na podstawie rzeczywistego środowiska.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Logowanie na platformie Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Usługa **azure Application Gateway** , która ma zostać utworzona, zostanie przyłączona do tej samej sieci wirtualnej co-lub równorzędnej sieci wirtualnej do--wystąpienia usługi w chmurze Azure wiosennej. Najpierw utwórz nową podsieć dla Application Gateway w sieci wirtualnej przy użyciu programu `az network vnet subnet create` , a także Utwórz publiczny adres IP jako fronton Application Gateway przy użyciu `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji przy użyciu `az network application-gateway create` usługi i określ prywatną w pełni kwalifikowaną nazwę domeny (FQDN) swojej aplikacji jako serwery w puli zaplecza. Następnie zaktualizuj ustawienia HTTP przy użyciu polecenia `az network application-gateway http-settings update` , aby użyć nazwy hosta z puli zaplecza.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Tworzenie bramy aplikacji na platformie Azure może potrwać do 30 minut. Po jego utworzeniu Sprawdź kondycję zaplecza przy użyciu polecenia `az network application-gateway show-backend-health` .  Jest to sprawdzenie, czy Brama aplikacji dociera do Twojej aplikacji za pomocą prywatnej nazwy FQDN.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

Dane wyjściowe wskazują stan kondycji puli zaplecza.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Uzyskiwanie dostępu do aplikacji przy użyciu publicznego adresu IP frontonu bramy aplikacji

Uzyskaj publiczny adres IP bramy aplikacji przy użyciu programu `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki.

  ![Aplikacja w publicznym adresie IP](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Zobacz też

- [Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieci wirtualnej](spring-cloud-troubleshooting-vnet.md)
- [Obowiązki klientów do uruchamiania chmury Azure wiosny w sieci wirtualnej](spring-cloud-vnet-customer-responsibilities.md)