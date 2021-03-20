---
title: Korygowanie zagrożeń i odblokowywanie użytkowników w Azure AD Identity Protection
description: Dowiedz się więcej na temat opcji zamykających aktywne wykrywania zagrożeń.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872828"
---
# <a name="remediate-risks-and-unblock-users"></a>Korygowanie zagrożeń i odblokowywanie użytkowników

Po zakończeniu [badania](howto-identity-protection-investigate-risk.md)należy podjąć działania w celu skorygowania ryzyka lub odblokowania użytkowników. Organizacje mogą również włączyć automatyczne korygowanie przy użyciu [zasad ryzyka](howto-identity-protection-configure-risk-policies.md). Organizacje powinny próbować zamknąć wszystkie wykryte zagrożenia, które są prezentowane w danym okresie. Firma Microsoft zaleca zamykanie zdarzeń najszybciej, jak to możliwe, ponieważ czas pracy z ryzykiem.

## <a name="remediation"></a>Korekty

Wszystkie aktywne wykrycia ryzyka przyczyniają się do obliczenia wartości o nazwie poziom ryzyka użytkownika. Poziom ryzyka użytkownika to wskaźnik (niski, średni, wysoki) dla prawdopodobieństwa naruszenia zabezpieczeń konta. Jako administrator chcesz, aby wszystkie wykrycia ryzyka były zamknięte, dzięki czemu zagrożoni użytkownicy nie będą już zagrożeni.

Niektóre wykryte ryzyka mogą być oznaczone przez funkcję ochrony tożsamości jako "zamknięte (systemowe)", ponieważ zdarzenia nie zostały już określone jako ryzykowne.

Administratorzy mogą korzystać z następujących opcji korygowania:

- Samodzielne korygowanie z użyciem zasad ryzyka
- Ręczne resetowanie hasła
- Odrzuć ryzyko związane z użytkownikiem
- Ręczne zamykanie indywidualnych wykrycia ryzyka

### <a name="self-remediation-with-risk-policy"></a>Samodzielne korygowanie z użyciem zasad ryzyka

Jeśli zezwolisz użytkownikom na samodzielne korygowanie, za pomocą usługi Azure AD Multi-Factor Authentication (MFA) i samoobsługowego resetowania hasła (SSPR) w ramach zasad ryzyka, mogą one odblokować się w przypadku wykrycia ryzyka. Te wykrycia są następnie uważane za zamknięte. Użytkownicy musieli wcześniej zarejestrowali usługę Azure AD MFA i SSPR, aby można było używać ich w przypadku wykrycia ryzyka.

Niektóre środki wykrywania mogą nie powodować zagrożenia dla poziomu sytuacji, w której użytkownik będzie musiał przeprowadzić samodzielną korektę, ale Administratorzy powinni nadal oszacować te wykrycia. Administratorzy mogą określić, że dodatkowe środki są niezbędne, takie jak [blokowanie dostępu z lokalizacji](../conditional-access/howto-conditional-access-policy-location.md) lub obniżenie akceptowalnego ryzyka w swoich zasadach.

### <a name="manual-password-reset"></a>Ręczne resetowanie hasła

Jeśli wymaganie resetowania hasła przy użyciu zasad ryzyka użytkownika nie jest opcją, Administratorzy mogą zamknąć wszystkie wykrycia ryzyka dla użytkownika z ręcznym resetowaniem hasła.

Administratorzy otrzymują dwie opcje podczas resetowania hasła dla swoich użytkowników:

- **Generuj hasło tymczasowe** — przez wygenerowanie hasła tymczasowego możesz natychmiast przywrócić tożsamość z powrotem do stanu bezpiecznego. Ta metoda wymaga skontaktowania się z użytkownikami, których to dotyczy, ponieważ chcą wiedzieć, co to jest hasło tymczasowe. Ponieważ hasło jest tymczasowe, użytkownik jest monitowany o zmianę hasła na coś nowego podczas kolejnego logowania.

- **Wymagaj od użytkownika zresetowania hasła** — wymaganie od użytkowników resetowania haseł umożliwia samoodzyskiwanie bez kontaktowania się z pomocą techniczną lub administratorem. Ta metoda ma zastosowanie tylko do użytkowników zarejestrowanych dla usługi Azure AD MFA i SSPR. W przypadku użytkowników, którzy nie zostali zarejestrowani, ta opcja jest niedostępna.

