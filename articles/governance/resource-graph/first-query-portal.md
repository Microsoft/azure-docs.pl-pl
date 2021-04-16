---
title: 'Szybki start: pierwsze zapytanie portalu'
description: W tym przewodniku Szybki start wykonaj kroki uruchamiania pierwszego zapytania z Azure Portal pomocą Azure Resource Graph Explorer.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533092"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Szybki start: uruchamianie pierwszego zapytania Resource Graph pomocą Azure Resource Graph Explorer

Możliwości usługi Azure Resource Graph są dostępne bezpośrednio w programie Azure Portal za pośrednictwem Azure Resource Graph Explorer. Resource Graph Explorer zawiera informacje o Azure Resource Manager zasobów i właściwościach, których można używać do wykonywania zapytań. Resource Graph Explorer udostępnia również czysty interfejs do pracy z wieloma zapytaniami, oceniania wyników, a nawet konwertowania wyników niektórych zapytań na wykres, który można przypiąć do pulpitu nawigacyjnego platformy Azure.

Na końcu tego przewodnika Szybki start będziesz używać narzędzi Azure Portal i Resource Graph Explorer do uruchamiania pierwszego zapytania Resource Graph i przypinania wyników do pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Otwórz okno [Azure Portal,](https://portal.azure.com) aby znaleźć i użyć Eksploratora Resource Graph, następujące kroki w celu uruchomienia pierwszego Resource Graph zapytania:

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz **pozycję Resource Graph Explorer.**

1. W części **Zapytanie 1** okna wprowadź zapytanie i `Resources | project name, type | limit 5` wybierz pozycję Uruchom **zapytanie**.

   > [!NOTE]
   > Ponieważ to przykładowe zapytanie nie zapewnia modyfikatora sortowania, takiego jak , wielokrotne uruchomienie tego zapytania prawdopodobnie spowoduje uzyskanie innego zestawu `order by` zasobów dla każdego żądania.

1. Przejrzyj odpowiedź na zapytanie na **karcie** Wyniki. Wybierz **kartę Komunikaty,** aby wyświetlić szczegółowe informacje o zapytaniu, w tym liczbę wyników i czas trwania zapytania. Błędy, jeśli istnieją, są wyświetlane na tej karcie.

1. Zaktualizuj zapytanie do `order by` właściwości **Name:** `Resources | project name, type | limit 5 | order by name asc` . Następnie wybierz pozycję **Uruchom zapytanie**.

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Ta kolejność poleceń najpierw ogranicza wyniki zapytania, a następnie je zamówia.

1. Zaktualizuj zapytanie, aby najpierw `order by` **zawierało właściwość Name,** `limit` a następnie pięć pierwszych wyników: `Resources | project name, type | order by name asc | limit 5` . Następnie wybierz pozycję **Uruchom zapytanie**.

Gdy końcowe zapytanie jest uruchamiane kilka razy, przy założeniu, że nic się nie zmienia w twoim środowisku, zwrócone wyniki są spójne i uporządkowane według właściwości **Name,** ale nadal ograniczone do pięciu najlepszych wyników.

### <a name="schema-browser"></a>Przeglądarka schematów

Przeglądarka schematów znajduje się w lewym okienku eksploratora Resource Graph Explorer. Ta lista zasobów zawiera  wszystkie typy zasobów platformy Azure, które są obsługiwane przez usługę Azure Resource Graph i istnieją w dzierżawie, do których masz dostęp. Rozwinięcie typu zasobu lub podwłaściwości pokazuje właściwości podrzędne, których można użyć do utworzenia Resource Graph zapytania.

Wybranie typu zasobu umieszcza `where type =="<resource type>"` się w polu zapytania. Wybranie jednej z właściwości podrzędnej powoduje `where <propertyName> == "INSERT_VALUE_HERE"` dodanie jej do pola zapytania.
Przeglądarka schematów to doskonały sposób odnajdywania właściwości do użycia w zapytaniach. Pamiętaj, aby zastąpić _wartość INSERT \_ VALUE \_ TUTAJ_ własną wartością. Dostosuj zapytanie przy użyciu warunków, operatorów i funkcji, aby osiągnąć zamierzone wyniki.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Tworzenie wykresu na Resource Graph zapytania

Po uruchomieniu poprzedniego zapytania, jeśli wybierzesz kartę **Wykresy,** zostanie wyświetlony komunikat", że "zestaw wyników nie jest zgodny z wizualizacją wykresu kołowego". Zapytania, które zawierają listę wyników, nie mogą być dokonywane na wykresie, ale zapytania, które zapewniają liczbę zasobów, mogą. Za pomocą [przykładowego zapytania zliczaj](./samples/starter.md#count-os)maszyny wirtualne według typu systemu operacyjnego utwórzmy wizualizację na Resource Graph operacyjnego.

1. W części **Zapytanie 1** okna wprowadź następujące zapytanie i wybierz pozycję **Uruchom zapytanie**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Wybierz **kartę Wyniki** i zwróć uwagę, że odpowiedź dla tego zapytania zawiera liczby.

1. Wybierz **kartę Wykresy.** Teraz zapytanie powoduje tworzenie wizualizacji. Zmień typ z _Wybierz typ wykresu..._ na Wykres _słupkowy_ lub _Wykres_ pierścieniowy, aby eksperymentować z dostępnymi opcjami wizualizacji.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Przypinanie wizualizacji zapytania do pulpitu nawigacyjnego

Jeśli masz wyniki zapytania, które można zwizualizować, wizualizację danych można przypiąć do jednego z pulpitów nawigacyjnych. Po uruchomieniu poprzedniego zapytania wykonaj następujące kroki:

1. Wybierz **pozycję** Zapisz i podaj nazwę "Maszyny wirtualne według typu systemu operacyjnego". Następnie wybierz **pozycję Zapisz** w dolnej części okienka po prawej stronie.

1. Wybierz **pozycję Uruchom** zapytanie, aby ponownie uruchomić zapytanie po jego zapisaniu.

1. Na karcie **Wykresy** wybierz wizualizację danych. Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego.**

1. Wybierz wyświetlone powiadomienie portalu lub wybierz pozycję **Pulpit nawigacyjny** w okienku po lewej stronie.

Zapytanie jest teraz dostępne na pulpicie nawigacyjnym z tytułem kafelka odpowiadającym nazwie zapytania. Jeśli zapytanie zostało niezapisane podczas przypinania, zamiast tego ma nazwę "Zapytanie 1".

Zapytanie i wynikowa wizualizacja danych są uruchamiane i aktualizowane przy każdym ładowaniu pulpitu nawigacyjnego, zapewniając dynamiczne i dynamiczne szczegółowe informacje w środowisku platformy Azure bezpośrednio w przepływie pracy.

> [!NOTE]
> Zapytania, których wynikiem jest lista, można również przypiąć do pulpitu nawigacyjnego. Ta funkcja nie jest ograniczona do wizualizacji danych zapytań.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Przykład importowania Resource Graph pulpitów nawigacyjnych Eksploratora

Aby podać przykłady zapytań Resource Graph i jak Resource Graph Explorer może służyć do ulepszania przepływu pracy usługi Azure Portal, wypróbuj te przykładowe pulpity nawigacyjne.

- [Resource Graph Explorer — przykładowy pulpit nawigacyjny #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Przykładowy obraz przykładowego pulpitu nawigacyjnego #1" lightbox="./media/arge-sample1-large.png":::

- [Resource Graph Explorer — przykładowy pulpit nawigacyjny #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Przykładowy obraz przykładowego pulpitu nawigacyjnego #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> Liczby i wykresy na powyższym przykładowym zrzucie ekranu pulpitu nawigacyjnego różnią się w zależności od środowiska platformy Azure.

1. Wybierz i pobierz przykładowy pulpit nawigacyjny, który chcesz ocenić.

1. W Azure Portal wybierz pozycję **Pulpit nawigacyjny** w okienku po lewej stronie.

1. Wybierz **pozycję Przekaż,** a następnie znajdź i wybierz pobrany przykładowy plik pulpitu nawigacyjnego. Następnie wybierz **pozycję Otwórz**.

Zaimportowany pulpit nawigacyjny zostanie automatycznie wyświetlony. Ponieważ istnieje ona teraz w Twojej Azure Portal, możesz eksplorować i wprowadzać zmiany zgodnie z potrzebami lub tworzyć nowe pulpity nawigacyjne na przykładzie w celu udostępniania ich zespołom. Aby uzyskać więcej informacji na temat pracy z pulpitami nawigacyjnymi, zobacz [Tworzenie i udostępnianie](../../azure-portal/azure-portal-dashboards.md)pulpitów nawigacyjnych w Azure Portal .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz usunąć przykładowe pulpity Resource Graph nawigacyjne ze środowiska Azure Portal, możesz to zrobić, wykonaj następujące czynności:

1. Wybierz **pozycję Pulpit** nawigacyjny w okienku po lewej stronie.

1. Z listy rozwijanej pulpitu nawigacyjnego wybierz przykładowy pulpit Resource Graph, który chcesz usunąć.

1. Wybierz **pozycję Usuń** z menu pulpitu nawigacyjnego w górnej części pulpitu nawigacyjnego, a następnie wybierz przycisk **OK,** aby potwierdzić.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start pierwsze zapytanie zostało uruchomione przy Azure Resource Graph Explorer i przedstawiono przykłady pulpitów nawigacyjnych obsługiwanych przez Resource Graph. Aby dowiedzieć się więcej o języku Resource Graph, przejdź do strony szczegółów języka zapytań.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji na temat języka zapytań](./concepts/query-language.md)
