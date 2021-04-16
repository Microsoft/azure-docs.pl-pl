---
title: Przykład strategii DoD Impact Level 5
description: Kroki wdrażania przykładu strategii DoD Impact Level 5, w tym szczegóły parametru artefaktu strategii.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: b978b9fb30732c13785a6a425e5195daf67bae0f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377407"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Wdrażanie przykładowej strategii DoD Impact Level 5

Aby wdrożyć przykładową Azure Blueprints Department of Defense Impact Level 5 (DoD IL5), należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji usługi Azure Government, przed [](https://azure.microsoft.com/global-infrastructure/government/request/) rozpoczęciem poproś o subskrypcję wersji próbnej.

## <a name="create-blueprint-from-sample"></a>Tworzenie strategii na przykładzie

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład **strategii DoD Impact Level 5** w obszarze _Inne przykłady_ i wybierz **pozycję Użyj tego przykładu.**

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa strategii:** podaj nazwę kopii przykładowej strategii DoD Impact Level 5.
   - **Lokalizacja definicji:** użyj wielokropka i wybierz grupę zarządzania, w którym chcesz zapisać kopię przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładu strategii można dostosować do twojego środowiska i potrzeb, ale ta modyfikacja może odejść od dopasowania do kontrolek DoD Impact Level 5.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **uwagi dotyczące zmian,** takie jak "Pierwsza wersja opublikowana z przykładu strategii DoD IL5". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym opublikowaniu kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której została zapisana. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje:** wybierz co najmniej jedną subskrypcję, która należy do grupy zarządzania, w ramach których zapisano kopię przykładu strategii. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania:** nazwa jest wstępnie wypełniana na podstawie nazwy strategii.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja:** wybierz region, w którym ma zostać utworzona tożsamość zarządzana. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Wersja definicji strategii:** wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Blokowanie przypisania

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną _opcję tożsamości zarządzanej przypisanej_ przez system.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Te parametry [są parametrami](../../concepts/parameters.md#dynamic-parameters) dynamicznymi, ponieważ są definiowane podczas przypisywania strategii. Aby uzyskać pełną listę lub parametry artefaktu i ich opisy, zobacz [Tabelę parametrów artefaktu](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrożenie trwa około godziny. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|DoD Impact Level 5|Przypisanie zasad|Lista użytkowników, którzy muszą znajdować się w grupie Administratorzy maszyny wirtualnej z systemem Windows|Rozdzielana średnikami lista użytkowników, którzy powinni zostać uwzględnioni w lokalnej grupie Administratorzy; Na przykład: Administrator; myUser1; myUser2|
|DoD Impact Level 5|Przypisanie zasad|Lista użytkowników wykluczonych z grupy administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista użytkowników, którzy powinni zostać wykluczeni z grupy lokalnej Administratorzy. Na przykład: Administrator; myUser1; myUser2|
|DoD Impact Level 5|Przypisanie zasad|Lista typów zasobów, które powinny mieć włączone dzienniki diagnostyczne||
|DoD Impact Level 5|Przypisanie zasad|Identyfikator obszaru roboczego usługi Log Analytics dla raportowania agenta maszyny wirtualnej|Identyfikator (GUID) obszaru roboczego usługi Log Analytics, w którym agenci maszyn wirtualnych powinni się zgłaszać|
|DoD Impact Level 5|Przypisanie zasad|Lista regionów, w Network Watcher powinny być włączone|Aby wyświetlić pełną listę regionów, użyj get-AzLocation,|
|DoD Impact Level 5|Przypisanie zasad|Minimalna wersja TLS dla serwerów sieci Web z systemem Windows|Minimalna wersja protokołu TLS, która powinna być włączona na serwerach internetowych z systemem Windows|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka PHP|Najnowsza obsługiwana wersja języka PHP dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Java|Najnowsza obsługiwana wersja języka Java dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Windows|Najnowsza obsługiwana wersja języka Python dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Linux|Najnowsza obsługiwana wersja języka Python dla App Services|
|DoD Impact Level 5|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z systemem Windows, które obsługują agenta usługi Log Analytics w celu dodania do zakresu inspekcji|Rozdzielana średnikami lista obrazów; Na przykład: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z systemem Linux, które obsługują agenta usługi Log Analytics w celu dodania do zakresu inspekcji|Rozdzielana średnikami lista obrazów; Na przykład: /subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Do subskrypcji powinien być przypisany więcej niż jeden właściciel|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy włączyć powiadomienie e-mail do właściciela subskrypcji dotyczące alertów o wysokiej ważności|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Debugowanie zdalne powinno być wyłączone dla aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: Upewnij się, że .NET Framework "najnowsza", jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Transparent Data Encryption w bazach danych SQL należy włączyć|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: administrator Azure Active Directory powinien być aprowstrowany dla serwerów SQL|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy włączyć tylko bezpieczne Redis Cache połączenia z siecią|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Rozwiązanie ochrony punktu końcowego powinno być zainstalowane w zestawach skalowania maszyn wirtualnych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Inspekcja nieograniczonego dostępu sieciowego do kont magazynu|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail do odbierania alertów zabezpieczeń|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy włączyć bezpieczny transfer na konta magazynu|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Funkcje adaptacyjnego sterowania aplikacją powinny być włączone na maszynach wirtualnych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: upewnij się, że "Wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji internetowej|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: dla Twojej subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: dla Subskrypcji powinien zostać podany adres e-mail kontaktu zabezpieczeń|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Zasady CORS nie powinny zezwalać każdemu zasobowi na dostęp do aplikacji internetowych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Przestarzałe konta powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji internetowej|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji internetowej|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "Wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Luki w zabezpieczeniach powinny zostać skorygowane przez rozwiązanie do oceny luk w zabezpieczeniach|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że .NET Framework ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji internetowej|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Aktualizacje systemu powinny być zainstalowane na maszynach|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: upewnij się, że "Wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: w aplikacji interfejsu API powinna być używana najnowsza wersja TLS|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: uwierzytelniania wieloskładnikowego należy włączyć dla kont z uprawnieniami do zapisu w subskrypcji|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail do odbierania alertów zabezpieczeń|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: upewnij się, że "Wersja PROTOKOŁU HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Rozszerzenie Microsoft IaaSAntimalware powinno zostać wdrożone na serwerach z systemem Windows|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Dostęp za pośrednictwem punktu końcowego dostępnego z Internetu powinien być ograniczony|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Security Center należy wybrać warstwę cenową Standardowa|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Inspekcja użycia niestandardowych reguł RBAC|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Inspekcja na serwerze SQL powinna być włączona|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: DDoS Protection standardowa powinna być włączona|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: w wystąpieniach zarządzanych SQL należy włączyć zaawansowane zabezpieczenia danych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Monitoruj brakujący program Endpoint Protection w Azure Security Center|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Kontrola dostępu do sieci just in time powinna być stosowana na maszynach wirtualnych|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: dla subskrypcji powinien zostać podany numer telefonu osoby kontaktowej ds. zabezpieczeń|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: klastry Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klienta|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: w ustawieniach usługi SQL Managed Instance należy ustawić typy usługi Advanced Threat Protection na wartość "Wszystkie" Advanced Data Security SQL|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Magazyn geograficznie nadmiarowy powinien być włączony dla kont magazynu|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt zasad: Upewnij się, że .NET Framework ".NET Framework" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy zainstalować aktualizacje systemu w zestawach skalowania maszyn wirtualnych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych programu SQL Server|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Zdalne debugowanie powinno być wyłączone dla aplikacji internetowych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz Azure SQL danych|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Efekt dla zasad: upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: typy usługi Advanced Threat Protection powinny być ustawione na wartość "Wszystkie" w ustawieniach Advanced Data Security SQL Server|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń kontenera|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Zdalne debugowanie powinno być wyłączone dla API Apps|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: agent usługi Log Analytics powinien być zainstalowany na Virtual Machine Scale Sets|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: w aplikacji internetowej powinna być używana najnowsza wersja TLS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: w aplikacji funkcji powinna być używana najnowsza wersja TLS|Azure Policy efekt dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: [wersja zapoznawcza]: Usługi Kubernetes należy uaktualnić do wersji kubernetes, która nie jest narażona na zagrożenia|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|
|DoD Impact Level 5|Przypisanie zasad|Wpływ na zasady: Należy skorygować luki w zabezpieczeniach baz danych SQL|Azure Policy dla tych zasad; Aby uzyskać więcej informacji na temat efektów, odwiedź stronę https://aka.ms/policyeffects|

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zostały już przejrzene kroki wdrażania przykładu strategii DoD Impact Level 5, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o mapowaniu strategii i kontroli:

> [!div class="nextstepaction"]
> [Strategia DoD Impact Level 5 — omówienie](./index.md) 
>  [Strategia DoD Impact Level 5 — mapowanie kontroli](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).