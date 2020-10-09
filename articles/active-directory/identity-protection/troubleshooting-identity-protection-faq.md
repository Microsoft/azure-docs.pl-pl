---
title: Często zadawane pytania dotyczące ochrony tożsamości w programie Azure Active Directory
description: Często zadawane pytania Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a249d5f3c47e8e8789f91f355c791cc50341ab01
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827909"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Często zadawane pytania dotyczące ochrony tożsamości w programie Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Odrzuć znane problemy dotyczące ryzyka związanego z użytkownikiem

**Odrzucanie ryzyka użytkownika** w ramach klasycznej ochrony tożsamości ustawia aktora w historii ryzyka użytkownika w usłudze **Azure AD**.

**Odrzucanie ryzyka użytkownika** w ramach ochrony tożsamości ustawia aktora w historii ryzyka użytkownika w usłudze Identity Protection **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Istnieje obecnie znany problem powodujący opóźnienie w przepływie nieprzerwanego ryzyka dla użytkownika. Jeśli masz "zasady ryzyka dla użytkowników", te zasady przestaną być stosowane do odrzuconych użytkowników w ciągu kilku minut od kliknięcia pozycji "Odrzuć ryzyko użytkownika". Istnieją jednak znane opóźnienia w przypadku, gdy środowisko użytkownika odświeża "stan ryzyka" odrzuconych użytkowników. Aby obejść ten element, należy odświeżyć stronę na poziomie przeglądarki, aby zobaczyć najnowszego stanu zagrożenia użytkownika.

## <a name="risky-users-report-known-issues"></a>Ryzykowni użytkownicy zgłaszają znane problemy

W zapytaniach w polu Nazwa **użytkownika** jest rozróżniana wielkość liter, podczas gdy zapytania w polu **Nazwa** to Case-niezależny od.

Przełączenie **Pokaż daty jako** Ukrycie kolumny **Ostatnia aktualizacja ryzyka** . Aby odczytać kolumnę, kliknij **kolumny** w górnej części bloku ryzykownych użytkowników.

**Odrzuć wszystkie zdarzenia** z klasycznej ochrony tożsamości ustawia stan wykrycia ryzyka **zamknięte (rozwiązane)**.

## <a name="risky-sign-ins-report-known-issues"></a>Ryzykowne logowania zgłaszają problemy

**Rozwiązywanie problemów** związanych z wykrywaniem ryzyka ustawia stan na **użytkowników, którzy przechodzą przez zasady oparte na ryzyku**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-a-user-is-at-risk"></a>Dlaczego użytkownik jest narażony na ryzyko?

