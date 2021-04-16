---
title: Protokół AMQP 1.0 Azure Service Bus i Event Hubs przewodnik | Microsoft Docs
description: Przewodnik po protokole dotyczący wyrażeń i opisu protokołu AMQP 1.0 w Azure Service Bus i Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 8575e17cd06a4153928837e6990c764d7a29993f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502066"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Protokół AMQP 1.0 w Azure Service Bus i Event Hubs przewodniku po protokole

Advanced Message Queueing Protocol 1.0 to standardowy protokół ramek i transferu, który umożliwia asynchroniczne, bezpieczne i niezawodne przesyłanie komunikatów między dwiema stronami. Jest to podstawowy protokół usługi Azure Service Bus Messaging i Azure Event Hubs.  

AmQP 1.0 jest wynikiem szerokiej współpracy branżowej, która łączy dostawców oprogramowania pośredniczącego, takich jak Microsoft i Red Hat, z wieloma użytkownikami oprogramowania pośredniczącego do obsługi komunikatów, takimi jak JP Morgan Morgan Reprezentujący branżę usług finansowych. Forum techniczne standaryzacji dla specyfikacji rozszerzeń i protokołu AMQP to ISO i uzyskało formalne zatwierdzenie jako standard międzynarodowy, jak ISO/IEC 19494:2014. 

## <a name="goals"></a>Cele

Ten artykuł zawiera podsumowanie podstawowych pojęć dotyczących specyfikacji komunikatów AMQP 1.0 wraz ze specyfikacjami rozszerzeń opracowanymi przez komitet techniczny [DSMMC i](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) wyjaśnia, jak program Azure Service Bus implementuje te specyfikacje i opiera się na nich.

Celem jest, aby każdy deweloper używający dowolnego istniejącego stosu klienta AMQP 1.0 na dowolnej platformie mógł wchodzić w interakcje z usługą Azure Service Bus za pośrednictwem usługi AMQP 1.0.

