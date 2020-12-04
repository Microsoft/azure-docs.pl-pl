---
title: Uwierzytelnianie oparte na certyfikatach X. 509 w klastrze Service Fabric
description: Informacje o uwierzytelnianiu opartym na certyfikatach w klastrach Service Fabric i sposobach wykrywania, łagodzenia i rozwiązywania problemów związanych z certyfikatem.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 4d81cb9d224bdc2e3002c621c86729df235e0d81
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574772"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Uwierzytelnianie oparte na certyfikatach X. 509 w klastrach Service Fabric

Ten artykuł uzupełnia wprowadzenie do [Service Fabric zabezpieczenia klastra](service-fabric-cluster-security.md)i zawiera szczegółowe informacje dotyczące uwierzytelniania opartego na certyfikatach w klastrach Service Fabric. Przyjęto założenie, że czytelnik jest zaznajomiony z podstawowymi pojęciami dotyczącymi zabezpieczeń, a także z kontrolkami, które Service Fabric uwidaczniają kontrolę nad bezpieczeństwem klastra.  

Tematy objęte tym tytułem:

* Podstawowe informacje dotyczące uwierzytelniania opartego na certyfikatach
* Tożsamości i ich odpowiednie role
* Reguły konfiguracji certyfikatów
* Rozwiązywanie problemów i często zadawane pytania

## <a name="certificate-based-authentication-basics"></a>Podstawowe informacje dotyczące uwierzytelniania opartego na certyfikatach
Jako krótki odświeżacz, w zabezpieczeniach, certyfikat jest instrumentem przeznaczonym do wiązania informacji dotyczących jednostki (podmiot) do ich posiadania pary asymetrycznych kluczy kryptograficznych i dlatego stanowi podstawową konstrukcję kryptografii klucza publicznego. Klucze reprezentowane przez certyfikat mogą służyć do ochrony danych lub do potwierdzania tożsamości posiadaczy kluczy; gdy jest używany w połączeniu z systemem infrastruktury kluczy publicznych (PKI), certyfikat może reprezentować dodatkowe cechy jego tematu, takie jak własność domeny internetowej lub określone uprawnienia przyznane mu przez wystawcę certyfikatu (znany jako urząd certyfikacji lub urząd certyfikacji). Typowa aplikacja certyfikatów obsługuje protokół kryptograficzny Transport Layer Security (TLS), co umożliwia bezpieczną komunikację w sieci komputerowej. Klient i serwer używają certyfikatów do zapewnienia prywatności i integralności ich komunikacji oraz do przeprowadzania wzajemnego uwierzytelniania.

W Service Fabric podstawowa warstwa klastra (Federacji) również kompiluje protokół TLS (między innymi protokołami) w celu zapewnienia niezawodnej, bezpiecznej sieci węzłów uczestniczących. Połączenia do klastra za pośrednictwem interfejsów API klienta Service Fabric używają protokołu TLS oraz do ochrony ruchu, a także do ustanowienia tożsamości stron. W odniesieniu do uwierzytelniania w Service Fabric certyfikat może być używany do potwierdzenia następujących oświadczeń: a) prezenter poświadczeń certyfikatu posiadający klucz prywatny certyfikatu b) skrót SHA-1 certyfikatu (odcisk palca) jest zgodny z deklaracją uwzględnioną w definicji klastra, lub c) Wspólna nazwa podmiotu wyróżniającego certyfikatu jest zgodna z deklaracją uwzględnioną w definicji klastra , a wystawca certyfikatu jest znany lub zaufany.

Na powyższej liście "b" jest colloquially znany jako "Przypinanie odcisku palca"; w tym przypadku deklaracja odnosi się do określonego certyfikatu, a siła schematu uwierzytelniania opiera się na zapewnieniu, że nie jest ona w stanie w sposób nieprzewidziany do sfałszowania certyfikatu, który tworzy taką samą wartość skrótu jak inna, podczas gdy nadal jest prawidłowy, dobrze sformułowany obiekt we wszystkich innych aspektach. Element "c" reprezentuje alternatywną formę deklarowania certyfikatu, w którym siła schematu jest połączona z kombinacją podmiotu certyfikatu i urzędu wystawiającego certyfikaty. W tym przypadku deklaracja odnosi się do klasy certyfikatów — wszystkie dwa certyfikaty o tej samej charakterystyce są uznawane za w pełni równoważne. 

W poniższych sekcjach wyjaśniono, jak środowisko uruchomieniowe Service Fabric używa i sprawdza poprawność certyfikatów w celu zapewnienia bezpieczeństwa klastra.

