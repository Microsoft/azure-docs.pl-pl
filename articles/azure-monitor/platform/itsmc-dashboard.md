---
title: Badanie błędów przy użyciu pulpitu nawigacyjnego
description: Ten dokument zawiera informacje o błędach na pulpicie nawigacyjnym ITSMC.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089279"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Badanie błędów przy użyciu pulpitu nawigacyjnego ITSMC

Ten artykuł zawiera informacje o pulpicie nawigacyjnym łącznik zarządzania usługami IT (ITSMC). Pulpit nawigacyjny ułatwia badanie stanu ITSMC.

## <a name="view-the-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego

Wykonaj następujące kroki, aby otworzyć pulpit nawigacyjny.

1. Wybierz pozycję **wszystkie zasoby**, a następnie Znajdź przycisk **Servicedesk (*Nazwa obszaru roboczego*)**.

   ![Zrzut ekranu pokazujący zasoby w usługach platformy Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. W lewym okienku wybierz pozycję **źródła danych obszaru roboczego**, a następnie wybierz pozycję **połączenia narzędzia ITSM**.

   ![Zrzut ekranu pokazujący Wybieranie połączeń narzędzia ITSM w obszarze źródła danych w miejscu pracy.](media/itsmc-overview/add-new-itsm-connection.png)

1. W sekcji **Podsumowanie** wybierz pozycję **Wyświetl podsumowanie** , aby wyświetlić wykres podsumowania.

    ![Zrzut ekranu pokazujący opcję wyświetlania podsumowania w sekcji Podsumowanie.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Wybierz wykres w sekcji **Podsumowanie** , aby otworzyć pulpit nawigacyjny.

    ![Zrzut ekranu, który pokazuje Wybieranie wykresu podsumowania.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Przejrzyj pulpit nawigacyjny pod kątem stanu i wszystkich błędów w łączniku.
    ![Zrzut ekranu przedstawiający pulpit nawigacyjny.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Omówienie elementów pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera informacje o alertach, które zostały wysłane do narzędzia Narzędzia ITSM przy użyciu tego łącznika.

Pulpit nawigacyjny jest podzielony na cztery sekcje:

- **Utworzone elementy robocze**: wykres i tabela przedstawiają liczbę elementów roboczych według typu. Wybierz wykres lub tabelę, aby dowiedzieć się więcej o elementach roboczych.
      ![Zrzut ekranu przedstawiający sekcję elementy robocze, które zostały utworzone.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **Komputery**, na których ma to wpływ: tabela zawiera szczegółowe informacje o elementach konfiguracji, które utworzyły elementy robocze.
      Aby uzyskać więcej informacji o elementach konfiguracji, wybierz wiersze w tabelach.
      Tabela zawiera ograniczoną liczbę wierszy. Aby wyświetlić całą listę, wybierz pozycję **Zobacz wszystko**.
      ![Zrzut ekranu przedstawiający sekcję komputery, których dotyczy problem.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Stan łącznika**: wykres i tabela zawierają informacje o stanie łącznika. Wybierz wykres lub komunikaty z tabeli, aby uzyskać więcej szczegółów. Tabela zawiera ograniczoną liczbę wierszy. Aby wyświetlić całą listę, wybierz pozycję **Zobacz wszystko**.
      ![Zrzut ekranu pokazujący sekcję stan łącznika.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Reguły alertów**: Ta sekcja zawiera informacje o liczbie wykrytych reguł alertów. Wybierz wiersze w tabelach, aby uzyskać więcej szczegółów na temat wykrytych reguł. Tabela zawiera ograniczoną liczbę wierszy. Aby wyświetlić całą listę, wybierz pozycję **Zobacz wszystko**.
      ![Zrzut ekranu pokazujący sekcję reguły alertów.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Następne kroki

Sprawdź [typowe błędy stanu łącznika](itsmc-dashboard-errors.md).
