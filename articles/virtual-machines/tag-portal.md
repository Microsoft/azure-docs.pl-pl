---
title: Jak oznaczyć maszynę wirtualną przy użyciu Azure Portal
description: Dowiedz się więcej na temat tagowania maszyny wirtualnej przy użyciu Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594975"
---
# <a name="tagging-a-vm-using-the-portal"></a>Tagowanie maszyny wirtualnej przy użyciu portalu

W tym artykule opisano sposób dodawania tagów do maszyny wirtualnej przy użyciu portalu. Tagi to zdefiniowane przez użytkownika pary klucz/wartość, które mogą być umieszczone bezpośrednio w ramach zasobu lub grupy zasobów. Platforma Azure obsługuje obecnie do 50 tagów na zasób i grupę zasobów. Tagi mogą być umieszczane na zasobie w momencie tworzenia lub dodawane do istniejącego zasobu.


1. Przejdź do maszyny wirtualnej w portalu.
1. W obszarze **podstawy** wybierz **pozycję kliknij tutaj, aby dodać tagi**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Zrzut ekranu przedstawiający sekcję podstawowe strony maszyny wirtualnej.":::

1. Dodaj wartość w obszarze **Nazwa** i **wartość** , a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Zrzut ekranu przedstawiający stronę dodawania pary klucz wartość jako tag.":::



**Następne kroki**

- Aby dowiedzieć się więcej o znakowaniu zasobów platformy Azure, zobacz [Azure Resource Manager omówienie](../azure-resource-manager/management/overview.md) i [Używanie tagów do organizowania zasobów platformy Azure](../azure-resource-manager/management/tag-resources.md).
- Aby dowiedzieć się, jak tagi mogą ułatwić zarządzanie użyciem zasobów platformy Azure, zobacz [Opis rachunku na korzystanie z platformy Azure](../cost-management-billing/understand/review-individual-bill.md) i [Uzyskiwanie szczegółowych informacji o zużyciu zasobów Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).