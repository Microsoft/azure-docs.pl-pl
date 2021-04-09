---
title: Przykład strategii z ograniczeniami w Nowej Zelandii ISM
description: Przegląd przykładowego planu z ograniczeniami do usługi ISM. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803932"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Przykład strategii z ograniczeniami w Nowej Zelandii ISM

Plan z ograniczoną ochroną w ramach usługi ISM jest oparty na programie do zarządzania, który używa [Azure Policy](../../policy/overview.md) , które ułatwiają ocenę konkretnych [ręcznych kontrolek zabezpieczeń informacji w Nowej Zelandii](https://www.nzism.gcsb.govt.nz/) . Ten plan ułatwia klientom wdrożenie podstawowego zestawu zasad dla dowolnej architektury wdrożonej na platformie Azure, która musi implementować kontrolki dla Nowej Zelandii z ograniczeniami.

## <a name="control-mapping"></a>Mapowanie kontrolek

[Mapowanie formantów Azure Policy](../../policy/samples/new-zealand-ism.md) zawiera szczegółowe informacje o definicjach zasad uwzględnionych w tym planie oraz o tym, jak te definicje zasad są mapowane na **kontrolki** w podręczniku zabezpieczeń informacji Nowej Zelandii. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć plan platformy Azure w Nowej Zelandii z ograniczeniami, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład strategii z **ograniczoną ochroną** w usłudze ISM w ramach _innych próbek_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego planu z ograniczoną ochroną w ramach usługi ISM.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów uwzględnionych w przykładowej strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale zmiana ta może nie przełączać się z wyrównaniam do programu z Nową Zelandią ograniczonymi kontrolkami.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana w ramach strategii z ograniczeniami do usługi ISM w Nowej Zelandii". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

### <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

### <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Lista użytkowników, którzy muszą być uwzględnieni w grupie administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista użytkowników, którzy powinni być uwzględnieni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Lista użytkowników, którzy muszą być wykluczeni z grupy administratorów maszyn wirtualnych systemu Windows|Rozdzielana średnikami lista użytkowników, którzy powinni zostać wykluczeni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Lista użytkowników, których Grupa Administratorzy maszyny wirtualnej systemu Windows musi zawierać tylko|Rozdzielana średnikami lista wszystkich oczekiwanych członków lokalnej grupy Administratorzy; Przykład: administrator; Użytkownik1; Do|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Log Analytics identyfikator obszaru roboczego dla raportowania agenta maszyny wirtualnej|Identyfikator (GUID) obszaru roboczego Log Analytics, w którym agenci maszyn wirtualnych powinni zgłosić|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail na potrzeby otrzymywania raportów skanowania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt zastosowania zasad: zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: powinien być przypisany do subskrypcji więcej niż jeden właściciel|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla Application Gateway|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Wymaganie trybu WAF dla Application Gateway|W usłudze Application Gateway należy włączyć tryb zapobiegania lub wykrywania|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy włączyć Transparent Data Encryption w bazach danych SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalnie: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: Deploy-Konfiguruj agenta zależności, który ma być włączony na maszynach wirtualnych z systemem Windows|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Wpływ na zasady: Administrator Azure Active Directory powinien zostać zainicjowany dla serwerów SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy włączyć tylko bezpieczne połączenia z pamięcią podręczną platformy Azure dla Redis|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceny zasad: w przypadku maszyn z systemem Windows nie ma żadnych określonych członków w grupie Administratorzy|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalnie: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: [wersja zapoznawcza]: Agent Log Analytics powinien być włączony dla obrazów maszyn wirtualnych wymienionych na liście|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalna: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: [wersja zapoznawcza]: Agent Log Analytics powinien być włączony dla obrazów maszyn wirtualnych wymienionych na liście|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny ograniczać dostęp do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalnie: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: Deploy-Skonfiguruj agenta zależności, który ma być włączony w zestawach skalowania maszyn wirtualnych z systemem Windows|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji w zestawach skalowania maszyn wirtualnych należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceniania zasad: Inspekcja maszyn z systemem Windows, które mają dodatkowe konta w grupie Administratorzy|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy włączyć bezpieczny transfer do kont magazynu|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla usługi platformy Azure front-drzwi|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Wymagania w trybie WAF dla usługi Azure front-drzwi|Należy włączyć tryb zapobiegania lub wykrywania w usłudze Azure front-drzwi|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: adaptacyjne kontrolki aplikacji do definiowania bezpiecznych aplikacji powinny być włączone na maszynach|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: [wersja zapoznawcza]: dostęp publiczny do konta magazynu powinien być niedozwolony|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceny zasad: Przeprowadź inspekcję serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacji|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Minimalna wersja protokołu TLS dla serwerów sieci Web systemu Windows|Serwery sieci Web systemu Windows z niższymi wersjami protokołu TLS zostaną ocenione jako niezgodne|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalnie: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: Agent Log Analytics powinien być włączony w zestawach skalowania maszyn wirtualnych dla wymienionych obrazów maszyn wirtualnych|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Opcjonalnie: Lista niestandardowych obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu dodatkowych dla obrazów w galerii dla zasad: Agent Log Analytics powinien być włączony w zestawach skalowania maszyn wirtualnych dla wymienionych obrazów maszyn wirtualnych|Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceny zasad: Przeprowadź inspekcję maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy.|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: konta przestarzałe powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: subskrypcje platformy Azure powinny mieć profil dziennika aktywności|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych||
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: aktualizacje systemu powinny być zainstalowane na maszynach|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: usługi MFA powinny mieć włączone konta z uprawnieniami do zapisu w Twojej subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: powinien być włączony Standard Azure DDoS Protection|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami właściciela w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Monitoruj brakujące Endpoint Protection w Azure Security Center|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Dziennik aktywności powinien być zachowywany przez co najmniej jeden rok.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: klastry Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Windows, na których nie jest włączona funkcja Windows Defender Exploit Guard|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceniania zasad: Inspekcja maszyn z systemem Windows, na których nie jest włączona funkcja Windows Defender Exploit Guard|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Stan zgodności do raportowania dla maszyn z systemem Windows, na których funkcja Windows Defender Exploit Guard jest niedostępna|Funkcja Windows Defender Exploit Guard jest dostępna tylko w systemie Windows 10/Windows Server z aktualizacją 1709. Ustawienie tej wartości na "niezgodne" pokazuje komputery ze starszymi wersjami, na których funkcja Windows Defender Exploit Guard nie jest dostępna (na przykład Windows Server 2012 R2) jako niezgodne. Ustawienie tej wartości na "zgodny" powoduje wyświetlenie tych maszyn jako zgodnych.|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji na maszynach należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami do odczytu w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla API Apps|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceniania zasad: Inspekcja maszyn z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: konta przestarzałe z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: należy używać najnowszej wersji protokołu TLS w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące ustawień zabezpieczeń "Zasady konta"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Wymuszaj historię haseł dla lokalnych kont maszyn wirtualnych z systemem Windows|Określa limity użycia hasła — ile razy należy utworzyć nowe hasło dla konta użytkownika, aby można było powtórzyć hasło|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceniania zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące ustawień zabezpieczeń "Zasady konta"|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Maksymalny wiek hasła dla lokalnych kont maszyny wirtualnej systemu Windows|Określa maksymalną liczbę dni, które mogą upłynąć przed zmianą hasła konta użytkownika; format wartości jest dwóch liczb całkowitych rozdzielonych przecinkami, co oznacza zakresem włącznie|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Minimalny wiek hasła dla lokalnych kont maszyny wirtualnej systemu Windows|Określa minimalną liczbę dni, które muszą upłynąć, zanim będzie można zmienić hasło do konta użytkownika.|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Minimalna długość hasła dla lokalnych kont maszyny wirtualnej systemu Windows|Określa minimalną liczbę znaków, które może zawierać hasło konta użytkownika|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Hasło musi spełniać wymagania dotyczące złożoności lokalnych kont maszyn wirtualnych z systemem Windows|Określa, czy hasło konta użytkownika musi być złożone; w razie potrzeby złożone hasło nie może zawierać części nazwy konta użytkownika ani jego pełnej nazwy; może składać się z co najmniej 6 znaków; zawierają kombinację wielkich i małych liter, cyfr i znaków niealfabetycznych|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: maszyny wirtualne dostępne z Internetu powinny być chronione przy użyciu sieciowych grup zabezpieczeń|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Linux, które mają konta bez hasła|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceniania zasad: Inspekcja maszyn z systemem Linux z kontami bez hasła|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: w aplikacja funkcji powinna być używana najnowsza wersja protokołu TLS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt dla zasad: [wersja zapoznawcza]: cały ruch internetowy powinien być kierowany za pośrednictwem wdrożonej zapory platformy Azure|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|W Nowej Zelandii ISM z ograniczeniami|Przypisanie zasad|Efekt zasad: luki w zabezpieczeniach baz danych SQL należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).