---
title: Przewodnik po protokole Połączenia hybrydowe Azure Relay | Microsoft Docs
description: W tym artykule opisano interakcje po stronie klienta z usługą Połączenia hybrydowe Relay do łączenia klientów w rolach odbiornika i nadawcy.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 36321f88de173a37c9aa6615c4c0f2b29aec9f20
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976966"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Protokół Połączenia hybrydowe Azure Relay

Azure Relay to jeden z najważniejszych filarów funkcji Azure Service Bus platform. Nowe _połączenia hybrydowe_ możliwości przekazywania to bezpieczna, dwukierunkowa ewolucja, oparta na protokołach http i WebSockets. Zastępuje ona dawną funkcję o nazwie _BizTalk Services_ , która została zbudowana na mocy własnościowego protokołu. Integracja Połączenia hybrydowe z platformą Azure App Services będzie nadal działać zgodnie z oczekiwaniami.

Połączenia hybrydowe umożliwia komunikację dwukierunkową, odpowiedź typu żądanie i strumień binarny oraz prosty przepływ datagramu między dwiema aplikacjami sieciowymi. Obie strony mogą znajdować się za translatorem adresów sieciowych lub zaporami.

W tym artykule opisano interakcje po stronie klienta z usługą Połączenia hybrydowe Relay do łączenia klientów w rolach odbiornika i nadawcy. Opisano w nim również, jak odbiorniki akceptują nowe połączenia i żądania.

## <a name="interaction-model"></a>Model interakcji

Przekaźnik Połączenia hybrydowe łączy dwie strony, dostarczając termin w chmurze platformy Azure, który strony mogą odnajdywać i łączyć się z perspektywą sieci. Termin "połączenie hybrydowe" jest określany w tej i innej dokumentacji, w interfejsach API, a także w Azure Portal. Punkt końcowy usługi Połączenia hybrydowe jest określany jako "usługa" w dalszej części tego artykułu.

Usługa umożliwia przekazywanie połączeń gniazd sieci Web oraz żądań i odpowiedzi HTTP (S).

Model interakcji jest oszczędny dla nomenklatury ustalonej przez wiele innych interfejsów API sieci. Istnieje odbiornik, który najpierw wskazuje gotowość do obsługi połączeń przychodzących, a następnie akceptuje je po nadejściu. Po drugiej stronie klient nawiązuje połączenie z odbiornikiem, co oczekuje na zaakceptowanie tego połączenia do ustanowienia dwukierunkowej ścieżki komunikacji. "Connect", "nasłuchuj" i "Akceptuj" są te same warunki, które znajdują się w większości interfejsów API gniazda.

Każdy przekazany model komunikacji obejmuje każdą ze stron nawiązywania połączeń wychodzących do punktu końcowego usługi. Dzięki temu "odbiornik" jest również "klientem" w Colloquial, a także może spowodować inne przeciążenia terminologii. W związku z tym do Połączenia hybrydowe jest używana dokładna terminologia:

Programy po obu stronach połączenia są nazywane "klientami", ponieważ są klientami usługi. Klient, który czeka na i akceptuje połączenia, jest "odbiornikiem" lub mówi, że należy do "roli odbiornika". Klient inicjujący nowe połączenie w kierunku odbiornika za pośrednictwem usługi nosi nazwę "nadawca" lub znajduje się w "roli nadawcy".

### <a name="listener-interactions"></a>Interakcje odbiornika

Odbiornik ma pięć interakcji z usługą; wszystkie szczegóły dotyczące sieci są opisane w dalszej części tego artykułu w sekcji Dokumentacja.

Komunikaty Listen, Accept i Request są odbierane z usługi. Operacje odnawiania i ping są wysyłane przez odbiornik.

#### <a name="listen-message"></a>Komunikat o nasłuchiwaniu

Aby wskazać gotowość usługi do akceptowania połączeń przez odbiornik, tworzy połączenie wychodzące protokołu WebSocket. Uzgadnianie połączenia przenosi nazwę połączenia hybrydowego skonfigurowanego w przestrzeni nazw usługi Relay oraz token zabezpieczający, który przydaje "nasłuchiwanie" w tej nazwie.

Gdy protokół WebSocket zostanie zaakceptowany przez usługę, rejestracja zostanie zakończona, a ustanowiony protokół WebSocket jest aktywny jako "Kanał kontrolny" służący do włączania wszystkich kolejnych interakcji. Usługa umożliwia maksymalnie 25 współbieżnych odbiorników dla jednego połączenia hybrydowego. Należy określić przydział dla AppHooks.

W przypadku Połączenia hybrydowe, jeśli istnieją co najmniej dwa aktywne detektory, połączenia przychodzące są równoważony między nimi w kolejności losowej; w celu uzyskania najlepszego wysiłku podjęto próbę uczciwej dystrybucji.

#### <a name="accept-message"></a>Akceptuj komunikat

