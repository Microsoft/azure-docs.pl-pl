---
title: Rejestruj niestandardowe próbki głosu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Utwórz niestandardowy dźwięk z jakością produkcyjną, przygotowując niezawodny skrypt, wykonując dobry talent głosu i rejestrując profesjonalny.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 6d2ad87f6a8c79f61d62140a1549968cd88d83aa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722317"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Rejestruj przykłady głosu, aby utworzyć niestandardowy głos

Tworzenie niestandardowego, niestandardowego głosu dla produkcji nie jest zobowiązaniem niezależnym. Centralny składnik niestandardowego głosu to duża kolekcja próbek audio dla człowieka. Należy koniecznie zapewnić wysoką jakość tych nagrań dźwiękowych. Wybierz talent głosu, który ma doświadczenie z tym rodzajem nagrań, i poproś go o nagranie przez właściwy inżynier ds. nagrywania przy użyciu profesjonalnego sprzętu.

Przed wprowadzeniem tych nagrań potrzebny jest skrypt: słowa, które będą wymawiane przez Talente głosu do tworzenia próbek audio. Aby uzyskać najlepsze wyniki, Twój skrypt musi mieć dobre pokrycie fonetyczne i dostateczną odmianę do uczenia niestandardowego modelu głosu.

Wiele małych, ale ważnych szczegółów, aby utworzyć profesjonalne nagrywanie głosu. Ten przewodnik jest planem procesu, który pomoże Ci uzyskać dobre, spójne wyniki.

> [!NOTE]
> Jeśli chcesz przeprowadzić uczenie głosu neuronowych, musisz określić profil talenta głosowego z plikiem zgody na dźwięk dostarczonym w przypadku głosu talent, aby użyć jego danych mowy do uczenia niestandardowego modelu głosowego. Podczas przygotowywania skryptu nagrywania upewnij się, że zawarto następujące zdanie. 

> "I [stan imię i nazwisko] wie, że nagrania mojego głosu będą używane przez użytkownika [Nadaj nazwę firmie] do tworzenia i używania syntetycznej wersji mojego głosu".
To zdanie zostanie użyte do sprawdzenia, czy dane szkolenia są wykonywane przez tę samą osobę, która wyraża zgodę. Więcej informacji na temat [weryfikacji talent głosu](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) można znaleźć tutaj.

