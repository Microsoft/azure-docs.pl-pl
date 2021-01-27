---
title: 'Samouczek: przykład strategii do nowego środowiska'
description: W tym samouczku użyto przykładu strategii do utworzenia definicji planu, która konfiguruje dwie grupy zasobów i konfiguruje przypisanie roli dla każdego z nich.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915560"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Samouczek: Tworzenie środowiska na podstawie przykładu strategii

Przykładowe plany zawierają przykłady czynności, które można wykonać przy użyciu planów platformy Azure. Każdy z nich jest przykładem z konkretnym zamiarem lub przeznaczeniem, ale nie tworzy samodzielnie całego środowiska. Każdy z nich jest przeznaczony do rozpoczęcia eksplorowania przy użyciu planów platformy Azure z różnymi kombinacjami zawartych artefaktów, projektów i parametrów.

Poniższy samouczek korzysta z **grup zasobów z** przykładowym planem RBAC, aby zaprezentować różne aspekty usługi Azure Plans. Omówione są następujące czynności:

> [!div class="checklist"]
> - Utwórz nową definicję planu z przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji
> - Sprawdzanie wdrożonych zasobów do przypisania
> - Cofnij przypisanie planu w celu usunięcia blokad

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka jest wymagana subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-blueprint-definition-from-sample"></a>Utwórz definicję planu z przykładu

Najpierw Zaimplementuj przykład strategii. Importowanie powoduje utworzenie nowego planu w środowisku na podstawie przykładu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź **grupy zasobów z** przykładowym planem RBAC w obszarze _inne przykłady_ i wybierz je.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładu strategii. W tym samouczku zostanie użyta nazwa _2-RGS-with-role-przypisań_.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania lub subskrypcję, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Ten przykład definiuje dwie grupy zasobów z nazwami wyświetlanymi _ProdRG_ i _PreProdRG_. Końcowa nazwa i lokalizacja każdej grupy zasobów są ustawiane podczas przypisywania planu. Do grupy zasobów _ProdRG_ jest przypisana rola _współautor_ , a grupa zasobów _PreProdRG_ jest przypisana do ról _właściciel_ i _czytelnicy_ . Role przypisane w definicji są statyczne, ale użytkownik, aplikacja lub Grupa, do której przypisano rolę, jest ustawiana podczas przypisywania strategii.

1. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

W tym kroku zostanie utworzona kopia przykładowej definicji planu w wybranej grupie zarządzania lub subskrypcji. Zapisana definicja planu jest zarządzana jak każdy plan utworzony od podstaw. Przykład można zapisać do grupy zarządzania lub subskrypcji dowolną liczbę razy. Jednak każda kopia musi mieć unikatową nazwę.

Po **pomyślnym** wyświetleniu powiadomienia portalu dotyczącego zapisywania definicji planu przejdź do następnego kroku.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i wymagań. W tym samouczku nie wprowadzimy żadnych zmian.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć definicję strategii _dwurgs-with-role-przypisania_ , a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. W nowym okienku po prawej stronie Podaj **wersję** _1,0_ dla kopii przykładu planu. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z grup zasobów przy użyciu przykładowego planu RBAC". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

Ten krok umożliwia przypisanie planu do subskrypcji. Po opublikowaniu można nadal wprowadzać zmiany. Dodatkowe zmiany wymagają opublikowania z nową wartością **wersji** , aby śledzić różnice między różnymi wersjami tej samej definicji planu.

Po **pomyślnym** wyświetleniu powiadomienia portalu publikowanie definicji strategii przejdź do następnego kroku.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć definicję strategii _dwurgs-with-role-przypisania_ , a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy definicji planu.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Na potrzeby tego samouczka wybierz pozycję _Wschodnie stany USA 2_.
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję _1,0_ kopii przykładowej definicji planu.

   - Przypisanie blokady

     Wybierz tryb blokowania _tylko do odczytu_ . Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję _przypisaną do systemu_ . Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Dla każdego artefaktu ustaw wartość parametru na wartości zdefiniowane w kolumnie **wartość** . W obszarze `{Your ID}` Wybierz swoje konto użytkownika platformy Azure.

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów ProdRG|Grupa zasobów|Nazwa|ProductionRG|Definiuje nazwę pierwszej grupy zasobów.|
     |Grupa zasobów ProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Ustawia lokalizację pierwszej grupy zasobów.|
     |Współautor|Przypisanie roli|Użytkownik lub Grupa|{Identyfikator}|Definiuje użytkownika lub grupę, która ma udzielić przypisywania roli _współautor_ w ramach pierwszej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Nazwa|PreProductionRG|Definiuje nazwę drugiej grupy zasobów.|
     |Grupa zasobów PreProdRG|Grupa zasobów|Lokalizacja|Zachodnie stany USA|Ustawia lokalizację drugiej grupy zasobów.|
     |Właściciel|Przypisanie roli|Użytkownik lub Grupa|{Identyfikator}|Definiuje użytkownika lub grupę, która ma przyznawać przypisanie roli _właściciela_ w ramach drugiej grupy zasobów.|
     |Czytelnicy|Przypisanie roli|Użytkownik lub Grupa|{Identyfikator}|Definiuje użytkownika lub grupę, która ma udzielić przypisywania roli _czytelnicy_ w ramach drugiej grupy zasobów.|

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony.

W tym kroku wdrożono określone zasoby i skonfiguruje wybrane **przypisanie blokady**. Zastosowanie blokad planów może potrwać do 30 minut.

Po wyświetleniu powiadomienia portalu **przypisywanie definicji planu powiodło** się przejdź do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspekcja zasobów wdrożonych przez przypisanie

