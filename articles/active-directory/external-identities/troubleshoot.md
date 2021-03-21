---
title: Rozwiązywanie problemów ze współpracą B2B — Azure Active Directory | Microsoft Docs
description: Rozwiązywanie typowych problemów związanych z Azure Active Directory współpracy B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100365433"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Rozwiązywanie problemów Azure Active Directory współpracy B2B

Poniżej przedstawiono niektóre zagadnienia dotyczące typowych problemów związanych z usługą współpracy między firmami Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > - **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **Od października 2021** firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Dodaliśmy użytkownika zewnętrznego, ale nie są one widoczne w mojej globalnej książce adresowej lub w selektorze osób

W przypadku, gdy użytkownicy zewnętrzni nie są wypełnini na liście, replikacja może zająć kilka minut.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Użytkownik gościa B2B nie jest wyświetlany w selektorze usługi SharePoint Online/OneDrive osoby

Możliwość wyszukiwania istniejących użytkowników-Gości w selektorze osób usługi SharePoint Online (SPO) jest domyślnie wyłączona, aby dopasować starsze zachowanie.

Tę funkcję można włączyć za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie dzierżawy i kolekcji witryn. Funkcję można ustawić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite, które umożliwiają członkom przeszukiwanie wszystkich istniejących użytkowników-Gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na już inicjowane witryny SPO.

## <a name="invitations-have-been-disabled-for-directory"></a>Zaproszenia zostały wyłączone dla katalogu

Jeśli otrzymasz powiadomienie, że nie masz uprawnień do zapraszania użytkowników, sprawdź, czy konto użytkownika jest autoryzowane do zapraszania użytkowników zewnętrznych w obszarze Azure Active Directory > ustawienia użytkownika > użytkowników zewnętrznych > zarządzania ustawieniami współpracy zewnętrznej:

![Zrzut ekranu przedstawiający ustawienia użytkowników zewnętrznych](media/troubleshoot/external-user-settings.png)

Jeśli ostatnio zmodyfikowano te ustawienia lub przypisano do użytkownika rolę zapraszania gościa, może upłynąć do 15-60 minut, zanim zmiany zaczną obowiązywać.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Użytkownik, który zaprosił, otrzymuje błąd podczas realizacji

Typowe błędy obejmują:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Administrator osoby zaproszonej nie zezwolił na tworzenie EmailVerified użytkowników w swojej dzierżawie

Gdy zapraszasz użytkowników, których organizacja używa Azure Active Directory, ale gdzie konto określonego użytkownika nie istnieje (na przykład użytkownik nie istnieje w usłudze Azure AD contoso.com). Administrator contoso.com może mieć zasady, które uniemożliwiają tworzenie użytkowników. Użytkownik musi skontaktować się z administratorem, aby określić, czy jest dozwolony dostęp użytkowników zewnętrznych. Administrator użytkownika zewnętrznego może wymagać zezwolenia użytkownikom zweryfikowanym pocztą E-mail w swojej domenie (zobacz ten [artykuł](/powershell/module/msonline/set-msolcompanysettings) na temat zezwalania na zweryfikowane wiadomości e-mail).

