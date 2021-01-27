---
title: Przykładowy plan HIPAA HITRUST 9,2 — Omówienie
description: Omówienie przykładu planu HIPAA HITRUST 9,2. Ten przykładowy plan ułatwia klientom ocenę określonych kontrolek HIPAA HITRUST 9,2.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: bd65b6113f291457096bacc02bdbcfd92d6e0f84
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915549"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>Przykład planu HIPAA HITRUST 9,2

Przykład planu HIPAA HITRUST 9,2 zawiera funkcję ładu Guard-szyny używające [Azure Policy](../../policy/overview.md) , które pomagają ocenić konkretne kontrolki HIPAA HiTRUST 9,2. Ten plan ułatwia klientom wdrożenie podstawowego zestawu zasad dla dowolnej architektury wdrożonej na platformie Azure, która musi implementować kontrolki HIPAA HITRUST 9,2.

## <a name="control-mapping"></a>Mapowanie kontrolek

[Mapowanie formantów Azure Policy](../../policy/samples/hipaa-hitrust-9-2.md) zawiera szczegółowe informacje dotyczące definicji zasad uwzględnionych w ramach tego planu oraz sposób mapowania tych definicji zasad do **domen zgodności** i **kontrolek** w HIPAA HiTRUST 9,2. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć przykład planów platformy Azure HIPAA HITRUST 9,2, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład planu **HIPAA HiTRUST** w _innych_ przykładach i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładu planu HIPAA HiTRUST 9,2.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może nie przełączać jej do HIPAA HITRUST 9,2.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana w przykładowej strategii HIPAA HiTRUST 9,2". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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