Przypisanie planu tworzy i śledzi artefakty zdefiniowane w definicji strategii. Stan zasobów można zobaczyć na stronie przypisanie strategii i przez bezpośrednie przeszukanie zasobów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **przypisane plany** po lewej stronie. Użyj filtrów, aby znaleźć przypisanie strategii _-dwa-RGS-with-role-przypisania_ , a następnie wybierz je.

   Na tej stronie można zobaczyć, że przypisanie zakończyło się pomyślnie, a także listę utworzonych zasobów wraz z ich stanem blokady. Jeśli przypisanie zostało zaktualizowane, lista rozwijana **operacja przypisywania** zawiera szczegółowe informacje dotyczące wdrożenia każdej wersji definicji. Wszystkie utworzone zasoby na liście można wybrać i otworzyć stronę właściwości zasoby.

1. Wybierz grupę zasobów **ProductionRG** .

   Zobaczymy, że nazwa grupy zasobów to **ProductionRG** , a nie nazwa wyświetlana artefaktu _ProdRG_. Ta nazwa jest zgodna z wartością ustawioną podczas przypisywania strategii.

1. Wybierz stronę **Kontrola dostępu (IAM)** po lewej stronie, a następnie kartę **przypisania ról** .

   Tutaj widzimy, że Twoje konto ma przydzielone role _współautor_ względem zakresu _tego zasobu_. Przypisanie planu _przypisania 2-RGS-z-rolą_ ma rolę _właściciela_ , która została użyta do utworzenia grupy zasobów. Te uprawnienia są również używane do zarządzania zasobami ze skonfigurowanymi blokadami planów.

1. Ze stron nadrzędnych Azure Portal wybierz pozycję **przypisanie — dwa-RGS-with-role-przypisań** , aby przejść do tyłu jednej strony, a następnie wybierz grupę zasobów **PreProductionRG** .

1. Wybierz stronę **Kontrola dostępu (IAM)** po lewej stronie, a następnie kartę **przypisania ról** .

   W tym miejscu widzimy, że Twoje konto ma przyznane zarówno rolę _właściciela_ , jak i _czytelnika_ , zarówno w zakresie _tego zasobu_. Przypisanie planu ma także rolę _właściciela_ , taką jak pierwsza grupa zasobów.

1. Wybierz kartę **Odmów przypisań** .

   Przypisanie planu spowodowało utworzenie [przypisania odmowy](../../../role-based-access-control/deny-assignments.md) w wdrożonej grupie zasobów w celu wymuszenia trybu blokowania _tylko do odczytu_ . Przypisanie Odmów uniemożliwia komuś z odpowiednimi prawami na karcie _przypisania roli_ w celu podjęcia określonych działań. Przypisanie Odmów ma wpływ na _wszystkich podmiotów zabezpieczeń_.

1. Zaznacz pozycję Odmów przypisania, a następnie wybierz stronę **odmowa uprawnień** po lewej stronie.

   Przypisanie odmowy uniemożliwia wszystkie operacje z konfiguracją **\* akcji *_ i _*** , ale zezwala na dostęp do odczytu przez wykluczenie **\* /Read** **za pośrednictwem** noiss.

1. Ze stron nadrzędnych Azure Portal wybierz pozycję **Kontrola dostępu PreProductionRG (IAM)**. Następnie wybierz stronę **Przegląd** po lewej stronie, a następnie przycisk **Usuń grupę zasobów** . Wprowadź nazwę _PreProductionRG_ , aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń** w dolnej części okienka.

   Wyświetlenie powiadomienia portalu **nie powiodło się usunięcie grupy zasobów PreProductionRG** . W przypadku, gdy konto ma uprawnienia do usuwania grupy zasobów, zostanie odmowa dostępu przez przypisanie planu. Należy pamiętać, że podczas przypisywania strategii zaznaczono tryb blokowania _tylko do odczytu_ . Blokada strategii uniemożliwia konto z uprawnieniami, nawet _właściciel_, od usunięcia zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze zasoby zostały utworzone zgodnie z definicją, a blokady planów uniemożliwiły niepożądane usunięcie, nawet z konta z uprawnieniami.

## <a name="unassign-the-blueprint"></a>Cofnij przypisanie planu

Ostatnim krokiem jest usunięcie przydziału planu i wdrożonych zasobów.
Usunięcie przypisania nie powoduje usunięcia wdrożonych artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **przypisane plany** po lewej stronie. Użyj filtrów, aby znaleźć przypisanie strategii _-dwa-RGS-with-role-przypisania_ , a następnie wybierz je.

1. Wybierz przycisk **Cofnij przypisanie** planu w górnej części strony. Zapoznaj się z ostrzeżeniem w oknie dialogowym potwierdzenia, a następnie wybierz przycisk **OK**.

   Po usunięciu przypisania planu blokady planów również są usuwane. Utworzone zasoby mogą być ponownie usuwane przez konto z uprawnieniami.

1. Wybierz pozycję **grupy zasobów** z menu platformy Azure, a następnie wybierz pozycję **ProductionRG**.

1. Wybierz stronę **Kontrola dostępu (IAM)** po lewej stronie, a następnie kartę **przypisania ról** .

Zabezpieczenia dla każdej grupy zasobów nadal mają wdrożone przypisania ról, ale przypisanie strategii nie ma już dostępu _właściciela_ .

Gdy zostanie wyświetlone powiadomienie o **usunięciu przypisania** strategii, przejdź do następnego kroku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Usuń następujące zasoby:

- _ProductionRG_ grupy zasobów
- _PreProductionRG_ grupy zasobów
- Definicja planu _dwa-RGS-with-role-przypisań_

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć nowy plan z przykładowej definicji. Aby dowiedzieć się więcej na temat planów platformy Azure, przejdź do artykułu dotyczącego cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](../concepts/lifecycle.md)