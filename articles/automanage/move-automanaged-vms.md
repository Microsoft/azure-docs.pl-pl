---
title: Przenoszenie maszyny wirtualnej usługi Azure automanage w różnych regionach
description: Dowiedz się, jak przenieść maszynę wirtualną z autozarządzaniem między regionami
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101650469"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Przenoszenie maszyny wirtualnej usługi Azure automanage do innego regionu
W tym artykule opisano sposób utrzymania włączonej autozarządzania na maszynie wirtualnej (VM) podczas przenoszenia jej do innego regionu. Możesz chcieć przenieść maszyny wirtualne do innego regionu z kilku powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, spełnić wymagania wewnętrzne zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności. Przenoszenie tych maszyn wirtualnych może być obecnie autozarządzane i może być konieczne ich Autozarządzanie po przeniesieniu.

## <a name="prerequisites"></a>Wymagania wstępne
* Upewnij się, że region docelowy jest [obsługiwany przez Autozarządzanie](./automanage-virtual-machines.md#prerequisites).
* Upewnij się, że region obszaru roboczego Log Analytics, region konta usługi Automation i region docelowy to wszystkie regiony obsługiwane przez mapowania regionów w [tym miejscu](../automation/how-to/region-mappings.md).

## <a name="prepare-your-automanaged-vms-for-moving"></a>Przygotuj zarządzane przez siebie maszyny wirtualne do przeniesienia
Wyłącz Autozarządzanie na maszynach wirtualnych zarządzanych przez siebie. Możesz to zrobić, wybierając maszyny wirtualne w bloku Autozarządzanie, a następnie klikając pozycję **Wyłącz Autozarządzanie** w bloku Autozarządzanie.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Przenieś swoje zarządzane przez siebie maszyny wirtualne i ponownie włącz Autozarządzanie
Aby uzyskać szczegółowe informacje na temat przenoszenia maszyn wirtualnych, zobacz ten [artykuł](../resource-mover/tutorial-move-region-virtual-machines.md).

Po przeniesieniu maszyn wirtualnych między regionami można ponownie włączyć na nich funkcję Autozarządzanie. Szczegóły są dostępne [tutaj](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o usłudze Azure automanage](./automanage-virtual-machines.md)
* [Wyświetlanie często zadawanych pytań dotyczących usługi Azure automanage](./faq.md)