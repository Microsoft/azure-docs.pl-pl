---
title: Resource Manager przykłady szablonów — Azure Front Door
description: Informacje o przykładowych Azure Resource Manager szablonów dostarczonych dla Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: e939aec8f1cf5fa615904bb81bad82ea3cf22c61
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587480"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager szablonów dla Azure Front Door

> [!Note]
> Ta dokumentacja dotyczy wersji Azure Front Door Standardowa/Premium (wersja zapoznawcza). Szukasz informacji na temat Azure Front Door? Wyświetl [tutaj .](../front-door-overview.md)

W poniższej tabeli przedstawiono linki do Azure Resource Manager szablonów Azure Front Door, z architekturami referencyjnymi, w tym z innymi usługami platformy Azure.

| Sample | Opis |
|-|-|
| [Front Door (szybkie tworzenie)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Tworzy podstawowy profil Front Door, w tym punkt końcowy, grupę źródeł, źródło i trasę.  |
| [Zestaw reguł](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Tworzy profil Front Door zestaw reguł.  |
| [Zasady WAF z zarządzanym zestawem reguł](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | Tworzy profil Front Door i WAF z zarządzanym zestawem reguł.  |
| [Zasady WAF z regułą niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Tworzy profil Front Door i WAF z regułą niestandardową.  |
|**App Service źródła**| **Opis** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Tworzy aplikację App Service z publicznym punktem końcowym i Front Door profilu.  |
| [App Service z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Tworzy aplikację App Service z prywatnym punktem końcowym i Front Door profilu.  |
| [App Service środowiska z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Tworzy środowisko App Service, aplikację z prywatnym punktem końcowym i profil Front Door końcowego.  |
|**Azure Functions źródła**| **Opis** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Tworzy aplikację Azure Functions z publicznym punktem końcowym i Front Door profilu.  |
| [Azure Functions z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Tworzy aplikację Azure Functions z prywatnym punktem końcowym i Front Door profilu.  |
|**API Management źródła**| **Opis** |
| [API Management (zewnętrzne)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Tworzy wystąpienie API Management z zewnętrzną integracją z siecią wirtualną i Front Door profilu.  |
|**Źródła magazynu**| **Opis** |
| [Statyczna witryna internetowa magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Tworzy konto usługi Azure Storage i statyczną witrynę internetową z publicznym punktem końcowym oraz Front Door profilu.  |
| [Obiekty blob magazynu z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Tworzy konto usługi Azure Storage i kontener obiektów blob z prywatnym punktem końcowym oraz Front Door profilu.  |
|**Application Gateway źródła**| **Opis** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Tworzy Application Gateway i profil Front Door użytkownika. |
|**Źródła maszyn wirtualnych**| **Opis** |
| [Maszyna wirtualna z Private Link service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Tworzy maszynę wirtualną i Private Link usługi oraz Front Door profilu. |
| | |
