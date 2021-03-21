---
title: Przykłady usługi CIS Microsoft Azure 1.1.0 plan testu porównawczego
description: Omówienie przykładowych 1.1.0 planów testów porównawczych usługi Microsoft Azure CIS. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: b6188d96971329a83bc82042a1f840bd893354eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103197797"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v110-blueprint-sample"></a>Przykłady usługi CIS Microsoft Azure 1.1.0 plan testu porównawczego

Przykłady usługi CIS Microsoft Azure 1.1.0 plan testu porównawczego w wersji testowej obejmuje [Azure Policy](../../policy/overview.md) , które pomagają ocenić konkretne zalecenia dotyczące usługi CIS Microsoft Azure. Ten plan ułatwia klientom wdrożenie podstawowego zestawu zasad dla dowolnej architektury wdrożonej przez platformę Azure, która musi implementować usługi CIS Microsoft Azure zalecenia dotyczące testów porównawczych 1.1.0.

## <a name="recommendation-mapping"></a>Mapowanie zaleceń

[Azure Policy mapowanie rekomendacji](../../policy/samples/cis-azure-1-1-0.md) zawiera szczegółowe informacje dotyczące definicji zasad uwzględnionych w ramach tego planu oraz sposób mapowania tych definicji zasad na **zalecenia** dotyczące usługi CIS Microsoft Azure testów porównawczych 1.1.0. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć przykłady planów platformy Azure dla usługi CIS Microsoft Azure, można wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykłady usługi **CIS Microsoft Azure 1.1.0 plan testu porównawczego v** w _innych próbkach_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego planu porównawczego usługi CIS Microsoft Azure.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów uwzględnionych w przykładowej strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacje mogą nie zostać przesunięte z wyrównania z użyciem usług CIS Microsoft Azure przykłady testów porównawczych 1.1.0.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu" Przykłady planów testów usługi CIS Microsoft Azure ". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
|Inspekcja usług CIS Microsoft Azure fundacje testów porównawczych 1.1.0 i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista regionów, w których należy włączyć Network Watcher|Rozdzielana średnikami lista regionów. Aby wyświetlić pełną listę regionów, Użyj Get-AzLocation. Np.: wschód; eastus2|
|Inspekcja usług CIS Microsoft Azure fundacje testów porównawczych 1.1.0 i wdrażanie określonych rozszerzeń obsługi maszyn wirtualnych|Przypisanie zasad|Lista rozszerzeń maszyn wirtualnych, które są zatwierdzone do użycia|Rozdzielana średnikami lista rozszerzeń. Aby wyświetlić pełną listę rozszerzeń maszyn wirtualnych, użyj polecenie Get-AzVMExtensionImage. Np.: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).