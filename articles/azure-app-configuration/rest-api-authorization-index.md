---
title: Interfejs API REST usługi Azure App Configuration — autoryzacja
description: Strony referencyjne na potrzeby autoryzacji za pomocą interfejsu API REST usługi Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424178"
---
# <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do procedury używanej do określenia uprawnień, które obiekt wywołujący podczas wykonywania żądania. Istnieje wiele modeli autoryzacji. Model autoryzacji używany dla żądania zależy od używanej metody [uwierzytelniania](./rest-api-authentication-index.md) . Poniżej wymieniono modele autoryzacji.

## <a name="hmac"></a>FUNKCJE

Model [modelu autoryzacji](./rest-api-authorization-hmac.md) skojarzony z uwierzytelnianiem HMAC dzieli uprawnienia na tylko do odczytu lub do odczytu i zapisu. Szczegóły można znaleźć na stronie [autoryzacji HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Model autoryzacji](./rest-api-authorization-azure-ad.md) skojarzony z uwierzytelnianiem Azure Active Directory (Azure AD) używa usługi Azure RBAC do kontrolowania uprawnień. Aby uzyskać szczegółowe informacje, zobacz stronę [autoryzacja usługi Azure AD](./rest-api-authorization-azure-ad.md) .
