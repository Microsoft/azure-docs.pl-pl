---
title: Logowanie przy użyciu uwierzytelniania przy użyciu konta służbowego — Azure AD
description: Dowiedz się, jak zalogować się do konta służbowego przy użyciu różnych metod weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: b373288eb5cd47f99bdbb25f961e1330a708d7d1
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739192"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Zaloguj się do konta służbowego przy użyciu metody weryfikacji dwuskładnikowej

> [!NOTE]
> Celem tego artykułu jest omówienie typowego logowania. Aby uzyskać pomoc w zakresie logowania lub rozwiązywania problemów, zobacz Temat Having problems with Azure AD Multi-Factor Authentication (Problemy z uwierzytelnianiem [wieloskładnikowym w usłudze Azure AD).](multi-factor-authentication-end-user-troubleshoot.md)

## <a name="what-will-your-sign-in-experience-be"></a>Jakie będzie środowisko logowania?
Środowisko logowania różni się w zależności od tego, co wybierzesz jako drugi czynnik: połączenie telefoniczne, aplikacja do uwierzytelniania lub teksty. Wybierz opcję, która najlepiej opisuje to, co robisz:

| Jak się zalogować? |
| --- |
| [Za pomocą połączenia telefonicznego z moim telefonem komórkowym lub biurowym](#signing-in-with-a-phone-call) |
| [Za pomocą wiadomości SMS na mój telefon komórkowy](#signing-in-with-a-text-message)
| [Za pomocą powiadomień z Microsoft Authenticator aplikacji](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Za pomocą kodów weryfikacyjnych z Microsoft Authenticator aplikacji |
| [Z alternatywną metodą, ponieważ nie mogę teraz użyć preferowanej metody](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logowanie za pomocą połączenia telefonicznego
Poniżej opisano środowisko weryfikacji dwuetapowej z połączeniem z telefonem komórkowym lub biurowym.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.  
2. Firma Microsoft dzwoni do Ciebie.  
3. Odpowiedz na telefon i naciśnij klawisz #.  

## <a name="signing-in-with-a-text-message"></a>Logowanie przy użyciu wiadomości SMS
Poniższe informacje opisują dwuetapowe środowisko weryfikacji z wiadomością SMS na telefon komórkowy:

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła wiadomość SMS zawierającą kod liczbowy.
3. Wprowadź kod w polu dostarczonym na stronie logowania.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logowanie się przy użyciu Microsoft Authenticator aplikacji
Poniższe informacje opisują sposób korzystania z aplikacji Microsoft Authenticator do weryfikacji dwuetapowej. Istnieją dwa różne sposoby korzystania z aplikacji. Możesz odbierać powiadomienia wypychane na urządzeniu lub otworzyć aplikację, aby uzyskać kod weryfikacyjny.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu powiadomienia z Microsoft Authenticator aplikacji
1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła powiadomienie do aplikacji Microsoft Authenticator na Urządzeniu.

   ![Firma Microsoft wysyła powiadomienie](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otwórz powiadomienie na telefonie i wybierz pozycję **Weryfikuj** klucz. Jeśli Firma wymaga numeru PIN, wprowadź go tutaj.
4. Teraz powinno na ciebie zostać zalogowanie.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu kodu weryfikacyjnego za pomocą Microsoft Authenticator aplikacji

Jeśli używasz aplikacji Microsoft Authenticator do uzyskania kodów weryfikacyjnych, po otwarciu aplikacji zobaczysz numer pod nazwą konta. Ta liczba zmienia się co 30 sekund, aby nie używać tej samej liczby dwa razy. Gdy zostanie wyświetlony monit o kod weryfikacyjny, otwórz aplikację i użyj dowolnego aktualnie wyświetlanego numeru.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wyświetli monit o kod weryfikacyjny.

   ![Wprowadzanie kodu weryfikacyjnego](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otwórz aplikację Microsoft Authenticator telefonie i wprowadź kod w polu, w którym się logujesz.

## <a name="signing-in-with-an-alternate-method"></a>Logowanie się przy użyciu alternatywnej metody
Czasami nie masz telefonu lub urządzenia, które zostały ustawione jako preferowana metoda weryfikacji. W takiej sytuacji zalecamy skonfigurowanie metod tworzenia kopii zapasowych dla konta. W poniższej sekcji pokazano, jak zalogować się przy użyciu alternatywnej metody, gdy metoda podstawowa może być niedostępny.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Wybierz **opcję Użyj innej opcji weryfikacji.** Zobaczysz różne opcje weryfikacji w zależności od tego, ile masz konfiguracji.
3. Wybierz alternatywną metodę i zaloguj się.

   ![Używanie metody alternatywnej](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Następne kroki
- Jeśli masz problemy z logowaniem przy użyciu weryfikacji dwuetapowej, uzyskaj więcej informacji na stronie Having problems with Azure AD Multi-Factor Authentication (Problemy z usługą [Azure AD Multi-Factor Authentication).](multi-factor-authentication-end-user-troubleshoot.md)

- Dowiedz się, jak [zarządzać ustawieniami weryfikacji dwuetapowej.](multi-factor-authentication-end-user-manage-settings.md)

- Dowiedz się, jak [rozpocząć pracę z aplikacją Microsoft Authenticator,](user-help-auth-app-download-install.md) aby używać powiadomień do logowania się zamiast wiadomości sms i połączeń telefonicznych.
