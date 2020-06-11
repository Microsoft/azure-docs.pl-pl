---
title: Ciągła ocena dostępu w usłudze Azure AD
description: Szybsze reagowanie na zmiany stanu użytkownika dzięki ciągłej ocenie dostępu w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0cb402741163c657b3e7961eb5a4f9c8e18dafd
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673024"
---
# <a name="continuous-access-evaluation"></a>Ciągła weryfikacja dostępu

Usługi firmy Microsoft, takie jak Azure Active Directory (Azure AD) i Office 365, używają otwartych standardów i protokołów w celu zmaksymalizowania współdziałania. Jedną z najważniejszych z nich jest Open ID Connect (OIDC). Gdy aplikacja kliencka, taka jak program Outlook, nawiązuje połączenie z usługą, taką jak Exchange Online, żądania interfejsu API są autoryzowane przy użyciu tokenów dostępu OAuth 2,0. Domyślnie te tokeny dostępu są prawidłowe przez jedną godzinę. Po ich wygaśnięciu klient zostanie przekierowany z powrotem do usługi Azure AD w celu jego odświeżenia. Pozwala to również na ocenę zasad dostępu użytkowników — nie można odświeżyć tokenu ze względu na zasady dostępu warunkowego lub, ponieważ użytkownik został wyłączony w katalogu. 

Wygaśnięcie i odświeżenie tokenu jest standardowym mechanizmem w branży. W tym przypadku Klienci wyrazili wątpliwości dotyczące opóźnienia między zmianą warunków ryzyka dla użytkownika (na przykład przechodzenie z biura firmy do lokalnej usługi kawowej lub poświadczeń użytkownika odnalezionych na czarnym rynku) oraz, kiedy zasady mogą być wymuszane w odniesieniu do tej zmiany. Poznamy podejście "Blunt Object" dotyczące krótszych okresów istnienia tokenu, ale znaleziono, że mogą one obniżyć wydajność i niezawodność użytkowników bez eliminowania zagrożeń.

Czasowa reakcja na naruszenia zasad lub problemy z zabezpieczeniami naprawdę wymaga "konwersacji" między wystawcą tokenów, takimi jak Azure AD, a jednostką uzależnioną, taką jak Exchange Online. Ta dwukierunkowa konwersacja daje nam dwie ważne możliwości. Jednostka uzależniona może zwrócić uwagę na to, że zmiany uległy zmianie, takie jak klient pochodzący z nowej lokalizacji i informujący wystawca tokenów. Zapewnia również wystawcy tokenów sposób informowania jednostki uzależnionej o zaprzestanie poszanowania tokenów dla danego użytkownika ze względu na złamanie konta, wyłączenie lub inne problemy. Mechanizmem tej konwersacji jest ciągły dostęp do wersji ewaluacyjnej (CAE).

