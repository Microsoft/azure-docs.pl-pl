---
title: Przykład wdrażania FedRAMP High plan
description: Wdróż kroki dla przykładu FedRAMP High plan, w tym szczegóły parametru artefaktu strategii.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 53d48eff79f7227373c1e7dfb0c34ef17a00e125
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594766"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>Wdróż przykład FedRAMP High plan

Aby wdrożyć usługę Azure Plans FedRAMP High plan, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład **FedRAMP High** plan w obszarze _inne przykłady_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładu FedRAMP High plan.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może nie przełączać się do wyrównania z FedRAMP wysokimi kontrolkami.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu FedRAMP High plan". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

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
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Log Analytics identyfikator obszaru roboczego, dla którego należy skonfigurować maszyny wirtualne|To jest identyfikator (GUID) obszaru roboczego Log Analytics, dla którego należy skonfigurować maszyny wirtualne.|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienia dziennika diagnostycznego. Akceptowalne wartości można znaleźć Azure Monitor w obszarze [schematy dzienników diagnostycznych](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista użytkowników, którzy powinni być wykluczeni z grupy administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być wykluczone w grupie lokalnej Administratorzy. Przykład: administrator; Użytkownik1; Do|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista użytkowników, którzy powinni zostać dołączeni do grupy administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: administrator; Użytkownik1; Do|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Linux VM Scale Sets (VMSS)|Przypisanie zasad|Log Analytics obszar roboczy dla VM Scale Sets systemu Linux (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Linux VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Linux|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Log Analytics obszar roboczy dla VM Scale Sets systemu Windows (VMSS)|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Windows|Jeśli ten obszar roboczy znajduje się poza zakresem przypisania, musisz ręcznie udzielić uprawnienia "Log Analytics współautor" (lub podobnego) do identyfikatora podmiotu zabezpieczeń przypisania zasad.|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych: \[\]|
|Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu|Przypisanie zasad|Efekt|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Wartość w dniach okresu przechowywania (0 oznacza nieograniczony czas przechowywania)|Dni przechowywania (opcjonalnie, 180 dni, jeśli nie określono)|
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu na potrzeby inspekcji programu SQL Server|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzone SQL Server, które będzie współużytkowane przez wszystkie serwery w tym regionie). Ważne — w przypadku właściwej operacji inspekcji nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu.|
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń|Przypisanie zasad|Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń|Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu.|
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć)|Grupa zasobów, w której zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć.|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Dozwolone lokalizacje dla zasobów i grup zasobów|Lista lokalizacji platformy Azure, które organizacja może określić podczas wdrażania zasobów. Ta podana wartość jest również używana przez zasady "dozwolone lokalizacje" w ramach inicjatywy Policy.|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Ocena luk w zabezpieczeniach powinna być włączona na Virtual Machines|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Magazyn Geograficznie nadmiarowy powinien być włączony dla kont magazynu|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Przestarzałe konta powinny zostać usunięte z subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do zapisu w subskrypcji|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|
|\[Wersja zapoznawcza \] : Inspekcja FedRAMP wysoka kontrola i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database|Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md).|


## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrożeniem przykładu FedRAMP High plan zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o mapowaniu planów i kontroli:

> [!div class="nextstepaction"]
> [FedRAMP wysoki plan — Omówienie](./index.md) 
>  [FedRAMP wysokiego planu kontroli planów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
