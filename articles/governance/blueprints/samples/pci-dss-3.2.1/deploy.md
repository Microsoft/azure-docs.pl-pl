---
title: Przykład wdrażania planu PCI-DSS v 3.2.1
description: Wdróż kroki dla przykładowej usługi Payment Card Industry Data Security Standard v 3.2.1 z uwzględnieniem szczegółów parametrów artefaktu.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 9df9392430fd0496ffb5e635a6fc3e31ba708208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76905558"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Wdróż przykład planu PCI-DSS v 3.2.1

Aby wdrożyć przykład planów PCI-DSS w wersji 3.2.1, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Utwórz nowy plan z przykładu
> - Oznacz swoją kopię przykładowej publikacji jako **opublikowaną**
> - Przypisz swoją kopię planu do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free) .

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw Zaimplementuj przykład strategii, tworząc nowy plan w środowisku przy użyciu przykładu jako starter.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz przycisk **Utwórz** w obszarze _Utwórz plan_.

1. Znajdź przykład planu **PCI-DSS v 3.2.1** pod _innymi_ przykładami i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawy_ przykładu planu:

   - **Nazwa**planu: Podaj nazwę kopii przykładu planu PCI-DSS w wersji 3.2.1.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _artefakty_ w górnej części strony lub **Następny: artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zostaną zdefiniowane w dalszej części. Wybierz pozycję **Zapisz wersję roboczą** po zakończeniu przeglądania przykładu planu.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładowej

Twoja kopia przykładu strategii została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być **opublikowany** , aby można go było przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może ją przenieść z warstwy PCI-DSS v 3.2.1 Standard.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **definicje** strategii po lewej stronie. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Publikuj plan** w górnej części strony. Na nowej stronie z prawej strony Podaj **wersję** kopii przykładowej strategii. Ta właściwość jest przydatna w przypadku późniejszej modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu planu PCI-DSS v 3.2.1". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
|\[Inspekcja w wersji zapoznawczej\] PCI v 3.2.1:2018 r kontrolki i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji|Przypisanie zasad|Lista typów zasobów | Inspekcja ustawień diagnostycznych dla wybranych typów zasobów. Wartość domyślna to wszystkie zasoby są wybrane| 
|Dozwolone lokalizacje|Przypisanie zasad|Lista dozwolonych lokalizacji|Lista lokalizacji centrów danych dozwolonych dla każdego zasobu, który ma zostać wdrożony w programie. Ta lista jest dostosowywana do odpowiednich lokalizacji platformy Azure globalnie. Wybierz lokalizacje, które chcesz zezwolić.| 
|Dozwolone lokalizacje grup zasobów|Przypisanie zasad |Dozwolona lokalizacja |Te zasady pozwalają ograniczyć lokalizacje, w których organizacja może tworzyć grupy zasobów w programie. Służy do wymuszania wymagań dotyczących zgodności obszarów geograficznych.| 
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Dni przechowywania|Przechowywanie danych w liczbie dni. Wartość domyślna to 180, ale PCI wymaga 365.| 
|Wdrażanie inspekcji na serwerach SQL|Przypisanie zasad|Nazwa grupy zasobów dla konta magazynu|Inspekcja zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage (konto magazynu zostanie utworzone w każdym regionie, w którym zostanie utworzone SQL Server, które będzie współużytkowane przez wszystkie serwery w tym regionie).| 

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków związanych z wdrożeniem przykładu planu PCI-DSS v 3.2.1 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat mapowania przeglądu i kontroli:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 plan — przegląd](./index.md)
> [PCI-DSS v 3.2.1 — Mapowanie formantów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
