---
title: Uwierzytelnianie oparte na certyfikatach X.509 w klastrze sieci szkieletowej usług
description: Dowiedz się więcej o uwierzytelnianiu opartym na certyfikatach w klastrach sieci szkieletowej usług oraz o wykrywaniu, ograniczaniu i rozwiązywaniu problemów związanych z certyfikatami.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429670"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Uwierzytelnianie oparte na certyfikatach X.509 w klastrach sieci szkieletowej usług

Ten artykuł stanowi uzupełnienie wprowadzenia do [zabezpieczeń klastra sieci szkieletowej](service-fabric-cluster-security.md)usług i przechodzi do szczegółów uwierzytelniania opartego na certyfikatach w klastrach sieci szkieletowej usług. Zakładamy, że czytelnik jest zaznajomiony z podstawowych pojęć zabezpieczeń, a także z formantów, które usługa sieci szkieletowej udostępnia do kontrolowania zabezpieczeń klastra.  

Tematy objęte tym tytułem:

* Podstawowe informacje o uwierzytelnianiu oparte na certyfikatach
* Tożsamość i ich odpowiednie role
* Reguły konfiguracji certyfikatów
* Rozwiązywanie problemów i często zadawane pytania

## <a name="certificate-based-authentication-basics"></a>Podstawowe informacje o uwierzytelnianiu oparte na certyfikatach
Jako krótki odświeżacz, w bezpieczeństwie certyfikat jest instrumentem przeznaczonym do powiązania informacji dotyczących jednostki (podmiotu) z ich posiadaniem pary asymetrycznych kluczy kryptograficznych, a więc stanowi podstawową konstrukcję kryptografii klucza publicznego. Klucze reprezentowane przez certyfikat mogą być używane do ochrony danych lub do udowodnienia tożsamości posiadaczy kluczy; w połączeniu z systemem infrastruktury kluczy publicznych (PKI) certyfikat może reprezentować dodatkowe cechy jego podmiotu, takie jak własność domeny internetowej lub niektóre uprawnienia przyznane mu przez wystawcę certyfikatu (znanego jako urząd certyfikacji lub urząd certyfikacji). Powszechną aplikacją certyfikatów jest obsługa protokołu kryptograficznego TLS (Transport Layer Security), umożliwiającego bezpieczną komunikację za pośrednictwem sieci komputerowej. W szczególności klient i serwer używają certyfikatów, aby zapewnić prywatność i integralność komunikacji, a także przeprowadzić wzajemne uwierzytelnianie.

W sieci szkieletowej usług podstawowa warstwa klastra (Federation) również opiera się na TLS (wśród innych protokołów), aby osiągnąć niezawodną, bezpieczną sieć uczestniczących węzłów. Połączenia z klastrem za pośrednictwem interfejsów API klienta sieci szkieletowej usług używają protokołu TLS, a także do ochrony ruchu, a także do ustanawiania tożsamości stron. W szczególności, gdy jest używany do uwierzytelniania w sieci szkieletowej usług, certyfikat może służyć do udowodnienia następujących oświadczeń: a) osoba obsługująca poświadczenia certyfikatu jest w posiadaniu klucza prywatnego certyfikatu b) skrót SHA-1 certyfikatu ("odcisk palca") pasuje do deklaracji zawartej w definicji klastra lub c) wyróżniająca się nazwa pospolitowa podmiotu certyfikatu jest zgodna z deklaracją zawartą w definicji klastra , a wystawca certyfikatu jest znany lub zaufany.

Na powyższej liście "b" jest potocznie znane jako "przypinanie odcisku palca"; w takim przypadku deklaracja odnosi się do określonego certyfikatu, a siła schematu uwierzytelniania opiera się na założeniu, że jest niewykonalne obliczeniowo, aby utworzyć certyfikat, który wytwarza taką samą wartość mieszania jak inny, a jednocześnie jest prawidłowym, dobrze ukształtowanym obiektem we wszystkich innych aspektach. Pozycja "c" stanowi alternatywną formę deklarowania świadectwa, w przypadku gdy siła systemu opiera się na połączeniu przedmiotu świadectwa i organu wydającego. W takim przypadku deklaracja odnosi się do klasy certyfikatów - wszelkie dwa certyfikaty o tych samych cechach są uważane za w pełni równoważne. 

W poniższych sekcjach szczegółowo opisano, w jaki sposób środowisko uruchomieniowe sieci szkieletowej usług używa i sprawdza certyfikaty w celu zapewnienia zabezpieczeń klastra.

