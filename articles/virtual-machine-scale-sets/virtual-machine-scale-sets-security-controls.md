---
title: Kontrolki zabezpieczeń dla usługi Azure Virtual Machine Scale Sets
description: Lista kontrolna zabezpieczeń na potrzeby oceny Virtual Machine Scale Sets platformy Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029420"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Kontrolki zabezpieczeń dla usługi Azure Virtual Machine Scale Sets

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | |
| Obsługa iniekcji sieci wirtualnej| Tak | |
| Izolacja sieci i obsługa zapór| Tak |  |
| Obsługa tunelowania wymuszonego| Tak | Zobacz [Konfigurowanie wymuszonego tunelowania przy użyciu Azure Resource Manager model wdrażania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/tutorial-monitor.md) oraz [monitorowanie i aktualizowanie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines/windows/tutorial-monitor.md). |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak |  |
| Autoryzacja| Tak |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zobacz [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Usługa Azure Virtual Machines obsługuje szyfrowanie [ExpressRoute](../expressroute/index.yml) i wirtualne. Zobacz [szyfrowanie w trakcie przesyłania na maszynach wirtualnych](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; Zobacz [Azure Disk Encryption dla Virtual Machine Scale Sets](disk-encryption-overview.md)|
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem protokołów HTTPS i TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak |  | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
