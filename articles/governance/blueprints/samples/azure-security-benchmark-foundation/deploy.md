---
title: Przykład wdrażania planu usługi Azure Security test Foundation
description: Kroki wdrażania przykładowego planu usługi Azure Security test Foundation, w tym szczegóły parametru artefaktu planu.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: aaaabc8767c6d80548a26d64d8557587180fb6f3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095299"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Wdróż przykład planu usługi Azure Security test Foundation

Aby wdrożyć przykładowy plan usługi Azure Security test Foundation, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład planu **usługi Azure Security test Foundation** w obszarze _inne przykłady_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego planu usługi Azure Security test Foundation.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowego planu można dostosować do środowiska i potrzeb, ale modyfikacja może go przenieść z planu usługi Azure Security test Foundation.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładowego planu usługi Azure Security test Foundation". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość.
     - Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii.
       Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Wybierz opcję domyślną tożsamości zarządzanej _przypisanej przez system_ lub opcję tożsamości _przypisanej przez użytkownika_.

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.
    
     - **Prefiks zasobów i grup zasobów**: ten ciąg jest używany jako prefiks dla wszystkich nazw zasobów i grup zasobów
     - **Nazwa centrum**: nazwa centrum
     - **Przechowywanie dzienników (w dniach)**: liczba dni zachowywania dzienników; wprowadzenie wartości "0" powoduje, że dzienniki są przechowywane przez czas nieokreślony
     - **Wdróż centrum**: wprowadź wartość "true" lub "false", aby określić, czy przypisanie wdraża składniki centrum architektury
     - **Lokalizacja centrum**: Lokalizacja grupy zasobów centrum
     - **Docelowe adresy IP**: docelowe adresy IP dla łączności wychodzącej; rozdzielana przecinkami lista adresów IP lub prefiksów zakresów adresów IP
     - **Nazwa Network Watcher**: nazwa zasobu Network Watcher
     - **Nazwa grupy zasobów Network Watcher**: Nazwa Network Watcher grupy zasobów
     - **Włącz ochronę DDoS**: wprowadź wartość "prawda" lub "fałsz", aby określić, czy DDoS Protection jest włączona w sieci wirtualnej
     
    > [!NOTE] 
    > Jeśli Network Watcher jest już włączona, zaleca się użycie istniejącej Network Watcher grupy zasobów. Należy również podać lokalizację istniejącej Network Watcher grupy zasobów dla parametru artefaktu **Network Watcher lokalizacji grupy zasobów**.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Grupa zasobów centrum|Grupa zasobów|Nazwa grupy zasobów|Zablokowany — łączy prefiks z nazwą centrum|
|Grupa zasobów centrum|Grupa zasobów|Lokalizacja grupy zasobów|Zablokowane — używa lokalizacji centrum|
|Szablon zapory platformy Azure|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure||
|Azure Log Analytics i szablon diagnostyki|Szablon usługi Resource Manager|Log Analytics lokalizacji obszaru roboczego|Lokalizacja, w której jest tworzony Log Analytics obszar roboczy; Uruchom `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` w usłudze Azure PowersShell, aby wyświetlić dostępne regiony|
|Azure Log Analytics i szablon diagnostyki|Szablon usługi Resource Manager|Identyfikator konta Azure Automation (opcjonalnie)|Identyfikator zasobu konta usługi Automation; służy do tworzenia połączonej usługi między Log Analytics i kontem usługi Automation|
|Szablon sieciowej grupy zabezpieczeń platformy Azure|Szablon usługi Resource Manager|Włącz dzienniki przepływu sieciowej grupy zabezpieczeń|Wprowadź wartość "true" lub "false", aby włączyć lub wyłączyć dzienniki przepływu sieciowej grupy zabezpieczeń|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu sieci wirtualnej|Prefiks adresu sieci wirtualnej dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu podsieci zapory|Prefiks adresu podsieci zapory dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu podsieci bastionu|Prefiks adresu podsieci bastionu dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy|Prefiks adresu podsieci bramy dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu podsieci zarządzania|Prefiks adresu podsieci zarządzania dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiks adresu podsieci pola skoku|Prefiks adresu podsieci pola skoku dla sieci wirtualnej centrum|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Nazwy adresów podsieci (opcjonalnie)|Tablica nazw podsieci do wdrożenia w sieci wirtualnej centrum; na przykład "subnet1", "podsieci subnet2"|
|Szablon centrum Virtual Network platformy Azure|Szablon usługi Resource Manager|Prefiksy adresów podsieci (opcjonalnie)|Tablica prefiksów adresów IP dla opcjonalnych podsieci dla sieci wirtualnej koncentratora; na przykład "10.0.7.0/24", "10.0.8.0/24"|
|Grupa zasobów szprych|Grupa zasobów|Nazwa grupy zasobów|Zablokowany — łączy prefiks z nazwą szprychy|
|Grupa zasobów szprych|Grupa zasobów|Lokalizacja grupy zasobów|Zablokowane — używa lokalizacji centrum|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Wdróż szprychę|Wprowadź wartość "true" lub "false", aby określić, czy przypisanie wdraża składniki szprych architektury|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Identyfikator subskrypcji centrum|Identyfikator subskrypcji, w której jest wdrożony centrum; wartość domyślna to subskrypcja, w której znajduje się definicja planu|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Nazwa szprychy|Nazwa szprychy|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Prefiks adresu Virtual Network|Prefiks adresu Virtual Network dla sieci wirtualnej szprychy|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Prefiks adresu podsieci|Prefiks adresu podsieci dla sieci wirtualnej szprychy|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Nazwy adresów podsieci (opcjonalnie)|Tablica nazw podsieci do wdrożenia w sieci wirtualnej szprychy; na przykład "subnet1", "podsieci subnet2"|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Prefiksy adresów podsieci (opcjonalnie)|Tablica prefiksów adresów IP dla opcjonalnych podsieci dla sieci wirtualnej szprychy; na przykład "10.0.7.0/24", "10.0.8.0/24"|
|Szablon usługi Azure Virtual Network szprych|Szablon usługi Resource Manager|Wdróż szprychę|Wprowadź wartość "true" lub "false", aby określić, czy przypisanie wdraża składniki szprych architektury|
|Szablon Network Watcher platformy Azure|Szablon usługi Resource Manager|Lokalizacja Network Watcher|Lokalizacja zasobu Network Watcher|
|Szablon Network Watcher platformy Azure|Szablon usługi Resource Manager|Lokalizacja grupy zasobów Network Watcher|Jeśli Network Watcher jest już włączona, wartość tego parametru **musi** być zgodna z nazwą istniejącej Network Watcher grupy zasobów.|

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` , sprawdź, czy parametr planu **Network Watcher nazwa grupy zasobów** określa istniejącą Network Watcher nazwę grupy zasobów i czy parametr artefaktu **Network Watcher lokalizacji grupy zasobów** określa istniejącą lokalizację Network Watcher grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładowego planu usługi Azure Security test Foundation zapoznaj się z następującym artykułem, aby dowiedzieć się więcej o architekturze:

> [!div class="nextstepaction"]
> [Plan usługi Azure Security test Foundation — Omówienie](./index.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
