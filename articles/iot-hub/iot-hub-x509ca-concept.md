---
title: Pojęcia dotyczące zabezpieczeń platformy Azure IoT Hub X. 509 | Microsoft Docs
description: Koncepcja — Omówienie certyfikatów urzędu certyfikacji X. 509 w procesie produkcji urządzeń IoT i uwierzytelniania.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: c707f6108c73a268bcac18c45afb70ae17185bb8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308116"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Omówienie pojęć związanych z certyfikatami urzędu certyfikacji X. 509 w branży IoT

W tym artykule opisano wartość przy użyciu certyfikatów programu X. 509 urzędu certyfikacji w obszarze produkcja i uwierzytelnianie urządzeń IoT w celu IoT Hub. Zawiera informacje o konfiguracji łańcucha dostaw i korzyściach z wyróżniania.

W tym artykule opisano:

* Jakie certyfikaty urzędu certyfikacji X. 509 są i jak je pobrać

* Jak zarejestrować certyfikat certyfikatu X. 509 urzędu certyfikacji w celu IoT Hub

* Jak skonfigurować łańcuch dostaw produkcyjnych dla uwierzytelniania opartego na urzędzie certyfikacji X. 509

* Jak urządzenia podpisane przy użyciu urzędu certyfikacji X. 509 łączą się z IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie przez urząd certyfikacji X. 509 jest podejściem do uwierzytelniania urządzeń w IoT Hub przy użyciu metody, która znacznie upraszcza tworzenie tożsamości urządzeń i zarządzanie cyklem życia w łańcuchu dostaw.

Atrybut odróżniający uwierzytelniania urzędu certyfikacji X. 509 jest relacją "jeden do wielu", a certyfikat urzędu certyfikacji ma urządzenia podrzędne. Ta relacja umożliwia rejestrację dowolnej liczby urządzeń w IoT Hub przez rejestrację certyfikatu X. 509 urzędu certyfikacji. w przeciwnym razie unikatowe certyfikaty urządzeń muszą być wstępnie zarejestrowane dla każdego urządzenia, aby można było nawiązać połączenie z urządzeniem. Ta relacja jeden do wielu upraszcza również operacje zarządzania cyklem życia certyfikatów urządzeń.

Innym ważnym atrybutem uwierzytelniania urzędu certyfikacji X. 509 jest uproszczenie logistyki łańcucha dostaw. Bezpieczne uwierzytelnianie urządzeń wymaga, aby każde urządzenie posiada unikatowy klucz tajny, taki jak klucz jako podstawa zaufania. W przypadku uwierzytelniania opartego na certyfikatach ten klucz tajny jest kluczem prywatnym. Typowy przepływ produkcji urządzenia obejmuje wiele kroków i powierników. Bezpieczne zarządzanie kluczami prywatnymi urządzeń w wielu powiernikach i utrzymywanie zaufania jest trudne i kosztowne. Korzystanie z urzędów certyfikacji rozwiązuje ten problem przez podpisywanie każdego elementu podrzędnego do kryptograficznego łańcucha zaufania, a nie przy użyciu kluczy prywatnych urządzeń. Każdy powiernik z kolei umożliwia znakowanie urządzeń w odpowiednim kroku procesu przepływu produkcji. Ogólny wynik to optymalny łańcuch dostaw z wbudowaną odpowiedzialnością za korzystanie z kryptograficznego łańcucha zaufania. Warto zauważyć, że ten proces daje największe zabezpieczenia, gdy urządzenia chronią swoje unikatowe klucze prywatne. W tym celu zachęcamy do używania sprzętowych modułów zabezpieczeń (HSM), które umożliwiają wewnętrznie generowanie kluczy prywatnych, które nigdy nie będą widoczne.

Ten artykuł zawiera kompleksowy widok użycia uwierzytelniania urzędu certyfikacji X. 509 z konfiguracji łańcucha dostaw do połączenia urządzenia, przy jednoczesnym użyciu rzeczywistego przykładu do poznania zwiększyć.

## <a name="introduction"></a>Wprowadzenie

Certyfikat X. 509 urzędu certyfikacji jest certyfikatem cyfrowym, którego posiadacz może podpisać inne certyfikaty. Ten certyfikat cyfrowy to X. 509, ponieważ jest zgodny ze standardem formatowania certyfikatu zalecanym przez specyfikację RFC 5280 w standardzie IETF i jest urzędem certyfikacji, ponieważ jego posiadacz może podpisać inne certyfikaty.