## <a name="identities-and-their-respective-roles"></a>Tożsamość i ich odpowiednie role
Przed zagłębieniem się w szczegóły uwierzytelniania lub zabezpieczania kanałów komunikacyjnych ważne jest, aby wymienić uczestniczące podmioty i odpowiadające im role, które odgrywają w klastrze:
- środowisko uruchomieniowe sieci szkieletowej usług, określane jako "system": zestaw usług, które zapewniają abstrakcje i funkcje reprezentujące klaster. Odnosząc się do komunikacji w klastrze między wystąpieniami systemu, użyjemy terminu "tożsamość klastra"; podczas odwoływania się do klastra jako adresata/obiektu docelowego ruchu spoza klastra, użyjemy terminu "tożsamość serwera".
- hostowane aplikacje, określane jako "aplikacje": kod dostarczony przez właściciela klastra, który jest aranżowany i wykonywany w klastrze
- klienci: jednostki mogą łączyć się z funkcjami i wykonywać je w klastrze zgodnie z konfiguracją klastra. Rozróżniamy dwa poziomy uprawnień - odpowiednio "użytkownik" i "admin". Klient "użytkownik" jest ograniczony przede wszystkim do operacji tylko do odczytu (ale nie wszystkie funkcje tylko do odczytu), podczas gdy klient "admin" ma nieograniczony dostęp do funkcji klastra. (Aby uzyskać więcej informacji, zobacz [Role zabezpieczeń w klastrze sieci szkieletowej usług).](service-fabric-cluster-security-roles.md)
- (Tylko na platformie Azure) usługi sieci szkieletowej usług, które organizują i udostępniają formanty do działania i zarządzania klastrami sieci szkieletowej usług, określane jako po prostu "usługa". W zależności od środowiska "usługa" może odnosić się do dostawcy zasobów sieci szkieletowej usługi Azure lub innych dostawców zasobów będących własnością zespołu sieci szkieletowej usług i obsługiwanych przez zespół sieci szkieletowej usług.

W bezpiecznym klastrze każda z tych ról może być skonfigurowana z własną, odrębną tożsamością, zadeklarowaną jako parowanie wstępnie zdefiniowanej nazwy roli i odpowiadających jej poświadczeń. Usługa Sieci szkieletowej obsługuje deklarowanie poświadczeń jako certyfikatów lub jednostki usługi opartej na domenie. (Obsługiwane są również tożsamości oparte na systemie Windows/Kerberos, ale wykraczają poza zakres tego artykułu; odnoszą się do [zabezpieczeń opartych na systemie Windows w klastrach sieci szkieletowej usług.)](service-fabric-windows-cluster-windows-security.md) W klastrach platformy Azure role klientów mogą być również deklarowane jako [tożsamości oparte na usłudze Azure Active Directory.](service-fabric-cluster-creation-setup-aad.md)

Jak nawiązywał do powyżej, środowisko uruchomieniowe sieci szkieletowej usług definiuje dwa poziomy uprawnień w klastrze: "admin" i "user". Klient administratora i składnik "system" będą działać z uprawnieniami "admin", a więc są niewyróżniające się od siebie. Po ustanowieniu połączenia w/do klastra uwierzytelniony wywoływacz zostanie przyznany przez środowisko uruchomieniowe sieci szkieletowej usług jedną z dwóch ról jako podstawa dla późniejszej autoryzacji. W poniższych sekcjach dokładnie przeanalizujemy uwierzytelnianie.

## <a name="certificate-configuration-rules"></a>Reguły konfiguracji certyfikatów
### <a name="validation-rules"></a>Reguły sprawdzania poprawności
Ustawienia zabezpieczeń klastra sieci szkieletowej usług opisują zasadniczo następujące aspekty:
- typ uwierzytelniania; jest to czas tworzenia, niezmienne charakterystyczne dla klastra. Przykładami takich ustawień są "ClusterCredentialType", "ServerCredentialType", a dozwolone wartości to "none", "x509" lub "windows". W tym artykule skupiono się na uwierzytelnianiu typu x509.
- (uwierzytelnianie) zasady walidacji; te ustawienia są ustawiane przez właściciela klastra i opisują, które poświadczenia są akceptowane dla danej roli. Przykłady zostaną szczegółowo zbadane bezpośrednio poniżej.
- ustawienia używane do dostosowywania lub subtelnej zmiany wyniku uwierzytelniania; przykłady tutaj obejmują flagi (de-) ograniczenie wymuszania list odwołania certyfikatów itp.

> [!NOTE]
> Przykłady konfiguracji klastra poniżej są fragmenty manifestu klastra w formacie XML, jako najbardziej strawione format, który obsługuje bezpośrednio funkcje sieci szkieletowej usług opisane w tym artykule. Te same ustawienia mogą być wyrażone bezpośrednio w reprezentacji JSON definicji klastra, czy autonomiczny manifest klastra json lub szablon mangement zasobów platformy Azure.

Reguła sprawdzania poprawności certyfikatu składa się z następujących elementów:
- odpowiednia rola: klient, klient administracyjny (rola uprzywilejowana)
- poświadczenie zaakceptowane dla roli, zadeklarowane przez odcisk palca lub nazwę pospolitą podmiotu

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Deklaracje sprawdzania poprawności certyfikatów oparte na odciskach palców
W przypadku reguł sprawdzania poprawności opartych na odciskach palców poświadczenia przedstawione przez wywołującego żądającego połączenia w/do klastra zostaną sprawdzone w następujący sposób:
  - poświadczenie jest prawidłowym, dobrze sformułowanym certyfikatem: jego łańcuch może być zbudowany, podpisy są zgodne
  - certyfikat jest ważny czas (NotBefore <= teraz < NotAfter)
  - skrót SHA-1 certyfikatu jest zgodny z deklaracją, jako porównanie ciągów bez uwzględniania wielkości liter, z wyłączeniem wszystkich odstępów

