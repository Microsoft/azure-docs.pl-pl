---
title: Przykład strategii testu porównawczego zabezpieczeń platformy Azure — omówienie
description: Omówienie przykładu strategii testu porównawczego zabezpieczeń platformy Azure. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: 38a50970999965b6a86b8ce8882006c169f2dc5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919242"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Przykładowy plan usługi Azure Security test

Przykładowy plan usługi Azure Security Tests oferuje funkcję ładu Guard — szyny używające [Azure Policy](../../policy/overview.md) , które ułatwiają ocenę określonych kontrolek [usługi Azure Security test w wersji 1](../../../security/benchmarks/overview.md) . Ta strategia ułatwia klientom wdrażanie podstawowego zestawu zasad dla dowolnej architektury wdrożonej przez platformę Azure, w której planowane jest zaimplementowanie mechanizmów kontroli testu porównawczego zabezpieczeń platformy Azure.

## <a name="control-mapping"></a>Mapowanie kontrolek

[Mapowanie formantów Azure Policy](../../policy/samples/azure-security-benchmark.md) zawiera szczegółowe informacje o definicjach zasad uwzględnionych w ramach tego planu oraz o tym, jak te definicje zasad są mapowane do **domen zgodności** i **kontroli** w ramach testu porównawczego zabezpieczeń platformy Azure. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć plan platformy Azure dotyczący planu wydajności Azure Security test, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład planu **usługi Azure Security test w wersji 1** pod _innymi przykładami_ i wybierz nazwę, aby wybrać ten przykład.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego planu usługi Azure Security test.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów uwzględnionych w przykładowej strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może nie przełączać się z zaleceniami dotyczącymi usługi Azure Security test.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładowego planu usługi Azure Security test". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

### <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

### <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista użytkowników wykluczonych z grupy administratorów maszyn wirtualnych systemu Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być wykluczone w grupie lokalnej Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista użytkowników, którzy muszą być uwzględnieni w grupie administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista użytkowników, których Grupa Administratorzy maszyny wirtualnej systemu Windows musi zawierać *tylko*|Rozdzielana średnikami lista wszystkich oczekiwanych członków lokalnej grupy Administratorzy. Przykład: administrator; Użytkownik1; Do|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista regionów, w których należy włączyć Network Watcher|Aby wyświetlić pełną listę regionów, użyj Get-AzLocation|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Sieć wirtualna, w której powinny być połączone maszyny wirtualne|Przykład:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Brama sieci, która powinna być używana przez sieci wirtualne|Przykład:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista identyfikatorów obszarów roboczych, do których mają być nawiązywane połączenia Log Analytics agenci|Rozdzielana średnikami lista identyfikatorów obszaru roboczego, z którymi ma być połączony Agent Log Analytics|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych|Inspekcja ustawień diagnostycznych dla wybranych typów zasobów|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Najnowsza wersja języka PHP|Najnowsza obsługiwana wersja języka PHP dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Najnowsza wersja języka Java|Najnowsza obsługiwana wersja języka Java dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Najnowsza wersja języka Python systemu Windows|Najnowsza obsługiwana wersja języka Python dla App Services|
|Inspekcja zaleceń usługi Azure Security test i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Linux|Najnowsza obsługiwana wersja języka Python dla App Services|

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).