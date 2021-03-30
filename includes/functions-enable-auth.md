---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648956"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Włącz App Service uwierzytelnianie/autoryzację

Platforma App Service umożliwia korzystanie z usługi Azure Active Directory (AAD) i wielu dostawców tożsamości innych firm do uwierzytelniania klientów. Ta strategia służy do implementowania niestandardowych reguł autoryzacji dla funkcji i można korzystać z informacji o użytkowniku z kodu funkcji. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](../articles/app-service/overview-authentication-authorization.md) i [Praca z tożsamościami klientów](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Uwierzytelnianie żądań za pomocą usługi Azure API Management (APIM)

APIM zapewnia różne opcje zabezpieczeń interfejsu API dla żądań przychodzących. Aby dowiedzieć się więcej, zobacz [API Management zasady uwierzytelniania](../articles/api-management/api-management-authentication-policies.md). Mając APIM na miejscu, można skonfigurować aplikację funkcji do akceptowania żądań tylko z adresu IP wystąpienia usługi APIM. Aby dowiedzieć się więcej, zobacz temat [Ograniczenia adresów IP](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
