---
title: Przykład wdrażania planu CAF migracji dla strefy docelowej
description: Wdróż kroki dla planu strefy CAF migracji, w tym szczegóły parametrów artefaktu.
ms.date: 05/06/2020
ms.topic: sample
ms.openlocfilehash: 109c9a2c4c5670d6f5a676498ae1010a4ab0e418
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "82871185"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Wdrażanie przykładowej platformy wdrażania Microsoft Cloud dla platformy Azure Migrowanie planu strefy ładunkowej

Aby wdrożyć przykład strategii CAF migracji planów platformy Azure, należy wykonać następujące czynności:

> [!div class="checklist1"]
> - Zalecane do wdrożenia przykładu strategii [CAF Foundation](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź przykład planu **strefy CAF migracji** w ramach _innych próbek_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:
   - **Nazwa** planu Podaj nazwę kopii przykładowego planu strefy CAF migracji.
   - **Lokalizacja definicji** Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może zostać przeniesiona z przewodnika po CAF migracji strefy ładunkowej.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładowej strategii CAF migracji na żywo". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość.
     - Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii.
       Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji**planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.
    
   - Przypisanie blokady

     Wybierz ustawienie blokowania planu dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Wybierz opcję Domyślna _przypisana przez system_ lub opcję tożsamości _przypisanej do użytkownika_ .

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.

       - **Organizacja**: Wprowadź nazwę organizacji, taką jak contoso lub Fabrikam, musi być unikatowa.
       - **AzureRegion**: Wybierz jeden region świadczenia usługi Azure na potrzeby wdrożenia.
       
   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w ramach którego jest zdefiniowany. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Utworzono przypisanie strategii i rozpocznie się wdrażanie artefaktów. Wdrożenie trwa około 5 minut. Aby sprawdzić stan wdrożenia, Otwórz przypisanie planu.

> [!WARNING]
> Usługa plany platformy Azure i wbudowane przykłady planów są **bezpłatne**. Zasoby platformy Azure są [wyceniane według produktu](https://azure.microsoft.com/pricing/). Za pomocą [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) można oszacować koszt uruchamiania zasobów wdrożonych przez ten przykład strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Wdróż wirtualną strefę wyładunkowej|Szablon usługi Resource Manager|IPAddress_Space|**Zablokowane** — Podaj pierwsze dwa oktety, 10,0|
|Wdróż Key Vault|Szablon usługi Resource Manager|KV — AccessPolicy|**Zablokowany** — Grupa lub identyfikator obiektu użytkownika, aby udzielić uprawnień do w Key Vault|
|Wdróż Log Analytics|Szablon usługi Resource Manager|LogAnalytics_DataRetention|Dane z **zablokowanej** liczby dni zostaną zachowane w log Analytics|
|Wdróż Log Analytics|Szablon usługi Resource Manager|LogAnalytics_Location|Region **zablokowany** używany podczas ustanawiania obszaru roboczego|
|Wdróż Azure Migrate|Szablon usługi Resource Manager|Azure_Migrate_Location|**Zablokowane** — wybierz region do wdrożenia Azure Migrate|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładu CAF migrowania strefy ładunkowej, odwiedź następujące artykuły, aby dowiedzieć się więcej o architekturze:

> [!div class="nextstepaction"]
> [CAF Migrowanie strefy docelowej — Omówienie](./index.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