Typowe stosy protokołu AMQP 1.0 ogólnego przeznaczenia, takie jak [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) lub [AMQP.NET Alne,](https://github.com/Azure/amqpnetlite)implementują wszystkie podstawowe elementy protokołu AMQP 1.0, takie jak sesje lub linki. Te kluczowe elementy są czasami opakowane za pomocą interfejsu API wyższego poziomu. Apache Proton oferuje nawet dwa interfejsy API imperatywnego interfejsu Api programu Apache Reactor i reaktywny interfejs API Reactor.

W poniższej dyskusji przyjęto założenie, że zarządzanie połączeniami, sesjami i linkami AMQP oraz obsługa transferów ramek i sterowania przepływem są obsługiwane przez odpowiedni stos (taki jak Apache Proton-C) i nie wymagają zbyt wiele, jeśli konkretną uwagę zwracają deweloperzy aplikacji. W sposób abstrakcyjny zakładamy istnienie kilku typów pierwotnych interfejsu API,  takich  jak możliwość nawiązywania połączenia i tworzenia jakiejś formy obiektów abstrakcji nadawcy i odbiorcy, które następnie mają pewien kształt i `send()` `receive()` operacji.

Podczas omawiania zaawansowanych możliwości usługi Azure Service Bus, takich jak przeglądanie komunikatów lub zarządzanie sesjami, te funkcje są wyjaśnione w terminach AMQP, ale także jako wielowarstwowa pseudo implementacja na podstawie zakładanej abstrakcji interfejsu API.

## <a name="what-is-amqp"></a>Co to jest amqp?

AMQP to protokół ramek i transferu. Ramka oznacza, że zapewnia strukturę dla strumieni danych binarnych, które przepływa w dowolnym kierunku połączenia sieciowego. Struktura zapewnia rozdyskonanie odrębnych bloków danych, nazywanych *ramkami,* które mają być wymieniane między połączonymi stronami. Możliwości transferu zapewniają, że obie komunikujące się strony mogą ustalić wspólne informacje o tym, kiedy mają być przesyłane ramki i kiedy transfery należy uznać za ukończone.

W przeciwieństwie do wcześniejszych wygasłych wersji roboczych wytwarzanych przez grupę roboczą AMQP, które są nadal w użyciu przez kilku brokerów komunikatów, ostateczny i standardowy protokół AMQP 1.0 grupy roboczej nie określa obecności brokera komunikatów ani żadnej konkretnej topologii dla jednostek wewnątrz brokera komunikatów.

Protokół może być używany do symetrycznej komunikacji równorzędnej w celu interakcji z brokerami komunikatów, które obsługują kolejki, oraz do publikowania/subskrybowania jednostek, Azure Service Bus nie. Można jej również używać do interakcji z infrastrukturą obsługi komunikatów, gdzie wzorce interakcji różnią się od zwykłych kolejek, jak w przypadku Azure Event Hubs. Centrum zdarzeń działa jak kolejka, gdy zdarzenia są do niego wysyłane, ale działa bardziej jak usługa magazynu szeregowego, gdy zdarzenia są z niego odczytywane; Przypomina on nieco taśmę. Klient wybiera przesunięcie do dostępnego strumienia danych, a następnie jest obsługiwane wszystkie zdarzenia z tego przesunięcia do najnowszej dostępnej wersji.

Protokół AMQP 1.0 został zaprojektowany tak, aby można go było rozszerzać, umożliwiając dalsze specyfikacje w celu zwiększenia jego możliwości. Ilustrują to trzy specyfikacje rozszerzeń omówione w tym dokumencie. W przypadku komunikacji za pośrednictwem istniejącej infrastruktury protokołów HTTPS/WebSocket skonfigurowanie natywnych portów TCP protokołu AMQP może być trudne. Specyfikacja powiązania definiuje sposób warstwie AMQP za pośrednictwem obiektów WebSocket. W przypadku interakcji z infrastrukturą obsługi komunikatów w sposób żądania/odpowiedzi na potrzeby zarządzania lub w celu zapewnienia zaawansowanych funkcji specyfikacja zarządzania amqp definiuje wymagane podstawowe typy pierwotne interakcji. W przypadku integracji modelu autoryzacji federacyjnych specyfikacja zabezpieczeń opartych na oświadczeniach AMQP definiuje sposób kojarzenia i odnawiania tokenów autoryzacji skojarzonych z linkami.

## <a name="basic-amqp-scenarios"></a>Podstawowe scenariusze amqp

W tej sekcji wyjaśniono podstawowe użycie usługi AMQP 1.0 z usługą Azure Service Bus, co obejmuje tworzenie połączeń, sesji i linków oraz przesyłanie komunikatów do i z jednostek usługi Service Bus, takich jak kolejki, tematy i subskrypcje.

Najbardziej autorytatywnym źródłem informacji na temat sposobu działania protokołu AMQP jest specyfikacja [AMQP 1.0,](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)ale została ona napisana w celu dokładnego przewodnika implementacji, a nie uczenia protokołu. W tej sekcji skupiono się na wprowadzeniu tak dużej terminologii, jaka jest potrzebna do opisywania, Service Bus korzysta z amqp 1.0. Aby zapoznać się z bardziej kompleksowym wprowadzeniem do technologii AMQP, a także szerszej dyskusji na temat amqp 1.0, możesz przejrzeć [ten kurs wideo.][this video course]

### <a name="connections-and-sessions"></a>Połączenia i sesje

AmQP wywołuje kontenery komunikowania *programów;* Zawierają one *węzły*, które są komunikują się jednostkami wewnątrz tych kontenerów. Kolejka może być takim węzłem. AmQP umożliwia multipleksowanie, dzięki czemu jedno połączenie może być używane dla wielu ścieżek komunikacji między węzłami; Na przykład klient aplikacji może jednocześnie odbierać z jednej kolejki i wysyłać do innej kolejki za pośrednictwem tego samego połączenia sieciowego.

![Diagram przedstawiający sesje i połączenia między kontenerami.][1]

Połączenie sieciowe jest zatem zakotwiczone w kontenerze. Jest on inicjowany przez kontener w roli klienta, który nawiązał wychodzące połączenie gniazda TCP z kontenerem w roli odbiorcy, który nasłuchuje i akceptuje przychodzące połączenia TCP. Uwierzytelnianie połączeń obejmuje negocjowanie wersji protokołu, deklarowanie lub negocjowanie użycia zabezpieczeń na poziomie transportu (TLS/SSL) oraz uwierzytelnianie/autoryzację w zakresie połączenia opartym na protokole SASL.

Azure Service Bus zawsze wymaga użycia TLS. Obsługuje ona połączenia za pośrednictwem portu TCP 5671, w ramach którego połączenie TCP jest najpierw nałowane przy użyciu protokołu TLS przed wprowadzeniem uściślenia protokołu AMQP, a także obsługuje połączenia za pośrednictwem portu TCP 5672, zgodnie z którym serwer natychmiast oferuje obowiązkowe uaktualnienie połączenia do protokołu TLS przy użyciu modelu zalecanego przez protokół AMQP. Powiązanie protokołu WebSocket protokołu AMQP tworzy tunel za pośrednictwem portu TCP 443, który jest następnie odpowiednikiem połączeń PROTOKOŁU AMQP 5671.

Po skonfigurowaniu połączenia i zabezpieczeń TLS Service Bus dwie opcje mechanizmu SASL:

* SASL PLAIN jest często używana do przekazywania poświadczeń nazwy użytkownika i hasła do serwera. Service Bus nie ma kont, ale [nazwane](service-bus-sas.md)reguły zabezpieczeń dostępu współdzielonych , które uprawniają do praw i są skojarzone z kluczem. Nazwa reguły jest używana jako nazwa użytkownika, a klucz (jako tekst zakodowany w formacie base64) jest używany jako hasło. Uprawnienia skojarzone z wybraną regułą określają operacje dozwolone w połączeniu.
* Interfejs SASL ANONYMOUS jest używany do pomijania autoryzacji SASL, gdy klient chce korzystać z modelu zabezpieczeń opartego na oświadczeniach (CBS) opisanego w dalszej części. W przypadku tej opcji połączenie klienta może zostać nawiązane anonimowo przez krótki czas, podczas którego klient może wchodzić w interakcję tylko z punktem końcowym CBS, a uściślić połączenie CBS musi zostać zakończone.

Po nawiązaniu połączenia transportowego kontenery deklarują maksymalny rozmiar ramki, który mogą obsłużyć, a po upływie limitu czasu bezczynności zostaną one rozłączne w sposób jednostronny, jeśli połączenie nie będzie mieć żadnej aktywności.

Deklarują również, ile kanałów współbieżnych jest obsługiwanych. Kanał jest jednokierunkową, wychodzącą ścieżką transferu wirtualnego w górnej części połączenia. Sesja przyjmuje kanał z każdego ze wzajemnie połączonych kontenerów, aby tworzyć dwukierunkową ścieżkę komunikacji.

Sesje mają model sterowania przepływem oparty na oknach; Po utworzeniu sesji każda strona deklaruje, ile ramek może zaakceptować w swoim oknie odbierania. Gdy strony wymieniają ramki, transferowane ramki wypełniają to okno i transfery zatrzymują się, gdy okno jest pełne i dopóki okno nie zostanie zresetowane lub rozszerzone przy użyciu przepływu *performatywnego* *(performative* to termin PROTOKOŁU AMQP dla gestów na poziomie protokołu wymienianych między dwiema stronami).

Ten model oparty na oknach jest w przybliżeniu analogiczny do koncepcji TCP sterowania przepływem opartego na oknach, ale na poziomie sesji wewnątrz gniazda. Istnieje koncepcja protokołu zezwalania na wiele współbieżnych sesji, aby ruch o wysokim priorytecie mógł być przesyłany przez ograniczany normalny ruch, tak jak w przypadku obwodu autostradowego.

Azure Service Bus aktualnie używa dokładnie jednej sesji dla każdego połączenia. Maksymalny Service Bus ramki to 262 144 bajty (256 K bajtów) dla Service Bus Standardowa. Wynosi 1 048 576 (1 MB) dla Service Bus Premium i Event Hubs. Service Bus nie nakłada żadnych okien ograniczania na poziomie sesji, ale regularnie resetuje okno w ramach sterowania przepływem na poziomie łącza (zobacz [następną sekcję](#links)).

Połączenia, kanały i sesje są efemerycznych. Jeśli połączenie bazowe zwija się, połączenia, tunel TLS, kontekst autoryzacji SASL i sesje muszą zostać ponownie nawiązane.

### <a name="amqp-outbound-port-requirements"></a>Wymagania dotyczące portów wychodzących amqp

Klienci, którzy używają połączeń PROTOKOŁU AMQP za pośrednictwem protokołu TCP, wymagają otwarcia portów 5671 i 5672 w lokalnej zaporze. Wraz z tymi portami może być konieczne otwarcie dodatkowych portów, jeśli włączono funkcję [EnableLinkRedirect.](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) `EnableLinkRedirect` to nowa funkcja obsługi komunikatów, która pomaga pominąć jeden przeskok podczas odbierania komunikatów, co pomaga zwiększyć przepływność. Klient rozpocznie bezpośrednią komunikację z usługą back-end za pośrednictwem zakresu portów 104XX, jak pokazano na poniższej ilustracji. 

![Lista portów docelowych][4]

Klient .NET zakończyłby się niepowodzeniem z błędem SocketException ("Podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu"), jeśli te porty są zablokowane przez zaporę. Tę funkcję można wyłączyć przez ustawienie parametrów połączenia, co wymusza komunikację klientów ze zdalną usługą za `EnableAmqpLinkRedirect=false` pośrednictwem portu 5671.


### <a name="links"></a>Linki

AmQP przesyła komunikaty za pośrednictwem linków. Link to ścieżka komunikacji utworzona za pośrednictwem sesji, która umożliwia przesyłanie komunikatów w jednym kierunku; Negocjacja stanu transferu jest za pośrednictwem połączenia i dwukierunkowa między połączonymi stronami.

![Zrzut ekranu przedstawiający sesję z połączeniem połączenia między dwoma kontenerami.][2]

Łącza mogą być tworzone przez dowolny kontener w dowolnym momencie i za pośrednictwem istniejącej sesji, co sprawia, że protokół AMQP różni się od wielu innych protokołów, w tym HTTP i MQTT, gdzie inicjowanie transferów i ścieżki transferu jest wyłącznym uprawnieniem strony tworzącej połączenie gniazda.

Kontener inicjujący łącza prosi przeciwny kontener o zaakceptowanie linku i wybiera rolę nadawcy lub odbiorcy. W związku z tym albo kontener może inicjować tworzenie jednokierunkowych lub dwukierunkowych ścieżek komunikacji, z których drugi jest modelowany jako pary linków.

Linki są nazwane i skojarzone z węzłami. Jak dano na początku, węzły są komunikują się jednostkami wewnątrz kontenera.

W Service Bus węzeł jest bezpośrednio odpowiednikiem kolejki, tematu, subskrypcji lub podqueue deadletter kolejki lub subskrypcji. W związku z tym nazwa węzła używana w programie AMQP jest względną nazwą jednostki w przestrzeni Service Bus nazw. Jeśli kolejka ma nazwę `myqueue` , jest to również jej nazwa węzła AMQP. Subskrypcja tematu jest zgodna z konwencją interfejsu API protokołu HTTP przez  posortowanie jej do kolekcji zasobów "subscriptions", a zatem podsieć subskrypcji w temacie **mytopic** ma nazwę węzła protokołu AMQP **mytopic/subscriptions/sub**.

Klient łączący musi również używać nazwy węzła lokalnego do tworzenia linków. Service Bus nie preskrypcja tych nazw węzłów i nie interpretuje ich. Stosy klienta AMQP 1.0 zwykle używają schematu, aby mieć pewność, że te efemeracyjne nazwy węzłów są unikatowe w zakresie klienta.

### <a name="transfers"></a>Transfery

Po nawiązanego linku komunikaty mogą być przesyłane za pośrednictwem tego linku. W protokole AMQP transfer jest wykonywany za pomocą jawnego gestu protokołu *(transferu* performatywnego), który przenosi komunikat z nadawcy do odbiorcy za pośrednictwem łącza. Przeniesienie jest ukończone, gdy jest "rozliczone", co oznacza, że obie strony ustaliły wspólne zrozumienie wyniku tego przeniesienia.

![Diagram przedstawiający transfer komunikatu między nadawcą i odbiornikiem oraz dyspozycją, która jest z niego wynika.][3]

W najprostszym przypadku nadawca może wybrać wysyłanie komunikatów "wstępnie ustalone", co oznacza, że klient nie jest zainteresowany wynikiem, a odbiorca nie przesyła żadnych opinii na temat wyniku operacji. Ten tryb jest obsługiwany przez Service Bus na poziomie protokołu AMQP, ale nie jest on ujawniony w żadnym interfejsie API klienta.

Zwykły przypadek jest taki, że komunikaty są wysyłane w sposób nierozwiązywalny, a odbiorca wskazuje akceptację lub odrzucenie przy użyciu *dyspozycji* performatywnej. Odrzucenie ma miejsce, gdy odbiorca nie może zaakceptować komunikatu z jakiegokolwiek powodu, a komunikat o odrzuceniu zawiera informacje o przyczynie, czyli strukturze błędów zdefiniowanej przez usługę AMQP. Jeśli komunikaty zostaną odrzucone z powodu błędów wewnętrznych wewnątrz usługi Service Bus, usługa zwraca dodatkowe informacje wewnątrz tej struktury, które mogą służyć do zapewniania wskazówek diagnostycznych personelowi pomocy technicznej w przypadku zgłaszania żądań pomocy technicznej. Więcej szczegółów na temat błędów znajdziesz później.

Specjalną formą odrzucenia  jest stan zwalniania, który wskazuje, że odbiorca nie ma technicznego zastrzeżeń co do przeniesienia, ale również nie interesuje się rozliczaniem przeniesienia. Taki przypadek występuje na przykład wtedy, gdy komunikat jest dostarczany do klienta usługi Service Bus, a klient wybiera opcję "porzucenia" komunikatu, ponieważ nie może wykonać pracy wynikającej z przetwarzania komunikatu; Samo dostarczanie komunikatów nie jest uszkodzone. Odmianą tego stanu jest *stan modyfikacji,* który umożliwia zmiany komunikatu w przypadku jego zwalniania. Obecnie ten stan nie jest używany przez Service Bus w.

Specyfikacja AMQP 1.0 definiuje kolejny stan dyspozycji o nazwie *received*(odebrano), który w szczególności ułatwia obsługę odzyskiwania linków. Odzyskiwanie linków umożliwia odtworzenie stanu połączenia i wszelkich oczekujących dostaw na podstawie nowego połączenia i sesji, gdy poprzednie połączenie i sesja zostały utracone.

Service Bus nie obsługuje odzyskiwania linków; Jeśli klient utraci połączenie z usługą Service Bus z nierozwiązaną oczekiwaną wiadomością, transfer komunikatów zostanie utracony, a klient musi ponownie nawiązać połączenie, ponownie nawiązać połączenie i ponowić próbę transferu.

W związku z tym usługi Service Bus i Event Hubs obsługują transfer "co najmniej raz", w którym nadawca może mieć pewność, że komunikat został zapisany i zaakceptowany, ale nie obsługuje transferów "dokładnie jeden raz" na poziomie amqp, gdzie system spróbuje odzyskać połączenie i kontynuować negocjowanie stanu dostarczania, aby uniknąć duplikowania transferu komunikatów.

Aby skompensować możliwe zduplikowane wysyłanie, Service Bus obsługuje wykrywanie duplikatów jako opcjonalną funkcję w kolejkach i tematach. Wykrywanie duplikatów rejestruje identyfikatory komunikatów wszystkich komunikatów przychodzących w zdefiniowanym przez użytkownika oknie czasowym, a następnie dyskretnie porzuca wszystkie komunikaty wysyłane przy użyciu tych samych identyfikatorów komunikatów w tym samym oknie.

### <a name="flow-control"></a>Sterowanie przepływem

Oprócz omówionego wcześniej modelu sterowania przepływem na poziomie sesji każde łącze ma własny model sterowania przepływem. Sterowanie przepływem na poziomie sesji chroni kontener przed koniecznością obsługi zbyt wielu ramek jednocześnie, a sterowanie przepływem na poziomie łącza umieszcza aplikację w odpowiedzialności za to, ile komunikatów ma obsługiwać z linku i kiedy.

![Zrzut ekranu przedstawiający dziennik przedstawiający źródło, obiekt docelowy, port źródłowy, port docelowy i nazwę protokołu. W pierwszym wierszu port docelowy 10401 (0x28 A 1) jest opisany kolorem czarnym.][4]

W przypadku linku transfer może nastąpić tylko wtedy, gdy nadawca ma wystarczającą *ilość środków na link*. Credit link to licznik ustawiony przez odbiorcę przy użyciu *przepływu* performatywnego, którego zakres jest ograniczony do łącza. Gdy nadawca ma przypisane informacje o linku, próbuje wykorzystać te informacje, dostarczając komunikaty. Każde dostarczanie komunikatów zmniejsza ilość pozostałych środków na link o 1. Po zuzysłaniu środków na połączenia dostawy są zatrzymywane.

Gdy Service Bus pełni rolę odbiorcy, natychmiast zapewnia nadawcy wiele środków na łączenie, dzięki czemu komunikaty mogą być wysyłane natychmiast. W związku z tym, że są używane  Service Bus czasami wysyła przepływ do nadawcy w celu zaktualizowania salda środków na link.

W roli nadawcy Service Bus komunikaty w celu wykorzystania wszelkich zaległych środków na link.

Wywołanie "odbierania" na poziomie interfejsu  API przekłada się na przepływ performatywny wysyłany do usługi Service Bus przez klienta, Service Bus zużywa te środków, pobierając pierwszy dostępny, odblokowany komunikat z kolejki, blokując go i przesyłając. Jeśli nie ma żadnych komunikatów dostępnych do dostarczenia, wszelkie zaległe kredyty za pomocą dowolnego linku utworzonego z określoną jednostką pozostają rejestrowane w kolejności odlotu, a komunikaty są blokowane i przenoszone w momencie ich dostarczenia, aby użyć wszelkich zaległych środków.

Blokada komunikatu jest zwalniana, gdy transfer jest rozstrzygany w jednym z stanów końcowych zaakceptowanych, odrzuconych lub *wydanych*. Komunikat jest usuwany z Service Bus po zaakceptowaniu stanu *terminalu*. Pozostaje ona w Service Bus i jest dostarczana do następnego odbiorcy, gdy transfer osiągnie dowolny z pozostałych stanów. Service Bus automatycznie przenosi komunikat do kolejki zakleszczenia jednostki, gdy osiągnie maksymalną liczbę dostaw dozwoloną dla jednostki z powodu powtarzających się odrzuceń lub wydań.

Mimo że interfejsy API usługi Service Bus nie uwidoczniają obecnie bezpośrednio takiej opcji, klient protokołu AMQP niższego poziomu może użyć modelu środków na link, aby przekształcić interakcję "w stylu ściągania" w przypadku wystawiania jednej jednostki środków dla każdego żądania odbioru w model "w stylu wypychania", wystawiając dużą liczbę środków na łącza, a następnie odbierając komunikaty, gdy staną się dostępne bez dalszej interakcji. Wypychanie jest obsługiwane za pośrednictwem ustawień właściwości [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) lub [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) W przypadku wartości innych niż zero klient AMQP używa go jako środków na połączenie.

W tym kontekście ważne jest, aby zrozumieć, że zegar wygaśnięcia blokady komunikatu wewnątrz jednostki rozpoczyna się, gdy komunikat jest owijany z jednostki, a nie gdy komunikat jest umieszczany w sieci. Zawsze, gdy klient wskazuje gotowość do odbierania komunikatów przez wystawienie środków na link, oczekuje się, że będzie aktywnie ściągał komunikaty przez sieć i był gotowy do ich obsługi. W przeciwnym razie blokada komunikatu może wygasła, zanim komunikat zostanie dostarczony. Użycie kontroli przepływu link-credit powinno bezpośrednio odzwierciedlać natychmiastową gotowość do radzenia sobie z dostępnymi komunikatami wysłanymi do odbiorcy.

Podsumowując, poniższe sekcje zawierają schematyczny przegląd przepływu performatywnego podczas różnych interakcji interfejsu API. W każdej sekcji opisano inną operację logiczną. Niektóre z tych interakcji mogą być "z opóźnieniem", co oznacza, że mogą być wykonywane tylko wtedy, gdy jest to wymagane. Utworzenie nadawcy komunikatów może nie spowodować interakcji sieciowej, dopóki pierwszy komunikat nie zostanie wysłany lub zażądany.

Strzałki w poniższej tabeli pokazują performatywny kierunek przepływu.

#### <a name="create-message-receiver"></a>Tworzenie odbiornika komunikatów

| Klient | Service Bus |
| --- | --- |
| `--> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link ID}<br/>)` |Klient dołącza do jednostki jako odbiornik |
| Service Bus odpowiedzi dołączające koniec linku |`<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>target={client link ID}<br/>)` |

#### <a name="create-message-sender"></a>Tworzenie nadawcy komunikatów

| Klient | Service Bus |
| --- | --- |
| `--> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>)` |Brak akcji |
| Brak akcji |`<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>)` |

#### <a name="create-message-sender-error"></a>Tworzenie nadawcy komunikatów (błąd)

| Klient | Service Bus |
| --- | --- |
| `--> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>)` |Brak akcji |
| Brak akcji |`<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>)` |

#### <a name="close-message-receiversender"></a>Zamykanie odbiorcy/nadawcy komunikatów

| Klient | Service Bus |
| --- | --- |
| `--> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>)` |Brak akcji |
| Brak akcji |`<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>)` |

#### <a name="send-success"></a>Wysyłanie (powodzenie)

| Klient | Service Bus |
| --- | --- |
| `--> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |Brak akcji |
| Brak akcji |`<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>)` |

#### <a name="send-error"></a>Wyślij (błąd)

| Klient | Service Bus |
| --- | --- |
| `--> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |Brak akcji |
| Brak akcji |`<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>)` |

#### <a name="receive"></a>Odbieranie

| Klient | Service Bus |
| --- | --- |
| `--> flow(<br/>link-credit=1<br/>)` |Brak akcji |
| Brak akcji |`< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |
| `--> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>)` |Brak akcji |

#### <a name="multi-message-receive"></a>Odbieranie wielu komunikatów

| Klient | Service Bus |
| --- | --- |
| `--> flow(<br/>link-credit=3<br/>)` |Brak akcji |
| Brak akcji |`< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |
| Brak akcji |`< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |
| Brak akcji |`< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>)` |
| `--> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>)` |Brak akcji |

### <a name="messages"></a>Komunikaty

W poniższych sekcjach wyjaśniono, które właściwości ze standardowych sekcji komunikatów AMQP są używane przez program Service Bus i jak są mapowe na zestaw Service Bus API.

Każda właściwość, która musi zostać zdefiniowana przez aplikację, powinna być mapowana na mapę usługi `application-properties` AMQP.

#### <a name="header"></a>header

| Nazwa pola | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| Trwałe |- |- |
| priority |- |- |
| czas wygaśnięcia |Czas do życia dla tego komunikatu |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| liczba dostaw |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Nazwa pola | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| identyfikator komunikatu |Zdefiniowany przez aplikację, bezpłatny identyfikator dla tego komunikatu. Służy do wykrywania duplikatów. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Zdefiniowany przez aplikację identyfikator użytkownika, który nie jest interpretowany przez Service Bus. |Niedostępne za pośrednictwem interfejsu API Service Bus API. |
| na wartość |Identyfikator docelowy zdefiniowany przez aplikację, który nie jest interpretowany przez Service Bus. |[Do](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Zdefiniowany przez aplikację identyfikator celu komunikatu, który nie jest interpretowany przez Service Bus |[Etykieta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| odpowiadanie na |Zdefiniowany przez aplikację wskaźnik ścieżki odpowiedzi, który nie jest interpretowany przez Service Bus. |[Replyto](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Zdefiniowany przez aplikację identyfikator korelacji, który nie jest interpretowany przez Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Zdefiniowany przez aplikację wskaźnik typu zawartości dla treści, który nie jest interpretowany przez Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| kodowanie zawartości |Zdefiniowany przez aplikację wskaźnik kodowania zawartości dla treści, który nie jest interpretowany przez Service Bus. |Niedostępne za pośrednictwem interfejsu API Service Bus API. |
| bezwzględny czas wygaśnięcia |Deklaruje moment wygaśnięcia bezwzględnej wiadomości. Ignorowane w danych wejściowych (zaobserwowano czas wygaśnięcia nagłówka), autorytatywne w danych wyjściowych. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| czas tworzenia |Deklaruje, kiedy komunikat został utworzony. Nie są używane przez Service Bus |Niedostępne za pośrednictwem interfejsu API Service Bus api. |
| identyfikator grupy |Zdefiniowany przez aplikację identyfikator powiązanego zestawu komunikatów. Służy do Service Bus sesji. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| sekwencja-grup |Licznik identyfikujący względny numer sekwencji komunikatu wewnątrz sesji. Ignorowane przez Service Bus. |Niedostępne za pośrednictwem interfejsu API Service Bus api. |
| identyfikator odpowiedzi na grupę |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Adnotacje komunikatów

Istnieje kilka innych właściwości komunikatów usługi Service Bus, które nie są częścią właściwości komunikatu AMQP i są przekazywane jako `MessageAnnotations` w komunikacie.

| Klucz mapy adnotacji | Użycie | Nazwa interfejsu API |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklaruje, kiedy komunikat powinien pojawić się w jednostce |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | Klucz zdefiniowany przez aplikację, który decyduje o partycji, w której powinien zostać wyświetlony komunikat. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partition-key | Zdefiniowana przez aplikację wartość klucza partycji, gdy transakcja ma być używana do wysyłania komunikatów za pośrednictwem kolejki transferu. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enqueued-time | Zdefiniowany przez usługę czas UTC reprezentujący rzeczywisty czas kolejkowania komunikatu. Ignorowane w danych wejściowych. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sequence-number | Zdefiniowany przez usługę unikatowy numer przypisany do komunikatu. | [Sequencenumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| przesunięcie x-opt | Zdefiniowany przez usługę numer sekwencji w kolejkach komunikatu. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-locked-until | Zdefiniowane przez usługę. Data i godzina zablokowania komunikatu w kolejce/subskrypcji. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-source | Zdefiniowane przez usługę. Jeśli komunikat jest odbierany z kolejki utraconych wiadomości, jest to źródło oryginalnego komunikatu. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Możliwość transakcji

Transakcja grupuje razem co najmniej dwie operacje w zakresie wykonania. Z natury taka transakcja musi zapewnić, że wszystkie operacje należące do danej grupy operacji będą wspólnie zakończyć się powodzeniem lub niepowodzeniem.
Operacje są pogrupowane według identyfikatora `txn-id` .

W przypadku interakcji transakcyjnej klient działa jako , który kontroluje `transaction controller` operacje, które powinny być zgrupowane razem. Service Bus Service działa jako i `transactional resource` wykonuje pracę zgodnie z żądaniem . `transaction controller`

Klient i usługa komunikują się `control link` za pośrednictwem , który jest ustalany przez klienta. Komunikaty i są wysyłane przez kontroler za pośrednictwem linku sterowania w celu przydzielenia i ukończenia transakcji (nie reprezentują one podziału pracy `declare` `discharge` transakcyjnej). Rzeczywiste wysyłanie/odbieranie nie jest wykonywane na tym linku. Każda żądana operacja transakcyjna jest jawnie identyfikowana za pomocą żądanego i w związku z tym `txn-id` może wystąpić w dowolnym linku w połączeniu. Jeśli link kontroli zostanie zamknięty, gdy istnieją utworzone transakcje niepodzielone, wszystkie takie transakcje zostaną natychmiast wycofane, a próby wykonania dalszych prac transakcyjnych na nich doprowadzą do awarii. Komunikaty w linku sterującym nie mogą być wstępnie ustalone.

Każde połączenie musi inicjować własny link sterujący, aby móc uruchamiać i kończyć transakcje. Usługa definiuje specjalny element docelowy, który działa jako `coordinator` . Klient/kontroler ustanawia połączenie kontrolki z tym elementem docelowym. Link sterowania znajduje się poza granicą jednostki, to oznacza, że tego samego linku sterującego można użyć do inicjowania i łączenia transakcji dla wielu jednostek.

#### <a name="starting-a-transaction"></a>Uruchamianie transakcji

Aby rozpocząć pracę transakcyjną. Kontroler musi uzyskać od `txn-id` koordynatora . Robi to, wysyłając `declare` komunikat typu. Jeśli deklaracja powiedzie się, koordynator odpowiada przy użyciu wyniku dyspozycji, który wykonuje przypisany `txn-id` .

| Klient (kontroler) | Kierunek | Service Bus (Koordynator) |
| :--- | :---: | :--- |
| attach(<br/>name={nazwa linku},<br/>... ,<br/>role =**nadawca**,<br/>target =**Coordinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={nazwa linku},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state =**Zadeklarowane**(<br/>**txn-id**={identyfikator transakcji}<br/>))|

#### <a name="discharging-a-transaction"></a>Rozsyłanie transakcji

Kontroler kończy pracę transakcyjną, wysyłając `discharge` komunikat do koordynatora. Kontroler wskazuje, że chce zatwierdzić lub wycofać pracę transakcyjną, ustawiając `fail` flagę na treści chrzętnych. Jeśli koordynator nie może ukończyć zadania, komunikat zostanie odrzucony, a wynik będzie zawierał `transaction-error` wartość .

> Uwaga: fail=true odnosi się do wycofywania transakcji, a fail=false odnosi się do zatwierdzenia.

| Klient (kontroler) | Kierunek | Service Bus (Koordynator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={identyfikator transakcji}<br/>))|
| | . . . <br/>Praca transakcyjna<br/>na innych linkach<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Nieznajomy(txn-id=0, <br/> fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Wysyłanie komunikatu w transakcji

Wszystkie działania transakcyjne są wykonywane przy użyciu stanu dostarczania transakcyjnego, `transactional-state` który zawiera identyfikator txn-id. W przypadku wysyłania komunikatów stan transakcyjny jest przekierowyny przez ramkę transferu komunikatu. 

| Klient (kontroler) | Kierunek | Service Bus (Koordynator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={identyfikator transakcji}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state= <br/> TransactionalState( <br/> txn-id=0)**)<br/>{ ładunek }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Rozsyłanie komunikatu w transakcji

Dyspozycja komunikatów obejmuje operacje takie jak `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Aby wykonać te operacje w ramach transakcji, przekaż wartość `transactional-state` z rozporządzaniem.

