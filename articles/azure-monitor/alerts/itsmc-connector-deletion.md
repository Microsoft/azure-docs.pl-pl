---
title: Usuwanie nieużywanych łączników ITSM
description: Ten artykuł zawiera wyjaśnienie sposobu usuwania łączników ITSM i skojarzonych z nimi grup akcji.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387942"
---
# <a name="delete-unused-itsm-connectors"></a>Usuwanie nieużywanych łączników ITSM

Proces usuwania nieużywanych łączników zarządzanie usługami IT (ITSM) ma dwie fazy. Usuwasz wszystkie akcje skojarzone z łącznikiem ITSM, a następnie usuwasz sam łącznik. Najpierw usuwasz akcje, ponieważ akcje bez łącznika mogą powodować błędy w subskrypcji.

## <a name="delete-associated-actions"></a>Usuwanie skojarzonych akcji

1. W Azure Portal wybierz pozycję **Monitoruj.**
  
    ![Zrzut ekranu przedstawiający wybór opcji Monitoruj.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Wybierz pozycję **Alerty.**
   
    ![Zrzut ekranu przedstawiający wybór opcji Alerty.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Wybierz **pozycję Zarządzaj akcjami.**
   
    ![Zrzut ekranu przedstawiający wybór opcji Zarządzaj akcjami.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Wybierz grupę akcji skojarzoną z łącznikiem ITSM, który chcesz usunąć. W tym artykule użyto przykładu łącznika Cherwell.
   
    ![Zrzut ekranu przedstawiający akcje skojarzone z łącznikiem Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Przejrzyj informacje, a następnie wybierz pozycję **Usuń grupę akcji.**

    ![Zrzut ekranu przedstawiający informacje o grupie akcji i przycisk usuwania grupy.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Usuwanie łącznika

1. Na pasku wyszukiwania wyszukaj **usługę servicedesk**. Następnie wybierz **pozycję ServiceDesk** na liście zasobów.

    ![Zrzut ekranu przedstawiający wyszukiwanie i wybieranie aplikacji ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Wybierz **pozycję Itsm Connections (Połączenia ITSM),** a następnie wybierz łącznik Cherwell.

    ![Zrzut ekranu przedstawiający łącznik Cherwell I T S M.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Wybierz pozycję **Usuń**.

    ![Zrzut ekranu przedstawiający przycisk usuwania dla łącznika T S M.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznikiem ITSM](./itsmc-resync-servicenow.md)
