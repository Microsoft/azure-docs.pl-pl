---
title: Resource Manager przykłady szablonów — Azure Front Door
description: Informacje o przykładowych szablonach Azure Resource Manager dla Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: 3559270710f56d43fe486e2e3d3e41c63e2a619b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565835"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager szablonów dla Azure Front Door

> [!Note]
> Ta dokumentacja dotyczy wersji Azure Front Door Standardowa/Premium (wersja zapoznawcza). Szukasz informacji na temat Azure Front Door? Wyświetl [tutaj](../front-door-overview.md).

W poniższej tabeli przedstawiono linki do szablonów Azure Resource Manager dla Azure Front Door, z architekturami referencyjnymi, w tym z innymi usługami platformy Azure.

| Sample | Opis |
|-|-|
| [Front Door (szybkie tworzenie)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Tworzy podstawowy profil Front Door, w tym punkt końcowy, grupę źródeł, źródło i trasę.  |
| [Zestaw reguł](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Tworzy profil Front Door zestaw reguł.  |
| [Zasady aplikacji sieci WAF z regułą niestandardową](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Tworzy profil aplikacji Front Door aplikacji sieci Szkieletowej z regułą niestandardową.  |
|**App Service źródła danych**| **Opis** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Tworzy aplikację App Service z publicznym punktem końcowym i profilem Front Door aplikacji.  |
| [App Service z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Tworzy aplikację App Service z prywatnym punktem końcowym i Front Door profilu.  |
| [App Service środowiska z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Tworzy środowisko App Service, aplikację z prywatnym punktem końcowym i profil Front Door końcowego.  |
|**Azure Functions źródła danych**| **Opis** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Tworzy aplikację Azure Functions z publicznym punktem końcowym i Front Door profilu.  |
| [Azure Functions z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Tworzy aplikację Azure Functions z prywatnym punktem końcowym i Front Door profilu.  |
|**API Management źródła**| **Opis** |
| [API Management (zewnętrzne)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Tworzy wystąpienie API Management z zewnętrzną integracją z siecią wirtualną oraz Front Door profilu.  |
|**Źródła magazynu**| **Opis** |
| [Statyczna witryna internetowa magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Tworzy konto usługi Azure Storage i statyczną witrynę internetową z publicznym punktem końcowym oraz Front Door profilu.  |
| [Obiekty blob magazynu z Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Tworzy konto usługi Azure Storage i kontener obiektów blob z prywatnym punktem końcowym oraz Front Door profilu.  |
|**Application Gateway źródła**| **Opis** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Tworzy Application Gateway i profil Front Door użytkownika. |
|**Źródła maszyn wirtualnych**| **Opis** |
| [Maszyna wirtualna z Private Link service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Tworzy maszynę wirtualną i Private Link usługi oraz Front Door profilu. |
| | |