| Klient (kontroler) | Kierunek | Service Bus (Koordynator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={identyfikator transakcji}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ ładunek }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Zaawansowane Service Bus zarządzania

Ta sekcja obejmuje zaawansowane możliwości Azure Service Bus opartych na wersjach roboczych rozszerzeń AMQP, które są obecnie opracowywane w komitetze technicznym THE DLA AMQP. Service Bus implementuje najnowsze wersje tych wersji i wdraża zmiany wprowadzone, gdy te wersje robocze osiągną stan standardowy.

> [!NOTE]
> Service Bus zaawansowane operacje obsługi komunikatów są obsługiwane za pośrednictwem wzorca żądania/odpowiedzi. Szczegóły tych operacji opisano w artykule [AMQP 1.0 in Service Bus: request-response-based operations (AmQP 1.0:](service-bus-amqp-request-response.md)operacje oparte na żądaniach i odpowiedziach).
> 
> 

### <a name="amqp-management"></a>Zarządzanie usługą AMQP

Specyfikacja zarządzania amqp jest pierwszym z wersji roboczej rozszerzeń omówiony w tym artykule. Ta specyfikacja definiuje zestaw protokołów nakładanych na protokół AMQP, który umożliwia interakcje zarządzania z infrastrukturą obsługi komunikatów za pośrednictwem protokołu AMQP. Specyfikacja definiuje operacje ogólne, takie jak *tworzenie,* odczytywanie,  *aktualizowanie* i usuwanie na przykład zarządzanie jednostkami w infrastrukturze obsługi komunikatów i zestaw operacji zapytań. 

