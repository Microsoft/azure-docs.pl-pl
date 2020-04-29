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
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430879"
---
# <a name="common-parameters-and-headers"></a>Typowe parametry i nagłówki

Następujące informacje są wspólne dla wszystkich operacji, które mogą być powiązane z Key Vault zasobami:

- Zamień `{api-version}` na wersję interfejsu API w identyfikatorze URI.
- Zamień `{subscription-id}` na identyfikator subskrypcji w identyfikatorze URI
- Zamień `{resource-group-name}` na grupę zasobów. Aby uzyskać więcej informacji, zobacz Używanie grup zasobów do zarządzania zasobami platformy Azure.
- Zamień `{vault-name}` na nazwę magazynu kluczy w identyfikatorze URI.
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
