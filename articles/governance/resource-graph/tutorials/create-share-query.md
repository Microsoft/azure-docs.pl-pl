---
title: 'Samouczek: zarządzanie zapytaniami w Azure Portal'
description: W tym samouczku utworzysz zapytanie grafu zasobów i udostępnisz nowe zapytanie innym osobom w Azure Portal.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916682"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Samouczek: Tworzenie i udostępnianie kwerendy wykresu zasobów platformy Azure w Azure Portal

Eksplorator usługi Azure Resource Graph umożliwia zapisywanie zapytań grafu zasobów bezpośrednio w Azure Portal. Istnieją dwa typy zapytań: _Private_ i _Shared_. Zapytanie prywatne zostanie zapisane w ustawieniach Azure Portal. Udostępnione zapytanie jest zasobem Azure Resource Manager, którym można zarządzać za pomocą kontroli dostępu opartej na rolach (Azure RBAC) i chronionej przy użyciu blokad zasobów. Oba typy zapytań są szyfrowane w stanie spoczynku.

Zapisując zapytania w Azure Portal, można zaoszczędzić czas, w którym można szukać ulubionych lub często używanych zapytań. Po udostępnieniu zapytań można ułatwić zespołowi zrealizowanie celów spójności i wydajności poprzez powtarzanie.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> - Tworzenie i usuwanie zapytania prywatnego
> - Utwórz zapytanie udostępnione
> - Odnajdź udostępnione zapytania
> - Usuń udostępnione zapytanie

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-and-delete-a-private-query"></a>Tworzenie i usuwanie zapytania prywatnego

Zapytania prywatne są dostępne i widoczne tylko dla konta, które je tworzy. Ponieważ są one zapisywane w ustawieniach Azure Portal konta, mogą być tworzone, używane i usuwane tylko z wewnątrz Azure Portal. Zapytanie prywatne nie jest zasobem Menedżer zasobów. Aby utworzyć nowe zapytanie prywatne, wykonaj następujące kroki:

1. W menu portalu wybierz pozycję **wszystkie usługi** lub użyj pola wyszukiwania Azure w górnej części wszystkich stron. Wyszukaj, a następnie wybierz pozycję **Eksplorator grafów zasobów**.

1. Na karcie **zapytanie 1** na stronie Eksplorator zasobów platformy Azure wprowadź następujące zapytanie:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Wybierz pozycję **Uruchom zapytanie** , aby wyświetlić wyniki zapytania w dolnym okienku.

   Aby uzyskać więcej informacji na temat tego zapytania, zobacz [przykłady — liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-os).

1. Wybierz **pozycję Zapisz** lub **Zapisz jako**, **Wprowadź liczbę maszyn wirtualnych według systemu operacyjnego** jako nazwę, pozostaw jako **kwerendę prywatną**, a następnie wybierz pozycję **Zapisz** w dolnej części okienka **Zapisz zapytanie** . Tytuł karty zostanie zmieniony z **zapytania 1** na **liczbę maszyn wirtualnych według systemu operacyjnego**.

1. Przejdź do Eksploratora grafów zasobów platformy Azure w Azure Portal, a następnie wróć do niego. Zwróć uwagę, że zapisane zapytanie nie jest już wyświetlane, a karta **zapytanie 1** zwróci wartość.

1. Wybierz pozycję **Otwórz zapytanie**. Upewnij się, że typ jest **kwerendą prywatną**. Zapisane nazwy **maszyn wirtualnych według systemu operacyjnego** zostaną wyświetlone na liście **Nazwa zapytania** . Po wybraniu linku tytułu zapisanego zapytania zostanie ono załadowane do nowej karty zawierającej nazwę tego zapytania.

   > [!NOTE] 
   > Gdy zapisane zapytanie jest otwarte, a na karcie zostanie wyświetlona jego nazwa, wybranie przycisku **Zapisz** aktualizuje go o wszelkie wprowadzone zmiany. Aby utworzyć nowe zapisane zapytanie z tego otwartego zapytania, wybierz pozycję **Zapisz jako** i postępuj zgodnie z tym, jeśli zapisano zupełnie nowe zapytanie.

