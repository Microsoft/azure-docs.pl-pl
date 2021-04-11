---
title: Rozwiązywanie problemów dotyczących uruchamiania/zatrzymywania maszyn wirtualnych (Podgląd)
description: W tym artykule opisano sposób rozwiązywania problemów występujących w funkcji uruchamiania/zatrzymywania maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111799"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Rozwiązywanie typowych problemów dotyczących uruchamiania/zatrzymywania maszyn wirtualnych (wersja zapoznawcza)

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów, które mogą wystąpić podczas próby instalacji i konfiguracji maszyn wirtualnych i uruchamiania (wersja zapoznawcza). Aby uzyskać ogólne informacje, zobacz [Omówienie uruchamiania/zatrzymywania maszyn wirtualnych](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Ogólna weryfikacja i rozwiązywanie problemów

W tej sekcji opisano sposób rozwiązywania ogólnych problemów z scenariuszami harmonogramów i identyfikowanie głównej przyczyny.

### <a name="azure-dashboard"></a>Pulpit nawigacyjny platformy Azure

Możesz rozpocząć od przejrzenia udostępnionego pulpitu nawigacyjnego platformy Azure. Udostępniony pulpit nawigacyjny platformy Azure wdrożony jako część uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) to szybki i łatwy sposób sprawdzenia stanu każdej operacji wykonanej na maszynach wirtualnych. Zapoznaj się z **ostatnio podjętymi akcjami na kafelku maszyny wirtualne** , aby zobaczyć wszystkie ostatnie operacje wykonywane na maszynach wirtualnych. Istnieje kilka opóźnień, około 5 minut, aby dane były wyświetlane w raporcie w miarę ściągania danych z zasobu Application Insights.

### <a name="logic-apps"></a>Logic Apps

W zależności od tego, który Logic Apps został włączony do obsługi scenariusza uruchamiania/zatrzymywania, możesz przejrzeć jego historię uruchamiania, aby pomóc w ustaleniu, dlaczego scenariusz zaplanowanego uruchamiania/zamykania nie został pomyślnie ukończony dla co najmniej jednej docelowej maszyny wirtualnej. Aby dowiedzieć się, jak szczegółowo zapoznać się z tematami, zobacz [Logic Apps Run History](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Azure Storage

Możesz przejrzeć szczegóły operacji wykonanych na maszynach wirtualnych, które są zapisywane w tabeli **requestsstoretable** na koncie usługi Azure Storage używanym do uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza). Aby wyświetlić te rekordy, wykonaj następujące czynności.

1. Przejdź do konta magazynu w Azure Portal i wybierz pozycję * * Eksplorator usługi Storage (wersja zapoznawcza) w okienku po lewej stronie.
1. Wybierz pozycję **tabele** , a następnie wybierz pozycję **requeststoretable**.
1. Każdy rekord w tabeli reprezentuje akcję uruchamiania/zatrzymania wykonywaną względem maszyny wirtualnej platformy Azure w oparciu o zakres docelowy zdefiniowany w scenariuszu aplikacji logiki. Wyniki można filtrować według jednej z właściwości rekordu (na przykład SYGNATURy CZASowej, akcji lub TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure Functions

Możesz przejrzeć szczegóły dotyczące najnowszych wywołań dla dowolnej Azure Functions odpowiedzialnych za uruchomienie maszyny wirtualnej i zatrzymanie jej wykonywania. Najpierw zapoznaj się z przepływem wykonania.

Przepływ wykonywania dla scenariusza **zaplanowanego** i **sekwencjonowanego** jest kontrolowany przez tę samą funkcję. Schemat ładunku to określenie, który scenariusz jest wykonywany. W **zaplanowanym** scenariuszu przepływ wykonywania jest **zaplanowaną** kolejką HTTP > **VirtualMachineRequestOrchestrator** Queue > kolejką **VirtualMachineRequestExecutor** .

W aplikacji logiki **zaplanowana** funkcja http jest wywoływana z schematem ładunku. Po odebraniu żądania przez **zaplanowaną** funkcję http wysyła ona informacje do funkcji kolejki programu **Orchestrator** , która z kolei tworzy kilka kolejek dla każdej maszyny wirtualnej w celu wykonania tej akcji.

Wykonaj następujące kroki, aby wyświetlić szczegóły wywołania.

1. W Azure Portal przejdź do **Azure Functions**.
1. Wybierz aplikację funkcji do uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza) z listy.
1. Wybierz pozycję **funkcje** w okienku po lewej stronie.
1. Na liście zobaczysz kilka funkcji skojarzonych z każdym scenariuszem. Wybierz **zaplanowaną** funkcję http.
1. Wybierz pozycję **monitor** z okienka po lewej stronie.
1. Wybierz najnowszy ślad wykonania, aby wyświetlić szczegóły wywołania i sekcję wiadomości dla szczegółowego rejestrowania.
1. Powtórz te same kroki dla każdej funkcji opisanej w części Recenzja przepływu wykonania.

Aby dowiedzieć się więcej na temat monitorowania Azure Functions, zobacz [analizowanie Azure Functions telemetrii w Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat monitorowania Azure Functions i aplikacji logiki:

* [Monitoruj Azure Functions](../../azure-functions/functions-monitoring.md).

* [Jak skonfigurować monitorowanie dla Azure Functions](../../azure-functions/configure-monitoring.md).

* [Monitoruj aplikacje logiki](../../logic-apps/monitor-logic-apps.md).