---
title: Frontony usługi Azure API Management | Microsoft Docs
description: Dowiedz się więcej na temat niestandardowych zaliczeń w API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500619"
---
# <a name="backends-in-api-management"></a>Zakończyło się w API Management

*Wewnętrzna baza danych* (lub *zaplecze interfejsu API*) w API Management to usługa http implementująca interfejs API frontonu i jego operacje.

Podczas importowania niektórych interfejsów API API Management automatycznie konfiguruje zaplecze interfejsu API. Na przykład API Management konfiguruje zaplecze podczas importowania [specyfikacji openapi](import-api-from-oas.md), [interfejsu API protokołu SOAP](import-soap-api.md)lub zasobów platformy Azure, takich jak [usługa Azure aplikacja funkcji](import-function-app-as-api.md) lub [aplikacja logiki](import-logic-app-as-api.md)wyzwolona przy użyciu protokołu HTTP.

API Management obsługuje również korzystanie z innych zasobów platformy Azure, takich jak [klaster Service Fabric](how-to-configure-service-fabric-backend.md) lub Usługa niestandardowa jako zaplecze interfejsu API. Korzystanie z tych niestandardowych zapleczy wymaga dodatkowej konfiguracji, na przykład do autoryzacji poświadczeń żądań do usługi zaplecza oraz do definiowania operacji interfejsu API. Można konfigurować te punkty końcowe i zarządzać nimi w Azure Portal lub przy użyciu interfejsów API lub narzędzi platformy Azure.

Po utworzeniu zaplecza można odwołać się do adresu URL zaplecza w interfejsach API. Użyj [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zasad, aby przekierować przychodzące żądanie interfejsu API do niestandardowej wewnętrznej bazy danych zamiast domyślnego zaplecza dla tego interfejsu API.

## <a name="benefits-of-backends"></a>Zalety użycia

Niestandardowe zaplecze mają kilka korzyści, w tym:

* Deabstrakcyjne informacje o usłudze wewnętrznej bazy danych, podwyższaniu wykorzystania w interfejsach API i udoskonalonym ładu  
* Łatwe do użycia przez skonfigurowanie zasad przekształcania na istniejącym interfejsie API
* Korzystając z funkcji API Management, można przechowywać wpisy tajne w Azure Key Vault, jeśli [nazwane wartości](api-management-howto-properties.md) są skonfigurowane dla uwierzytelniania dla nagłówka lub parametru zapytania

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj [Service Fabric zaplecza](how-to-configure-service-fabric-backend.md) przy użyciu Azure Portal.
* Frontony można również skonfigurować za pomocą szablonów API Management [interfejsu API REST](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)lub [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

