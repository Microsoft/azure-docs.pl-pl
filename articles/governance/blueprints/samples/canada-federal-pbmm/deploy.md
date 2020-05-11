---
title: Przykład wdrażania planu Federal PBMM z Kanady
description: Wdróż kroki dla przykładu Federalnej usługi PBMM w Kanadzie, w tym szczegóły parametru artefaktu.
ms.date: 05/08/2020
ms.topic: sample
ms.openlocfilehash: f9234b3ff44d3536380e19cb685ca4e3bbd9b0a5
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005696"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>Wdróż przykłady planu PBMM federalnego Kanady

Aby wdrożyć przykłady planów Federal PBMM w Kanadzie, należy podjąć następujące kroki:

> [!div class="checklist"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free) .

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź próbkę **Federal PBMM Canada** w obszarze _inne przykłady_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładu strategii.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może spowodować przeniesienie go do warstwy Standardowa.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu Federal PBMM Canada". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisz przykładową kopię

Po pomyślnym **opublikowaniu**kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. Ten krok polega na tym, że parametry są dostarczane, aby każde wdrożenie kopii przykładowej strategii było unikatowe.

1. Wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **zasady** w okienku po lewej stronie. Na stronie **zasady** wybierz pozycję **plany**.

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

Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja\]zapoznawcza: wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux |Przypisywanie zasad |Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Linux |Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Wersja\]zapoznawcza: wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux |Przypisywanie zasad |Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu |Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych:`[]` |
|\[Wersja\]zapoznawcza: wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows |Przypisywanie zasad |Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu |Pustą tablicę można użyć do wskazania braku parametrów opcjonalnych:`[]` |
|\[Wersja\]zapoznawcza: wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows |Przypisywanie zasad |Obszar roboczy Log Analytics dla maszyn wirtualnych z systemem Windows |Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Wersja\]zapoznawcza: Inspekcja federalnych PBMM formantów w Kanadzie i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisywanie zasad |Log Analytics identyfikator obszaru roboczego, dla którego należy skonfigurować maszyny wirtualne |To jest identyfikator (GUID) obszaru roboczego Log Analytics, dla którego należy skonfigurować maszyny wirtualne. |
|\[Wersja\]zapoznawcza: Inspekcja federalnych PBMM formantów w Kanadzie i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisywanie zasad |Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych |Lista typów zasobów, które mają być objęte inspekcją, jeśli ustawienie dziennika diagnostycznego nie jest włączone. Akceptowalne wartości można znaleźć Azure Monitor w obszarze [schematy dzienników diagnostycznych](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[Wersja\]zapoznawcza: Inspekcja federalnych PBMM formantów w Kanadzie i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisywanie zasad |Grupa administratorów |Group. Przykład: `Administrator; myUser1; myUser2` |
|\[Wersja\]zapoznawcza: Inspekcja federalnych PBMM formantów w Kanadzie i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Przypisywanie zasad |Lista użytkowników, którzy powinni zostać dołączeni do grupy administratorów maszyn wirtualnych z systemem Windows |Rozdzielana średnikami lista elementów członkowskich, które powinny być uwzględnione w lokalnej grupie Administratorzy. Przykład: `Administrator; myUser1; myUser2` |
|Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu |Przypisywanie zasad |Efekt |Informacje dotyczące efektów zasad można znaleźć w [opisie efektów Azure Policy](../../../policy/concepts/effects.md). |
|Wdrażanie inspekcji na serwerach SQL |Przypisywanie zasad |Wartość w dniach okresu przechowywania (0 oznacza nieograniczony czas przechowywania) |Dni przechowywania (opcjonalnie, _180_ dni, jeśli nie określono) |
|Wdrażanie inspekcji na serwerach SQL |Przypisywanie zasad |Nazwa grupy zasobów dla konta magazynu na potrzeby inspekcji programu SQL Server |Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu jest tworzone w każdym regionie, w którym utworzono SQL Server, który jest współużytkowany przez wszystkie serwery w tym regionie). Ważne — w przypadku właściwej operacji inspekcji nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu. |
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń |Przypisywanie zasad |Prefiks konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń |Ten prefiks jest połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu. |
|Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń |Przypisywanie zasad |Nazwa grupy zasobów dla konta magazynu dla diagnostyki sieciowej grupy zabezpieczeń (musi istnieć) |Grupa zasobów, w której jest tworzone konto magazynu. Ta grupa zasobów musi już istnieć. |

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrożeniem przykładowej PBMM Federalnej Kanady zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat mapowania i kontroli:

> [!div class="nextstepaction"]
> [Kanada Federal PBMM Plans — Omówienie](./index.md)
> [planów federalnych PBMMych w Kanadzie — Mapowanie formantów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