Firma Microsoft jest wczesnym uczestnikiem inicjatywy protokołu do oceny dostępu ciągłego (CAEP) w ramach wspólnej grupy [sygnałów i zdarzeń](https://openid.net/wg/sse/) w usłudze OpenID Connect Foundation. Dostawcy tożsamości i jednostki uzależnione będą mogły wykorzystać zdarzenia zabezpieczeń i sygnały zdefiniowane przez grupę roboczą, aby ponownie autoryzować lub przerwać dostęp. Jest to atrakcyjne działanie i poprawi zabezpieczenia na wielu platformach i aplikacjach.

Ze względu na to, że korzyści z zabezpieczeń są tak duże, wdrażamy wstępną implementację od firmy Microsoft równolegle do naszej działalności w ramach organizacji normalizacyjnych. W miarę jak pracujemy nad wdrożeniem tych funkcji oceny ciągłego dostępu (CAE) w ramach usług firmy Microsoft, poznamy wiele i udostępniamy te informacje społecznościom ze standardami. Mamy nadzieję, że nasze środowisko wdrażania może pomóc w poinformowaniu jeszcze lepszego standardu branżowego i zdecydowaniu się na wdrożenie tej normy po ratyfikacji, umożliwiając wszystkim usługom uczestniczącym korzystanie z programu.

## <a name="how-does-cae-work-in-microsoft-services"></a>Jak działa CAE w usługach firmy Microsoft?

Koncentrujemy się na początkowej implementacji ciągłej oceny dostępu do programów Exchange i Teams. Mamy nadzieję, że w przyszłości obsłużymy wsparcie dla innych usług firmy Microsoft. Zaczniemy włączać ciągłą ocenę dostępu tylko dla dzierżawców bez zasad dostępu warunkowego. Będziemy korzystać z naszych nauk z tej fazy CAE, aby informować nas o trwającym wdrożeniu CAE.

## <a name="service-side-requirements"></a>Wymagania po stronie usługi

Ciągła ocena dostępu jest implementowana przez umożliwienie usługom (dostawcom zasobów) subskrybowania krytycznych zdarzeń w usłudze Azure AD, dzięki czemu te zdarzenia mogą być oceniane i wymuszane niemal w czasie rzeczywistym. W tym początkowym wdrożeniu CAE zostaną wymuszone następujące zdarzenia:

- Konto użytkownika zostało usunięte lub wyłączone
- Hasło użytkownika jest zmieniane lub resetowane
- Uwierzytelnianie wieloskładnikowe jest włączone dla użytkownika
- Administrator jawnie odwołuje wszystkie tokeny odświeżania dla użytkownika
- Wykryto podwyższone ryzyko użytkownika Azure AD Identity Protection

W przyszłości mamy nadzieję, że dodasz więcej zdarzeń, w tym zdarzenia, takie jak lokalizacja i zmiany stanu urządzenia. **Mimo że naszym celem jest wymuszenie, w niektórych przypadkach opóźnienie do 15 minut może być zaobserwowane z powodu czasu propagacji zdarzeń**. 

## <a name="client-side-claim-challenge"></a>Wyzwanie dla żądania po stronie klienta

Przed ciągłą oceną dostępu klienci będą zawsze próbować odtworzyć token dostępu z jego pamięci podręcznej, o ile nie wygasł. Dzięki CAE wprowadzamy nowy przypadek, że dostawca zasobów może odrzucić token nawet wtedy, gdy nie wygasł. Aby poinformować klientów o konieczności obejścia swojej pamięci podręcznej, nawet jeśli tokeny w pamięci podręcznej nie wygasły, wprowadzimy mechanizm o nazwie **wyzwanie dla żądania**. CAE wymaga aktualizacji klienta w celu zrozumienia wyzwania żądania. Najnowsza wersja następujących aplikacji poniżej obsługuje wyzwanie dla żądania:

- Program Outlook dla systemu Windows 
- Program Outlook dla systemu iOS 
- Program Outlook dla systemu Android 
- Outlook dla komputerów Mac 
- Zespoły dla systemu Windows
- Zespoły dla systemu iOS 
- Zespoły dla systemu Android 
- Zespoły dla komputerów Mac 

## <a name="token-lifetime"></a>Czas życia tokenu

Ze względu na to, że ryzyko i zasady są oceniane w czasie rzeczywistym, klienci, którzy negocjują sesje z obsługą oceny ciągłego dostępu, będą korzystać z CAE zamiast istniejących zasad okresów istnienia tokenu dostępu statycznego, co oznacza, że zasady okresowego okresu istnienia tokenów nie będą już brane pod uwagę dla klientów obsługujących CAE.

Okres istnienia tokenu dostępu zostanie zwiększony do 24 godzin w sesjach CAE. Odwołanie jest obsługiwane przez zdarzenia krytyczne i ocenę zasad, a nie okres. Ta zmiana zwiększa stabilność aplikacji bez wpływu na stan bezpieczeństwa. 

## <a name="example-flows"></a>Przykładowe przepływy

### <a name="user-revocation-event-flow"></a>Przepływ zdarzeń odwołania użytkownika:

![Przepływ zdarzeń odwołania użytkownika](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient obsługujący CAE przedstawia poświadczenia lub token odświeżenia w usłudze AAD z prośbą o token dostępu dla niektórych zasobów.
1. Token dostępu jest zwracany wraz z innymi artefaktami dla klienta.
1. Administrator jawnie [odwołuje wszystkie tokeny odświeżania dla użytkownika](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Zdarzenie odwołania zostanie wysłane do dostawcy zasobów z usługi Azure AD.
1. Dostawca zasobów przedstawia token dostępu. Dostawca zasobów szacuje ważność tokenu i sprawdza, czy istnieją jakieś zdarzenia odwołania dla użytkownika. Dostawca zasobów używa tych informacji do podejmowania decyzji o udzieleniu dostępu do zasobu.
1. W takim przypadku dostawca zasobów nie zezwala na dostęp i wysyła żądanie 401 + żądanie do klienta
1. Klient z obsługą CAE rozumie wyzwanie 401 i roszczeń. Pomija pamięć podręczną i wraca do kroku 1, wysyłając token odświeżania wraz z wezwaniem do usługi Azure AD. Usługa Azure AD będzie następnie ponownie szacować wszystkie warunki i monitować użytkownika o ponowne uwierzytelnienie w tym przypadku.

## <a name="faqs"></a>Często zadawane pytania

### <a name="what-is-the-lifetime-of-my-access-token"></a>Jaki jest okres istnienia tokenu dostępu?

Jeśli nie korzystasz z klientów z systemem CAE, domyślny okres istnienia tokenu dostępu będzie nadal wynosić 1 godzinę, chyba że okres istnienia tokenu dostępu zostanie skonfigurowany z użyciem funkcji wersji zapoznawczej [okres istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) .

Jeśli używasz klientów obsługujących CAE, którzy negocjują sesje obsługujące CAE, ustawienia listy CTL dla okresu istnienia tokenu dostępu zostaną nadpisywane i okres istnienia tokenu dostępu będzie wynosić 24 godziny.

### <a name="how-quick-is-enforcement"></a>Jak szybko jest wymuszane wymuszanie?

Mimo że naszym celem jest wymuszenie, w niektórych przypadkach opóźnienie do 15 minut może być zaobserwowane z powodu czasu propagacji zdarzeń.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak będzie CAE z częstotliwością logowania?

Częstotliwość logowania będzie uznawana za CAE lub bez niej.

## <a name="next-steps"></a>Następne kroki

[Ogłaszanie ciągłej oceny dostępu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