Wszystkie te gesty wymagają interakcji między klientem a infrastrukturą obsługi komunikatów i dlatego specyfikacja definiuje sposób modelowania tego wzorca interakcji na podstawie wzorca AMQP: klient łączy się z infrastrukturą obsługi komunikatów, inicjuje sesję, a następnie tworzy parę linków. W jednym linku klient działa jako nadawca, a na drugim działa jako odbiorca, tworząc w ten sposób parę linków, które mogą działać jako kanał dwukierunkowy.

| Operacja logiczna | Klient | Service Bus |
| --- | --- | --- |
| Tworzenie ścieżki odpowiedzi na żądanie |`--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>)` |Brak akcji |
| Tworzenie ścieżki odpowiedzi na żądanie |Brak akcji |`\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>)` |
| Tworzenie ścieżki odpowiedzi na żądanie |`--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>)` | |
| Tworzenie ścieżki odpowiedzi na żądanie |Brak akcji |`\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>)` |

Mając tę parę linków, implementacja żądania/odpowiedzi jest prosta: żądanie jest komunikatem wysyłanym do jednostki w infrastrukturze obsługi komunikatów, która rozumie ten wzorzec. W tym komunikacie żądania pole *odpowiedzi* w sekcji *properties*  jest ustawione na identyfikator docelowy linku, do którego ma być dostarczana odpowiedź. Jednostka obsługi przetwarza żądanie, a następnie dostarcza odpowiedź  za pośrednictwem linku, którego identyfikator docelowy odpowiada *wskazanemu identyfikatorowi odpowiedzi.*

