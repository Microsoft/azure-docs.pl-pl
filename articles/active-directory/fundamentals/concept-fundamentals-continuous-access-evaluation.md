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
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873305"
---
# <a name="continuous-access-evaluation"></a>Ciągła ocena dostępu

Usługi firmy Microsoft, takie jak azure active directory (Azure AD) i Office 365, używają otwartych standardów i protokołów, aby zmaksymalizować współdziałanie. Jednym z najbardziej krytycznych jest Open ID Connect (OIDC). Gdy aplikacja kliencka, taka jak Outlook, łączy się z usługą, taką jak Exchange Online, żądania interfejsu API są autoryzowane przy użyciu tokenów dostępu OAuth 2.0. Domyślnie te tokeny dostępu są prawidłowe przez jedną godzinę. Po ich wygaśnięciu klient jest przekierowywane z powrotem do usługi Azure AD, aby je odświeżyć. Zapewnia to również możliwość ponownej oceny zasad dostępu użytkownika — możemy zdecydować się nie odświeżać tokenu z powodu zasad dostępu warunkowego lub ponieważ użytkownik został wyłączony w katalogu. 

Słyszeliśmy przytłaczające opinie naszych klientów: jednogodzinne opóźnienie z powodu okresu istnienia tokenu dostępu do ponownego zastosowania zasad dostępu warunkowego i zmian w stanie użytkownika (na przykład: wyłączone z powodu furlough) nie jest wystarczająco dobre.

