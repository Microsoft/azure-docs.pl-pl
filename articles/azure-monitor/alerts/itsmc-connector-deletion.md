---
title: Usuwanie łącznika narzędzia ITSM i skojarzonej z nim akcji
description: Ten artykuł zawiera wyjaśnienie sposobu usuwania łącznika narzędzia ITSM i skojarzonych z nim grup akcji.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036490"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Usuwanie nieużywanych łączników narzędzia ITSM

Proces usuwania nieużywanego łącznika zawiera 2 fazy:

1. Usuwanie skojarzonych akcji: należy usunąć wszystkie akcje skojarzone z łącznikiem narzędzia ITSM. Należy to zrobić, aby nie mieć akcji bez łącznika, które mogą powodować błędy w subskrypcji.

2. Usuwanie nieużywanego łącznika narzędzia ITSM.

## <a name="deletion-of-the-associated-actions"></a>Usuwanie skojarzonych akcji

1. Aby znaleźć grupę akcji, należy przejść do zrzutu ekranu "Monitoruj"  ![ wyboru monitora.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Wybierz zrzut ekranu "alerty"  ![ dla wybranych alertów.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Wybierz zrzut ekranu "Zarządzaj akcjami"  ![ w obszarze Zarządzanie akcjami.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Zaznacz wszystkie łączniki narzędzia ITSM połączone z Cherwellem  ![ zrzutu ekranu łączników narzędzia ITSM, które są połączone z Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Usuń  ![ zrzut ekranu grupy akcji usuwania grupy akcji.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Usuwanie nieużywanego łącznika narzędzia ITSM

1. Należy wyszukać i wybrać pozycję "Servicedesk" w górnym pasku wyszukiwania  ![ zrzut ekranu wyszukiwania, a następnie wybrać pozycję "Servicedesk" La.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Wybierz pozycję "połączenia narzędzia ITSM" i wybierz zrzut ekranu łącznika Cherwell  ![ z łączników CHERWELL narzędzia ITSM.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Wybierz pozycję "Usuń"  ![ zrzut ekranu przedstawiający usunięcie łącznika narzędzia ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)
