---
title: Typowe kody błędów dla Azure Key Vault | Microsoft Docs
description: Typowe kody błędów dla Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 9ae13b88d767e43c425ceb86d0be455cebc0e6ac
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462528"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Typowe kody błędów dla Azure Key Vault

Kody błędów wymienione w poniższej tabeli mogą zostać zwrócone przez operację w magazynie kluczy Azure

| Kod błędu | Komunikat użytkownika |
|--|--|
| VaultAlreadyExists |  Próba utworzenia nowego magazynu kluczy o określonej nazwie nie powiodła się, ponieważ nazwa jest już używana. Jeśli magazyn kluczy został niedawno usunięty z tą nazwą, nadal może być w stanie usunięte nietrwałe. Możesz sprawdzić, czy istnieje w stanie nietrwałego usunięcia w [tym miejscu](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
| VaultNameNotValid |  Nazwa magazynu powinna składać się z 24 znaków alfanumerycznych i zaczynać się od alfabetu |
| AccessDenied |  W zasadach dostępu nie ma uprawnień do wykonania tej operacji. |
| ForbiddenByFirewall |  Adres klienta nie jest autoryzowany, a element wywołujący nie jest zaufaną usługą. |
| ConflictError |  Żądasz wielu operacji na tym samym elemencie.  |
| RegionNotSupported |  Określony region platformy Azure nie jest obsługiwany w przypadku tego zasobu. |
| SkuNotSupported |  Określony typ jednostki SKU nie jest obsługiwany w przypadku tego zasobu. |
| ResourceNotFound |  Nie znaleziono określonego zasobu platformy Azure. |
| ResourceGroupNotFound | Nie znaleziono określonej grupy zasobów platformy Azure. |
| CertificateExpired |  Sprawdź datę wygaśnięcia i okres ważności certyfikatu. |


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [przewodnikiem dla deweloperów Azure Key Vault](developers-guide.md)
- Przeczytaj więcej [na temat uwierzytelniania w magazynie kluczy](authentication.md)