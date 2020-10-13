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
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877000"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Typowe kody błędów dla Azure Key Vault

Kody błędów wymienione w poniższej tabeli mogą zostać zwrócone przez operację w magazynie kluczy Azure

| Kod błędu | Komunikat użytkownika |
|--|--|
| VaultAlreadyExists |  Określony magazyn kluczy już istnieje (w stanie nieusuniętym lub w innej subskrypcji). |
| VaultNameNotValid |  Nazwa magazynu powinna składać się z 24 znaków alfanumerycznych i zaczynać się od alfabetu |
| AccessDenied |  W zasadach dostępu nie ma uprawnień do wykonania tej operacji. |
| ForbiddenByFirewall |  Adres klienta nie jest autoryzowany, a element wywołujący nie jest zaufaną usługą. |
| ConflictError |  Żądasz wielu operacji na tym samym elemencie.  |
| RegionNotSupported |  Określony region platformy Azure nie jest obsługiwany w przypadku tego zasobu. |
| SkuNotSupported |  Określony typ jednostki SKU nie jest obsługiwany w przypadku tego zasobu. |
| ResourceNotFound |  Nie znaleziono określonego zasobu platformy Azure. |
| CertificateExpired |  Sprawdź datę wygaśnięcia i okres ważności certyfikatu. |


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [przewodnikiem dla deweloperów Azure Key Vault](developers-guide.md)
- Przeczytaj więcej [na temat uwierzytelniania w magazynie kluczy](authentication.md)
