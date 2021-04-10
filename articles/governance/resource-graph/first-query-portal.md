---
title: 'Szybki Start: pierwsze zapytanie w portalu'
description: W tym przewodniku szybki start wykonaj kroki, aby uruchomić pierwsze zapytanie z Azure Portal przy użyciu Eksploratora Azure Resource Graph.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: cb62a1ae3a29a7aae793fcee6fd896fe0260dc10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920103"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Szybki Start: uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph

Możliwości wykresu zasobów platformy Azure są dostępne bezpośrednio w Azure Portal za pomocą Eksploratora Azure Resource Graph. Eksplorator wykresów zasobów zawiera umożliwia przeglądania informacje o typach zasobów Azure Resource Manager i właściwościach, które można badać. Eksplorator grafu zasobów udostępnia również czysty interfejs do pracy z wieloma zapytaniami, oceniając wyniki, a nawet konwertując wyniki niektórych zapytań na wykres, który może zostać przypięty do pulpitu nawigacyjnego platformy Azure.

Na końcu tego przewodnika Szybki Start będziesz używać Azure Portal i Eksploratora grafów zasobów do uruchamiania pierwszej kwerendy grafu zasobów i przypiętia wyników do pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Otwórz [Azure Portal](https://portal.azure.com) , aby znaleźć i użyć Eksploratora grafu zasobów, wykonując następujące kroki, aby uruchomić pierwszą kwerendę wykresu zasobów:

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Eksplorator grafów zasobów**.

1. W porcji **zapytania 1** okna wprowadź zapytanie `Resources | project name, type | limit 5` i wybierz polecenie **Uruchom zapytanie**.

   > [!NOTE]
   > Ponieważ w tym przykładzie zapytania nie określono modyfikatora sortowania, takiego jak `order by` uruchomienie tego zapytania wiele razy, prawdopodobnie zostanie wyznaczony inny zestaw zasobów dla każdego żądania.

1. Zapoznaj się z odpowiedzią zapytania na karcie **wyniki** . Wybierz kartę **komunikaty** , aby wyświetlić szczegółowe informacje o zapytaniu, w tym liczbę wyników i czas trwania zapytania. Błędy, jeśli istnieją, są wyświetlane na tej karcie.

1. Zaktualizuj zapytanie do `order by` właściwości **name** : `Resources | project name, type | limit 5 | order by name asc` . Następnie wybierz pozycję **Uruchom zapytanie**.

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie porządkuje je.

1. Zaktualizuj zapytanie do pierwszej `order by` właściwości **nazwy** , a następnie `limit` do pięciu pierwszych wyników: `Resources | project name, type | order by name asc | limit 5` . Następnie wybierz pozycję **Uruchom zapytanie**.

Gdy ostateczne zapytanie jest uruchamiane kilka razy, przy założeniu, że nic w środowisku nie zmienia się, zwracane wyniki są spójne i uporządkowane według właściwości **nazwy** , ale nadal są ograniczone do pięciu pierwszych wyników.

### <a name="schema-browser"></a>Przeglądarka schematów

Przeglądarka schematów znajduje się w lewym okienku Eksploratora grafów zasobów. Ta lista zasobów zawiera wszystkie _typy_ zasobów platformy Azure, które są obsługiwane przez usługę Azure Resource Graph i które istnieją w dzierżawie, do której masz dostęp. Rozszerzanie typu zasobu lub podwłaściwości pokazuje właściwości podrzędne, których można użyć do utworzenia kwerendy wykresu zasobów.

Wybór typu zasobu `where type =="<resource type>"` do pola zapytania. Wybranie jednej z właściwości podrzędnych powoduje dodanie `where <propertyName> == "INSERT_VALUE_HERE"` do pola zapytania.
Przeglądarka schematów to doskonały sposób odnajdywania właściwości do użycia w zapytaniach. Pamiętaj, aby w _\_ \_ tym miejscu zastąpić wartość Wstaw_ przy użyciu własnej wartości, Dostosuj zapytanie o warunki, operatory i funkcje, aby osiągnąć zamierzone wyniki.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Tworzenie wykresu na podstawie zapytania wykresu zasobów

Po uruchomieniu poprzedniego zapytania, jeśli wybierzesz kartę **wykresy** , zostanie wyświetlony komunikat "zestaw wyników nie jest zgodny z wizualizacją wykresu kołowego". Zapytania, które nie mogą zostać wprowadzone do wykresu, mogą zawierać kwerendy, które zapewniają liczbę zasobów. Korzystając z [przykładowych maszyn wirtualnych typu zapytanie-liczba](./samples/starter.md#count-os), Utwórz wizualizację z zapytania grafu zasobów.

1. W części **kwerenda 1** okna wprowadź następujące zapytanie i wybierz polecenie **Uruchom zapytanie**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Wybierz kartę **wyniki** i pamiętaj, że odpowiedź dla tego zapytania zawiera liczby.

1. Wybierz kartę **wykresy** . Teraz zapytanie prowadzi do wizualizacji. Zmień typ z _Wybierz typ wykresu.._ . na _Wykres słupkowy_ lub _wykres pierścieniowy_ , aby eksperymentować z dostępnymi opcjami wizualizacji.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Przypinanie wizualizacji zapytania do pulpitu nawigacyjnego

Wyniki zapytania, które można wizualizować, można przypinać do jednego z pulpitów nawigacyjnych. Po uruchomieniu poprzedniego zapytania wykonaj następujące kroki:

1. Wybierz pozycję **Zapisz** i podaj nazwę "maszyny wirtualne według typu systemu operacyjnego". Następnie wybierz pozycję **Zapisz** w dolnej części okienka po prawej stronie.

1. Wybierz pozycję **Uruchom zapytanie** , aby ponownie uruchomić zapytanie, które zostało zapisane.

1. Na karcie **wykresy** wybierz wizualizację danych. Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

1. Wybierz wyświetlone powiadomienie portalu lub wybierz pozycję **pulpit nawigacyjny** w okienku po lewej stronie.

Zapytanie jest teraz dostępne na pulpicie nawigacyjnym z tytułem kafelka pasującego do nazwy zapytania. Jeśli zapytanie zostało anulowane, gdy zostało przypięte, jego nazwa to "Query 1".

Zapytanie i wyniki wizualizacji danych, które można uruchamiać i aktualizować po każdym załadowaniu pulpitu nawigacyjnego, zapewniające czas rzeczywisty i dynamiczny wgląd w środowisko platformy Azure bezpośrednio w przepływie pracy.

> [!NOTE]
> Zapytania, które powodują, że lista może być również przypięta do pulpitu nawigacyjnego. Funkcja nie jest ograniczona do wizualizacji danych zapytań.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importuj przykładowe pulpity nawigacyjne Eksploratora grafów zasobów

Aby przedstawić przykłady zapytań dotyczących wykresów zasobów i jak można użyć Eksploratora grafów zasobów do usprawnienia przepływu pracy Azure Portal, wypróbuj te przykładowe pulpity nawigacyjne.

- [Eksplorator grafów zasobów — przykładowy pulpit nawigacyjny #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Przykład obrazu dla przykładowego pulpitu nawigacyjnego #1" lightbox="./media/arge-sample1-large.png":::

- [Eksplorator grafów zasobów — przykładowy pulpit nawigacyjny #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Przykład obrazu dla przykładowego pulpitu nawigacyjnego #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> Liczniki i wykresy w powyższym przykładowym zrzucie ekranu nawigacyjnego różnią się w zależności od środowiska platformy Azure.

1. Wybierz i Pobierz przykładowy pulpit nawigacyjny, który chcesz oszacować.

1. W obszarze Azure Portal wybierz pozycję **pulpit nawigacyjny** w okienku po lewej stronie.

1. Wybierz pozycję **Przekaż**, a następnie Znajdź i wybierz pobrany przykładowy plik pulpitu nawigacyjnego. Następnie wybierz pozycję **Otwórz**.

Zaimportowany pulpit nawigacyjny jest automatycznie wyświetlany. Ponieważ teraz istnieje ona w Azure Portal, możesz eksplorować i wprowadzać zmiany w miarę potrzeb lub tworzyć nowe pulpity nawigacyjne z przykładu, aby udostępnić swoje zespoły. Aby uzyskać więcej informacji na temat pracy z pulpitami nawigacyjnymi, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć przykładowe pulpity nawigacyjne grafu zasobów ze środowiska Azure Portal, możesz to zrobić, wykonując następujące czynności:

1. Wybierz pozycję **pulpit nawigacyjny** w okienku po lewej stronie.

1. Z listy rozwijanej pulpit nawigacyjny wybierz Pulpit nawigacyjny przykładowy Wykres zasobów, który chcesz usunąć.

1. Wybierz pozycję **Usuń** z menu Pulpit nawigacyjny w górnej części pulpitu nawigacyjnego i wybierz pozycję **OK** , aby potwierdzić.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto Eksploratora Azure Resource Graph do uruchomienia pierwszego zapytania i przeszukasz przykłady pulpitu nawigacyjnego obsługiwane przez Wykres zasobów. Aby dowiedzieć się więcej na temat języka grafu zasobów, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)