### <a name="dismiss-user-risk"></a>Odrzuć ryzyko związane z użytkownikiem

Jeśli Resetowanie hasła nie jest opcją dla Ciebie, ponieważ na przykład użytkownik został usunięty, można zrezygnować z wykrywania ryzyka użytkownika.

Po kliknięciu przycisku **Odrzuć ryzyko związane z użytkownikiem** wszystkie zdarzenia zostaną zamknięte, a zagrożony użytkownik nie jest już narażony. Jednak ponieważ ta metoda nie ma wpływu na istniejące hasło, nie powoduje, że powiązana tożsamość nie zostanie przywrócona do stanu bezpiecznego. 

### <a name="close-individual-risk-detections-manually"></a>Ręczne zamykanie indywidualnych wykrycia ryzyka

Wykrywanie indywidualnych zagrożeń można zamknąć ręcznie. Poprzez ręczne zamykanie wykrywania ryzyka można obniżyć poziom ryzyka użytkownika. Zazwyczaj wykrycia ryzyka są zamykane ręcznie w odpowiedzi na powiązane badanie. Na przykład podczas rozmowy z użytkownikiem wykaże, że aktywne wykrywanie ryzyka nie jest już wymagane. 
 
Podczas ręcznego zamykania wykrywania ryzyka można wykonać jedną z następujących czynności, aby zmienić stan wykrywania ryzyka:

- Potwierdzanie naruszenia zabezpieczeń użytkownika
- Odrzuć ryzyko związane z użytkownikiem
- Potwierdź bezpieczne logowanie
- Potwierdzanie naruszenia logowania

## <a name="unblocking-users"></a>Odblokowywanie użytkowników

Administrator może zdecydować się na zablokowanie logowania na podstawie ich zasad lub dochodzeń związanych z ryzykiem. Może wystąpić blok oparty na zalogowaniu lub ryzyku użytkownika.

### <a name="unblocking-based-on-user-risk"></a>Odblokowywanie na podstawie ryzyka użytkownika

Aby odblokować konto zablokowane z powodu ryzyka użytkownika, Administratorzy mogą korzystać z następujących opcji:

1. **Resetowanie hasła** — możesz zresetować hasło użytkownika.
1. **Odrzuć ryzyko** związane z użytkownikiem — zasady ryzyka dla użytkowników blokują użytkownika, jeśli osiągnięto skonfigurowany poziom ryzyka użytkownika na potrzeby blokowania dostępu. Poziom ryzyka użytkownika można zmniejszyć, odrzucając ryzyko związane z ryzykiem lub ręcznie zamykając zgłoszone wykrycie zagrożeń.
1. **Wykluczanie użytkownika z zasad** — Jeśli sądzisz, że Bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w artykule [jak: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Wyłącz zasady** — Jeśli sądzisz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz artykuł [jak: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Odblokowywanie na podstawie ryzyka związanego z logowaniem

Aby odblokować konto na podstawie ryzyka związanego z logowaniem, Administratorzy mogą skorzystać z następujących opcji:

1. **Zaloguj się ze znanej lokalizacji lub urządzenia** — typową przyczyną zablokowanych podejrzanych logowań są próby logowania z nieznanych lokalizacji lub urządzeń. Użytkownicy mogą szybko określić, czy powód to powód blokowania, próbując zalogować się ze znajomej lokalizacji lub urządzenia.
1. **Wykluczanie użytkownika z zasad** — Jeśli sądzisz, że Bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w artykule [jak: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Wyłącz zasady** — Jeśli sądzisz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz artykuł [jak: Konfigurowanie i Włączanie zasad ryzyka](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>Podgląd programu PowerShell

Za pomocą modułu Microsoft Graph programu PowerShell SDK w wersji zapoznawczej organizacje mogą zarządzać ryzykiem przy użyciu programu PowerShell. Moduły w wersji zapoznawczej i przykładowy kod można znaleźć w [repozytorium GitHub usługi Azure AD](https://github.com/AzureAD/IdentityProtectionTools). 

`Invoke-AzureADIPDismissRiskyUser.ps1`Skrypt uwzględniony w repozytorium umożliwia organizacjom odrzucanie wszystkich ryzykownych użytkowników w ich katalogach.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview-identity-protection.md).
