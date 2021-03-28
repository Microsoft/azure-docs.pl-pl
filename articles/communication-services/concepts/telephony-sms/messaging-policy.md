---
title: Zasady obsługi komunikatów
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o zasadach obsługi wiadomości SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646760"
---
# <a name="azure-communication-services-messaging-policy"></a>Zasady obsługi komunikatów w usłudze Azure Communications Services

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Usługi komunikacyjne platformy Azure są przekształcane w sposób, w jaki klienci współpracują z klientami dzięki tworzeniu rozbudowanych, niestandardowych środowisk komunikacyjnych, które wykorzystują te same usługi klasy korporacyjnej, które odnoszą się do zespołów Microsoft Teams i Skype. Zintegruj funkcję obsługi wiadomości SMS z rozwiązaniami komunikacyjnymi, aby dotrzeć do klientów w dowolnym czasie i w dowolnym miejscu, w którym są one potrzebne. Wystarczy pamiętać o kilku wymaganiach dotyczących obsługi wiadomości, aby rozpocząć pracę.

Wiemy, że wymagania dotyczące obsługi komunikatów mogą być zniechęcającee, ale są one łatwe do zapamiętania "COMS":

- C — wyrażanie zgody
- O-Opt-Out
- M — zawartość komunikatu
- Fałszowanie S

Opracowano te zasady obsługi komunikatów, aby pomóc spełnić wymagania prawne i dostosować je przy użyciu zalecanych najlepszych rozwiązań. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Wyrażanie zgody 

### <a name="what-is-consent"></a>Co to jest zgoda?

Zgoda to umowa między ty i odbiorcą wiadomości, która umożliwia wysyłanie do nich automatycznych komunikatów. Musisz uzyskać zgodę przed wysłaniem pierwszej wiadomości i należy wyjaśnić adresatowi, że zgadzają się na otrzymywanie wiadomości od użytkownika. Ta procedura jest znana jako otrzymywanie "wcześniejszej zgody" od osoby, którą zamierzasz zgłosić.

Wysyłane wiadomości muszą być tego samego typu komunikatów, które użytkownik wyraził zgodę na otrzymywanie i powinny być wysyłane tylko do numeru przekazanego przez odbiorcę. Jeśli zamierzasz wysyłać komunikaty informacyjne, takie jak przypomnienia o terminie lub alerty, można wyrazić zgodę na zapis lub ustnie. Jeśli zamierzasz wysyłać wiadomości promocyjne, takie jak wiadomości sprzedażowe lub marketingowe promujące produkt lub usługę, należy napisać zgodę.

### <a name="how-do-you-obtain-consent"></a>Jak uzyskać zgodę?

Zgodę można uzyskać na wiele sposobów, na przykład:

- gdy użytkownik wprowadza swój numer telefonu do witryny sieci Web, 
- gdy użytkownik inicjuje wymianę wiadomości SMS lub
- gdy użytkownik wysyła słowo kluczowe rejestracji do Twojego numeru telefonu. 
 
Bez względu na to, jak wyrażasz zgodę, ty i klienci muszą upewnić się, że zgoda jest niejednoznaczna. Zakres zgody powinien zostać wyczyszczony dla odbiorcy.


### <a name="consent-requirements"></a>Wymagania dotyczące zgody:

- Przed uzyskaniem zgody Podaj "wywołanie do akcji". Ty i klienci powinni zapewnić potencjalnym odbiorcom komunikatów komunikat "wywołanie do działania", który zaprasza je do uczestnictwa w programie obsługi wiadomości. Wywołanie akcji powinno obejmować co najmniej: (1) tożsamość nadawcy wiadomości, (2) wyczyścić instrukcje wyboru (3) instrukcje wyboru i (4) wszystkie powiązane opłaty za komunikaty.
- Nie można przenieść ani przypisać zgody. Każda zgoda, którą użytkownik dostarczy do Ciebie, nie może zostać przeniesiona ani sprzedana do innej firmy. Jeśli zbierasz zgodę użytkownika na udzielenie innej firmy, należy jasno określić osobę trzecią. Należy również podać, że uzyskana zgoda dotyczy tylko komunikacji od innych firm.
- Zgoda jest ograniczona do celu. Osoba, która udostępnia swój numer w określonym celu, wyraża zgodę na otrzymywanie komunikacji tylko w tym konkretnym celu i od danego nadawcy wiadomości. Przed uzyskaniem zgody należy jasno poinformować zamierzonego odbiorcę wiadomości, Jeśli wyślesz wiadomości cykliczne lub wiadomości z podmiotu stowarzyszonego.