Wszelkie błędy zaufania napotkane podczas tworzenia łańcucha lub sprawdzania poprawności zostaną pominięte dla deklaracji opartych na odciskach palców, z wyjątkiem wygasłych certyfikatów — chociaż istnieją również przepisy dotyczące tego przypadku. W szczególności błędy związane z: stan odwołania jest nieznany lub w trybie offline, niezaufany root, nieprawidłowe użycie klucza, częściowy łańcuch są uważane za błędy niealne; Założeniem w tym przypadku jest to, że certyfikat jest jedynie kopertą dla pary kluczy - bezpieczeństwo polega na tym, że właściciel klastra ustanowił w miejscach środek zabezpieczający klucz prywatny.

Poniższy fragment manifestu klastra jest przykładem takiego zestawu reguł sprawdzania poprawności opartych na odciskach palców:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Każdy z powyższych wpisów odnosi się do określonej tożsamości, jak opisano wcześniej; każdy wpis umożliwia również określenie wielu wartości, jako listy ciągów oddzielonych przecinkami. W tym przykładzie po pomyślnym sprawdzeniu poprawności poświadczeń przychodzących prezenter certyfikatu z odciskiem palca SHA-1 'd5ec... 4264" otrzyma rolę "admina"; odwrotnie, rozmówca uwierzytelniający się z certyfikatem '7c8f... 01b0" zostanie przyznana rola "użytkownika", ograniczona głównie do operacji tylko do odczytu. Wywołujący przychodzący, który przedstawia certyfikat, którego odcisk palca jest abcd ... 1234" lub "ef01... 5678' zostaną zaakceptowane jako węzeł równorzędny w klastrze. Wreszcie klient łączący się z punktem końcowym zarządzania klastra będzie oczekiwać odcisk palca certyfikatu serwera jest ef01... 5678'. 

Jak wspomniano wcześniej, sieci szkieletowej usług nie zawiera przepisów dotyczących przyjmowania wygasłych certyfikatów; Powodem jest to, że certyfikaty mają ograniczony okres istnienia i, po zadeklarowaniu przez odcisk palca (który odwołuje się do określonego wystąpienia certyfikatu), zezwalając na wygaśnięcie certyfikatu spowoduje albo niepowodzenie połączenia z klastrem, albo bezpośrednie zwinięcie klastra. Zbyt łatwo jest zapomnieć lub zaniedbywać obracanie certyfikatu przypiętego odciskiem palca i niestety odzyskanie z takiej sytuacji jest trudne.

W tym celu właściciel klastra może jawnie stwierdzić, że certyfikaty z podpisem własnym zadeklarowane za pomocą odcisku palca uznaje się za ważne, w następujący sposób:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
To zachowanie nie obejmuje certyfikatów wystawionych przez urząd certyfikacji; gdyby tak było, cofnięty, znany z naruszenia wygasłego certyfikatu mógłby stać się "ważny", gdy tylko nie figuruje już na liście odwołania certyfikatu urzędu certyfikacji, a tym samym stanowi zagrożenie dla bezpieczeństwa. W przypadku certyfikatów z podpisem własnym właściciel klastra jest uważany za jedyną stronę odpowiedzialną za zabezpieczenie klucza prywatnego certyfikatu, co nie ma miejsca w przypadku certyfikatów wystawionych przez urząd certyfikacji — właściciel klastra może nie wiedzieć, w jaki sposób i kiedy ich certyfikat został uznany za naruszony.

#### <a name="common-name-based-certificate-validation-declarations"></a>Deklaracje sprawdzania poprawności certyfikatów oparte na nazwach pospolitych
Deklaracje oparte na nazwach pospolitych przyjmują jedną z następujących form:
- nazwa pospolita podmiotu (tylko)
- nazwa potoczna tematu z przypinaniem wystawcy

Najpierw rozważmy fragment manifestu klastra przykładem obu stylów deklaracji:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklaracje odnoszą się odpowiednio do tożsamości serwera i klastra; należy zauważyć, że deklaracje oparte na CN mają własne sekcje w manifeście klastra, oddzielone od standardowego "Zabezpieczenia". W obu deklaracjach "Nazwa" reprezentuje nazwę pospolitą przedmiotu wyróżniającą certyfikatu, a pole "Wartość" reprezentuje oczekiwanego wystawcę w następujący sposób:

- w pierwszym przypadku deklaracja stwierdza, że element nazwy pospolitej wyróżniający certyfikat serwera powinien być zgodny z ciągiem "server.demo.system.servicefabric.azure-int"; puste pole "Wartość" oznacza oczekiwanie, że katalog główny łańcucha certyfikatów jest zaufany w węźle/komputerze, na którym certyfikat serwera jest sprawdzany; w systemie Windows oznacza to, że certyfikat może łańcuch do dowolnego certyfikatu zainstalowanego w magazynie "Trusted Root CA";
- w drugim przypadku deklaracja stwierdza, że prezenter certyfikatu jest akceptowany jako węzeł równorzędny w klastrze, jeśli nazwa pospolita certyfikatu jest zgodna z ciągiem "cluster.demo.system.servicefabric.azure-int", *a* odcisk palca bezpośredniego wystawcy certyfikatu pasuje do jednego z wpisów oddzielonych przecinkami w polu "Wartość". (Ten typ reguły jest potocznie nazywany "nazwą pospolitą z przypinaniem wystawcy".)

