---
title: Wzorce integracji z kontraktami inteligentnymi — Azure łańcucha bloków Workbench
description: Omówienie wzorców integracji kontraktu inteligentnego w usłudze Azure łańcucha bloków Workbench Preview.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015506"
---
# <a name="smart-contract-integration-patterns"></a>Wzorce integracji kontraktów inteligentnych

Inteligentne kontrakty często przedstawiają biznesowe przepływy pracy, które muszą zostać zintegrowane z systemami zewnętrznymi i urządzeniami.

Wymagania tych przepływów pracy obejmują konieczność inicjowania transakcji w księdze rozproszonej, która obejmuje dane z systemu zewnętrznego, usługi lub urządzenia. Muszą również mieć system zewnętrzny reagowanie na zdarzenia pochodzące z inteligentnych kontraktów w księdze rozproszonej.

Integracja z interfejsem API REST i obsługą komunikatów wysyła transakcje z systemów zewnętrznych do inteligentnych kontraktów zawartych w aplikacji Azure łańcucha bloków Workbench. Wysyła również powiadomienia o zdarzeniach do systemów zewnętrznych w oparciu o zmiany, które mają miejsce w aplikacji.

W przypadku scenariuszy integracji danych usługa Azure łańcucha bloków Workbench obejmuje zestaw widoków bazy danych, które scalają kombinację danych transakcyjnych z łańcucha bloków i meta-danych dotyczących aplikacji i inteligentnych kontraktów.

Ponadto niektóre scenariusze, takie jak te powiązane z łańcuchem dostaw lub nośnikiem, mogą również wymagać integracji dokumentów. Chociaż usługa Azure łańcucha bloków Workbench nie zapewnia wywołań interfejsu API do bezpośredniej obsługi dokumentów, dokumenty mogą być zawarte w aplikacji łańcucha bloków. Ta sekcja zawiera również ten wzorzec.

Ta sekcja zawiera wzorce identyfikowane w celu zaimplementowania każdego z tych typów integracji w kompleksowych rozwiązaniach.

## <a name="rest-api-based-integration"></a>Integracja oparta na interfejsie API REST

Możliwości w ramach aplikacji sieci Web wygenerowanej przez usługę Azure łańcucha bloków Workbench są udostępniane za pośrednictwem interfejsu API REST. Funkcje obejmują usługę Azure łańcucha bloków Workbench, która umożliwia przekazywanie, Konfigurowanie i administrowanie aplikacjami, wysyłanie transakcji do księgi rozproszonej oraz wykonywanie zapytań dotyczących metadanych aplikacji i danych księgi.

Interfejs API REST jest używany głównie dla klientów interaktywnych, takich jak aplikacje internetowe, mobilne i bot.

W tej sekcji przedstawiono wzorce ukierunkowane na aspekty interfejsu API REST, które wysyłają transakcje do rozproszonej księgi i wzorców, które wykonują zapytania dotyczące danych o transakcjach z bazy danych *łańcucha* usługi Azure łańcucha bloków Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Wysyłanie transakcji do księgi rozproszonej z systemu zewnętrznego

Interfejs API REST usługi Azure łańcucha bloków Workbench wysyła uwierzytelnione żądania do wykonywania transakcji w księdze rozproszonej.

![Wysyłanie transakcji do księgi rozproszonej](./media/integration-patterns/send-transactions-ledger.png)

Wykonywanie transakcji odbywa się przy użyciu przedstawionego wcześniej procesu, gdzie:

-   Aplikacja zewnętrzna jest uwierzytelniana do Azure Active Directory obsługiwanego w ramach wdrożenia usługi Azure łańcucha bloków Workbench.
-   Autoryzowani użytkownicy otrzymują token okaziciela, który można wysłać z żądaniami do interfejsu API.
-   Aplikacje zewnętrzne umożliwiają wywoływanie interfejsu API REST przy użyciu tokenu okaziciela.
-   Interfejs API REST pakuje żądanie jako komunikat i wysyła go do Service Bus. Z tego miejsca są pobierane, podpisane i wysyłane do odpowiedniej księgi rozproszonej.
-   Interfejs API REST wysyła żądanie do usługi Azure łańcucha bloków Workbench SQL DB, aby zarejestrować żądanie i ustalić bieżący stan aprowizacji.
-   Baza danych SQL zwraca stan aprowizacji, a wywołanie interfejsu API zwraca identyfikator do zewnętrznej aplikacji, która go wywołała.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Wykonywanie zapytania dotyczącego metadanych łańcucha bloków Workbench i transakcji księgi rozproszonej

