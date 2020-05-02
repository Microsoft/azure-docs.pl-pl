---
title: Przykład wdrażania usługi Azure Security test
description: Wdróż kroki dla przykładowego planu usługi Azure Security test, w tym szczegóły parametrów artefaktu.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725966"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>Wdróż przykład planu usługi Azure Security test

Aby wdrożyć plan platformy Azure dotyczący planu wydajności Azure Security test, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free) .

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź przykład planu **testu porównawczego zabezpieczeń platformy Azure** pod _innymi_ przykładami, a następnie wybierz nazwę, aby wybrać ten przykład.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładowego planu usługi Azure Security test.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może nie przełączać się z zaleceniami dotyczącymi usługi Azure Security test.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładowego planu usługi Azure Security test". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisz przykładową kopię

Po pomyślnym **opublikowaniu**kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. Ten krok polega na tym, że parametry są dostarczane, aby każde wdrożenie kopii przykładowej strategii było unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz plan** w górnej części strony definicji planu.

1. Podaj wartości parametrów dla przypisania planu:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji zostanie utworzone przypisanie dla każdego z nich przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Zmień stosownie do potrzeb lub pozostaw jako is.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji**planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania planu dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w ramach którego jest zdefiniowany. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Utworzono przypisanie strategii i rozpocznie się wdrażanie artefaktów. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, Otwórz przypisanie planu.

> [!WARNING]
> Usługa plany platformy Azure i wbudowane przykłady planów są **bezpłatne**. Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt uruchamiania zasobów wdrożonych przez ten przykład strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|
Lista użytkowników wykluczonych z grupy administratorów maszyn wirtualnych systemu Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być wykluczone w grupie lokalnej Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Lista użytkowników, którzy muszą być uwzględnieni w grupie administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Lista użytkowników, których Grupa Administratorzy maszyny wirtualnej systemu Windows musi zawierać *tylko*|Rozdzielana średnikami lista wszystkich oczekiwanych członków lokalnej grupy Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Lista regionów, w których należy włączyć Network Watcher|Aby wyświetlić pełną listę regionów, Użyj Get-AzLocation|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Sieć wirtualna, w której powinny być połączone maszyny wirtualne|Przykład:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Brama sieci, która powinna być używana przez sieci wirtualne|Przykład:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Lista identyfikatorów obszarów roboczych, do których mają być nawiązywane połączenia Log Analytics agenci|Rozdzielana średnikami lista identyfikatorów obszaru roboczego, z którymi ma być połączony Agent Log Analytics|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych|Inspekcja ustawień diagnostycznych dla wybranych typów zasobów|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Najnowsza wersja języka PHP|Najnowsza obsługiwana wersja języka PHP dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Najnowsza wersja języka Java|Najnowsza obsługiwana wersja języka Java dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Najnowsza wersja języka Python systemu Windows|Najnowsza obsługiwana wersja języka Python dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisywanie zasad|Najnowsza wersja języka Python dla systemu Linux|Najnowsza obsługiwana wersja języka Python dla App Services|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrażaniem przykładowego planu usługi Azure Security test można znaleźć w następujących artykułach, aby dowiedzieć się więcej o mapowaniu planów i kontroli:

> [!div class="nextstepaction"]
> [Plan testu porównawczego zabezpieczeń Azure — omówienie](./index.md)
> planu[testów zabezpieczeń platformy Azure](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