Wzorzec oczywiście wymaga, aby kontener klienta i identyfikator wygenerowany przez klienta dla miejsca docelowego odpowiedzi był unikatowy dla wszystkich klientów i ze względów bezpieczeństwa również trudny do przewidzenia.

Wymiany komunikatów używane dla protokołu zarządzania i dla wszystkich innych protokołów, które używają tego samego wzorca, mają miejsce na poziomie aplikacji; Nie definiują nowych gestów na poziomie protokołu AMQP. Jest to zamierzone, dzięki czemu aplikacje mogą natychmiast korzystać z tych rozszerzeń ze zgodnymi stosami AMQP 1.0.

Service Bus obecnie nie implementuje żadnych podstawowych funkcji specyfikacji zarządzania, ale wzorzec żądania/odpowiedzi zdefiniowany przez specyfikację zarządzania jest podstawą funkcji zabezpieczeń opartej na oświadczeniach i dla niemal wszystkich zaawansowanych możliwości omówiony w poniższych sekcjach:

### <a name="claims-based-authorization"></a>Autoryzacja oparta na oświadczeniach

Wersja robocza specyfikacji AMQP Claims-Based Authorization (CBS) opiera się na wzorcu żądania/odpowiedzi specyfikacji zarządzania i opisuje uogólniony model używania federacyjnych tokenów zabezpieczających za pomocą usługi AMQP.

