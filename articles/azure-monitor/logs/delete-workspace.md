---
title: Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/20/2020
ms.openlocfilehash: 83a64e3348d4af768c56609df3df5c9194ec5af1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030872"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics

W tym artykule wyjaśniono koncepcję nietrwałego usunięcia obszaru roboczego usługi Azure Log Analytics i odzyskiwania usuniętego obszaru roboczego.

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego Log Analytics jest wykonywana operacja usuwania nietrwałego umożliwiająca odzyskanie obszaru roboczego, w tym jego danych i podłączonych agentów w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Po okresie usuwania nietrwałego zasób obszaru roboczego i jego dane nie są odzyskiwalne i umieszczane w kolejce w celu pełnego przeczyszczenia w ciągu 30 dni. Nazwa obszaru roboczego jest "wydana" i można go użyć do utworzenia nowego obszaru roboczego.

> [!NOTE]
> Jeśli chcesz zastąpić zachowanie nietrwałego usuwania i trwale usunąć obszar roboczy, wykonaj czynności opisane w [obszarze trwałe usuwanie obszaru roboczego](#permanent-workspace-delete).

Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ mogą istnieć ważne dane i konfiguracja, które mogą negatywnie wpłynąć na działanie usługi. Sprawdź, jakie agenci, rozwiązania i inne usługi platformy Azure przechowują swoje dane w Log Analytics, na przykład:

* Rozwiązania do zarządzania
* Azure Automation
* Agenci działający na maszynach wirtualnych z systemem Windows i Linux
* Agenci działający na komputerach z systemem Windows i Linux w danym środowisku
* System Center Operations Manager

Operacja usuwania nietrwałego usuwa zasób obszaru roboczego, a wszystkie powiązane uprawnienia użytkowników są zerwane. Jeśli użytkownicy są powiązani z innymi obszarami roboczymi, mogą nadal korzystać z Log Analytics z tymi innymi obszarami roboczymi.

## <a name="soft-delete-behavior"></a>Zachowanie usuwania nietrwałego

Operacja usuwania obszaru roboczego służy do usuwania obszaru roboczego Menedżer zasobów zasobu, ale jego konfiguracja i dane będą przechowywane przez 14 dni, a jednocześnie zostanie wyświetlony wygląd obszaru roboczego. Wszyscy agenci i System Center Operations Manager grupy zarządzania skonfigurowane do raportowania do obszaru roboczego pozostają w stanie oddzielonym w okresie usuwania nietrwałego. Usługa zapewnia Dodatkowo mechanizm odzyskiwania usuniętego obszaru roboczego, w tym jego danych i połączonych zasobów, głównie cofając operację usuwania.

> [!NOTE] 
> Zainstalowane rozwiązania i połączone usługi, takie jak konto Azure Automation, zostaną trwale usunięte z obszaru roboczego w czasie usuwania i nie można ich odzyskać. Te zmiany należy skonfigurować ponownie po operacji odzyskiwania, aby przełączyć obszar roboczy do wcześniej skonfigurowanego stanu.

Obszar roboczy można usunąć przy użyciu [programu PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace), [interfejsu API REST](/rest/api/loganalytics/workspaces/delete)lub w [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń**  w górnej części środkowego okienka.
4. Zostanie wyświetlona strona potwierdzenia, która pokazuje pozyskiwanie danych w obszarze roboczym w ciągu ostatniego tygodnia. 
5. Jeśli chcesz trwale usunąć obszar roboczy, usuwając opcję w celu późniejszego odzyskania, zaznacz pole wyboru **Usuń obszar roboczy trwale** .
6. Wpisz nazwę obszaru roboczego, który chcesz potwierdzić, a następnie kliknij przycisk **Usuń**.

   ![Potwierdzenie usunięcia obszaru roboczego](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Trwałe usuwanie obszaru roboczego
Metoda usuwania nietrwałego może nie mieścić się w niektórych scenariuszach, takich jak programowanie i testowanie, gdzie trzeba powtórzyć wdrożenie z tymi samymi ustawieniami i nazwą obszaru roboczego. W takich przypadkach można trwale usunąć obszar roboczy i "przesłonić" okres usuwania nietrwałego. Operacja usuwania trwałego obszaru roboczego zwalnia nazwę obszaru roboczego i można utworzyć nowy obszar roboczy przy użyciu tej samej nazwy.

> [!IMPORTANT]
> Użyj trwałej operacji usuwania obszaru roboczego z zachowaniem ostrożności od nieodwracalnej i nie będzie możliwe odzyskanie obszaru roboczego i jego danych.

Aby trwale usunąć obszar roboczy przy użyciu Azure Portal, zaznacz pole wyboru **Usuń obszar roboczy trwałe** przed kliknięciem przycisku **Usuń** .

Aby trwale usunąć obszar roboczy przy użyciu programu PowerShell, Dodaj tag "-ForceDelete", aby trwale usunąć obszar roboczy. Opcja "-ForceDelete" jest obecnie dostępna z AZ. OperationalInsights 2.3.0 lub nowszą. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Odzyskiwanie obszaru roboczego
Po przypadkowe lub celowe usunięciu obszaru roboczego Log Analytics usługa umieści obszar roboczy w stanie usuwania nietrwałego, dzięki czemu nie będzie dostępny dla żadnej operacji. Nazwa usuniętego obszaru roboczego jest zachowywana w okresie usuwania nietrwałego i nie można jej używać do tworzenia nowego obszaru roboczego. Po okresie usuwania nieodzyskiwalnego obszar roboczy nie jest możliwy do odzyskania, jest zaplanowany do trwałego usunięcia oraz jego nazwy wydanej i można go użyć do utworzenia nowego obszaru roboczego.

Obszar roboczy możesz odzyskać w okresie usuwania nietrwałego, w tym jego dane, konfigurację i połączone agenci. Musisz mieć uprawnienia współautora do subskrypcji i grupy zasobów, w której znajduje się obszar roboczy przed operacją usuwania nietrwałego. Odzyskiwanie obszaru roboczego jest wykonywane przez ponowne utworzenie obszaru roboczego Log Analytics zawierającego szczegóły usuniętego obszaru roboczego, w tym:

- Identyfikator subskrypcji
- Nazwa grupy zasobów
- Nazwa obszaru roboczego
- Region (Region)

> [!IMPORTANT]
> Jeśli obszar roboczy został usunięty w ramach operacji usuwania grupy zasobów, musisz najpierw utworzyć ponownie grupę zasobów.

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**. Zostanie wyświetlona lista obszarów roboczych, które znajdują się w wybranym zakresie.
3. Kliknij przycisk **Odzyskaj** w lewym górnym menu, aby otworzyć stronę z obszarami roboczymi w stanie usuwania nietrwałego, który może zostać odzyskany.

   ![Zrzut ekranu przedstawiający ekran Log Analytics obszarów roboczych w Azure Portal z funkcją Odzyskaj wyróżnioną na pasku menu.](media/delete-workspace/recover-menu.png)

4. Wybierz obszar roboczy, a następnie kliknij przycisk **Odzyskaj** , aby odzyskać ten obszar roboczy.

   ![Zrzut ekranu okna dialogowego Odzyskiwanie usuniętych Log Analytics obszarów roboczych w Azure Portal z wyróżnionym obszarem roboczym i wybranym przyciskiem Odzyskaj.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Obszar roboczy i wszystkie jego dane zostaną przywrócone po operacji odzyskiwania. Rozwiązania i połączone usługi zostały trwale usunięte z obszaru roboczego, gdy zostało usunięte, i należy je ponownie skonfigurować w celu przeniesienia obszaru roboczego do wcześniej skonfigurowanego stanu. Niektóre dane mogą nie być dostępne dla zapytań po odzyskaniu obszaru roboczego do momentu ponownego zainstalowania skojarzonych rozwiązań, a ich schematy zostaną dodane do obszaru roboczego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby usunąć obszar roboczy, musisz mieć co najmniej *log Analytics uprawnienia współautora* .

* Jeśli nie masz pewności, czy usunięty obszar roboczy jest w stanie usuwania nietrwałego i można go odzyskać, kliknij pozycję [Otwórz kosz](#recover-workspace) na stronie *obszary robocze log Analytics* , aby wyświetlić listę obszarów roboczych usuniętych na subskrypcję. Trwale usunięte obszary robocze nie znajdują się na liście.
* Jeśli zostanie wyświetlony komunikat o błędzie *Ta nazwa obszaru roboczego jest już używana* lub *występuje konflikt* podczas tworzenia obszaru roboczego, może to być od:
  * Nazwa obszaru roboczego jest niedostępna i jest używana przez kogoś w organizacji lub przez innego klienta.
  * Obszar roboczy został usunięty w ciągu ostatnich 14 dni, a jego nazwa jest zarezerwowana dla okresu usuwania nietrwałego. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego w celu utworzenia nowego obszaru roboczego o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy, a następnie wykonaj trwałe usuwanie:<br>
    1. [Odzyskaj](#recover-workspace) obszar roboczy.
    2. [Trwale Usuń](#permanent-workspace-delete) obszar roboczy.
    3. Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego.
 
      Po pomyślnym zakończeniu wywołania usuwania na zapleczu można przywrócić obszar roboczy i ukończyć trwałą operację usuwania w jednej z sugerowanych wcześniej metod.

* Jeśli otrzymasz kod odpowiedzi 204 z *zasobem, który nie został odnaleziony* podczas usuwania obszaru roboczego, mogą wystąpić kolejne operacje ponawiania prób. 204 to pusta odpowiedź, która zazwyczaj oznacza, że zasób nie istnieje, więc usuwanie zostało ukończone bez wykonywania żadnych czynności.
* Jeśli usuniesz grupę zasobów i dołączysz obszar roboczy, zobaczysz usunięty obszar roboczy na stronie [Otwórz kosz](#recover-workspace) , ale operacja odzyskiwania zakończy się niepowodzeniem z kodem błędu 404, ponieważ Grupa zasobów nie istnieje — Utwórz ponownie grupę zasobów i spróbuj ponownie wykonać operację odzyskiwania.
