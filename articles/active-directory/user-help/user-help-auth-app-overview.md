---
title: Czym jest aplikacja Microsoft Authenticator? — Azure Active Directory | Microsoft Docs
description: Informacje o aplikacji Microsoft Authenticator, w tym wyjaśnienie czym ona jest oraz jak działa, a także jakie informacje zawarto w tej sekcji zawartości.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055235"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Czym jest aplikacja Microsoft Authenticator?

Aplikacja Microsoft Authenticator pomaga w logowaniu się do kont, jeśli używana jest weryfikacja dwuskładnikowa. Weryfikacja dwuskładnikowa zapewnia bezpieczniejszy dostęp do kont, szczególnie podczas wyświetlania informacji poufnych. Ponieważ hasła mogą zostać zapomniane, skradzione lub ujawnione, weryfikacja dwuskładnikowa zapewnia dodatkowy poziom zabezpieczeń, który pomaga chronić konto, utrudniając włamanie się na nie innym osobom.

Aplikacji Microsoft Authenticator można używać na wiele sposobów, na przykład:

- Odpowiedz na monit o uwierzytelnienie po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

- Zaloguj się bez wprowadzania hasła przy użyciu nazwy użytkownika, aplikacji uwierzytelniania i urządzenia przenośnego z odciskiem palca, z własnej sieci lub numeru PIN.

- Jako generator kodu dla innych kont, które obsługują aplikacje uwierzytelniające.

> [!Important]
> Aplikacja Microsoft Authenticator współpracuje z dowolnym kontem korzystającym z weryfikacji dwuskładnikowej i obsługuje standardy hasła jednorazowego (TOTP) oparte na czasie.
>
>Ten artykuł jest przeznaczony dla użytkowników próbujących pobrać i użyć aplikacji Microsoft Authenticator jako metody weryfikacji zabezpieczeń. Jeśli jesteś administratorem, który szuka informacji o sposobie włączania logowania bez hasła przy użyciu aplikacji uwierzytelniania dla pracowników i innych funkcji, zobacz [Włączanie logowania bezhasłem przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)](../authentication/howto-authentication-passwordless-phone.md).

## <a name="terminology"></a>Terminologia