Firma Microsoft była wczesnym uczestnikiem inicjatywy CAEP (Continuous Access Evaluation Protocol) w ramach grupy roboczej [Shared Signals and Events](https://openid.net/wg/sse/) w OpenID Foundation. Dostawcy tożsamości i podmioty uzależniające będą mogli wykorzystać zdarzenia i sygnały zabezpieczeń zdefiniowane przez grupę roboczą do ponownego autoryzowania lub zakończenia dostępu. Jest to ekscytująca praca i poprawi bezpieczeństwo na wielu platformach i aplikacjach.

Ponieważ korzyści w zakresie bezpieczeństwa są tak duże, wdrażamy początkową implementację specyficzną firmy Microsoft równolegle do naszej dalszej pracy w organach normalizacyjnych. W miarę wdrażania tych funkcji ciągłej oceny dostępu (CAE) w usługach firmy Microsoft wiele się nauczyliśmy i udostępniamy te informacje społeczności standardów. Mamy nadzieję, że nasze doświadczenie we wdrażaniu pomoże w osiągnięciu jeszcze lepszego standardu branżowego i zobowiązujemy się do wdrożenia tego standardu po ich ratyfikacji, umożliwiając wszystkim uczestniczącym usługom korzystanie z tego systemu.

## <a name="how-does-cae-work-in-microsoft-services"></a>Jak działa CAE w usługach firmy Microsoft?

Koncentrujemy naszą wstępną implementację ciągłej oceny dostępu do exchange i zespołów. Mamy nadzieję rozszerzyć wsparcie na inne usługi firmy Microsoft w przyszłości. Zaczniemy włączać ciągłą ocenę dostępu tylko dla dzierżawców bez zasad dostępu warunkowego. Wykorzystamy nasze wnioski z tej fazy CAE, aby poinformować o naszym ciągłym wdrażaniu CAE.

## <a name="service-side-requirements"></a>Wymagania dotyczące serwisu

Ciągła ocena dostępu jest implementowana przez umożliwienie usługom (dostawcom zasobów) subskrybowania zdarzeń o znaczeniu krytycznym w usłudze Azure AD, dzięki czemu zdarzenia te mogą być oceniane i wymuszane w czasie zbliżonym do rzeczywistego. Następujące zdarzenia będą wymuszane w tym początkowym wdrożeniem CAE:

- Konto użytkownika jest usuwane lub wyłączone
- Hasło użytkownika zostało zmienione lub zresetowane
- Administrator jawnie odwołuje wszystkie tokeny odświeżania dla użytkownika
- Podwyższone ryzyko użytkownika wykryte przez usługę Azure AD Identity Protection

W przyszłości mamy nadzieję dodać więcej zdarzeń, w tym zdarzeń, takich jak zmiany lokalizacji i stanu urządzenia. **Podczas gdy naszym celem jest, aby egzekwowanie było natychmiastowe, w niektórych przypadkach opóźnienie do 15 minut może być obserwowane ze względu na czas propagacji zdarzeń.** 

## <a name="client-side-claim-challenge"></a>Wyzwanie roszczenia po stronie klienta

Przed ciągłą oceną dostępu klienci zawsze będą próbować odtworzyć token dostępu z jego pamięci podręcznej, o ile nie wygasł. W przypadku CAE wprowadzamy nową sprawę, w którym dostawca zasobów może odrzucić token, nawet jeśli nie wygasł. Aby poinformować klientów o ominięciu pamięci podręcznej, nawet jeśli tokeny buforowane nie wygasły, wprowadzamy mechanizm zwany **żądaniem.** CAE wymaga aktualizacji klienta, aby zrozumieć wyzwanie roszczenia. Najnowsza wersja następujących aplikacji poniżej wsparcie roszczenia wyzwanie:

- Program Outlook dla systemu Windows 
- Aplikacja Outlook dla systemu iOS 
- Outlook Android 
- Outlook Mac 
- Usługa Teams dla systemu Windows
- Zespoły iOS 
- Zespoły Android 
- Teams Mac 

## <a name="token-lifetime"></a>Czas życia tokenu

Ponieważ ryzyko i zasady są oceniane w czasie rzeczywistym, klienci negocjują sesje obsługujące ocenę ciągłego dostępu będą polegać na CAE zamiast na istniejących zasadach okresu istnienia tokenu dostępu statycznego, co oznacza, że konfigurowalne zasady okresu istnienia tokenu nie będą już honorowane dla klientów obsługujących CAE, którzy negocjują sesje obsługujące CAE.

Wydłużymy okres istnienia tokenu dostępu do 24 godzin w sesjach CAE. Odwołanie jest spowodowane krytycznymi zdarzeniami i oceną zasad, a nie arbitralnym okresem. Ta zmiana zwiększa stabilność aplikacji bez wpływu na postawę zabezpieczeń. 

## <a name="example-flows"></a>Przykładowe przepływy

### <a name="user-revocation-event-flow"></a>Przepływ zdarzeń odwołania użytkownika:

![Przepływ zdarzeń odwołania użytkownika](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient obsługujący cae przedstawia poświadczenia lub token odświeżania do usługi AAD z prośbą o token dostępu dla niektórych zasobów.
1. Token dostępu jest zwracany wraz z innymi artefaktami do klienta.
1. Administrator jawnie [odwołuje wszystkie tokeny odświeżania dla użytkownika](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Zdarzenie odwołania zostanie wysłane do dostawcy zasobów z usługi Azure AD.
1. Token dostępu jest prezentowany dostawcy zasobów. Dostawca zasobów ocenia ważność tokenu i sprawdza, czy istnieje zdarzenie odwołania dla użytkownika. Dostawca zasobów używa tych informacji, aby zdecydować o udzieleniu dostępu do zasobu, czy nie.
1. W takim przypadku dostawca zasobów odmawia dostępu i wysyła do klienta żądanie 401+
1. Klient obsługujący CAE rozumie wyzwanie roszczenia 401+. Pomija pamięci podręczne i wraca do kroku 1, wysyłając jego token odświeżania wraz z wyzwaniem oświadczeń z powrotem do usługi Azure AD. Usługa Azure AD następnie ponownie oceni wszystkie warunki i poprosi użytkownika o ponowne uwierzytelnienie w tym przypadku.
 
## <a name="faqs"></a>Często zadawane pytania

### <a name="what-is-the-lifetime-of-my-access-token"></a>Jaki jest okres istnienia tokenu dostępu?

Jeśli nie używasz klientów obsługujących CAE, domyślny okres istnienia tokenu dostępu będzie nadal 1 godzina, chyba że skonfigurowano okres istnienia tokenu dostępu z funkcją podglądu [konfigurowalny okres istnienia tokenu (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

Jeśli używasz klientów obsługujących CAE, którzy negocjują sesje obsługujące CAE, ustawienia CTL dla okresu istnienia tokenu dostępu zostaną zastąpione, a okres istnienia tokenu dostępu będzie 24 godziny.

### <a name="how-quick-is-enforcement"></a>Jak szybkie jest egzekwowanie przepisów?

Podczas gdy naszym celem jest, aby wymuszanie było natychmiastowe, w niektórych przypadkach opóźnienie do 15 minut może być obserwowane ze względu na czas propagacji zdarzeń.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak cae będzie działać z częstotliwością logowania?

Częstotliwość logowania będzie honorowana z CAE lub bez niego.

## <a name="next-steps"></a>Następne kroki

[Ogłaszanie ciągłej oceny dostępu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