Interfejs API REST usługi Azure łańcucha bloków Workbench wysyła uwierzytelnione żądania do szczegółów zapytania związanych z wykonywaniem inteligentnego kontraktu w księdze rozproszonej.

![Wykonywanie zapytania dotyczącego metadanych](./media/integration-patterns/querying-metadata.png)

Wykonywanie zapytań odbywa się przy użyciu przedstawionych wcześniej procesów, gdzie:

1. Aplikacja zewnętrzna jest uwierzytelniana do Azure Active Directory obsługiwanego w ramach wdrożenia usługi Azure łańcucha bloków Workbench.
2. Autoryzowani użytkownicy otrzymują token okaziciela, który można wysłać z żądaniami do interfejsu API.
3. Aplikacje zewnętrzne umożliwiają wywoływanie interfejsu API REST przy użyciu tokenu okaziciela.
4. Interfejs API REST wysyła zapytanie o dane żądania z bazy danych SQL i zwraca go do klienta.

## <a name="messaging-integration"></a>Integracja z obsługą wiadomości

Integracja z obsługą wiadomości ułatwia interakcję z systemami, usługami i urządzeniami, w których logowanie interakcyjne nie jest możliwe ani pożądane. Integracja z obsługą wiadomości koncentruje się na dwóch typach komunikatów: komunikaty żądające transakcji są wykonywane w księdze rozproszonej oraz zdarzenia udostępniane przez ten księgę w przypadku zrealizowania transakcji.

Integracja z obsługą komunikatów koncentruje się na wykonywaniu i monitorowaniu transakcji związanych z tworzeniem, tworzeniem kontraktu i wykonywaniem transakcji w ramach kontraktów. jest ona głównie używana przez *bezobsługowe* systemy zaplecza.

Ta sekcja analizuje wzorce ukierunkowane na aspekty interfejsu API opartego na komunikatach, które wysyłają transakcje do rozproszonej księgi i wzorców, które reprezentują komunikaty o zdarzeniach udostępniane przez podstawową księgę rozproszoną.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Jednokierunkowe dostarczanie zdarzeń od kontraktu inteligentnego do odbiorcy zdarzeń 

W tym scenariuszu zdarzenie występuje w ramach kontraktu inteligentnego, na przykład zmiany stanu lub wykonania określonego typu transakcji. To zdarzenie jest emitowane za pośrednictwem Event Grid do odbiorców podrzędnych, a Ci klienci podejmują odpowiednie działania.

Przykładowy scenariusz polega na tym, że gdy wystąpi transakcja, odbiorca będzie otrzymywać alerty i może podejmować działania, takie jak rejestrowanie informacji w bazie danych SQL lub Common Data Service. Ten scenariusz jest tym samym wzorcem, który Workbench, aby wypełnić bazę danych SQL w *łańcuchu off* .

Kolejną może być to, że inteligentne przejście kontraktu do określonego stanu, na przykład gdy kontrakt przejdzie do *OutOfCompliance*. Po zmianie tego stanu może zostać wyzwolony alert wysyłany do telefonu komórkowego administratora.

![Dostarczanie zdarzeń jednokierunkowych](./media/integration-patterns/one-way-event-delivery.png)

Ten scenariusz odbywa się przy użyciu przedstawionego wcześniej procesu, gdzie:

-   Inteligentne przejście kontraktu do nowego stanu i wysłanie zdarzenia do księgi.
-   Księga otrzymuje i dostarcza zdarzenie do usługi Azure łańcucha bloków Workbench.
-   Usługa Azure łańcucha bloków Workbench subskrybuje zdarzenia z księgi i odbiera zdarzenie.
-   Usługa Azure łańcucha bloków Workbench publikuje zdarzenie dla subskrybentów na Event Grid.
-   Systemy zewnętrzne są subskrybowane Event Grid, zużywają komunikat i podejmują odpowiednie działania.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Jednokierunkowe dostarczanie komunikatów z systemu zewnętrznego do kontraktu inteligentnego