|Nazwa artefaktu |Nazwa parametru |Opis |
|---|---|---|
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony |Włączanie lub wyłączanie zbyt niesieciowej grupy zabezpieczeńego monitorowania reguł ruchu przychodzącego |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Konta: stan konta gościa |Określa, czy lokalne konto gościa jest wyłączone. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji |Włączać lub wyłączać monitorowanie aplikacji listy dozwolonych w Azure Security Center |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zezwalaj na jednoczesne połączenia z Internetem lub z domeną systemu Windows |Określ, czy komputery mogą łączyć się z siecią opartą na domenach i w tym samym czasie bez domeny. Wartość 0 zezwala na jednoczesne połączenia, a wartość 1 blokuje je. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS v2 |Włączanie lub wyłączanie monitorowania użycia protokołu HTTPS w aplikacji interfejsu API w wersji 2 |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Nazwy aplikacji (obsługuje symbole wieloznaczne) |Rozdzielana średnikami lista nazw aplikacji, które powinny być zainstalowane. np. "Microsoft SQL Server 2014 (64-bitowe); Microsoft Visual Studio kod "lub" Microsoft SQL Server 2014 * "(aby dopasować każdą aplikację rozpoczynającą się od" Microsoft SQL Server 2014 ") |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Inspekcja kończenia procesu |Określa, czy zdarzenia inspekcji są generowane po zakończeniu procesu. Zalecane do monitorowania zakończenia procesów krytycznych. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Inspekcja nieograniczonego dostępu sieciowego do kont magazynu |Włącz lub Wyłącz monitorowanie dostępu do sieci na koncie magazynu |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Inspekcja: zamknij system natychmiast, jeśli nie można rejestrować wyników inspekcji |Przeprowadza inspekcję, jeśli system zostanie zamknięty, gdy nie można zarejestrować zdarzeń zabezpieczeń. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Odciski palców certyfikatów |Rozdzielana średnikami lista odcisków palców certyfikatów, które powinny znajdować się w magazynie zaufanych certyfikatów głównych (CERT: \ LocalMachine\Root). np. THUMBPRINT1; THUMBPRINT2; THUMBPRINT3 |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Należy włączyć dzienniki diagnostyczne na kontach wsadowych |Włączać lub wyłączać monitorowanie dzienników diagnostycznych na kontach wsadowych |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone |Włączać lub wyłączać monitorowanie dzienników diagnostycznych na kontach centrów zdarzeń |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone |Włącz lub Wyłącz monitorowanie dzienników diagnostycznych w usłudze Azure Search |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone |Włącz lub Wyłącz monitorowanie dzienników diagnostycznych w Service Fabric |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych |Włączanie lub wyłączanie monitorowania na potrzeby szyfrowania dysków maszyn wirtualnych |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Włącz niezabezpieczone logowania gościa |Określa, czy klient SMB zezwoli na niezabezpieczone logowanie gościa na serwerze SMB. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Kontrola dostępu do sieci just-in-time powinna być stosowana na maszynach wirtualnych |Włącz lub Wyłącz monitorowanie dostępu do sieci just in Time |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Porty zarządzania powinny być zamknięte na maszynach wirtualnych |Włącz lub Wyłącz monitorowanie otwartych portów zarządzania na Virtual Machines |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji |Włączanie lub wyłączanie monitorowania usługi MFA dla kont z uprawnieniami do zapisu w ramach subskrypcji |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji |Włączanie lub wyłączanie monitorowania usługi MFA dla kont z uprawnieniami właściciela w ramach subskrypcji |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dostęp sieciowy: ścieżki rejestru, do których można uzyskiwać dostęp anonimowo |Określa, które ścieżki rejestru będą dostępne za pośrednictwem sieci, niezależnie od użytkowników lub grup wymienionych na liście kontroli dostępu (ACL) `winreg` klucza rejestru. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dostęp sieciowy: ścieżki rejestru i ścieżki podrzędne dostępne zdalnie |Określa ścieżki rejestru i ścieżki podrzędne, które będą dostępne za pośrednictwem sieci, niezależnie od użytkowników lub grup wymienionych na liście kontroli dostępu (ACL) `winreg` klucza rejestru. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Dostęp sieciowy: udziały, do których można uzyskiwać dostęp anonimowo |Określa, które udziały sieciowe mają być dostępne dla użytkowników anonimowych. Konfiguracja domyślna tego ustawienia zasad ma niewielki wpływ, ponieważ wszyscy użytkownicy muszą zostać uwierzytelnieni, zanim będą mogli uzyskać dostęp do zasobów udostępnionych na serwerze. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Konsola odzyskiwania: Zezwalaj na kopiowanie dyskietek i dostęp do wszystkich dysków i wszystkich folderów |Określa, czy polecenie SET konsoli odzyskiwania ma być dostępne, co umożliwia ustawianie zmiennych środowiskowych konsoli odzyskiwania. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API |Włącz lub Wyłącz monitorowanie zdalnego debugowania dla aplikacji interfejsu API |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web |Włącz lub Wyłącz monitorowanie zdalnego debugowania dla aplikacji internetowej |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Wymagane przechowywanie (w dniach) dla dzienników na kontach usługi Batch |Wymagany okres przechowywania dzienników diagnostycznych w dniach |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Wymagane przechowywanie (w dniach) dzienników w usłudze Azure Search |Wymagany okres przechowywania dzienników diagnostycznych w dniach |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Wymagane przechowywanie (w dniach) dzienników na kontach centrów zdarzeń |Wymagany okres przechowywania dzienników diagnostycznych w dniach |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Nazwa grupy zasobów dla konta magazynu (musi istnieć) do wdrożenia ustawień diagnostycznych dla sieciowych grup zabezpieczeń |Grupa zasobów, w której zostanie utworzone konto magazynu. Ta grupa zasobów musi już istnieć. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Role-Based Access Control (RBAC) należy używać w usługach Kubernetes Services |Włącz lub Wyłącz monitorowanie usług Kubernetes bez włączonej kontroli RBAC |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza |Włącz lub Wyłącz monitorowanie Transparent Data Encryption (TDE) z obsługą własnego klucza. TDE z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza |Włącz lub Wyłącz monitorowanie Transparent Data Encryption (TDE) z obsługą własnego klucza. TDE z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Prefiks konta magazynu dla konta magazynu regionalnego w celu wdrożenia ustawień diagnostycznych dla sieciowych grup zabezpieczeń |Ten prefiks zostanie połączony z lokalizacją sieciowej grupy zabezpieczeń w celu utworzenia nazwy utworzonego konta magazynu. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych |Włącz lub Wyłącz raportowanie zestawów skalowania maszyn wirtualnych w ramach aktualizacji systemu |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych |Włącz lub Wyłącz raportowanie zestawów skalowania maszyn wirtualnych w ramach aktualizacji systemu |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Wyłączanie rozpoznawania nazw multiemisji |Określa, czy jest włączona funkcja LLMNR, pomocniczy Protokół rozpoznawania nazw, który jest przesyłany przy użyciu multiemisji przez łącze podsieci lokalnej w pojedynczej podsieci. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager |Włączanie lub wyłączanie monitorowania klasycznych maszyn wirtualnych obliczeniowych |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować |Włącz lub Wyłącz monitorowanie luk w zabezpieczeniach systemu operacyjnego dla zestawów skalowania maszyn wirtualnych |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach |Włączać lub wyłączać Wykrywanie luk w zabezpieczeniach maszyn wirtualnych przez rozwiązanie do oceny usterek |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL |Przeprowadź inspekcję wystąpień zarządzanych SQL, które nie mają włączonej cyklicznej oceny ocen luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (domena): stosowanie lokalnych reguł zapory |Określa, czy Administratorzy lokalni mogą tworzyć lokalne reguły zapory, które są stosowane w połączeniu z regułami zapory skonfigurowanymi przez zasady grupy dla profilu domeny. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (domena): zachowanie dla połączeń wychodzących |Określa zachowanie połączeń wychodzących dla profilu domeny, który nie jest zgodny z regułą zapory wychodzącej. Wartość domyślna 0 oznacza zezwalanie na połączenia, a wartość 1 oznacza blokowanie połączeń. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (domena): zachowanie dla połączeń wychodzących |Określa zachowanie połączeń wychodzących dla profilu domeny, który nie jest zgodny z regułą zapory wychodzącej. Wartość domyślna 0 oznacza zezwalanie na połączenia, a wartość 1 oznacza blokowanie połączeń. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (domena): wyświetlanie powiadomień |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi wyświetla powiadomienia użytkownikowi, gdy program ma zablokowany dostęp do połączenia przychodzącego dla profilu domeny. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (domena): Użyj ustawień profilu |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi używa ustawień profilu domeny do filtrowania ruchu sieciowego. W przypadku wybrania opcji wyłączone Zapora systemu Windows z zabezpieczeniami zaawansowanymi nie będzie używać żadnych reguł zapory ani reguł zabezpieczeń połączeń dla tego profilu. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (prywatna): stosowanie reguł zabezpieczeń połączeń lokalnych |Określa, czy Administratorzy lokalni mogą tworzyć reguły zabezpieczeń połączeń, które są stosowane razem z regułami zabezpieczeń połączeń skonfigurowanymi przez zasady grupy dla profilu prywatnego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (prywatna): zastosuj reguły zapory lokalnej |Określa, czy Administratorzy lokalni mogą tworzyć lokalne reguły zapory, które są stosowane w połączeniu z regułami zapory skonfigurowanymi przez zasady grupy dla profilu prywatnego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (prywatna): zachowanie dla połączeń wychodzących |Określa zachowanie dla połączeń wychodzących dla profilu prywatnego, który nie jest zgodny z regułą zapory wychodzącej. Wartość domyślna 0 oznacza zezwalanie na połączenia, a wartość 1 oznacza blokowanie połączeń. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (prywatna): wyświetlanie powiadomień |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi wyświetla powiadomienia użytkownikowi, gdy program ma zablokowany dostęp do połączenia przychodzącego dla profilu prywatnego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (prywatna): Użyj ustawień profilu |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi używa ustawień profilu prywatnego do filtrowania ruchu sieciowego. W przypadku wybrania opcji wyłączone Zapora systemu Windows z zabezpieczeniami zaawansowanymi nie będzie używać żadnych reguł zapory ani reguł zabezpieczeń połączeń dla tego profilu. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (publiczna): zastosuj reguły zabezpieczeń połączeń lokalnych |Określa, czy Administratorzy lokalni mogą tworzyć reguły zabezpieczeń połączeń, które są stosowane wraz z regułami zabezpieczeń połączeń skonfigurowanymi przez zasady grupy dla profilu publicznego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (publiczna): zastosuj reguły zapory lokalnej |Określa, czy Administratorzy lokalni mogą tworzyć lokalne reguły zapory, które są stosowane w połączeniu z regułami zapory skonfigurowanymi przez zasady grupy dla profilu publicznego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (publiczna): zachowanie połączeń wychodzących |Określa zachowanie połączeń wychodzących dla profilu publicznego, który nie jest zgodny z regułą zapory wychodzącej. Wartość domyślna 0 oznacza zezwalanie na połączenia, a wartość 1 oznacza blokowanie połączeń. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (publiczna): wyświetlanie powiadomień |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi wyświetla powiadomienia użytkownikowi, gdy program ma zablokowany dostęp do połączenia przychodzącego dla profilu publicznego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows (publiczna): Użyj ustawień profilu |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi używa ustawień profilu publicznego do filtrowania ruchu sieciowego. W przypadku wybrania opcji wyłączone Zapora systemu Windows z zabezpieczeniami zaawansowanymi nie będzie używać żadnych reguł zapory ani reguł zabezpieczeń połączeń dla tego profilu. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows: domena: Zezwalaj na odpowiedź emisji pojedynczej |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi umożliwia komputerowi lokalnemu odbieranie odpowiedzi emisji pojedynczej do wychodzących komunikatów multiemisji lub emisji; dla profilu domeny. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows: Private: Zezwalaj na odpowiedź emisji pojedynczej |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi umożliwia komputerowi lokalnemu odbieranie odpowiedzi emisji pojedynczej do wychodzących komunikatów multiemisji lub emisji; dla profilu prywatnego. |
|Inspekcja kontrolek HITRUST/HIPAA i wdrażanie określonych rozszerzeń maszyn wirtualnych do obsługi wymagań inspekcji |Zapora systemu Windows: Public: Zezwalaj na odpowiedź emisji pojedynczej |Określa, czy Zapora systemu Windows z zabezpieczeniami zaawansowanymi umożliwia komputerowi lokalnemu odbieranie odpowiedzi emisji pojedynczej do wychodzących komunikatów multiemisji lub emisji; dla profilu publicznego. |

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
