---
title: Zarządzanie certyfikatami w klastrze Service Fabric
description: Informacje o zarządzaniu certyfikatami w klastrze Service Fabric zabezpieczonym za pomocą certyfikatów X. 509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: a8a7e8954f3c9d5b54c2e1ed9caa330ef92d4512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099510"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Zarządzanie certyfikatami w klastrach Service Fabric

Ten artykuł dotyczy aspektów zarządzania certyfikatami używanymi do zabezpieczania komunikacji w klastrach usługi Azure Service Fabric i uzupełnia wprowadzenie do [Service Fabric zabezpieczenia klastra](service-fabric-cluster-security.md) , a także wyjaśnienia dotyczące [uwierzytelniania opartego na certyfikatach X. 509 w Service Fabric](cluster-security-certificates.md). Przyjęto założenie, że czytelnik jest zaznajomiony z podstawowymi pojęciami dotyczącymi zabezpieczeń, a także z kontrolkami, które Service Fabric uwidaczniają konfigurację zabezpieczeń klastra.  

Aspekty objęte tym tytułem:

* Co to jest "Zarządzanie certyfikatami"?
* Role i jednostki objęte zarządzaniem certyfikatami
* Przejazd certyfikatu
* Głębokie szczegółowe na przykład
* Rozwiązywanie problemów i często zadawane pytania

Jednakże w pierwszej kolejności: Ten artykuł podejmuje próbę sparowania teoretycznej strony z praktycznymi przykładami, które wymagają, dobrze, konkretne usługi, technologie i tak dalej. Ponieważ pokaźną częścią odbiorców jest wewnętrzna firma Microsoft, odnosimy się do usług, technologii i produktów specyficznych dla Microsoft Azure. Możesz zadawać w sekcji komentarzy informacje na temat wyjaśnień lub wskazówek, w przypadku których szczegóły dotyczące firmy Microsoft nie mają zastosowania w Twoim przypadku.

## <a name="defining-certificate-management"></a>Definiowanie zarządzania certyfikatami
Jak widać w [artykule pomocnika](cluster-security-certificates.md), certyfikat jest obiektem kryptograficznym zasadniczo wiążącym parę kluczy asymetrycznych z atrybutami opisującymi jednostkę, którą reprezentuje. Jednak jest to również obiekt "nietrwały", w tym, że jego okres istnienia jest skończona i jest podatny na naruszenie przypadkowego ujawnienia lub pomyślne wykorzystanie certyfikatu z punktu widzenia zabezpieczeń. Oznacza to konieczność zmiany certyfikatów — rutynowo lub w odpowiedzi na zdarzenie związane z bezpieczeństwem. Innym aspektem zarządzania (i jest całego tematu) jest ochrona kluczy prywatnych certyfikatu lub wpisów tajnych chroniących tożsamości jednostek, które są związane z zaświadczeniem i aprowizacji. Przyjrzyjmy się procesom i procedurom używanym do uzyskiwania certyfikatów i bezpiecznego (i bezpiecznego) transportu do miejsca, w którym są potrzebne jako "Zarządzanie certyfikatami". Niektóre operacje zarządzania — takie jak rejestracja, ustawienie zasad i kontrolki autoryzacji — wykraczają poza zakres tego artykułu. Nadal inne osoby — takie jak inicjowanie obsługi, odnawianie, ponowne tworzenie klucza lub odwoływanie — są tylko incydentowo powiązane z Service Fabric; należy jednak zająć się tymi informacjami w pewnym stopniu, ponieważ zrozumienie tych operacji może pomóc w poprawnym zabezpieczeniu klastra. 

Celem jest zautomatyzowanie zarządzania certyfikatami tak, aby zapewnić nieprzerwaną dostępność klastra i zaoferować gwarancje bezpieczeństwa, pod warunkiem, że proces ten jest niezależny od użytkownika. Ten cel jest obecnie osiągalny w klastrach usługi Azure Service Fabric. w pozostałej części artykułu zostanie najpierw rozbudowy zarządzania certyfikatami, a później zostaną one skoncentrowane na włączeniu funkcji autorzucenia.

W odróżnieniu od tematów objętych zakresem są:
  - założenia związane z rozdzieleniem przypisań między właścicielem i platformą w kontekście zarządzania certyfikatami
  - długi potok certyfikatów od wystawienia do użycia
  - Rotacja certyfikatów — dlaczego, jak i kiedy
  - co może być niewłaściwe?