Korzystanie z urzędu certyfikacji X. 509 jest najlepiej zrozumiałe w odniesieniu do konkretnego przykładu. Rozważmy firmę-X, producenta inteligentnych elementów widget, które są przeznaczone do instalacji profesjonalnej. Firma-X websources (produkcja i instalacja). Producent kontraktów IT w celu wyprodukowania inteligentnych-X-widget i Service Provider technik-Z do zainstalowania. Firma-X chce, aby element widget Smart-X był bezpośrednio dostarczany z fabryki do Z programu Factory-Z do pracy z firmą Z w ramach instalacji i łączył się bezpośrednio z wystąpieniem IoT Hub firmy-X, po zakończeniu instalacji bez dalszej interwencji z firmy-X. Aby to nastąpić, firma-X musi wykonać kilka jednorazowych operacji konfiguracyjnych, aby utworzyć element widget Smart-X-dla automatycznego połączenia. Z kompleksowym scenariuszem pozostała część tego artykułu jest następująca:

* Pozyskiwanie certyfikatu urzędu certyfikacji X. 509

* Zarejestruj certyfikat certyfikatu X. 509 urzędu certyfikacji, aby IoT Hub

* Podpisywanie urządzeń do łańcucha zaufania

* Połączenie z urządzeniem

## <a name="acquire-the-x509-ca-certificate"></a>Pozyskiwanie certyfikatu urzędu certyfikacji X. 509

Firma-X ma możliwość zakupu certyfikatu z urzędu certyfikacji X. 509 z publicznego głównego urzędu certyfikacji lub utworzenia go za pośrednictwem procesu z podpisem własnym. Jedna opcja jest optymalna dla innych, w zależności od scenariusza aplikacji. Niezależnie od opcji, proces obejmuje dwa podstawowe kroki, generując parę kluczy publiczny/prywatny i podpisując klucz publiczny w certyfikacie.

![Przepływ do generowania certyfikatów X509CA](./media/iot-hub-x509ca-concept/csr-flow.png)

Szczegółowe informacje o tym, jak wykonać te kroki, różnią się w zależności od różnych dostawców usług.

### <a name="purchasing-an-x509-ca-certificate"></a>Zakup certyfikatu X. 509 urzędu certyfikacji

Zakup certyfikatu urzędu certyfikacji ma zalety posiadania dobrze znanego głównego urzędu certyfikacji jako zaufanej strony trzeciej w celu zagwarantowania autentyczności urządzeń IoT, gdy urządzenia nawiązują połączenie. Firma-X wybierze tę opcję, jeśli zamierzasz użyć widżetu inteligentnego X, aby móc korzystać z produktów lub usług innych firm po nawiązaniu początkowego połączenia z IoT Hub.

Aby kupić certyfikat urzędu certyfikacji X. 509, firma-X wybierz dostawcę usług certyfikatów głównych. Wyszukiwanie w Internecie dla frazy "główny urząd certyfikacji" spowoduje zwrócenie prawidłowych potencjalnych klientów. Główny urząd certyfikacji poprowadzi firmę-X na temat tworzenia pary kluczy publicznych/prywatnych oraz sposobu generowania żądania podpisania certyfikatu (CSR) dla swoich usług. CSR to formalny proces stosowania certyfikatu z urzędu certyfikacji. Wynikiem tego zakupu jest certyfikat używany jako certyfikat urzędu certyfikacji. Mając powszechność certyfikatów X. 509, certyfikat jest prawdopodobnie poprawnie sformatowany w standardzie RFC 5280 grupy IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Tworzenie certyfikatu certyfikacji X. 509 z podpisem własnym

Proces tworzenia certyfikatu z podpisem własnym X. 509 urzędu certyfikacji jest podobny do kupowania, z wyjątkiem tego, że osoba podpisująca inną firmę, jak główny urząd certyfikacji. W naszym przykładzie firma-X podpisuje swój certyfikat urzędu zamiast głównego urzędu certyfikacji. Firma-X może wybrać tę opcję do testowania, dopóki nie będą gotowi do zakupu certyfikatu urzędu certyfikacji. Firma-X może również używać certyfikatu urzędu certyfikacji X. 509 z podpisem własnym w środowisku produkcyjnym, jeśli element widget inteligentny-X nie jest przeznaczony do łączenia się z innymi usługami innych firm poza IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Zarejestruj certyfikat X. 509 w celu IoT Hub