1. Aby usunąć zapisane zapytanie, wybierz pozycję **Otwórz zapytanie** ponownie i sprawdź, czy pole **Typ** jest ustawione na **zapytanie prywatne**. W wierszu zapisanego `Count VMs by OS` zapytania wybierz pozycję **Usuń** (ikona kosza). W oknie dialogowym potwierdzenia wybierz pozycję **tak** , aby zakończyć usuwanie zapytania.
   Następnie zamknij okienko **Otwórz zapytanie** .

## <a name="create-a-shared-query"></a>Utwórz zapytanie udostępnione

W przeciwieństwie do zapytania prywatnego, udostępnione zapytanie jest zasobem Menedżer zasobów. Oznacza to, że zapytanie jest zapisywane w grupie zasobów, można je zarządzać i kontrolować przy użyciu funkcji RBAC na platformie Azure i nawet być chronione przy użyciu blokad zasobów. Jako zasób, każdy, kto ma odpowiednie uprawnienia, może go zobaczyć i używać. Aby utworzyć nowe zapytanie udostępnione, wykonaj następujące kroki:

1. W menu portalu wybierz pozycję **wszystkie usługi** lub użyj pola wyszukiwania Azure w górnej części wszystkich stron, aby wyszukać i wybrać pozycję **Eksplorator grafów zasobów**.