Istnieje również scenariusz, który przepływa od przeciwnego kierunku. W takim przypadku zdarzenie jest generowane przez czujnik lub system zewnętrzny, a dane z tego zdarzenia powinny być wysyłane do kontraktu inteligentnego.

Typowym przykładem jest dostarczanie danych z rynków finansowych, na przykład cen towarów, giełd lub obligacji, do kontraktu inteligentnego.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie Workbench usługi Azure łańcucha bloków w oczekiwanym formacie

Niektóre aplikacje są kompilowane do integracji z usługą Azure łańcucha bloków Workbench i bezpośrednio generują i wysyłają komunikaty w oczekiwanych formatach.

![Bezpośrednie dostarczanie](./media/integration-patterns/direct-delivery.png)

To dostarczanie odbywa się przy użyciu przedstawionego wcześniej procesu, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, który wyzwala Tworzenie komunikatu dla usługi Azure łańcucha bloków Workbench.
-   System zewnętrzny ma kod zapisany w celu utworzenia tego komunikatu w znanym formacie i wysyła go bezpośrednio do Service Bus.
-   Usługa Azure łańcucha bloków Workbench subskrybuje zdarzenia z Service Bus i pobiera komunikat.
-   Usługa Azure łańcucha bloków Workbench inicjuje wywołanie do księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
-   Po otrzymaniu wiadomości kontrakt przechodzi do nowego stanu.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatu w formacie nieznanego do usługi Azure łańcucha bloków Workbench

Niektórych systemów nie można modyfikować w celu dostarczania komunikatów w standardowym formacie używanym przez usługę Azure łańcucha bloków Workbench. W takich przypadkach często można używać istniejących mechanizmów i formatów komunikatów z tych systemów. W tym celu typy komunikatów natywnych tych systemów można przekształcać przy użyciu Logic Apps, Azure Functions lub innego kodu niestandardowego do zamapowania na jeden z oczekiwanych standardowych formatów obsługi komunikatów.

![Nieznany format wiadomości](./media/integration-patterns/unknown-message-format.png)

Odbywa się to przy użyciu wcześniej utworzonego procesu, gdzie:

-   Zdarzenie występuje w systemie zewnętrznym, który wyzwala Tworzenie komunikatu.
-   Aplikacja logiki lub kod niestandardowy jest używany do otrzymywania wiadomości i przekształcania go w standardowy komunikat o formacie Workbench usługi Azure łańcucha bloków.
-   Aplikacja logiki wysyła przekształcony komunikat bezpośrednio do Service Bus.
-   Usługa Azure łańcucha bloków Workbench subskrybuje zdarzenia z Service Bus i pobiera komunikat.
-   Usługa Azure łańcucha bloków Workbench inicjuje wywołanie do księgi, wysyłając dane z systemu zewnętrznego do określonej funkcji na umowie.
-   Funkcja wykonuje i zwykle modyfikuje stan. Zmiana stanu przenosi do przodu przepływ pracy biznesowej odzwierciedlony w ramach kontraktu inteligentnego, co umożliwia wykonywanie innych funkcji w miarę potrzeb.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Przejście kontroli do procesu zewnętrznego i oczekiwanie na ukończenie

Istnieją scenariusze, w których kontrakt inteligentny musi zatrzymać wykonywanie wewnętrzne i przełączać się do procesu zewnętrznego. Następnie proces zewnętrzny zostanie ukończony, wyśle komunikat do kontraktu inteligentnego, a wykonywanie będzie kontynuowane w ramach kontraktu inteligentnego.

#### <a name="transition-to-the-external-process"></a>Przejście do procesu zewnętrznego

Ten wzorzec jest zwykle implementowany przy użyciu następujących metod:

-   Inteligentne przejścia kontraktu do określonego stanu. W tym stanie do momentu podjęcia odpowiedniej akcji w systemie zewnętrznym można wykonać dowolną lub ograniczoną liczbę funkcji.
-   Zmiana stanu jest określana jako zdarzenie dla użytkownika podrzędnego.
-   Odbiorca podrzędny odbiera zdarzenie i wyzwala wykonywanie kodu zewnętrznego.

