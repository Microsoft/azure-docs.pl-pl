---
title: Logowanie przy użyciu uwierzytelniania za pomocą konta służbowego — Azure AD
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
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834170"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Logowanie się do konta służbowego przy użyciu metody weryfikacji dwuskładnikowej

> [!NOTE]
> Celem tego artykułu jest przeprowadzenie typowych czynności związanych z logowaniem. Aby uzyskać pomoc przy rejestrowaniu lub rozwiązywaniu problemów, zobacz problem [z usługą Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Jakie będzie środowisko logowania?
Środowisko logowania różni się w zależności od tego, co jest używane jako drugi czynnik: połączenie telefoniczne, aplikacja uwierzytelniania lub teksty. Wybierz opcję, która najlepiej opisuje to, co robisz:

| Jak się zalogować? |
| --- |
| [Połączenie telefoniczne z moim telefonem komórkowym lub biurem](#signing-in-with-a-phone-call) |
| [Z tekstem na moim telefonie komórkowym](#signing-in-with-a-text-message)
| [Z powiadomieniami z aplikacji Microsoft Authenticator](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Z kodem weryfikacyjnym z aplikacji Microsoft Authenticator |
| [Za pomocą alternatywnej metody, ponieważ nie można teraz używać metody my preferowanej](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logowanie za pomocą połączenia telefonicznego
Poniższe informacje opisują proces weryfikacji dwuetapowej z wywołaniem telefonu komórkowego lub biurowego.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.  
2. Firma Microsoft dzwoni do Ciebie.  
3. Odpowiedz na telefon i naciśnij klawisz #.  

## <a name="signing-in-with-a-text-message"></a>Logowanie za pomocą wiadomości SMS
Poniższe informacje opisują proces weryfikacji dwuetapowej przy użyciu wiadomości tekstowej na telefonie komórkowym:

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła do Ciebie wiadomość tekstową zawierającą kod liczbowy.
3. Wprowadź kod w polu udostępnionym na stronie logowania.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logowanie za pomocą aplikacji Microsoft Authenticator
Poniższe informacje opisują środowisko korzystania z aplikacji Microsoft Authenticator na potrzeby weryfikacji dwuetapowej. Istnieją dwa różne sposoby używania aplikacji. Można odbierać powiadomienia wypychane na urządzeniu lub można otworzyć aplikację w celu uzyskania kodu weryfikacyjnego.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu powiadomienia z aplikacji Microsoft Authenticator
1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft wysyła powiadomienie do aplikacji Microsoft Authenticator na urządzeniu.

   ![Firma Microsoft wysyła powiadomienie](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Otwórz powiadomienie na telefonie i wybierz polecenie **Weryfikuj** klucz. Jeśli firma wymaga numeru PIN, wprowadź ją w tym miejscu.
4. Teraz należy się zalogować.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aby zalogować się przy użyciu kodu weryfikacyjnego za pomocą aplikacji Microsoft Authenticator

Jeśli używasz aplikacji Microsoft Authenticator do uzyskiwania kodów weryfikacyjnych, po otwarciu aplikacji zobaczysz numer w polu Nazwa konta. Ta liczba zmienia się co 30 sekund, aby nie używała tego samego numeru dwa razy. Gdy zostanie wyświetlony monit o podanie kodu weryfikacyjnego, Otwórz aplikację i użyj dowolnego numeru, który jest aktualnie wyświetlany.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Firma Microsoft pyta o kod weryfikacyjny.

   ![Wprowadź kod weryfikacyjny](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Otwórz aplikację Microsoft Authenticator na telefonie i wprowadź kod w polu, w którym się logujesz.

## <a name="signing-in-with-an-alternate-method"></a>Logowanie za pomocą alternatywnej metody
Czasami nie masz telefonu ani urządzenia skonfigurowanego jako preferowana metoda weryfikacji. Dlatego zalecamy skonfigurowanie metod tworzenia kopii zapasowych dla Twojego konta. W poniższej sekcji pokazano, jak zalogować się przy użyciu alternatywnej metody, jeśli podstawowa metoda może być niedostępna.

1. Zaloguj się do aplikacji lub usługi, takiej jak Microsoft 365 przy użyciu nazwy użytkownika i hasła.
2. Wybierz opcję **Użyj innej opcji weryfikacji**. Zobaczysz różne opcje weryfikacji w zależności od tego, ile masz konfiguracji.
3. Wybierz alternatywną metodę i zaloguj się.

   ![Użyj alternatywnej metody](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Następne kroki
- Jeśli masz problemy z zalogowaniem się przy użyciu weryfikacji dwuetapowej, uzyskaj więcej informacji na temat problemów [z usługą Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Dowiedz się, jak [zarządzać ustawieniami weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md).

- Dowiedz się, jak rozpocząć [pracę z aplikacją Microsoft Authenticator](user-help-auth-app-download-install.md) , aby można było zalogować się przy użyciu powiadomień zamiast tekstów i połączeń telefonicznych.