1. Na karcie **zapytanie 1** na stronie Eksplorator zasobów platformy Azure wprowadź następujące zapytanie:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Wybierz pozycję **Uruchom zapytanie** , aby wyświetlić wyniki zapytania w dolnym okienku.

   Aby uzyskać więcej informacji na temat tego zapytania, zobacz [przykłady — liczba maszyn wirtualnych według typu systemu operacyjnego](../samples/starter.md#count-os).

1. Wybierz pozycję **Zapisz** lub **Zapisz jako**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Zapisz nowe zapytanie przy użyciu przycisku Zapisz" border="false":::

1. W okienku **Zapisz zapytanie** wprowadź wartość w polu **Liczba maszyn wirtualnych według systemu operacyjnego** .

1. Zmień typ na **zapytanie udostępnione**, Ustaw opis na **Liczba maszyn wirtualnych według typu systemu operacyjnego** i ustaw **subskrypcję** , aby określić miejsce utworzenia zasobu zapytania.

1. Pozostaw zaznaczone pole wyboru **Publikuj do zasobów-Graph-zapytania** , a w **pozycji Grupa zasobów** ustaw wartość **(Stany Zjednoczone) zachodnie stany USA**.

1. Wybierz pozycję **Zapisz** w dolnej części okienka **Zapisz zapytanie** . Tytuł karty zostanie zmieniony z **zapytania 1** na **liczbę maszyn wirtualnych według systemu operacyjnego**. Przy pierwszym użyciu grupy zasobów **zapytania z wykresem zasobów** czas zapisywania trwa dłużej niż oczekiwano, gdy grupa zasobów zostanie utworzona.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Zapisz nowe zapytanie jako zapytanie udostępnione" border="false":::

   > [!NOTE] 
   > Możesz wyczyścić pole wyboru **Publikuj do zasobów-Graph-** querys, jeśli chcesz podać nazwę istniejącej grupy zasobów, w której ma zostać zapisane zapytanie udostępnione. Użycie domyślnej nazwy grupy zasobów dla zapytań sprawia, że zapytania udostępnione są łatwiejsze do odnajdywania. Umożliwia również dokładniejsze przeznaczenie tej grupy zasobów. Można jednak wybrać istniejącą grupę zasobów ze względów bezpieczeństwa na podstawie istniejących uprawnień.

1. Przejdź do Eksploratora grafów zasobów platformy Azure w Azure Portal, a następnie wróć do niego. Zwróć uwagę, że zapisane zapytanie nie jest już wyświetlane, a karta **zapytanie 1** zwróci wartość.

1. Wybierz pozycję **Otwórz zapytanie**. Sprawdź, czy typ jest ustawiony na **zapytanie udostępnione** i czy kombinacja **subskrypcji** i **grupy zasobów** jest zgodna z miejscem, w którym zapisano zapytanie. Liczba zapisanych **maszyn wirtualnych według elementu systemu operacyjnego** zostanie teraz wyświetlona na liście **Nazwa zapytania** . Wybierz łącze tytuł zapisanego zapytania, aby załadować je do nowej karty zawierającej nazwę tego zapytania. Jako zapytanie udostępnione wyświetla ikonę na karcie obok tytułu, oznaczając ją jako udostępnioną.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Pokaż udostępnione zapytanie z ikoną" border="false":::

   > [!NOTE] 
   > Gdy zapisane zapytanie jest otwarte, a karta wyświetla jego nazwę, przycisk **Zapisz** aktualizuje go przy użyciu wszelkich wprowadzonych zmian. Aby utworzyć nowe zapisane zapytanie, wybierz pozycję **Zapisz jako** i postępuj zgodnie z tym, jeśli zapisano zupełnie nowe zapytanie.

## <a name="discover-shared-queries"></a>Odnajdź udostępnione zapytania

Ponieważ udostępnione zapytanie jest zasobem Menedżer zasobów, można je znaleźć na kilka sposobów:

- Z Eksploratora grafów zasobów. Wybierz pozycję **Otwórz zapytanie** i ustaw typ **zapytania udostępnione**.
- Na stronie portalu kwerendy grafu zasobów.
- Z grupy zasobów, w której zapisano udostępnione zapytanie.
- Za pomocą zapytania do grafu zasobów.

### <a name="view-resource-graph-queries"></a>Wyświetl zapytania grafu zasobów

W Azure Portal na stronie kwerendy grafu zasobów są wyświetlane udostępnione zapytania, do których mają dostęp zalogowane konto. Ta strona umożliwia filtrowanie według nazwy, subskrypcji, grupy zasobów i innych właściwości zapytania grafu zasobów. Za pomocą tego interfejsu można także Tagi, eksportować i usuwać zapytania dotyczące wykresów zasobów.

Wybranie jednego z zapytań spowoduje otwarcie strony zapytania wykresu zasobów. Podobnie jak w przypadku innych zasobów Menedżer zasobów, ta strona oferuje Interaktywny przegląd wraz z dziennikiem aktywności, kontrolą dostępu i tagami. Możesz również zastosować blokadę zasobu bezpośrednio z tej strony.

Uzyskaj dostęp do strony zapytania grafu zasobów z menu Portal, wybierając pozycję **wszystkie usługi** lub korzystając z pola wyszukiwania platformy Azure w górnej części wszystkich stron. Wyszukaj i wybierz pozycję **Eksplorator grafów zasobów**.

### <a name="list-resource-groups-resources"></a>Wyświetlanie listy zasobów grup zasobów

Zapytanie grafu zasobów jest wyświetlane wraz z innymi zasobami, które są częścią grupy zasobów.
Wybranie zapytania grafu zasobów spowoduje otwarcie strony dla tego zapytania. Opcje menu wielokropka i skrótu (wyzwalane przez kliknięcie prawym przyciskiem myszy) działają tak samo jak na stronie kwerendy grafu zasobów.

### <a name="query-resource-graph"></a>Zapytanie grafu zasobów

Zapytania dotyczące wykresów zasobów można znaleźć za pomocą zapytania do grafu zasobów. Poniższe kwerendy wykresu zasobów są ograniczone przez typ `Microsoft.ResourceGraph/queries` , a następnie używa `project` do wyświetlania tylko nazwy, godziny modyfikacji i samego zapytania:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Uruchom udostępnione zapytanie

Zapytanie udostępnione grafu zasobów można uruchomić z `{{shared-query-uri}}` składnią (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [Składnia zapytania udostępnionego](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Usuń udostępnione zapytanie

Jeśli zapytanie udostępnione nie jest już potrzebne, usuń je. Usuwając zapytanie udostępnione, można usunąć odpowiedni zasób Menedżer zasobów. Wszystkie pulpity nawigacyjne, do których wykres wyników został przypięty, teraz wyświetlają komunikat o błędzie. Gdy zostanie wyświetlony komunikat o błędzie, użyj przycisku **Usuń z pulpitu nawigacyjnego** , aby wyczyścić pulpit nawigacyjny.

Zapytanie udostępnione można usunąć za pomocą następujących interfejsów:
- Strona zapytania grafu zasobów
- Strona kwerendy wykresu zasobów
- Strona **otwieranie zapytania** w Eksploratorze grafu zasobów
- Strona grupy zasobów

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Usuń utworzone zapytania prywatne i udostępnione, jeśli nie będą już potrzebne.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono zapytania prywatne i udostępnione. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](../concepts/query-language.md)