W obu przypadkach łańcuch certyfikatu jest zbudowany i oczekuje się, że będzie wolny od błędów; oznacza to, że błędy odwołania, częściowy łańcuch lub błędy zaufania z nieprawidłowym czasem są uważane za krytyczne, a sprawdzanie poprawności certyfikatu zakończy się niepowodzeniem. Przypinanie wystawców spowoduje uznanie stanu "niezaufany katalog główny" za błąd nieobjęty krytyczną; wbrew pozorom jest to bardziej rygorystyczna forma walidacji, ponieważ pozwala właścicielowi klastra ograniczyć zestaw autoryzowanych/akceptowanych emitentów do własnej infrastruktury kluczy publicznych.

Po zbudowaniu łańcucha certyfikatów jest on sprawdzany względem standardowych zasad TLS/SSL z zadeklarowanym podmiotem jako nazwą zdalną; certyfikat będzie uważany za zgodny, jeśli jego nazwa zwyczajowa podmiotu lub którykolwiek z jego tematów alternatywnych nazw pasuje do deklaracji CN z manifestu klastra. Symbole wieloznaczne są obsługiwane w tym przypadku, a dopasowanie ciągu jest niewrażliwe na wielkości liter.

(Należy wyjaśnić, że sekwencja opisana powyżej może być wykonana dla każdego typu użycia klucza zadeklarowanego przez certyfikat; jeśli certyfikat określa użycie klucza uwierzytelniania klienta, łańcuch jest zbudowany i oceniany najpierw dla roli klienta. W przypadku powodzenia, ocena kończy się i sprawdzania poprawności jest pomyślny. Jeśli certyfikat nie ma użycia uwierzytelniania klienta lub sprawdzanie poprawności nie powiodło się, środowisko uruchomieniowe sieci szkieletowej usług skompiluje i oceni łańcuch uwierzytelniania serwera.)

Aby zakończyć przykład, poniższy fragment ilustruje deklarowanie certyfikatów klientów według nazwy pospolitej:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Powyższe deklaracje odpowiadają odpowiednio tożsamości administratora i użytkownika; sprawdzanie poprawności certyfikatów zadeklarowanych w ten sposób jest dokładnie tak, jak opisano w poprzednich przykładach certyfikatów klastra i serwera.

> [!NOTE]
> Deklaracje oparte na nazwach pospolitych mają na celu uproszczenie rotacji i ogólnie zarządzanie certyfikatami klastra. Zaleca się jednak przestrzeganie następujących zaleceń w celu zapewnienia ciągłej dostępności i bezpieczeństwa klastra:
  * preferuj przypinanie wystawcy do polegania na zaufanych korzeniach
  * uniknięcia mieszania emitentów z różnych PKI
  * zapewnić, aby wszyscy oczekiwani emitenci są wymienieni w deklaracji certyfikatu; niedopasowanie wystawcy spowoduje nieudaną weryfikację
  * upewnij się, że punkty końcowe zasad certyfikatów infrastruktury kluczy publicznych są wykrywalne, dostępne i dostępne — oznacza to, że punkty końcowe AIA, CRL lub OCSP są zadeklarowane na certyfikacie liścia i że są dostępne, aby można było ukończyć tworzenie łańcucha certyfikatów.

Łącząc to wszystko razem, po otrzymaniu żądania połączenia w klastrze zabezpieczonym certyfikatami X.509, środowisko uruchomieniowe sieci szkieletowej usług użyje ustawień zabezpieczeń klastra do sprawdzania poprawności poświadczeń strony zdalnej, jak opisano powyżej; Jeśli się powiedzie, osoba dzwoniąca/zdalnego jest uważana za uwierzytelnioną. Jeśli poświadczenia są zgodne z wieloma regułami sprawdzania poprawności, środowisko wykonawcze przyzna wywołującemu najwyższą uprzywilejowaną rolę dowolnej z dopasowanych reguł. 

### <a name="presentation-rules"></a>Reguły prezentacji
W poprzedniej sekcji opisano, jak działa uwierzytelnianie w klastrze zabezpieczonym certyfikatem; w tej sekcji wyjaśniono, jak środowisko uruchomieniowe sieci szkieletowej usług sam odnajduje i ładuje certyfikaty, których używa do komunikacji w klastrze; nazywamy je zasadami "prezentacji".

Podobnie jak w przypadku reguł sprawdzania poprawności, reguły prezentacji określają rolę i skojarzoną deklarację poświadczeń, wyrażoną za pomocą odcisku palca lub nazwy pospolitej. W przeciwieństwie do reguł sprawdzania poprawności deklaracje oparte na nazwach pospolitych nie mają przepisów dotyczących przypinania wystawcy; pozwala to na większą elastyczność, a także lepszą wydajność. Reguły prezentacji są zadeklarowane w sekcji "Typ węzła" manifestu klastra dla każdego typu węzła odrębnego; ustawienia są podzielone od sekcji Zabezpieczenia klastra, aby umożliwić każdemu typowi węzła pełną konfigurację w jednej sekcji. W klastrach usługi Azure Service Fabric deklaracje certyfikatów typu węzła domyślnie do odpowiednich ustawień w sekcji Zabezpieczenia definicji klastra.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Deklaracje prezentacji certyfikatów oparte na odciskach palców
Jak opisano wcześniej, środowisko uruchomieniowe sieci szkieletowej usług rozróżnia jego rolę jako elementu równorzędnego innych węzłów w klastrze i jako serwer dla operacji zarządzania klastrem. Zasadniczo te ustawienia mogą być konfigurowane wyraźnie, ale w praktyce mają tendencję do wyrównania. W dalszej części tego artykułu zakładamy, że ustawienia są zgodne z prostotą.

