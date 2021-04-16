---
title: Omówienie zestawów dostępności
description: Dowiedz się więcej o zestawach dostępności na platformie Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: b4e9f106354915fe40a4bcf9b60bc35443345202
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530584"
---
# <a name="availability-sets-overview"></a>Omówienie zestawów dostępności

Ten artykuł zawiera omówienie funkcji dostępności maszyn wirtualnych platformy Azure.

## <a name="what-is-an-availability-set"></a>Co to jest zestaw dostępności? 

Zestaw dostępności to logiczne grupowanie maszyn wirtualnych, które umożliwia platformie Azure zrozumienie sposobu kompilacji aplikacji w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby w zestawie dostępności utworzyć co najmniej dwie maszyny wirtualne, aby zapewnić wysoką dostępność aplikacji i spełnić wymagania umowy SLA platformy Azure na [poziomie 99,95%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/) Sam zestaw dostępności nie jest kosztem. Płacisz tylko za każde wystąpienie maszyny wirtualnej, które tworzysz.

## <a name="how-do-availability-sets-work"></a>Jak działają zestawy dostępności?
Każda maszyna wirtualna w zestawie dostępności ma przypisaną domenę **aktualizacji** i **domenę** błędów przez podstawową platformę Azure. Każdy zestaw dostępności można skonfigurować z maksymalnie trzema domenami błędów i dwudziestoma domenami aktualizacji. Domeny aktualizacji wskazują grupy maszyn wirtualnych i podstawowy sprzęt fizyczny, który można uruchomić ponownie w tym samym czasie. Jeśli w pojedynczym zestawie dostępności skonfigurowano więcej niż pięć maszyn wirtualnych, szósta maszyna wirtualna jest umieszczana w tej samej domenie aktualizacji, co pierwsza maszyna wirtualna, siódma — w tej samej domenie aktualizacji co druga maszyna wirtualna itd. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie. Domena aktualizacji po ponownym rozruchu otrzymuje 30 minut na odzyskanie sprawności zanim konserwacja zostanie zainicjowana w innej domenie aktualizacji.

Domeny błędów definiują grupę maszyn wirtualnych, które korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Domyślnie maszyny wirtualne skonfigurowane w zestawie dostępności są rozdzielone między maksymalnie trzy domeny błędów. Umieszczenie maszyn wirtualnych w zestawie dostępności nie chroni aplikacji przed błędami związanymi z systemem operacyjnym lub daną aplikacją, ale ogranicza wpływ potencjalnych awarii sprzętu fizycznego, awarii sieci i przerw w dostawie prądu.

:::image type="content" source="./media/virtual-machines-common-manage-availability/ud-fd-configuration.png" alt-text="Diagram przedstawiający różne klastry obliczeniowe podzielone na domeny błędów i w tych domenach błędów mamy wiele domen aktualizacji":::

Maszyny wirtualne są również dostosowane do domen błędów dysku. To wyrównanie zapewnia, że wszystkie dyski zarządzane dołączone do maszyny wirtualnej znajdują się w tych samych domenach błędów. 

W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. 

:::image type="content" source="./media/virtual-machines-common-manage-availability/md-fd-updated.png" alt-text="Diagram przedstawiający sposób wyrównywania domen błędów dla dysków i maszyn wirtualnych.":::

## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](/azure/architecture/checklist/resiliency-per-service) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).

