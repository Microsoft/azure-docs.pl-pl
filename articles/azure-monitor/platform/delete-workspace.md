---
title: Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 0858d448cf768dbe6ea48f07247725fac30da860
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758917"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics

W tym artykule wyjaśniono koncepcję nietrwałego usunięcia obszaru roboczego usługi Azure Log Analytics i odzyskiwania usuniętego obszaru roboczego.

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego Log Analytics jest wykonywana operacja usuwania nietrwałego umożliwiająca odzyskanie obszaru roboczego, w tym jego danych i podłączonych agentów w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Po okresie niemożliwym do usunięcia nie można odzyskać zasobu obszaru roboczego i jego danych — dane są umieszczane w kolejce do trwałego usunięcia i całkowicie przeczyszczone w ciągu 30 dni. Nazwa obszaru roboczego jest "wydana" i można go użyć do utworzenia nowego obszaru roboczego.

> [!NOTE]
> Jeśli chcesz zastąpić zachowanie nietrwałego usuwania i trwale usunąć obszar roboczy, wykonaj czynności opisane w [obszarze trwałe usuwanie obszaru roboczego](#permanent-workspace-delete).

Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ mogą istnieć ważne dane i konfiguracja, które mogą negatywnie wpłynąć na działanie usługi. Sprawdź, jakie agenci, rozwiązania i inne usługi platformy Azure przechowują swoje dane w Log Analytics, na przykład:

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

Obszar roboczy można usunąć przy użyciu [programu PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0&preserve-view=true), [interfejsu API REST](/rest/api/loganalytics/workspaces/delete)lub w [Azure Portal](https://portal.azure.com).

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

Obszar roboczy możesz odzyskać w okresie usuwania nietrwałego, w tym jego dane, konfigurację i połączone agenci. Musisz mieć uprawnienia współautora do subskrypcji i grupy zasobów, w której znajduje się obszar roboczy przed operacją usuwania nietrwałego. Odzyskiwanie obszaru roboczego odbywa się przez utworzenie obszaru roboczego Log Analytics zawierającego szczegółowe informacje o usuniętym obszarze roboczym, w tym:

- Identyfikator subskrypcji
- Nazwa grupy zasobów
- Nazwa obszaru roboczego
- Region (Region)

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

> [!NOTE]
> * Ponowne tworzenie obszaru roboczego w okresie usuwania nietrwałego wskazuje, że ta nazwa obszaru roboczego jest już używana. 
 
## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby usunąć obszar roboczy, musisz mieć co najmniej *log Analytics uprawnienia współautora* .

* Jeśli nie masz pewności, czy usunięty obszar roboczy jest w stanie usuwania nietrwałego i można go odzyskać, kliknij pozycję [Odzyskaj](#recover-workspace) na stronie *obszary robocze log Analytics* , aby wyświetlić listę nieusuniętych obszarów roboczych, które zostały usunięte na subskrypcję. Trwale usunięte obszary robocze nie znajdują się na liście.
* Jeśli zostanie wyświetlony komunikat o błędzie *Ta nazwa obszaru roboczego jest już używana* lub *występuje konflikt* podczas tworzenia obszaru roboczego, może to być od:
  * Nazwa obszaru roboczego jest niedostępna i jest używana przez kogoś w organizacji lub przez innego klienta.
  * Obszar roboczy został usunięty w ciągu ostatnich 14 dni, a jego nazwa jest zarezerwowana dla okresu usuwania nietrwałego. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego w celu utworzenia nowego obszaru roboczego o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usuwanie:<br>
    1. [Odzyskaj](#recover-workspace) obszar roboczy.
    2. [Trwale Usuń](#permanent-workspace-delete) obszar roboczy.
    3. Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego.
* Jeśli zobaczysz kod odpowiedzi 204, który pokazuje, że *nie znaleziono zasobu*, przyczyną może być kolejny próba użycia operacji usuwania obszaru roboczego. 204 to pusta odpowiedź, która zazwyczaj oznacza, że zasób nie istnieje, więc usuwanie zostało ukończone bez wykonywania żadnych czynności.
  Po pomyślnym zakończeniu wywołania usuwania na zapleczu można przywrócić obszar roboczy i ukończyć trwałą operację usuwania w jednej z sugerowanych wcześniej metod.

