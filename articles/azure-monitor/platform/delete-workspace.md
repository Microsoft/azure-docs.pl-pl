---
title: Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/30/2020
ms.openlocfilehash: 7ed01a57a4c2a55d777907a6cc14b111fb2086e3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731904"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics

W tym artykule wyjaśniono koncepcję nietrwałego usunięcia obszaru roboczego usługi Azure Log Analytics i odzyskiwania usuniętego obszaru roboczego. 

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego Log Analytics jest wykonywana operacja usuwania nietrwałego umożliwiająca odzyskanie obszaru roboczego, w tym jego danych i podłączonych agentów w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Po okresie niemożliwym do usunięcia nie można odzyskać zasobu obszaru roboczego i jego danych — dane są umieszczane w kolejce do trwałego usunięcia i całkowicie przeczyszczone w ciągu 30 dni. Nazwa obszaru roboczego jest "wydana" i można go użyć do utworzenia nowego obszaru roboczego.

> [!NOTE]
> Jeśli chcesz przesłonić zachowanie nietrwałego usuwania i trwale usunąć obszar roboczy, wykonaj czynności opisane w [obszarze trwałe usuwanie obszaru roboczego](#permanent-workspace-delete).

Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ mogą istnieć ważne dane i konfiguracja, które mogą negatywnie wpłynąć na działanie usługi. Zapoznaj się z agentami, rozwiązaniami i innymi usługami i źródłami platformy Azure, które przechowują dane w Log Analytics, na przykład:

* Rozwiązania do zarządzania
* Azure Automation
* Agenci działający na maszynach wirtualnych z systemem Windows i Linux
* Agenci działający na komputerach z systemem Windows i Linux w danym środowisku
* System Center Operations Manager

Operacja usuwania nietrwałego usuwa zasób obszaru roboczego, a wszystkie powiązane uprawnienia użytkowników są zerwane. Jeśli użytkownicy są powiązani z innymi obszarami roboczymi, mogą nadal korzystać z Log Analytics z tymi innymi obszarami roboczymi.

## <a name="soft-delete-behavior"></a>Zachowanie nietrwałego usuwania

Operacja usuwania obszaru roboczego służy do usuwania obszaru roboczego Menedżer zasobów zasobu, ale jego konfiguracja i dane będą przechowywane przez 14 dni, a jednocześnie zostanie wyświetlony wygląd obszaru roboczego. Wszyscy agenci i System Center Operations Manager grupy zarządzania skonfigurowane do raportowania do obszaru roboczego pozostają w stanie oddzielonym w okresie usuwania nietrwałego. Usługa zapewnia Dodatkowo mechanizm odzyskiwania usuniętego obszaru roboczego, w tym jego danych i połączonych zasobów, głównie cofając operację usuwania.

> [!NOTE] 
> Zainstalowane rozwiązania i połączone usługi, takie jak konto Azure Automation, zostaną trwale usunięte z obszaru roboczego w czasie usuwania i nie można ich odzyskać. Te zmiany należy skonfigurować ponownie po operacji odzyskiwania, aby przełączyć obszar roboczy do wcześniej skonfigurowanego stanu.

Obszar roboczy można usunąć przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)lub w [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Aby się zalogować, przejdź do [Azure Portal](https://portal.azure.com). 
2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń** w górnej części środkowego okienka.
   ![Opcja usuwania z okienka właściwości obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace.png)
4. Gdy zostanie wyświetlone okno komunikatu potwierdzenia z prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **tak**.
   ![Potwierdzenie usunięcia obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby usunąć obszar roboczy, musisz mieć co najmniej *log Analytics uprawnienia współautora* .<br>
Jeśli zostanie wyświetlony komunikat o błędzie *Ta nazwa obszaru roboczego jest już używana* lub *występuje konflikt* podczas tworzenia obszaru roboczego, może to być od:
* Nazwa obszaru roboczego jest niedostępna i jest używana przez kogoś w organizacji lub przez innego klienta.
* Obszar roboczy został usunięty w ciągu ostatnich 14 dni, a jego nazwa jest zarezerwowana dla okresu usuwania nietrwałego. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego w celu utworzenia nowego obszaru roboczego o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usuwanie:<br>
   1. [Odzyskaj](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) obszar roboczy.
   2. [Trwale Usuń](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) obszar roboczy.
   3. Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego.

## <a name="permanent-workspace-delete"></a>Trwałe usuwanie obszaru roboczego
Metoda usuwania nietrwałego może nie mieścić się w niektórych scenariuszach, takich jak programowanie i testowanie, gdzie trzeba powtórzyć wdrożenie z tymi samymi ustawieniami i nazwą obszaru roboczego. W takich przypadkach można trwale usunąć obszar roboczy i "przesłonić" okres usuwania nietrwałego. Operacja usuwania trwałego obszaru roboczego zwalnia nazwę obszaru roboczego i można utworzyć nowy obszar roboczy przy użyciu tej samej nazwy.


> [!IMPORTANT]
> Użyj trwałej operacji usuwania obszaru roboczego z zachowaniem ostrożności od nieodwracalnej i nie będzie możliwe odzyskanie obszaru roboczego i jego danych.

Trwałe usuwanie obszaru roboczego można obecnie wykonać za pośrednictwem interfejsu API REST.

> [!NOTE]
> Wszystkie żądania interfejsu API muszą zawierać Token autoryzacji okaziciela w nagłówku żądania.
>
> Token można uzyskać przy użyciu:
> - [Rejestracje aplikacji](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Przejdź do Azure Portal przy użyciu konsoli dewelopera (F12) w przeglądarce. Zapoznaj się z jedną z **partii?** wystąpień dla ciągu uwierzytelniania w obszarze **nagłówki żądania**. Będzie to miało *autoryzację wzorca: okaziciela <token> *. Skopiuj i Dodaj do wywołania interfejsu API, jak pokazano w przykładach.
> - Przejdź do witryny dokumentacji REST platformy Azure. Naciśnij przycisk **Wypróbuj** na dowolnym interfejsie API, skopiuj token okaziciela i dodaj go do wywołania interfejsu API.
Aby trwale usunąć obszar roboczy, użyj [obszarów roboczych — Usuń]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) wywołanie interfejsu API REST z tagiem Force:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Gdzie "eyJ0eXAiOiJKV1Qi..." przedstawia pełny Token autoryzacji.

## <a name="recover-workspace"></a>Odzyskiwanie obszaru roboczego

Jeśli masz uprawnienia współautora do subskrypcji i grupy zasobów, do której został skojarzony obszar roboczy przed operacją usuwania nietrwałego, możesz odzyskać ją w okresie usuwania nietrwałego, w tym jego dane, konfigurację i połączone agenci. Po okresie usuwania nietrwałego obszar roboczy nie jest możliwy do odzyskania i zostaje przypisany do trwałego usunięcia. Nazwy usuniętych obszarów roboczych są zachowywane w okresie usuwania nietrwałego i nie mogą być używane podczas próby utworzenia nowego obszaru roboczego.  

Aby odzyskać obszar roboczy, można utworzyć obszar roboczy ze szczegółowymi informacjami o usuniętym obszarze roboczym, takich jak *Identyfikator subskrypcji*, *Nazwa grupy zasobów*, *Nazwa obszaru roboczego* i *region*. Jeśli grupa zasobów została również usunięta i nie istnieje, Utwórz grupę zasobów o tej samej nazwie, która została użyta przed usunięciem, a następnie Utwórz obszar roboczy przy użyciu dowolnej z następujących metod: [Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace), [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate).

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Obszar roboczy i wszystkie jego dane zostaną przywrócone po operacji odzyskiwania. Rozwiązania i połączone usługi zostały trwale usunięte z obszaru roboczego, gdy zostało usunięte, i należy je ponownie skonfigurować w celu przeniesienia obszaru roboczego do wcześniej skonfigurowanego stanu. Niektóre dane mogą nie być dostępne dla zapytań po odzyskaniu obszaru roboczego do momentu ponownego zainstalowania skojarzonych rozwiązań, a ich schematy zostaną dodane do obszaru roboczego.

> [!NOTE]
> * Odzyskiwanie obszaru roboczego nie jest obsługiwane w [Azure Portal](https://portal.azure.com). 
> * Ponowne tworzenie obszaru roboczego w okresie usuwania nietrwałego wskazuje, że ta nazwa obszaru roboczego jest już używana. 
> 
