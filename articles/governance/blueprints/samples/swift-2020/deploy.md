---
title: Wdrażanie SWIFT dostawcy CSP — przykład strategii CSCF V2020
description: Wdróż kroki dla przykładowej strategii SWIFT CSP-CSCF V2020, w tym szczegóły parametru artefaktu.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 8f28bae58128306776fa18a6b383e324482bb03f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582001"
---
# <a name="deploy-the-swift-csp-cscf-v2020-blueprint-sample"></a>Wdróż przykład SWIFT programu CSP — CSCF V2020 plan

Aby wdrożyć usługę Azure Plans SWIFT CSP-CSCF V2020 plan, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. W obszarze _inne przykłady_ Znajdź **kod SWIFT programu CSP-CSCF V2020** , a następnie wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowej strategii SWIFT CSP-CSCF V2020.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może nie przełączać się do wyrównania przy użyciu przycisków SWIFT CSP-CSCF V2020.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z poziomu przykładu SWIFT CSP-CSCF V2020 plan". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja zapoznawcza \] : Inspekcja SWIFT dostawcy usług kryptograficznych — CSCF V2020 Controls i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienia dziennika diagnostycznego. Akceptowalne wartości można znaleźć w [kategorii dzienników zasobów Azure monitor](../../../../azure-monitor/essentials/resource-logs-categories.md#supported-log-categories-per-resource-type).|
|\[Wersja zapoznawcza \] : Inspekcja SWIFT dostawcy usług kryptograficznych — CSCF V2020 Controls i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Identyfikatory połączonych obszarów roboczych|Rozdzielana średnikami lista identyfikatorów obszaru roboczego, z którymi ma być połączony Agent Log Analytics|
|\[Wersja zapoznawcza \] : Inspekcja SWIFT dostawcy usług kryptograficznych — CSCF V2020 Controls i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista użytkowników, którzy powinni zostać dołączeni do grupy administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: administrator; Użytkownik1; Do|
|\[Wersja zapoznawcza \] : Inspekcja SWIFT dostawcy usług kryptograficznych — CSCF V2020 Controls i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Nazwa domeny (FQDN)|W pełni kwalifikowana nazwa domeny (FQDN), do której należy dołączyć maszyny wirtualne z systemem Windows|
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|Wdróż Log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Log Analytics obszar roboczy dla VM Scale Sets systemu Windows (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdróż Log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu|Przypisanie zasad|Efekt|Informacje dotyczące efektów zasad można znaleźć w [opisie Azure Policy efektów](../../../policy/concepts/effects.md)|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Wartość w dniach okresu przechowywania (0 oznacza nieograniczony czas przechowywania)|Dni przechowywania (opcjonalnie, 180 dni, jeśli nie określono)|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu na potrzeby inspekcji programu SQL Server|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzone SQL Server, które będzie współużytkowane przez wszystkie serwery w tym regionie). Ważne — w przypadku właściwej operacji inspekcji nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu.|
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń|Przypisanie zasad|Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń|Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu.|
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć)|Grupa zasobów, w której zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć.|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładowego planu CSCF V2020, odwiedź następujące artykuły, aby dowiedzieć się więcej o mapowaniu planów i kontroli:

> [!div class="nextstepaction"]
> [SWIFT CSP-CSCF V2020 — Omówienie](./index.md) 
>  [SWIFT CSP — CSCF V2020 — Mapowanie formantów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).