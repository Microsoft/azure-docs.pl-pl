---
title: AMQP 1,0 Azure Service Bus i Event Hubs Przewodnik po protokole | Microsoft Docs
description: Przewodnik po protokole do wyrażeń i opisów AMQP 1,0 w Azure Service Bus i Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2154221ebfe69b659ff83100ed614133e178ccdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624493"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1,0 Azure Service Bus i Event Hubs Przewodnik po protokole

Protokół Advanced Message Queueing Protocol 1,0 to ustandaryzowany protokół obsługi ramek i transferu w celu asynchronicznego, bezpiecznego i niezawodnego przesyłania komunikatów między dwiema stronami. Jest to podstawowy protokół Azure Service Bus Messaging i Azure Event Hubs.  

AMQP 1,0 to wynik szerokiej współpracy branżowej, która współpracowała z dostawcami oprogramowania pośredniczącego, takimi jak firma Microsoft i Red Hat, z wieloma użytkownikami oprogramowania pośredniczącego do obsługi komunikatów, takimi jak JP Morgan Chase reprezentujący branżę usług finansowych. Forum normalizacyjne techniczne dla protokołów AMQP i specyfikacji rozszerzeń jest języka Oasis i uzyskało formalne zatwierdzenie jako standard międzynarodowy jako ISO/IEC 19494:2014. 

## <a name="goals"></a>Cele

W tym artykule podsumowano podstawowe pojęcia specyfikacji obsługi komunikatów w systemie AMQP 1,0 wraz ze specyfikacjami rozszerzeń opracowanymi przez [Komitet techniczny języka Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) i wyjaśniono, w jaki sposób Azure Service Bus implementuje i tworzy na podstawie tych specyfikacji.

Celem jest dla każdego dewelopera korzystającego z dowolnego istniejącego stosu klienta AMQP 1,0 na dowolnej platformie, aby możliwe było współdziałanie z Azure Service Bus za pośrednictwem AMQP 1,0.

