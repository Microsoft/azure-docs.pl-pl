---
title: Często zadawane pytania dotyczące ochrony tożsamości w programie Azure Active Directory
description: Często zadawane pytania Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6117b1ac78faf84d73f5a78202709aec7a1f84d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492590"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Często zadawane pytania dotyczące ochrony tożsamości w programie Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Odrzuć znane problemy dotyczące ryzyka związanego z użytkownikiem

**Odrzucanie ryzyka użytkownika** w ramach klasycznej ochrony tożsamości ustawia aktora w historii ryzyka użytkownika w usłudze **Azure AD**.

**Odrzucanie ryzyka użytkownika** w ramach ochrony tożsamości ustawia aktora w historii ryzyka użytkownika w usłudze Identity Protection **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Istnieje obecnie znany problem powodujący opóźnienie w przepływie nieprzerwanego ryzyka dla użytkownika. Jeśli masz "zasady ryzyka dla użytkowników", te zasady przestaną być stosowane do odrzuconych użytkowników w ciągu kilku minut od kliknięcia pozycji "Odrzuć ryzyko użytkownika". Istnieją jednak znane opóźnienia w przypadku, gdy środowisko użytkownika odświeża "stan ryzyka" odrzuconych użytkowników. Aby obejść ten element, należy odświeżyć stronę na poziomie przeglądarki, aby zobaczyć najnowszego stanu zagrożenia użytkownika.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-a-user-at-risk"></a>Dlaczego użytkownik jest narażony na ryzyko?