## <a name="identities-and-their-respective-roles"></a>Tożsamości i ich odpowiednie role
Przed przekazaniem szczegółów dotyczących uwierzytelniania lub zabezpieczania kanałów komunikacji należy zwrócić uwagę na listę uczestniczących aktorów oraz odpowiednie role, które odgrywają w klastrze:
- środowisko uruchomieniowe Service Fabric, określane jako "system": zestaw usług, które zapewniają abstrakcje i funkcje reprezentujące klaster. W przypadku odwoływania się do komunikacji w klastrze między wystąpieniami systemu zostanie użyty termin "tożsamość klastra"; w przypadku odwoływania się do klastra jako odbiorcy/celu ruchu spoza klastra używany jest termin "tożsamość serwera".
- aplikacje hostowane, określane jako "aplikacje": kod dostarczony przez właściciela klastra, który jest zorganizowany i wykonywany w klastrze
- Klienci: jednostki, które mogą nawiązywać połączenia i wykonywać funkcje w klastrze, zgodnie z konfiguracją klastra. Rozróżniamy odpowiednio dwa poziomy uprawnień — "użytkownik" i "admin". Klient "użytkownik" jest ograniczony głównie do operacji tylko do odczytu (ale nie wszystkich funkcji tylko do odczytu), natomiast klient "admin" ma nieograniczony dostęp do funkcjonalności klastra. (Aby uzyskać więcej informacji, zobacz [role zabezpieczeń w klastrze Service Fabric](service-fabric-cluster-security-roles.md)).
- (Tylko platforma Azure) usługi Service Fabric, które organizują i uwidaczniają kontrolki do działania i zarządzania klastrami Service Fabric, zwanymi po prostu "usługą". W zależności od środowiska "usługa" może odwoływać się do dostawcy zasobów usługi Azure Service Fabric lub innych dostawców zasobów należących do zespołu Service Fabric.

