---
title: Wskazówki dotyczące projektowania asystentów głosowych w systemie Windows
titleSuffix: Azure Cognitive Services
description: Wskazówki dotyczące najlepszych rozwiązań dotyczących projektowania środowiska agenta głosowego.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92165149"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Środowiska asystenta projektowego dla systemu Windows 10

Asystenci głosu opracowani w systemie Windows 10 muszą implementować poniższe wskazówki dotyczące środowiska użytkownika, aby zapewnić najlepsze możliwe środowisko aktywacji głosu w systemie Windows 10. Ten dokument zawiera Przewodnik po zrozumieniu kluczowych działań potrzebnych do integracji asystenta głosowego z powłoką systemu Windows 10.

## <a name="contents"></a>Zawartość

- [Podsumowanie widoków aktywacji głosu obsługiwanych w systemie Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Podsumowanie wymagań](#requirements-summary)
- [Najlepsze rozwiązania dotyczące dobrego środowiska nasłuchiwania](#best-practices-for-good-listening-experiences)
- [Wskazówki dotyczące projektowania aktywacji głosu w aplikacji](#design-guidance-for-in-app-voice-activation)
- [Wskazówki dotyczące projektowania aktywacji głosu powyżej blokady](#design-guidance-for-voice-activation-above-lock)
- [Wskazówki dotyczące projektowania w wersji zapoznawczej aktywacji głosu](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Podsumowanie widoków aktywacji głosu obsługiwanych w systemie Windows 10

System Windows 10 wnioskuje o środowisko aktywacji dla kontekstu klienta na podstawie kontekstu urządzenia. Poniższa tabela podsumowująca to ogólny przegląd różnych widoków dostępnych, gdy ekran jest włączony.

| Widok (dostępność) | Kontekst urządzenia | Cel klienta | Pojawia się, gdy | Wymagania projektowe |
| --- | --- | --- | --- | --- |
| **W aplikacji (19H1)** | Po zablokowaniu asystent ma fokus | Korzystanie z aplikacji Asystenta | Asystent przetwarza żądanie w aplikacji | Główne środowisko nasłuchiwania w aplikacji |
| **Powyżej blokady (19H2)** | Powyżej blokady, nieuwierzytelnione | Korzystanie z Asystenta, ale z odległości | System jest zablokowany i uaktywnia żądania. | Wizualizacje pełnoekranowe dla wielopolowego interfejsu użytkownika. Implementuj zasady odrzucania, aby nie blokować odblokowywania. |
| **Wersja zapoznawcza aktywacji głosowej (20H1)** | Po zablokowaniu asystent nie ma fokusu | Korzystanie z Asystenta, ale mniej niepożądanego sposobu | System jest niższy niż blokada i żądania asystenta w tle | Minimalna Kanwa. Zmień rozmiar lub ręcznie w widoku głównej aplikacji zgodnie z wymaganiami. |

## <a name="requirements-summary"></a>Podsumowanie wymagań

Aby uzyskać dostęp do różnych środowisk, wymagany jest minimalny nakład pracy. Jednak Asystenci muszą zaimplementować odpowiednie wskazówki dotyczące projektowania dla każdego widoku. Poniższa tabela zawiera listę kontrolną wymagań, które należy wykonać.

| **Widok aktywacji głosowej** | **Podsumowanie wymagań Asystenta** |
| --- | --- |
| **W aplikacji** | <ul><li>Przetwarzanie żądania w aplikacji</li><li>Zawiera wskaźniki interfejsu użytkownika dla Stanów nasłuchiwania</li><li>Interfejs użytkownika dostosowuje się jako zmiany rozmiaru okna</li></ul> |
| **Powyżej blokady** | <ul><li>Wykrywaj stan blokady i Żądaj aktywacji</li><li>Nie udostępniaj zawsze trwałego środowiska użytkownika, które blokuje dostęp do ekranu blokady systemu Windows</li><li>Zapewnianie pełnych wizualizacji ekranu i środowiska głosowego</li><li>Honorowe wskazówki dotyczące odrzucania poniżej</li><li>Poniżej przedstawiono zagadnienia dotyczące prywatności i zabezpieczeń</li></ul> |
| **Wersja zapoznawcza aktywacji głosowej** | <ul><li>Wykrywanie stanu odblokowywania i aktywacja w tle żądania</li><li>Rysowanie minimalnego środowiska nasłuchiwania w okienku podglądu</li><li>Narysuj blisko siebie symbol X w prawym górnym rogu i Odrzuć i Zatrzymaj przesyłanie strumieniowe audio po naciśnięciu</li><li>Zmień rozmiar lub ręcznie na widok aplikacji asystenta głównego w miarę potrzeb, aby zapewnić odpowiedzi</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Najlepsze rozwiązania dotyczące dobrego środowiska nasłuchiwania

Asystenci powinni stworzyć środowisko nasłuchiwania, aby zapewnić krytyczne Opinie, aby klient mógł zrozumieć stan asystenta. Poniżej przedstawiono niektóre możliwe stany, które należy wziąć pod uwagę podczas tworzenia środowiska asystenta. Są to tylko możliwe sugestie, a nie obowiązkowe wskazówki.

- Asystent jest dostępny dla danych wejściowych mowy
- Asystent jest w trakcie aktywowania (naciśnięcie przycisku słowa kluczowego lub mikrofonu)
- Asystent aktywnie przesyła strumieniowo dźwięk do chmury Asystenta
- Asystent jest gotowy, aby klient mógł zacząć mówić
- Asystent słyszy, że wyrazy są określane
- Asystent ma świadomość, że klient wykonuje rozmowy
- Asystent przetwarza i przygotowywa odpowiedź
- Asystent odpowiada

Nawet jeśli Stany zmieniają się szybko, warto zastanowić się nad zapewnieniem środowiska użytkownika dla Stanów, ponieważ czasy trwania są zmienne w ekosystemie systemu Windows. Opinie wizualne oraz krótkie CHIMES audio lub chirps, nazywane także &quot; earcons &quot; , mogą być częścią rozwiązania. Podobnie karty wizualne powiązane z opisami audio sprawiają, że są odpowiednie opcje odpowiedzi.

## <a name="design-guidance-for-in-app-voice-activation"></a>Wskazówki dotyczące projektowania aktywacji głosu w aplikacji

Gdy aplikacja asystenta ma fokus, cel klienta jest jasno widoczny dla aplikacji, więc wszystkie środowiska aktywacji głosowej powinny być obsługiwane przez główny widok aplikacji. Ten widok może zostać zmieniony przez klienta. Aby pomóc w wyjaśnieniu interakcji asystenta, w pozostałej części tego dokumentu używany jest konkretny przykład asystenta usług finansowych o nazwie contoso. W tym i kolejnych diagramach, co mówi klient, będzie wyświetlany w kreskówki bąbelki mowy po lewej stronie z odpowiedziami asystenta w kreskówkiych dymkach po prawej stronie.

**Widok w aplikacji. Początkowy stan po rozpoczęciu aktywacji głosu:** 
 ![ zrzut ekranu przedstawiający aplikację asystenta finansów contoso, która jest otwarta na domyślnej kanwie. Dymek mowy kreskówki po prawej stronie mówi "contoso".](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Widok w aplikacji. Po pomyślnym aktywacji głosu rozpocznie się nasłuchiwanie:** ![ zrzut ekranu przedstawiający asystenta głosowego w systemie Windows, podczas gdy asystent głosowy słucha](media/voice-assistants/windows_voice_assistant/listening.png)

**Widok w aplikacji. Wszystkie odpowiedzi pozostają w środowisku aplikacji.** ![ Zrzut ekranu asystenta głosowego w systemie Windows jako odpowiedzi Asystenta](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Wskazówki dotyczące projektowania aktywacji głosu powyżej blokady

Dostępne w przypadku 19H2, dostępne są Asystenci na platformie aktywacji głosu systemu Windows.

### <a name="customer-opt-in"></a>Zgoda klienta

Aktywacja głosowa powyżej blokady jest zawsze domyślnie wyłączona. Klienci mogą korzystać z ustawień systemu Windows>prywatności>aktywacji głosu. Aby uzyskać szczegółowe informacje na temat monitorowania i monitowania dla tego ustawienia, zobacz [powyższe Przewodnik po implementacji blokady](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Nie zastępowanie ekranu blokady

Gdy powiadomienia lub inne punkty integracji z programem w warstwie Standardowa są nadal dostępne dla Asystenta, ekran blokady systemu Windows zawsze definiuje początkowy proces obsługi klienta do momentu, gdy nastąpi Aktywacja głosowa. Po wykryciu aktywacji głosu aplikacja asystenta jest tymczasowo wyświetlana nad ekranem blokady. Aby uniknąć nieporozumień klientów, gdy jest aktywny z blokady, aplikacja asystenta nigdy nie musi przedstawić interfejsu użytkownika w celu poproszenia o dowolny rodzaj poświadczeń lub zalogowania.

![Zrzut ekranu okna blokady systemu Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Powyższe środowisko blokady po aktywacji głosu

Gdy ekran jest włączony, aplikacja asystenta jest pełnym ekranem bez paska tytułu powyżej ekranu blokady. Większe wizualizacje i silne opisy głosu przy użyciu mocnego interfejsu głosowego zezwalają na przypadki, w których klient jest zbyt daleko do odczytu interfejsu użytkownika lub jest zajęty przy użyciu innego zadania (innego niż komputer).

Gdy ekran pozostanie wyłączony, aplikacja asystenta może odtworzyć earcon, aby wskazać, że Asystent jest aktywowany i zapewnia obsługę tylko głosu.

![Zrzut ekranu asystenta głosowego powyżej blokady](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Zasady odrzucania

Asystent musi zaimplementować wskazówki dotyczące odrzucenia w tej sekcji, aby ułatwić klientom logowanie przy następnym zalogowaniu do komputera z systemem Windows. Poniżej znajdują się określone wymagania, które musi zaimplementować Asystent:

- **Wszystkie kanwy asystenta, które wyświetlają powyższą blokadę, muszą zawierać znak X** w prawym górnym rogu, który odrzuci asystenta.
- **Naciśnięcie dowolnego klucza musi również odrzucić aplikację asystenta**. Wprowadzanie z klawiatury to tradycyjny sygnał aplikacji blokady, który klient chce się zalogować. W związku z tym wszystkie dane wejściowe klawiatury/tekstu nie powinny być kierowane do aplikacji. Zamiast tego aplikacja powinna zostać odrzucana po wykryciu danych wejściowych klawiatury, aby klient mógł łatwo zalogować się na swoim urządzeniu.
- **Jeśli ekran zostanie wyłączony, aplikacja musi się odrzucić.** Dzięki temu następnym razem, gdy klient korzysta z komputera, ekran logowania będzie gotowy i oczekuje na te komputery.
- Jeśli aplikacja jest &quot; używana &quot; , może ona być kontynuowana nad blokadą. &quot;w użyciu &quot; stanowi wszelkie dane wejściowe lub wyjściowe. Na przykład podczas przesyłania strumieniowego muzyki lub filmu wideo aplikacja może kontynuować pracę powyżej blokady. &quot;Postępuj zgodnie z &quot; innymi multiturnmi krokami okna dialogowego, aby zachować aplikację powyżej blokady.
- **Szczegóły implementacji dotyczące odrzucania aplikacji** można znaleźć [w powyższym przewodniku implementacji blokady](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Zrzut ekranu przedstawiający powyższy widok blokady aplikacji asystenta finansów firmy Contoso.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Zrzut ekranu przedstawiający pulpit z ekranem blokady systemu Windows.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;Zagadnienia dotyczące zabezpieczeń prywatności powyżej blokady

Wiele komputerów jest przenośnych, ale nie zawsze w zasięgu klienta. Mogą one zostać krótko pozostawiły w pokojach hotelowych, na stanowiskach samolotu lub w obszarach roboczych, gdzie inne osoby mają fizyczny dostęp. Jeśli asystenci, którzy zostali włączeni powyżej blokady nie są przygotowani, mogą stać się podmiotem klasy, tak jak ataki &quot; [Akcja Maid](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot; .

W związku z tym asystent powinien postępować zgodnie z instrukcjami w tej sekcji, aby zapewnić bezpieczeństwo środowiska. Interakcja powyżej blokady występuje, gdy użytkownik systemu Windows jest nieuwierzytelniony. Oznacza to, że ogólnie rzecz biorąc, **dane wejściowe asystenta powinny również być traktowane jako nieuwierzytelnione**.

- Asystenci powinni **zaimplementować listę dozwolonych umiejętności, aby identyfikować umiejętności, które są potwierdzone bezpiecznym i bezpiecznym** dostępnym powyżej blokady.
- Technologie identyfikacji głośników mogą odgrywać rolę w łagodzeniu pewnych zagrożeń, ale nie jest to odpowiedni zamiennik do uwierzytelniania systemu Windows.
- Lista dozwolonych umiejętności powinna uwzględniać trzy klasy działań lub umiejętności:

| **Action — Klasa** | **Opis** | **Przykłady (niepełna lista)** |
| --- | --- | --- |
| Bezpieczne bez uwierzytelniania | Informacje ogólne dotyczące przeznaczenia lub podstawowe polecenie i kontrola aplikacji | &quot;Co to jest czas? &quot; &quot; Odtwórz następną ścieżkę&quot; |
| Bezpieczna z IDENTYFIKATORem osoby mówiącej | Ryzyko personifikacji, ujawnianie informacji osobistych. | &quot;Co&#39;s moje moje terminy? &quot; , &quot; Przejrzyj moją listę zakupów &quot; , &quot; Odpowiedz na wywołanie&quot; |
| Bezpieczne tylko po uwierzytelnieniu systemu Windows | Akcje wysokiego ryzyka, których atakujący może użyć, aby uszkodzić klienta | &quot;Kup więcej artykułów spożywczych &quot; , &quot; Usuń mój (ważny) termin &quot; , &quot; Wyślij wiadomość SMS, a następnie &quot; &quot; Uruchom stronę sieci Web (szkodliwa)&quot; |

W przypadku firmy Contoso ogólne informacje dotyczące publicznej informacji o zapasach są bezpieczne bez uwierzytelniania. Informacje specyficzne dla klienta, takie jak liczba posiadanych udziałów, prawdopodobnie są bezpieczne z IDENTYFIKATORem osoby mówiącej. Jednak kupowanie lub sprzedawanie zasobów nigdy nie powinno być dozwolone bez uwierzytelniania systemu Windows.

Aby dodatkowo zabezpieczyć środowisko, **linki Weblinks lub inne uruchomienie aplikacji do aplikacji będzie zawsze blokowane w systemie Windows, dopóki klient nie zaloguje się.** Jako ostatnie środki zaradcze firma Microsoft zastrzega sobie prawo do usunięcia aplikacji z listy dozwolonych asystentów włączonych, jeśli poważny problem z zabezpieczeniami nie zostanie rozwiązany w odpowiednim czasie.

## <a name="design-guidance-for-voice-activation-preview"></a>Wskazówki dotyczące projektowania w wersji zapoznawczej aktywacji głosu

Po zablokowaniu, gdy aplikacja asystenta _nie_ ma fokusu, system Windows udostępnia mniej niepożądanego interfejsu użytkownika aktywacji głosu, aby pomóc w zapewnieniu przepływu przez klienta. Jest to szczególnie istotne w przypadku fałszywych aktywacji, które byłyby wysoce zakłócane, jeśli uruchomiły pełną aplikację. Głównym pomysłem jest to, że każdy asystent ma inną stronę główną w powłoce, ikonę asystenta paska zadań. Gdy wystąpi żądanie aktywacji w tle, pojawi się mały widok nad ikoną paska zadań asystenta. Asystenci powinny zapewnić małe środowisko nasłuchiwania na tej kanwie. Po przetworzeniu żądań asystent może zdecydować się na zmianę rozmiaru tego widoku, aby pokazać odpowiedź w kontekście lub aby ręcznie wyświetlić widok głównej aplikacji w celu wyświetlenia większych, bardziej szczegółowych wizualizacji.

- Aby zachować minimalny poziom, wersja zapoznawcza nie ma paska tytułu, dlatego **Asystent musi narysować symbol X w prawym górnym rogu, aby umożliwić klientom odrzucanie tego widoku.** Zapoznaj się z tematem [zamykanie aplikacji](windows-voice-assistants-implementation-guide.md#closing-the-application) dla konkretnych interfejsów API do wywołania po naciśnięciu przycisku Odrzuć.
- Aby obsługiwać wersje zapoznawcze aktywacji głosu, Asystenci mogą zapraszać klientów do przypinania asystenta do paska zadań podczas pierwszego uruchomienia.

**Wersja zapoznawcza aktywacji głosowej: stan początkowy**

Asystent firmy Contoso ma stronę główną na pasku zadań: przewirowanie, okrągłą ikonę.

![Zrzut ekranu przedstawiający asystenta głosowego w systemie Windows jako ikonę na pasku zadań przed aktywacją](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**W miarę postępów aktywacji** asystent żąda aktywacji w tle. Asystent otrzymuje małe okienko podglądu (domyślna szerokość 408 i Wysokość: 248). Jeśli aktywacja głosu po stronie serwera określa, że sygnał był fałszywie dodatni, ten widok może zostać odrzucony w celu zapewnienia minimalnej przerwy.

![Zrzut ekranu asystenta głosowego w systemie Windows w widoku kompaktowym podczas weryfikacji aktywacji](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Po potwierdzeniu końcowej aktywacji** asystent prezentuje środowisko słuchania. Asystent musi zawsze narysować Odrzuć X w prawym górnym rogu wersji zapoznawczej aktywacji głosowej.

![Zrzut ekranu przedstawiający asystenta głosowego w systemie Windows nasłuchiwanie w widoku kompaktowym](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**Szybkie odpowiedzi** mogą być wyświetlane w wersji zapoznawczej aktywacji głosu. TryResizeView umożliwi asystentom żądanie różnych rozmiarów.

![Zrzut ekranu asystenta głosowego w odpowiedziach systemu Windows w widoku kompaktowym](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Ręczny**. W dowolnym momencie asystent może przełączać się do widoku głównej aplikacji, aby uzyskać więcej informacji, dialogu lub odpowiedzi wymagających większej ilości ekranu. Zapoznaj się z sekcją [przejście z widoku kompaktowego do pełnego widoku,](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) Aby uzyskać szczegółowe informacje o implementacji.

![Zrzuty ekranu asystenta głosowego w systemie Windows przed rozpoczęciem i po rozwinięciu widoku kompaktowego](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do tworzenia asystenta głosowego](how-to-windows-voice-assistants-get-started.md)