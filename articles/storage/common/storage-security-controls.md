---
title: Mechanizmy zabezpieczeń
titleSuffix: Azure Storage
description: Lista kontrolna zabezpieczeń do oceny usługi Azure Storage.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082360"
---
# <a name="security-controls-for-azure-storage"></a>Środki kontroli zabezpieczeń usługi Azure Storage

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Yes |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Yes | Zobacz [Szyfrowanie usługi magazynowania przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Yes | Obsługa standardowych mechanizmów HTTPS/TLS.  Użytkownicy mogą również szyfrować dane przed ich przesłaniem do usługi. |
| Szyfrowane wywołania interfejsu API| Yes |  |

## <a name="network"></a>Sieć

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Yes |  |
| Obsługa tagów usług| Yes | Zobacz [omówienie tagów usługi platformy Azure, aby](../../virtual-network/service-tags-overview.md) uzyskać więcej informacji na temat tagów usługi obsługiwanych przez usługę Azure Storage. |
| Obsługa wtrysku sieci wirtualnej| Nie dotyczy |  |
| Izolacja sieci i obsługa zapory| Yes | |
| Wymuszone wsparcie tunelowania| Nie dotyczy |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Yes | Metryki usługi Azure Monitor|
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania | Yes | Dziennik aktywności usługi Azure Resource Manager |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Dzienniki diagnostyczne usługi.|

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Usługa Azure Active Directory, klucz udostępniony, token dostępu współdzielonego. |
| Autoryzacja| Yes | Obsługa autoryzacji za pośrednictwem rbac, list ACL POSIX i tokenów SAS |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Yes | Obsługa wersji dostawcy zasobów za pośrednictwem interfejsów API usługi Azure Resource Manager |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)