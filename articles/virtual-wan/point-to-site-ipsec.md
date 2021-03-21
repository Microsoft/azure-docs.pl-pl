---
title: Zasady protokołu IPsec dla sieci wirtualnej punkt-lokacja
titleSuffix: Azure Virtual WAN
description: Dowiedz się więcej o zasadach łączności IPsec punkt-lokacja usługi Azure Virtual Machines.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746987"
---
# <a name="point-to-site-ipsec-policies"></a>Zasady IPsec punkt-lokacja

W tym artykule przedstawiono kombinacje obsługiwanych zasad protokołu IPsec dla połączenia sieci VPN typu punkt-lokacja w wirtualnej sieci WAN platformy Azure.

## <a name="default-ipsec-policies"></a>Domyślne zasady protokołu IPsec

W poniższej tabeli przedstawiono domyślne parametry protokołu IPsec dla połączeń sieci VPN typu punkt-lokacja. Te parametry są automatycznie wybierane przez wirtualną bramę sieci VPN typu punkt-lokacja w przypadku utworzenia profilu punkt-lokacja.

| Ustawienie | Parametry |
|--- |--- |
| Faza 1 — szyfrowanie IKE | AES256 |
| Integralność IKE fazy 1 |  SHA256 |
| Grupa DH | DHGroup24 |
| Faza 2 — szyfrowanie protokołu IPsec | GCMAES256|
| Faza 2 — integralność protokołu IPsec | GCMAES25 |
| Grupa PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Niestandardowe zasady protokołu IPsec

Podczas pracy z niestandardowymi zasadami protokołu IPsec należy pamiętać o następujących wymaganiach:

* **IKE** — dla fazy 1 usługi IKE można wybrać dowolny parametr z szyfrowania IKE oraz dowolny parametr z integralności IKE oraz dowolny parametr z grupy DH.
* **IPSec** — dla fazy 2 protokołu IPSec, można wybrać dowolny parametr z szyfrowania IPSec oraz dowolny parametr z integralności IPSec i PFS. W przypadku GCM dowolnego z parametrów szyfrowania IPsec lub integralności IPsec należy użyć tego samego algorytmu. Na przykład jeśli wybrano opcję GCMAES128 dla szyfrowania IPsec, należy również wybrać opcję GCMAES128 dla integralności IPsec.  

W poniższej tabeli przedstawiono dostępne parametry protokołu IPsec dla połączeń sieci VPN typu punkt-lokacja.

| Ustawienie | Parametry |
|--- |--- |
| Faza 1 — szyfrowanie IKE | AES128, AES256, GCMAES128, GCMAES256 |
| Integralność IKE fazy 1 |  SHA256, SHA384 |
| Grupa DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Faza 2 — szyfrowanie protokołu IPsec | GCMAES128, GCMAES256, SHA256|
| Faza 2 — integralność protokołu IPsec | GCMAES128, GCMAES256 |
| Grupa PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Następne kroki

Zobacz [jak utworzyć połączenie punkt-lokacja](virtual-wan-point-to-site-portal.md)

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz temat [często zadawane pytania dotyczące wirtualnej sieci WAN](virtual-wan-faq.md).
