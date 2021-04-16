---
title: Przykład strategii DoD Impact Level 4
description: Kroki wdrażania przykładu strategii DoD Impact Level 4, w tym szczegóły parametru artefaktu strategii.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: ce0d2d162bf77c147e0e4bc26e68964cd50f4372
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378452"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Wdrażanie przykładowej strategii DoD Impact Level 4

Aby wdrożyć Azure Blueprints strategii Department of Defense Impact Level 4 (DoD IL4), należy wykonać następujące kroki:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji usługi Azure Government, przed rozpoczęciem poproś o subskrypcję [wersji](https://azure.microsoft.com/global-infrastructure/government/request/) próbnej.

## <a name="create-blueprint-from-sample"></a>Tworzenie strategii na przykładzie

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład **strategii DoD Impact Level 4 w** obszarze _Inne przykłady_ i wybierz **pozycję Użyj tego przykładu.**

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa strategii:** podaj nazwę kopii przykładu strategii DoD Impact Level 4.
   - **Lokalizacja definicji:** użyj wielokropka i wybierz grupę zarządzania, w którym chcesz zapisać kopię przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładu strategii można dostosować do twojego środowiska i potrzeb, ale ta modyfikacja może odejść od dopasowania do kontrolek DoD Impact Level 4.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **uwagi dotyczące zmian,** takie jak "Pierwsza wersja opublikowana z przykładu strategii DoD IL4". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym opublikowaniu kopii przykładu strategii można ją przypisać do subskrypcji w grupie zarządzania, w której została zapisana. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje:** wybierz co najmniej jedną subskrypcję, która należy do grupy zarządzania, w ramach których zapisano kopię przykładu strategii. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania:** nazwa jest wstępnie wypełniana na podstawie nazwy strategii.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja:** wybierz region, w którym ma zostać utworzona tożsamość zarządzana. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Wersja definicji strategii:** wybierz **opublikowaną** wersję kopii przykładu strategii.

   - Blokowanie przypisania

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną _opcję tożsamości zarządzanej przypisanej_ przez system.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Te parametry [są parametrami](../../concepts/parameters.md#dynamic-parameters) dynamicznymi, ponieważ są definiowane podczas przypisywania strategii. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabela parametrów artefaktu](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Dozwolone lokalizacje|Przypisanie zasad|Dozwolone lokalizacje|Ta zasada umożliwia ograniczenie lokalizacji, które organizacja może określić podczas wdrażania zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.|
|Dozwolone lokalizacje dla grup zasobów|Przypisanie zasad |Dozwolone lokalizacje|Te zasady umożliwiają ograniczenie lokalizacji, w których organizacja może tworzyć grupy zasobów. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Wartość w dniach okresu przechowywania (0 oznacza nieograniczone przechowywanie)|Dni przechowywania (opcjonalnie 180 dni, jeśli nieokreślone)|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu do inspekcji programu SQL Server|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzony SQL Server, który będzie współużytowany przez wszystkie serwery w tym regionie). Ważne — w celu prawidłowego działania inspekcji nie należy usuwać ani zmieniać nazwy grupy zasobów ani kont magazynu.|
|Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń|Przypisanie zasad|Prefiks konta magazynu na potrzeby diagnostyki sieciowej grupy zabezpieczeń|Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu.|
|Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu na potrzeby diagnostyki sieciowej grupy zabezpieczeń (musi istnieć)|Grupa zasobów, w których zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć.|
|Wdrażanie agenta usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Linux|Przypisanie zasad|Obszar roboczy usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnień "Współautor usługi Log Analytics" (lub podobnych) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które mają obsługiwany system operacyjny Linux do dodania do zakresu|Pusta tablica może służyć do wskazywania żadnych parametrów opcjonalnych: \[\]|
|Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Obszar roboczy usługi Log Analytics dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnień "Współautor usługi Log Analytics" (lub podobnych) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które mają obsługiwany system operacyjny Linux do dodania do zakresu|Pusta tablica może służyć do wskazywania żadnych parametrów opcjonalnych: \[\]|
|Wdrażanie agenta usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows|Przypisanie zasad|Obszar roboczy usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnień "Współautor usługi Log Analytics" (lub podobnych) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta usługi Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które mają obsługiwany system operacyjny Windows do dodania do zakresu|Pusta tablica może służyć do wskazywania żadnych parametrów opcjonalnych: \[\]|
|Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Obszar roboczy usługi Log Analytics dla maszyn wirtualnych z systemem Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnień "Współautor usługi Log Analytics" (lub podobnych) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|Wdrażanie agenta usługi Log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych, które mają obsługiwany system operacyjny Windows do dodania do zakresu|Pusta tablica może służyć do wskazywania żadnych parametrów opcjonalnych: \[\]|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Członkowie do dołączona do grupy lokalnej Administratorzy|Rozdzielana średnikami lista elementów członkowskich, które powinny być wykluczone z grupy lokalnej Administratorzy. Na przykład: Administrator; myUser1; użytkownik myUser2|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Członkowie, którzy powinni być wykluczeni z grupy lokalnej Administratorzy|Rozdzielana średnikami lista elementów członkowskich, które powinny zostać uwzględnione w lokalnej grupie Administratorzy. Na przykład: Administrator; myUser1; myUser2|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Lista typów zasobów, które powinny mieć włączone dzienniki diagnostyczne|Lista typów zasobów do inspekcji, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Dopuszczalne wartości można znaleźć w Azure Monitor [schematów dzienników diagnostycznych.](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Identyfikator obszaru roboczego usługi Log Analytics, dla których należy skonfigurować maszyny wirtualne|Jest to identyfikator (GUID) obszaru roboczego usługi Log Analytics, dla których należy skonfigurować maszyny wirtualne.|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Długoterminowe geograficznie nadmiarowe kopie zapasowe powinny być włączone dla baz Azure SQL danych|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Zrozumienie [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Na serwerach SQL należy włączyć ocenę luk w zabezpieczeniach|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Zrozumienie [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Magazyn geograficznie nadmiarowy powinien być włączony dla kont magazynu|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Zrozumienie [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Zrozumienie [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Przestarzałe konta powinny zostać usunięte z subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|CorS nie powinien zezwalać każdemu zasobowi na dostęp do aplikacji internetowej|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Azure Policy [Efekty zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Należy zainstalować aktualizacje systemu w zestawach skalowania maszyn wirtualnych|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Opis [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Opis [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Opis [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|
|\[Wersja \] zapoznawcza: DoD Impact Level 4|Przypisanie zasad|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w subskrypcji|Informacje o efektach zasad można znaleźć na stronie Understand Azure Policy Effects (Opis [efektów Azure Policy zasad).](../../../policy/concepts/effects.md)|

## <a name="next-steps"></a>Następne kroki

Teraz, po przejrzenia kroków wdrażania przykładu strategii DoD Impact Level 4, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o mapowaniu strategii i kontroli:

> [!div class="nextstepaction"]
> [Strategia DoD Impact Level 4 — omówienie](./index.md) 
>  [Strategia DoD Impact Level 4 — mapowanie kontrolek](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