| Okres|Opis|
| ----|-----------|
| Weryfikacja dwuskładnikowa | Proces weryfikacji, który wymaga specjalnego użycia tylko dwóch rodzajów informacji o weryfikacji, takich jak hasło i numer PIN. Aplikacja Microsoft Authenticator obsługuje zarówno standardową weryfikację dwuskładnikową, jak i logowanie bez hasła. |
| Multi-Factor Authentication (MFA) | Wszystkie weryfikacje dwuskładnikowe są odmianami uwierzytelniania wieloskładnikowego, które wymaga użycia *co najmniej* dwóch rodzajów informacji o weryfikacji, w zależności od wymagań organizacji. |
| Konto Microsoft (nazywane też kontem MSA) | Tworzysz własne konta osobiste, aby uzyskać dostęp do firmowych produktów firmy Microsoft i usług w chmurze, takich jak Outlook, OneDrive, Xbox LIVE lub Microsoft 365. Konto Microsoft jest tworzone i przechowywane w systemie kont tożsamości konsumentów firmy Microsoft. |
| Konto służbowe | Twoja organizacja tworzy konto służbowe (np alain@contoso.com .), aby umożliwić dostęp do wewnętrznych i potencjalnie ograniczonych zasobów, takich jak Microsoft Azure, Windows Intune i Microsoft 365. |
| Kod weryfikacyjny | Sześciocyfrowy kod, który jest wyświetlany w aplikacji wystawcy uwierzytelnienia, w obszarze każdego dodanego konta. Kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając wielokrotne użycie kodu. Jest to również nazywany jednorazowym kodem dostępu (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>W jaki sposób weryfikacja dwuskładnikowa współdziała z aplikacją

Weryfikacja dwuskładnikowa współdziała z aplikacją Microsoft Authenticator w następujący sposób:

- **Zawiadomienie.** Wpisz nazwę użytkownika i hasło na urządzeniu, za pomocą którego logujesz się do swojego służbowego lub osobistego konta Microsoft, a aplikacja Microsoft Authenticator wyśle powiadomienie z prośbą o **zatwierdzenie logowania**. Wybierz pozycję **Zatwierdź**, jeśli rozpoznajesz próbę logowania. W przeciwnym razie wybierz pozycję **Odmów**. Jeśli wybierzesz pozycję **Odmów**, możesz również oznaczyć żądanie jako fałszywe.

- **Kod weryfikacyjny.** Wpisz nazwę użytkownika i hasło na urządzeniu, za pomocą którego logujesz się do swojego służbowego lub osobistego konta Microsoft, a następnie skopiuj powiązany kod weryfikacyjny z ekranu **Konta** aplikacji Microsoft Authenticator. Kod weryfikacyjny jest również nazywany uwierzytelnianiem jednorazowym kodem dostępu (OTP).

- **Logowanie bez hasła.** Wpisz nazwę użytkownika na urządzeniu, za pomocą którego logujesz się do swojego służbowego lub osobistego konta Microsoft, a następnie użyj urządzenia przenośnego do potwierdzenia swojej tożsamości za pomocą odcisku palca, twarzy lub numeru PIN. W przypadku tej metody nie musisz wprowadzać hasła.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Zdecydowanie o użyciu możliwości biometrycznych swojego urządzenia

Jeśli do zakończenia procesu uwierzytelniania używasz numeru PIN, możesz skonfigurować aplikację Microsoft Authenticator, aby zamiast tego używała możliwości biometrycznych (rozpoznawania odcisku palca lub twarzy) urządzenia. Możesz to skonfigurować podczas pierwszego użycia aplikacji Authenticator do zweryfikowania swojego konta. Wystarczy jako metodę identyfikacji wybrać opcję użycia możliwości biometrycznych urządzenia zamiast numeru PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Kto decyduje o możliwości użycia tej funkcji?

W zależności od typu konta o konieczności korzystania z weryfikacji dwuskładnikowej może zdecydować Twoja organizacja lub ta decyzja może zostać przeniesiona na użytkownika.

- **Konto służbowe.** Jeśli używasz konta służbowego (na przykład alain@contoso.com), to Twoja organizacja decyduje, czy musisz korzystać z weryfikacji dwuskładnikowej i konkretnych metod weryfikacji. Aby uzyskać więcej informacji o dodawaniu konta służbowego do aplikacji Microsoft Authenticator, zobacz [Dodawanie konta służbowego](user-help-auth-app-add-work-school-account.md).

- **Osobiste konto Microsoft.** Weryfikację dwuskładnikowa można skonfigurować dla osobistych kont Microsoft (na przykład alain@outlook.com). Aby uzyskać więcej informacji na temat dodawania Twojego osobistego konta Microsoft, zobacz [Dodawanie kont osobistych](user-help-auth-app-add-personal-ms-account.md).

- **Konto inne niż Microsoft.** Weryfikację dwuskładnikowa można skonfigurować dla kont innych niż Microsoft (na przykład alain@gmail.com). Konta nienależące do firmy Microsoft mogą używać innego terminu na oznaczenia weryfikacji dwuskładnikowej. Funkcja ta powinna znajdować się w ustawieniach zabezpieczeń (**Security**) lub ustawieniach logowania (**Sign-in**). Aplikacja Microsoft Authenticator działa przy użyciu dowolnego konta, które obsługuje standardy TOTP. Aby uzyskać więcej informacji na temat dodawanie kont innych niż Microsoft, zobacz [Dodawanie kont innych niż Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>W tej sekcji

| Artykuł | Opis |
| ------ | ------------ |
| [Pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md) | W tym artykule opisano lokalizację oraz sposób pobierania i instalowania aplikacji Microsoft Authenticator dla urządzeń z systemami Android i iOS. |
| [Dodawanie kont służbowych](user-help-auth-app-add-work-school-account.md) | W tym artykule opisano sposób dodawania różnych służbowych i osobistych kont do aplikacji Microsoft Authenticator. |
| [Dodawanie kont osobistych](user-help-auth-app-add-personal-ms-account.md) | W tym artykule opisano sposób dodawania do aplikacji Microsoft Authenticator osobistych kont Microsoft. |
| [Dodawanie kont innych niż Microsoft](user-help-auth-app-add-non-ms-account.md) | W tym artykule opisano sposób dodawania do aplikacji Microsoft Authenticator kont nienależących do firmy Microsoft. |
| [Ręczne dodawanie kont](user-help-auth-app-add-account-manual.md) | W tym artykule opisano ręczne dodawanie kont do aplikacji Microsoft Authenticator, jeśli nie można zeskanować udostępnionego kodu QR. |
| [Logowanie się za pomocą aplikacji](user-help-auth-app-sign-in.md) | W tym artykule opisano sposób logowania się do różnych kont przy użyciu aplikacji Microsoft Authenticator.|
| [Wykonywanie kopii zapasowych i odzyskiwanie poświadczeń konta](user-help-auth-app-backup-recovery.md) | Zawiera informacje dotyczące tworzenia kopii zapasowej i odzyskiwania poświadczeń konta za pomocą aplikacji Microsoft Authenticator. |
| [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md) | Zawiera odpowiedzi na często zadawane pytania dotyczące aplikacji. |