Taki aspekt, jak Zabezpieczanie/zarządzanie nazwami domen, rejestrowanie się w certyfikatach lub Konfigurowanie kontroli autoryzacji w celu wymuszania wystawiania certyfikatów poza zakresem tego artykułu. Zapoznaj się z urzędem rejestrowania (RA) dla ulubionej usługi infrastruktury kluczy publicznych (PKI). Użytkownicy wewnętrzni firmy Microsoft: skontaktuj się z bezpieczeństwem platformy Azure.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Role i jednostki objęte zarządzaniem certyfikatami
Podejście zabezpieczeń w klastrze Service Fabric jest przypadkiem "właściciel klastra deklaruje go, Service Fabric środowisko uruchomieniowe wymusza je". Oznacza to, że niemal żadne z certyfikatów, kluczy lub innych poświadczeń tożsamości uczestniczących w działaniu klastra nie pochodzą z samej usługi; są one deklarowane przez właściciela klastra. Ponadto właściciel klastra jest odpowiedzialny za zainicjowanie certyfikatów w klastrze, odnowienie ich w razie potrzeby i zapewnienie bezpieczeństwa certyfikatów przez cały czas. Dokładniej mówiąc, właściciel klastra musi upewnić się, że:
  - Certyfikaty zadeklarowane w sekcji NodeType manifestu klastra można znaleźć w każdym węźle tego typu, zgodnie z [regułami prezentacji](cluster-security-certificates.md#presentation-rules)
  - Certyfikaty zadeklarowane powyżej są instalowane z odpowiednimi kluczami prywatnymi
  - Certyfikaty zadeklarowane w regułach prezentacji powinny przekazać [reguły walidacji](cluster-security-certificates.md#validation-rules) 

Service Fabric, w swojej części, przyjmuje obowiązki:
  - Lokalizowanie/Znajdowanie certyfikatów zgodnych z deklaracjami w definicji klastra  
  - Udzielanie dostępu do odpowiednich kluczy prywatnych do jednostek kontrolowanych Service Fabric na podstawie "potrzeby"
  - weryfikowanie certyfikatów w ścisły sposób zgodnie z ustalonymi najlepszymi rozwiązaniami dotyczącymi zabezpieczeń i definicją klastra
  - Zgłaszanie alertów w przypadku nieoczekiwanego wygaśnięcia certyfikatów lub niepowodzenia wykonywania podstawowych kroków weryfikacji certyfikatu
  - Weryfikowanie (w pewnym stopniu) aspektów związanych z certyfikatem definicji klastra przez podstawową konfigurację hostów 

Wynika to z tego, że obciążenie zarządzania certyfikatami (jako aktywne operacje) odbywa się wyłącznie na właścicielu klastra. W poniższych sekcjach zajmiemy się bliżej każdej operacji zarządzania z dostępnymi mechanizmami i ich wpływem na klaster.

## <a name="the-journey-of-a-certificate"></a>Przejazd certyfikatu
Pozwól nam szybko ponownie odwiedzić postęp certyfikatu z wystawienia do użycia w kontekście klastra Service Fabric:

  1. Właściciel domeny rejestruje się w urzędzie rejestrowania infrastruktury PKI domeny lub podmiotu, które chce skojarzyć z certyfikatami; z kolei certyfikaty będą stanowić dowód prawa własności wymienionej domeny lub tematu
  2. Właściciel domeny wyznacza również w urzędzie rejestrowania tożsamości autoryzowanych podmiotów żądających — jednostki uprawnione do żądania rejestracji certyfikatów z określoną domeną lub podmiotem; w Microsoft Azure domyślnym dostawcą tożsamości jest Azure Active Directory, a autoryzowani osoby żądające są Wyznaczeni przez odpowiadającą im tożsamość usługi AAD (lub grupy zabezpieczeń).
  3. Autoryzowany Obiekt żądający następnie rejestruje certyfikat za pośrednictwem tajnej usługi zarządzania; w Microsoft Azure jest to Azure Key Vault (AKV), które bezpiecznie przechowują i umożliwiają pobieranie kluczy tajnych/certyfikatów przez autoryzowanych jednostek. AKV również ponownie klucze certyfikatu zgodnie z konfiguracją w skojarzonych zasadach certyfikatu. (AKV używa usługi AAD jako dostawcy tożsamości).
  4. Autoryzowany wejście metody Retriever, do którego odwołuje się jako "Agent aprowizacji" — pobiera certyfikat, włącznie z jego kluczem prywatnym, z magazynu i instaluje go na komputerach obsługujących klaster
  5. Usługa Service Fabric (z podwyższonym poziomem uprawnień w każdym węźle) przyznaje dostęp do certyfikatu w celu zezwolenia na Service Fabric jednostek; są one Wyznaczeni przez grupy lokalne i podzielone między ServiceFabricAdministrators i ServiceFabricAllowedUsers
  6. Service Fabric środowisko uruchomieniowe uzyskuje dostęp do certyfikatu w celu ustanowienia Federacji lub do uwierzytelniania żądań przychodzących od autoryzowanych klientów
  7. Agent aprowizacji monitoruje certyfikat magazynu i wyzwala przepływ aprowizacji po wykryciu odnowienia. następnie właściciel klastra aktualizuje definicję klastra, w razie potrzeby, aby wskazać zamiar przejęcia certyfikatu.
  8. Agent aprowizacji lub właściciel klastra również jest odpowiedzialny za czyszczenie/usuwanie nieużywanych certyfikatów
  
W naszym przypadku pierwsze dwa kroki w sekwencji powyżej są w znacznym stopniu niepowiązane; jedyne połączenie polega na tym, że wspólna nazwa podmiotu certyfikatu jest nazwą DNS zadeklarowaną w definicji klastra.

Te kroki przedstawiono poniżej: Zwróć uwagę na różnice w aprowizacji między certyfikatami zadeklarowanymi przez odcisk palca i nazwę pospolitą odpowiednio.

*Rys. 1.* Przepływ wystawiania i aprowizacji certyfikatów zadeklarowanych przez odcisk palca.
![Certyfikaty aprowizacji zadeklarowane przez odcisk palca][Image1]

*Rysunek 2.* Wystawianie i proces aprowizacji certyfikatów zadeklarowanych przez wspólną nazwę podmiotu.
![Certyfikaty aprowizacji zadeklarowane przez wspólną nazwę podmiotu][Image2]

### <a name="certificate-enrollment"></a>Rejestrowanie certyfikatów
Ten temat został szczegółowo opisany w [dokumentacji](../key-vault/certificates/create-certificate.md)Key Vaultej. w tym miejscu zawieramy streszczenie w celu zapewnienia ciągłości i łatwiejszego odwoływania się do nich. Kontynuując korzystanie z platformy Azure jako kontekstu, a Azure Key Vault jako usługi zarządzania tajnego, autoryzowany Obiekt żądający certyfikatu musi mieć co najmniej uprawnienia do zarządzania certyfikatami w magazynie, udzielane przez właściciela magazynu; zleceniodawca zarejestruje się w następujący sposób:
    - tworzy zasady certyfikatów w Azure Key Vault (AKV), które określają domenę/podmiot certyfikatu, żądany wystawcę, typ klucza i długość, zamierzone użycie klucza i wiele więcej; Aby uzyskać szczegółowe informacje, zobacz [Certyfikaty w Azure Key Vault](../key-vault/certificates/certificate-scenarios.md) . 
    - tworzy certyfikat w tym samym magazynie z zasadami określonymi powyżej; to z kolei generuje parę kluczy jako obiekty magazynu, żądanie podpisania certyfikatu podpisane przy użyciu klucza prywatnego, a następnie przekazywane do wystawiającego wystawcy na potrzeby podpisywania
    - gdy wystawca (urząd certyfikacji) odpowie z podpisanym certyfikatem, wynik zostanie scalony z magazynem, a certyfikat jest dostępny dla następujących operacji:
      - w obszarze {vaultUri}/Certificates/{Name}: certyfikat zawierający klucz publiczny i metadane
      - w obszarze {vaultUri}/Keys/{Name}: klucz prywatny certyfikatu, dostępny dla operacji kryptograficznych (Zawijanie/depakowanie, podpisywanie/Weryfikowanie)
      - w obszarze {vaultUri}/Secrets/{Name}: certyfikat z kluczem prywatnym, dostępny do pobrania jako niechroniony plik PFX lub PEM  
    Odwołaj się do certyfikatu magazynu, w rzeczywistości chronologicznej linii wystąpień certyfikatów, udostępniając zasady. Wersje certyfikatów zostaną utworzone na podstawie atrybutów okresu istnienia i odnawiania zasad. Zdecydowanie zaleca się, aby certyfikaty magazynu nie współużytkują podmiotów lub domen/nazw DNS; może to powodować zakłócenia w klastrze w celu aprowizacji wystąpień certyfikatów z różnych certyfikatów magazynu, z identycznymi podmiotami, ale znacząco różnymi innymi atrybutami, takimi jak wystawca, użycie klucza itd.

W tym momencie certyfikat istnieje w magazynie, gotowy do użycia. Od do do:

### <a name="certificate-provisioning"></a>Inicjowanie obsługi certyfikatów
Wspomniano "Agent aprowizacji", który jest jednostką, która pobiera certyfikat, włącznie z jego kluczem prywatnym, z magazynu i instaluje go na wszystkich hostach klastra. (Odwołaj to Service Fabric nie udostępnia certyfikatów). W naszym kontekście klaster będzie hostowany w kolekcji maszyn wirtualnych platformy Azure i/lub zestawów skalowania maszyn wirtualnych. Na platformie Azure Inicjowanie obsługi certyfikatu z magazynu na maszynę wirtualną/VMSS można osiągnąć przy użyciu następujących mechanizmów — przy założeniu, że Agent aprowizacji ma wcześniej przyznane uprawnienia get do magazynu przez właściciela magazynu: 
  - ad hoc: operator pobiera certyfikat z magazynu (jako PFX/PKCS #12 lub PEM) i instaluje go w każdym węźle
  - jako wpis "tajny" zestawu skalowania maszyn wirtualnych podczas wdrażania: usługa obliczeniowa pobiera, korzystając z tożsamości pierwszej firmy w imieniu operatora, certyfikatu z magazynu z obsługą wdrożenia szablonu i instaluje go w każdym węźle zestawu skalowania maszyn wirtualnych ([np](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).); należy pamiętać, że umożliwia to tylko aprowizacji wpisów tajnych wersji
  - przy użyciu [rozszerzenia maszyny wirtualnej Key Vault](../virtual-machines/extensions/key-vault-windows.md) pozwala to na inicjowanie obsługi certyfikatów przy użyciu deklaracji bez wersji i okresowe odświeżanie obserwowanych certyfikatów. W takim przypadku oczekuje się, że maszyna wirtualna/VMSS ma [zarządzaną tożsamość](../virtual-machines/security-policy.md#managed-identities-for-azure-resources), tożsamość, której udzielono dostępu do magazynów zawierających obserwowane certyfikaty.

Mechanizm ad hoc nie jest zalecany z wielu powodów, od zabezpieczeń do dostępności i nie zostanie jeszcze omówiona w tym miejscu. Aby uzyskać szczegółowe informacje, zobacz [Certyfikaty w zestawach skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

Inicjowanie obsługi VMSS-/COMPUTE-based oferuje zalety zabezpieczeń i dostępności, ale również oferuje ograniczenia. Jest to wymagane przez zaprojektowanie certyfikatów jako wpisów tajnych, co sprawia, że jest to odpowiednie tylko w przypadku klastrów zabezpieczonych za pomocą certyfikatów zadeklarowanych przez odcisk palca. W przeciwieństwie do zainicjowania obsługi opartej na rozszerzeniu maszyny wirtualnej Key Vault będzie zawsze instalowana Najnowsza wersja każdego zaobserwowanego certyfikatu, dzięki czemu jest to odpowiednie tylko w przypadku klastrów zabezpieczonych za pomocą certyfikatów zadeklarowanych przez wspólną nazwę podmiotu. Aby wyróżnić, nie należy używać mechanizmu aprowizacji AutoRefresh (takiego jak rozszerzenie KVVM) dla certyfikatów zadeklarowanych przez wystąpienie (czyli według odcisku palca) — ryzyko utraty dostępności jest znaczne.

Mogą istnieć inne mechanizmy aprowizacji; Powyższe dane są obecnie akceptowane dla klastrów usługi Azure Service Fabric.

### <a name="certificate-consumption-and-monitoring"></a>Użycie i monitorowanie certyfikatów
Jak wspomniano wcześniej, środowisko uruchomieniowe Service Fabric jest odpowiedzialne za lokalizowanie i używanie certyfikatów zadeklarowanych w definicji klastra. W artykule na temat [uwierzytelniania opartego na certyfikatach](cluster-security-certificates.md) wyjaśniono, w jaki sposób Service Fabric implementują odpowiednio reguły prezentacji i walidacji, a nie będzie oglądany w tym miejscu. Chcemy przystąpić do uzyskiwania dostępu i przyznawania uprawnień, a także do monitorowania.

Odwołaj te certyfikaty w Service Fabric są używane w wielu celach, od wzajemnego uwierzytelniania w warstwie Federacji do uwierzytelniania TLS dla punktów końcowych zarządzania; wymaga to, aby różne składniki lub usługi systemowe miały dostęp do klucza prywatnego certyfikatu. Środowisko uruchomieniowe Service Fabric regularnie skanuje magazyn certyfikatów, szukając dopasowań dla każdej znanej reguły prezentacji; dla każdego zgodnego certyfikatu znajduje się odpowiedni klucz prywatny, a jego poufna lista kontroli dostępu jest aktualizowana w celu uwzględnienia uprawnień — zwykle odczytanych i wykonywanych na odpowiedniej tożsamości, która go wymaga. (Ten proces jest nieformalnie określany jako "ACLing"). Ten proces jest uruchamiany w 1-minutowy erze, a także obejmuje certyfikaty aplikacji, takie jak te używane do szyfrowania ustawień lub jako certyfikaty punktów końcowych. ACLing są zgodne z regułami prezentacji, dlatego ważne jest, aby pamiętać, że certyfikaty zadeklarowane przez odcisk palca i które są autoodświeżane bez aktualizacji Konfiguracja klastra nie będą dostępne.    

### <a name="certificate-rotation"></a>Obrót certyfikatu
W ramach tej uwagi: Grupa IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) formularz definiuje [odnowienie](https://tools.ietf.org/html/rfc3647#section-4.4.6) jako wystawianie certyfikatu z tymi samymi atrybutami, co zastępowany certyfikat — wystawca, klucz publiczny podmiotu i informacje są zachowywane, a [następnie ponowne generowanie klucza](https://tools.ietf.org/html/rfc3647#section-4.4.7) jako wystawianie certyfikatu z nową parą kluczy, bez ograniczeń dotyczących tego, czy wystawca może się zmienić. Mając na uwadze, że rozróżnienie może być ważne (Rozważ uwzględnienie przypadków certyfikatów zadeklarowanych przez wspólną nazwę podmiotu z przypinaniem wystawcy), będziemy wybierali neutralny termin "rotacja" w celu uwzględnienia obu scenariuszy. (Należy pamiętać, że w przypadku nieformalnego użycia "odnowienie" odwołuje się do ponownego tworzenia kluczy). 

Wcześniej zaobserwowano, że Azure Key Vault obsługuje automatyczne obracanie certyfikatów: zasady kojarzenia certyfikatu definiują punkt w czasie, czyli dni przed wygaśnięciem lub procent łącznego okresu istnienia, gdy certyfikat zostanie obrócony w magazynie. Agent aprowizacji musi zostać wywołany po tym punkcie w czasie i przed wygaśnięciem poprzedniego certyfikatu w celu rozesłania tego nowego certyfikatu do wszystkich węzłów klastra. Service Fabric będzie pomocne przez podnoszenie poziomu ostrzeżeń dotyczących kondycji, gdy data wygaśnięcia certyfikatu (i który jest aktualnie używany w klastrze) występuje szybciej niż wstępnie określony interwał. Automatyczny Agent aprowizacji (tj. rozszerzenie maszyny wirtualnej magazynu kluczy) skonfigurowany do obserwowania certyfikatu magazynu, będzie okresowo sondował magazyn, wykrywał rotację i pobiera i instaluje nowy certyfikat. Inicjowanie obsługi administracyjnej wykonywane za pośrednictwem funkcji "Secrets" maszyny wirtualnej/VMSS wymaga autoryzowanego operatora do zaktualizowania maszyny wirtualnej/VMSS przy użyciu identyfikatora URI magazynu kluczy z wersjami odpowiadającego nowemu certyfikatowi.

W obu przypadkach ten obrócony certyfikat jest teraz udostępniany wszystkim węzłom i został opisany mechanizm Service Fabric służy do wykrywania rotacji; Poinformuj nas o tym, co się stanie dalej — przy założeniu rotacji zastosowanej do certyfikatu klastra zadeklarowanego według nazwy pospolitej podmiotu
  - w przypadku nowych połączeń w programie, a także do klastra, środowisko uruchomieniowe Service Fabric będzie znajdować i wybierać ostatnio wystawiony pasujący certyfikat (największą wartość właściwości "NotBefore"). Zwróć uwagę na to, że jest to zmiana z poprzednich wersji środowiska uruchomieniowego Service Fabric.
  - istniejące połączenia będą utrzymywane lub mogą działać w sposób naturalny lub kończące się w inny sposób; Procedura obsługi wewnętrznej zostanie powiadomiona o tym, że istnieje nowe dopasowanie

> [!NOTE] 
> Przed wersjami 7.2.445 (7,2 CU4) Service Fabric wybrany certyfikat z poprzednią datą wygaśnięcia (certyfikat z najdalej właściwością "NotAfter")

To tłumaczy następujące ważne uwagi:
  - Certyfikat odnowienia może zostać zignorowany, jeśli jego Data wygaśnięcia jest wcześniejsza niż certyfikat aktualnie używany.
  - Dostępność klastra lub aplikacji hostowanych ma pierwszeństwo przed dyrektywą, aby obrócić certyfikat; klaster zostanie w końcu zbieżny z nowym certyfikatem, ale bez gwarancji czasowych. Wynika to z tego, że:
  - Może nie być natychmiast oczywisty w przypadku obserwatora, że obrócony certyfikat całkowicie zamieni poprzednią; jedynym sposobem zapewnienia (dla certyfikatów klastra) ponownego uruchomienia maszyn hosta. Należy zauważyć, że nie jest wystarczające, aby ponownie uruchomić węzły Service Fabric, jako składniki trybu jądra, w których nie wpłynie to na połączenia dzierżawy w klastrze. Należy również zauważyć, że ponowne uruchomienie maszyny wirtualnej/VMSS może spowodować tymczasową utratę dostępności. (W przypadku certyfikatów aplikacji wystarczy uruchomić ponownie tylko odpowiednie wystąpienia aplikacji).
  - Wprowadzenie certyfikatu z ponownym zatwierdzeniem, który nie spełnia reguł walidacji, może efektywnie przerwać klaster. Najbardziej typowym przykładem jest sytuacja, w której występuje nieoczekiwany wystawca: certyfikaty klastra są deklarowane przez wspólną nazwę podmiotu z przypinaniem wystawcy, ale obrócony certyfikat został wystawiony przez nowy/niezadeklarowany wystawca.     

### <a name="certificate-cleanup"></a>Czyszczenie certyfikatu
W tej chwili nie ma żadnych przepisów na platformie Azure w celu jawnego usunięcia certyfikatów. Często nie jest to proste zadanie, aby określić, czy dany certyfikat jest używany w danym momencie. Jest to trudniejsze dla certyfikatów aplikacji niż w przypadku certyfikatów klastra. Service Fabric sam, a nie jako agent aprowizacji, nie usunie certyfikatu zadeklarowanego przez użytkownika w żadnej sytuacji. W przypadku standardowych mechanizmów aprowizacji:
  - Certyfikaty zadeklarowane jako wpisy tajne maszyny wirtualnej/VMSS będą obsługiwane, o ile odwołują się do nich w definicji maszyny wirtualnej/VMSS i są możliwe do pobierania z magazynu (usunięcie wpisu tajnego magazynu/certyfikatu zakończy się niepowodzeniem w kolejnych wdrożeniach maszyn wirtualnych/VMSS; podobnie, wyłączenie wpisu tajnego w magazynie spowoduje również niepowodzenie wdrożeń maszyn wirtualnych/VMSS, które odwołują się do tej
  - Poprzednie wersje certyfikatów obsługiwane za pośrednictwem rozszerzenia maszyny wirtualnej magazynu kluczy mogą lub nie mogą być obecne w węźle VM/VMSS. Agent pobiera i instaluje tylko bieżącą wersję i nie usuwa żadnych certyfikatów. Odtworzenie obrazu węzła (co zwykle odbywa się co miesiąc) spowoduje zresetowanie magazynu certyfikatów do zawartości obrazu systemu operacyjnego, tak aby poprzednie wersje zostały niejawnie usunięte. Mimo że skalowanie w górę zestawu skalowania maszyn wirtualnych spowoduje, że zostanie zainstalowana tylko bieżąca wersja zaobserwowanych certyfikatów; nie zakładaj jednorodności węzłów w odniesieniu do zainstalowanych certyfikatów.   

## <a name="simplifying-management---an-autorollover-example"></a>Uproszczenie zarządzania — przykład przerzucania
Opisano mechanizmy, ograniczenia, Intricate reguły i definicje, a następnie wydano fatalne przewidywania awarii. Jest to możliwe, jednak Pokazywanie, jak skonfigurować automatyczne zarządzanie certyfikatami, aby uniknąć wszystkich problemów. Wykonujemy to w kontekście klastra Service Fabric platformy Azure działającego na PaaSv2 zestawie skalowania maszyn wirtualnych przy użyciu Azure Key Vault do zarządzania wpisami tajnymi i używania tożsamości zarządzanych w następujący sposób:
  - Walidacja certyfikatów została zmieniona z przypinania odcisków palca do obiektu subject + wystawcy
    - Certyfikaty są rejestrowane i uzyskiwane z zaufanego magazynu (Key Vault) i odświeżane przez agenta — w tym przypadku rozszerzenie maszyny wirtualnej magazynu kluczy
    - Inicjowanie obsługi certyfikatów jest zmieniane z poziomu czasu wdrożenia i opartej na wersji (zgodnie z ComputeRP) do wdrożenia po wdrożeniu i korzystania z identyfikatorów URI magazynu kluczy bez wersji
    - Dostęp do magazynu kluczy jest przyznawany przez zarządzane tożsamości przypisane przez użytkownika; tożsamość UA jest tworzona i przypisana do zestawu skalowania maszyn wirtualnych podczas wdrażania
    - Po wdrożeniu Agent (rozszerzenie maszyny wirtualnej KV) będzie sondował i odświeża obserwowane certyfikaty w każdym węźle zestawu skalowania maszyn wirtualnych. Rotacja certyfikatów jest w pełni zautomatyzowana, ponieważ SF będzie automatycznie wybierać najbardziej ważny certyfikat

Sekwencja jest w pełni skryptowa/zautomatyzowana i umożliwia wstępne wdrażanie klastra, które zostało skonfigurowane na potrzeby automatycznego przerzucania certyfikatów. Szczegółowe kroki są podane poniżej. Będziemy używać kombinacji poleceń cmdlet programu PowerShell i fragmentów szablonów JSON. Te same funkcje są osiągalne ze wszystkimi obsługiwanymi sposobami współpracy z platformą Azure.

> [!NOTE]
> W tym przykładzie przyjęto założenie, że certyfikat już istnieje w magazynie; Rejestrowanie i odnawianie certyfikatu zarządzanego przez Magazyn kluczy wymaga wstępnych czynności ręcznych, zgodnie z opisem we wcześniejszej części tego artykułu. W przypadku środowisk produkcyjnych należy użyć certyfikatów zarządzanych przez Magazyn kluczy — poniżej znajduje się przykładowy skrypt specyficzny dla wewnętrznej infrastruktury PKI firmy Microsoft.

> [!NOTE]
> Autorollover certyfikatu ma sens tylko w przypadku certyfikatów wystawionych przez urzędy certyfikacji; przy użyciu certyfikatów z podpisem własnym, w tym tych, które zostały wygenerowane podczas wdrażania klastra Service Fabric w Azure Portal, jest bezsensownie, ale nadal jest możliwe w przypadku wdrożeń lokalnych/opartych na deweloperach przez zadeklarowanie odcisku palca wystawcy jako takiego samego jak certyfikat liścia.

### <a name="starting-point"></a>Punkt początkowy
W przypadku usługi zwięzłości przyjmujemy następujący stan początkowy:
  - Klaster Service Fabric istnieje i jest zabezpieczony przy użyciu certyfikatu wystawionego przez urząd certyfikacji zadeklarowanego przez odcisk palca.
  - Certyfikat jest przechowywany w magazynie i zainicjowany jako wpis tajny zestawu skalowania maszyn wirtualnych
  - Ten sam certyfikat zostanie użyty do przekonwertowania klastra na typowe deklaracje certyfikatów oparte na nazwach i dlatego może być zweryfikowany przez podmiot i wystawcę; Jeśli tak się nie dzieje, uzyskaj certyfikat wystawiony przez urząd certyfikacji przeznaczony do tego celu i dodaj go do definicji klastra przez odcisk palca, jak wyjaśniono [tutaj](service-fabric-cluster-security-update-certs-azure.md)

Oto fragment kodu JSON z szablonu odpowiadającego takiemu stanowi — należy pamiętać, że pominięcie wielu wymaganych ustawień i ilustruje jedynie aspekty dotyczące certyfikatu:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Powyżej stwierdza się, że certyfikat z odciskiem palca ```json [parameters('primaryClusterCertificateTP')] ``` i znaleziony w identyfikatorze URI magazynu kluczy ```json [parameters('clusterCertificateUrlValue')] ``` jest zadeklarowany jako jedyny certyfikat klastra przez odcisk palca. Następnie skonfigurujemy dodatkowe zasoby potrzebne do zapewnienia autorollover certyfikatu.

### <a name="setting-up-prerequisite-resources"></a>Konfigurowanie zasobów wymaganych wstępnie
Jak wspomniano wcześniej, certyfikat zainicjowany jako wpis tajny zestawu skalowania maszyn wirtualnych jest pobierany z magazynu przez usługę dostawcy zasobów Microsoft. COMPUTE przy użyciu tożsamości pierwszej firmy i w imieniu operatora wdrażania. W przypadku automatycznego przerzucania zmiany zostaną przełączone do korzystania z tożsamości zarządzanej przypisanej do zestawu skalowania maszyn wirtualnych, a przyznano uprawnienia do wpisów tajnych magazynu.

Wszystkie kolejne fragmenty powinny zostać wdrożone w sposób równoległy — są one osobno wyświetlane na potrzeby analizy odtwarzania i wyjaśnień.

Najpierw Zdefiniuj tożsamość przypisaną przez użytkownika (wartości domyślne są uwzględniane jako przykłady) — zapoznaj się z [oficjalną dokumentacją](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) dotyczącą aktualnych informacji:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Następnie przyznaj tej tożsamości dostęp do wpisów tajnych magazynu — zapoznaj się z [oficjalną dokumentacją](/rest/api/keyvault/vaults/updateaccesspolicy) dotyczącą bieżących informacji:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

W następnym kroku będziemy:
  - Przypisywanie tożsamości przypisanej do użytkownika do zestawu skalowania maszyn wirtualnych
  - Zadeklaruj zależność zestawu skalowania maszyn wirtualnych przy tworzeniu tożsamości zarządzanej i w wyniku udzielenia mu dostępu do magazynu
  - Zadeklaruj rozszerzenie maszyny wirtualnej magazynu kluczy, co wymaga pobrania obserwowanych certyfikatów przy uruchamianiu ([Oficjalna dokumentacja](../virtual-machines/extensions/key-vault-windows.md))
  - Zaktualizuj definicję rozszerzenia maszyny wirtualnej Service Fabric, aby zależała od rozszerzenia KVVM, i aby przekonwertować certyfikat klastra na nazwę pospolitą (wprowadzamy te zmiany w jednym kroku, ponieważ należą one do zakresu tego samego zasobu).

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Należy pamiętać, że chociaż nie został jawnie wymieniony powyżej, że adres URL certyfikatu magazynu został usunięty z sekcji "OsProfile" zestawu skalowania maszyn wirtualnych.
Ostatnim krokiem jest zaktualizowanie definicji klastra w celu zmiany deklaracji certyfikatu z odciskiem palca na nazwę pospolitą — tutaj przypięto również odciski palców wystawcy:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

W tym momencie można uruchomić aktualizacje wymienione powyżej w jednym wdrożeniu. w tej części usługa dostawcy zasobów Service Fabric podzieli uaktualnienie klastra w kilka kroków zgodnie z opisem w temacie [konwertowanie certyfikatów klastra z odciskiem palca na nazwę pospolitą](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Analiza i obserwacje
Ta sekcja jest częścią przechwycenia w celu wyjaśnienia szczegółowych czynności opisanych powyżej, a także do rysowania ważnych aspektów.

#### <a name="certificate-provisioning-explained"></a>Inicjowanie obsługi certyfikatów, wyjaśnione
Rozszerzenie KVVM, jako agent aprowizacji, działa w sposób ciągły zgodnie z ustaloną częstotliwością. W przypadku niepowodzenia pobrania zaobserwowanego certyfikatu nadal będzie on dalej w wierszu, a następnie przechodzi do trybu hibernacji do następnego cyklu. Rozszerzenie SFVM, jako agent inicjujący klaster, będzie wymagało zadeklarowanych certyfikatów, aby można było utworzyć klaster. To z kolei oznacza, że rozszerzenie SFVM może zostać uruchomione tylko po pomyślnym pobraniu certyfikatów klastra, które jest tutaj wskazywane przez ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` klauzulę oraz ustawienia rozszerzenia KeyVaultVM ```json "requireInitialSync": true``` . Wskazuje to na rozszerzenie KVVM, które w pierwszym uruchomieniu (po wdrożeniu lub ponownym uruchomieniu) musi przechodzić przez zaobserwowane certyfikaty do momentu pomyślnego pobrania wszystkich. Ustawienie tego parametru na false, powiązane z błędem pobrania certyfikatów klastra spowoduje niepowodzenie wdrożenia klastra. Bez względu na to, że wymaga synchronizacji początkowej z błędną/nieprawidłową listą obserwowanych certyfikatów, spowoduje to niepowodzenie rozszerzenia KVVM, a więc ponowne wdrożenie klastra nie powiodło się.  

#### <a name="certificate-linking-explained"></a>Łączenie certyfikatów, wyjaśnienie
Być może zauważono flagę "linkOnRenewal" rozszerzenia KVVM i oznacza to, że jest ona ustawiona na wartość false. W tym miejscu rozmieszczono tutaj szczegółowe omówienie zachowania kontrolowanego przez tę flagę i jego wpływ na działanie klastra. Należy zauważyć, że to zachowanie jest specyficzne dla systemu Windows.

Zgodnie z jego [definicją](../virtual-machines/extensions/key-vault-windows.md#extension-schema):

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Certyfikaty używane do nawiązania połączenia TLS są zazwyczaj [uzyskiwane jako dojście](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) za pośrednictwem dostawcy obsługi zabezpieczeń kanału S-Channel — to oznacza, że klient nie uzyskuje bezpośredniego dostępu do klucza prywatnego certyfikatu. Polecenie s-Channel obsługuje przekierowywanie (łączenie) poświadczeń w postaci rozszerzenia certyfikatu ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): Jeśli ta właściwość jest ustawiona, jego wartość reprezentuje odcisk palca certyfikatu "odnowienie", a w zamian zostanie podjęta próba załadowania połączonego certyfikatu. W rzeczywistości spowoduje to połączoną listę połączonych (i miejmy nadzieję acykliczne), dopóki nie zostanie ona zakończona certyfikatem "Final" — jeden bez znacznika odnowienia. Ta funkcja, gdy jest używana w rozsądny sposób, to doskonałe środki zaradcze przed utratą dostępności powodowaną przez wygasłe certyfikaty (na przykład). W innych przypadkach może być powodem przestoju trudnego do zdiagnozowania i rozwiązania problemu. Polecenie S-Channel wykonuje przechodzenie certyfikatów na podstawie ich właściwości odnowienia bezwarunkowo niezależnie od podmiotu, wystawców lub innych określonych atrybutów, które uczestniczą w weryfikacji podanego certyfikatu przez klienta. Istnieje również możliwość, że wynikający z tego certyfikat nie ma skojarzonego klucza prywatnego lub klucz nie został ACLed do jego potencjalnego klienta. 
 
Jeśli jest włączone łączenie, rozszerzenie maszyny wirtualnej magazynu kluczy, przy pobieraniu zaobserwowanego certyfikatu z magazynu, podejmie próbę znalezienia pasujących istniejących certyfikatów w celu połączenia ich za pośrednictwem właściwości rozszerzenia odnowienia. Dopasowanie jest (wyłącznie) w oparciu o alternatywną nazwę podmiotu (SAN) i działa jak exemplified poniżej.
Przyjęto założenie dwóch istniejących certyfikatów w następujący sposób: A: CN = "accessorers", SAN = {"alice.universalexports.com"}, Renew = "" B: CN = "bity Roberta", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, odnowienie = ""
 
Załóżmy, że certyfikat C jest pobierany przez KVVM EXT: CN = "Mallory", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
Lista sieci SAN jest w pełni dołączona do języka C, a więc. Renew = C. odcisk palca; Lista sieci SAN B jest wspólną częścią języka C, ale nie jest w pełni dołączana, więc B. odnowienie pozostanie puste.
 
Każda próba wywołania działanie funkcji AcquireCredentialsHandle (S-Channel) w tym stanie dla certyfikatu A rzeczywiście kończy wysyłanie C do strony zdalnej. W przypadku Service Fabric, [podsystem transportowy](service-fabric-architecture.md#transport-subsystem) klastra używa protokołu S-Channel do uwierzytelniania wzajemnego, dlatego opisane powyżej zachowanie ma bezpośredni wpływ na podstawową komunikację klastra. Kontynuacja powyższego przykładu, przy założeniu, że jest certyfikatem klastra, co dzieje się dalej:
  - Jeśli klucz prywatny języka C nie jest ACLd na konto, na którym działa sieć szkieletowa, zobaczymy błędy, aby uzyskać klucz prywatny (SEC_E_UNKNOWN_CREDENTIALS lub podobny).
  - Jeśli klucz prywatny języka C jest dostępny, zobaczymy błędy autoryzacji zwrócone przez inne węzły (CertificateNotMatched, Brak autoryzacji itp.). 
 
W obu przypadkach transport kończy się niepowodzeniem i klaster może wejść w dół. Objawy różnią się. Aby zwiększyć możliwości, konsolidacja zależy od kolejności odnowienia, która jest określana według kolejności listy obserwowanych certyfikatów rozszerzenia KVVM, harmonogramu odnawiania w magazynie, a nawet błędów przejściowych, które mogłyby zmienić kolejność pobierania.

Aby wyeliminować takie zdarzenia, zalecamy:
  - nie należy mieszać sieci San różnych certyfikatów magazynu; Każdy certyfikat magazynu powinien działać w określonym celu, a ich temat i sieć SAN powinny odzwierciedlać, że z specyfiką
  - Uwzględnij wspólną nazwę podmiotu na liście SAN (tak samo, "CN = <subject common name> ")  
  - w razie wątpliwości należy wyłączyć Łączenie przy odnawianiu dla certyfikatów zainicjowanych przy użyciu rozszerzenia KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Dlaczego warto używać tożsamości zarządzanej przypisanej przez użytkownika? Jakie są implikacje ich używania?
Zgodnie z powyższymi fragmentami kodu JSON w celu zagwarantowania powodzenia konwersji wymagana jest konkretna sekwencjonowanie operacji/aktualizacji oraz zachowanie dostępności klastra. W każdym przypadku zasób zestawu skalowania maszyn wirtualnych deklaruje i używa jego tożsamości do pobierania wpisów tajnych w jednym (z perspektywy użytkownika). Rozszerzenie maszyny wirtualnej Service Fabric (które powoduje uruchomienie klastra) zależy od ukończenia rozszerzenia maszyny wirtualnej magazynu kluczy, które jest zależne od pomyślnego pobrania obserwowanych certyfikatów. Rozszerzenie KVVM używa tożsamości zestawu skalowania maszyn wirtualnych w celu uzyskania dostępu do magazynu, co oznacza, że zasady dostępu w magazynie muszą zostać już zaktualizowane przed wdrożeniem zestawu skalowania maszyn wirtualnych. 

Aby usunąć tożsamość zarządzaną lub przypisać ją do innego zasobu, operator wdrożenia musi mieć wymaganą rolę (ManagedIdentityOperator) w ramach subskrypcji lub grupy zasobów, a także do ról wymaganych do zarządzania innymi zasobami, do których odwołuje się ten szablon. 

Z punktu widzenia zabezpieczeń należy odwołać się, że maszyna wirtualna (zestaw skalowania) jest uznawana za granicę zabezpieczeń w odniesieniu do jej tożsamości na platformie Azure. Oznacza to, że każda aplikacja hostowana na maszynie wirtualnej może w zasadzie uzyskać token dostępu reprezentujący tokeny dostępu do tożsamości zarządzane przez maszynę wirtualną są uzyskiwane z nieuwierzytelnionego punktu końcowego IMDS. Jeśli zauważasz, że maszyna wirtualna jest w środowisku udostępnionym lub wieloma dzierżawcami, prawdopodobnie ta metoda pobierania certyfikatów klastra nie jest wskazana. Jest jednak jedynym mechanizmem aprowizacji przystosowanym do przerzucania certyfikatów.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Rozwiązywanie problemów i często zadawane pytania

*P*: jak programowo zarejestrować się w certyfikacie zarządzanym magazynu kluczy?
Odp.: Znajdź nazwę wystawcy z dokumentacji magazynu kluczy *, a następnie* Zastąp ją w skrypcie poniżej.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*P*: co się stanie, gdy certyfikat zostanie wystawiony przez niezadeklarowany/nieokreślony wystawcę? Gdzie mogę uzyskać wyczerpującą listę aktywnych wystawców danej infrastruktury kluczy publicznych?
Odp *.:* Jeśli deklaracja certyfikatu określa odciski palców wystawcy, a bezpośredni wystawca certyfikatu nie jest uwzględniony na liście przypiętych wystawców, certyfikat będzie uznawany za nieważny — niezależnie od tego, czy jego katalog główny jest zaufany przez klienta. W związku z tym ważne jest, aby upewnić się, że lista wystawców jest aktualna/aktualna. Wprowadzenie nowego wystawcy jest zdarzeniem rzadkim i powinno być szeroko ogólnodostępne przed rozpoczęciem wystawiania certyfikatów. 

Ogólnie rzecz biorąc, Infrastruktura PKI publikuje i utrzymuje oświadczenie dotyczące postępowania certyfikacyjnego zgodnie ze standardem IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Między innymi informacje będą obejmować wszystkie aktywne emitenci. Pobieranie tej listy programistycznej może się różnić od infrastruktury PKI do innej.   

W przypadku firmy Microsoft — wewnętrzny infrastruktur kluczy publicznych zapoznaj się z wewnętrzną dokumentacją dla punktów końcowych/zestawów SDK używanych do pobrania autoryzowanych wystawców; jest on odpowiedzialny za to, aby właściciel klastra przesondował tę listę okresowo i upewnić się, że ich definicja klastra zawiera *wszystkie* oczekiwane wystawcy.

*P*: czy jest obsługiwanych wiele infrastruktur kluczy publicznych? 
Odp *.: tak*; nie można zadeklarować wielu wpisów CN w manifeście klastra o tej samej wartości, ale może wyświetlić listę wystawców z wielu infrastruktur kluczy publicznych odpowiadających temu samemu identyfikatorowi CN. Nie jest to zalecane rozwiązanie, a praktyki przejrzystości certyfikatu mogą uniemożliwiać wydawanie takich certyfikatów. Jednak w przypadku migrowania z jednej infrastruktury PKI do innej jest to akceptowalny mechanizm.

*P*: co zrobić, jeśli bieżący certyfikat klastra nie został wystawiony przez urząd certyfikacji lub nie ma zamierzonego tematu? 
Odp *.: uzyskanie* certyfikatu z zamierzonym tematem i dodanie go do definicji klastra jako pomocniczego, przez odciskiem palca. Po pomyślnym zakończeniu uaktualnienia zainicjuj kolejne uaktualnienie konfiguracji klastra, aby przekonwertować deklarację certyfikatu na nazwę pospolitą. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