Gdy nadawca otworzy nowe połączenie z usługą, usługa wybiera i powiadamia jeden z aktywnych odbiorników w połączeniu hybrydowym. To powiadomienie jest wysyłane do odbiornika przez otwarty kanał kontrolny jako komunikat JSON. Komunikat zawiera adres URL punktu końcowego protokołu WebSocket, z którym odbiornik musi nawiązać połączenie w celu zaakceptowania połączenia.

Adres URL może i musi być używany bezpośrednio przez odbiornik bez żadnej dodatkowej pracy.
Zakodowane informacje są prawidłowe tylko przez krótki czas, głównie w przypadku, gdy nadawca chce czekać na zakończenie ustanawiania połączenia. Maksymalna wartość jest równa 30 sekund. Adresu URL można użyć tylko dla jednej próby nawiązania połączenia. Gdy tylko zostanie nawiązane połączenie protokołu WebSocket z terminem URL, wszystkie dalsze działania dotyczące tego obiektu WebSocket są przekazywane od i do nadawcy. Takie zachowanie ma miejsce bez jakichkolwiek interwencji ani interpretacji przez usługę.

### <a name="request-message"></a>Komunikat żądania

Oprócz połączeń protokołu WebSocket odbiornik może również odbierać ramki żądań HTTP od nadawcy, jeśli ta funkcja jest jawnie włączona w połączeniu hybrydowym.

Odbiorniki, które dołączają do Połączenia hybrydowe z obsługą protokołu HTTP, muszą obsługiwać `request` gest. Odbiornik, który nie obsługuje `request` i w związku z tym powoduje, że powtarzające się błędy przekroczenia limitu czasu podczas połączenia mogą zostać zablokowane przez usługę w przyszłości.

Metadane nagłówka ramki HTTP są tłumaczone na format JSON w celu uproszczenia obsługi przez platformę odbiornika, również ponieważ biblioteki analizy nagłówków HTTP są rzadkie niż analizatory JSON. Metadane HTTP, które mają zastosowanie tylko do relacji między nadawcą i bramą protokołu HTTP przekaźnika, w tym informacje o autoryzacji, nie są przekazywane dalej. Treść żądania HTTP jest w niewidoczny sposób transferowana jako binarne ramki protokołu WebSocket.

Odbiornik może odpowiadać na żądania HTTP przy użyciu równoważnego gestu odpowiedzi.

Przepływ żądania/odpowiedzi domyślnie używa kanału kontroli, ale może być "uaktualniony" do odrębnego terminu protokołu WebSocket w razie potrzeby. Różne połączenia protokołu WebSocket zwiększają przepływność dla każdej konwersacji klienta, ale ciężarją odbiornik z większą liczbą połączeń, które muszą być obsługiwane, co może nie być możliwe w przypadku lekkich klientów.

