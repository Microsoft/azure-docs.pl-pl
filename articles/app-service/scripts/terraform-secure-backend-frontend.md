---
title: 'Terraform: wdrażanie aplikacji sieci Web frontonu i aplikacji sieci Web zaplecza bezpiecznie połączonej z integracją z siecią wirtualną i prywatnym punktem końcowym'
description: Dowiedz się, jak App Service za pomocą dostawcy Terraform wdrożyć dwie aplikacje sieci Web, które są bezpiecznie połączone z prywatnymi punktami końcowymi i integracją z siecią wirtualną
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739837"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Twórz bezpieczne, połączone aplikacje sieci Web dzięki integracji z prywatnymi punktami końcowymi i sieciami wirtualnymi

W tym artykule przedstawiono przykład użycia [prywatnego punktu końcowego](../networking/private-endpoint.md) i integracji regionalnej sieci [wirtualnej](../web-sites-integrate-with-vnet.md) w celu nawiązania bezpiecznego połączenia dwóch aplikacji internetowych (frontonu i zaplecza), wykonując następujące czynności:
- Wdrażanie sieci wirtualnej
- Utwórz pierwszą podsieć do integracji
- Utwórz drugą podsieć dla prywatnego punktu końcowego, należy ustawić konkretny parametr, aby wyłączyć zasady sieciowe
- Wdróż jeden App Service plan typu PremiumV2 lub PremiumV3, wymagany dla funkcji prywatnego punktu końcowego
- Tworzenie aplikacji sieci Web frontonu przy użyciu określonych ustawień aplikacji do korzystania z prywatnej strefy DNS, [więcej szczegółów](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Łączenie aplikacji sieci Web frontonu z podsiecią integracji
- Tworzenie aplikacji sieci Web zaplecza
- Utwórz strefę prywatną DNS z nazwą strefy linku prywatnego dla aplikacji internetowej privatelink.azurewebsites.net
- Połącz tę strefę z siecią wirtualną
- Utwórz prywatny punkt końcowy dla aplikacji sieci Web zaplecza w podsieci punktu końcowego i zarejestruj nazwy DNS (witryny sieci Web i SCM) w wcześniej utworzonej strefie prywatnej DNS

## <a name="how-to-use-terraform-in-azure"></a>Jak używać Terraform na platformie Azure

Przejdź do [dokumentacji platformy Azure](/azure/developer/terraform/) , aby dowiedzieć się, jak używać programu Terraform z platformą Azure.

## <a name="the-complete-terraform-file"></a>Kompletny plik Terraform

Aby użyć tego pliku, należy zmienić właściwość nazwy dla zasobów frontwebapp i backwebapp (nazwa webapp musi być unikatową nazwą DNS na całym świecie). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Następne kroki


> [Dowiedz się więcej o korzystaniu z Terraform na platformie Azure](/azure/developer/terraform/)