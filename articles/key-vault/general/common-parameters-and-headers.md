---
title: Typowe parametry i nagłówki
description: Parametry i nagłówki wspólne dla wszystkich operacji, które można wykonać w odniesieniu do Key Vault zasobów.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749874"
---
# <a name="common-parameters-and-headers"></a>Typowe parametry i nagłówki

Poniższe informacje są wspólne dla wszystkich operacji, które można wykonać w odniesieniu do Key Vault zasobów:

- Nagłówek HTTP `Host` musi być zawsze obecny i musi określać nazwę hosta magazynu. Przykład: `Host: contoso.vault.azure.net`. Należy pamiętać, że większość technologii klienta wypełnia `Host` nagłówek z URI. Na przykład dla `GET https://contoso.vault.azure.net/secrets/mysecret{...}` klasy zostanie ustawiona `Host` wartość `contoso.vault.azure.net` . Oznacza to, że w przypadku uzyskania dostępu do usługi Key Vault przy użyciu nieprzetworzonym adresem IP, takim jak , automatyczna wartość nagłówka będzie nieprawidłowa i trzeba będzie ręcznie sprawdzić, czy nagłówek zawiera nazwę `GET https://10.0.0.23/secrets/mysecret{...}` `Host` hosta `Host` magazynu.
- `{api-version}`Zamień na wersję api-version w numerze URI.
- Zastąp `{subscription-id}` identyfikatorem subskrypcji w identyfikatorze URI
- Zastąp `{resource-group-name}` grupę zasobami. Aby uzyskać więcej informacji, zobacz Using Resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure).
- Zastąp `{vault-name}` element nazwą magazynu kluczy w adresie URI.
- Ustaw nagłówek Content-Type na wartość application/json.
- Ustaw nagłówek Authorization na wartość JSON Web Token uzyskaną z Azure Active Directory (AAD). Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie Azure Resource Manager](authentication-requests-and-responses.md) żądań.

## <a name="common-error-response"></a>Często spotykany błąd odpowiedzi
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
| message | ciąg | Opis tego, co było przyczyną błędu. |



## <a name="see-also"></a>Zobacz też
 [Azure Key Vault REST API Reference](/rest/api/keyvault/)
