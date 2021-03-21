---
title: Azure Virtual Network — koncepcje i najlepsze rozwiązania
description: Dowiedz się więcej na temat pojęć i najlepszych rozwiązań dotyczących platformy Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223622"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Koncepcje i najlepsze rozwiązania dotyczące platformy Azure Virtual Network

W tym artykule opisano kluczowe koncepcje i najlepsze rozwiązania dotyczące usługi Azure Virtual Network (VNet).

## <a name="vnet-concepts"></a>Pojęcia sieci wirtualnej

- **Przestrzeń adresowa:** Podczas tworzenia sieci wirtualnej należy określić niestandardową prywatną przestrzeń adresową IP przy użyciu adresów Public i Private (RFC 1918). Platforma Azure przypisze zasobom w sieci wirtualnej prywatny adres IP z przydzielonej przestrzeni adresowej. Na przykład jeśli maszyna wirtualna jest wdrażana w sieci wirtualnej z przestrzenią adresową 10.0.0.0/16, do maszyny wirtualnej zostanie przypisany prywatny adres IP, taki jak 10.0.0.4.
- **Podsieci:** Podsieci umożliwiają segmentację sieci wirtualnej w co najmniej jedną podsiecią i przydzielenie części przestrzeni adresowej sieci wirtualnej do każdej podsieci. Następnie można wdrożyć zasoby platformy Azure w określonej podsieci. Podobnie jak w przypadku tradycyjnego sieci, podsieci umożliwiają segmentację przestrzeni adresowej sieci wirtualnej na segmenty, które są odpowiednie dla sieci wewnętrznej w organizacji. Zwiększa to również efektywność alokacji adresów. Zasoby w podsieciach można zabezpieczyć za pomocą sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [sieciowe grupy zabezpieczeń](./network-security-groups-overview.md).
- **Regiony**: Sieć wirtualna jest objęta zakresem pojedynczego regionu/lokalizacji; Jednak wiele sieci wirtualnych z różnych regionów można połączyć ze sobą za pomocą komunikacji równorzędnej Virtual Network.
- **Subskrypcja:** Sieć wirtualna jest objęta zakresem subskrypcji. W każdej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) platformy Azure oraz w każdym [regionie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) świadczenia usługi Azure możesz zaimplementować wiele sieci wirtualnych.

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas tworzenia sieci na platformie Azure ważne jest, aby pamiętać o następujących zasadach dotyczących projektowania uniwersalnego:

- Upewnij się, że przestrzenie adresowe nie nakładają się na siebie. Upewnij się, że przestrzeń adresowa sieci wirtualnej (blok CIDR) nie nakłada się na inne zakresy sieci w organizacji.
- Podsieci nie powinny obejmować całej przestrzeni adresowej sieci wirtualnej. Planuj z wyprzedzeniem i Zarezerwuj pewną przestrzeń adresową na przyszłość.
- Zalecane jest używanie mniejszej ilości dużych sieci wirtualnych zamiast wielu małych sieci wirtualnychów. Pozwoli to zapobiec obciążeniu zarządzania.
- Zabezpiecz sieć wirtualną przez przypisanie sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do podsieci poniżej.

## <a name="next-steps"></a>Następne kroki

 Aby rozpocząć korzystanie z sieci wirtualnej, utwórz ją, wdróż w niej kilka maszyn wirtualnych i rozpocznij komunikację pomiędzy maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz przewodnik Szybki start [Tworzenie sieci wirtualnej](quick-create-portal.md).