Rozważmy następujący fragment manifestu klastra:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Element "ClusterCertificate" demonstruje pełny schemat, w tym parametry opcjonalne ("X509FindValueSecondary") lub te z odpowiednimi ustawieniami domyślnymi ("X509StoreName"); inne deklaracje przedstawiają skrócony formularz. Powyższa deklaracja certyfikatu klastra stanowi, że ustawienia zabezpieczeń węzłów typu "nt1vm" są inicjowane za pomocą certyfikatu 'cc71.. 1984" jako podstawowy, a '49e2.. 19d6' świadectwo jako świadectwo wtórne; oczekuje się, że oba certyfikaty zostaną\'znalezione w magazynie certyfikatów LocalMachine My (lub w ścieżce równoważnej linuksa, *var/lib/sfcerts).*

#### <a name="common-name-based-certificate-presentation-declarations"></a>Deklaracje prezentacji certyfikatów oparte na nazwach pospolitych
Certyfikaty typu węzła mogą być również zadeklarowane przez nazwę pospolitą podmiotu, jak na przykładzie poniżej:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Dla obu typów deklaracji węzeł sieci szkieletowej usług odczytuje konfigurację podczas uruchamiania, lokalizuje i ładuje określone certyfikaty i sortuje je w porządku malejącym atrybutu NotAfter; wygasłe certyfikaty są ignorowane, a pierwszy element listy jest wybierany jako poświadczenie klienta dla każdego połączenia sieci szkieletowej usług, które zostało podjęte przez ten węzeł. (W efekcie sieci szkieletowej usług faworyzuje najdalej wygasający certyfikat.)

Należy zauważyć, że w przypadku deklaracji prezentacji opartych na nazwie pospolitej certyfikat jest uważany za zgodny, jeśli jego nazwa pospolita podmiotu jest równa pola X509FindValue (lub X509FindValueSecondary) jako uwzględniającego wielkość liter, dokładne porównanie ciągów. Jest to w przeciwieństwie do reguł sprawdzania poprawności, która obsługuje dopasowywanie symboli wieloznacznych, a także porównania ciągów bez uwzględniania wielkości liter.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Różne ustawienia konfiguracji certyfikatów
Wcześniej wspomniano, że ustawienia zabezpieczeń klastra sieci szkieletowej usług umożliwiają również subtelną zmianę zachowania kodu uwierzytelniania. Chociaż artykuł na [temat ustawień klastra sieci szkieletowej usług](service-fabric-cluster-fabric-settings.md) reprezentuje kompleksową i najbardziej aktualną listę ustawień, rozwiniemy znaczenie wybranych kilku ustawień zabezpieczeń w tym miejscu, aby zakończyć pełną uwidocznić na podstawie uwierzytelniania opartego na certyfikatach. Dla każdego ustawienia wyjaśnimy intencji, domyślne wartości/zachowania, jak to wpływa na uwierzytelnianie i jakie wartości są dopuszczalne.