Firma-X musi zarejestrować urząd certyfikacji X. 509, aby IoT Hub, w którym będzie on obsługiwał uwierzytelnianie inteligentnych elementów widget. Jest to jednorazowy proces, który umożliwia uwierzytelnianie dowolnej liczby urządzeń typu "Smart-X" i zarządzanie nią. Ten proces jest jednorazowy ze względu na relację jeden-do-wielu między certyfikatem i urządzeniami urzędu, a także stanowi jedną z głównych zalet korzystania z metody uwierzytelniania urzędu certyfikacji X. 509. Alternatywą jest przekazywanie odcisków palców poszczególnych certyfikatów dla każdego i każdego urządzenia inteligentnego, co pozwala na dodawanie do kosztów operacyjnych.

Zarejestrowanie certyfikatu urzędu certyfikacji X. 509 jest procesem dwuetapowym, przekazywaniem certyfikatu i dowodem posiadania certyfikatu.

![Rejestrowanie certyfikatu X509CA](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>Przekazywanie certyfikatu w urzędzie certyfikacji X. 509

Proces przekazywania certyfikatu X. 509 urzędu certyfikacji polega na tym, że Przekaż certyfikat urzędu certyfikacji do IoT Hub. IoT Hub oczekuje certyfikatu w pliku. Firma-X po prostu przekazuje plik certyfikatu. W żadnym wypadku plik certyfikatu nie może zawierać żadnych kluczy prywatnych. Najlepsze rozwiązania z zakresu standardów związanych z infrastrukturą kluczy publicznych (PKI) mają na celu założenia, że informacje o prywatnej firmie X w tym przypadku znajdują się wyłącznie w ramach firmy-X.

### <a name="proof-of-possession-of-the-certificate"></a>Potwierdzenie posiadania certyfikatu

Certyfikat certyfikatu X. 509 urzędu certyfikacji, podobnie jak każdy certyfikat cyfrowy, to informacje publiczne, które są podatne na podsłuchiwanie. W związku z tym osoba przechwytująca może przechwycić certyfikat i spróbować go przekazać jako własny. W naszym przykładzie IoT Hub chcemy upewnić się, że certyfikat urzędu certyfikacji firma-X jest przekazywany w rzeczywistości do firmy-X. Robi to przez wyzwanie firmy-X, aby udowodnić, że w rzeczywistości posiadają certyfikat poprzez [przepływ dowodu posiadania (pop)](https://tools.ietf.org/html/rfc5280#section-3.1). Przepływ dowodu posiadania wiąże się z IoT Hub wygenerowaniem losowej liczby do podpisania przez firmę-X przy użyciu jego klucza prywatnego. Jeśli spełnione są najlepsze rozwiązania związane z infrastrukturą kluczy publicznych w firmie X, a klucz prywatny jest chroniony, tylko w celu poprawnego reagowania na wyzwanie dowodu posiadania. IoT Hub przechodzi do rejestru urzędu certyfikacji X. 509 po pomyślnej reakcji na wyzwanie dowodu posiadania.

Pomyślna odpowiedź na wyzwanie związane z dowodem posiadania z IoT Hub kończy rejestrację urzędu certyfikacji X. 509.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Podpisywanie urządzeń do łańcucha zaufania

IoT wymaga, aby każde urządzenie miało unikatową tożsamość. Te tożsamości znajdują się w formularzach certyfikaty dla schematów uwierzytelniania opartych na certyfikatach. W naszym przykładzie oznacza to, że każdy widżet inteligentny-X musi dysponować unikatowym certyfikatem urządzenia. W jaki sposób Instalator firmy-X dla tego elementu jest w łańcuchu dostaw?

Jednym ze sposobów, aby to zrobić, jest wstępne generowanie certyfikatów dla inteligentnych elementów widget i powierzenie znajomości odpowiednich kluczy prywatnych urządzeń z partnerami łańcucha dostaw. W przypadku firm-X oznacza to powierzenie fabryki-Y i technika-Z. Chociaż jest to prawidłowa Metoda, wiąże się to z wyzwaniami, które należy przezwyciężyć, aby zapewnić zaufanie w następujący sposób:

1. Konieczność udostępniania kluczy prywatnych urządzeń z partnerami łańcucha dostaw oprócz ignorowania najlepszych praktyk infrastruktury PKI, które nigdy nie współdzielą kluczy prywatnych, sprawia, że Tworzenie zaufania w łańcuchu dostaw jest kosztowne. Oznacza to, że systemy kapitałowe, takie jak bezpieczne pokoje do kluczy prywatnych urządzenia, i procesy, takie jak okresowe inspekcje zabezpieczeń, muszą być zainstalowane. Dodaj koszt do łańcucha dostaw.

2. Bezpieczne ewidencjonowanie urządzeń w łańcuchu dostaw i zarządzanie nimi w ramach wdrożenia to zadanie jedno-do-jednego dla każdej pary klucz-urządzenie od punktu unikatowego certyfikatu urządzenia (w związku z tym klucza prywatnego) do wycofania urządzenia. Uniemożliwia to zarządzanie grupami urządzeń, chyba że koncepcja grup jest wyraźnie wbudowana w proces. Bezpieczeństwo i zarządzanie cyklem życia urządzeń w tym przypadku jest dużym obciążeniem operacji. W naszym przykładzie firma-X ponosi ten ciężar.

Uwierzytelnianie certyfikatu X. 509 urzędu certyfikacji oferuje eleganckie rozwiązania umożliwiające aforee wyzwań przy użyciu łańcuchów certyfikatów. Łańcuch certyfikatów wynika z podpisywania urzędu certyfikacji pośredniego urzędu certyfikacji, który z kolei podpisuje inny pośredni urząd certyfikacji i tak dalej, aż do ostatniego pośredniego urzędu certyfikacji podpisującego urządzenie. W naszym przykładzie firma-X oznakuje fabrykę-Y, która z kolei jest oznaką technika Z, która oznacza, że jest to element widget inteligentny-X.

![Hierarchia łańcucha certyfikatów](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Powyższe kaskadowe certyfikaty w łańcuchu przedstawiają wartość logiczną urzędu certyfikacji. Wiele łańcuchów dostaw jest zgodnych z tą logiczną informacją o tym, że każdy pośredni urząd certyfikacji jest zalogowany do łańcucha podczas uzyskiwania wszystkich nadrzędnych certyfikatów urzędu certyfikacji, a ostatni pośredni urząd certyfikacji rejestruje każde urządzenie i wstrzyknąć wszystkie certyfikaty urzędu z łańcucha do urządzenia. Jest to typowe w przypadku, gdy firma produkcyjna kontraktu z hierarchią fabryk przeprodukuje daną fabrykę do produkcji. Hierarchia może mieć wiele poziomów głębokości (na przykład według lokalizacji typu Geografia/produkt/linia produkcyjna), tylko fabryka na końcu uzyskuje dostęp do urządzenia, ale łańcuch jest utrzymywany w górnej części hierarchii.

Alternatywne łańcuchy mogą korzystać z różnych pośrednich urzędów certyfikacji z urządzeniem, na którym wystąpiła praca z urządzeniem przez urząd certyfikacji w tym momencie. Modele hybrydowe są również dostępne w przypadku, gdy tylko część urzędu certyfikacji ma fizyczne interakcje z urządzeniem.

W naszym przykładzie zarówno fabryka-Y, jak i technika-Z są współpracujące z widżetem Smart-X. Gdy element Company-x jest właścicielem inteligentnych-X-widget, faktycznie nie działa on fizycznie w całym łańcuchu dostaw. Łańcuch certyfikatów zaufania dla widżetu inteligentnego X składa się z tego względu jako producenta podpisywania X firmy-Y, która z kolei ma charakter technika, który następnie będzie dostarczać końcowy podpis do inteligentnych elementów widget-X. Tworzenie i instalacja widżetu inteligentnego X obejmuje fabryki-Y i technikę Z przy użyciu odpowiednich certyfikatów pośrednich urzędów certyfikacji do podpisywania każdego i każdego elementu widget inteligentnych-X. Wynik tego całego procesu to inteligentne-X — widżety z unikatowymi certyfikatami urządzeń i łańcuchem certyfikatów zaufania do certyfikatu urzędu certyfikacji firmy-X.

![Łańcuch zaufania z certyfikatów jednej firmy do certyfikatów innej firmy](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Jest to dobry punkt, aby przejrzeć wartość metody urzędu certyfikacji X. 509. Zamiast wstępnie generować i wyłączać certyfikaty dla każdego widżetu inteligentnego X w łańcuchu dostaw, firma-X musi tylko raz podpisać fabrykę-Y. Zamiast śledzić każde urządzenie w całym cyklu życia urządzenia, firma-X może teraz śledzić urządzenia i zarządzać nimi za pośrednictwem grup, które w sposób naturalny pochodzą z procesu łańcucha dostaw.

Ostatnia, ale nie najmniejsza, metoda uwierzytelniania urzędu certyfikacji nie umożliwia bezpiecznej odpowiedzialności w łańcuchu dostaw produkcyjnych urządzenia. Ze względu na proces łańcucha certyfikatów działania każdego elementu członkowskiego w łańcuchu są kryptograficznie rejestrowane i możliwe do zweryfikowania.

Ten proces polega na pewnych założeń, które muszą być wyznaczone do kompletności. Wymaga niezależnego utworzenia unikatowej pary kluczy publiczny/prywatny i że klucz prywatny ma być chroniony na urządzeniu. Na szczęście należy zabezpieczyć mikroukłady krzemu w postaci bezpiecznych modułów sprzętowych (HSM), które mogą wewnętrznie generować klucze i chronić klucze prywatne. Firma-X należy dodać jeden z tych mikroukładów do składowych komponentów typu Smart-X-widget.

## <a name="device-connection"></a>Połączenie z urządzeniem

Poprzednie sekcje powyżej zostały skompilowane do połączenia urządzenia. Po prostu rejestrując certyfikat dla urzędu certyfikacji X. 509 do IoT Hub jeden raz, jak potencjalnie miliony urządzeń nawiązują połączenie i uwierzytelniają się po raz pierwszy?  Ułatwia za pomocą tego samego przekazanie certyfikatu i przepływu potwierdzania, który został wcześniej osiągnięty podczas rejestrowania certyfikatu X. 509 urzędu certyfikacji.

Urządzenia wytworzone na potrzeby uwierzytelniania urzędu certyfikacji X. 509 są wyposażone w unikatowe certyfikaty urządzeń i łańcuch certyfikatów z odpowiedniego łańcucha dostaw produkcji. Połączenie urządzenia, nawet po raz pierwszy, odbywa się w procesie dwuetapowym: przekazywanie łańcucha certyfikatów i potwierdzenie jego posiadania.

Podczas przekazywania łańcucha certyfikatów urządzenie przekazuje jego unikatowy certyfikat wraz z łańcuchem certyfikatów zainstalowanym w ramach tego programu do IoT Hub. Przy użyciu wstępnie zarejestrowanego certyfikatu w urzędzie certyfikacji X. 509 IoT Hub może przeprowadzić kryptograficzną weryfikację kilku rzeczy, że przekazany łańcuch certyfikatów jest wewnętrznie spójny i że łańcuch pochodzi od prawidłowego właściciela certyfikatu X. 509 urzędu certyfikacji. Po prostu był to proces rejestracji urzędu certyfikacji X. 509, IoT Hub mógłby zainicjować proces reagowania na potwierdzenie nieposiadania, aby upewnić się, że łańcuch i ten certyfikat urządzenia rzeczywiście należy do urządzenia. Robi to przez wygenerowanie losowego wyzwania do podpisania przez urządzenie przy użyciu jego klucza prywatnego do walidacji przez IoT Hub. Wyzwalacze pomyślnej odpowiedzi IoT Hub do zaakceptowania urządzenia jako autentycznego i przyznania połączenia IT.

W naszym przykładzie każdy element inteligentny-X-widget przekaże unikatowy certyfikat urządzenia razem z certyfikatami Factory-Y i technika-Z X. 509 urzędu certyfikacji, a następnie reaguje na wyzwanie dowodu posiadania z IoT Hub.

![Przepływ z jednego certyfikatu do innego, żądanie pop z centrum](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Należy zauważyć, że podstawy zaufania są przechowywane w ochronie kluczy prywatnych, w tym kluczy prywatnych urządzeń. W związku z tym nie można już uzyskać wystarczającej ważności bezpiecznych mikroukładów krzemu w postaci bezpiecznych modułów sprzętowych (HSM) na potrzeby ochrony kluczy prywatnych urządzeń, a także ogólnie najlepszym rozwiązaniem, aby nigdy nie współdzielić żadnych kluczy prywatnych, takich jak jedna fabryka, która powierza innym swojemu kluczowi prywatnemu.