Domyślny model zabezpieczeń usługi AMQP omówiony we wprowadzeniu jest oparty na sygnaturze dostępu współdzielonego i integruje się z połączeniem AMQP handshake. Korzystanie z sygnatury dostępu współdzielonego ma tę zaletę, że zapewnia rozszerzalny model, dla którego zdefiniowano zestaw mechanizmów, z których może skorzystać każdy protokół, który formalnie opiera się na sygnaturze dostępu współdzielonego. Do tych mechanizmów należą "PLAIN" do transferu nazw użytkowników i haseł, "EXTERNAL" do powiązania z zabezpieczeniami na poziomie protokołu TLS, "ANONYMOUS" w celu wyrażenia braku jawnego uwierzytelniania/autoryzacji oraz szeroka gama dodatkowych mechanizmów, które umożliwiają przekazywanie poświadczeń lub tokenów uwierzytelniania i/lub autoryzacji.

Integracja sygnatury dostępu współdzielonego usługi AMQP ma dwie wady:

* Wszystkie poświadczenia i tokeny są w zakresie połączenia. Infrastruktura obsługi komunikatów może chcieć zapewnić rozróżnianą kontrolę dostępu dla encji. na przykład umożliwianie bearerowi tokenu wysyłania do kolejki A, ale nie do kolejki B. W kontekście autoryzacji zakotwiczony w połączeniu nie jest możliwe użycie jednego połączenia, a jeszcze użycie różnych tokenów dostępu dla kolejki A i kolejki B.
* Tokeny dostępu są zwykle ważne tylko przez ograniczony czas. Ta ważność wymaga, aby użytkownik okresowo żądał tokenów i umożliwia wystawcy tokenu odmówienie wystawienia nowego tokenu, jeśli uprawnienia dostępu użytkownika uległy zmianie. Połączenia AMQP mogą trwać przez długi czas. Model SASL umożliwia ustawienie tokenu tylko w czasie połączenia, co oznacza, że infrastruktura obsługi komunikatów musi rozłączyć klienta po wygaśnięciu tokenu lub musi zaakceptować ryzyko umożliwienia dalszej komunikacji z klientem, który ma prawa dostępu, może zostać odwołana w międzyczasie.

