---
title: Logowanie przy użyciu informacji o uwierzytelnianiu tożsamości — Azure AD
description: Dowiedz się więcej na temat sposobu logowania przy użyciu różnych metod weryfikacji tożsamości w temacie Informacje zabezpieczające.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88799387"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Zaloguj się przy użyciu weryfikacji dwuetapowej lub informacji zabezpieczających

Po skonfigurowaniu weryfikacji dwuetapowej lub informacji o zabezpieczeniach będziesz mieć możliwość zalogowania się do konta przy użyciu określonej metody weryfikacji.

> [!Note]
> Jeśli nadal używasz środowiska weryfikacji dwuetapowej, musisz skonfigurować metody weryfikacji, postępując zgodnie z instrukcjami podanymi w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .
>
> Jeśli administrator włączył środowisko informacje zabezpieczające, należy ustawić metody weryfikacyjne przy użyciu następujących artykułów krok po kroku:<ul><li>[Skonfiguruj informacje zabezpieczające do korzystania z aplikacji uwierzytelniania](security-info-setup-auth-app.md)</li><li>[Set up security info to use text messaging](security-info-setup-text-msg.md) (Konfigurowanie informacji zabezpieczających w celu korzystania z wiadomości tekstowych)</li><li>[Skonfiguruj informacje zabezpieczające do korzystania z połączenia telefonicznego](security-info-setup-phone-number.md)</li><li>[Skonfiguruj informacje zabezpieczające, aby użyć klucza zabezpieczeń](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Zaloguj się przy użyciu powiadomienia aplikacji uwierzytelniania na urządzeniu przenośnym

1. Zaloguj się do swojego konta przy użyciu nazwy użytkownika i hasła.

2. Wybierz pozycję **Zatwierdź** na podstawie powiadomienia o zatwierdzeniu wysłanego do urządzenia przenośnego.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Zaloguj się przy użyciu kodu aplikacji uwierzytelniania na urządzeniu przenośnym

1. Zaloguj się do swojego konta przy użyciu nazwy użytkownika i hasła.

2. Otwórz aplikację Authenticator i wpisz losowo wygenerowany kod dla swojego konta w polu **Wprowadź kod** .

## <a name="sign-in-using-your-phone-number"></a>Zaloguj się przy użyciu numeru telefonu

1. Zaloguj się do swojego konta przy użyciu nazwy użytkownika i hasła.

2. Odpowiedz na telefonie i postępuj zgodnie z instrukcjami.

## <a name="sign-in-using-a-text-message"></a>Zaloguj się przy użyciu wiadomości tekstowej

1. Zaloguj się do swojego konta przy użyciu nazwy użytkownika i hasła.

2. Otwórz wiadomość tekstową i wpisz kod z wiadomości tekstowej w polu **Wprowadź kod** .

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Zaloguj się przy użyciu klucza zabezpieczeń na ekranie blokady

1. Po zarejestrowaniu klucza zabezpieczeń wybierz obraz klucza zabezpieczeń z ekranu blokady systemu Windows 10.

2. Wstaw klucz zabezpieczeń do portu USB urządzenia i zaloguj się do systemu Windows przy użyciu numeru PIN klucza zabezpieczeń.

    ![Logowanie za pomocą klucza zabezpieczeń na ekranie blokady systemu Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Zaloguj się przy użyciu klucza zabezpieczeń i przeglądarki Microsoft Edge

1. Po zarejestrowaniu klucza zabezpieczeń Otwórz przeglądarkę Microsoft Edge.

2. Po wyświetleniu monitu o zalogowanie należy wstawić klucz zabezpieczeń do portu USB urządzenia i zalogować się do systemu Windows przy użyciu numeru PIN klucza zabezpieczeń.

    ![Logowanie za pomocą klucza zabezpieczeń przy użyciu przeglądarki Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Aby uzyskać informacje na temat logowania za pomocą aplikacji Microsoft Authenticator, zobacz artykuł [Logowanie do kont przy użyciu aplikacji Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Zaloguj się przy użyciu innej metody weryfikacji

Jeśli z jakiegoś powodu nie możesz użyć podstawowej metody logowania, możesz użyć innej wcześniej skonfigurowanej metody weryfikacji.

1. Zaloguj się do swojego konta w zwykły sposób, a następnie wybierz łącze **Zaloguj w innym** miejscu na stronie **weryfikacji dwuetapowej** .

    ![Zmień metodę weryfikacji logowania](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Jeśli nie widzisz linku **w inny sposób** , oznacza to, że nie zostały skonfigurowane żadne inne metody weryfikacji i należy skontaktować się z administratorem w celu uzyskania pomocy w celu zalogowania się do konta. Gdy administrator pomoże Ci się zalogować, pamiętaj o dodaniu dodatkowych metod weryfikacji. Aby uzyskać więcej informacji na temat dodawania metod weryfikacji, zobacz artykuł [Zarządzanie ustawieniami w ramach weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md) .
    >
    >Jeśli zobaczysz link **w inny sposób** , ale nadal nie zobaczysz żadnych innych metod weryfikacji, musisz skontaktować się z administratorem w celu uzyskania pomocy w celu zalogowania się do konta.

2. Wybierz alternatywną metodę weryfikacji i Kontynuuj proces weryfikacji dwuetapowej.

3. Po ponownym uruchomieniu konta możesz zaktualizować metody weryfikacyjne (jeśli to konieczne). Aby uzyskać więcej informacji na temat dodawania lub zmieniania metod, zobacz artykuł [Zarządzanie ustawieniami w ramach weryfikacji dwuetapowej](multi-factor-authentication-end-user-manage-settings.md) .

## <a name="next-steps"></a>Następne kroki

- Informacje o zabezpieczeniach w artykule [Przegląd informacji zabezpieczających (wersja zapoznawcza)](./security-info-setup-signin.md) .

- Więcej informacji na temat weryfikacji dwuetapowej w artykule [Przegląd weryfikacji dwuetapowej](./multi-factor-authentication-end-user-first-time.md) .

- Zresetuj hasło, jeśli zostało zgubione lub zapomniane, z poziomu [portalu resetowania haseł](https://passwordreset.microsoftonline.com/)

- Wskazówki i pomoc dotyczące rozwiązywania problemów z logowaniem znajdują się w artykule [Nie można zalogować się na konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).