W zabezpieczonym klastrze każdą z tych ról można skonfigurować przy użyciu własnej, odrębnej tożsamości, zadeklarowanej jako parowanie wstępnie zdefiniowanej nazwy roli i odpowiedniego poświadczenia. Service Fabric obsługuje deklarowanie poświadczeń jako certyfikatów lub nazwy głównej usługi opartej na domenie. (Identyfikatory systemu Windows-/Kerberos-based są również obsługiwane, ale wykraczają poza zakres tego artykułu; zapoznaj się z [zabezpieczeniami opartymi na systemie Windows w klastrach Service Fabric](service-fabric-windows-cluster-windows-security.md).) W klastrach platformy Azure role klientów mogą być również deklarowane jako [tożsamości oparte na Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Jak sugerowali powyżej, środowisko uruchomieniowe Service Fabric definiuje dwa poziomy uprawnień w klastrze: "admin" i "User". Klient administracyjny i składnik "system" mogą działać z uprawnieniami administratora i dlatego nie można ich odróżnić od siebie. Po nawiązaniu połączenia z klastrem, uwierzytelniony obiekt wywołujący zostanie udzielony przez Service Fabric środowisko uruchomieniowe jednej z dwóch ról jako podstawy dla kolejnej autoryzacji. Szczegółowe omówienie uwierzytelniania znajdziesz w poniższych sekcjach.

## <a name="certificate-configuration-rules"></a>Reguły konfiguracji certyfikatów
### <a name="validation-rules"></a>Reguły walidacji
Ustawienia zabezpieczeń klastra Service Fabric opisują w zasadzie następujące aspekty:
- Typ uwierzytelniania; jest to czas utworzenia, niezmienne cechy charakterystyczne klastra. Przykładami takich ustawień są "ClusterCredentialType", "ServerCredentialType", a dozwolone wartości to "none", "x509" lub "Windows". Ten artykuł koncentruje się na uwierzytelnianiu typu x509.
- Reguły walidacji (uwierzytelnianie); te ustawienia są ustawiane przez właściciela klastra i opisują, które poświadczenia są akceptowane dla danej roli. Przykłady zostaną sprawdzone na głębokości bezpośrednio poniżej.
- ustawienia służące do dostosowywania lub niewielkich zmian wyniku uwierzytelniania; Przykłady obejmują flagi (de-) ograniczają wymuszanie list odwołania certyfikatów itd.

> [!NOTE]
> Przykłady konfiguracji klastra podane poniżej stanowią fragmenty z manifestu klastra w formacie XML, ponieważ najbardziej szyfrowany format, który obsługuje bezpośrednio funkcje Service Fabric opisane w tym artykule. Te same ustawienia można wyrazić bezpośrednio w reprezentacjach JSON definicji klastra, niezależnie od tego, czy jest to autonomiczny manifest klastra JSON, czy szablon zarządzania zasobami platformy Azure.

Reguła walidacji certyfikatu składa się z następujących elementów:
- odpowiednia rola: klient, klient administracyjny (rola uprzywilejowana)
- poświadczenie zaakceptowane dla roli, zadeklarowane za pomocą odcisku palca lub nazwy pospolitej podmiotu

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Deklaracje weryfikacji certyfikatu opartego na odcisku palca
W przypadku reguł weryfikacji opartych na odcisku palca poświadczenia przedstawione przez wywołującego żądające połączenia w/do klastra zostaną zweryfikowane w następujący sposób:
  - poświadczenie jest prawidłowym poprawnie sformułowanym certyfikatem: jego łańcuch może być zbudowany, dopasowanie podpisów
  - certyfikat jest prawidłowy (NotBefore <= Now < NotAfter)
  - skrót SHA-1 certyfikatu pasuje do deklaracji, jako porównanie ciągów bez uwzględniania wielkości liter, z wyłączeniem wszystkich białych znaków

Wszelkie błędy zaufania napotkane podczas tworzenia łańcucha lub walidacji zostaną pominięte dla deklaracji opartych na odcisku palca, z wyjątkiem wygasłych certyfikatów — mimo że w tym przypadku istnieją postanowienia. W odniesieniu do błędów związanych z: stan odwołania jest nieznany lub w trybie offline, niezaufanego certyfikatu głównego, nieprawidłowe użycie klucza, łańcuch częściowy jest traktowany jako Błędy niekrytyczne; w tym przypadku, w tym przypadku, jest to, że certyfikat jest tylko kopertą pary kluczy — zabezpieczenia polegają na tym, że właściciel klastra ustawił w miejscu miary, aby zabezpieczyć klucz prywatny.

Poniższy fragment z manifestu klastra exemplifies taki zestaw reguł walidacji opartych na odcisku palca:

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

Każdy z powyższych wpisów odnosi się do określonej tożsamości zgodnie z wcześniejszym opisem. Każdy wpis umożliwia również określenie wielu wartości, jako listę ciągów rozdzielanych przecinkami. W tym przykładzie, po pomyślnym zweryfikowaniu poświadczeń przychodzących, prezenter certyfikatu z odciskiem palca SHA-1 5ec... 4264 "zostanie przyznana rola" admin "; odwrotnie, obiekt wywołujący uwierzytelniający się przy użyciu certyfikatu "7c8f... 01b0 "zostanie przyznana rola" użytkownika ", ograniczona do wszystkich operacji tylko do odczytu. Wywołujący ruch przychodzący, który przedstawia certyfikat, którego odcisk palca to "abcd... 1234 ' lub ' EF01... 5678 ' zostanie zaakceptowany jako węzeł równorzędny w klastrze. Na koniec klient łączący się z punktem końcowym zarządzania klastra będzie oczekiwał odcisku palca certyfikatu serwera jako "EF01... 5678 '. 

Jak wspomniano wcześniej, Service Fabric stanowi wprowadzenie do akceptowania wygasłych certyfikatów; przyczyną jest to, że certyfikaty mają ograniczony okres istnienia i, po zadeklarowaniu przez odcisk palca (odwołujące się do określonego wystąpienia certyfikatu), co pozwala na wygaśnięcie certyfikatu, spowoduje nawiązanie połączenia z klastrem lub niepowodzenie jego zwinięcia. Jest to bardzo proste, zapomniane lub zaniedbanie rotacji certyfikatu przypiętego odcisku palca, a tym samym jest trudne odzyskiwanie z takiej sytuacji.

W tym celu właściciel klastra może jawnie stwierdzać, że certyfikaty z podpisem własnym zadeklarowane za pomocą odcisku palca są uznawane za prawidłowe, w następujący sposób:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Takie zachowanie nie obejmuje certyfikatów wystawionych przez urząd certyfikacji. w takim przypadku certyfikat odwołany, znany jako uznany za naruszony może stać się "prawidłowy", gdy tylko nie będzie się znajdować na liście odwołania certyfikatów urzędu certyfikacji i w ten sposób stanowić zagrożenie bezpieczeństwa. W przypadku certyfikatów z podpisem własnym właściciel klastra jest traktowany jako jedyną osobę odpowiedzialną za ochronę klucza prywatnego certyfikatu, który nie jest używany w przypadku certyfikatów wystawionych przez urzędy certyfikacji — właściciel klastra może nie wiedzieć, jak lub kiedy certyfikat został zadeklarowany jako naruszony.

#### <a name="common-name-based-certificate-validation-declarations"></a>Wspólne deklaracje walidacji certyfikatu oparte na nazwach
Wspólne deklaracje oparte na nazwach przyjmują jedną z następujących form:
- Nazwa pospolita podmiotu (tylko)
- Nazwa pospolita podmiotu z przypinaniem wystawcy

Pozwól nam najpierw rozważyć wyciąg z manifestu klastra exemplifying oba style deklaracji:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklaracje odnoszą się odpowiednio do tożsamości serwera i klastra; Należy zauważyć, że deklaracje oparte na CN mają własne sekcje w manifeście klastra, niezależnie od standardowego "zabezpieczenia". W obu deklaracjach, "name" reprezentuje wspólną nazwę podmiotu w certyfikacie, a pole "value" reprezentuje oczekiwanego wystawcy w następujący sposób:

- w pierwszym przypadku deklaracja stwierdza, że element nazwa pospolita podmiotu certyfikatu serwera powinien pasować do ciągu "server.demo.system. servicefabric. Azure-int"; puste pole "value" wskazuje, że katalog główny łańcucha certyfikatów jest zaufany w węźle/komputerze, na którym jest sprawdzany certyfikat serwera; w systemie Windows oznacza to, że certyfikat może być powiązany z dowolnym certyfikatem zainstalowanym w magazynie "zaufany główny urząd certyfikacji".
- w drugim przypadku deklaracja stwierdza, że prezenter certyfikatu jest akceptowany jako węzeł równorzędny w klastrze, jeśli nazwa pospolita certyfikatu jest zgodna z ciągiem "cluster.demo.system. servicefabric. Azure-int" *i* odcisk palca bezpośredniego wystawcy certyfikatu jest zgodny z jednym z wpisów oddzielonych przecinkami w polu "wartość". (Ten typ reguły jest colloquially znany jako "nazwa pospolita z przypinaniem wystawcy".)

W obu przypadkach łańcuch certyfikatu jest zbudowany i oczekuje się, że jest on bez błędów; oznacza to, że błędy odwołania, łańcuch częściowy lub nieprawidłowe Błędy zaufania są uznawane za krytyczne i sprawdzanie poprawności certyfikatu zakończy się niepowodzeniem. Przypinanie wystawców spowoduje rozważenie stanu niezaufanego elementu głównego jako błędu niekrytycznego; Pomimo wyglądu jest to ściślejsza forma weryfikacji, ponieważ umożliwia właścicielowi klastra ograniczenie zestawu autoryzowanych/akceptowanych wystawców do własnej infrastruktury kluczy publicznych.

Po skompilowaniu łańcucha certyfikatów zostanie on sprawdzony pod kątem standardowych zasad protokołu TLS/SSL z zadeklarowanym podmiotem jako nazwa zdalna; certyfikat będzie uznawany za dopasowanie, jeśli jego wspólna nazwa podmiotu lub jakakolwiek z nazw alternatywnych podmiotu jest zgodna z deklaracją CN z manifestu klastra. W tym przypadku symbole wieloznaczne są obsługiwane, a w przypadku dopasowania ciągu nie jest rozróżniana wielkość liter.

(Należy wyjaśnić, że sekwencja opisana powyżej może zostać wykonana dla każdego typu użycia klucza zadeklarowanego w certyfikacie; Jeśli certyfikat określa użycie klucza uwierzytelniania klienta, łańcuch zostanie skompilowany i oceniony jako pierwszy dla roli klienta. W razie sukcesu Ocena zakończy się pomyślnie i sprawdzanie poprawności zakończy się powodzeniem. Jeśli certyfikat nie ma użycia uwierzytelniania klienta lub Walidacja nie powiodła się, środowisko uruchomieniowe Service Fabric będzie kompilować i oszacować łańcuch do uwierzytelniania serwera).

Aby ukończyć ten przykład, Poniższy fragment ilustruje deklarowanie certyfikatów klienta według nazwy pospolitej:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Powyższe deklaracje odpowiadają odpowiednio tożsamościom administratora i użytkownika; Sprawdzanie poprawności certyfikatów zadeklarowanych w ten sposób jest dokładnie opisane w poprzednich przykładach w przypadku certyfikatów klastra i serwera.

> [!NOTE]
> Wspólne deklaracje oparte na nazwach są przeznaczone do uproszczenia rotacji, a ogólnie zarządzania certyfikatami klastra. Zaleca się jednak przestrzeganie następujących zaleceń w celu zapewnienia ciągłej dostępności i bezpieczeństwa klastra:
  * Preferuj Przypinanie wystawcy, aby polegać na zaufanych korzeniach
  * Unikaj mieszania wystawcy z różnych infrastruktur kluczy publicznych
  * Upewnij się, że wszystkie oczekiwane wystawcy są wymienione w deklaracji certyfikatu; niezgodność wystawcy spowoduje niepowodzenie weryfikacji
  * Upewnij się, że punkty końcowe zasad certyfikatów infrastruktury kluczy publicznych są wykrywalne, dostępne i dostępne — oznacza to, że punkty końcowe AIA, listy CRL lub protokołu OCSP są deklarowane w certyfikacie liścia i są dostępne, aby można było ukończyć Kompilowanie łańcucha certyfikatów.

Powiązane ze sobą, po odebraniu żądania połączenia w klastrze zabezpieczonym za pomocą certyfikatów X. 509, środowisko uruchomieniowe Service Fabric będzie używać ustawień zabezpieczeń klastra do weryfikowania poświadczeń strony zdalnej zgodnie z opisem powyżej; Jeśli to się powiedzie, jednostka wywołująca/zdalna jest uważana za uwierzytelnioną. Jeśli poświadczenie jest zgodne z wieloma regułami walidacji, środowisko uruchomieniowe przydzieli obiektowi wywołującemu najwyższe uprzywilejowane role dowolnych zgodnych reguł. 

### <a name="presentation-rules"></a>Reguły prezentacji
W poprzedniej sekcji opisano, jak uwierzytelnianie działa w klastrze zabezpieczonym certyfikatem; w tej sekcji wyjaśniono, jak samo środowisko uruchomieniowe Service Fabric odnajduje i ładuje certyfikaty, których używa do komunikacji w klastrze; Nazywamy te reguły "prezentacja".

Podobnie jak w przypadku reguł walidacji, reguły prezentacji określają rolę i skojarzoną deklarację poświadczeń, wyrażoną przez odcisk palca lub nazwę pospolitą. W przeciwieństwie do reguł walidacji, wspólne deklaracje oparte na nazwach nie mają przepisów dotyczących przypinania wystawcy. zapewnia to większą elastyczność i lepszą wydajność. Reguły prezentacji są deklarowane w sekcjach "NodeType" manifestu klastra dla każdego typu odrębnego węzła; ustawienia są podzielone z sekcji zabezpieczeń klastra, aby zezwolić na każdy typ węzła na pełną konfigurację w jednej sekcji. W klastrach usługi Azure Service Fabric, węzeł typu węzła domyślnie określa odpowiednie ustawienia w sekcji Zabezpieczenia definicji klastra.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Deklaracje prezentacji certyfikatu opartego na odcisku palca
Jak opisano wcześniej, środowisko uruchomieniowe Service Fabric rozróżnia rolę równorzędną innych węzłów w klastrze oraz jako serwer operacji zarządzania klastrem. W zasadzie te ustawienia można skonfigurować odrębnie, ale w przypadku, w którym mają one być wyrównane. W pozostałej części tego artykułu przyjęto, że ustawienia są zgodne dla uproszczenia.

Rozważmy następujący fragment z manifestu klastra:
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
Element "ClusterCertificate" demonstruje pełny schemat, w tym parametry opcjonalne ("X509FindValueSecondary") lub te z odpowiednimi wartościami domyślnymi ("X509StoreName"); inne deklaracje pokazują Skrócony formularz. Deklaracja certyfikatu klastra powyżej wskazuje, że ustawienia zabezpieczeń węzłów typu "nt1vm" są inicjowane z certyfikatem "cc71. 1984 ' jako podstawowa i "49e2... certyfikat 19d6 jako pomocniczy; oczekuje się, że oba certyfikaty znajdują się w \' magazynie certyfikatów LocalMachine my "(lub w równoważnej ścieżce systemu Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Wspólne deklaracje prezentacji certyfikatu opartego na nazwach
Typ węzła certyfikaty można także zadeklarować według nazwy pospolitej podmiotu, jak exemplified poniżej:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Dla każdego typu deklaracji węzeł Service Fabric odczytuje konfigurację podczas uruchamiania, lokalizuje i ładuje określone certyfikaty i sortuje je w kolejności malejącej według ich atrybutu NotAfter; wygasłe certyfikaty są ignorowane, a pierwszy element listy jest wybierany jako poświadczenie klienta dla dowolnych połączeń Service Fabricych podejmowanych przez ten węzeł. (W efekcie Service Fabric preferuje najdalej certyfikat wygasający).

Należy pamiętać, że w przypadku deklaracji prezentacji opartych na typowych nazwach certyfikat jest uznawany za dopasowanie, jeśli nazwa pospolita podmiotu jest równa wartości pola X509FindValue (lub X509FindValueSecondary) deklaracji jako uwzględniającego wielkość liter, dokładne porównanie ciągów. Jest to w przeciwieństwie do reguł walidacji, które obsługują Dopasowywanie symboli wieloznacznych, a także Porównywanie ciągów bez uwzględniania wielkości liter.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Różne ustawienia konfiguracji certyfikatu
Wspomniano wcześniej, że ustawienia zabezpieczeń klastra Service Fabric umożliwiają również zachowanie niewielkich zmian w zachowaniu kodu uwierzytelniania. Artykuł w [Service Fabric ustawieniach klastra](service-fabric-cluster-fabric-settings.md) przedstawia kompleksową i najbardziej aktualną listę ustawień, dlatego na podstawie wybranych tutaj kilku ustawień zabezpieczeń rozwiniemy w celu pełnego udostępnienia uwierzytelniania opartego na certyfikatach. Dla każdego ustawienia wyjaśnimy zamierzenie, wartość domyślną/zachowanie, jak ma to wpływ na uwierzytelnianie i jakie wartości są akceptowalne.

Jak wspomniano wcześniej, sprawdzanie poprawności certyfikatu zawsze implikuje Kompilowanie i ocenianie łańcucha certyfikatu. W przypadku certyfikatów wystawionych przez urząd certyfikacji to pozornie proste wywołanie interfejsu API systemu operacyjnego powoduje zwykle kilka wywołań wychodzących do różnych punktów końcowych wystawiającej infrastrukturę PKI, buforowanie odpowiedzi i tak dalej. W przypadku występowania wywołań weryfikacji certyfikatów w klastrze Service Fabric wszystkie problemy z punktami końcowymi infrastruktury PKI mogą spowodować zredukowanie dostępności klastra lub niewłaściwego podziału. Nie można pominąć wywołań wychodzących (zobacz poniżej w sekcji często zadawane pytania, aby uzyskać więcej informacji na ten temat). można użyć następujących ustawień w celu zamaskowania błędów walidacji spowodowanych błędami wywołań listy CRL.

  * CrlCheckingFlag — w sekcji "zabezpieczenia" ciąg przekonwertowany na UINT. Wartość tego ustawienia jest używana przez Service Fabric do maskowania błędów stanu łańcucha certyfikatów przez zmianę zachowania tworzenia łańcucha; jest ona przenoszona do wywołania interfejsu [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) systemu Win32 jako parametr "flagiDW" i może być ustawiona na dowolną prawidłową kombinację flag akceptowanych przez funkcję. Wartość 0 wymusza ignorowanie wszelkich błędów stanu zaufania przez środowisko uruchomieniowe Service Fabric. nie jest to zalecane, ponieważ jego użycie stanowiłoby znaczące narażenie na bezpieczeństwo. Wartość domyślna to 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Kiedy używać: do testowania lokalnego z certyfikatami z podpisem własnym lub certyfikatów deweloperów, które nie są w pełni uformowane/nie mają właściwej infrastruktury kluczy publicznych do obsługi certyfikatów. Mogą również używać jako ograniczenia w środowiskach gapped powietrze podczas przejścia między infrastruktur kluczy publicznych.

  Jak używać: zajmiemy się przykładem, aby wymusić sprawdzenie odwołania w celu uzyskania dostępu tylko do buforowanych adresów URL. Łożono
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  następnie deklaracja w manifeście klastra:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError — w sekcji "Security" wartość logiczna z wartością domyślną "false". Reprezentuje skrót służący do pomijania stanu błędu tworzenia łańcucha "odwoływania w trybie offline" (lub kolejnego stanu błędu walidacji zasad łańcucha).

  Kiedy używać: testowanie lokalne lub z certyfikatami deweloperskimi, które nie są obsługiwane przez właściwą infrastrukturę PKI. Używaj jako środków zaradczych w środowiskach gapped Air lub gdy infrastruktura PKI jest niedostępna.

  Jak używać:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Inne istotne ustawienia (wszystkie w sekcji "zabezpieczenia"):
  * AcceptExpiredPinnedClusterCertificate — omówione w sekcji przeznaczonej dla weryfikacji certyfikatu opartego na odcisku palca; zezwala na akceptowanie wygasłych certyfikatów klastra z podpisem własnym. 
  * CertificateExpirySafetyMargin — interwał wyrażony w minutach przed sygnaturą czasową NotAfter certyfikatu i w trakcie którego certyfikat jest traktowany jako zagrożony dla wygaśnięcia. Service Fabric monitoruje certyfikaty klastra i okresowo emituje raporty kondycji na ich pozostałej dostępności. W interwale "bezpieczeństwo" te raporty kondycji są podniesione do stanu "ostrzeżenie". Wartość domyślna to 30 dni.
  * CertificateHealthReportingInterval — określa częstotliwość raportów kondycji dotyczących pozostałego czasu ważności certyfikatów klastra. Raporty będą emitowane tylko raz dla tego interwału. Wartość jest wyrażona w sekundach, a domyślna to 8 godzin.
  * EnforcePrevalidationOnSecurityChanges — wartość logiczna steruje zachowaniem uaktualniania klastra podczas wykrywania zmian ustawień zabezpieczeń. Jeśli ustawiono wartość "true", uaktualnienie klastra podejmie próbę upewnienia się, że co najmniej jeden certyfikat pasujący do dowolnej z reguł prezentacji może przekazać odpowiednią regułę walidacji. Przed zainstalowaniem nowych ustawień do dowolnego węzła zostanie wykonane wstępne sprawdzanie poprawności, ale jest ono uruchamiane tylko w węźle hostującym podstawową replikę usługi Cluster Manager w momencie inicjowania uaktualnienia. W przypadku tego zapisu ustawienie ma wartość "false" i zostanie ustawione na wartość "true" dla nowych klastrów usługi Azure Service Fabric z wersją środowiska uruchomieniowego rozpoczynającą się od 7,1.
 
### <a name="end-to-end-scenario-examples"></a>Kompleksowy scenariusz (przykłady)
Zostały wyszukane reguły prezentacji, reguły walidacji i dostosowywanie flag, ale w jaki sposób wszystko to działa razem? W tej sekcji przeprowadzimy dwie kompleksowe przykłady pokazujące, w jaki sposób można wykorzystać ustawienia zabezpieczeń w celu zapewnienia bezpiecznego uaktualniania klastra. Należy pamiętać, że nie jest to kompleksowe Dissertation na temat właściwego zarządzania certyfikatami w Service Fabric, poszukaj w tym temacie artykułu towarzyszącego.

Rozdzielenie reguł prezentacji i walidacji stanowi oczywiste pytanie (lub istotność), czy mogą się rozbieżne i jakie konsekwencje byłyby. Istnieje również możliwość, że wybór węzła certyfikatu uwierzytelniania nie spowoduje przekazania reguł walidacji innego węzła. W rzeczywistości ta rozbieżność stanowi główną przyczynę incydentów związanych z uwierzytelnianiem. W tym samym czasie oddzielenie tych reguł pozwala klastrowi kontynuować działanie podczas uaktualniania, co powoduje zmianę ustawień zabezpieczeń klastra. Należy wziąć pod uwagę, że dzięki rozszerzeniu pierwszej reguły sprawdzania poprawności do pierwszego kroku wszystkie węzły klastra zostaną zbieżne w nowych ustawieniach przy zachowaniu bieżących poświadczeń. 

Należy przypomnieć, że w klastrze Service Fabric postęp uaktualniania (do 5) "uaktualnienie domen" lub "do". Tylko węzły w bieżącym UD są uaktualniane/zmieniane w danym momencie, a uaktualnienie przejdzie do kolejnego UD tylko wtedy, gdy będzie to możliwe. (Zobacz [aktualizacje klastrów Service Fabric](service-fabric-cluster-upgrade.md) i inne artykuły w tym samym temacie, aby uzyskać więcej informacji.) Zmiany certyfikatu/zabezpieczeń są szczególnie ryzykowne, ponieważ mogą izolować węzły z klastra lub pozostawić klaster na granicy utraty kworum.

Użyjemy następującej notacji do opisania ustawień zabezpieczeń węzła:

Te: {P:{TP = A}, V:{TP = A}}, gdzie:
  - "Te" reprezentuje węzeł w domenie uaktualnienia *k*
  - "P" reprezentuje bieżące reguły prezentacji węzła (przy założeniu, że odwołuje się tylko do certyfikatów klastra); 
  - "V" reprezentuje bieżące reguły walidacji węzła (tylko certyfikat klastra)
  - "TP = A" reprezentuje deklarację opartą na odcisku palca (TP) z "A", która jest odciskiem palca certyfikatu
  - "CN = B" reprezentuje wspólną deklarację opartą na nazwach (CN), przy czym "B" jest nazwą pospolitą podmiotu certyfikatu 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Obracanie certyfikatu klastra zadeklarowanego przez odcisk palca
Poniższa sekwencja opisuje, jak można użyć uaktualnienia 2-etapowego, aby bezpiecznie wprowadzić pomocniczy certyfikat klastra, zadeklarowany przez odcisk palca; Pierwsza faza wprowadza nową deklarację certyfikatu w regułach walidacji, a druga faza wprowadza ją w regułach prezentacji:
  - stan początkowy: N0 = {P:{TP = A}, V:{TP = A}},... Te = {P:{TP = A}, V:{TP = A}} — klaster jest w stanie spoczynku, wszystkie węzły mają wspólną konfigurację
  - Po zakończeniu uaktualniania domeny 0: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Te = {P:{TP = A}, V:{TP = A}} — węzły w UD0 będą zawierać certyfikat A i akceptują certyfikaty A lub B; wszystkie inne węzły obecne i akceptują tylko certyfikat
  - Po zakończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Te = {P:{TP = A}, V:{TP = A, TP = B}} — wszystkie węzły obecne są certyfikat A, wszystkie węzły akceptują certyfikat A lub B
      
W tym momencie klaster jest ponownie w równowadze, a druga faza uaktualnienia/zmiany ustawień zabezpieczeń może rozpocząć się:
  - Po zakończeniu uaktualniania domeny 0: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Te = {P:{TP = A}, V:{TP = A, TP = B}}-węzły w UD0 rozpoczną prezentowanie B, który jest akceptowany przez każdy inny węzeł w klastrze.
  - Po zakończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Te = {P:{TP = A, TP = B}, V:{TP = A, TP = B}} — wszystkie węzły zostały przełączone w celu przesłania certyfikatu B. certyfikat A można teraz wycofać/usunąć z definicji klastra przy użyciu kolejnego zestawu uaktualnień.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Konwertowanie klastra z odcisku palca do deklaracji certyfikatów opartych na nazwie
Podobnie zmiana typu deklaracji certyfikatu (od odcisku palca do nazwy pospolitej) będzie zgodna z tym samym wzorcem, jak powyżej. Należy pamiętać, że reguły sprawdzania poprawności umożliwiają deklarowanie certyfikatów danej roli przez odcisk palca i nazwę pospolitą w tej samej definicji klastra. Z kolei, Chociaż reguły prezentacji zezwalają tylko na jedną formę deklaracji. W przypadku incydentu bezpieczne podejście do konwertowania certyfikatu klastra z odciskiem palca na nazwę pospolitą polega na wprowadzeniu zamierzonego certyfikatu docelowego najpierw według odcisku palca, a następnie zmiany tej deklaracji na nazwę pospolitą opartą na nazwie. W poniższym przykładzie przyjęto założenie, że odcisk palca "A" i wspólna nazwa podmiotu "B" odwołują się do tego samego certyfikatu. 

  - stan początkowy: N0 = {P:{TP = A}, V:{TP = A}},... Te = {P:{TP = A}, V:{TP = A}} — klaster jest w stanie spoczynku, wszystkie węzły mają wspólną konfigurację z odciskiem palca certyfikatu podstawowego
  - Po zakończeniu uaktualniania domeny 0: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Te = {P:{TP = A}, V:{TP = A}} — węzły w UD0 będą zawierać certyfikat A i akceptują certyfikaty z odciskiem palca A lub pospolitą nazwą B; wszystkie inne węzły obecne i akceptują tylko certyfikat
  - Po zakończeniu ostatniej domeny uaktualnienia: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Te = {P:{TP = A}, V:{TP = A, CN = B}} — wszystkie węzły obecne certyfikatu A, wszystkie węzły akceptują certyfikat A (TP) lub B (CN)

W tym momencie możemy kontynuować Zmienianie reguł prezentacji przy kolejnym uaktualnieniu:
  - Po zakończeniu uaktualniania domeny 0: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Te = {P:{TP = A}, V:{TP = A, CN = B}} — węzły w UD0 będą prezentować certyfikat B znaleziony przez CN i akceptują certyfikaty z odciskiem palca A lub nazwa pospolita B; wszystkie inne węzły są obecne i akceptują tylko certyfikaty wybrane przez odcisk palca
  - Po zakończeniu ostatniej domeny uaktualnienia: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Te = {P:{CN = B}, V:{TP = A, CN = B}} — wszystkie węzły są obecne certyfikat B znaleziony przez CN, wszystkie węzły akceptują certyfikat A (TP) lub B (CN)
    
Zakończenie fazy 2 oznacza także konwersję klastra do typowych certyfikatów opartych na nazwach. deklaracje weryfikacji na podstawie odcisku palca można usunąć podczas kolejnego uaktualniania klastra.

> [!NOTE]
> W klastrach usługi Azure Service Fabric wymienione powyżej przepływy pracy są zorganizowane przez dostawcę zasobów Service Fabric; Właściciel klastra nadal jest odpowiedzialny za Inicjowanie obsługi certyfikatów w klastrze zgodnie ze wskazanymi regułami (prezentacją lub walidacją) i zachęca się do wykonywania zmian w wielu krokach.

W osobnym artykule omówiono zarządzanie certyfikatami i inicjowanie ich obsługi w klastrze Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Rozwiązywanie problemów i często zadawane pytania
Debugowanie problemów związanych z uwierzytelnianiem w klastrach Service Fabric nie jest proste, dlatego hopeful następujące wskazówki i porady mogą pomóc. Najprostszym sposobem rozpoczęcia badania jest zbadanie Service Fabric dzienników zdarzeń w węzłach klastra — niekoniecznie są to tylko te, które pokazują objawy, ale również węzły, które są, ale nie mogą połączyć się z jednym z ich sąsiadów. W systemie Windows zdarzenia o znaczeniu są zwykle rejestrowane odpowiednio w kanałach "Applications and Services Logs\Microsoft-ServiceFabric\Admin" lub "działa". Czasami może być pomocne [włączenie rejestrowania CAPI2](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), aby przechwycić więcej szczegółów dotyczących sprawdzania poprawności certyfikatu, pobierania list CRL/CTL itd. (należy pamiętać, aby wyłączyć go po zakończeniu odtwórzu, może być całkiem pełne).

Typowe objawy polegające na tym, że w klastrze występują problemy z uwierzytelnianiem: 
  - węzły są wyłączone/cykliczne 
  - próby połączenia zostały odrzucone
  - Przekroczono limit czasu próby połączenia

Każdy ze objawów może być spowodowany przez różne problemy, a ta sama główna przyczyna może pokazywać różne manifesty; w związku z tym zostanie wystawiona tylko niewielki przykład typowych problemów z zaleceniami dotyczącymi ich rozwiązywania. 

* Węzły mogą wymieniać komunikaty, ale nie mogą się łączyć. Możliwa przyczyna przerwania połączenia to błąd "certyfikat niezgodny" — jedna ze stron w ramach połączeń Service Fabric-Service Fabrica przedstawia certyfikat, który nie powoduje wykonania reguł walidacji odbiorcy. Może towarzyszyć jeden z następujących błędów: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Aby zdiagnozować/zbadać więcej: w każdym z węzłów próbujących nawiązać połączenie Sprawdź, który certyfikat jest prezentowany; Sprawdź certyfikat i wypróbuj i Emuluj reguły walidacji (Sprawdź odciski palca lub nazwa pospolita równości, sprawdź odciski palców wystawcy, jeśli zostały określone).

  Inny typowy towarzyszący kod błędu może być następujący:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  W takim przypadku certyfikat jest zadeklarowany za pomocą nazwy pospolitej i stosuje jedną z następujących czynności:
    - emitenci nie są przypięti, a certyfikat główny nie jest zaufany lub
    - wystawcy są przypięte, ale deklaracja nie zawiera odcisku palca bezpośredniego wystawcy tego certyfikatu

* Węzeł jest w górę, ale nie może połączyć się z innymi węzłami; inne węzły nie odbierają ruchu przychodzącego z węzła, który się nie powiodło. W takim przypadku istnieje możliwość, że ładowanie certyfikatu w węźle lokalnym nie powiedzie się. Wyszukaj następujące błędy:
  - nie znaleziono certyfikatu — upewnij się, że certyfikaty zadeklarowane w regułach prezentacji mogą być rozpoznawane przez zawartość magazynu certyfikatów LocalMachine\My (lub określony). 
    Możliwe przyczyny niepowodzenia: 
      - nieprawidłowe znaki w deklaracji odcisku palca
      - certyfikat nie jest zainstalowany
      - certyfikat wygasł
      - Deklaracja Common Name zawiera prefiks "CN =".
      - Deklaracja określa symbol wieloznaczny i nie istnieje dokładne dopasowanie w magazynie certyfikatów (Deklaracja: CN = *. moja domena. com, rzeczywisty certyfikat: CN = Server. moja domena. com)

  - nieznane poświadczenia — wskazuje, że brakuje klucza prywatnego odpowiadającego certyfikatowi, zazwyczaj towarzyszy mu kod błędu: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Aby rozwiązać ten fakt, sprawdź istnienie klucza prywatnego; Sprawdź, czy SFAdmins udzielono dostępu "Odczyt | wykonywanie" do klucza prywatnego.

  - zły typ dostawcy — wskazuje certyfikat kryptografii nowej generacji (CNG) ("Dostawca magazynu kluczy oprogramowania firmy Microsoft"); w tej chwili Service Fabric obsługuje tylko certyfikaty CAPI1. Zwykle towarzyszy kod błędu:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Aby rozwiązać ten certyfikat, należy utworzyć go ponownie przy użyciu CAPI1 (np. "Microsoft Enhanced RSA And AES Cryptographic Provider"). Aby uzyskać więcej informacji na temat dostawców usług kryptograficznych, zobacz [opis dostawców usług kryptograficznych](/windows/win32/seccertenroll/understanding-cryptographic-providers) .