Specyfikacja AMQP CBS zaimplementowana przez usługę Service Bus umożliwia elegancki obejście obu tych problemów: umożliwia klientowi skojarzenie tokenów dostępu z każdym węzłem i zaktualizowanie tych tokenów przed wygaśnięciem bez przerywania przepływu komunikatów.

Usługa CBS definiuje wirtualny węzeł zarządzania o *nazwie $cbs*, który ma być dostarczany przez infrastrukturę obsługi komunikatów. Węzeł zarządzania akceptuje tokeny w imieniu innych węzłów w infrastrukturze obsługi komunikatów.

Gest protokołu to wymiana żądań/odpowiedzi zgodnie ze specyfikacją zarządzania. Oznacza to, że klient ustanawia parę linków z węzłem *$cbs, a* następnie przekazuje żądanie w linku wychodzącym, a następnie czeka na odpowiedź w linku przychodzącym.

Komunikat żądania ma następujące właściwości aplikacji:

| Klucz | Opcjonalne | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| operation |Nie |ciąg |**put-token** |
| typ |Nie |ciąg |Typ tokenu, który jest umieszczany. |
| name |Nie |ciąg |"Odbiorcy", do których odnosi się token. |
| Wygaśnięcia |Tak |sygnatura czasowa |Czas wygaśnięcia tokenu. |

