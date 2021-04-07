---
title: Interfejs API REST usługi Azure App Configuration — autoryzacja
description: Strony referencyjne na potrzeby autoryzacji za pomocą interfejsu API REST usługi Azure App Configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932646"
---
# <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do procedury używanej do określenia uprawnień, które obiekt wywołujący podczas wykonywania żądania. Istnieje wiele modeli autoryzacji. Model autoryzacji używany dla żądania zależy od używanej metody [uwierzytelniania](./rest-api-authentication-index.md) . Poniżej wymieniono modele autoryzacji.

## <a name="hmac"></a>FUNKCJE

Model [modelu autoryzacji](./rest-api-authorization-hmac.md) skojarzony z uwierzytelnianiem HMAC dzieli uprawnienia na tylko do odczytu lub do odczytu i zapisu. Szczegóły można znaleźć na stronie [autoryzacji HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Model autoryzacji](./rest-api-authorization-azure-ad.md) skojarzony z uwierzytelnianiem Azure Active Directory (Azure AD) używa usługi Azure RBAC do kontrolowania uprawnień. Aby uzyskać szczegółowe informacje, zobacz stronę [autoryzacja usługi Azure AD](./rest-api-authorization-azure-ad.md) .