Jak wspomniano, sprawdzanie poprawności certyfikatu zawsze oznacza budowanie i ocenę łańcucha certyfikatu. W przypadku certyfikatów wystawionych przez urząd certyfikacji to pozornie proste wywołanie interfejsu API systemu operacyjnego zazwyczaj wiąże się z kilkoma wywołaniami wychodzącymi do różnych punktów końcowych wystawiającej infrastruktury kluczy publicznych, buforowania odpowiedzi i tak dalej. Biorąc pod uwagę częstość występowania wywołań sprawdzania poprawności certyfikatów w klastrze sieci szkieletowej usług, wszelkie problemy w punktach końcowych infrastruktury kluczy publicznych mogą spowodować zmniejszenie dostępności klastra lub bezpośredni podział. Chociaż nie można pominąć wywołań wychodzących (zobacz poniżej w sekcji CZĘSTO ZADAWANE PYTANIA, aby uzyskać więcej informacji na ten temat), następujące ustawienia mogą służyć do maskowania błędów sprawdzania poprawności spowodowanych niepowodzeniem wywołań crl.

  * CrlCheckingFlag - w sekcji "Bezpieczeństwo" ciąg przekonwertowany na UINT. Wartość tego ustawienia jest używana przez sieć szkieletową usług do maskowania błędów stanu łańcucha certyfikatów przez zmianę zachowania budynku łańcucha; jest przekazywana do wywołania Certyfikatu Certyfikatu Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) jako parametr "dwFlags" i może być ustawiona na dowolną prawidłową kombinację flag zaakceptowanych przez funkcję. Wartość 0 wymusza środowisko uruchomieniowe sieci szkieletowej usług do ignorowania błędów stanu zaufania — nie jest to zalecane, ponieważ jego użycie stanowiłoby znaczną ekspozycję zabezpieczeń. Wartość domyślna to 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Kiedy używać: do testowania lokalnego, z certyfikatami z podpisem własnym lub certyfikatami dewelopera, które nie są w pełni ukształtowane/nie mają odpowiedniej infrastruktury klucza publicznego do obsługi certyfikatów. Może również wykorzystywać jako łagodzenie w środowiskach wypaczonych powietrzem podczas przejścia między PKI.

  Sposób użycia: weźmiemy przykład, który wymusza sprawdzanie odwołania, aby uzyskać dostęp tylko do buforowanych adresów URL. Zakładając:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  następnie deklaracja w manifeście klastra staje się:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - w sekcji "Bezpieczeństwo" wartość logiczna z domyślną wartością "false". Reprezentuje skrót do wygaszania stanu błędu budynku łańcucha "odwołanie w trybie offline" (lub kolejny stan błędu sprawdzania poprawności zasad łańcucha).

  Kiedy używać: testowanie lokalne lub certyfikaty dewelopera nie są poparte odpowiednią pki. Należy używać jako środki zaradcze w środowiskach wypatrzanych powietrzem lub gdy wiadomo, że pki są niedostępne.

  Sposób użycia:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Inne godne uwagi ustawienia (wszystkie w sekcji "Zabezpieczenia"):
  * AcceptExpiredPinnedClusterCertificate — omówione w sekcji poświęconej weryfikacji certyfikatu opartego na odciskach palców; umożliwia akceptowanie wygasłych certyfikatów klastra z podpisem własnym. 
  * CertificateExpirySafetyMargin - interwał, wyrażony w minutach poprzedzających sygnaturę czasową certyfikatu NotAfter i podczas którego certyfikat jest uważany za zagrożony wygaśnięciem. Usługa Service Fabric monitoruje certyfikaty klastra i okresowo emituje raporty dotyczące kondycji dotyczące ich pozostałej dostępności. W przedziale "bezpieczeństwa" te raporty dotyczące zdrowia są podwyższone do stanu "ostrzeżenie". Wartość domyślna to 30 dni.
  * CertificateHealthReportingInterval - kontroluje częstotliwość raportów kondycji dotyczących pozostałej ważności czasu certyfikatów klastra. Raporty będą emitowane tylko raz w tym przedziale. Wartość jest wyrażona w sekundach, z domyślną wartością 8 godzin.
  * EnforcePrevalidationOnSecurityChanges - logiczne, kontroluje zachowanie uaktualnienia klastra po wykryciu zmian ustawień zabezpieczeń. Jeśli ustawiona jest wartość "true", uaktualnienie klastra będzie próbowało upewnić się, że co najmniej jeden z certyfikatów pasujących do dowolnej reguł prezentacji może przekazać odpowiednią regułę sprawdzania poprawności. Wstępna weryfikacja jest wykonywana przed zastosowaniem nowych ustawień do dowolnego węzła, ale jest uruchamiana tylko w węźle obsługującym podstawową replikę usługi Menedżera klastrów w momencie inicjowania uaktualnienia. W chwili pisania tego zapisu ustawienie ma wartość domyślną "false" i zostanie ustawiona na "true" dla nowych klastrów sieci szkieletowej usług Azure z wersją środowiska wykonawczego, począwszy od 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Scenariusz end-to-end (przykłady)
Przyjrzeliśmy się regułom prezentacji, regułom sprawdzania poprawności i podkręcaniu flag, ale jak to wszystko działa razem? W tej sekcji będziemy pracować przez dwa przykłady end-to-end pokazujące, jak ustawienia zabezpieczeń mogą być wykorzystywane do bezpiecznego uaktualniania klastra. Należy zauważyć, że nie ma to być wyczerpująca rozprawa na temat prawidłowego zarządzania certyfikatami w sieci szkieletowej usług, poszukaj artykułu towarzyszącego na ten temat.

Rozdzielenie zasad prezentacji i walidacji rodzi oczywiste pytanie (lub obawy) dotyczące tego, czy mogą się one różnić i jakie byłyby konsekwencje. Jest rzeczywiście możliwe, że wybór certyfikatu uwierzytelniania przez węzeł nie przejdzie reguł sprawdzania poprawności innego węzła. W rzeczywistości ta rozbieżność jest główną przyczyną incydentów związanych z uwierzytelnianiem. Jednocześnie oddzielenie tych reguł umożliwia klastrowi kontynuowanie pracy podczas uaktualniania, które zmienia ustawienia zabezpieczeń klastra. Należy wziąć pod uwagę, że rozszerzając najpierw reguły sprawdzania poprawności jako pierwszy krok, wszystkie węzły klastra będą zbiegać się w nowych ustawieniach, podczas gdy nadal przy użyciu bieżących poświadczeń. 