Jeśli jesteś klientem Azure AD Identity Protection, przejdź do widoku [ryzykowne użytkownicy](howto-identity-protection-investigate-risk.md#risky-users) i kliknij użytkownika o podwyższonym ryzyku. W szufladzie na dole karta "Historia ryzyka" będzie zawierać wszystkie zdarzenia, które doprowadziły do zmiany ryzyka użytkownika. Aby wyświetlić wszystkie ryzykowne logowania dla użytkownika, kliknij pozycję ryzykowne logowania użytkownika. Aby wyświetlić wszystkie wykrycia ryzyka dla tego użytkownika, kliknij pozycję wykrycia ryzyka użytkownika.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Jak mogę uzyskać raport wykrywania określonego typu?

Przejdź do widoku wykrywania ryzyka i przefiltruj według typu wykrywania. Następnie można pobrać ten raport z programu. Wolumin CSV lub. Format JSON przy użyciu przycisku **Pobierz** znajdującego się u góry. Aby uzyskać więcej informacji, zobacz artykuł [How to: badanie ryzyka](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Dlaczego nie mogę ustawić własnych poziomów ryzyka dla każdego wykrywania ryzyka?

Poziomy ryzyka związane z ochroną tożsamości są oparte na dokładności wykrywania i obsługiwane przez naszą nadzorowany Uczenie maszynowe. Aby dostosować środowisko, które są prezentowane użytkownikom, administrator może uwzględnić/wykluczyć niektórych użytkowników/grupy z zasad ryzyka użytkownika i logowania.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Dlaczego lokalizacja logowania jest niezgodna z miejscem, w którym użytkownik rzeczywiście się zalogował?

Mapowanie geolokalizacji IP jest wyzwaniem obejmującym cały branżę. Jeśli uważasz, że lokalizacja wymieniona w raporcie logowania nie jest zgodna z rzeczywistą lokalizacją, skontaktuj się z pomocą techniczną firmy Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Jak mogę zamknąć określone wykrycia ryzyka, jak w starym interfejsie użytkownika?

Aby przesłać opinię na temat wykrywania ryzyka, można potwierdzić, że zalogowanie zostało naruszone lub bezpieczne. Opinie nadawane na zalogowaniu Trickles się na wszystkie wykrycia związane z tym logowaniem. Jeśli chcesz zamknąć wykrywania, które nie są połączone z logowaniem, możesz podać tę opinię na poziomie użytkownika. Aby uzyskać więcej informacji, zobacz artykuł [jak: wyrażanie opinii o ryzyku w Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Jak daleko można wrócić w czasie, aby zrozumieć, co się dzieje z moim użytkownikiem?

- Widok [ryzykownych użytkowników](howto-identity-protection-investigate-risk.md#risky-users) przedstawia ryzyko związane z ryzykiem użytkownika w oparciu o wszystkie przeszłe logowania. 
- W widoku [ryzykownych](howto-identity-protection-investigate-risk.md#risky-sign-ins) logowań w ciągu ostatnich 30 dni są wyświetlane objawy ryzyka. 
- W widoku [wykrycia ryzyka](howto-identity-protection-investigate-risk.md#risk-detections) są wyświetlane wykrycia ryzyka w ciągu ostatnich 90 dni.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Jak można dowiedzieć się więcej na temat konkretnego wykrywania?

Wszystkie wykrycia ryzyka są udokumentowane w artykule [co to jest ryzykowne](concept-identity-protection-risks.md#risk-types-and-detection). Aby dowiedzieć się więcej o wykrywaniu, możesz umieścić wskaźnik myszy nad symbolem (i) obok wykrywania na Azure Portal.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak działają mechanizmy przesyłania opinii w ramach ochrony tożsamości?

**Potwierdź naruszenie zabezpieczeń** (w przypadku logowania) — informuje Azure AD Identity Protection, że logowanie nie zostało wykonane przez właściciela tożsamości i wskazuje na naruszenie zabezpieczeń.

- Po otrzymaniu opinii o stanie zalogować się i zagrożeniu użytkownika w celu **potwierdzenia bezpieczeństwa** i **wysokiego**poziomu ryzyka.

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka.

    > [!NOTE]
    > Jeśli użytkownik jest już korygowany, nie klikaj przycisku **Potwierdź złamane zabezpieczenia** , ponieważ przeniesie stan zagrożenia logowania i użytkownika w celu **potwierdzenia** , że poziom zagrożenia został naruszony i podwyższonego poziomu ryzyka na **wysoki**.

**Potwierdź bezpieczne** (w przypadku logowania) — informuje Azure AD Identity Protection, że logowanie zostało wykonane przez właściciela tożsamości i nie wskazuje na naruszenie zabezpieczeń.

- Po otrzymaniu tej opinii przeniesiemy stan ryzyka logowania (nie użytkownika), aby **potwierdzić bezpieczny** i poziom ryzyka do **-** .

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka.

    > [!NOTE]
    > Jeśli uważasz, że użytkownik nie zostanie naruszony, użyj opcji **Odrzuć ryzyko** dla użytkownika na poziomie użytkownika, a nie za pomocą **potwierdzonego bezpiecznego** na poziomie logowania. **Ryzyko odrzucania użytkowników** na poziomie użytkownika powoduje zamknięcie ryzyka użytkownika i wszystkich wcześniejszych zagrożeń związanych z logowaniem i wykryciem ryzyka.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Dlaczego widzę użytkownika o niskim lub wyższym stopniu ryzyka, nawet jeśli nie są wyświetlane ryzykowne logowania ani wykrycia ryzyka w usłudze Identity Protection?

Ze względu na to, że ryzyko związane z użytkownikiem ma charakter zbiorczy i nie wygasa, użytkownik może mieć ryzyko związane z niską lub wyższą konfiguracją, nawet jeśli nie ma żadnych ostatnich ryzykownych logowań ani wykrytych zagrożeń. Taka sytuacja może wystąpić, jeśli jedyne złośliwe działanie użytkownika miało miejsce poza przedziałem czasu, w którym przechowywane są szczegóły ryzykownych logowań i zagrożeń. Nie wygasa to ryzyka dla użytkowników, ponieważ wiadomo, że złe osoby, które pozostały w środowisku klienta przez 140 dni przed przystąpieniem do naruszenia tożsamości. Klienci mogą przeglądać oś czasu ryzyka użytkownika, aby zrozumieć, dlaczego użytkownik jest narażony na ryzyko: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Dlaczego logowanie ma wynik "ryzyko związane z logowaniem (zagregowany)" o wysokim stopniu, gdy powiązane z nim środki wykrywania mają niski lub średniego ryzyka?

Wysoki zagregowany wskaźnik ryzyka może opierać się na innych funkcjach logowania lub w przypadku, gdy wygenerowane jest więcej niż jedno wykrycie dla tego logowania. Z drugiej strony Logowanie może mieć ryzyko związane z logowaniem (zagregowany), nawet jeśli wykrycie skojarzone z logowaniem ma wysokie ryzyko. 

### <a name="why-is-the-detection-which-is-linked-to-a-risky-sign-in-have-a-different-risk-level-than-the-sign-in-risk-level-real-time"></a>Dlaczego wykrywanie, które jest połączone z ryzykownym logowaniem, ma różny poziom ryzyka niż poziom ryzyka logowania (w czasie rzeczywistym)? 

Niedawno wprowadziliśmy usprawnienia w zakresie obliczania ryzyka związanego z logowaniem w czasie rzeczywistym. Niezgodność zaobserwowana między poziomem wykrywania ryzyka a poziomem ryzyka związanego z logowaniem jest wynikiem tych zmian. Należy pamiętać, że ryzyko związane z logowaniem w czasie rzeczywistym jest wartością używaną podczas wymuszania zasad. 