> Niestandardowy głos neuronowych jest dostępny z ograniczonym dostępem. Upewnij się, że rozumiesz odpowiednie [wymagania AI](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , i [Zastosuj tutaj dostęp](https://aka.ms/customneural). 

> [!TIP]
> Aby uzyskać najwyższej jakości wyniki, rozważ zaangażowanie firmy Microsoft w rozwój niestandardowego głosu. Firma Microsoft ma rozległe doświadczenie w zakresie tworzenia głosów o wysokiej jakości dla własnych produktów, w tym Cortany i pakietu Office.

## <a name="voice-recording-roles"></a>Role nagrywania głosu

Istnieje cztery podstawowe role w projekcie niestandardowego nagrywania głosu:

Rola|Przeznaczenie
-|-
Talent głosu        |Ten głos osoby będzie stanowić podstawę niestandardowego głosu.
Inżynier rejestrowania  |Widzi techniczne aspekty nagrania i obsługuje urządzenia rejestrujące.
General            |Przygotowuje skrypt i zaautokaruje wydajność talent głosu.
Edytor              |Kończy pliki audio i przygotowuje je do przekazania do niestandardowego portalu głosowego.

Osoba może wypełnić więcej niż jedną rolę. W tym przewodniku przyjęto założenie, że użytkownik będzie przede wszystkim wypełniać rolę dyrektora i zatrudniać zarówno talent głosu, jak i inżyniera rejestrująca Jeśli chcesz samodzielnie nagrać nagrania, ten artykuł zawiera pewne informacje o roli inżyniera. Rola edytora nie jest wymagana do momentu zakończenia sesji, więc może być wykonywana przez dyrektora lub inżyniera rejestratora.

## <a name="choose-your-voice-talent"></a>Wybierz talent głosu

Aktory ze środowiskami w VoiceOver lub znakami głosowymi czynią dobre niestandardowe Talente. Można również często znaleźć odpowiednie talent wśród anonsów i grup dyskusyjnych.

Wybierz pozycję Voice talent, z której polubisz głos naturalny. Istnieje możliwość tworzenia unikatowych głosów "Character", ale jest to znacznie trudniejsze w przypadku większości talent w celu ich spójnego działania, a nakład pracy może spowodować nadużycie.

> [!TIP]
> Ogólnie rzecz biorąc, Unikaj używania rozpoznawalnych głosów do tworzenia niestandardowego głosu — chyba że oczywiście celem jest utworzenie głosu osobistości. Małe znane głosy są zwykle mniej rozpraszane dla użytkowników.

Najważniejszym czynnikiem do wybierania głosu talent jest spójność. Nagrania powinny być odtwarzane w taki sam sposób, jak w tym samym dniu w tym samym pokoju. Można to idealne rozwiązanie dzięki dobrym praktykom rejestrowania i inżynierom.

Talent głosu to druga połowa równania. Muszą być w stanie mówić ze stałą częstotliwością, poziomem głośności, pozostałością i kolorem. Należy wyczyścić diction. Talent musi mieć możliwość ścisłej kontroli ich zmienności, emocjonalnej wpływ na mowę i mannerisms mowy.

Nagrywanie niestandardowych przykładów głosowych może być bardziej fatiguing niż inne rodzaje pracy głosowej. Większość talent głosu może zarejestrować się przez dwa lub trzy godziny dziennie. Ogranicz liczbę sesji do trzech lub czterech tygodni, z których każdy ma wartość z zakresu od do do między, jeśli to możliwe.

Skontaktuj się z talentą głosu, aby utworzyć "Persona", która definiuje ogólny dźwięk i emocjonalneję niestandardowego głosu. W procesie należy określić, co to jest "neutralny" dźwięk, jak dla danej osoby. Korzystając z funkcji niestandardowego głosu neuronowych, możesz nauczyć model, który mówisz z emocji. Zdefiniuj "Style głosu" i podawaj talent głosu, aby odczytywać skrypt w taki sposób, aby lepiej style, które chcesz.  

Osoba może mieć, na przykład, charakter naturalny. Dlatego głos "ich" może mieć zanotowanie optimism nawet wtedy, gdy mówisz w sposób neutralny. Jednak taka cecha ma charakter delikatny i spójny. Odsłuchaj odczyty według istniejących głosów, aby uzyskać pomysł dotyczący tego, czego potrzebujesz.

> [!TIP]
> Zazwyczaj należy nawiązać własne nagranie głosowe. Twoja talent głosu powinna być przedstawiona dla kontraktu dotyczącego pracy w ramach zatrudnienia dla projektu.

## <a name="create-a-script"></a>Tworzenie skryptu

Punktem początkowym dowolnej niestandardowej sesji nagrywania głosu jest skrypt, który zawiera wyrażenia długości do wymowy przez talent głosu. (Termin "wyrażenia długości" obejmuje zarówno pełne zdania, jak i krótsze frazy.

Wyrażenia długości w skrypcie mogą pochodzić z dowolnego miejsca: fikcja, nie fikcyjne, transkrypcje mowy, raporty o nowościach i inne elementy dostępne w formie drukowanej. Jeśli chcesz się upewnić, że Twój głos jest dobrze na konkretnych rodzajach wyrazów (takich jak Terminologia medyczna lub żargon programowania), możesz chcieć uwzględnić zdania z dokumentów badania uniwersyteckieowych lub dokumentacji technicznej. Krótkie omówienie potencjalnych problemów prawnych można znaleźć w sekcji ["prawa"](#legalities) . Możesz również napisać własny tekst.

Twoje wyrażenia długości nie muszą pochodzić z tego samego źródła ani tego samego rodzaju źródła. Nie trzeba nawet niczego robić ze sobą. Jeśli jednak użyjesz opcji Ustaw zwroty (na przykład "pomyślnie zalogowano") w aplikacji mowy, upewnij się, że zostały one uwzględnione w skrypcie. Dzięki temu niestandardowy głos będzie lepszym rozwiązaniem do wymawiania tych fraz. A jeśli zdecydujesz się na użycie nagrania zamiast syntezy głosu, będziesz już mieć ten sam głos.

Chociaż spójność jest kluczem podczas wybierania głosu talent, odmianą jest Hallmark dobrego skryptu. Skrypt powinien zawierać wiele różnych słów i zdań z różnymi długościami, strukturami i nastrójami zdań. Każdy dźwięk w języku powinien być reprezentowany wielokrotnie i w wielu kontekstach (nazywanych *pokryciem fonetycznym*).

Ponadto tekst powinien zawierać wszystkie sposoby reprezentowania określonego dźwięku na piśmie oraz umieścić każdy dźwięk w różnych miejscach w zdaniach. Oba zdania deklaratywne i pytania powinny być zawarte i odczytywane przy użyciu odpowiednich intonation.

Trudno jest napisać skrypt, który zapewnia *wystarczającą ilość* danych, aby umożliwić portalowi Custom Speech tworzenie dobrego głosu. W rzeczywistości Najprostszym sposobem, aby skrypt, który osiąga niezawodne pokrycie fonetyczne, ma uwzględniać dużą liczbę próbek. Standardowe głosy udostępniane przez firmę Microsoft zostały skompilowane na podstawie dziesiątek tysięcy wyrażenia długości. Należy przygotować się do zarejestrowania kilku tysięcy wyrażenia długości na co najmniej, aby utworzyć niestandardowy dźwięk o jakości produkcyjnej.

Sprawdź uważnie skrypt pod kątem błędów. Jeśli to możliwe, należy ją także sprawdzić. Gdy uruchamiasz skrypt za pomocą talent, prawdopodobnie wychwytuje kilka błędów.

### <a name="script-format"></a>Format skryptu

Skrypt można napisać w programie Microsoft Word. Skrypt jest przeznaczony do użycia podczas sesji rejestrowania, więc można go skonfigurować w dowolny sposób, w jaki można łatwo korzystać z programu. Utwórz plik tekstowy, który jest wymagany przez niestandardowy Portal głosowy osobno.

Podstawowy format skryptu zawiera trzy kolumny:

* Liczba wypowiedź, rozpoczynając od 1. Numerowanie ułatwia wszystkim osobom w programie Studio odwoływanie się do określonego wypowiedź ("Spróbuj ponownie użyć numeru 356"). Możesz użyć funkcji numeracji akapitów wyrazów, aby automatycznie numerować wiersze tabeli.
* Pusta kolumna, w której zostanie zapisany kod Take Number lub Time każdego wypowiedźu, aby ułatwić znalezienie go w gotowym rejestrowaniu.
* Tekst samej wypowiedź.

![Przykładowy skrypt](media/custom-voice/script.png)

> [!NOTE]
> Większość Studios rekordu w krótkich segmentach znanych jako *trwają*. Każda z nich przyjmuje zwykle od 10 do 24 wyrażenia długości. Po prostu przenotuj liczbę z przejęcia, aby znaleźć wypowiedź później. Jeśli rejestrujesz się w programie Studio, który preferuje dłuższe nagrania, zamiast tego należy zwrócić uwagę na kod czasu. Studio będzie widoczne w widocznym czasie.

Aby pisać notatki, pozostaw wystarczającą ilość miejsca po każdym wierszu. Upewnij się, że żaden wypowiedź nie jest podzielony między stronami. Ponumeruj strony i wydrukuj swój skrypt po jednej stronie arkusza.

Drukuj trzy kopie skryptu: jeden dla talent, jeden dla inżyniera i jeden dla dyrektora (ty). Użyj klipu papierowego zamiast zszywek: doświadczony wykonawca dla głosu oddzieli strony, aby uniknąć konieczności tworzenia szumów, ponieważ strony są włączone.

### <a name="legalities"></a>Prawa

W obszarze prawo autorskie odczytywanie tekstu z prawami autorskimi może być wydajnością, dla którego autor utworu powinien być kompensowany. Ta wydajność nie będzie rozpoznawalna w końcowym produkcie, niestandardowym. W związku z tym, legalność korzystania z pracy chronionej prawem autorskim do tego celu nie jest dobrze ustalona. Firma Microsoft nie może udzielić porady prawnej dotyczącej tego problemu. Zapoznaj się z własnymi prawnikami.

Na szczęście można całkowicie uniknąć tych problemów. Istnieje wiele źródeł tekstu, których można użyć bez uprawnień lub licencji.

|Źródło tekstu|Opis|
|-|-|
|[CMU Arctic korpus](http://festvox.org/cmu_arctic/)|Informacje o 1100 zdaniach wybranych przed prawami autorskimi są przeznaczone do użycia w projektach syntezy mowy. Doskonały punkt wyjścia.|
|Nie działa już<br>w obszarze prawa autorskie|Zwykle działa opublikowany przed 1923. W przypadku języka angielskiego [Projekt Gutenberg](https://www.gutenberg.org/) zawiera dziesiątki tysięcy takich utworów. Możesz chcieć skupić się na nowszych działaniach, ponieważ język będzie bliżej nowoczesnego języka angielskiego.|
|Działa dla instytucji rządowych &nbsp;|Program Works utworzony przez Stany Zjednoczone władze rządowe nie ma prawa autorskiego w Stany Zjednoczone, chociaż rząd może zatwierdzić prawa autorskie w innych krajach/regionach.|
|Domena publiczna|Działa, w przypadku których prawa autorskie zostały jawnie odkazane lub które zostały dedykowane dla domeny publicznej. Odstąpienie praw autorskich w niektórych jurysdykcjach może nie być możliwe.|
|Legalnie licencjonowane działania|Program współpracuje z licencją, taką jak Creative Commons Attribution lub licencję bezpłatna dokumentacja (GFDL). Program Wikipedia używa GFDL. Niektóre licencje mogą jednak nałożyć ograniczenia dotyczące wydajności licencjonowanej zawartości, która może mieć wpływ na tworzenie niestandardowego modelu głosu, dlatego należy uważnie przeczytać licencję.|

## <a name="recording-your-script"></a>Nagrywanie skryptu

Zarejestruj swój skrypt w profesjonalnej wersji programu Studio, która jest wyspecjalizowana w pracy głosowej. Będą mieć zarejestrowany kabinę, właściwy sprzęt i odpowiednie osoby, które będą mogły je obsługiwać. Nie skimp podczas rejestrowania.

Dyskutuj o projekcie za pomocą inżyniera rejestratora Studio i posłuchaj ich opinii. Nagrywanie powinno mieć małą lub niewielką kompresję zakresu dynamicznego (maksymalnie 4:1). Niezwykle ważne jest, aby dźwięk miał spójny wolumin i wysoki współczynnik sygnału do szumu, bez zbędnych dźwięków.

### <a name="do-it-yourself"></a>Zrób to samodzielnie

Jeśli chcesz samodzielnie nagrywać, zamiast przechodzenia do programu nagrywania Studio, Oto krótkie. Dzięki powiększeniu możliwości rejestrowania i wykastowania w domu łatwiej jest łatwo znaleźć odpowiednie opinie i zasoby w trybie online.

"Kabina nagrywania" powinna być małym pomieszczenie bez zauważalnego ECHA lub "tonu pokoju". Powinien być możliwie cichy i soundproof, jak to możliwe. Drapes na ściany mogą służyć do redukcji echa i neutralizacji lub "deaden" dźwięku pokoju.

Używaj mikrofonu o wysokiej jakości ("MIC"), który jest przeznaczony do nagrywania głosu. Sennheiser, AKG, a nawet nowsze powiększenie MICS mogą dać dobre wyniki. Możesz kupić mikrofon lub wystawić go od lokalnego wynajmu w języku dźwiękowym. Poszukaj jednego z interfejsem USB. Ten typ mikrofonu wygodnie łączy element Microphone, Preamp i Przetwornik analogowo-cyfrowy w jednym pakiecie, upraszczając podłączenie.

Możesz również użyć mikrofonu analogowego. Wiele domów dzierżawy oferuje "" "," znanej "Mikrotelefony do ich znaku głosowego. Należy zauważyć, że profesjonalne narzędzia analogowe korzystają ze zrównoważonych łączników XLR, a nie do 1/4-calowych wtyków używanych w sprzęcie konsumenckim. Jeśli przejdziesz do trybu analogowego, musisz również mieć Preamp i interfejs audio komputera z tymi łącznikami.

Zainstaluj mikrofon w stojaku lub wysięgniku i zainstaluj filtr pop przed mikrofonem, aby wyeliminować hałas z "plosive", takich jak "p" i "b". Niektóre Mikrotelefony są dołączone do instalacji zawieszenia, która izoluje je od drgań w stojaku, co jest przydatne.

Talent głosu musi pozostać w spójnej odległości od mikrofonu. Użyj taśmy na podłożu, aby oznaczyć miejsce, w którym powinny się odistnieć. Jeśli talent woli, weź pod uwagę szczególną uwagę na monitorowanie odległości MIC i unikanie hałasu z krzesła.

Użyj stojaka do przechowywania skryptu. Należy unikać Angling, aby mógł on odzwierciedlać dźwięk w kierunku mikrofonu.

Osoba pracująca nad urządzeniem rejestrującym — inżynier musi znajdować się w oddzielnym pokoju od talent, dzięki czemu można komunikować się z talent w kabinie nagrywania ( *obwód TalkBack).*

Nagranie powinno zawierać możliwie najmniejszą liczbę szumów, a celem jest przeprowadzenie przez 80-lub lepszego współczynnika sygnału do szumu.

Nasłuchuje blisko nagrań ciszy w "kabinie", na którym znajduje się dowolny hałas i eliminuje przyczynę. Typowymi źródłami szumu są otwory powietrza, stateczniki fluorescencyjne, ruch w pobliżu dróg i wentylatory (nawet komputery z notesem mogą mieć wentylatory). Mikrofony i kable mogą pobrać szum elektryczny z pobliżu okablowania AC, zazwyczaj Hum lub brzęczyk. Brzęczyk może być również spowodowany przez *pętlę uziemienia*, która jest spowodowana przez urządzenia podłączone do więcej niż jednego obwodu elektrycznego.

> [!TIP]
> W niektórych przypadkach można użyć korektora lub wtyczki oprogramowania do redukcji szumów, aby pomóc w usunięciu szumów z nagrań, chociaż zawsze najlepszym rozwiązaniem jest zatrzymanie go w źródle.

Ustaw poziomy tak, aby większość dostępnego dynamicznego zakresu rejestrowania cyfrowego była używana bez powiększania. Oznacza to, że Ustaw głośność audio, ale nie tak, aby stała się zniekształcona. Na poniższej ilustracji przedstawiono przykład przebiegu dobrego rejestrowania:

![Dobry przebieg nagrywania](media/custom-voice/good-recording.png)

W tym miejscu jest używana większość zakresu (Height), ale najwyższe wartości szczytowe sygnału nie docierają do góry ani do dołu okna. Można też zobaczyć, że cisz w nagraniu ma przybliżoną cienkią linię, wskazującą na niską podłogę szumu. To nagranie ma akceptowalny współczynnik zakresu dynamicznego i sygnału do szumu.

Rejestruj bezpośrednio na komputerze za pośrednictwem interfejsu audio o wysokiej jakości lub portu USB, w zależności od używanego mikrofonu. W przypadku analogu należy zachować prosty łańcuch audio: MIC, Preamp, Audio Interface, Computer. W rozsądnych kosztach można licencjonować zarówno [Narzędzia Avid Pro](https://www.avid.com/en/pro-tools) , jak i [Adobe Audition](https://www.adobe.com/products/audition.html) . Jeśli budżet jest niezwykle ścisły, wypróbuj bezpłatną [Audacity](https://www.audacityteam.org/).

Rejestruj o 44,1 kHz 16-bitowej monophonic (jakość dysku CD) lub lepiej. Bieżący stan grafiki to 48 kHz 24-bitowy, jeśli sprzęt obsługuje tę funkcję. Przed przesłaniem danych do niestandardowego portalu głosowego należy przepróbkować dźwięk do 16-bitowej wersji $ kHz. W dalszym ciągu są potrzebne oryginalne nagranie w przypadku modyfikacji zdarzeń.

Najlepiej, aby inne osoby mogły korzystać z ról dyrektorów, inżynierów i talentów. Nie próbuj nic robić. W szczypania, jedna osoba może być zarówno dyrektorem, jak i inżynierem.

### <a name="before-the-session"></a>Przed sesją

Aby uniknąć marnowania czasu Studio, uruchom skrypt za pomocą talent głosu przed sesją rejestrowania. Gdy talent głosu zapoznaje się z tekstem, może wyjaśnić odmowę dowolnego nieznanego wyrazu.

> [!NOTE]
> Większość rejestrowania Studios oferuje elektroniczny sposób wyświetlania skryptów w kabinie nagrywania. W takim przypadku wpisz notatki do wykonania bezpośrednio w dokumencie skryptu. Nadal chcesz, aby kopia papieru była zanotować w trakcie sesji, chociaż. Większość inżynierów będzie mieć również twardą kopię. I nadal chcesz utworzyć trzecią drukowaną kopię jako kopię zapasową dla talent w przypadku, gdy komputer nie działa.

Talent głosu może zażądać, który wyraz ma zostać wyróżniony w wypowiedź ("słowo operacyjne"). Poinformuj ich o tym, że chcesz czytać naturalny bez określonego nacisku. Można dodać wyróżnienie w przypadku, gdy funkcja rozpoznawania mowy jest syntezą; nie powinna być częścią oryginalnego nagrywania.

Skieruj talent, aby wymawiał słowa odrębnie. Każdy wyraz skryptu powinien znajdować się jako zapisany. DYwięki nie należy pomijać ani następować ze sobą, jak często jest to bardzo popularne, chyba że zostały *one zapisaną w skrypcie*.

|Tekst pisany|Niechciane wymowa|
|-|-|
|nigdy nie przekazywać Ci|nigdy nie przekazywać Ci|
|Istnieją cztery sygnalizatory|Istnieją cztery sygnalizatory|
|jak dziś Pogoda|jak dzisiaj "Pogoda"|
|Powiedz Witaj znajomemu|Powiedz Witaj znajomemu Lil "|

Talent *nie* powinna dodawać odrębnych pauz między wyrazami. Zdanie powinno nadal przepływać naturalnie, nawet przy jednoczesnym wymuszeniu niewielkiej formalnej. To dokładne rozróżnienie może potrwać od razu.

### <a name="the-recording-session"></a>Sesja rejestrowania

Utwórz nagranie odwołania lub *plik dopasowania* dla typowej wypowiedź na początku sesji. Podawaj talent, aby powtórzyć ten wiersz każdej strony lub tak. Za każdym razem Porównaj nowe nagranie z odwołaniem. Takie rozwiązanie pomaga talent zachować spójność w zakresie woluminów, tempo, skoku i intonation. W tym czasie inżynier może użyć pliku dopasowania jako odniesienia dla poziomów i ogólnej spójności dźwięku.

Plik dopasowania jest szczególnie ważny podczas wznawiania nagrywania po przerwie lub w innym dniu. Należy to zrobić kilka razy dla talent i ponowienia ich powtarzania za każdym razem, dopóki nie zostaną one dopasowane.

Zaczekaj na talent, aby zająć się głęboką oddechą i wstrzymywać przez chwilę przed każdym wypowiedźem. Zanotuj kilka sekund wyciszenia między wyrażenia długości. Wyrazy należy wymusić w taki sam sposób, w jaki się pojawiają, biorąc pod uwagę kontekst. Na przykład "Record" jako zlecenie jest wymawiane inaczej od "Record" jako rzeczownik.

Zanotuj dobre pięć sekund ciszenia przed pierwszym nagraniem, aby przechwycić "sygnał pokoju". To rozwiązanie pomaga w niestandardowym portalu głosu kompensować wszystkie pozostałe szumy w nagraniach.

> [!TIP]
> Wszystko, co jest potrzebne do przechwycenia, to talenta głosu, dzięki czemu można nagrywać monophonic (jeden kanał) tylko w swoich wierszach. Jeśli jednak nagrywasz w stereo, możesz użyć drugiego kanału, aby nagrać czat w pokoju kontroli w celu przechwycenia dyskusji w konkretnych wierszach lub przeniesieniu. Usuń tę ścieżkę z wersji, która została przekazana do niestandardowego portalu głosowego.

Nasłuchiwanie blisko wydajności talent głosu przy użyciu słuchawek. Szukasz dobrych, ale naturalnych diction, Popraw wymowę i brak niechcianych dźwięków. Nie niechętnie zezwalają, aby poprosił talent o ponowne zarejestrowanie wypowiedź, które nie spełnia tych standardów.

> [!TIP]
> W przypadku korzystania z dużej liczby wyrażenia długości jeden wypowiedź może nie mieć zauważalnego wpływu na wynikowy niestandardowy głos. Może być bardziej przydatny do zwykłego zanotowania wszelkich wyrażenia długości z problemami, wykluczania ich z zestawu danych i sprawdzenia, jak nastąpi wyłączenie niestandardowego głosu. Zawsze możesz wrócić do programu Studio i nagrać pominięte próbki później.

Należy zwrócić uwagę na kod Take lub Time w skrypcie dla każdego wypowiedźu. Poproszenie inżyniera o oznaczeniu każdego wypowiedź w metadanych lub arkuszu wskaźnika rejestrowania.

Podejmij regularne przerwy i zapewnij napoje, które pomogą Ci w talent głosu w dobrym kształcie.

### <a name="after-the-session"></a>Po sesji

Nowoczesne nagrywanie Studios jest uruchamiane na komputerach. Na końcu sesji otrzymujesz co najmniej jeden plik audio, a nie taśmę. Pliki te prawdopodobnie będą w formacie WAV lub AIFF w jakości CD (16-bitowym 44,1 kHz) lub lepiej. 48 kHz 24-bitowe są popularne i pożądane. Wyższe szybkości próbkowania, takie jak 96 kHz, zazwyczaj nie są koniecznie.

Niestandardowy Portal głosowy wymaga, aby każdy dostarczony wypowiedź miał własny plik. Każdy plik audio dostarczany przez Studio zawiera wiele wyrażenia długości. Podstawowe zadanie poprodukcyjne polega na rozdzieleniu nagrań i przygotowania ich do przesłania. Inżynier rejestrowania może umieścić znaczniki w pliku (lub podać osobny arkusz kontrolny), aby wskazać, gdzie są uruchamiane poszczególne wypowiedź.

Użyj notatek, aby znaleźć dokładne informacje, a następnie użyj narzędzia do edycji dźwięku, takiego jak [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)lub Free [Audacity](https://www.audacityteam.org/), aby skopiować każdy wypowiedź do nowego pliku.

Pozostaw tylko około 0,2 sekund wyciszenia na początku i na końcu każdego klipu, z wyjątkiem pierwszego. Ten plik powinien rozpoczynać się od pełnego pięciu sekund wyciszenia. Nie używaj edytora dźwięku do elementów dyskretnych "zero out" pliku. Dołączenie "tonu pokoju" ułatwi niestandardowe algorytmy głosu, które wynagradzają wszystkie pozostałe szumy tła.

Uważnie nasłuchiwanie każdego pliku. Na tym etapie można edytować małe niechciane dźwięki, które nie zostały pominięte podczas nagrywania, takie jak niewielka smack lip przed wierszem, ale należy zachować ostrożność, aby nie usuwać żadnej rzeczywistej mowy. Jeśli nie możesz naprawić pliku, usuń go z zestawu danych i pamiętaj, że zostało to zrobione.

Przekonwertuj każdy plik na 16 bitów i szybkość próbkowania 16 kHz przed zapisaniem i, jeśli nagrano czat Studio, Usuń drugi kanał. Zapisz każdy plik w formacie WAV, nazywając je numerem wypowiedź ze skryptu.

Na koniec Utwórz *transkrypcję* , która kojarzy każdy plik WAV z wersją tekstową odpowiedniego wypowiedź. [Tworzenie niestandardowych głosów](./how-to-custom-voice-create-voice.md) zawiera szczegółowe informacje o wymaganym formacie. Możesz skopiować tekst bezpośrednio ze skryptu. Następnie utwórz plik zip plików WAV i transkrypcję tekstu.

Archiwizuj oryginalne nagrania w bezpiecznym miejscu, jeśli będą potrzebne później. Zachowaj swój skrypt i notatki.

## <a name="next-steps"></a>Następne kroki

Wszystko jest gotowe do przekazania swoich nagrań i utworzenia niestandardowego głosu.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych czcionek głosowych](./how-to-custom-voice-create-voice.md)