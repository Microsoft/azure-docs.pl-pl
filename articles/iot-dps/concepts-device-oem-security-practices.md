---
title: Praktyki dotyczące zabezpieczeń dla producentów — usługa Azure IoT Device Provisioning
description: Przeglądy typowych praktyk zabezpieczeń dla producentów OEM i wytwarzających urządzenia, które przygotowują urządzenia do rejestracji w usłudze Azure IoT Device Provisioning Service (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527211"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Praktyki dotyczące zabezpieczeń dla producentów urządzeń usługi Azure IoT
Ponieważ większa producenci wydają urządzenia IoT, warto zidentyfikować wskazówki dotyczące typowych praktyk. W tym artykule przedstawiono podsumowanie zalecanych zasad zabezpieczeń, które należy wziąć pod uwagę podczas produkcji urządzeń do użycia z usługą Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Wybieranie opcji uwierzytelniania urządzeń
> * Instalowanie certyfikatów na urządzeniach IoT
> * Integrowanie moduł TPM (TPM) z procesem produkcyjnym

## <a name="selecting-device-authentication-options"></a>Wybieranie opcji uwierzytelniania urządzeń
Najważniejszym celem każdej miary zabezpieczeń urządzeń IoT jest utworzenie bezpiecznego rozwiązania IoT. Jednak problemy, takie jak ograniczenia sprzętowe, koszt i poziom wiedzy z zakresu bezpieczeństwa, mają wpływ na wybrane opcje. Ponadto Twoje podejście do zabezpieczeń ma wpływ na połączenie urządzeń IoT z chmurą. Chociaż istnieje [kilka elementów zabezpieczeń IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) , które należy wziąć pod uwagę, najważniejszym elementem, który napotyka każdy klient, jest typ uwierzytelniania, który ma być używany. 

Trzy powszechnie używane typy uwierzytelniania to certyfikaty X. 509, moduły zaufanej platformy (TPM) i klucze symetryczne. Chociaż istnieją inne typy uwierzytelniania, większość klientów, którzy tworzą rozwiązania w usłudze Azure IoT, korzystają z jednego z tych trzech typów. W pozostałej części tego artykułu opisano specjaliści i wady dotyczące korzystania z poszczególnych typów uwierzytelniania.

### <a name="x509-certificate"></a>Certyfikat X. 509
Certyfikaty X. 509 to typy tożsamości cyfrowych, których można użyć do uwierzytelniania. Standard certyfikatu X. 509 został opisany w [dokumencie IETF RFC 5280](https://tools.ietf.org/html/rfc5280). W usłudze Azure IoT istnieją dwa sposoby uwierzytelniania certyfikatów:
- Odcisk palca. Algorytm odcisku palca jest uruchamiany w certyfikacie w celu wygenerowania ciągu szesnastkowego. Wygenerowany ciąg jest unikatowym identyfikator dla certyfikatu. 
- Uwierzytelnianie urzędu certyfikacji oparte na pełnym łańcuchu. Łańcuch certyfikatów jest hierarchiczną listą wszystkich certyfikatów wymaganych do uwierzytelnienia certyfikatu jednostki końcowej (EE). Aby uwierzytelnić certyfikat EE, należy uwierzytelnić każdy certyfikat w łańcuchu, łącznie z zaufanym głównym urzędem certyfikacji. 

Specjaliści dla X. 509:
- X. 509 jest najbezpieczniejszym typem uwierzytelniania obsługiwanym w usłudze Azure IoT.
- X. 509 pozwala na wysoki poziom kontroli na potrzeby zarządzania certyfikatami.
- Wielu dostawców jest dostępnych do udostępniania rozwiązań uwierzytelniania opartych na standardzie X. 509.

Wady dla X. 509:
- Wielu klientów może być zależne od dostawców zewnętrznych dla swoich certyfikatów.
- Zarządzanie certyfikatami może być kosztowne i zwiększa całkowity koszt rozwiązania.
- Zarządzanie cyklem życia certyfikatu może być trudne, jeśli logistyka nie jest dobrze przemyślana. 

### <a name="trusted-platform-module-tpm"></a>Moduł TPM
Moduł TPM, znany również jako [ISO/IEC 11889](https://www.iso.org/standard/66510.html), jest standardem do bezpiecznego generowania i przechowywania kluczy kryptograficznych. Moduł TPM odnosi się również do wirtualnego lub fizycznego urządzenia we/wy, które współdziała z modułami, które implementują Standard. Urządzenie TPM może istnieć jako dyskretny sprzęt, zintegrowany sprzęt, moduł oparty na oprogramowaniu układowym lub moduł oparty na oprogramowaniu. 

Istnieją dwie kluczowe różnice między moduły TPM i kluczami symetrycznymi: 
- Moduły TPM mogą również przechowywać certyfikaty X. 509.
- Zaświadczanie modułu TPM w usłudze DPS używa klucza poręczenia modułu TPM (EK) w postaci uwierzytelniania asymetrycznego. Przy użyciu uwierzytelniania asymetrycznego do szyfrowania jest używany klucz publiczny, a do odszyfrowywania jest używany oddzielny klucz prywatny. Natomiast klucze symetryczne używają uwierzytelniania symetrycznego, w którym klucz prywatny jest używany do szyfrowania i odszyfrowywania. 

Specjaliści dla modułów TPM:
- Program moduły TPM jest dołączany do standardowego sprzętu na wielu urządzeniach z systemem Windows z wbudowaną obsługą systemu operacyjnego. 
- Zaświadczanie modułu TPM jest łatwiejsze do zabezpieczenia przed zaświadczeniem klucza symetrycznego opartego na tokenach sygnatury dostępu współdzielonego (SAS).
- Możesz łatwo wygasać i odnawiać oraz przyrzucić poświadczenia urządzenia. Usługa DPS automatycznie przenosi IoT Hub poświadczenia za każdym razem, gdy urządzenie TPM ma do ponownego zainicjowania obsługi administracyjnej.

Wady modułu TPM: 
- Moduły TPM są złożone i mogą być trudne do użycia. 
- Programowanie aplikacji za pomocą moduły TPM jest trudne, chyba że masz fizycznego modułu TPM lub emulatora jakości.
- Może być konieczne przeprojektowanie tablicy urządzenia w celu dołączenia modułu TPM do sprzętu. 
- Jeśli porzucisz PORĘCZenie w module TPM, zniszczy tożsamość modułu TPM i utworzy nowy. Chociaż układ fizyczny pozostaje taki sam, ma nową tożsamość w rozwiązaniu IoT.

### <a name="symmetric-key"></a>Klucz symetryczny
W przypadku kluczy symetrycznych ten sam klucz jest używany do szyfrowania i odszyfrowywania wiadomości. W związku z tym ten sam klucz jest znany zarówno dla urządzenia, jak i usługi, która uwierzytelnia ją. Usługa Azure IoT obsługuje połączenia z kluczami symetrycznymi opartymi na tokenach SAS. Uwierzytelnianie przy użyciu klucza symetrycznego wymaga znaczącej odpowiedzialności właściciela, aby zabezpieczyć klucze i uzyskać równy poziom zabezpieczeń z uwierzytelnianiem X. 509. W przypadku korzystania z kluczy symetrycznych zaleca się ochronę kluczy przy użyciu sprzętowego modułu zabezpieczeń (HSM).

Specjaliści dla klucza symetrycznego:
- Korzystanie z kluczy symetrycznych jest najprostszym, najtańszym sposobem rozpoczęcia korzystania z uwierzytelniania.
- Używanie kluczy symetrycznych usprawnia proces, ponieważ nie ma żadnych dodatkowych do wygenerowania. 

Wady klucza symetrycznego: 
- Klucze symetryczne mają znaczny stopień wysiłku w celu zabezpieczenia kluczy. Ten sam klucz jest współużytkowany przez urządzenie i chmurę, co oznacza, że klucz musi być chroniony w dwóch miejscach. Z kolei wyzwanie z certyfikatami modułu TPM i X. 509 jest dowodem posiadania klucza publicznego bez ujawniania klucza prywatnego.
- Klucze symetryczne ułatwiają wykonywanie słabych praktyk w zakresie zabezpieczeń. Typową tendencją w przypadku kluczy symetrycznych jest stałe kodowanie nieszyfrowanych kluczy na urządzeniach. Chociaż ta metoda jest wygodna, pozostawia klucze zagrożone. Istnieje możliwość ograniczenia ryzyka przez bezpieczne przechowywanie klucza symetrycznego na urządzeniu. Jeśli jednak priorytet ma ostatecznie zabezpieczenia, a nie wygoda, należy użyć certyfikatów X. 509 lub modułu TPM do uwierzytelniania. 

### <a name="shared-symmetric-key"></a>Współużytkowany klucz symetryczny
Istnieje zróżnicowanie uwierzytelniania klucza symetrycznego znanego jako współużytkowany klucz symetryczny. Takie podejście obejmuje użycie tego samego klucza symetrycznego na wszystkich urządzeniach. Zaleca się unikanie korzystania z udostępnionych kluczy symetrycznych na urządzeniach. 

Pakiet Pro dla współdzielonego klucza symetrycznego:
- Łatwość zaimplementowania i niedrogi do produkcji na dużą skalę. 

Wady dla współdzielonego klucza symetrycznego: 
- Wysoce narażony na ataki. Korzyść z łatwej implementacji jest znacznie niezależna od ryzyka. 
- Każdy może personifikować swoje urządzenia, jeśli uzyska klucz współużytkowany.
- Jeśli korzystasz z udostępnionego klucza symetrycznego, który zostanie złamany, prawdopodobnie utracisz kontrolę nad urządzeniami. 

### <a name="making-the-right-choice-for-your-devices"></a>Wybór właściwego wyboru dla urządzeń
Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę korzyści i koszty każdego podejścia do unikatowego procesu produkcyjnego.  W przypadku uwierzytelniania urządzeń zwykle istnieje relacja odwrotna między tym, jak bezpiecznym podejściem jest i jak wygodne.  

## <a name="installing-certificates-on-iot-devices"></a>Instalowanie certyfikatów na urządzeniach IoT
Jeśli używasz certyfikatów X. 509 do uwierzytelniania urządzeń IoT, ta sekcja zawiera wskazówki dotyczące sposobu integrowania certyfikatów w procesie produkcyjnym. Musisz podjąć kilka decyzji.  Obejmują one decyzje dotyczące typowych zmiennych certyfikatów, podczas generowania certyfikatów i kiedy należy je zainstalować. 

Jeśli używasz hasła, możesz zażądać, aby nie można było używać tego samego certyfikatu we wszystkich urządzeniach, w taki sam sposób, w jaki można używać tego samego hasła na wszystkich urządzeniach. Najpierw użycie tego samego hasła wszędzie jest niebezpieczne. Firma ma narażone firmy na poważne ataki DDoS, w tym te, które podjęły system DNS na wschodnich Stanach Zjednoczonych. Nigdy nie używaj tego samego hasła wszędzie, nawet z kontami osobistymi. Drugim, certyfikat nie jest hasłem, jest to unikatowa tożsamość. Hasło przypomina kod tajny, który może być używany przez każdy z nich do otwierania drzwi w zabezpieczonym budynku.  Jest to coś, co znasz, i możesz nadać każdemu użytkownikowi hasło do uzyskiwania wejścia.  Certyfikat przypomina licencję sterownika ze zdjęciem i innymi szczegółami, które można wyświetlić w celu uzyskania bezpiecznego budynku. Jest ona powiązana z kim.  Pod warunkiem, że funkcja Guard dokładnie dopasowuje się do osób mających licencje sterownika, tylko można użyć licencji (tożsamości), aby uzyskać dostęp. 

### <a name="variables-involved-in-certificate-decisions"></a>Zmienne związane z decyzjami o certyfikacie
Należy wziąć pod uwagę następujące zmienne i jak każdy ma wpływ na cały proces produkcyjny. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Skąd pochodzą certyfikaty główne certyfikatu
Zarządzanie infrastrukturą kluczy publicznych (PKI) może być kosztowne i skomplikowane.  Szczególnie jeśli firma nie ma żadnego środowiska zarządzania infrastrukturą kluczy publicznych. Dostępne opcje to:
- Użyj infrastruktury PKI innej firmy. Pośrednich certyfikatów podpisywania można kupić od dostawcy certyfikatu innej firmy. Można też użyć prywatnego urzędu certyfikacji (CA). 
- Użyj samozarządzanej infrastruktury PKI. Możesz zachować własny system PKI i generować własne certyfikaty.
- Użyj usługi zabezpieczeń [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) . Ta opcja ma zastosowanie tylko do Azure Sphere urządzeń. 

#### <a name="where-certificates-are-stored"></a>Gdzie są przechowywane certyfikaty
Istnieje kilka czynników, które mają wpływ na decyzję w przypadku przechowywania certyfikatów. Te czynniki obejmują typ urządzenia, oczekiwane marginesy zysku (możliwość zapewnienia bezpiecznego magazynu), możliwości urządzeń i istniejącej technologii zabezpieczeń na urządzeniu, które mogą być używane. Należy wziąć pod uwagę następujące opcje:
- W sprzętowym module zabezpieczeń (HSM). Użycie modułu HSM jest zdecydowanie zalecane. Sprawdź, czy na płycie kontroli urządzenia jest już zainstalowany moduł HSM. Jeśli wiesz, że nie masz modułu HSM, skontaktuj się z producentem sprzętu w celu zidentyfikowania modułu HSM, który spełnia Twoje potrzeby.
- W bezpiecznym miejscu na dysku, takim jak środowisko wykonywania zaufanego (TEE).
- W lokalnym systemie plików lub magazynie certyfikatów. Na przykład magazyn certyfikatów systemu Windows. 

#### <a name="connectivity-at-the-factory"></a>Łączność w fabryce
Łączność w fabryce określa, jak i kiedy zostaną pobrane certyfikaty do zainstalowania na urządzeniach. Dostępne są następujące opcje łączności:
- Łączność. Połączenie jest optymalne, usprawnia proces generowania certyfikatów lokalnie. 
- Brak łączności. W takim przypadku należy użyć podpisanego certyfikatu z urzędu certyfikacji, aby generować certyfikaty urządzeń lokalnie i w trybie offline. 
- Brak łączności. W takim przypadku można uzyskać certyfikaty, które zostały wygenerowane wcześniej. Można też użyć infrastruktury PKI w trybie offline do lokalnego wygenerowania certyfikatów.

#### <a name="audit-requirement"></a>Wymagania dotyczące inspekcji
W zależności od typu wytwarzanych urządzeń może istnieć wymóg wymagany do utworzenia dziennika inspekcji, w jaki sposób tożsamości urządzeń są instalowane na urządzeniach. Inspekcja zwiększa znaczący koszt produkcji. Dlatego w większości przypadków tylko wtedy, gdy jest to konieczne. Jeśli nie masz pewności, czy inspekcja jest wymagana, skontaktuj się z działem praw firmy. Opcje inspekcji są następujące: 
- Nie jest wrażliwy na branżę. Inspekcja nie jest wymagana.
- Branża wrażliwa. Certyfikaty należy zainstalować w bezpiecznym pokoju zgodnie z wymaganiami certyfikacji zgodności. Jeśli potrzebujesz bezpiecznego pokoju do instalowania certyfikatów, być może wiesz już, jak certyfikaty są instalowane na urządzeniach. Prawdopodobnie masz już system inspekcji na miejscu. 

#### <a name="length-of-certificate-validity"></a>Długość ważności certyfikatu
Podobnie jak w przypadku licencji sterownika, certyfikaty mają datę wygaśnięcia ustawioną podczas tworzenia. Poniżej przedstawiono opcje długości ważności certyfikatu:
- Odnowienie nie jest wymagane.  To podejście używa długiego okresu odnawiania, więc nigdy nie będzie konieczne odnowienie certyfikatu w okresie istnienia urządzenia. Takie podejście jest wygodne, ale również ryzykowne.  Możesz zmniejszyć ryzyko przy użyciu bezpiecznego magazynu, takiego jak moduł HSM na urządzeniach. Zaleca się jednak unikanie korzystania z certyfikatów długotrwałych.
- Odnowienie jest wymagane.  Należy odnowić certyfikat w okresie istnienia urządzenia. Długość ważności certyfikatu zależy od kontekstu i konieczna jest strategia odnowienia.  Strategia powinna zawierać informacje o tym, gdzie są uzyskiwane certyfikaty, oraz o tym, jaki typ funkcjonalności sieci należy stosować w procesie odnawiania. 

### <a name="when-to-generate-certificates"></a>Kiedy generować certyfikaty
Możliwości łączności z Internetem w fabryce wpłyną na proces generowania certyfikatów. Istnieje kilka opcji generowania certyfikatów: 

- Wstępnie załadowanych certyfikatów.  Niektórzy dostawcy HSM oferują usługę Premium, w której dostawca modułu HSM instaluje certyfikaty dla klienta. Po pierwsze klienci nadają dostawcy HSM dostęp do certyfikatu podpisywania. Następnie dostawca modułu HSM instaluje certyfikaty podpisane przez ten certyfikat podpisywania na każdym module HSM, który kupuje przez klienta. Każdy klient musi zainstalować moduł HSM na urządzeniu. Chociaż ta usługa zwiększa koszty, pomaga usprawnić proces produkcyjny.  I rozwiązuje pytanie o to, kiedy należy zainstalować certyfikaty.
- Certyfikaty wygenerowane przez urządzenie.  Jeśli urządzenia generują wewnętrznie certyfikaty, należy wyodrębnić publiczny certyfikat X. 509 z urządzenia, aby zarejestrować go w usłudze DPS. 
- Połączona fabryka.  Jeśli Twoja fabryka ma łączność, możesz generować certyfikaty urządzeń za każdym razem, gdy ich potrzebujesz.
- Fabryka w trybie offline z własną infrastrukturą PKI. Jeśli fabryka nie ma łączności i używasz własnej infrastruktury kluczy publicznych z obsługą w trybie offline, możesz generować certyfikaty, gdy ich potrzebujesz.
- Fabryka w trybie offline z infrastrukturą PKI innych firm. Jeśli fabryka nie ma łączności i używasz infrastruktury PKI innej firmy, należy wcześniej generować certyfikaty. I będzie konieczne wygenerowanie certyfikatów z lokalizacji, która ma łączność. 

### <a name="when-to-install-certificates"></a>Kiedy instalować certyfikaty
Po wygenerowaniu certyfikatów dla urządzeń IoT można zainstalować je na urządzeniach. 

Jeśli używasz wstępnie załadowanych certyfikatów z modułem HSM, proces jest uproszczony. Po zainstalowaniu modułu HSM na urządzeniu kod urządzenia może uzyskać do niego dostęp. Następnie wywołasz interfejsy API modułu HSM, aby uzyskać dostęp do certyfikatu przechowywanego w module HSM. To podejście jest najwygodniejsze dla procesu produkcyjnego. 

Jeśli nie używasz wstępnie załadowanego certyfikatu, musisz zainstalować certyfikat w ramach procesu produkcyjnego. Najprostszym podejściem jest zainstalowanie certyfikatu w module HSM w tym samym czasie, w którym jest wybłysk początkowy obraz oprogramowania układowego. Proces musi dodać krok w celu zainstalowania obrazu na każdym urządzeniu. Po wykonaniu tego kroku można uruchomić końcowe sprawdzanie jakości i wszystkie inne kroki przed spakowaniem i dostarczeniem urządzenia. 

Dostępne są narzędzia oprogramowania umożliwiające uruchomienie procesu instalacji i ostatecznego sprawdzenia jakości w jednym kroku. Możesz zmodyfikować te narzędzia, aby wygenerować certyfikat lub ściągnąć certyfikat z wstępnie wygenerowanego magazynu certyfikatów. Następnie oprogramowanie może zainstalować certyfikat, który należy zainstalować. Narzędzia oprogramowania tego typu umożliwiają uruchamianie produkcji jakości produkcyjnej w odpowiedniej skali. 

Po zainstalowaniu certyfikatów na urządzeniach następnym krokiem jest zapoznanie się z tematem rejestrowanie urządzeń w usłudze [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrowanie modułu TPM z procesem produkcyjnym
W przypadku uwierzytelniania urządzeń IoT przy użyciu modułu TPM Ta sekcja zawiera wskazówki. Wskazówki dotyczą szeroko używanych urządzeń z modułem TPM 2,0, które mają obsługę klucza kodu uwierzytelniania wiadomości opartego na skrótach (HMAC). Specyfikacja modułu TPM dla układów modułów TPM jest standardem ISO, który jest obsługiwany przez Trusted Computing Group. Aby uzyskać więcej informacji na temat modułu TPM, zobacz specyfikacje dotyczące [modułów tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) i [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Przejmowanie własności modułu TPM
Krytycznym krokiem w wytwarzaniu urządzenia z mikroukładem modułu TPM jest przejęcie własności modułu TPM. Ten krok jest wymagany, aby można było podać klucz dla właściciela urządzenia. Pierwszym krokiem jest wyodrębnienie klucza poręczenia z urządzenia. Następnym krokiem jest faktyczne zastrzeżenie własności. Dowiesz się, jak to zrobić, zależy od tego, który moduł TPM i używany system operacyjny. W razie potrzeby skontaktuj się z producentem modułu TPM lub deweloperem systemu operacyjnego urządzenia, aby określić, jak przejąć własność. 

W procesie produkcyjnym można wyodrębnić PORĘCZę i prawo własności w różnym czasie, co zwiększa elastyczność. Wielu producentów wykorzystuje tę elastyczność poprzez dodanie sprzętowego modułu zabezpieczeń (HSM) w celu zwiększenia bezpieczeństwa urządzeń. Ta sekcja zawiera wskazówki dotyczące tego, kiedy należy wyodrębnić PORĘCZenie, kiedy należy zatwierdzić własność modułu TPM, i zagadnienia związane z integracją tych kroków z osią produkcyjną. 

> [!IMPORTANT]
> W poniższych wskazówkach przyjęto założenie, że używasz dyskretnego, oprogramowania układowego lub zintegrowanego modułu TPM. W miejscach, w których ma zastosowanie, wskazówki uzupełniają uwagi dotyczące korzystania z modułu TPM niedyskretnego lub oprogramowania. W przypadku korzystania z oprogramowania TPM można wykonać dodatkowe czynności, które nie obejmują tego przewodnika. Moduły TPM oprogramowania mają różne implementacje wykraczające poza zakres tego artykułu.  Ogólnie rzecz biorąc, istnieje możliwość integracji modułu TPM z oprogramowaniem na poniższej osi czasu produkcji ogólnej. Jednak chociaż emulowany przez oprogramowanie moduł TPM jest odpowiedni do tworzenia prototypów i testowania, nie może zapewnić tego samego poziomu zabezpieczeń jako dyskretnego, oprogramowania układowego lub zintegrowanego modułu TPM. Ogólnie rzecz biorąc, należy unikać używania oprogramowania TPM w środowisku produkcyjnym.

### <a name="general-manufacturing-timeline"></a>Ogólna oś czasu produkcji
Na poniższej osi czasu pokazano, jak moduł TPM przechodzi przez proces produkcyjny i kończący się na urządzeniu. Każdy proces produkcyjny jest unikatowy, a ta oś czasu pokazuje Najpopularniejsze wzorce. Oś czasu zawiera wskazówki dotyczące sytuacji, w których należy podjąć pewne działania z kluczami. 

#### <a name="step-1-tpm-is-manufactured"></a>Krok 1. wytworzono moduł TPM
- Jeśli kupujesz moduły TPM od producenta do użytku na urządzeniach, zobacz, czy użytkownicy będą wyodrębniać publiczne klucze poręczenia (EK_pubs). Jest to przydatne, jeśli producent zawiera listę EK_pubs z dostarczanymi urządzeniami. 
    > [!NOTE]
    > Można przyznać producentowi modułu TPM dostęp do zapisu do listy rejestracyjnej za pomocą zasad dostępu współdzielonego w usłudze aprowizacji.  Takie podejście umożliwia im dodanie moduły TPM do listy rejestracyjnej.  Ale jest to wczesne w procesie produkcyjnym i wymaga zaufania do producenta modułu TPM. Zrób to na własne ryzyko. 

- Jeśli produkujesz moduły TPM do sprzedawania producentom urządzeń, rozważ podawanie klientom listy EK_pubs wraz z ich fizycznymi moduły tpmami.  Udostępnienie klientom EK_pubsą zapisuje krok w procesie. 
- Jeśli produkujesz moduły TPM do użycia z własnymi urządzeniami, zidentyfikuj, który punkt w procesie jest najwygodniejszy do wyodrębnienia EK_pub. Można wyodrębnić EK_pub w dowolnym z pozostałych punktów na osi czasu. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Krok 2. moduł TPM został zainstalowany na urządzeniu
W tym momencie w procesie produkcyjnym należy wiedzieć, z którego wystąpienia DPS będzie korzystać urządzenie. W związku z tym można dodać urządzenia do listy rejestracji w celu automatycznego aprowizacji. Aby uzyskać więcej informacji o automatycznym aprowizacji urządzeń, zobacz [dokumentację usługi DPS](about-iot-dps.md).
- Jeśli nie wyodrębnisz EK_pub, teraz jest to dobry czas na wykonanie tej czynności. 
- W zależności od procesu instalacji modułu TPM ten krok jest również dobrym terminem do przejęcia własności modułu TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Krok 3. urządzenie ma zainstalowane oprogramowanie układowe i oprogramowanie
W tym momencie procesu należy zainstalować klienta usługi DPS wraz z zakresem identyfikatorów i globalnym adresem URL na potrzeby aprowizacji.
- Teraz Ostatnia szansa na wyodrębnienie EK_pub. Jeśli strona trzecia zainstaluje oprogramowanie na urządzeniu, dobrym pomysłem będzie wyodrębnienie EK_pub.
- Ten punkt w procesie produkcyjnym jest idealnym rozwiązaniem do przejęcia własności modułu TPM.  
    > [!NOTE]
    > Jeśli używasz oprogramowania TPM, możesz go zainstalować teraz.  Wyodrębnij EK_pub w tym samym czasie.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Krok 4. urządzenie jest spakowane i wysyłane do magazynu
Urządzenie może należeć do magazynu przez 6-12 miesięcy przed wdrożeniem. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Krok 5. urządzenie jest zainstalowane w lokalizacji
Po odebraniu urządzenia w jego końcowej lokalizacji odbywa się to przez automatyczne Inicjowanie obsługi przy użyciu funkcji DPS.

Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej](about-iot-dps.md#provisioning-process) i [zaświadczanie modułu TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Zasoby

Oprócz zalecanych zasad dotyczących zabezpieczeń w tym artykule usługa Azure IoT udostępnia zasoby, które ułatwiają wybór bezpiecznego sprzętu i tworzenie bezpiecznych wdrożeń IoT: 
- [Zalecenia dotyczące zabezpieczeń](../iot-fundamentals/security-recommendations.md) usługi Azure IoT umożliwiające zaplanowanie procesu wdrażania. 
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) oferuje usługę ułatwiającą tworzenie bezpiecznych wdrożeń IoT. 
- Aby uzyskać pomoc dotyczącą oceny środowiska sprzętowego, zobacz oficjalny dokument dotyczący [oceny zabezpieczeń IoT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Aby uzyskać pomoc dotyczącą wybierania bezpiecznego sprzętu, zobacz [właściwy bezpieczny sprzęt dla wdrożenia IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 