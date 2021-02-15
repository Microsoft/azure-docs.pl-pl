---
title: Śledzenie działania czujnika
description: Oś czasu zdarzeń przedstawia oś czasu aktywności wykrytych w sieci, w tym alerty i akcje zarządzania alertami, zdarzenia sieciowe i operacje użytkownika, takie jak logowanie użytkownika i usuwanie użytkownika.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: b8a7679521096cf3d98e8f55748eb0398fdc4ab8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523604"
---
# <a name="track-sensor-activity"></a>Śledzenie działania czujnika

## <a name="event-timeline"></a>Oś czasu zdarzeń

Oś czasu zdarzeń przedstawia oś czasu działania wykrytego przez czujnik. Na przykład:

  - Alerty i akcje zarządzania alertami

  - Zdarzenia sieciowe

  - Operacje użytkownika, takie jak logowanie użytkownika i usuwanie użytkownika

Oś czasu zdarzeń zawiera chronologiczny widok zdarzeń, które wystąpiły w sieci. Oś czasu zdarzeń umożliwia zrozumienie i analizę łańcucha zdarzeń, które poprzedzają atak lub zdarzenie, które pomagają w badaniu i dowodowych.

> [!NOTE]
> *Administratorzy* i *analityki zabezpieczeń* mogą wykonać procedury opisane w tej sekcji.

Aby wyświetlić dzienniki zdarzeń:

- Z menu po stronie wybierz pozycję **oś czasu zdarzenia**.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Wyświetl zdarzenia na osi czasu zdarzeń.":::

Oprócz wyświetlania zdarzeń wykrywanych przez czujnik można ręcznie dodawać zdarzenia do osi czasu. Ten proces jest przydatny, jeśli zdarzenie wystąpi w systemie zewnętrznym, ale ma wpływ na sieć i ważne jest, aby zarejestrować zdarzenie i przedstawić je jako część osi czasu.

Aby ręcznie dodać zdarzenia:

- Wybierz pozycję **Utwórz zdarzenie**.

Aby wyeksportować informacje dziennika zdarzeń do pliku CSV:

- Wybierz pozycję **Eksportuj**.

## <a name="filter-the-event-timeline"></a>Filtrowanie osi czasu zdarzeń

Przefiltruj oś czasu, aby wyświetlić urządzenia i interesujące Cię zdarzenia.

Aby odfiltrować oś czasu:

1. Wybierz pozycję **filtry zaawansowane**.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Użyj okna Zaawansowane filtry zdarzeń, aby odfiltrować zdarzenia.":::

2. Ustaw filtry zdarzeń w następujący sposób:

   - **Uwzględnij adres**: Wyświetla zdarzenia dla określonych urządzeń.

   - **Wyklucz adres**: Ukryj zdarzenia dla konkretnych urządzeń.

   - **Uwzględnij typy zdarzeń**: Wyświetlaj określone typy zdarzeń.

   - **Wyklucz typy zdarzeń**: Ukryj określone typy zdarzeń.

   - **Grupa urządzeń**: Wybierz grupę urządzeń, która została zdefiniowana w mapie urządzeń. Prezentowane są tylko zdarzenia z tej grupy.

3. Wybierz pozycję **Wyczyść wszystko** , aby wyczyścić wszystkie wybrane filtry.

4. Wyszukaj **tylko alerty**, **alerty i powiadomienia** lub **wszystkie zdarzenia**.

5. Wybierz pozycję **Wybierz datę** , aby wybrać konkretną datę. Wybierz dzień, godzinę i minutę. Wyświetlane są zdarzenia z wybranego przedziału czasu.

6.  Wybierz **operacje użytkownika** , aby uwzględnić lub wykluczyć zdarzenia operacji użytkownika.

7.  Wybierz strzałkę (**V**), aby wyświetlić więcej informacji o zdarzeniu:

    - Wybierz powiązane alerty (jeśli istnieją), aby wyświetlić szczegółowy opis alertu.

    - Wybierz urządzenie, aby wyświetlić je na mapie.

    - Wybierz pozycję **Filtruj zdarzenia według powiązanych urządzeń** , jeśli chcesz filtrować według powiązanych urządzeń.

    - Wybierz **plik PCAP** , aby pobrać plik PCAP (jeśli istnieje) zawierający przechwycenie pakietu dla całej sieci w określonym czasie. 
    
      Plik PCAP zawiera informacje techniczne, które mogą pomóc inżynierom sieci ustalić dokładne parametry zdarzenia. Plik PCAP można analizować za pomocą analizatora protokołów sieciowych, takiego jak Wireshark, aplikacji typu open source.

## <a name="next-steps"></a>Następne kroki

[Wyświetlanie alertów](how-to-view-alerts.md)