### <a name="consent-best-practices"></a>Najlepsze rozwiązania w zakresie zgody:

Oprócz opisanych powyżej wymagań dotyczących obsługi komunikatów warto wdrożyć kilka typowych najlepszych rozwiązań, w tym: 

- Szczegółowa informacja "wywołanie do działania". Aby upewnić się, że otrzymujesz odpowiednią zgodę, podaj
  - Nazwa lub opis programu obsługi wiadomości lub produktu
  - Liczba (y), z których odbiorcy będą odbierać komunikaty, i 
  - wszelkie stosowne warunki i postanowienia przed indywidualnymi dodatkami do otrzymywania wiadomości od użytkownika.
- Dokładne rekordy zgody. Należy zachować rejestr wszelkich wyrazów, które użytkownik dostarczy do Ciebie przez co najmniej cztery lata. Rekordy zgody mogą obejmować:
  - sygnatury czasowe
  - środek, za pomocą którego uzyskano zgodę
  - określona Kampania, dla której uzyskano zgodę
  - Przechwytywanie ekranu
  - Identyfikator sesji lub adres IP osoby, która wyraziła zgodę.
- Zasady ochrony prywatności i zabezpieczeń. Deweloperzy są zachęcani do zapewnienia niektórym zasadom zachowania poufności informacji, które mogą przeglądać odbiorcy wiadomości przed uzyskaniem zgody. Zalecamy również zachowanie aktywnych kontroli zabezpieczeń, aby chronić prywatne informacje osobowe.


## <a name="double-opt-in-consent"></a>Zgoda na podwójne wyrażenie zgody:

Usługi komunikacyjne platformy Azure zalecają użycie podwójnej zgody na wszystkie kampanie związane z obsługą wiadomości. Zgoda na podwójne akceptacje to dwuetapowy proces, w którym osoba pierwsza najpierw zapewnia zgodę na otrzymywanie określonych typów komunikatów. Następnie możesz wysłać komunikat z monitem o potwierdzenie, aby potwierdzić ich zgodę. Więcej komunikatów należy wysyłać tylko wtedy, gdy odbiorca wiadomości potwierdzi ich zgodę.

Początkowy komunikat potwierdzający, który wysyłasz powinien zawierać swoją tożsamość, opcję rezygnacji z przyszłych komunikatów (takich jak użycie polecenia "STOP"), numeru bezpłatnego lub polecenia "pomoc", aby uzyskać dodatkowe informacje, powiadomienia o zarejestrowaniu osoby w cyklicznym programie komunikatów, Krótki opis programu, częstotliwość wysyłania komunikatów cyklicznych i wszelkie powiązane opłaty. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Czy usługi komunikacyjne platformy Azure kiedykolwiek wymagają zgody na podwójne akceptacje?
Tak, a funkcja podwójnej zgody jest zawsze zalecana, usługa Azure Communication Services wymaga, aby w przypadku niektórych rodzajów kampanii związanych z obsługą phishingu używać zgody na podwójne użycie Te kampanie obejmują:
- Komunikaty o autogwarancjach
- Krótkoterminowe plany ubezpieczenia kondycji
- Refinansowanie długu lub zmniejszenie stawek odsetek w przypadku, gdy nie zostały dokonane przez instytucję finansową
- Komunikaty generacji potencjalnego klienta
- Konkursie, konkursy i prezentów
- Oferty pracy w domu

Kampanie, dla których wymagane jest zgoda podwójnej zgody, mogą ulec zmianie w uznaniu usług Azure Communications Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Wyjątki dotyczące tradycyjnych reguł zgody:
Mimo że wcześniejsza formalna zgoda jest zwykle wymagana przed wysłaniem komunikatu, istnieją dwie sytuacje, w których zgoda na wiadomość jest poszczególna.

