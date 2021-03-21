---
title: Kierowanie rozwiązań do monitorowania w Azure Monitor | Microsoft Docs
description: Ukierunkowane rozwiązania do monitorowania umożliwiają ograniczenie rozwiązań monitorowania do określonego zestawu agentów.  W tym artykule opisano, jak utworzyć konfigurację zakresu i zastosować ją do rozwiązania.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728573"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Kierowanie rozwiązań do monitorowania w Azure Monitor (wersja zapoznawcza)
Po dodaniu rozwiązania do monitorowania do subskrypcji jest ono automatycznie wdrażane domyślnie dla wszystkich agentów systemu Windows i Linux połączonych z obszarem roboczym Log Analytics.  Możesz chcieć zarządzać kosztami i ograniczyć ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów.  W tym artykule opisano sposób korzystania z funkcji **określania celu rozwiązania** , która umożliwia zastosowanie zakresu do rozwiązań.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Jak skierować rozwiązanie do rozwiązania
Istnieją trzy kroki umożliwiające kierowanie rozwiązania zgodnie z opisem w poniższych sekcjach. 


### <a name="1-create-a-computer-group"></a>1. Utwórz grupę komputerów
Należy określić komputery, które mają zostać dołączone do zakresu przez utworzenie [grupy komputerów](../logs/computer-groups.md) w Azure monitor.  Grupa komputerów może opierać się na zapytaniach dziennika lub zaimportowaniu z innych źródeł, takich jak Active Directory lub grupy programu WSUS. Jak [opisano poniżej](#solutions-and-agents-that-cant-be-targeted), tylko komputery, które są podłączone bezpośrednio do Azure monitor zostaną uwzględnione w zakresie.

Po utworzeniu grupy komputerów w obszarze roboczym zostanie ona uwzględniona w konfiguracji zakresu, którą można zastosować do jednego lub kilku rozwiązań.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Utwórz konfigurację zakresu
 **Konfiguracja zakresu** zawiera co najmniej jedną grupę komputerów i można ją zastosować do jednego lub kilku rozwiązań. 
 
 Utwórz konfigurację zakresu przy użyciu następującego procesu.  

 1. W Azure Portal przejdź do **obszaru log Analytics obszary robocze** i wybierz swój obszar roboczy.
 2. We właściwościach obszaru roboczego w obszarze **źródła danych obszaru roboczego** wybierz pozycję **konfiguracje zakresów**.
 3. Kliknij przycisk **Dodaj** , aby utworzyć nową konfigurację zakresu.
 4. Wpisz **nazwę** konfiguracji zakresu.
 5. Kliknij pozycję **Wybierz grupy komputerów**.
 6. Wybierz utworzoną grupę komputerów i opcjonalnie inne grupy, które mają zostać dodane do konfiguracji.  Kliknij pozycję **Wybierz**.  
 6. Kliknij przycisk **OK** , aby utworzyć konfigurację zakresu. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Zastosuj konfigurację zakresu do rozwiązania.
Gdy masz konfigurację zakresu, możesz ją zastosować do jednego lub kilku rozwiązań.  Należy pamiętać, że podczas konfigurowania pojedynczego zakresu z wieloma rozwiązaniami każde rozwiązanie może korzystać tylko z jednej konfiguracji zakresu.

Zastosuj konfigurację zakresu przy użyciu następującego procesu.  

 1. W Azure Portal przejdź do **obszaru log Analytics obszary robocze** i wybierz swój obszar roboczy.
 2. We właściwościach obszaru roboczego wybierz pozycję **rozwiązania**.
 3. Kliknij rozwiązanie, które chcesz zakres.
 4. We właściwościach rozwiązania w obszarze **źródła danych obszaru roboczego** wybierz pozycję **cel rozwiązania**.  Jeśli ta opcja jest niedostępna, [to rozwiązanie nie może być wskazywane](#solutions-and-agents-that-cant-be-targeted).
 5. Kliknij pozycję **Dodaj konfigurację zakresu**.  Jeśli masz już zastosowaną konfigurację do tego rozwiązania, ta opcja będzie niedostępna.  Przed dodaniem nowej konfiguracji należy usunąć istniejącą.
 6. Kliknij utworzoną konfigurację zakresu.
 7. Obejrzyj **stan** konfiguracji, aby upewnić się, że jest ona wyświetlana **pomyślnie**.  Jeśli stan wskazuje na błąd, kliknij wielokropek z prawej strony konfiguracji i wybierz pozycję **Edytuj konfigurację zakresu** , aby wprowadzić zmiany.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Rozwiązania i agenci, których nie można kierować
Poniżej przedstawiono kryteria dla agentów i rozwiązań, które nie mogą być używane z celem rozwiązania.

- Ukierunkowane rozwiązanie dotyczy tylko rozwiązań wdrażanych w agentach.
- Ukierunkowane rozwiązanie dotyczy tylko rozwiązań dostarczonych przez firmę Microsoft.  Nie dotyczy to rozwiązań [utworzonych przez siebie ani partnerów](./solutions.md).
- Można filtrować tylko agentów, którzy łączą się bezpośrednio z Azure Monitor.  Rozwiązania zostaną automatycznie wdrożone na wszystkich agentach, które są częścią połączonej grupy zarządzania Operations Manager, niezależnie od tego, czy są one uwzględnione w konfiguracji zakresu.

### <a name="exceptions"></a>Wyjątki
Obiektów docelowych rozwiązania nie można używać z następującymi rozwiązaniami, nawet jeśli spełniają one podane kryteria.

- Ocena Agent Health

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat monitorowania rozwiązań, w tym rozwiązań dostępnych do zainstalowania w środowisku, w temacie [Dodawanie rozwiązań monitorowania log Analytics platformy Azure do obszaru roboczego](solutions.md).
- Dowiedz się więcej na temat tworzenia grup komputerów w [grupach komputerów w zapytaniach dziennika Azure monitor](../logs/computer-groups.md).
