---
title: Azure API Management zaplecza | Microsoft Docs
description: Dowiedz się więcej o niestandardowych zapleczach w API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813024"
---
# <a name="backends-in-api-management"></a>Zaplecza w API Management

Zaplecza *(lub* zaplecza *interfejsu API*) w API Management jest usługą HTTP, która implementuje interfejs API frontu i jego operacje.

Podczas importowania niektórych interfejsów API API Management automatycznie konfiguruje zaplecza interfejsu API. Na przykład usługa API Management zaplecza podczas importowania specyfikacji [interfejsu OpenAPI,](import-api-from-oas.md)interfejsu API protokołu [SOAP](import-soap-api.md)lub zasobów platformy Azure, takich jak wyzwalana przez protokół HTTP aplikacja funkcji platformy [Azure](import-function-app-as-api.md) lub [aplikacja logiki.](import-logic-app-as-api.md)

API Management obsługuje również używanie innych zasobów platformy Azure, takich jak [Service Fabric klastra](how-to-configure-service-fabric-backend.md) lub usługi niestandardowej jako zaplecza interfejsu API. Korzystanie z tych niestandardowych za zaplecza wymaga dodatkowej konfiguracji, na przykład autoryzowania poświadczeń żądań do usługi zaplecza i definiowania operacji interfejsu API. Te zaplecza można konfigurować i zarządzać nimi w Azure Portal lub przy użyciu interfejsów API lub narzędzi platformy Azure.

Po utworzeniu zaplecza możesz odwoływać się do adresu URL zaplecza w interfejsach API. Użyj [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zasad, aby przekierować przychodzące żądanie interfejsu API do niestandardowego zaplecza zamiast domyślnego zaplecza dla tego interfejsu API.

## <a name="benefits-of-backends"></a>Zalety za zaplecza

Niestandardowe zaplecza oferuje kilka korzyści, w tym:

* Abstrakcja informacji o usłudze zaplecza, promowanie ponownego użyteczności między interfejsami API i ulepszony nadzór  
* Łatwo używane przez skonfigurowanie zasad przekształcania w istniejącym interfejsie API
* Wykorzystuje funkcje zarządzania API Management do obsługi wpisów tajnych w programie Azure Key Vault [jeśli](api-management-howto-properties.md) nazwane wartości są skonfigurowane do uwierzytelniania parametru nagłówka lub zapytania

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj Service Fabric [zaplecza przy](how-to-configure-service-fabric-backend.md) użyciu Azure Portal.
* Zaplecza można również skonfigurować przy użyciu interfejsu [API REST](/rest/api/apimanagement)API Management , Azure PowerShell lub Azure Resource Manager [szablonów.](../service-fabric/service-fabric-tutorial-deploy-api-management.md) [](/powershell/module/az.apimanagement/new-azapimanagementbackend)

