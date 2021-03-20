---
title: Typowe parametry i nagłówki
description: Parametry i nagłówki wspólne dla wszystkich operacji, które mogą być powiązane z Key Vault zasobami.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83005815"
---
# <a name="common-parameters-and-headers"></a>Typowe parametry i nagłówki

Następujące informacje są wspólne dla wszystkich operacji, które mogą być powiązane z Key Vault zasobami:

- Nagłówek HTTP `Host` musi być zawsze obecny i musi określać nazwę hosta magazynu. Przykład: `Host: contoso.vault.azure.net`. Należy pamiętać, że większość technologii klienta wypełnia `Host` nagłówek identyfikatorem URI. Na przykład `GET https://contoso.vault.azure.net/secrets/mysecret{...}` ustawi wartość `Host` jako `contoso.vault.azure.net` . Oznacza to, że jeśli dostęp do Key Vault za pomocą surowego adresu IP `GET https://10.0.0.23/secrets/mysecret{...}` , na przykład, automatyczna wartość `Host` nagłówka będzie nieprawidłowa i trzeba ręcznie upewnić się, że `Host` Nagłówek zawiera nazwę hosta magazynu.
- Zamień na `{api-version}` wersję interfejsu API w identyfikatorze URI.
- Zamień na `{subscription-id}` Identyfikator subskrypcji w identyfikatorze URI
- Zamień na `{resource-group-name}` grupę zasobów. Aby uzyskać więcej informacji, zobacz Używanie grup zasobów do zarządzania zasobami platformy Azure.
- Zamień na `{vault-name}` nazwę magazynu kluczy w identyfikatorze URI.
- Ustaw nagłówek Content-Type na wartość Application/JSON.
- Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z Azure Active Directory (AAD). Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań Azure Resource Manager](authentication-requests-and-responses.md) .

## <a name="common-error-response"></a>Częsta odpowiedź na błąd
Usługa będzie używać kodów stanu HTTP, aby wskazać powodzenie lub niepowodzenie. Ponadto błędy zawierają odpowiedź w następującym formacie:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nazwa elementu | Typ | Opis |
|---|---|---|
| kod | ciąg | Typ błędu, który wystąpił.|
| message | ciąg | Opis przyczyny błędu. |



## <a name="see-also"></a>Zobacz też
 [Dokumentacja interfejsu API REST Azure Key Vault](/rest/api/keyvault/)
