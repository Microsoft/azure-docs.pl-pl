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
ms.openlocfilehash: a543f03cb73d9eae8eaa81eeb3a37fd59e4e6a81
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685804"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Typowe kody błędów dla Azure Key Vault

Kody błędów wymienione w poniższej tabeli mogą zostać zwrócone przez operację w magazynie kluczy Azure

| Kod błędu | Komunikat użytkownika |
|--|--|
| VaultAlreadyExists |  Próba utworzenia nowego magazynu kluczy o określonej nazwie nie powiodła się, ponieważ nazwa jest już używana. Jeśli magazyn kluczy został niedawno usunięty z tą nazwą, nadal może być w stanie usunięte nietrwałe. Możesz sprawdzić, czy istnieje w stanie nietrwałego usunięcia w [tym miejscu](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
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
