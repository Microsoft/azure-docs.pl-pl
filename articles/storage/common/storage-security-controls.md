---
title: Mechanizmy zabezpieczeń
titleSuffix: Azure Storage
description: Wyświetlanie list kontrolnych kontroli zabezpieczeń na potrzeby oceny usługi Azure Storage. Obszary objęte ochroną danych, siecią, monitorowaniem, rejestrowaniem, tożsamością i konfiguracją.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715713"
---
# <a name="security-controls-for-azure-storage"></a>Kontrolki zabezpieczeń dla usługi Azure Storage

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Obsługa standardowych mechanizmów protokołu HTTPS/TLS.  Użytkownicy mogą także szyfrować dane przed ich przesłaniem do usługi. |
| Wywołania interfejsu API są szyfrowane| Tak |  |

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa tagów usługi| Tak | Zobacz [Omówienie tagów usługi platformy Azure](../../virtual-network/service-tags-overview.md) , aby uzyskać więcej informacji na temat tagów usługi obsługiwanych przez usługę Azure Storage. |
| Obsługa iniekcji sieci wirtualnej| Nie dotyczy |  |
| Izolacja sieci i obsługa zapory| Tak | |
| Obsługa tunelowania wymuszonego| Nie dotyczy |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Metryki Azure Monitor|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania | Tak | Dziennik aktywności platformy Azure |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Dzienniki zasobów Azure Monitor |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Azure Active Directory, klucz współużytkowany, token dostępu współdzielonego. |
| Autoryzacja| Tak | Obsługa autoryzacji za pośrednictwem usług Azure RBAC, list kontroli dostępu POSIX i tokenów SAS |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługa wersji dostawcy zasobów przy użyciu interfejsów API Azure Resource Manager |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).