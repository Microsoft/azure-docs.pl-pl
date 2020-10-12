---
title: Kontrolki zabezpieczeń dla Azure Key Vault
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81429865"
---
# <a name="security-controls-for-azure-key-vault"></a>Kontrolki zabezpieczeń dla Azure Key Vault

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Korzystanie z punktów końcowych usługi Virtual Network (VNet). |
| Obsługa iniekcji sieci wirtualnej| Nie |  |
| Izolacja sieci i obsługa zapór| Tak | Korzystanie z reguł zapory sieci wirtualnej. |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Przy użyciu Log Analytics. |
| Rejestrowanie i inspekcja w płaszczyźnie kontroli/zarządzania| Tak | Przy użyciu Log Analytics. |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Przy użyciu Log Analytics. |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Autoryzacja| Tak | Korzystanie z zasad dostępu Key Vault. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Wszystkie obiekty są zaszyfrowane. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klient kontroluje wszystkie klucze w Key Vault. Gdy są określone klucze z zabezpieczeniami sprzętowego modułu zabezpieczeń (HSM), moduł HSM poziomu 2 trybu FIPS chroni klucz, certyfikat lub wpis tajny. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Cała komunikacja odbywa się za pośrednictwem szyfrowanych wywołań interfejsu API |
| Wywołania interfejsu API są szyfrowane| Tak | Przy użyciu protokołu HTTPS. |

## <a name="access-controls"></a>Kontrole dostępu

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Kontrole dostępu do płaszczyzny kontroli/zarządzania | Tak | Kontrola dostępu oparta na rolach (RBAC) przy użyciu usługi Azure Resource Manager |
| Kontrola dostępu do płaszczyzny danych (na każdym poziomie usługi) | Tak | Zasady dostępu Key Vault |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).