---
title: Przykłady szablonów Menedżer zasobów — drzwi platformy Azure
description: Informacje dotyczące przykładowych szablonów Azure Resource Manager udostępnionych dla drzwi platformy Azure.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561750"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Szablony Azure Resource Manager dla drzwi platformy Azure

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Poniższa tabela zawiera linki do szablonów Azure Resource Manager dla drzwi platformy Azure, z uwzględnieniem architektur referencyjnych, w tym innych usług platformy Azure.

| Sample | Opis |
|-|-|
| [Zestaw reguł](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Tworzy profil drzwiczki i zestaw reguł.  |
|**App Service źródła**| **Opis** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Tworzy aplikację App Service z publicznym punktem końcowym oraz profilem drzwi przednich.  |
| [App Service z linkiem prywatnym](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Tworzy aplikację App Service z prywatnym punktem końcowym oraz profilem drzwi przednich.  |
| [Środowisko App Service z linkiem prywatnym](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Tworzy środowisko App Service, aplikację z prywatnym punktem końcowym i profilem drzwi przednich.  |
|**Azure Functions źródła**| **Opis** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Tworzy aplikację Azure Functions z publicznym punktem końcowym oraz profilem drzwi przednich.  |
| [Azure Functions z linkiem prywatnym](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Tworzy aplikację Azure Functions z prywatnym punktem końcowym oraz profilem drzwi przednich.  |
|**API Management źródła**| **Opis** |
| [API Management (zewnętrzny)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Tworzy wystąpienie API Management z integracją zewnętrznej sieci wirtualnej i profilem drzwi przednich.  |
|**Źródła magazynu**| **Opis** |
| [Statyczna witryna sieci Web magazynu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Tworzy konto usługi Azure Storage i statyczną witrynę sieci Web z publicznym punktem końcowym oraz profilem drzwi.  |
| [Obiekty blob magazynu z linkiem prywatnym](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Tworzy konto usługi Azure Storage i kontener obiektów blob z prywatnym punktem końcowym oraz profilem drzwi przednich.  |
|**Application Gateway źródła**| **Opis** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Tworzy Application Gateway i profil przedni drzwi. |
|**Źródła maszyn wirtualnych**| **Opis** |
| [Maszyna wirtualna z usługą linku prywatnego](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Tworzy maszynę wirtualną i usługę łącza prywatnego oraz profil dla drzwi. |
| | |