![Błąd wskazujący, że dzierżawca nie zezwala na zweryfikowane wiadomości e-mail](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Użytkownik zewnętrzny nie istnieje już w domenie federacyjnej

Jeśli używasz uwierzytelniania federacyjnego, a użytkownik nie istnieje jeszcze w Azure Active Directory, nie można zaprosić użytkownika.

Aby rozwiązać ten problem, administrator użytkownika zewnętrznego musi zsynchronizować konto użytkownika, aby Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Jak " \# ", który nie jest zwykle prawidłowym znakiem, jest synchronizowany z usługą Azure AD?

" \# " jest zastrzeżonym znakiem w UPN dla współpracy z usługą Azure AD B2B lub użytkownikami zewnętrznymi, ponieważ zaproszone konto user@contoso.com zostanie user_contoso. com # EXT # @fabrikam.onmicrosoft.com . Z tego względu \# nazwy UPN pochodzące z lokalnego nie mogą zalogować się do Azure Portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Wystąpił błąd podczas dodawania użytkowników zewnętrznych do zsynchronizowanej grupy

Użytkowników zewnętrznych można dodawać tylko do grup "Assigned" lub "Security", a nie do grup, które są w środowisku lokalnym.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mój użytkownik zewnętrzny nie otrzymał wiadomości e-mail do zrealizowania

Zapraszanie powinno sprawdzić swój filtr usługodawcy internetowego lub spamu, aby upewnić się, że dozwolony jest następujący adres: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Zauważ, że wiadomość niestandardowa nie jest dołączona do wiadomości z zaproszeniem w czasie

W celu zapewnienia zgodności z przepisami dotyczącymi ochrony prywatności nasze interfejsy API nie zawierają niestandardowych wiadomości e-mail z zaproszeniem, gdy:

- Zapraszający nie ma adresu e-mail w ramach zapraszanej dzierżawy
- Gdy podmiot zabezpieczeń appService wysyła zaproszenie

Jeśli ten scenariusz jest istotny, możesz pominąć nasz adres e-mail zaproszenia do obsługi interfejsu API i wysłać go za pomocą wybranego przez siebie mechanizmu poczty e-mail. Skontaktuj się z prawnikami w organizacji, aby upewnić się, że wszystkie wysyłane wiadomości e-mail również są zgodne z przepisami dotyczącymi ochrony prywatności.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Podczas próby zalogowania się do zasobu platformy Azure pojawia się błąd "AADSTS65005"

Użytkownik, który ma konto gościa nie może się zalogować i otrzymuje następujący komunikat o błędzie:

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

Użytkownik ma konto użytkownika platformy Azure i jest porzuconym lub niezarządzanym dzierżawcą. Ponadto nie ma żadnych administratorów globalnych w dzierżawie.

Aby rozwiązać ten problem, musisz przejąć pozostałą dzierżawę. Zapoznaj się z artykułem  [przejmowanie niezarządzanego katalogu jako administrator w Azure Active Directory](../enterprise-users/domains-admin-takeover.md). Aby zapewnić bezpośredni dowód, że masz kontrolę nad przestrzenią nazw, należy również uzyskać dostęp do internetowego serwera DNS dla danego sufiksu domeny. Gdy dzierżawa zostanie zwrócona do stanu zarządzanego, zapoznaj się z klientem, czy opuszczenie użytkowników i zweryfikowanej nazwy domeny jest najlepszą opcją dla swojej organizacji.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Użytkownik-Gość z dzierżawcą "just in Time" lub "wirusowym" nie może zresetować hasła

Jeśli dzierżawa tożsamości jest dzierżawą just-in-Time (JIT) lub wirusową (co oznacza, że jest to oddzielna, niezarządzana dzierżawa platformy Azure), tylko użytkownik-Gość może zresetować swoje hasło. Czasami organizacja zajmie się [zarządzaniem wirusami](../enterprise-users/domains-admin-takeover.md) , które są tworzone, gdy pracownicy używają służbowych adresów e-mail do rejestracji w usłudze. Gdy organizacja zajmie się wirusową dzierżawą, tylko administrator w tej organizacji może zresetować hasło użytkownika lub włączyć SSPR. Jeśli to konieczne, jako zapraszana organizacja możesz usunąć konto użytkownika-gościa z katalogu i ponownie wysłać zaproszenie.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Użytkownik-Gość nie może użyć modułu AzureAD PowerShell v1

Od 18 listopada 2019 użytkownicy-Goście w katalogu (zdefiniowani jako konta użytkowników, dla których właściwość **UserType** jest równa **gość**) są blokowani przy użyciu modułu AzureAD PowerShell v1. W przyszłości użytkownik musi być użytkownikiem będącym członkiem (w którym **element** **UserType** jest równy) lub używać modułu AzureAD PowerShell V2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>W dzierżawie platformy Azure USA nie można zaprosić użytkownika-gościa współpracy B2B

W chmurze platformy Azure dla instytucji rządowych USA współpraca B2B jest obecnie obsługiwana tylko między dzierżawcami, które są zarówno w chmurze platformy Azure USA, jak i obsługują współpracę B2B. Jeśli użytkownik zostanie uczestnikiem dzierżawy, który nie jest częścią chmury dla instytucji rządowych usługi Azure USA lub nie obsługuje jeszcze współpracy B2B, zostanie wyświetlony komunikat o błędzie. Aby uzyskać więcej informacji i ograniczeń, zobacz [Azure Active Directory — wersja Premium odmian P1 i P2](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Otrzymuję komunikat o błędzie z informacją, że usługa Azure AD nie może znaleźć aplikacji AAD-Extensions-App w mojej dzierżawie

W przypadku korzystania z funkcji samoobsługowego tworzenia nowego konta, takich jak niestandardowe atrybuty użytkownika lub przepływy użytkownika, wywoływana `aad-extensions-app. Do not modify. Used by AAD for storing user data.` jest automatycznie utworzona aplikacja. Są one używane przez tożsamości zewnętrzne usługi Azure AD do przechowywania informacji o użytkownikach, którzy rejestrują i pobierają atrybuty niestandardowe.

Jeśli przypadkowo usuniesz aplikację `aad-extensions-app`, masz 30 dni na jej odzyskanie. Aplikację można przywrócić przy użyciu modułu Azure AD PowerShell.

1. Uruchom moduł Azure AD PowerShell i uruchom program `Connect-AzureAD` .
1. Zaloguj się jako Administrator globalny dzierżawy usługi Azure AD, dla którego chcesz odzyskać usuniętą aplikację.
1. Uruchom polecenie programu PowerShell `Get-AzureADDeletedApplication` .
1. Znajdź aplikację na liście, w której rozpoczyna się nazwa wyświetlana `aad-extensions-app` , i skopiuj jej `ObjectId` wartość właściwości.
1. Uruchom polecenie programu PowerShell `Restore-AzureADDeletedApplication -ObjectId {id}` . Zamień `{id}` część polecenia na `ObjectId` z poprzedniego kroku.

Przywrócona aplikacja powinna teraz zostać wyświetlona w Azure Portal.

## <a name="next-steps"></a>Następne kroki

[Uzyskaj pomoc techniczną dotyczącą współpracy B2B](../fundamentals/active-directory-troubleshooting-support-howto.md)