- Odbiorca inicjuje komunikację. Jeśli użytkownik zainicjuje komunikację, wysyłając wiadomość do Ciebie, możesz podać odpowiednie informacje w odpowiedzi na konkretne zapytanie lub żądanie zawarte w komunikacie. Jednak domniemana zgoda udzielona przez daną osobę jest ograniczona do konwersacji, która została zainicjowana przez osobę, chyba że uzyska zgodę na dalsze komunikowanie się.
- Wykluczenia dla określonych usług. Istnieje kilka określonych usług, dla których może być implikowana zgoda na zainicjowanie komunikatu. Najbardziej typowe są następujące: 
- komunikaty dostarczania pakietów
- komunikaty instytucji finansowych, które dotyczą tematów zależnych od czasu (takich jak potencjalnie fałszywe transakcje lub naruszenia danych)
- komunikaty dostawcy usług opieki zdrowotnej, które obejmują informacje zależne od czasu oraz cel leczenia (takie jak przypomnienia o terminie lub egzaminie, wyniki laboratorium i powiadomienia dotyczące recepty). 
 
Żadna z tych komunikatów nie może zawierać zaproszonych ani anonsów.


## <a name="opt-out"></a>Rezygnacja

Adresaci komunikatów mogą odwołać zgodę i zrezygnować z otrzymywania przyszłych wiadomości w jakikolwiek rozsądny sposób. Nie można wyznaczyć wyłącznego środka dla adresatów wiadomości, aby odwołać zgodę. 

### <a name="opt-out-requirements"></a>Wymagania wstępne:

Upewnij się, że adresaci komunikatów mogą zrezygnować z przyszłych komunikatów w dowolnym momencie. Należy również zaoferować wiele opcji rezygnacji. Po wybraniu przez odbiorcę komunikatów dodatkowych komunikatów nie należy wysyłać ich, chyba że osoba ta nie udzieli odnowionej zgody.

Jednym z najbardziej typowych mechanizmów rezygnacji jest użycie słowa kluczowego "STOP" w początkowym komunikacie każdej nowej konwersacji. Przygotuj się do usunięcia klientów, którzy odpowiadają za małe litery "Stop" lub inne typowe słowa kluczowe, takie jak "Anuluj subskrypcję" lub "Cancel". Gdy osoba odwołuje się do Licencjobiorcy, należy usunąć je ze wszystkich cyklicznych kampanii do obsługi komunikatów, chyba że zdecyduje się na dalsze otrzymywanie komunikatów z określonego programu.

### <a name="opt-out-best-practices"></a>Wycofaj najlepsze rozwiązania:

Poza słowami kluczowymi inne typowe mechanizmy rezygnacji obejmują udostępnianie klientom wyznaczeniowego adresu e-mail, numeru telefonu działu pomocy technicznej lub linku do anulowania subskrypcji na stronie sieci Web. 


### <a name="how-we-handle-opt-out-requests"></a>Jak obsługujemy żądania rezygnacji:

