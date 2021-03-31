---
title: Przegląd zestawów dostępności
description: Dowiedz się więcej o zestawach dostępności na platformie Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510543"
---
# <a name="availability-sets-overview"></a>Przegląd zestawów dostępności

Ten artykuł zawiera omówienie funkcji dostępności maszyn wirtualnych platformy Azure.

## <a name="what-is-an-availability-set"></a>Co to jest zestaw dostępności? 

Zestaw dostępności to logiczne grupowanie maszyn wirtualnych, które umożliwia platformie Azure zrozumienie sposobu kompilacji aplikacji w celu zapewnienia nadmiarowości i dostępności. Zalecamy, aby co najmniej dwie maszyny wirtualne zostały utworzone w ramach zestawu dostępności, aby zapewnić wysoką dostępność aplikacji i spełnić warunki [umowy SLA na 99,95%](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Dla samego zestawu dostępności opłaty są naliczane tylko za każde utworzone wystąpienie maszyny wirtualnej.

## <a name="how-do-availability-sets-work"></a>Jak działają zestawy dostępności?
Każda maszyna wirtualna w zestawie dostępności ma przypisaną **domenę aktualizacji** i **domenę błędów** przez podstawową platformę Azure. Dany zestaw dostępności ma pięć domyślnie przypisanych domen aktualizacji, których użytkownik nie może konfigurować (następnie można zwiększyć liczbę wystąpień usługi Resource Manager, aby oferowała do 20 domen aktualizacji). Umożliwia to wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane równocześnie. Jeśli w pojedynczym zestawie dostępności skonfigurowano więcej niż pięć maszyn wirtualnych, szósta maszyna wirtualna jest umieszczana w tej samej domenie aktualizacji, co pierwsza maszyna wirtualna, siódma — w tej samej domenie aktualizacji co druga maszyna wirtualna itd. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie. Domena aktualizacji po ponownym rozruchu otrzymuje 30 minut na odzyskanie sprawności zanim konserwacja zostanie zainicjowana w innej domenie aktualizacji.

Domeny błędów definiują grupę maszyn wirtualnych, które korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Domyślnie maszyny wirtualne skonfigurowane w ramach zestawu dostępności są oddzielane do trzech domen błędów na potrzeby wdrożeń Menedżer zasobów. Umieszczenie maszyn wirtualnych w zestawie dostępności nie chroni aplikacji przed błędami związanymi z systemem operacyjnym lub daną aplikacją, ale ogranicza wpływ potencjalnych awarii sprzętu fizycznego, awarii sieci i przerw w dostawie prądu.

![Zestawy dostępności](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


Maszyny wirtualne korzystające z usługi [Azure Managed Disks](./faq-for-disks.md) są przydzielane do domen błędów dysków zarządzanych w przypadku korzystania z zarządzanego zestawu dostępności. Dzięki takiemu dopasowaniu wszystkie dyski zarządzane dołączone do maszyny wirtualnej działają w tej samej domenie błędów dysku zarządzanego. 

W zarządzanym zestawie dostępności można tworzyć tylko maszyny wirtualne z użyciem dysków zarządzanych. Liczba domen błędów dysku zarządzanego zależy od regionu — dwie lub trzy domeny błędów dysku zarządzanego na region. 

![Zarządzany zestaw dostępności](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>Następne kroki
Możesz teraz rozpocząć korzystanie z tych funkcji dostępności i nadmiarowości podczas kompilowania środowiska platformy Azure. Aby uzyskać informacje o najlepszych rozwiązaniach, zobacz [Azure availability best practices](/azure/architecture/checklist/resiliency-per-service) (Najlepsze rozwiązania dotyczące dostępności platformy Azure).

