---
title: Badanie błędu przy użyciu pulpitu nawigacyjnego
description: Ten dokument zawiera informacje na temat badania błędów przy użyciu pulpitu nawigacyjnego
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9a43318ed935dd1e71b6da75c9f078fbebbd29eb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599016"
---
# <a name="error-investigation-using-the-dashboard"></a>Badanie błędów przy użyciu pulpitu nawigacyjnego

Ta strona zawiera informacje o pulpicie nawigacyjnym łącznika narzędzia ITSM. Ten pulpit nawigacyjny pomoże Ci zbadać stan łącznika narzędzia ITSM.

## <a name="how-to-view-the-dashboard"></a>Jak wyświetlić pulpit nawigacyjny

Aby wyświetlić błędy na pulpicie nawigacyjnym, należy wykonać następujące czynności:

1. W obszarze **wszystkie zasoby** Znajdź pozycję **Servicedesk (*Nazwa obszaru roboczego*)**:

   ![Zrzut ekranu pokazujący najnowsze zasoby w Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

2. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie wybierz pozycję **połączenia narzędzia ITSM**:

   ![Zrzut ekranu pokazujący element menu Narzędzia ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)

3. W obszarze **Podsumowanie** w polu po lewej stronie **Łącznik zarządzania usługami IT** wybierz pozycję **Wyświetl podsumowanie**:

    ![Zrzut ekranu pokazujący podsumowanie widoku.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. W obszarze **Podsumowanie** w polu po lewej stronie **Łącznik zarządzania usługami IT** kliknij wykres:

    ![Zrzut ekranu pokazujący kliknięcie wykresu.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Za pomocą tego pulpitu nawigacyjnego będzie można sprawdzić stan i błędy w łączniku.
    ![Zrzut ekranu przedstawiający stan łącznika.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Elementy pulpitu nawigacyjnego

Pulpit nawigacyjny zawiera informacje o alertach, które zostały wysłane do narzędzia Narzędzia ITSM przy użyciu tego łącznika.
Pulpit nawigacyjny jest podzielony na cztery części:

1. Utworzono element roboczy: wykres i tabela poniżej zawierają liczbę elementów roboczych według typu. Po kliknięciu wykresu lub w tabeli można zobaczyć więcej szczegółów na temat elementów roboczych.
    ![Zrzut ekranu pokazujący utworzony element roboczy.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Komputery, których to dotyczy: tabele zawierają szczegółowe informacje o elementach konfiguracji, które utworzyły elementy konfiguracji.
    Klikając wiersze w tabelach, można uzyskać więcej szczegółów na temat elementów konfiguracji.
    Tabela zawiera ograniczoną liczbę wierszy, jeśli chcesz wyświetlić całą listę, a następnie kliknij pozycję "Zobacz wszystko".
    ![Zrzut ekranu przedstawiający komputery, na których ma wpływ.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Stan łącznika: wykres i tabela poniżej zawierają komunikaty o stanie łącznika. Klikając wykres w wierszach w tabeli, możesz uzyskać więcej szczegółów na temat komunikatów o stanie łącznika.
    Tabela zawiera ograniczoną liczbę wierszy, jeśli chcesz wyświetlić całą listę, a następnie kliknij pozycję "Zobacz wszystko".

    Szczegółowe informacje o komunikatach znajdują się w [tabeli.](itsmc-dashboard-errors.md)

    ![Zrzut ekranu przedstawiający stan łącznika.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Reguły alertów: tabele zawierają informacje o liczbie wykrytych reguł alertów.
    Klikając wiersze w tabelach, można uzyskać więcej szczegółowych informacji o wykrytych regułach.
    Tabela zawiera ograniczoną liczbę wierszy, jeśli chcesz wyświetlić całą listę, a następnie kliknij pozycję "Zobacz wszystko".
    ![Zrzut ekranu pokazujący reguły alertów.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)