---
title: Mechanizmy kontroli zabezpieczeń usługi Azure Key Vault
description: Lista kontrolna zabezpieczeń do oceny usługi Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429865"
---
# <a name="security-controls-for-azure-key-vault"></a>Mechanizmy kontroli zabezpieczeń usługi Azure Key Vault

W tym artykule dokumentuje zabezpieczenia wbudowane w usługę Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | Korzystanie z punktów końcowych usługi sieci wirtualnej (VNet). |
| Obsługa wtrysku sieci wirtualnej| Nie |  |
| Obsługa izolacji sieci i zaporowania| Tak | Korzystanie z reguł zapory sieci wirtualnej. |
| Wymuszone wsparcie tunelowania| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Korzystanie z analizy dzienników. |
| Rejestrowanie i inspekcja płaszczyzny sterowania/zarządzania| Tak | Korzystanie z analizy dzienników. |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Korzystanie z analizy dzienników. |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za pośrednictwem usługi Azure Active Directory. |
| Autoryzacja| Tak | Korzystanie z zasad dostępu do magazynu kluczy. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Wszystkie obiekty są szyfrowane. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klient kontroluje wszystkie klucze w magazynie kluczy. Po określeniu kluczy kopii zapasowej sprzętowego modułu zabezpieczeń (HSM) moduł HSM moduł HSM moduł HSM moduł HSM chroni klucz, certyfikat lub klucz tajny. |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Cała komunikacja odbywa się za pośrednictwem szyfrowanych wywołań INTERFEJSU API |
| Szyfrowane wywołania interfejsu API| Tak | Korzystanie z protokołu HTTPS. |

## <a name="access-controls"></a>Kontrole dostępu

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Sterowanie/Kontrola dostępu do płaszczyzny zarządzania | Tak | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontrola dostępu do płaszczyzny danych (na każdym poziomie usług) | Tak | Zasady dostępu do magazynu kluczy |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)