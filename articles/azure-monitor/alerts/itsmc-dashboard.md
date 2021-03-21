---
title: Badanie błędów przy użyciu pulpitu nawigacyjnego ITSMC
description: Dowiedz się, jak zbadać błędy przy użyciu pulpitu nawigacyjnego łącznik zarządzania usługami IT.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039533"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Badanie błędów przy użyciu pulpitu nawigacyjnego ITSMC

Ten artykuł zawiera informacje o pulpicie nawigacyjnym łącznik zarządzania usługami IT (ITSMC). Pulpit nawigacyjny ułatwia badanie stanu łącznika.

## <a name="view-errors"></a>Wyświetl błędy

Aby wyświetlić błędy na pulpicie nawigacyjnym:

1. Wybierz pozycję **wszystkie zasoby**, a następnie Znajdź przycisk **Servicedesk (*Nazwa obszaru roboczego*)**.

   ![Zrzut ekranu pokazujący zasoby w usługach platformy Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie wybierz pozycję **połączenia narzędzia ITSM**:

   ![Zrzut ekranu pokazujący Wybieranie połączeń narzędzia ITSM w obszarze źródła danych w miejscu pracy.](media/itsmc-overview/add-new-itsm-connection.png)

3. W obszarze **Podsumowanie** w obszarze **Łącznik zarządzania usługami IT** wybierz pozycję **Wyświetl podsumowanie**:

   ![Zrzut ekranu pokazujący przycisk Wyświetl podsumowanie.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Gdy wykres pojawi się w obszarze **Łącznik zarządzania usługami IT** , zaznacz go:

   ![Zrzut ekranu, który pokazuje wybór grafu.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Zostanie wyświetlony pulpit nawigacyjny. Służy do przeglądania stanu i błędów w łączniku.
   
   ![Zrzut ekranu przedstawiający stan łącznika na pulpicie nawigacyjnym.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Omówienie elementów pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera informacje na temat alertów, które zostały wysłane do narzędzia Narzędzia ITSM za pomocą tego łącznika. Pulpit nawigacyjny jest podzielony na cztery części.

### <a name="created-work-items"></a>Utworzone elementy robocze 

W obszarze **utworzone elementy robocze** wykres i tabela poniżej zawierają liczbę elementów roboczych według typu. Jeśli wybierzesz wykres lub tabelę, możesz zobaczyć więcej szczegółów na temat elementów roboczych.

![Zrzut ekranu pokazujący utworzony element roboczy.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Zmodyfikowane komputery 

W obszarze **komputery** , których dotyczy problem, w tabeli znajduje się lista komputerów i skojarzonych z nimi elementów roboczych. Po wybraniu wierszy w tabelach można uzyskać więcej szczegółów na temat komputerów.

Tabela zawiera ograniczoną liczbę wierszy. Jeśli chcesz zobaczyć wszystkie wiersze, wybierz pozycję **Zobacz wszystko**.

![Zrzut ekranu przedstawiający komputery, których to dotyczy.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Stan łącznika 

W obszarze **stan łącznika** wykres i tabela poniżej zawierają komunikaty o stanie łącznika. Po wybraniu grafu lub wierszy w tabeli można uzyskać więcej szczegółów na temat komunikatów.

Tabela zawiera ograniczoną liczbę wierszy. Jeśli chcesz zobaczyć wszystkie wiersze, wybierz pozycję **Zobacz wszystko**.

Aby dowiedzieć się więcej o komunikatach w tabeli, zobacz [ten artykuł](itsmc-dashboard-errors.md).

![Zrzut ekranu przedstawiający stan łącznika.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Zasady alertów 

W obszarze **reguły alertów** tabela zawiera informacje o liczbie wykrytych reguł alertów. Wybierając wiersze w tabeli, można uzyskać więcej szczegółowych informacji o wykrytych regułach.
    
Tabela zawiera ograniczoną liczbę wierszy. Jeśli chcesz zobaczyć wszystkie wiersze, wybierz pozycję **Zobacz wszystko**.

![Zrzut ekranu pokazujący reguły alertów.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