Jeśli jesteś klientem Azure AD Identity Protection, przejdź do widoku [ryzykowne użytkownicy](howto-identity-protection-investigate-risk.md#risky-users) i kliknij użytkownika o podwyższonym ryzyku. W szufladzie na dole karta "Historia ryzyka" będzie zawierać wszystkie zdarzenia, które doprowadziły do zmiany ryzyka użytkownika. Aby wyświetlić wszystkie ryzykowne logowania dla użytkownika, kliknij pozycję ryzykowne logowania użytkownika. Aby wyświetlić wszystkie wykrycia ryzyka dla tego użytkownika, kliknij pozycję wykrycia ryzyka użytkownika.

### <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Dlaczego moje logowanie zostało zablokowane, ale Ochrona tożsamości nie wygenerowała wykrywania ryzyka?
Logowania można blokować z kilku powodów. Należy pamiętać, że program Identity Protection generuje wyłącznie wykrywanie ryzyka, gdy w żądaniu uwierzytelniania są używane poprawne poświadczenia. Jeśli użytkownik używa nieprawidłowych poświadczeń, nie zostanie oflagowany przez ochronę tożsamości, ponieważ nie ma ryzyka naruszenia bezpieczeństwa poświadczeń, chyba że nieprawidłowy aktor używa poprawnych poświadczeń. Niektóre powody, dla których użytkownik może zostać zablokowany do podpisywania, który nie będzie generował usługi Identity Protection, obejmuje następujące kwestie:
* Adres **IP można zablokować** ze względu na złośliwe działanie z adresu IP. Komunikat zablokowany przez adres IP nie różni się od tego, czy poświadczenia były poprawne. Jeśli adres IP jest zablokowany i nie są używane poprawne poświadczenia, nie spowoduje to wygenerowania wykrywania ochrony tożsamości
* **[Inteligentna blokada](../authentication/howto-password-smart-lockout.md)** może blokować logowanie się po wielu nieudanych próbach
* Można wymusić **zasady dostępu warunkowego** , które używają warunków innych niż poziom ryzyka, aby zablokować żądanie uwierzytelniania.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Jak mogę uzyskać raport wykrywania określonego typu?

Przejdź do widoku wykrywania ryzyka i przefiltruj według typu wykrywania. Następnie można pobrać ten raport z programu. Wolumin CSV lub. Format JSON przy użyciu przycisku **Pobierz** znajdującego się u góry. Aby uzyskać więcej informacji, zobacz artykuł [How to: badanie ryzyka](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Dlaczego nie mogę ustawić własnych poziomów ryzyka dla każdego wykrywania ryzyka?

Poziomy ryzyka związane z ochroną tożsamości są oparte na dokładności wykrywania i obsługiwane przez naszą nadzorowany Uczenie maszynowe. Aby dostosować środowisko, które są prezentowane użytkownikom, administrator może uwzględnić/wykluczyć niektórych użytkowników/grupy z ryzyka użytkownika i Sign-In zasad ryzyka.

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

- Po otrzymaniu opinii o stanie zalogować się i zagrożeniu użytkownika w celu **potwierdzenia bezpieczeństwa** i **wysokiego** poziomu ryzyka.

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka.

    > [!NOTE]
    > Jeśli użytkownik jest już korygowany, nie klikaj przycisku **Potwierdź złamane zabezpieczenia** , ponieważ przeniesie stan zagrożenia logowania i użytkownika w celu **potwierdzenia** , że poziom zagrożenia został naruszony i podwyższonego poziomu ryzyka na **wysoki**.

**Potwierdź bezpieczne** (w przypadku logowania) — informuje Azure AD Identity Protection, że logowanie zostało wykonane przez właściciela tożsamości i nie wskazuje na naruszenie zabezpieczeń.

- Po otrzymaniu tej opinii przeniesiemy stan ryzyka logowania (nie użytkownika), aby **potwierdzić bezpieczny** i poziom ryzyka do **-** .

- Ponadto firma Microsoft udostępnia informacje o naszych systemach uczenia maszynowego na potrzeby przyszłych ulepszeń oceny ryzyka. 

    > [!NOTE]
    >Obecnie wybranie opcji Potwierdź bezpieczne na zalogowaniu nie powoduje samodzielnego logowania z tymi samymi właściwościami, które są oflagowane jako ryzykowne. Najlepszym sposobem uczenia systemu do poznania właściwości użytkownika jest użycie zasad logowania ryzykowne z uwierzytelnianiem MFA. Po wyświetleniu monitu o zalogowanie się dla usługi MFA, gdy użytkownik pomyślnie odpowie na żądanie, logowanie może się powieść i pomóc w nauczeniu systemu w zachowaniu praw użytkownika.
    >
    > Jeśli uważasz, że użytkownik nie zostanie naruszony, użyj opcji **Odrzuć ryzyko** dla użytkownika na poziomie użytkownika, a nie za pomocą **potwierdzonego bezpiecznego** na poziomie logowania. **Ryzyko odrzucania użytkowników** na poziomie użytkownika powoduje zamknięcie ryzyka użytkownika i wszystkich wcześniejszych zagrożeń związanych z logowaniem i wykryciem ryzyka.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Dlaczego widzę użytkownika o niskim lub wyższym stopniu ryzyka, nawet jeśli nie są wyświetlane ryzykowne logowania ani wykrycia ryzyka w usłudze Identity Protection?

Ze względu na to, że ryzyko związane z użytkownikiem ma charakter zbiorczy i nie wygasa, użytkownik może mieć ryzyko związane z niską lub wyższą konfiguracją, nawet jeśli nie ma żadnych ostatnich ryzykownych logowań ani wykrytych zagrożeń. Taka sytuacja może wystąpić, jeśli jedyne złośliwe działanie użytkownika miało miejsce poza przedziałem czasu, w którym przechowywane są szczegóły ryzykownych logowań i zagrożeń. Nie wygasa to ryzyka dla użytkowników, ponieważ wiadomo, że złe osoby, które pozostały w środowisku klienta przez 140 dni przed przystąpieniem do naruszenia tożsamości. Klienci mogą przeglądać oś czasu ryzyka użytkownika, aby zrozumieć, dlaczego użytkownik jest narażony na ryzyko: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Dlaczego logowanie ma wynik "ryzyko związane z logowaniem (zagregowany)" o wysokim stopniu, gdy powiązane z nim środki wykrywania mają niski lub średniego ryzyka?

Wysoki zagregowany wskaźnik ryzyka może opierać się na innych funkcjach logowania lub w przypadku, gdy wygenerowane jest więcej niż jedno wykrycie dla tego logowania. Z drugiej strony Logowanie może mieć ryzyko związane z logowaniem (zagregowany), nawet jeśli wykrycie skojarzone z logowaniem ma wysokie ryzyko.

### <a name="what-is-the-difference-between-the-activity-from-anonymous-ip-address-and-anonymous-ip-address-detections"></a>Czym różnią się wykrywanie "aktywność z anonimowego adresu IP" i "anonimowe adresy IP"?

Źródło wykrywania "anonimowego adresu IP" jest Azure AD Identity Protection, podczas gdy wykrywanie aktywności z anonimowego adresu IP jest zintegrowane z MCAS (Microsoft Cloud App Security). Chociaż mają bardzo podobne nazwy i jest możliwe, że w tych sygnałach może się pojawić nakładanie się, mają różne wykrycia zaplecza.