Właściwość *name* identyfikuje jednostkę, z którą należy skojarzyć token. W Service Bus jest to ścieżka do kolejki lub tematu/subskrypcji. Właściwość *type* identyfikuje typ tokenu:

| Typ tokenu | Opis tokenu | Typ treści | Uwagi |
| --- | --- | --- | --- |
| `jwt` |JSON Web Token (JWT) |Wartość AMQP (ciąg) |Jeszcze niedostępne. |
| `servicebus.windows.net:sastoken` |Service Bus tokenu SAS |Wartość AMQP (ciąg) |- |

Prawa do odsłaniania tokenów. Service Bus zna trzy prawa podstawowe: funkcja "Wyślij" umożliwia wysyłanie, "Nasłuchiwać" umożliwia odbieranie, a "Zarządzanie" umożliwia manipulowanie jednostkami. Service Bus sygnatury dostępu współdzielonego odnoszą się do reguł skonfigurowanych w przestrzeni nazw lub jednostce, a te reguły są konfigurowane przy użyciu praw. Dzięki podpisaniu tokenu za pomocą klucza skojarzonego z tą regułą token wyraża odpowiednie prawa. Token skojarzony z jednostką przy użyciu *tokenu put* umożliwia połączonemu klientowi interakcję z jednostką zgodnie z prawami tokenu. Link, w którym klient przejmuje rolę *nadawcy,* wymaga prawa "Wyślij". Podjęcie roli *odbiornika* wymaga prawa "Listen" (Nasłuchiwać).

Komunikat odpowiedzi ma następujące *wartości właściwości* aplikacji

| Klucz | Opcjonalne | Typ wartości | Zawartość wartości |
| --- | --- | --- | --- |
| kod stanu |Nie |int |Kod odpowiedzi HTTP **[RFC2616]**. |
| opis stanu |Tak |ciąg |Opis stanu. |

Klient może wielokrotnie wywołać *token put i* dla dowolnej jednostki w infrastrukturze obsługi komunikatów. Zakres tokenów jest ograniczony do bieżącego klienta i zakotwiczony w bieżącym połączeniu, co oznacza, że serwer porzuca wszystkie zachowane tokeny, gdy połączenie zostanie porzucane.

Bieżąca implementacja Service Bus zezwala na korzystanie z cbs tylko w połączeniu z metodą SASL "ANONYMOUS". Połączenie SSL/TLS musi zawsze istnieć przed rozpoczęciem uściślania sygnatury dostępu współdzielonego.

W związku z tym mechanizm ANONIMOWY musi być obsługiwany przez wybranego klienta AMQP 1.0. Dostęp anonimowy oznacza, że wstępne uściślicie połączenia, w tym utworzenie sesji początkowej, odbywa się bez Service Bus informacji o tym, kto tworzy połączenie.

Po nawiązaniu połączenia i sesji dołączanie linków do węzła *$cbs* i wysyłanie żądania *tokenu put* są jedynymi dozwolonymi operacjami. Prawidłowy token musi zostać pomyślnie ustawiony przy użyciu żądania *tokenu* put dla jakiegoś węzła jednostki w ciągu 20 sekund po nawiązaniu połączenia. W przeciwnym razie połączenie zostanie odrzucone przez Service Bus.

Klient jest następnie odpowiedzialny za śledzenie wygaśnięcia tokenu. Gdy token wygaśnie, Service Bus natychmiast porzuca wszystkie linki w połączeniu z odpowiednią jednostką. Aby zapobiec występowaniu problemu, klient może w dowolnym momencie zastąpić token węzła nowym tokenem za pośrednictwem wirtualnego węzła zarządzania usługi *$cbs* za pomocą tego samego *gestu tokenu* put i bez przeszkadzania w ruchu ładunku przepływa który przepływa przez różne łącza.

### <a name="send-via-functionality"></a>Funkcja wysyłania za pośrednictwem

[Nadawca wysyłania/transferu](service-bus-transactions.md#transfers-and-send-via) to funkcja, która umożliwia usłudze Service Bus przekazywanie danego komunikatu do jednostki docelowej za pośrednictwem innej jednostki. Ta funkcja służy do wykonywania operacji między jednostkami w ramach jednej transakcji.

Za pomocą tej funkcji tworzysz nadawcę i ustanawiasz link do `via-entity` . Podczas ustanawiania połączenia przekazywane są dodatkowe informacje w celu ustalenia prawdziwego miejsca docelowego komunikatów/transferów w tym linku. Po pomyślnym dołączeniu wszystkie komunikaty wysłane za pośrednictwem tego  linku są automatycznie przekazywane do jednostki docelowej za pośrednictwem *elementu via-entity*. 

> Uwaga: Przed ustanowieniem tego linku  należy przeprowadzić uwierzytelnianie zarówno dla *jednostki via,* jak i jednostki docelowej.

| Klient | Kierunek | Service Bus |
| :--- | :---: | :--- |
| attach(<br/>name={nazwa linku},<br/>role=sender,<br/>source={identyfikator linku klienta},<br/>target=**{via-entity}**,<br/>**properties=map [( <br/> com.microsoft:transfer-destination-address= <br/> {destination-entity})]**) | ------> | |
| | <------ | attach(<br/>name={nazwa linku},<br/>role=receiver,<br/>source={identyfikator linku klienta},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{jednostka docelowa})]) |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi AMQP, odwiedź następujące linki:

* [Service Bus amqp overview (Omówienie usługi AMQP)]
* [Obsługa amQP 1.0 dla Service Bus partycjonowanych kolejek i tematów]
* [AMQP in Service Bus for Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus omówienie usługi AMQP]: service-bus-amqp-overview.md
[Obsługa amqp 1.0 dla Service Bus partycjonowanych kolejek i tematów]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