Przypomnijmy, że w klastrze sieci szkieletowej usług uaktualnienie postępuje przez (maksymalnie 5) "domen uaktualnienia" lub UD. Tylko węzły w bieżącym UD są uaktualniane/zmieniane w danym momencie, a uaktualnienie przejdzie do następnego UD tylko wtedy, gdy umożliwia to dostępność klastra. (Aby uzyskać więcej informacji, zobacz [uaktualnienia klastra sieci szkieletowej](service-fabric-cluster-upgrade.md) usług i inne artykuły na ten sam temat). Zmiany certyfikatów/zabezpieczeń są szczególnie ryzykowne, ponieważ mogą izolować węzły od klastra lub pozostawić klaster na krawędzi utraty kworum.

Do opisania ustawień zabezpieczeń węzła użyjemy następującego notacji:

Nk: {P:{TP=A}, V:{TP=A}}, gdzie:
  - 'Nk' reprezentuje węzeł w domenie uaktualnienia *k*
  - "P" reprezentuje bieżące reguły prezentacji węzła (przy założeniu, że odnosimy się tylko do certyfikatów klastra); 
  - "V" reprezentuje bieżące reguły sprawdzania poprawności węzła (tylko certyfikat klastra)
  - 'TP=A' reprezentuje deklarację opartą na odciskach palców (TP), przy czym "A" jest odciskiem palca certyfikatu
  - "CN=B" oznacza deklarację opartą na nazwie zwyczajowej (CN), przy czym "B" jest nazwą pospolitą przedmiotu certyfikatu 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Obracanie certyfikatu klastra zadeklarowanego za pomocą odcisku palca
W poniższej sekwencji opisano, jak uaktualnienie dwuetapowe może służyć do bezpiecznego wprowadzenia certyfikatu klastra pomocniczego, zadeklarowanego przez odcisk palca; pierwsza faza wprowadza nową deklarację certyfikatu do reguł walidacji, a drugi etap wprowadza ją do reguł prezentacji:
  - stan początkowy: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - klaster jest w spoczynku, wszystkie węzły mają wspólną konfigurację
  - po ukończeniu uaktualnienia domeny 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - węzły w UD0 przedstawią certyfikat A i zaakceptują certyfikaty A lub B; wszystkie inne węzły obecne i akceptują tylko certyfikat A
  - po ukończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - wszystkie węzły mają certyfikat A, wszystkie węzły będą akceptować certyfikat A lub B
      
W tym momencie klaster jest ponownie w równowadze, a druga faza uaktualnienia / zmiany ustawień zabezpieczeń może rozpocząć:
  - po ukończeniu uaktualnienia domeny 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - węzły w UD0 zaczną prezentować B, który jest akceptowany przez inny węzeł w klastrze.
  - po ukończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - wszystkie węzły przełączyły się na okazywanie certyfikatu B. Certyfikat A może być teraz wycofany/usunięty z definicji klastra z kolejnym zestawem uaktualnień.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Konwertowanie klastra z odcisków palców na deklaracje certyfikatów oparte na nazwach pospolitych
Podobnie zmiana typu deklaracji certyfikatu (z odcisku palca na nazwę pospolitą) będzie zgodna z tym samym wzorcem, co powyżej. Należy zauważyć, że reguły sprawdzania poprawności zezwalają na deklarowanie certyfikatów danej roli za pomocą odcisku palca i nazwy pospolitej w tej samej definicji klastra. Natomiast zasady prezentacji dopuszczają tylko jedną formę deklaracji. Nawiasem mówiąc, bezpieczne podejście do konwersji certyfikat klastra z odcisku palca na nazwę pospolitą jest wprowadzenie zamierzonego certyfikatu docelowego najpierw za pomocą odcisku palca, a następnie zmiana tej deklaracji na pospolitą na opartą na nazwie. W poniższym przykładzie założymy, że odcisk palca "A" i nazwa pospolita podmiotu "B" odnoszą się do tego samego certyfikatu. 

  - stan początkowy: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - klaster jest w spoczynku, wszystkie węzły mają wspólną konfigurację, przy czym A jest podstawowym odciskiem palca certyfikatu
  - po ukończeniu uaktualnienia domeny 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - węzły w UD0 przedstawią certyfikat A i zaakceptują certyfikaty z nadrukiem palca A lub nazwą zwyczajową B; wszystkie inne węzły obecne i akceptują tylko certyfikat A
  - po ukończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - wszystkie węzły prezentują certyfikat A, wszystkie węzły będą akceptować certyfikat A (TP) lub B (CN)

W tym momencie możemy kontynuować zmianę reguł prezentacji z kolejnym uaktualnieniem:
  - po ukończeniu uaktualnienia domeny 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - węzły w UD0 przedstawią certyfikat B znaleziony przez CN i zaakceptują certyfikaty z nadrukiem palca A lub nazwą pospolitą B; wszystkie inne węzły obecne i akceptują tylko certyfikat A, wybrany za pomocą odcisku palca
  - po ukończeniu ostatniej domeny uaktualnienia: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - wszystkie węzły obecne certyfikat B znalezione przez CN, wszystkie węzły zaakceptują certyfikat A (TP) lub B (CN)
    
Zakończenie fazy 2 oznacza również konwersję klastra na certyfikaty oparte na nazwach pospolitych; deklaracje sprawdzania poprawności oparte na odciskach palców można usunąć w kolejnym uaktualnieniu klastra.