Typowe stosy AMQP 1,0, takie jak [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) lub [AMQP.NET Lite](https://github.com/Azure/amqpnetlite), implementują wszystkie podstawowe elementy protokołu AMQP 1,0, takie jak sesje lub łącza. Te elementy podstawowe są czasami opakowane przy użyciu interfejsu API wyższego poziomu. Program Apache Proton oferuje dwa, bezwzględny interfejs API programu Messenger i reaktywny interfejs API reaktora.

W poniższej dyskusji przyjęto założenie, że zarządzanie połączeniami AMQP, sesjami i łączami oraz obsługą transferów ramek i sterowania przepływem jest obsługiwane przez odpowiedni stos (na przykład Apache Proton-C) i nie wymaga dużej uwagi od deweloperów aplikacji. Częściowo zakładamy istnienie kilku prymitywów interfejsu API, takich jak możliwość nawiązywania połączenia, oraz do tworzenia niektórych postaci obiektów abstrakcji *nadawcy* i *odbiorników* , które mają `send()` odpowiednio kształt i `receive()` operacje.

W przypadku korzystania z zaawansowanych możliwości Azure Service Bus, takich jak przeglądanie komunikatów lub zarządzanie sesjami, te funkcje są wyjaśnione w AMQPe, ale również jako w warstwowych implementacjach interfejsów API.

## <a name="what-is-amqp"></a>Co to jest AMQP?

AMQP to protokół ramek i transferu. Ramka oznacza, że zapewnia strukturę strumieni danych binarnych, które są przesyłane w dowolnym kierunku połączenia sieciowego. Struktura zawiera rozróżnienie dla różnych bloków danych, nazywanych *ramkami*, do wymiany między połączonymi stronami. Możliwości transferu zapewniają, że obie strony komunikują się, że obie osoby komunikujące się w nich mogą ustalić, kiedy ramki są transferowane, a transfery są uznawane za kompletne.

W przeciwieństwie do wcześniej wygasłych wersji roboczych utworzonych przez AMQP grupę roboczą, które są nadal używane przez kilku brokerów komunikatów, ostateczny i ustandaryzowany protokół AMQP 1,0 nie określa obecności brokera komunikatów ani żadnej określonej topologii dla jednostek w brokerze komunikatów.

Protokół ten może służyć do symetrycznej komunikacji równorzędnej w celu interakcji z brokerami komunikatów obsługującymi kolejki i jednostki publikowania/subskrybowania, jak Azure Service Bus. Może również służyć do interakcji z infrastrukturą obsługi komunikatów, w której wzorce interakcji różnią się od zwykłych kolejek, tak jak w przypadku Event Hubs platformy Azure. Centrum zdarzeń działa jak kolejka, gdy do niej są wysyłane zdarzenia, ale działa podobnie jak usługa magazynu szeregowego, gdy zdarzenia są odczytywane. jest nieco podobna do stacji taśm. Klient wybiera przesunięcie w dostępnym strumieniu danych, a następnie obsługuje wszystkie zdarzenia z tego przesunięcia do najnowszej dostępnej wersji.

Protokół AMQP 1,0 został zaprojektowany tak, aby był rozszerzalny, umożliwiając dalsze specyfikacje, aby zwiększyć jego możliwości. Trzy specyfikacje rozszerzeń omówione w tym dokumencie ilustrują to. W przypadku komunikacji za pośrednictwem istniejącej infrastruktury HTTPS/WebSockets Konfigurowanie natywnych portów AMQP TCP może być trudne. Specyfikacja powiązania definiuje, jak warstwa AMQP przez obiekty WebSockets. W celu interakcji z infrastrukturą obsługi komunikatów w celach związanych z zarządzaniem lub w celu zapewnienia zaawansowanych funkcji Specyfikacja AMQP Management definiuje wymagane podstawowe elementy bazowe interakcji. W przypadku integracji z federacyjnym modelem autoryzacji AMQP Specyfikacja zabezpieczeń oparta na oświadczeniach definiuje sposób kojarzenia i odnawiania tokenów autoryzacji skojarzonych z linkami.

## <a name="basic-amqp-scenarios"></a>Podstawowe scenariusze AMQP

W tej sekcji objaśniono podstawowe użycie AMQP 1,0 z Azure Service Bus, które obejmują tworzenie połączeń, sesji i linków oraz przenoszenie komunikatów do i z jednostek Service Bus, takich jak kolejki, tematy i subskrypcje.

Najbardziej autorytatywne źródło informacji o tym, jak działa AMQP, jest [specyfikacją AMQP 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html), ale specyfikacja została zapisywana w celu precyzyjnego wdrożenia przewodnika, a nie do uczenia się protokołu. Ta sekcja koncentruje się na wprowadzaniu tyle terminologii, ile jest potrzebnych do opisywania, w jaki sposób Service Bus używa AMQP 1,0. Aby zapoznać się z bardziej szczegółowym wprowadzeniem do AMQP, a także szerszej dyskusji na temat AMQP 1,0, możesz przejrzeć [ten kurs wideo][this video course].

### <a name="connections-and-sessions"></a>Połączenia i sesje

AMQP wywołuje *kontenery* programów komunikujących się; zawierają *węzły*, które są komunikującymi się jednostkami w tych kontenerach. Kolejka może być węzłem. AMQP umożliwia obsługę multipleksera, dlatego pojedyncze połączenie może być używane dla wielu ścieżek komunikacji między węzłami. na przykład klient aplikacji może jednocześnie odbierać z jednej kolejki i wysyłać do innej kolejki za pośrednictwem tego samego połączenia sieciowego.

![Diagram przedstawiający sesje i połączenia między kontenerami.][1]

Połączenie sieciowe jest zakotwiczone w kontenerze. Jest inicjowany przez kontener w roli klienta, co powoduje wychodzące połączenie gniazda TCP z kontenerem w roli odbiornika, który nasłuchuje i akceptuje przychodzące połączenia TCP. Uzgadnianie połączenia obejmuje negocjowanie wersji protokołu, deklarowanie lub negocjowanie użycia zabezpieczeń na poziomie transportu (TLS/SSL) oraz uzgadnianie uwierzytelniania i autoryzacji w zakresie połączenia opartym na protokole SASL.

Azure Service Bus wymaga użycia protokołu TLS przez cały czas. Obsługuje ona połączenia za pośrednictwem portu TCP 5671, zgodnie z którym połączenie TCP jest najpierw nałożone przy użyciu protokołu TLS przed wprowadzeniem uzgadniania protokołu AMQP, a także obsługuje połączenia za pośrednictwem portu TCP 5672, dzięki czemu serwer natychmiast oferuje obowiązkowe uaktualnienie połączenia do protokołu TLS przy użyciu modelu zalecanego przez AMQP. Powiązanie AMQP WebSockets tworzy tunel przez port TCP 443, który następnie jest równoważny z połączeniami AMQP 5671.

Po skonfigurowaniu połączenia i protokołu TLS Service Bus oferuje dwie opcje mechanizmu SASL:

* SASL PLAIN jest często używany do przekazywania poświadczeń nazwy użytkownika i hasła do serwera. Service Bus nie ma kont, ale nazwanych [reguł zabezpieczeń dostępu współdzielonego](service-bus-sas.md), które przyznają prawa i są skojarzone z kluczem. Nazwa reguły jest używana jako hasło jako nazwa użytkownika i klucz (jako tekst zakodowany w formacie base64). Prawa skojarzone z wybraną regułą regulują operacje dozwolone w ramach połączenia.
* Protokół SASL jest używany do pomijania autoryzacji SASL, gdy klient chce użyć modelu opartego na oświadczeniach zabezpieczeń (CBS), który został opisany w dalszej części. Po wybraniu tej opcji połączenie klienta może być określane anonimowo przez krótki czas, w którym klient może współdziałać z punktem końcowym CBS, a uzgadnianie CBS musi zostać zakończone.

Po nawiązaniu połączenia transportowego kontenery każdy deklarują maksymalny rozmiar ramki, który chcą obsłużyć, oraz po upływie limitu czasu bezczynności, jeśli połączenie nie zostanie przerwane.

Deklarują również, ile współbieżnych kanałów są obsługiwane. Kanał to jednokierunkowy, wychodząca ścieżka transferu wirtualnego na górze połączenia. Sesja pobiera kanał z każdego połączonego kontenera w celu utworzenia dwukierunkowej ścieżki komunikacji.

Sesje mają model sterowania przepływem oparty na oknie; Po utworzeniu sesji każda ze stron deklaruje, ile ramek jest do zaakceptowania w oknie odbierania. Gdy strony wymieniają ramki, przenoszone ramki wypełniają okno i transfery Zatrzymaj, gdy okno jest zapełnione i dopóki okno nie zostanie zresetowane lub rozwinięte przy użyciu *przepływu performative* (*PERFORMATIVE* to termin AMQP dla gestów poziomu protokołu wymienianych między obiema stronami).

Ten model oparty na oknach jest w przybliżeniu podobny do koncepcji TCP sterowania przepływem opartym na oknach, ale na poziomie sesji w gnieździe. Koncepcja protokołu zezwalająca na wiele współbieżnych sesji istnieje, tak że ruch o wysokim priorytecie może być rushed, a poprzedni ruch z ograniczeniami, na przykład na torze ekspresowym Express.

Azure Service Bus obecnie używa dokładnie jednej sesji dla każdego połączenia. Maksymalny rozmiar ramki Service Bus wynosi 262 144 bajtów (256 – K b) dla Service Bus Standard. Jest 1 048 576 (1 MB) dla Service Bus Premium i Event Hubs. Service Bus nie nakłada żadnych określonych okien ograniczania przepustowości, ale resetuje okno regularnie w ramach sterowania przepływem na poziomie linku (zobacz [następną sekcję](#links)).

Połączenia, kanały i sesje są nieulotne. Jeśli połączenie podstawowe jest zwinięte, należy ponownie nawiązać połączenia, tunel protokołu TLS, kontekst autoryzacji SASL i sesje.

### <a name="amqp-outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących AMQP

Klienci korzystający z połączeń AMQP za pośrednictwem protokołu TCP potrzebują portów 5671 i 5672 do otwarcia w lokalnej zaporze. Wraz z tymi portami może być konieczne otwarcie dodatkowych portów, jeśli funkcja [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) jest włączona. `EnableLinkRedirect` to nowa funkcja obsługi komunikatów, która pomaga w pomijaniu jednego przeskoku podczas odbierania komunikatów, co pomaga zwiększyć przepływność. Klient zacznie komunikować się bezpośrednio z usługą zaplecza za pośrednictwem zakresu portów 104XX, jak pokazano na poniższej ilustracji. 

![Lista portów docelowych][4]

Klient platformy .NET może zakończyć się niepowodzeniem z użyciem gniazda SocketException ("podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu"), jeśli te porty są blokowane przez zaporę. Tę funkcję można wyłączyć przez ustawienie `EnableAmqpLinkRedirect=false` w parametrach połączenia, które wymuszają klientom komunikację z usługą zdalną przez port 5671.


### <a name="links"></a>Linki

AMQP przesyła komunikaty przez linki. Link jest ścieżką komunikacyjną utworzoną za pośrednictwem sesji, która umożliwia przesyłanie komunikatów w jednym kierunku; negocjowanie stanu transferu odbywa się nad łączem i dwukierunkową między połączonymi stronami.

![Zrzut ekranu przedstawiający sesję łączącą połączenie między dwoma kontenerami.][2]

Linki można tworzyć za pomocą kontenera w dowolnym momencie i za pośrednictwem istniejącej sesji, co sprawia, że AMQP różni się od wielu innych protokołów, w tym HTTP i MQTT, gdzie inicjowanie transferów i ścieżki transferu jest wyłącznym uprawnieniem dla strony tworzącej połączenie gniazda.

Kontener inicjujący łączenie prosi o odłączenie kontenera w celu zaakceptowania linku i wybiera rolę nadawcy lub odbiornika. W związku z tym, każdy kontener może inicjować Tworzenie jednokierunkowych lub dwukierunkowe ścieżek komunikacyjnych, z których każdy jest modelem par łączy.

Linki mają nazwę i są skojarzone z węzłami. Jak wspomniano na początku, węzły są komunikującymi się jednostkami wewnątrz kontenera.

W Service Bus węzeł jest bezpośrednio równoważny z kolejką, tematem, subskrypcją lub podkolejką utraconych wiadomości w kolejce lub subskrypcji. Nazwa węzła używana w AMQP to nazwa względna jednostki wewnątrz przestrzeni nazw Service Bus. Jeśli kolejka ma nazwę `myqueue` , to również jej nazwa węzła AMQP. Subskrypcja tematu jest zgodna z Konwencją interfejsu API protokołu HTTP przez sortowanie do kolekcji zasobów "subskrypcje", a tym samym subskrypcja **podrzędna** tematu **zawiera AMQP** nazwa węzła **/subskrypcje/sub**.

Klient łączący jest również wymagany do tworzenia linków przy użyciu nazwy lokalnego węzła. Service Bus nie zawiera opisów tych nazw węzłów i nie interpretuje ich. Stosy klientów z systemem AMQP 1,0 zwykle używają schematu, aby zapewnić, że te nazwy tymczasowych węzłów są unikatowe w zakresie klienta.

### <a name="transfers"></a>Sunięcia

Po nawiązaniu połączenia można przesłać komunikaty za pośrednictwem tego linku. W programie AMQP transfer jest wykonywany z jawnym gestem protokołu ( *transfer* performative), który przenosi komunikat z nadawcy do odbiorcy przez łącze. Przeniesienie jest zakończone, gdy jest "rozliczane", co oznacza, że obie strony ustanowiły wspólne zrozumienie wyniku tego transferu.

![Diagram przedstawiający transfer komunikatów między nadawcą i odbiorcą i dyspozycją, które z niego wynikają.][3]

W najprostszym przypadku nadawca może zdecydować się na wysłanie komunikatów ", które zostały wstępnie rozliczone", co oznacza, że klient nie interesuje wyników, a odbiorca nie poda żadnej opinii na temat wyniku operacji. Ten tryb jest obsługiwany przez Service Bus na poziomie protokołu AMQP, ale nie został ujawniony w żadnym z interfejsów API klienta.

Regularna sytuacja polega na tym, że komunikaty są wysyłane bez rozliczenia, a odbiorca wskazuje akceptację lub odrzucenie przy użyciu performative *dyspozycji* . Odrzucanie występuje, gdy odbiorca nie może zaakceptować komunikatu z jakiegokolwiek powodu, a komunikat o odrzuceniu zawiera informacje o przyczynie, czyli strukturę błędu zdefiniowaną przez AMQP. Jeśli komunikaty są odrzucane z powodu błędów wewnętrznych w Service Bus, usługa zwraca dodatkowe informacje wewnątrz tej struktury, które mogą być używane do udostępniania wskazówek diagnostycznych pracownikowi pomocy technicznej w przypadku zgłoszenia żądań pomocy technicznej. Więcej informacji o błędach można znaleźć w dalszej części artykułu.

Specjalna forma odrzucenia jest stanem *wydania* , który wskazuje, że odbiorca nie ma technicznych obiektów do przekazania, ale również nie interesuje się w rozliczeniu transferu. Taka sytuacja występuje na przykład wtedy, gdy komunikat jest dostarczany do klienta Service Bus, a klient zdecyduje się na "Porzuć" komunikat, ponieważ nie może wykonać pracy w wyniku przetwarzania komunikatu; samo dostarczenie wiadomości nie jest zgodne z błędem. Zmiana tego stanu jest *zmieniona* , co pozwala na wprowadzenie zmian w wiadomości w miarę ich zwolnienia. Ten stan nie jest już używany przez Service Bus.

Specyfikacja AMQP 1,0 definiuje kolejny stan dyspozycji o nazwie *Received*, który szczególnie pomaga obsłużyć odzyskiwanie linków. Odzyskiwanie linków umożliwia przywracanie stanu linku i wszystkich oczekujących dostaw na nowe połączenie i sesję, gdy wcześniejsze połączenie i sesja zostały utracone.

Service Bus nie obsługuje odzyskiwania linków; Jeśli Klient utraci połączenie w celu Service Bus z oczekującym nierozstrzygniętym transferem komunikatów, ten transfer komunikatów zostanie utracony, a klient musi ponownie nawiązać połączenie, nawiązać ponownie połączenie i ponowić próbę przeniesienia.

W związku z tym Service Bus i Event Hubs obsługiwać "co najmniej raz", w przypadku których nadawca może być zapewniony dla wiadomości przechowywanej i zaakceptowanej, ale nie obsługuje transferów "dokładnie raz" na poziomie AMQP, w którym system podejmie próbę odzyskania linku i kontynuuje negocjowanie stanu dostarczania, aby uniknąć duplikowania transferu komunikatów.

Aby skompensować możliwe niezduplikowane komunikaty, Service Bus obsługuje wykrywanie duplikatów jako opcjonalną funkcję w kolejkach i tematach. Wykrywanie duplikatów rejestruje identyfikatory komunikatów wszystkich komunikatów przychodzących w przedziale czasu zdefiniowanym przez użytkownika, a następnie w trybie dyskretnym odrzuca wszystkie komunikaty wysyłane z tymi samymi identyfikatorami komunikatów w tym samym oknie.

### <a name="flow-control"></a>Sterowanie przepływem

Oprócz modelu sterowania przepływem na poziomie sesji, który został wcześniej omówiony, każdy link ma własny model sterowania przepływem. Sterowanie przepływem na poziomie sesji chroni kontener przed zajściem zbyt wielu ramek jednocześnie, sterowanie przepływem na poziomie łącza umieszcza aplikację, która będzie mogła obsługiwać wiele komunikatów, które chcą obsłużyć z linku.

![Zrzut ekranu przedstawiający dziennik z danymi źródłowymi, docelowymi, port źródłowy, port docelowy i nazwa protokołu. W pierwszym wierszu port docelowy 10401 (0x28 A 1) jest opisany w czerni.][4]

W przypadku linku transfery mogą wystąpić tylko wtedy, gdy nadawca dysponuje wystarczającym *środkiem konsolidacji*. Link kredyt jest licznikiem ustawionym przez odbiornik przy użyciu performative *przepływu* , który jest objęty zakresem linku. Gdy nadawca ma przypisany kredyt, próbuje użyć tego kredytu, dostarczając komunikaty. Każde dostarczanie komunikatów zmniejsza pozostałe środki w wysokości łącznej o 1. Gdy zostanie użyte środki łącza, dostawy są zatrzymane.

Gdy Service Bus znajduje się w roli odbiornika, natychmiast udostępnia nadawcy z dużą wysokością kredytu, dzięki czemu komunikaty mogą być wysyłane od razu. Gdy jest używany kredyt łącza, Service Bus sporadycznie wysyła performative przepływu do nadawcy w celu zaktualizowania salda *środków* łącza.

W roli nadawcy Service Bus wysyła wiadomości w celu użycia wszelkich zaległych środków łącza.

Wywołanie "Receive" na poziomie interfejsu API tłumaczy do performative *przepływu* wysyłanego do Service Bus przez klienta i Service Bus zużywa te środki, pobierając pierwszy dostępną, odblokowany komunikat z kolejki, blokując ją i przekazując ją. Jeśli nie ma żadnych komunikatów, które są dostępne do dostarczenia, wszelkie zaległe środki związane z tym konkretną jednostką pozostaną zarejestrowane w kolejności przybycia, a komunikaty są blokowane i przesyłane, gdy staną się dostępne, aby można było korzystać z jakichkolwiek zaległych środków.

Blokada komunikatu jest publikowana, gdy transfer zostanie rozliczony do jednego z Stanów końcowych *przyjętych*, *odrzuconych* lub *wydanych*. Wiadomość zostanie usunięta z Service Bus, gdy stan końcowy zostanie *zaakceptowany*. Pozostanie w Service Bus i zostanie dostarczone do następnego odbiorcy, gdy transfer osiągnie jakikolwiek inny stan. Service Bus automatycznie przenosi komunikat do kolejki utraconych danych jednostki, gdy osiągnie maksymalną liczbę dostaw dozwoloną dla jednostki z powodu powtarzających się odrzuceń lub wydań.

Mimo że interfejsy API Service Bus nie ujawniają bezpośrednio takiej opcji, klient niskiego poziomu protokołu AMQP może użyć modelu kredytowego dla linku, aby przekształcić funkcję "w stylu ściągania" o wystawienie jednej jednostki środków w odniesieniu do każdego żądania odbioru do modelu "push-Style", wydając dużą liczbę kredytów na łącza, a następnie odbierając komunikaty, gdy staną się dostępne bez żadnej dalsze Wypychanie jest obsługiwane za pomocą ustawień właściwości [MessagingFactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) lub [MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) . Jeśli są one inne niż zero, klient AMQP używa go jako łącznego kredytu.

W tym kontekście ważne jest, aby zrozumieć, że zegar czasu wygaśnięcia blokady komunikatu w jednostce jest uruchamiany, gdy komunikat jest pobierany z jednostki, a nie w momencie umieszczenia komunikatu w sieci. Za każdym razem, gdy klient wskaże gotowość do odbierania komunikatów przez wystawienie kredytu łącza, oczekuje się, że będzie ona aktywnie ściągać komunikaty w sieci i być gotowa do ich obsługi. W przeciwnym razie blokada komunikatów mogła wygasnąć, zanim komunikat zostanie nawet dostarczony. Użycie kontroli przepływu w ramach konsolidacji musi bezpośrednio odzwierciedlać natychmiastową gotowość do postępowania z dostępnymi komunikatami wysyłanymi do odbiorcy.

W obszarze Podsumowanie poniższe sekcje zawierają opis schematu przepływu performative podczas różnych interakcji interfejsu API. Każda sekcja zawiera opis innej operacji logicznej. Niektóre z tych interakcji mogą być "opóźnione", co oznacza, że mogą być wykonywane tylko wtedy, gdy jest to wymagane. Utworzenie nadawcy wiadomości może nie spowodować interakcji sieciowej do momentu wysłania lub zażądania pierwszej wiadomości.

Strzałki w poniższej tabeli pokazują kierunek przepływu performative.

#### <a name="create-message-receiver"></a>Utwórz odbiorcę wiadomości

| Klient | Service Bus |
| --- | --- |
| --> dołączyć (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**odbiornik**,<br/>Źródło = {Nazwa jednostki},<br/>Target = {identyfikator łącza klienta}<br/>) |Klient dołącza do jednostki jako odbiornik |
| Odpowiedzi Service Bus dołączania końca linku |< — Dołącz (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**nadawca**,<br/>Źródło = {Nazwa jednostki},<br/>Target = {identyfikator łącza klienta}<br/>) |

#### <a name="create-message-sender"></a>Tworzenie nadawcy wiadomości

| Klient | Service Bus |
| --- | --- |
| --> dołączyć (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**nadawca**,<br/>Źródło = {identyfikator łącza klienta},<br/>Target = {Nazwa jednostki}<br/>) |Brak akcji |
| Brak akcji |< — Dołącz (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**odbiornik**,<br/>Źródło = {identyfikator łącza klienta},<br/>Target = {Nazwa jednostki}<br/>) |

#### <a name="create-message-sender-error"></a>Utwórz nadawcę wiadomości (błąd)

| Klient | Service Bus |
| --- | --- |
| --> dołączyć (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**nadawca**,<br/>Źródło = {identyfikator łącza klienta},<br/>Target = {Nazwa jednostki}<br/>) |Brak akcji |
| Brak akcji |< — Dołącz (<br/>Nazwa = {Nazwa łącza},<br/>uchwyt = {dojście numeryczne},<br/>rola =**odbiornik**,<br/>Źródło = null,<br/>Target = null<br/>)<br/><br/><--Odłącz (<br/>uchwyt = {dojście numeryczne},<br/>zamknięte =**true**,<br/>błąd = {informacje o błędzie}<br/>) |

#### <a name="close-message-receiversender"></a>Zamknij odbiorcę/nadawcę wiadomości

| Klient | Service Bus |
| --- | --- |
| --> odłączania (<br/>uchwyt = {dojście numeryczne},<br/>zamknięte =**true**<br/>) |Brak akcji |
| Brak akcji |<--Odłącz (<br/>uchwyt = {dojście numeryczne},<br/>zamknięte =**true**<br/>) |

#### <a name="send-success"></a>Wyślij (powodzenie)

| Klient | Service Bus |
| --- | --- |
| --> transfer (<br/>Identyfikator dostarczania = {dojście numeryczne},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,, więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |Brak akcji |
| Brak akcji |<--Dyspozycja (<br/>Rola = odbiornik,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>rozliczone =**true**,<br/>stan =**zaakceptowane**<br/>) |

#### <a name="send-error"></a>Wyślij (błąd)

| Klient | Service Bus |
| --- | --- |
| --> transfer (<br/>Identyfikator dostarczania = {dojście numeryczne},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,, więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |Brak akcji |
| Brak akcji |<--Dyspozycja (<br/>Rola = odbiornik,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>rozliczone =**true**,<br/>State =**odrzucone**(<br/>błąd = {informacje o błędzie}<br/>)<br/>) |

#### <a name="receive"></a>Odbieranie

| Klient | Service Bus |
| --- | --- |
| --> Flow (<br/>Link — kredyt = 1<br/>) |Brak akcji |
| Brak akcji |< transfer (<br/>Identyfikator dostarczania = {dojście numeryczne},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,<br/>więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |
| --> Dyspozycja (<br/>rola =**odbiornik**,<br/>First = {Delivery ID},<br/>Last = {Delivery ID},<br/>rozliczone =**true**,<br/>stan =**zaakceptowane**<br/>) |Brak akcji |

#### <a name="multi-message-receive"></a>Odbieranie z obsługą wiadomości

| Klient | Service Bus |
| --- | --- |
| --> Flow (<br/>Link — kredyt = 3<br/>) |Brak akcji |
| Brak akcji |< transfer (<br/>Identyfikator dostarczania = {dojście numeryczne},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,<br/>więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |
| Brak akcji |< transfer (<br/>Identyfikator dostarczania = {dojście numeryczne + 1},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,<br/>więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |
| Brak akcji |< transfer (<br/>Delivery-ID = {dojście numeryczne + 2},<br/>Delivery-tag = {dojście binarne},<br/>rozliczone =**Fałsz**,<br/>więcej =**Fałsz**,<br/>State =**null**,<br/>Wznów =**Fałsz**<br/>) |
| --> Dyspozycja (<br/>Rola = odbiornik,<br/>First = {Delivery ID},<br/>Last = {Delivery ID + 2},<br/>rozliczone =**true**,<br/>stan =**zaakceptowane**<br/>) |Brak akcji |

### <a name="messages"></a>Komunikaty

W poniższych sekcjach wyjaśniono, które właściwości w standardowych sekcjach komunikatów AMQP są używane przez Service Bus i jak są mapowane na Service Bus zestaw interfejsów API.

Każda właściwość, którą aplikacja musi definiować, powinna być mapowana na `application-properties` mapę AMQP.

#### <a name="header"></a>header

| Nazwa pola | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| służąc |- |- |
| priority |- |- |
| czas wygaśnięcia |Czas wygaśnięcia dla tej wiadomości |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| pierwszy — przejmujący |- |- |
| Liczba dostaw |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Nazwa pola | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| Identyfikator komunikatu |Zdefiniowany przez aplikację identyfikator dowolnej postaci dla tego komunikatu. Używany do wykrywania duplikatów. |[Identyfikatora](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Identyfikator użytkownika zdefiniowany przez aplikację, nieinterpretowany przez Service Bus. |Niedostępne za pomocą interfejsu API Service Bus. |
| na wartość |Zdefiniowany przez aplikację identyfikator docelowy, nieinterpretowany przez Service Bus. |[Do](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Identyfikator przeznaczenie komunikatu zdefiniowany przez aplikację, nieinterpretowany przez Service Bus. |[Etykieta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Odpowiedz do |Zdefiniowany przez aplikację wskaźnik ścieżki odpowiedzi, nieinterpretowany przez Service Bus. |[From](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Zdefiniowany przez aplikację identyfikator korelacji, nieinterpretowany przez Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Typ zawartości |Zdefiniowany przez aplikację wskaźnik typu zawartości dla treści, nieinterpretowany przez Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Kodowanie zawartości |Zdefiniowany przez aplikację wskaźnik kodowania zawartości dla treści, nieinterpretowany przez Service Bus. |Niedostępne za pomocą interfejsu API Service Bus. |
| bezwzględny czas wygaśnięcia |Deklaruje, że bezwzględnie utraci komunikat. Zignorowano w danych wejściowych (zaobserwowane jest czas wygaśnięcia nagłówka), autorytatywny dla danych wyjściowych. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| czas utworzenia |Deklaruje, kiedy wiadomość została utworzona. Nieużywane przez Service Bus |Niedostępne za pomocą interfejsu API Service Bus. |
| Identyfikator grupy |Identyfikator zdefiniowany przez aplikację dla powiązanego zestawu komunikatów. Używany do sesji Service Bus. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Sekwencja grup |Licznik identyfikujący względny numer sekwencji komunikatu w ramach sesji. Zignorowane przez Service Bus. |Niedostępne za pomocą interfejsu API Service Bus. |
| Odpowiedz do grupy-ID |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Adnotacje komunikatów

Istnieje kilka innych właściwości komunikatów usługi Service Bus, które nie są częścią właściwości komunikatu AMQP i są przesyłane wraz z `MessageAnnotations` wiadomością.

| Klucz mapowania adnotacji | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| x-opt-planowana-czas w kolejce | Deklaruje, w którym czasie komunikat powinien pojawić się w jednostce |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-Partition-Key | Klucz zdefiniowany przez aplikację wskazujący partycję, w której ma być używany komunikat. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-Via-Partition-Key | Zdefiniowana przez aplikację wartość klucza partycji, gdy transakcja ma być używana do wysyłania wiadomości za pośrednictwem kolejki transferu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-w czasie | Czas UTC zdefiniowany przez usługę reprezentujący rzeczywisty czas umieszczenie komunikatu. Zignorowano przy wejściu. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-Sequence-Number | Unikatowy numer zdefiniowany przez usługę przypisany do wiadomości. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Zdefiniowany przez usługę numer sekwencyjny wiadomości w kolejce. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-Locked-until | Zdefiniowane przez usługę. Data i godzina, do której wiadomość zostanie zablokowana w kolejce/subskrypcji. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-utracono-Źródło | Zdefiniowane przez usługę. Jeśli wiadomość zostanie odebrana z kolejki utraconych wiadomości, Źródło oryginalnej wiadomości. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Możliwość transakcji

Transakcja grupuje razem co najmniej dwie operacje w zakresie wykonania. O charakterze taka transakcja musi zapewnić, że wszystkie operacje należące do danej grupy operacji kończą się powodzeniem lub niepowodzeniem.
Operacje są pogrupowane według identyfikatora `txn-id` .

W przypadku interakcji transakcyjnej klient pełni funkcję `transaction controller` , która kontroluje operacje, które powinny być zgrupowane razem. Usługa Service Bus działa jako `transactional resource` a i wykonuje działania zgodnie z żądaniem `transaction controller` .

Klient i usługa komunikują się za pośrednictwem `control link` , który jest ustanowiony przez klienta. `declare`Komunikaty i `discharge` są wysyłane przez kontroler za pośrednictwem linku sterującego, aby alokować i uzupełniać transakcje odpowiednio (nie reprezentują rozgraniczenia pracy transakcyjnej). Rzeczywiste wysyłanie/odbieranie nie jest wykonywane dla tego linku. Każda żądana operacja transakcyjna jest jawnie zidentyfikowana z odpowiednią `txn-id` i dlatego może wystąpić na dowolnym łączu połączenia. Jeśli łącze kontrolne jest zamknięte, gdy istnieją naliczane transakcje, a następnie wszystkie takie transakcje są natychmiast wycofywane i próby wykonania dalszych operacji transakcyjnych na nich zakończą się niepowodzeniem. Komunikaty na linku kontroli nie mogą być wstępnie rozliczane.

Każde połączenie musi inicjować swój własny link kontroli, aby można było rozpocząć i zakończyć transakcje. Usługa definiuje specjalne miejsce docelowe, które działa jako `coordinator` . Klient/kontroler nawiązuje połączenie z tym obiektem docelowym. Link sterujący znajduje się poza granicą jednostki, to oznacza, że ten sam link kontrolny może służyć do inicjowania i zwalniania transakcji dla wielu jednostek.

#### <a name="starting-a-transaction"></a>Uruchamianie transakcji

, Aby rozpocząć pracę transakcyjną. Kontroler musi uzyskać `txn-id` od koordynatora. Robi to przez wysłanie `declare` komunikatu typu. Jeśli deklaracja zostanie zakończona pomyślnie, koordynator odpowie z wynikiem dyspozycji, który przenosi przypisane `txn-id` .

| Klient (kontroler) | Kierunek | Service Bus (koordynator) |
| :--- | :---: | :--- |
| Klej<br/>Nazwa = {Nazwa łącza},<br/>... ,<br/>rola =**nadawca**,<br/>Target =**koordynator**<br/>) | ------> |  |
|  | <------ | Klej<br/>Nazwa = {Nazwa łącza},<br/>... ,<br/>Target = Coordinator ()<br/>) |
| sunięć<br/>Identyfikator dostarczania = 0,...)<br/>{AmqpValue (**DECLARE ()**)}| ------> |  |
|  | <------ | dyspozycji <br/> pierwsze = 0, ostatnie = 0, <br/>State =**zadeklarowane**(<br/>**transakcja-ID**= {Transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Rozładowywanie transakcji

Kontroler kończy działanie transakcyjne przez wysłanie `discharge` komunikatu do koordynatora. Kontroler wskazuje, że chce zatwierdzić lub wycofać czynności transakcyjne przez ustawienie `fail` flagi w treści zrzutu. Jeśli koordynator nie jest w stanie zakończyć zrzutu, komunikat zostanie odrzucony z wynikiem przeprowadzenia `transaction-error` .

> Uwaga: błąd = true oznacza wycofanie transakcji i niepowodzenie = false oznacza zatwierdzenie.

| Klient (kontroler) | Kierunek | Service Bus (koordynator) |
| :--- | :---: | :--- |
| sunięć<br/>Identyfikator dostarczania = 0,...)<br/>{AmqpValue (DECLARE ())}| ------> |  |
|  | <------ | dyspozycji <br/> pierwsze = 0, ostatnie = 0, <br/>State = zadeklarowane (<br/>transakcja-ID = {Transaction ID}<br/>))|
| | . . . <br/>Działania transakcyjne<br/>na inne linki<br/> . . . |
| sunięć<br/>Identyfikator dostarczania = 57,...)<br/>{ AmqpValue (<br/>**Zrzut (transakcja-ID = 0, <br/> Niepowodzenie = false)**)}| ------> |  |
| | <------ | dyspozycji <br/> pierwsze = 57, ostatnie = 57, <br/>State =**zaakceptowane ()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Wysyłanie komunikatu w transakcji

Wszystkie zadania transakcyjne są wykonywane ze transakcyjnym stanem dostarczania `transactional-state` , który ma identyfikator transakcja. W przypadku wysyłania komunikatów stan transakcyjny jest przenoszony przez ramkę transferu wiadomości. 

| Klient (kontroler) | Kierunek | Service Bus (koordynator) |
| :--- | :---: | :--- |
| sunięć<br/>Identyfikator dostarczania = 0,...)<br/>{AmqpValue (DECLARE ())}| ------> |  |
|  | <------ | dyspozycji <br/> pierwsze = 0, ostatnie = 0, <br/>State = zadeklarowane (<br/>transakcja-ID = {Transaction ID}<br/>))|
| sunięć<br/>dojście = 1,<br/>Identyfikator dostarczania = 1, <br/>**stan = <br/> TransactionalState ( <br/> transakcja-ID = 0)**)<br/>ładunku| ------> |  |
| | <------ | dyspozycji <br/> pierwsze = 1, ostatnie = 1, <br/>State =**TransactionalState ( <br/> transakcja-ID = 0, <br/> wynik = zaakceptowane ()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Likwidowanie wiadomości w transakcji

Dyspozycja komunikatów obejmuje operacje takie jak `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Aby wykonać te operacje w ramach transakcji, należy przekazać ją do `transactional-state` dyspozycji.

| Klient (kontroler) | Kierunek | Service Bus (koordynator) |
| :--- | :---: | :--- |
| sunięć<br/>Identyfikator dostarczania = 0,...)<br/>{AmqpValue (DECLARE ())}| ------> |  |
|  | <------ | dyspozycji <br/> pierwsze = 0, ostatnie = 0, <br/>State = zadeklarowane (<br/>transakcja-ID = {Transaction ID}<br/>))|
| | <------ |sunięć<br/>dojście = 2,<br/>Identyfikator dostarczania = 11, <br/>State = null)<br/>ładunku|  
| dyspozycji <br/> pierwsze = 11, ostatnie = 11, <br/>State =**TransactionalState ( <br/> transakcja-ID = 0, <br/> wynik = zaakceptowane ()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Zaawansowane możliwości Service Bus

W tej części omówiono zaawansowane możliwości Azure Service Bus, które są oparte na rozszerzeniach roboczych do AMQP, obecnie opracowywanych w Komitecie technicznym języka Oasis dla AMQP. Service Bus implementuje najnowsze wersje tych wersji roboczych i przyjmuje zmiany wprowadzone w miarę osiągnięcia stanu wersji roboczej.

> [!NOTE]
> Zaawansowane operacje Service Bus komunikatów są obsługiwane za pomocą wzorca żądania/odpowiedzi. Szczegóły tych operacji są opisane w artykule [AMQP 1,0 w Service Bus: operacje oparte na odpowiedzi na żądanie](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP Management

Specyfikacja AMQP Management to pierwszy z tych rozszerzeń, które zostały omówione w tym artykule. Ta specyfikacja definiuje zestaw protokołów warstwowych na podstawie protokołu AMQP, który pozwala na interakcje zarządzania z infrastrukturą obsługi komunikatów w AMQP. Specyfikacja definiuje operacje ogólne, takie jak *Tworzenie*, *odczytywanie*, *Aktualizowanie* i *usuwanie* w celu zarządzania jednostkami wewnątrz infrastruktury obsługi komunikatów i zestawem operacji zapytań.

Wszystkie te gesty wymagają interakcji żądania/odpowiedzi między klientem i infrastrukturą obsługi komunikatów, dlatego specyfikacja definiuje, jak modelować ten wzorzec interakcji w oparciu o AMQP: klient łączy się z infrastrukturą obsługi komunikatów, inicjuje sesję, a następnie tworzy parę linków. W przypadku jednego linku klient pełni rolę nadawcy, a drugie działa jako odbiornik, tworząc parę łączy, które mogą pełnić rolę kanału dwukierunkowego.

| Operacja logiczna | Klient | Service Bus |
| --- | --- | --- |
| Utwórz ścieżkę odpowiedzi na żądanie |--> dołączyć (<br/>Nazwa = {*Nazwa łącza*},<br/>uchwyt = {*dojście numeryczne*},<br/>rola =**nadawca**,<br/>Źródło =**null**,<br/>Target = "Entity/$management"<br/>) |Brak akcji |
| Utwórz ścieżkę odpowiedzi na żądanie |Brak akcji |\<-- attach(<br/>Nazwa = {*Nazwa łącza*},<br/>uchwyt = {*dojście numeryczne*},<br/>rola =**odbiornik**,<br/>Źródło = null,<br/>Target = "Moja jednostka"<br/>) |
| Utwórz ścieżkę odpowiedzi na żądanie |--> dołączyć (<br/>Nazwa = {*Nazwa łącza*},<br/>uchwyt = {*dojście numeryczne*},<br/>rola =**odbiornik**,<br/>source = "Entity/$management",<br/>Target = "WebClient $ ID"<br/>) | |
| Utwórz ścieżkę odpowiedzi na żądanie |Brak akcji |\<-- attach(<br/>Nazwa = {*Nazwa łącza*},<br/>uchwyt = {*dojście numeryczne*},<br/>rola =**nadawca**,<br/>Źródło = "Moja jednostka",<br/>Target = "WebClient $ ID"<br/>) |

Mając tę parę linków, implementacja żądania/odpowiedzi jest prosta: żądanie jest komunikatem wysyłanym do jednostki wewnątrz infrastruktury obsługi komunikatów, która rozumie ten wzorzec. W tym komunikacie żądania w polu *odpowiedź do* w sekcji *Właściwości* jest ustawiany identyfikator *docelowy* dla łącza, na którym ma być dostarczana odpowiedź. Jednostka obsługi przetwarza żądanie, a następnie dostarcza odpowiedź za pośrednictwem linku, którego identyfikator *docelowy* pasuje do wskazanego identyfikatora *odpowiedzi* .

Wzorzec oczywiście wymaga, aby kontener klienta i identyfikator wygenerowany przez klienta dla miejsca docelowego odpowiedzi były unikatowe dla wszystkich klientów i ze względów bezpieczeństwa trudno było przewidzieć.

Wymiany komunikatów używanych przez protokół zarządzania i dla wszystkich innych protokołów, które używają tego samego wzorca, są wykonywane na poziomie aplikacji; nie definiują one nowych gestów poziomu protokołu AMQP. Jest to zamierzone, dzięki czemu aplikacje mogą korzystać z tych rozszerzeń za pomocą zgodnych stosów AMQP 1,0.

Service Bus obecnie nie implementuje żadnych podstawowych funkcji specyfikacji zarządzania, ale wzorzec żądania/odpowiedzi zdefiniowany przez specyfikację zarządzania jest podstawą dla funkcji zabezpieczeń opartych na oświadczeniach i dla niemal wszystkich zaawansowanych możliwości omówionych w następujących sekcjach:

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

Wersja robocza AMQP oświadczeń-based-Authorization (CBS) jest oparta na wzorcu żądania/odpowiedzi specyfikacji zarządzania i opisuje Uogólniony model, w którym można używać federacyjnych tokenów zabezpieczających z AMQP.

Domyślny model zabezpieczeń AMQP omówiony w wprowadzeniu jest oparty na protokole SASL i integruje się z uzgadnianiem połączenia AMQP. Użycie protokołu SASL ma zalety, aby zapewniać rozszerzalny model, dla którego zdefiniowano zestaw mechanizmów, z których każdy protokół, który formalnie dzieli się na SASL, może skorzystać z zalet. Wśród tych mechanizmów są "zwykłe" do przenoszenia nazw użytkowników i haseł, "zewnętrzne", aby powiązać zabezpieczenia na poziomie protokołu TLS, "ANONIMOWe", co oznacza brak jawnego uwierzytelniania/autoryzacji i wiele różnorodnych dodatkowych mechanizmów zezwalających na przekazywanie poświadczeń uwierzytelniania i/lub tokenów autoryzacji.

Integracja SASL AMQP ma dwie wady:

* Wszystkie poświadczenia i tokeny są objęte zakresem połączenia. Infrastruktura obsługi komunikatów może chcieć zapewnić zróżnicowaną kontrolę dostępu dla poszczególnych jednostek. na przykład umożliwienie okaziciela tokenu do wysłania do kolejki A, ale nie do kolejki B. W przypadku kontekstu autoryzacji zakotwiczonego w połączeniu nie można użyć jednego połączenia i jeszcze użyć różnych tokenów dostępu dla kolejki A i kolejki B.
* Tokeny dostępu są zwykle ważne tylko przez ograniczony czas. To poprawność wymaga, aby użytkownik okresowo ponownie nabywał tokeny, i umożliwia wystawcy token odmowę wydania nowego tokenu, Jeśli zmieniono uprawnienia dostępu użytkownika. Połączenia AMQP mogą być ostatnio przez długi czas. Model SASL pozwala tylko na ustawienie tokenu w czasie połączenia, co oznacza, że infrastruktura obsługi komunikatów musi rozłączyć klienta po wygaśnięciu tokenu lub musi zaakceptować ryzyko zezwolenia na kontynuowanie komunikacji z klientem, do którego prawa dostępu mogą odwołać się w tymczasowym systemie.

Specyfikacja AMQP CBS zaimplementowana przez Service Bus umożliwia eleganckie obejście obu tych problemów: umożliwia klientowi kojarzenie tokenów dostępu z każdym węzłem i aktualizowanie tych tokenów przed ich wygaśnięciem, bez przerywania przepływu komunikatów.

CBS definiuje wirtualny węzeł zarządzania o nazwie *$CBS*, który ma być udostępniany przez infrastrukturę obsługi komunikatów. Węzeł zarządzania akceptuje tokeny w imieniu innych węzłów w infrastrukturze obsługi wiadomości.

Gest protokołu jest wymianą żądania/odpowiedzi zgodnie z definicją w specyfikacji zarządzania. Oznacza to, że klient ustanowi parę linków z węzłem *$CBS* , a następnie przekaże żądanie dotyczące łącza wychodzącego, a następnie czeka na odpowiedź w łączu przychodzącym.

Komunikat żądania ma następujące właściwości aplikacji:

| Klucz | Opcjonalne | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| operation |Nie |ciąg |**Put-token** |
| typ |Nie |ciąg |Typ umieszczanego tokenu. |
| name |Nie |ciąg |"Odbiorcy", do którego ma zastosowanie token. |
| datę |Tak |sygnatura czasowa |Czas wygaśnięcia tokenu. |

Właściwość *name* identyfikuje jednostkę, z którą ma zostać skojarzony token. W Service Bus jest to ścieżka do kolejki lub tematu/subskrypcji. Właściwość *Type* identyfikuje typ tokenu:

| Typ tokenu | Opis tokenu | Typ treści | Uwagi |
| --- | --- | --- | --- |
| AMQP: JWT |Token sieci Web JSON (JWT) |AMQP — wartość (ciąg) |Jeszcze niedostępne. |
| AMQP: SWT |Prosty token sieci Web (SWT) |AMQP — wartość (ciąg) |Obsługiwane tylko dla tokenów SWT wystawionych przez usługę AAD/ACS |
| ServiceBus. Windows. NET: sastoken |Token SAS Service Bus |AMQP — wartość (ciąg) |- |

Tokeny przyznaje prawa. Service Bus wie o trzech podstawowych prawach: "Send" umożliwia wysyłanie, "Listen" włącza otrzymywanie, a "manage" włącza manipulowanie jednostkami. Tokeny SWT wystawiane przez usługę AAD/ACS jawnie zawierają te prawa jako oświadczenia. Tokeny sygnatury dostępu współdzielonego Service Bus odwołują się do reguł skonfigurowanych dla przestrzeni nazw lub jednostek. te reguły są konfigurowane z prawami. Podpisywanie tokenu z kluczem skojarzonym z tą regułą powoduje, że token wyraża odpowiednie prawa. Token skojarzony z jednostką przy użyciu elementu *Put-token* umożliwia połączonemu klientowi współpracującie z jednostką na prawach tokenu. Link, w którym klient przejmuje rolę *nadawcy* , wymaga prawa "Wyślij"; wykonanie operacji "Listen" w ramach roli *odbiornika* wymaga prawa "nasłuchiwanie".

Komunikat odpowiedzi zawiera następujące wartości *właściwości aplikacji*

| Klucz | Opcjonalne | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| stan — kod |Nie |int |Kod odpowiedzi HTTP **[RFC2616]**. |
| stan — opis |Tak |ciąg |Opis stanu. |

Klient może wielokrotnie wywoływać *token Put* i dla każdej jednostki w infrastrukturze obsługi wiadomości. Tokeny są objęte zakresem bieżącego klienta i zakotwiczone w bieżącym połączeniu, co oznacza, że serwer porzuca wszelkie zachowane tokeny, gdy połączenie zostanie odłączone.

Bieżąca implementacja Service Bus zezwala tylko na CBS w połączeniu z metodą SASL "ANONYMOUS". Połączenie SSL/TLS musi zawsze istnieć przed uzgodnieniem SASL.

W związku z tym mechanizm anonimowy musi być obsługiwany przez wybranego klienta AMQP 1,0. Dostęp anonimowy oznacza, że początkowe uzgadnianie połączeń, w tym tworzenie początkowej sesji, odbywa się bez Service Bus znajomości osoby tworzącej połączenie.

Po nawiązaniu połączenia i sesji, dołączeniu linków do węzła *$CBS* i wysłania żądania *Put token* są jedynymi dozwolonymi operacjami. Prawidłowy token musi zostać pomyślnie ustawiony przy użyciu żądania *Put tokenu* dla pewnego węzła jednostki w ciągu 20 sekund od nawiązania połączenia. w przeciwnym razie połączenie zostanie utworzone jednostronnie przez Service Bus.

Klient jest w dalszym ciągu odpowiedzialny za śledzenie wygaśnięcia tokenu. Po wygaśnięciu tokenu Service Bus natychmiast porzuca wszystkie linki połączenia z odpowiednią jednostką. Aby uniknąć wystąpienia problemu, klient może zastąpić token dla węzła nowym, w dowolnym momencie za pomocą węzła Virtual *$CBS* Management za pomocą tego samego gestu *Put token* i bez uzyskiwania informacji o ruchu ładunku, który przepływa na różnych łączach.

### <a name="send-via-functionality"></a>Funkcje wysyłania

[Nadawca Wyślij za pośrednictwem/transfer](service-bus-transactions.md#transfers-and-send-via) to funkcja, która umożliwia usłudze Service Bus przekazywanie danej wiadomości do jednostki docelowej za pośrednictwem innej jednostki. Ta funkcja służy do wykonywania operacji między jednostkami w ramach jednej transakcji.

Za pomocą tej funkcji utworzysz nadawcę i nawiążesz połączenie z `via-entity` . Podczas ustanawiania łącza są przekazywane dodatkowe informacje w celu ustalenia prawdziwej lokalizacji docelowej komunikatów/transferów na tym łączu. Po pomyślnym dołączeniu wszystkie komunikaty wysłane w tym linku są automatycznie przekazywane do *jednostki docelowej* za pośrednictwem *jednostki*. 

> Uwaga: przed ustanowieniem tego linku należy przeprowadzić *uwierzytelnianie zarówno dla jednostki, jak i* *obiektu docelowego* .

| Klient | Kierunek | Service Bus |
| :--- | :---: | :--- |
| Klej<br/>Nazwa = {Nazwa łącza},<br/>Rola = nadawca,<br/>Źródło = {identyfikator łącza klienta},<br/>Target =**{Via-Entity}**,<br/>**Właściwości = map [( <br/> com. Microsoft: transfer-Destination-Address = <br/> {miejsce_docelowe-Entity})]** ) | ------> | |
| | <------ | Klej<br/>Nazwa = {Nazwa łącza},<br/>Rola = odbiornik,<br/>Źródło = {identyfikator łącza klienta},<br/>Target = {Via-Entity},<br/>Właściwości = mapa [(<br/>com. Microsoft: transfer-Destination-Address =<br/>{Destination-Entity})] ) |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat AMQP, odwiedź następujące linki:

* [Service Bus AMQP — Omówienie]
* [Obsługa AMQP 1,0 Service Bus dla kolejek i tematów podzielonych na partycje]
* [AMQP w Service Bus dla systemu Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP — Omówienie]: service-bus-amqp-overview.md
[Obsługa AMQP 1,0 Service Bus dla kolejek i tematów podzielonych na partycje]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
