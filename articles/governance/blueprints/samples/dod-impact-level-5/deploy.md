---
title: Przykład planu wpływu na poziom 5 planów
description: Wdróż kroki dla przykładowego poziomu wpływu na 5 planów, w tym szczegóły parametrów artefaktu.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 6136136eef5d405ae0849e5ce8c8faede138fb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034904"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Wdrażanie przykładu planu wpływu na poziom DoD 5

Aby wdrożyć Departament planów platformy Azure na poziomie 5 (DoD IL5), należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład na **poziomie "5** " planu wpływu na _Pozostałe próbki_ , a następnie wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego poziomu wpływu na 5.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może przechodzić na wyrównanie z poziomu 5 formantów o poziomie wpływu na wartość DoD.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu planu dod IL5". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
|DoD Impact Level 5|Przypisanie zasad|Lista użytkowników, którzy muszą być uwzględnieni w grupie administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista użytkowników, którzy powinni być uwzględnieni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|DoD Impact Level 5|Przypisanie zasad|Lista użytkowników wykluczonych z grupy administratorów maszyn wirtualnych systemu Windows|Rozdzielana średnikami lista użytkowników, którzy powinni zostać wykluczeni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|DoD Impact Level 5|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych||
|DoD Impact Level 5|Przypisanie zasad|Log Analytics identyfikator obszaru roboczego dla raportowania agenta maszyny wirtualnej|Identyfikator (GUID) obszaru roboczego Log Analytics, w którym agenci maszyn wirtualnych powinni zgłosić|
|DoD Impact Level 5|Przypisanie zasad|Lista regionów, w których należy włączyć Network Watcher|Aby wyświetlić pełną listę regionów, użyj polecenie Get-AzLocation,|
|DoD Impact Level 5|Przypisanie zasad|Minimalna wersja protokołu TLS dla serwerów sieci Web systemu Windows|Minimalna wersja protokołu TLS, która powinna być włączona na serwerach sieci Web systemu Windows|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka PHP|Najnowsza obsługiwana wersja języka PHP dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Java|Najnowsza obsługiwana wersja języka Java dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Python systemu Windows|Najnowsza obsługiwana wersja języka Python dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Linux|Najnowsza obsługiwana wersja języka Python dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z systemem Windows, które obsługują agenta Log Analytics do dodania do zakresu inspekcji|Rozdzielana średnikami lista obrazów; Przykład:/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage|
|DoD Impact Level 5|Przypisanie zasad|Opcjonalne: lista obrazów maszyn wirtualnych z systemem Linux, które obsługują agenta Log Analytics do dodania do zakresu inspekcji|Rozdzielana średnikami lista obrazów; Przykład:/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: powinien być przypisany do subskrypcji więcej niż jeden właściciel|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć powiadomienie E-mail do właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności.|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacja funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć Transparent Data Encryption w bazach danych SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Administrator Azure Active Directory powinien zostać zainicjowany dla serwerów SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć tylko bezpieczne połączenia z Redis Cache|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Przeprowadź inspekcję nieograniczonego dostępu sieciowego do kont magazynu|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji w zestawach skalowania maszyn wirtualnych należy skorygować|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć bezpieczny transfer do kont magazynu|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć kopię zapasową nadmiarową dla Azure Database for PostgreSQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy podać adres e-mail kontaktu z zabezpieczeniami dla Twojej subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: konta przestarzałe powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: usterki należy skorygować w rozwiązaniu do oceny luk w zabezpieczeniach|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć kopię zapasową nadmiarową dla Azure Database for MySQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli używana jako część aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: aktualizacje systemu powinny być zainstalowane na maszynach|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja "HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: usługi MFA powinny mieć włączone konta z uprawnieniami do zapisu w Twojej subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy wybrać warstwę cenową standardowa Security Center|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Inspekcja użycia niestandardowych reguł RBAC|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć inspekcję w programie SQL Server|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Agent Log Analytics powinien być zainstalowany na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: powinien być włączony Standard DDoS Protection|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami właściciela w ramach subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniach zarządzanych SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: powiadomienia E-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Monitoruj brakujące Endpoint Protection w Azure Security Center|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Kontrola dostępu just in Time do sieci powinna być stosowana na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy podać numer telefonu kontaktu zabezpieczeń dla subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: klastry Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystko" w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Magazyn Geograficznie nadmiarowy powinien być włączony dla kont magazynu|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: powiadomienia E-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych programu SQL Server.|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: długoterminowe geograficznie nadmiarowe kopie zapasowe powinny być włączone dla baz danych Azure SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji na maszynach należy skorygować|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami do odczytu w ramach subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla API Apps|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: konta przestarzałe z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Agent Log Analytics powinien być zainstalowany na Virtual Machine Scale Sets|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: należy używać najnowszej wersji protokołu TLS w aplikacji sieci Web|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: w aplikacja funkcji powinna być używana najnowsza wersja protokołu TLS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: [wersja zapoznawcza]: usługi Kubernetes należy uaktualnić do wersji Kubernetes, która nie jest zagrożona|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: luki w zabezpieczeniach baz danych SQL należy skorygować|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładu "5" dotyczącego poziomu wpływu na plan, odwiedź następujące artykuły, aby dowiedzieć się więcej o mapowaniu planu i kontroli:

> [!div class="nextstepaction"]
> [Poziom wpływu na plan 5 — Omówienie](./index.md) 
>  [Poziom wpływu na plan 5 — Mapowanie formantów](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).