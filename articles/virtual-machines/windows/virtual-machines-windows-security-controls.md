---
title: Mechanizmy kontroli zabezpieczeń dla maszyn wirtualnych systemu Azure windows
description: Lista kontrolna zabezpieczeń do oceny maszyn wirtualnych systemu Azure Windows
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455346"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Mechanizmy kontroli zabezpieczeń dla maszyn wirtualnych systemu Windows

Ten artykuł dokumentuje mechanizmy kontroli zabezpieczeń wbudowane w maszyny wirtualne systemu Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak | |
| Obsługa wtrysku sieci wirtualnej| Tak | |
| Obsługa izolacji sieci i zapory sieciowej| Tak |  |
| Wymuszone wsparcie tunelowania| Tak | Zobacz [Konfigurowanie tunelowania wymuszonego przy użyciu modelu wdrażania usługi Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | [Monitoruj i aktualizuj maszynę wirtualną systemu Windows na platformie Azure](tutorial-monitoring.md). |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak |  |
| Autoryzacja| Tak |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zobacz [Szyfrowanie dysków wirtualnych na maszynie Wirtualnej systemu Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Maszyny wirtualne platformy Azure obsługuje [szyfrowanie usługi ExpressRoute](/azure/expressroute) i sieci wirtualnej. Zobacz [Szyfrowanie podczas przesyłania na maszynach wirtualnych](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klucze zarządzane przez klienta to obsługiwany scenariusz szyfrowania platformy Azure; zobacz [omówienie szyfrowania platformy Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowane wywołania interfejsu API| Tak | Za pośrednictwem protokołu HTTPS i TLS. |



## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak |  | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)