![Na diagramie przedstawiono zmianę stanu w ramach kontraktu powodującego przejście zdarzenia do księgi rozproszonej. Łańcucha bloków Workbench następnie pobiera zdarzenie i publikuje je.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Powrót kontroli z kontraktu inteligentnego

W zależności od możliwości dostosowania systemu zewnętrznego może być możliwe lub nie można dostarczać komunikatów w jednym z standardowych formatów, których oczekuje usługa Azure łańcucha bloków Workbench. W oparciu o możliwość generowania jednego z tych komunikatów przez system zewnętrzny można określić, które z poniższych dwóch ścieżek zwracanych są pobierane.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Bezpośrednie dostarczanie Workbench usługi Azure łańcucha bloków w oczekiwanym formacie

![Diagram przedstawia komunikat P z systemu zewnętrznego, który jest wybierany przez łańcucha bloków Workbench za pośrednictwem Service Bus. Łańcucha bloków Workbench następnie wysyła komunikat jako transakcję do księgi rozproszonej w imieniu agenta. Jest ona przenoszona do kontraktu, gdzie powoduje zmianę stanu.](./media/integration-patterns/direct-delivery.png)

W tym modelu komunikacja z umową i późniejsza zmiana stanu odbywa się po poprzednim procesie, gdzie-

-   Po osiągnięciu ukończenia lub określonego punktu kontrolnego w zewnętrznym wykonaniu kodu zdarzenie jest wysyłane do Service Bus połączonej z usługą Azure łańcucha bloków Workbench.

-   W przypadku systemów, które nie mogą być bezpośrednio dostosowywane w celu zapisania komunikatu, który jest zgodny z oczekiwaniami interfejsu API, zostanie on przekształcony.

-   Zawartość komunikatu jest pakowana i wysyłana do określonej funkcji w ramach kontraktu inteligentnego. To dostarczanie jest wykonywane w imieniu użytkownika skojarzonego z systemem zewnętrznym.

-   Funkcja wykonuje i zwykle modyfikuje stan. Zmiana stanu przenosi do przodu przepływ pracy biznesowej odzwierciedlony w ramach kontraktu inteligentnego, co umożliwia wykonywanie innych funkcji w miarę potrzeb.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Dostarczanie komunikatu w formacie nieznanego do usługi Azure łańcucha bloków Workbench

![Nieznany format wiadomości](./media/integration-patterns/unknown-message-format.png)

W tym modelu, w którym nie można bezpośrednio wysłać wiadomości w formacie standardowym, komunikacja z umową i późniejsza zmiana stanu odbywa się po poprzednim procesie, w którym:

1.  Po osiągnięciu ukończenia lub określonego punktu kontrolnego w zewnętrznym wykonaniu kodu zdarzenie jest wysyłane do Service Bus połączonej z usługą Azure łańcucha bloków Workbench.
2.  Aplikacja logiki lub kod niestandardowy jest używany do otrzymywania wiadomości i przekształcania go w standardowy komunikat o formacie Workbench usługi Azure łańcucha bloków.
3.  Aplikacja logiki wysyła przekształcony komunikat bezpośrednio do Service Bus.
4.  Usługa Azure łańcucha bloków Workbench subskrybuje zdarzenia z Service Bus i pobiera komunikat.
5.  Usługa Azure łańcucha bloków Workbench inicjuje wywołanie do księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
6. Zawartość komunikatu jest pakowana i wysyłana do określonej funkcji w ramach kontraktu inteligentnego. To dostarczanie jest wykonywane w imieniu użytkownika skojarzonego z systemem zewnętrznym.
7.  Funkcja wykonuje i zwykle modyfikuje stan. Zmiana stanu przenosi do przodu przepływ pracy biznesowej odzwierciedlony w ramach kontraktu inteligentnego, co umożliwia wykonywanie innych funkcji w miarę potrzeb.

## <a name="iot-integration"></a>Integracja IoT

Typowy scenariusz integracji polega na dołączeniu danych telemetrycznych pobranych z czujników w ramach kontraktu inteligentnego. W oparciu o dane dostarczane przez czujniki, inteligentne kontrakty mogą podejmować świadome działania i zmieniać stan kontraktu.

Na przykład jeśli ciężarówka dostarczająca lekarstwa miała temperaturę o do 110 stopni, może to mieć wpływ na efektywność medycyny i może spowodować publiczny problem z bezpieczeństwem, jeśli nie zostanie wykryta i usunięta z łańcucha dostaw. Jeśli sterownik skrócił samochód do 100 kilometrów na godzinę, otrzymane informacje o czujniku mogły spowodować anulowanie ubezpieczenia przez dostawcę ubezpieczeń. Jeśli samochód był samochodem wynajmu, dane GPS mogą wskazywać, kiedy kierowca przeszedł poza geografię objętą umową najmu, i naliczać kary.

Wyzwanie polega na tym, że czujniki te mogą dostarczać dane w stałym zakresie i nie jest konieczne wysyłanie wszystkich tych danych do kontraktu inteligentnego. Typowym podejściem jest ograniczenie liczby komunikatów wysyłanych do łańcucha bloków podczas dostarczania wszystkich komunikatów do magazynu pomocniczego. Na przykład dostarczanie komunikatów odebranych tylko przez stały interwał, na przykład raz na godzinę, i gdy zawarte wartości wykraczają poza uzgodniony zakres dla kontraktu inteligentnego. Sprawdzanie wartości, które wykraczają poza tolerancje, zapewnia, że dane dotyczące kontraktów biznesowej są odbierane i wykonywane. Sprawdzenie wartości w interwale potwierdza, że czujnik nadal jest zgłaszany. Wszystkie dane są wysyłane do pomocniczego magazynu raportowania, aby umożliwić szersze raportowanie, analizowanie i uczenie maszynowe. Na przykład podczas uzyskiwania odczytów czujników dla GPS mogą nie być wymagane co minutę dla kontraktu inteligentnego, mogą one dostarczać interesujące dane do użycia w raportach lub mapowaniu tras.

Na platformie Azure integracja z urządzeniami zwykle odbywa się przy użyciu IoT Hub. IoT Hub zapewnia Routing komunikatów na podstawie zawartości i umożliwia wcześniejsze opisywanie funkcji.

![Wiadomości IoT](./media/integration-patterns/iot.png)

Proces przedstawia wzorzec:

-   Urządzenie komunikuje się bezpośrednio lub za pośrednictwem bramy pola, aby IoT Hub.
-   IoT Hub odbiera komunikaty i szacuje komunikaty przed ustanowionymi trasami, które sprawdzają zawartość komunikatu, na przykład. *Czy czujnik raportuje temperaturę większą niż 50 stopni?*
-   IoT Hub wysyła komunikaty, które spełniają kryteria zdefiniowane Service Bus dla trasy.
-   Aplikacja logiki lub inny kod nasłuchuje Service Bus, które IoT Hub ustalone dla trasy.
-   Aplikacja logiki lub inny kod pobiera i przekształca komunikat w znany Format.
-   Przekształcony komunikat, teraz w formacie standardowym, jest wysyłany do Service Bus usługi Azure łańcucha bloków Workbench.
-   Usługa Azure łańcucha bloków Workbench subskrybuje zdarzenia z Service Bus i pobiera komunikat.
-   Usługa Azure łańcucha bloków Workbench inicjuje wywołanie do księgi, wysyłając dane z systemu zewnętrznego do określonego kontraktu.
-   Po otrzymaniu komunikatu kontrakt ocenia dane i może zmienić stan w oparciu o wynik tej oceny, na przykład w przypadku wysokiej temperatury, zmienić stan na *niezgodny*.

## <a name="data-integration"></a>Integracja danych

Oprócz interfejsu API REST i opartego na komunikatach usługa Azure łańcucha bloków Workbench zapewnia również dostęp do bazy danych SQL z uwzględnieniem metadanych aplikacji i umowy oraz danych transakcyjnych z rozproszonych ksiąg.

![Integracja danych](./media/integration-patterns/data-integration.png)

Integracja danych jest dobrze znana:

-   Usługa Azure łańcucha bloków Workbench przechowuje metadane dotyczące aplikacji, przepływów pracy, kontraktów i transakcji w ramach normalnego działania operacyjnego.
-   Systemy zewnętrzne lub narzędzia udostępniają co najmniej jedno okno dialogowe, które ułatwia zbieranie informacji o bazie danych, takich jak nazwa serwera bazy danych, nazwa bazy danych, typ uwierzytelniania, poświadczenia logowania i widoki bazy danych do użycia.
-   Zapytania są zapisywane w oparciu o widoki bazy danych, aby ułatwić wykorzystanie przez systemy zewnętrzne, usługi, raportowanie, narzędzia deweloperskie i narzędzia do pracy w przedsiębiorstwie.

## <a name="storage-integration"></a>Integracja z magazynem

Wiele scenariuszy może wymagać zadawania plików z zaświadczeniem. Z wielu powodów nieodpowiednie jest umieszczenie plików na łańcucha bloków. Zamiast tego typowe podejście polega na wykonaniu skrótu kryptograficznego (na przykład SHA-256) względem pliku i udostępnieniu tego skrótu w księdze rozproszonej. Ponowne wykonanie skrótu w dowolnym momencie powinien zwrócić ten sam wynik. Jeśli plik jest modyfikowany, nawet jeśli tylko jeden piksel jest modyfikowany w obrazie, skrót zwraca inną wartość.

![Integracja z magazynem](./media/integration-patterns/storage-integration.png)

Wzorzec można zaimplementować, gdzie:

-   System zewnętrzny utrwala plik w mechanizmie magazynu, takim jak Azure Storage.
-   Skrót jest generowany przy użyciu pliku lub pliku oraz skojarzonych metadanych, takich jak identyfikator właściciela, adres URL, pod którym znajduje się plik itp.
-   Skrót i wszelkie metadane są wysyłane do funkcji w ramach kontraktu inteligentnego, takiego jak *FileAdded*
-   W przyszłości plik i metadane mogą być ponownie zmieszane i porównywane z wartościami przechowywanymi w księdze.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Wymagania wstępne dotyczące implementowania wzorców integracji przy użyciu protokołu REST i interfejsów API komunikatów

Aby ułatwić użytkownikom zewnętrznym lub urządzeniu współpracę z inteligentnym kontraktem przy użyciu interfejsu API REST lub wiadomości, należy wykonać następujące czynności:

1. W Azure Active Directory dla konsorcjum tworzone jest konto, które reprezentuje system zewnętrzny lub urządzenie.
2. Co najmniej jedna z odpowiednich inteligentnych kontraktów dla aplikacji Azure łańcucha bloków Workbench ma funkcje zdefiniowane do akceptowania zdarzeń z systemu zewnętrznego lub urządzenia.
3. Plik konfiguracyjny aplikacji dla kontraktu inteligentnego zawiera rolę, do której przypisano system lub urządzenie.
4. Plik konfiguracji aplikacji dla kontraktu inteligentnego określa, w jaki sposób ta funkcja jest wywoływana przez określoną rolę.
5. Plik konfiguracji aplikacji i jego inteligentne kontrakty są przekazywane do usługi Azure łańcucha bloków Workbench.

Po przekazaniu aplikacji konto Azure Active Directory dla systemu zewnętrznego zostanie przypisane do kontraktu i skojarzonej roli.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testowanie przepływów integracji systemu zewnętrznego przed pisaniem kodu integracji 

Integracja z systemami zewnętrznymi jest kluczem wymaganym przez wiele scenariuszy. Pożądane jest, aby można było zweryfikować projekt inteligentny z umową przed lub równolegle z programowaniem kodu w celu integracji z systemami zewnętrznymi.

Korzystanie z Azure Active Directory (Azure AD) może znacznie przyspieszyć produktywność deweloperów i czas na wartość. W celu integracji kodu z systemem zewnętrznym może trwać nieuproszczony czas. Korzystając z usługi Azure AD i automatycznej generacji środowiska użytkownika przez usługę Azure łańcucha bloków Workbench, można zezwolić deweloperom na logowanie się do łańcucha bloków Workbench jako system zewnętrzny i wypełnianie wartości z systemu zewnętrznego za pośrednictwem środowiska użytkownika. Możesz szybko opracowywać i weryfikować pomysły w celu sprawdzenia środowiska koncepcji przed zapisaniem kodu integracji dla systemów zewnętrznych.
