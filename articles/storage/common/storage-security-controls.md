---
title: Mechanizmy zabezpieczeń
titleSuffix: Azure Storage
description: Lista kontrolna zabezpieczeń służąca do oceniania usługi Azure Storage.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 96fde15eb5071e157fedcff6154e6b0635a34721
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128034"
---
# <a name="security-controls-for-azure-storage"></a>Kontrolki zabezpieczeń dla usługi Azure Storage

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Yes |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Yes | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Obsługa standardowych mechanizmów protokołu HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich przesłaniem do usługi. |
| Wywołania interfejsu API są szyfrowane| Yes |  |

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes |  |
| Obsługa tagów usługi| Yes | Zobacz [Omówienie tagów usługi platformy Azure](../../virtual-network/service-tags-overview.md) , aby uzyskać więcej informacji na temat tagów usługi obsługiwanych przez usługę Azure Storage. |
| Obsługa iniekcji sieci wirtualnej| Nie dotyczy |  |
| Izolacja sieci i obsługa zapory| Yes | |
| Obsługa tunelowania wymuszonego| Nie dotyczy |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Metryki Azure Monitor|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania | Yes | Dziennik aktywności platformy Azure |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Dzienniki zasobów Azure Monitor |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Autoryzacja| Yes | Obsługa autoryzacji za pośrednictwem list kontroli dostępu RBAC, POSIX i tokenów SAS |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Obsługa wersji dostawcy zasobów przy użyciu interfejsów API Azure Resource Manager |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).