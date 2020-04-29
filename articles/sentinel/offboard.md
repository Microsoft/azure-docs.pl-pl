---
title: Usuń wskaźnik na platformie Azure | Microsoft Docs
description: Jak usunąć wystąpienie usługi Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77581688"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Usuń wskaźnik platformy Azure z obszaru roboczego

Jeśli nie chcesz już korzystać z funkcji wskaźnikowej platformy Azure, w tym artykule wyjaśniono, jak usunąć go z obszaru roboczego.

## <a name="how-to-remove-azure-sentinel"></a>Jak usunąć wskaźnik na platformie Azure

Postępuj zgodnie z tym procesem, aby usunąć wskaźnik platformy Azure z obszaru roboczego:

1. Przejdź do obszaru **wskaźnikowego platformy Azure**, a następnie kliknij pozycję **Ustawienia**, a następnie wybierz kartę **Usuń wskaźnik na platformie Azure**.

1. Przed usunięciem usługi Azure wskaźnikiem, Użyj pól wyboru, aby poinformować nas o tym, dlaczego są usuwane.

1. Wybierz pozycję **Usuń wskaźnik platformy Azure z obszaru roboczego**.
    
    ![Usuń rozwiązanie SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Co się stanie w tle?

Po usunięciu rozwiązania wskaźnik platformy Azure zajmie do 48 godzin, aby zakończyć pierwszą fazę procesu usuwania.

Po zidentyfikowaniu rozłączenia rozpocznie się proces odłączania.

**Konfiguracja tych łączników została usunięta:**
-   Office 365

-   AWS

-   Alerty zabezpieczeń usług firmy Microsoft (Azure ATP, Microsoft Cloud App Security, w tym Cloud Discovery do raportowania IT w tle, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Analiza zagrożeń

-   Typowe dzienniki zabezpieczeń (w tym dzienniki CEF, Barracuda i dziennik systemu) (Jeśli masz Azure Security Center, dzienniki będą nadal zbierane).

-   Zdarzenia zabezpieczeń systemu Windows (Jeśli masz Azure Security Center, dzienniki będą nadal zbierane).

W ciągu pierwszych 48 godzin reguły danych i analityczne (w tym konfiguracja automatyzacji w czasie rzeczywistym) nie będą już dostępne ani Queryable na platformie Azure.

**Po 30 dniach następujące zasoby zostaną usunięte:**

-   Zdarzenia (w tym metadane badania)

-   Reguły analityczne

-   Zakładki

Elementy PlayBook, zapisane skoroszyty, zapisane zapytania dotyczące polowań i notesy nie są usuwane. **Niektóre mogą zostać przerwane ze względu na usunięte dane. Można je usunąć ręcznie.**

Po usunięciu usługi istnieje 30-dniowy okres prolongaty, w którym można ponownie włączyć rozwiązanie, a Twoje dane i reguły analityczne zostaną przywrócone, ale skonfigurowane łączniki, które zostały odłączone, muszą być ponownie połączone.

> [!NOTE]
> Jeśli usuniesz rozwiązanie, subskrypcja będzie nadal rejestrowana w dostawcy zasobów platformy Azure. **Można je usunąć ręcznie.**




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób usuwania usługi Azure wskaźnikowej. Jeśli zmienisz zdanie i chcesz je zainstalować ponownie:
- Rozpocznij pracę [na płycie wskaźnikowej platformy Azure](quickstart-onboard.md).