Jeśli pojedyncze żądania nie spowodują rezygnacji z przyszłych komunikatów w ramach numeru bezpłatnych usług Azure Communications Services, cały ruch z tego numeru zostanie automatycznie zatrzymany. Nadal jednak należy upewnić się, że nie są wysyłane dodatkowe komunikaty dla tej kampanii do obsługi komunikatów z nowych lub różnych numerów. Jeśli masz wyraźnie uzyskaną zgodę na inną kampanię dotyczącą obsługi komunikatów, możesz nadal wysyłać komunikaty z innego numeru do tej kampanii. Przeczytaj sekcję często zadawanych pytań, aby dowiedzieć się więcej na temat [obsługi rezygnacji](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Zawartość komunikatu

### <a name="adult-content"></a>Zawartość dla dorosłych:

Zawartość komunikatów obejmująca elementy płeć, lubimy, alkohol, broń palna, tytoń, hazard lub konkursie i konkurs mogą wyzwalać dodatkowe wymagania. Ta zawartość jest wyraźnie zabroniona w niektórych jurysdykcjach. W przypadku wysłania wiadomości zawierającej tę zawartość, obowiązek przestrzegania wszystkich obowiązujących przepisów jurysdykcji, w których są otrzymywane powiadomienia. Na żądanie wymuszania ustawodawstwa lub usług Azure Communications Services należy przygotować się do zapewnienia dowodu zgody z prawami lokalnymi, które regulują zawartość dla dorosłych.

Nawet w przypadku, gdy taka zawartość nie jest sprzeczna z prawem, należy dołączyć mechanizm weryfikacji wieku na początku do bramy wiekowej zamierzonego odbiorcy wiadomości od treści dla dorosłych. W Stany Zjednoczone dodatkowe wymagania prawne dotyczą komunikacji marketingowej ukierunkowanej na dzieci w wieku wynoszącym 13. 

### <a name="prohibited-content"></a>Zabroniona zawartość:

Usługi komunikacyjne platformy Azure zabraniają określonej zawartości komunikatów niezależnie od zgody. Zabroniona zawartość obejmuje:
- Zawartość, która promuje nielegalne działania (np. uchylanie podatków lub crueltye zwierząt w Stany Zjednoczone)
- Lubimy mowy, zniesławiających mowy, nękanie lub inne mowę uznane za obraźliwe
- Zawartość pornograficznej
- Obscenicznych lub wulgarna zawartość
- Intimidation i zagrożenia
- Zawartość, która ma na celu wypełnianie oszustw, wprowadzanie szkód lub bezprawne uzyskanie wszelkich wartości 
- Zawartość, która odwołuje się do szkody, dyskryminacji lub przemocy
- Zawartość, która rozprzestrzenia się złośliwym oprogramowaniem
- Zawartość, która ma na celu uchylanie wymagań kontroli wieku

Firma Microsoft zastrzega sobie prawo do modyfikacji listy zabronionych treści wiadomości w dowolnym momencie.

## <a name="spoofing"></a>Spoofing (fałszowanie)

Fałszowanie polega na tym, że jest to czynność powodująca mylące lub niedokładne pochodzenie numeru, aby wyświetlić na urządzeniu odbiorcy wiadomości. Zdecydowanie odradzamy i dowolnego dostawcę usług, którego używasz do wysyłania sfałszowanych komunikatów. Fałszowanie osłony tożsamości nadawcy wiadomości i uniemożliwia odbiorcom wiadomości łatwe zrezygnowanie z niechcianej komunikacji. Wymagamy również, aby przestrzegać wszystkich obowiązujących przepisów dotyczących fałszowania.

## <a name="final-thoughts"></a>Uwagi końcowe

### <a name="legal-responsibility"></a>Odpowiedzialność prawna:

Te zasady obsługi komunikatów nie stanowią porady prawnej, a firma Microsoft zastrzega sobie prawo do modyfikowania zasad w dowolnym momencie. Usługi komunikacyjne Azure nie są odpowiedzialne za zapewnienie, że zawartość, czas lub odbiorcy komunikatów naszych klientów spełnią wszystkie obowiązujące wymagania prawne. 

Nasi klienci są odpowiedzialni za wszystkie wymagania dotyczące obsługi komunikatów. Jeśli jesteś dostawcą platformy lub oprogramowania korzystającym z usług Azure Communication Services na potrzeby przesyłania komunikatów, należy wymagać, aby klienci przestrzegali wszystkich wymagań omówionych w tych zasadach dotyczących obsługi komunikatów. Aby uzyskać dalsze wskazówki, CTIA zapewnia pomocne [zasady obsługi komunikatów i najlepsze rozwiązania](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf).

### <a name="penalties"></a>Przewidziane

Zachęcamy naszych klientów do opracowania i wdrożenia zasad i procedur zaprojektowanych w celu zapewnienia zgodności ze wszystkimi wymaganiami dotyczącymi obsługi komunikatów. Naruszenia wymagań dotyczących obsługi komunikatów mogą prowadzić do znaczących grzywien, które szybko mogą być w stanie uzyskać. Najlepszym zagadnieniem jest poznanie i przestrzeganie wszystkich odpowiednich wymagań dotyczących obsługi komunikatów i opracowywanie skutecznych środków zaradczych w celu uwzględnienia i wyeliminowania naruszeń przed ich rozproszeniem. W przypadku naruszenia zasad obsługi komunikatów lub innych wymagań prawnych firma Microsoft będzie współpracować z firmą, aby zapewnić przyszłą zgodność. Jednak firma Microsoft zastrzega sobie prawo do usunięcia wszystkich klientów z platformy usług Azure Communications Services, którzy pokazują wzorzec niezgodności z naszymi zasadami obsługi komunikatów lub wymaganiami prawnymi.