W przypadku kanału kontroli jednostki żądań i odpowiedzi są ograniczone do maksymalnie 64 kB. Metadane nagłówka HTTP są ograniczone do całkowitej 32 kB. Jeśli żądanie lub odpowiedź przekracza ten próg, odbiornik musi uaktualnić do terminowego protokołu WebSocket przy użyciu gestu równoważnego do obsługi [akceptacji](#accept-message).

W przypadku żądań usługa decyduje o kierowaniu żądań do kanału kontroli. Obejmuje to, ale mogą nie być ograniczone do przypadków, gdy żądanie przekracza 64 kB (nagłówki Plus treść) z prawej strony, lub jeśli żądanie jest wysyłane z użyciem ["fragmentarycznego" kodowania transferu](https://tools.ietf.org/html/rfc7230#section-4.1) , a usługa ma powód, aby żądanie przekroczy 64 KB lub odczytywać żądanie nie jest chwilowo wykonywane. Jeśli usługa zdecyduje się na dostarczenie żądania na życzenie, przekazuje on tylko ten adres.
Odbiornik musi ustalić termin protokołu WebSocket, a usługa bezzwłocznie dostarcza pełne żądanie, w tym treści w terminach protokołu WebSocket. W odpowiedzi musi również być używany termin WebSocket.

W przypadku żądań, które docierają do kanału kontroli, odbiornik decyduje o tym, czy odpowiedzieć na kanał kontrolny, czy przez termin. Usługa musi zawierać adres dla każdego żądania kierowanego w kanale kontroli. Ten adres jest prawidłowy tylko w przypadku uaktualniania z bieżącego żądania.

Jeśli odbiornik zdecyduje się na uaktualnienie, nawiązuje połączenie i natychmiast dostarcza odpowiedź w gnieździe utworzone termin.

Po ustanowieniu dla tego celu obiektu WebSocket odbiornik powinien go zachować do dalszej obsługi żądań i odpowiedzi z tego samego klienta. Usługa będzie obsługiwać protokół WebSocket, tak długo, jak połączenie gniazda HTTPS z nadawcą utrzymuje się i będzie kierować wszystkie kolejne żądania od tego nadawcy przez obsługiwany protokół WebSocket. Jeśli odbiornik wybierze opcję porzucenia obiektu WebSocket z jego strony, usługa spowoduje również porzucenie połączenia z nadawcą niezależnie od tego, czy kolejne żądanie może już być w toku.

#### <a name="renew-operation"></a>Odnów operację

Token zabezpieczający, który musi zostać użyty do zarejestrowania odbiornika i utrzymania kanału kontroli, może wygasnąć, gdy odbiornik jest aktywny. Wygaśnięcie tokenu nie ma wpływu na bieżące połączenia, ale powoduje, że kanał kontrolny zostanie porzucony przez usługę na lub wkrótce po chwili wygaśnięcia. Operacja "Odnów" jest komunikatem JSON, który odbiornik może wysłać, aby zastąpić token skojarzony z kanałem kontroli, dzięki czemu Kanał kontrolny może być utrzymywany przez dłuższy okres.

#### <a name="ping-operation"></a>Operacja ping

Jeśli kanał kontrolny pozostaje bezczynny przez długi czas, pośredników w taki sposób, jak moduły równoważenia obciążenia lub translatory adresów sieciowych, mogą porzucić połączenie TCP. Operacja "ping" pozwala uniknąć tego, wysyłając niewielką ilość danych w kanale, który przypomina każdą z tras sieciowych, że połączenie ma być aktywne, a także służy jako test "na żywo" dla odbiornika. Jeśli polecenie ping nie powiedzie się, Kanał kontrolny powinien być traktowany jako niezdatny do użytku, a odbiornik powinien ponownie nawiązać połączenie.

### <a name="sender-interaction"></a>Interakcja nadawcy

Nadawca ma dwa interakcje z usługą: łączy gniazdo internetowe lub wysyła żądania za pośrednictwem protokołu HTTPS. Nie można wysyłać żądań za pośrednictwem gniazda internetowego z roli nadawcy.

#### <a name="connect-operation"></a>Operacja połączenia

Operacja "Połącz" otwiera obiekt WebSocket w usłudze, podając nazwę połączenia hybrydowego i (opcjonalnie, ale wymagane domyślnie) token zabezpieczający przywołujący uprawnienie "Send" w ciągu zapytania. Usługa następnie współdziała z odbiornikiem w sposób opisany wcześniej, a odbiornik tworzy termin połączenia, który jest przyłączony do tego obiektu WebSocket. Po zaakceptowaniu protokołu WebSocket wszystkie dalsze interakcje z tym obiektem WebSocket są połączone z odbiornikiem.

#### <a name="request-operation"></a>Operacja żądania

W przypadku Połączenia hybrydowe, dla którego funkcja została włączona, nadawca może wysyłać wiele nieograniczonych żądań HTTP do odbiorników.

Z wyjątkiem tokenu dostępu przekazującego, który jest osadzony w ciągu zapytania lub w nagłówku HTTP żądania, przekaźnik jest w pełni przezroczysty dla wszystkich operacji HTTP na adresie przekaźnika i wszystkich sufiksów ścieżki adresu przekaźnika, pozostawiając odbiornik w pełni kontrolujące autoryzację na całej trasie, a nawet funkcje rozszerzenia HTTP, takie jak [CORS](https://www.w3.org/TR/cors/).

Autoryzacja nadawcy za pomocą punktu końcowego przekazywania jest domyślnie włączona, ale jest OPCJONALNa. Właściciel połączenia hybrydowego może zezwalać nadawcom anonimowym. Usługa przechwytuje, sprawdza i dzieli informacje o autoryzacji w następujący sposób:

1. Jeśli ciąg zapytania zawiera `sb-hc-token` wyrażenie, wyrażenie będzie zawsze usuwane z ciągu zapytania. Zostanie ona oceniona, jeśli autoryzacja przekazywania jest włączona.
2. Jeśli nagłówki żądania zawierają `ServiceBusAuthorization` nagłówek, wyrażenie nagłówka będzie zawsze usuwane z kolekcji nagłówków.
   Zostanie ona oceniona, jeśli autoryzacja przekazywania jest włączona.
3. Tylko wtedy, gdy autoryzacja przekazywania jest włączona, a nagłówki żądania zawierają `Authorization` nagłówek, a żaden z poprzednich wyrażeń nie jest obecny, nagłówek zostanie obliczony i usunięty. W przeciwnym razie `Authorization` jest zawsze przenoszona w niezmienionej postaci.

Jeśli nie ma aktywnego odbiornika, usługa zwróci kod błędu 502 "Zła brama". Jeśli usługa nie wydaje się obsłużyć żądania, usługa zwróci 504 "limit czasu bramy" po 60 sekundach.

### <a name="interaction-summary"></a>Podsumowanie interakcji

Ten model interakcji polega na tym, że klient nadawcy wyjdzie z uzgadniania za pomocą protokołu WebSocket "Clean", który jest połączony z odbiornikiem i nie wymaga dalszych preambuły ani przygotowania. Ten model pozwala praktycznie dowolnej istniejącej implementacji klienta protokołu WebSocket korzystać z usługi Połączenia hybrydowe, dostarczając poprawnie skonstruowany adres URL do warstwy klienta WebSocket.

Termin, przez jaki odbiornik, który uzyskuje dostęp, jest również czyszczony i może być dołączany do dowolnej istniejącej implementacji serwera WebSocket z niewielkim minimalnym abstrakcją, która rozróżnia operacje "Akceptuj" w odniesieniu do zdalnych odbiorników sieci w ramach platformy i Połączenia hybrydowe "Akceptuj".

Model żądanie/odpowiedź HTTP daje nadawcy w dużym stopniu nieograniczoną powierzchnię protokołu HTTP z OPCJONALNą warstwą autoryzacji. Odbiornik pobiera wstępnie przeanalizowane sekcje nagłówka żądania HTTP, które mogą zostać przywrócone do podrzędnego żądania HTTP lub obsługiwane w postaci, w której ramki binarne obsługujące treści HTTP. Odpowiedzi używają tego samego formatu. Interakcje z mniej niż 64 KB treści żądania i odpowiedzi mogą być obsługiwane przez pojedyncze gniazdo sieci Web udostępniane wszystkim nadawcom. Większe żądania i odpowiedzi można obsługiwać przy użyciu modelu termin.

## <a name="protocol-reference"></a>Odwołanie do protokołu

W tej sekcji opisano szczegółowe informacje o interakcjach protokołu opisanych wcześniej.

Wszystkie połączenia protokołu WebSocket są nawiązywane na porcie 443 w ramach uaktualnienia z protokołu HTTPS 1,1, który jest powszechnie abstrakcyjny przez niektóre interfejsy lub interfejsy API protokołu WebSocket. Opis jest zachowywany jako neutralny od implementacji, bez sugerowania określonej struktury.

### <a name="listener-protocol"></a>Protokół odbiornika

Protokół odbiornika składa się z dwóch gestów połączenia i trzech operacji komunikatów.

#### <a name="listener-control-channel-connection"></a>Połączenie kanału kontroli odbiornika

Kanał kontrolny jest otwierany przy tworzeniu połączenia WebSocket z:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address`Jest to w pełni kwalifikowana nazwa domeny przestrzeni nazw Azure Relay, która hostuje połączenie hybrydowe, zazwyczaj formularz `{myname}.servicebus.windows.net` .

Opcje parametrów ciągu zapytania są następujące.

| Parametr        | Wymagane | Opis
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Tak      | Dla roli odbiornika parametr musi mieć wartość **SB-HC-Action = Listen**
| `{path}`         | Tak      | Ścieżka przestrzeni nazw zakodowana w adresie URL wstępnie skonfigurowanego połączenia hybrydowego do zarejestrowania tego odbiornika. To wyrażenie jest dołączane do `$hc/` części stałej ścieżki.
| `sb-hc-token`    | Tak\*    | Odbiornik musi podać prawidłowy, zakodowany w adresie URL Service Bus token dostępu współdzielonego dla przestrzeni nazw lub połączenia hybrydowego, które przyznaje prawo **nasłuchiwanie** .
| `sb-hc-id`       | Nie       | Ten opcjonalny identyfikator dostarczony przez klienta umożliwia kompleksowe śledzenie diagnostyczne.

Jeśli połączenie z protokołem WebSocket nie powiedzie się z powodu braku rejestracji ścieżki połączenia hybrydowego lub nieprawidłowego lub brakującego tokenu lub innego błędu, opinia o błędzie jest podawana przy użyciu zwykłego modelu opinii o stanie HTTP 1,1. Opis stanu zawiera identyfikator śledzenia błędów, który może być przekazywany do działu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Brak tokenu zabezpieczeń lub jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden      | Token zabezpieczający jest nieprawidłowy dla tej ścieżki dla tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze.

Jeśli połączenie z protokołem WebSocket jest celowo zamykane przez usługę po jej początkowym skonfigurowaniu, powód tego problemu jest przekazywany przy użyciu odpowiedniego kodu błędu protokołu WebSocket wraz z opisowym komunikatem o błędzie, który zawiera również identyfikator śledzenia. Usługa nie będzie zamykać kanału kontroli bez napotkania warunku błędu. Każde czyste zamknięcie jest kontrolowane przez klienta.

| Stan usługi WS | Opis
| --------- | -------------------------------------------------------------------------------
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczający wygasł, dlatego zasady autoryzacji zostały naruszone.
| 1011      | Wystąpił problem w usłudze.

#### <a name="accept-handshake"></a>Akceptuj uzgadnianie

Powiadomienie "Accept" jest wysyłane przez usługę do odbiornika przez wcześniej ustanowiony kanał kontroli jako komunikat JSON w ramce tekstowej protokołu WebSocket. Brak odpowiedzi na tę wiadomość.

Komunikat zawiera obiekt JSON o nazwie "Accept", który w tym momencie definiuje następujące właściwości:

* **Address** — ciąg adresu URL, który ma być używany do ustanawiania protokołu WebSocket do usługi w celu zaakceptowania połączenia przychodzącego.
* **ID** — unikatowy identyfikator dla tego połączenia. Jeśli identyfikator został dostarczony przez klienta nadawcy, jest to wartość dostarczona przez nadawcę, w przeciwnym razie jest wartością wygenerowaną przez system.
* **connectHeaders** — wszystkie nagłówki HTTP, które zostały dostarczone do punktu końcowego przekazywania przez nadawcę, które obejmują również sek-WebSocket-Protocol i sek--Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

Adres URL adresu podany w komunikacie JSON jest używany przez odbiornik do ustanowienia protokołu WebSocket do akceptowania lub odrzucania gniazda nadawcy.

##### <a name="accepting-the-socket"></a>Akceptowanie gniazda

W celu zaakceptowania odbiornik nawiązuje połączenie z użyciem protokołu WebSocket z podanym adresem.

Jeśli komunikat "Accept" `Sec-WebSocket-Protocol` jest w nagłówku, oczekuje się, że odbiornik akceptuje tylko protokół WebSocket, jeśli obsługuje ten protokołu. Ponadto ustawia nagłówek jako protokół WebSocket.

To samo dotyczy `Sec-WebSocket-Extensions` nagłówka. Jeśli struktura obsługuje rozszerzenie, należy ustawić nagłówek na odpowiedź po stronie serwera dla wymaganego `Sec-WebSocket-Extensions` uzgadniania dla rozszerzenia.

Adres URL musi być używany jako — służy do ustanawiania gniazda akceptującego, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | Aby można było zaakceptować gniazdo, parametr musi być `sb-hc-action=accept`
| `{path}`       | Tak      | (zobacz poniższy akapit)
| `sb-hc-id`     | Nie       | Zobacz poprzedni opis **identyfikatora**.

`{path}` jest ścieżką przestrzeni nazw zakodowaną w adresie URL wstępnie skonfigurowanego połączenia hybrydowego, na którym ma zostać zarejestrowany ten odbiornik. To wyrażenie jest dołączane do `$hc/` części stałej ścieżki.

`path`Wyrażenie może być rozszerzone z sufiksem i wyrażeniem ciągu zapytania, które następuje po nazwie zarejestrowanej po oddzieleniu kreski ułamkowej.
Ten parametr umożliwia klientowi wysyłającemu przekazywanie argumentów wysyłania do odbiornika akceptującego, gdy nie można uwzględnić nagłówków HTTP. Oczekuje się, że struktura odbiornika analizuje część stałej ścieżki i zarejestrowaną nazwę ze ścieżki i tworzy resztę, prawdopodobnie bez argumentów ciągu zapytania, które są poprzedzone przez `sb-` , dostępne dla aplikacji w celu podjęcia decyzji o zaakceptowaniu połączenia.

Więcej informacji można znaleźć w sekcji "Protokół nadawcy".

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób:

| Kod | Błąd          | Opis
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Adres URL jest nieprawidłowy.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze

 Po nawiązaniu połączenia serwer zamyka protokół WebSocket, gdy obiekt WebSocket nadawcy zostanie zamknięty lub z następującym stanem:

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamknie połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczający wygasł, dlatego zasady autoryzacji zostały naruszone. |
| 1011      | Wystąpił problem w usłudze.                                            |

##### <a name="rejecting-the-socket"></a>Odrzucanie gniazda

 Odrzucanie gniazda po sprawdzeniu `accept` wiadomości wymaga podobnego uzgodnienia, aby kod stanu i opis stanu, który komunikują się z przyczyną odrzucenia, może przepływać do nadawcy.

 Wybór projektu protokołu to tutaj, aby użyć uzgadniania protokołu WebSocket (który jest przeznaczony do końca w określonym stanie błędu), dzięki czemu implementacje klienta odbiornika mogą nadal polegać na kliencie protokołu WebSocket i nie muszą korzystać z dodatkowego klienta systemu operacyjnego.

 Aby odrzucić gniazdo, klient Pobiera identyfikator URI adresu z `accept` komunikatu i dołącza dwa parametry ciągu zapytania do niego w następujący sposób:

| Param                   | Wymagane | Opis                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-statusCode        | Tak      | Liczbowy kod stanu HTTP.                |
| SB-HC-statusDescription | Tak      | Powód odczytania przez człowieka. |

Otrzymany identyfikator URI jest następnie używany do nawiązywania połączenia z użyciem protokołu WebSocket.

Po poprawnym ukończeniu tego uzgadniania celowe nie powiedzie się z kodem błędu HTTP 410, ponieważ nie ustanowiono protokołu WebSocket. Jeśli coś się nie stało, następujące kody opisują błąd:

| Kod | Błąd          | Opis                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Adres URL jest nieprawidłowy.                |
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze. |

#### <a name="request-message"></a>Komunikat żądania

`request`Wiadomość jest wysyłana przez usługę do odbiornika w kanale kontroli. Ten sam komunikat jest również wysyłany w momencie ustanowienia protokołu WebSocket.

`request`Składa się z dwóch części: nagłówka i binarnych ramek treści.
W przypadku braku treści ramki treści są pomijane. Właściwość Boolean `body` wskazuje, czy treść jest obecna w komunikacie żądania.

W przypadku żądania z treścią żądania struktura może wyglądać następująco:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Odbiornik musi obsługiwać otrzymywanie treści żądania rozdzielonej między wiele ramek binarnych (zobacz [fragmenty protokołu WebSocket](https://tools.ietf.org/html/rfc6455#section-5.4)).
Żądanie kończy się po odebraniu ramki binarnej z zestawem flag FIN.

Dla żądania bez treści istnieje tylko jedna ramka tekstowa.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

Zawartość JSON dla programu `request` jest następująca:

* ciąg **adresu** URI. Jest to termin, który ma być używany dla tego żądania. Jeśli żądanie przychodzące ma rozmiar większy niż 64 kB, pozostała część tego komunikatu pozostaje pusta, a klient musi zainicjować uzgodnienie terminowe równoważne `accept` operacji opisanej poniżej. Następnie usługa zostanie ukończona `request` na ustanowionym gnieździe sieci Web. Jeśli oczekiwana ilość odpowiedzi może przekroczyć 64 kB, odbiornik musi również inicjować uzgadnianie terminów, a następnie przenieść odpowiedź przez ustanowione gniazdo sieci Web.
* **ID** — ciąg. Unikatowy identyfikator dla tego żądania.
* **requestHeaders** — ten obiekt zawiera wszystkie nagłówki HTTP, które zostały dostarczone do punktu końcowego przez nadawcę, z wyjątkiem informacji o autoryzacji, jak wyjaśniono [powyżej](#request-operation), oraz nagłówków, które ściśle odnoszą się do połączenia z bramą. W konkretnym przypadku wszystkie nagłówki zdefiniowane lub zarezerwowane w [RFC7230](https://tools.ietf.org/html/rfc7230), z wyjątkiem `Via` , są usuwane i nie są przekazywane:

  * `Connection` (RFC7230, sekcja 6,1)
  * `Content-Length`  (RFC7230, sekcja 3.3.2)
  * `Host`  (RFC7230, sekcja 5,4)
  * `TE`  (RFC7230, sekcja 4,3)
  * `Trailer`  (RFC7230, sekcja 4,4)
  * `Transfer-Encoding`  (RFC7230, sekcja 3.3.1)
  * `Upgrade` (RFC7230, sekcja 6,7)
  * `Close`  (RFC7230, sekcja 8,1)

* **requestTarget** — ciąg. Ta właściwość zawiera  ["cel żądania" (RFC7230, sekcja 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) żądania. Zawiera część ciągu zapytania, która jest usuwana ze wszystkich parametrów z `sb-hc-` prefiksem.
* **Metoda** -ciąg. Jest to metoda żądania, na [RFC7231, sekcja 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT`Metoda nie może być używana.
* **Body** — wartość logiczna. Wskazuje, czy jedna lub więcej ramek treści binarnych jest następująca.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Odpowiadanie na żądania

Odbiorca musi odpowiedzieć. Powtarzający się błąd odpowiedzi na żądania podczas utrzymywania połączenia może spowodować zablokowanie odbiornika.

Odpowiedzi mogą być wysyłane w dowolnej kolejności, ale każde żądanie musi być odpowiedziane w ciągu 60 sekund, a dostarczenie zostanie zgłoszone jako zakończone niepowodzeniem. Termin 60 sekund jest liczony do momentu `response` otrzymania ramki przez usługę. Ciągła odpowiedź z wieloma ramkami binarnymi nie może stać się bezczynna przez ponad 60 sekund lub została przerwana.

Jeśli żądanie jest odbierane za pośrednictwem kanału kontroli, odpowiedź musi być wysłana w kanale kontroli, z którego żądanie zostało odebrane, lub musi zostać wysłane przez kanał terminowy.

Odpowiedź jest obiektem JSON o nazwie "Response". Reguły obsługi zawartości treści są dokładnie takie same jak w przypadku `request` wiadomości i oparte na `body` właściwości.

* Identyfikator **żądania** — ciąg. Wymagane. `id`Wartość właściwości `request` komunikatu, do którego ma zostać odtworzona odpowiedź.
* **StatusCode** — numer. Wymagane. liczbowy kod stanu HTTP, który wskazuje wynik powiadomienia. Wszystkie kody stanu [RFC7231, sekcja 6](https://tools.ietf.org/html/rfc7231#section-6) jest dozwolony, z wyjątkiem [502 "Zła brama"](https://tools.ietf.org/html/rfc7231#section-6.6.3) i [504 "limit czasu bramy"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** — ciąg. Obowiązkowe. Stan HTTP — fraza przyczyny kodu na [RFC7230, sekcja 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **ResponseHeaders** — nagłówki HTTP, które mają być ustawiane w zewnętrznej odpowiedzi HTTP.
  Tak jak w przypadku `request` , RFC7230 zdefiniowane nagłówki nie mogą być używane.
* **Body** — wartość logiczna. Wskazuje, czy elementy binarne ramek treści są następujące:

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Reagowanie przez termin

W przypadku odpowiedzi o wartości przekraczającej 64 kB odpowiedź musi zostać dostarczona przez gniazdo. Ponadto, jeśli żądanie przekroczy 64 kB, a `request` jedyne zawiera pole adresu, do uzyskania `request` . Po nawiązaniu tego celu należy dostarczyć odpowiedzi do odpowiedniego klienta i kolejnych żądań od tego odpowiedniego klienta w trakcie jego działania.

`address`Adres URL w programie `request` musi być używany jako-jest przeznaczony do tworzenia gniazda termin, ale zawiera następujące parametry:

| Parametr      | Wymagane | Opis
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Tak      | Aby można było zaakceptować gniazdo, parametr musi być `sb-hc-action=request`

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób:

| Kod | Błąd           | Opis
| ---- | --------------- | -----------------------------------
| 400  | Nieprawidłowe żądanie | Nierozpoznana akcja lub nieprawidłowy adres URL.
| 403  | Forbidden       | Adres URL wygasł.
| 500  | Błąd wewnętrzny  | Wystąpił problem w usłudze

 Po nawiązaniu połączenia serwer zamknie protokół WebSocket, gdy gniazdo HTTP klienta zostanie zamknięte lub z następującym stanem:

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Klient nadawcy zamknie połączenie.                                    |
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.                        |
| 1008      | Token zabezpieczający wygasł, dlatego zasady autoryzacji zostały naruszone. |
| 1011      | Wystąpił problem w usłudze.                                            |


#### <a name="listener-token-renewal"></a>Odnowienie tokenu odbiornika

Gdy token odbiornika wkrótce wygaśnie, może go zastąpić, wysyłając wiadomość z ramką tekstową do usługi za pośrednictwem ustalonego kanału kontroli. Wiadomość zawiera obiekt JSON o nazwie `renewToken` , który w tym momencie definiuje następującą właściwość:

* **token** — prawidłowy, zakodowany w adresie URL Service Bus token dostępu współdzielonego dla przestrzeni nazw lub połączenia hybrydowego, które przyznaje prawo do **nasłuchiwania** .

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

W przypadku niepowodzenia walidacji tokenu następuje odmowa dostępu, a usługa w chmurze zamyka protokół WebSocket kanału kontroli z błędem. W przeciwnym razie nie ma odpowiedzi.

| Stan usługi WS | Opis                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Token zabezpieczający wygasł, dlatego zasady autoryzacji zostały naruszone. |

### <a name="web-socket-connect-protocol"></a>Protokół połączenia z gniazdem internetowym

Protokół nadawcy jest efektywnie identyczny z sposobem ustanowienia odbiornika.
Celem jest maksymalny stopień przejrzystości dla kompleksowego protokołu WebSocket. Adres, z którym jest nawiązywane połączenie, jest taki sam jak dla odbiornika, ale "Akcja" różni się, a token wymaga innego uprawnienia:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

_Przestrzeń nazw_ jest w pełni kwalifikowaną nazwą domeny Azure Relay przestrzeni nazw, która hostuje połączenie hybrydowe, zazwyczaj formularz `{myname}.servicebus.windows.net` .

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym zdefiniowane przez aplikację. Wszystkie przekazane nagłówki są przesyłane do odbiornika i można je znaleźć na `connectHeader` obiekcie komunikatu o **akceptacji** kontroli.

Opcje parametrów ciągu zapytania są następujące:

| Param          | Wymagane? | Opis
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Tak       | Dla roli nadawcy parametr musi mieć wartość `sb-hc-action=connect` .
| `{path}`       | Tak       | (zobacz poniższy akapit)
| `sb-hc-token`  | Tak\*     | Odbiornik musi podać prawidłowy, zakodowany w adresie URL Service Bus token dostępu współdzielonego dla przestrzeni nazw lub połączenia hybrydowego, które przyznaje prawo do **wysyłania** .
| `sb-hc-id`     | Nie        | Opcjonalny identyfikator, który umożliwia kompleksowe śledzenie diagnostyczne i jest udostępniany odbiornikowi podczas uzgadniania akceptacji.

 `{path}`Jest ścieżką przestrzeni nazw zakodowaną w adresie URL wstępnie skonfigurowanego połączenia hybrydowego, na którym ma zostać zarejestrowany ten odbiornik. `path`Wyrażenie można rozszerzyć za pomocą sufiksu i wyrażenia ciągu zapytania w celu dalszej komunikacji. Jeśli połączenie hybrydowe jest zarejestrowane pod ścieżką `hyco` , `path` po wyrażeniu mogą występować `hyco/suffix?param=value&...` parametry ciągu zapytania zdefiniowane w tym miejscu. Kompletne wyrażenie może następnie być następujące:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

`path`Wyrażenie jest przesyłane do odbiornika w identyfikatorze URI adresu zawartego w komunikacie kontroli "Accept".

Jeśli połączenie z protokołem WebSocket nie powiedzie się z powodu braku rejestracji ścieżki połączenia hybrydowego, nieprawidłowego lub brakującego tokenu lub innego błędu, opinia o błędzie jest podawana przy użyciu zwykłego modelu opinii o stanie HTTP 1,1. Opis stanu zawiera identyfikator śledzenia błędów, który może być przekazywany do działu pomocy technicznej platformy Azure:

| Kod | Błąd          | Opis
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nie znaleziono      | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji   | Brak tokenu zabezpieczeń lub jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden      | Token zabezpieczający jest nieprawidłowy dla tej ścieżki i dla tej akcji.
| 500  | Błąd wewnętrzny | Wystąpił problem w usłudze.

Jeśli połączenie z protokołem WebSocket jest celowo zamykane przez usługę po jej początkowym skonfigurowaniu, powód tego problemu jest przekazywany przy użyciu odpowiedniego kodu błędu protokołu WebSocket wraz z opisowym komunikatem o błędzie, który zawiera także identyfikator śledzenia.

| Stan usługi WS | Opis
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Odbiornik zamyka gniazdo.
| 1001      | Ścieżka połączenia hybrydowego została usunięta lub wyłączona.
| 1008      | Token zabezpieczający wygasł, więc zasady autoryzacji zostały naruszone.
| 1011      | Wystąpił problem w usłudze.

### <a name="http-request-protocol"></a>Protokół żądań HTTP

Protokół żądania HTTP zezwala na dowolne żądania HTTP, z wyjątkiem uaktualnień protokołu.
Żądania HTTP są wskazywane przez zwykły adres środowiska uruchomieniowego jednostki, bez $hc wrostkowe, które są używane przez klientów protokołu WebSocket dla połączeń hybrydowych.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

_Przestrzeń nazw_ jest w pełni kwalifikowaną nazwą domeny Azure Relay przestrzeni nazw, która hostuje połączenie hybrydowe, zazwyczaj formularz `{myname}.servicebus.windows.net` .

Żądanie może zawierać dowolne dodatkowe nagłówki HTTP, w tym zdefiniowane przez aplikację. Wszystkie podane nagłówki, z wyjątkiem tych, które są bezpośrednio zdefiniowane w RFC7230 (zobacz [komunikat żądania](#request-message)) przepływ do odbiornika i można je znaleźć w `requestHeader` obiekcie komunikatu **żądania** .

Opcje parametrów ciągu zapytania są następujące:

| Param          | Wymagane? | Opis
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Tak\*     | Odbiornik musi podać prawidłowy, zakodowany w adresie URL Service Bus token dostępu współdzielonego dla przestrzeni nazw lub połączenia hybrydowego, które przyznaje prawo do **wysyłania** .

Token może być również przenoszony w `ServiceBusAuthorization` `Authorization` nagłówku lub http. Token może zostać pominięty, jeśli połączenie hybrydowe jest skonfigurowane do zezwalania na żądania anonimowe.

Ponieważ usługa efektywnie działa jako serwer proxy, nawet jeśli nie jest to prawdziwy serwer proxy HTTP, dodaje `Via` nagłówek lub Adnotuj istniejący `Via` nagłówek zgodny z [RFC7230, sekcja 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Usługa dodaje nazwę hosta przestrzeni nazw przekaźnika do `Via` .

| Kod | Komunikat  | Opis                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Żądanie zostało obsłużone przez co najmniej jeden odbiornik.  |
| 202  | Zaakceptowano | Żądanie zostało zaakceptowane przez co najmniej jeden odbiornik. |

Jeśli wystąpi błąd, usługa może odpowiedzieć w następujący sposób. Określa, czy odpowiedź pochodzi z usługi lub czy odbiornik może być zidentyfikowany za pośrednictwem obecności `Via` nagłówka. Jeśli nagłówek jest obecny, odpowiedź pochodzi z odbiornika.

| Kod | Błąd           | Opis
| ---- | --------------- |--------- |
| 404  | Nie znaleziono       | Ścieżka połączenia hybrydowego jest nieprawidłowa lub podstawowy adres URL jest nieprawidłowo sformułowany.
| 401  | Brak autoryzacji    | Brak tokenu zabezpieczeń lub jest on nieprawidłowo sformułowany lub nieprawidłowy.
| 403  | Forbidden       | Token zabezpieczający jest nieprawidłowy dla tej ścieżki i dla tej akcji.
| 500  | Błąd wewnętrzny  | Wystąpił problem w usłudze.
| 503  | Zła brama     | Nie można skierować żądania do żadnego odbiornika.
| 504  | Limit czasu bramy | Żądanie zostało skierowane do odbiornika, ale odbiornik nie potwierdził potwierdzenia w wymaganym czasie.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)