> [!NOTE]
> W klastrach usługi Azure Service Fabric przepływy pracy przedstawione powyżej są organizowane przez dostawcę zasobów sieci szkieletowej usług; właściciel klastra jest nadal odpowiedzialny za inicjowanie obsługi administracyjnej certyfikatów do klastra zgodnie ze wskazanymi regułami (prezentacja lub sprawdzanie poprawności) i jest zachęcany do wykonywania zmian w wielu krokach.

W osobnym artykule zajmiemy się tematem zarządzania i inicjowania obsługi administracyjnej certyfikatów do klastra sieci szkieletowej usług.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Rozwiązywanie problemów i często zadawane pytania
Podczas debugowania problemów związanych z uwierzytelnianiem w klastrach sieci szkieletowej usług nie jest łatwe, mamy nadzieję, że następujące wskazówki i wskazówki mogą pomóc. Najprostszym sposobem rozpoczęcia badania jest zbadanie dzienników zdarzeń sieci szkieletowej usług w węzłach klastra — niekoniecznie tylko tych, które wykazują objawy, ale także węzłów, które są w górę, ale nie mogą połączyć się z jednym z ich sąsiadów. W systemie Windows zdarzenia o istotnym znaczeniu są zazwyczaj rejestrowane w kanałach "Dzienniki aplikacji i usług\Microsoft-ServiceFabric\Admin" lub "Operacyjne". Czasami może być [pomocne, aby włączyć capi2 rejestrowania,](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)aby uchwycić więcej szczegółów dotyczących sprawdzania poprawności certyfikatu, pobieranie CRL / CTL itp (Czy pamiętaj, aby wyłączyć go po zakończeniu repro, może to być dość pełne.)

Typowe objawy, które objawiają się w klastrze występują problemy z uwierzytelnianiem są: 
  - węzły są w dół/jazda na rowerze 
  - próby połączenia są odrzucane
  - próby połączenia są przesuwem czasu

Każdy z objawów może być spowodowany przez różne problemy, a ta sama przyczyna może wykazywać różne objawy; jako takie, po prostu wymienimy małą próbkę typowych problemów, z zaleceniami dotyczącymi ich naprawienia. 

* Węzły mogą wymieniać wiadomości, ale nie można nawiązać połączenia. Możliwą przyczyną niepowodzenia połączenia jest błąd "certyfikat nie dopasowywał się" — jedna ze stron w połączeniach sieci szkieletowej usług z siecią szkieletową usług przedstawia certyfikat, który nie spełnia reguł sprawdzania poprawności adresata. Może towarzyszyć jeden z następujących błędów: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Aby zdiagnozować/zbadać dalej: na każdym z węzłów próbujących połączenie, należy określić, który certyfikat jest prezentowany; sprawdź certyfikat i spróbuj emulować reguły sprawdzania poprawności (sprawdź odcisk palca lub wspólną nazwę równości, sprawdź odciski palców wystawcy, jeśli są określone).

  Innym częstym towarzyszącym kodem błędu może być:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  W takim przypadku certyfikat jest zadeklarowany nazwą pospolitą i stosuje się jedną z następujących właściwości:
    - wystawcy nie są przypięte, a certyfikat główny nie jest zaufany lub
    - emitenci są przypięte, ale deklaracja nie zawiera odcisk palca bezpośredniego wystawcy tego certyfikatu

* Węzeł jest w górę, ale nie może połączyć się z innymi węzłami; inne węzły nie odbierają ruchu przychodzącego z węzła, który ulegnie awarii. W takim przypadku jest możliwe, że ładowanie certyfikatu nie powiedzie się w węźle lokalnym. Poszukaj następujących błędów:
  - nie znaleziono certyfikatu — upewnij się, że certyfikaty zadeklarowane w regułach prezentacji mogą być rozpoznawane przez zawartość magazynu certyfikatów LocalMachine\My (lub zgodnie z określoną). 
    Możliwe przyczyny awarii mogą obejmować: 
      - nieprawidłowe znaki w deklaracji odcisku palca
      - certyfikat nie jest zainstalowany
      - certyfikat wygasł
      - deklaracja nazwy pospolitej zawiera prefiks "CN="
      - deklaracja określa symbol wieloznaczny i nie istnieje dokładne dopasowanie w magazynie certyfikatów (deklaracja: CN=*.mojadomena.com, rzeczywisty certyfikat: CN=server.mydomain.com)

  - nieznane poświadczenia - wskazuje brakujący klucz prywatny odpowiadający certyfikatowi, zazwyczaj któremu towarzyszy kod błędu: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Aby temu zaradzić, sprawdź istnienie klucza prywatnego; weryfikacji SFAdmins jest przyznawany "read|execute" dostęp do klucza prywatnego.

  - zły typ dostawcy - wskazuje certyfikat Crypto New Generation (CNG) ("Dostawca magazynu kluczy oprogramowania firmy Microsoft"); w tej chwili usługa Service Fabric obsługuje tylko certyfikaty CAPI1. Zazwyczaj towarzyszy mu kod błędu:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Aby rozwiązać ten problem, ponownie utwórz certyfikat klastra przy użyciu dostawcy CAPI1 (np. Więcej informacji na temat dostawców kryptograficznych można znaleźć [w opisie dostawców kryptograficznych](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

