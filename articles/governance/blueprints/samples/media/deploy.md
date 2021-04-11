---
title: Przykład wdrażania planu multimediów
description: Wdróż kroki dla przykładu planu multimediów, w tym szczegóły parametrów artefaktu.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 760f75711f66e2b178ef12a9a4e24ff2e536179c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386404"
---
# <a name="deploy-the-media-blueprint-sample"></a>Wdróż przykład planu multimediów

Aby wdrożyć przykład planu multimediów, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład planu **multimediów** w obszarze _inne przykłady_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładu strategii.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może spowodować przeniesienie go do warstwy Standardowa.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu usługi Media plan". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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

Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux |Przypisanie zasad |Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Linux |Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../../../azure-monitor/logs/quick-create-workspace.md). |
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux |Przypisanie zasad |Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu |Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: `[]` |
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows |Przypisanie zasad |Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu |Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: `[]` |
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows |Przypisanie zasad |Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Windows |Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../../../azure-monitor/logs/quick-create-workspace.md). |
|\[Wersja zapoznawcza \] : Inspekcja kontrolek nośników i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisanie zasad |Log Analytics identyfikator obszaru roboczego, dla którego należy skonfigurować maszyny wirtualne |To jest identyfikator (GUID) obszaru roboczego Log Analytics, dla którego należy skonfigurować maszyny wirtualne. |
|\[Wersja zapoznawcza \] : Inspekcja kontrolek nośników i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisanie zasad |Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych |Lista typów zasobów, które mają być objęte inspekcją, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Akceptowalne wartości można znaleźć Azure Monitor w obszarze [schematy dzienników diagnostycznych](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas). |
|\[Wersja zapoznawcza \] : Inspekcja kontrolek nośników i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisanie zasad |Grupa administratorów |Group. Przykład: `Administrator; myUser1; myUser2` |
|\[Wersja zapoznawcza \] : Inspekcja kontrolek nośników i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisanie zasad |Lista użytkowników, którzy powinni zostać dołączeni do grupy administratorów maszyn wirtualnych z systemem Windows |Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: `Administrator; myUser1; myUser2` |
|Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu |Przypisanie zasad |Efekt |Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md). |
|Wdrażanie inspekcji na serwerach SQL |Przypisanie zasad |Wartość w dniach okresu przechowywania (0 oznacza nieograniczony czas przechowywania) |Dni przechowywania (opcjonalnie, _180_ dni, jeśli nie określono) |
|Wdrażanie inspekcji na serwerach SQL |Przypisanie zasad |Nazwa grupy zasobów dla konta magazynu na potrzeby inspekcji programu SQL Server |Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu jest tworzone w każdym regionie, w którym utworzono SQL Server, który jest współużytkowany przez wszystkie serwery w tym regionie). Ważne — w przypadku właściwej operacji inspekcji nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu. |
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń |Przypisanie zasad |Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń |Ten prefiks jest połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu. |
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń |Przypisanie zasad |Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć) |Grupa zasobów, w której jest tworzone konto magazynu. Ta grupa zasobów musi już istnieć. |

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrażaniem przykładu multimediów zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat mapowania przeglądu i kontroli:

> [!div class="nextstepaction"]
> [Plany multimediów — Omówienie](./index.md) 
>  [Plany multimediów — Mapowanie formantów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
