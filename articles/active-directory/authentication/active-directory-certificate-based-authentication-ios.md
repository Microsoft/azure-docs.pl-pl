---
title: Uwierzytelnianie oparte na certyfikatach w usłudze iOS — usługa Azure Active Directory
description: Dowiedz się więcej o obsługiwanych scenariuszach i wymaganiach dotyczących konfigurowania uwierzytelniania opartego na certyfikatach dla usługi Azure Active Directory w rozwiązaniach z urządzeniami z systemem iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639626"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Uwierzytelnianie oparte na certyfikatach usługi Azure Active Directory w usłudze iOS

Aby zwiększyć bezpieczeństwo, urządzenia z systemem iOS mogą używać uwierzytelniania opartego na certyfikatach (CBA) do uwierzytelniania w usłudze Azure Active Directory (Azure AD) przy użyciu certyfikatu klienta na urządzeniu podczas łączenia się z następującymi aplikacjami lub usługami:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Używanie certyfikatów eliminuje konieczność wprowadzania kombinacji nazwy użytkownika i hasła do niektórych aplikacji poczty i pakietu Microsoft Office na urządzeniu przenośnym.

W tym artykule opisano wymagania i obsługiwane scenariusze konfigurowania analizy analizy apo na urządzeniu z systemem iOS. Usługa CBA dla systemu iOS jest dostępna w chmurach publicznych platformy Azure, w chmurze microsoft government cloud, w usłudze Microsoft Cloud Germany i w witrynie Microsoft Azure China 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Aplikacja Azure Information Protection |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Intune Portal firmy |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Microsoft Teams |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| OneNote |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| OneDrive |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Outlook |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Power BI |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Skype dla firm |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Słowo / Excel / PowerPoint |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |
| Yammer |![Sprawdź znak oznaczający wsparcie dla tej aplikacji][1] |

## <a name="requirements"></a>Wymagania

Aby używać analizy analizy kosztów ios w systemach iOS, obowiązują następujące wymagania i zagadnienia:

* Wersja systemu operacyjnego urządzenia musi być iOS 9 lub wyższa.
* Program Microsoft Authenticator jest wymagany dla aplikacji pakietu Office w systemie iOS.
* W pęku kluczy systemu macOS należy utworzyć preferencję tożsamości zawierającą adres URL uwierzytelniania serwera ADFS. Aby uzyskać więcej informacji, zobacz [Tworzenie preferencji tożsamości w programie Keychain Access na komputerze Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Obowiązują następujące wymagania i zagadnienia dotyczące usług federacyjnych Active Directory (ADFS):

* Serwer usługi ADFS musi być włączony do uwierzytelniania certyfikatów i używać uwierzytelniania federacyjnego.
* Certyfikat musi być używany do użycia klucza rozszerzonego (EKU) i zawierać nazwę UPN użytkownika w *alternatywnej nazwie podmiotu (nazwa główna NT).*

## <a name="configure-adfs"></a>Konfigurowanie usługi ADFS

Aby usługa Azure AD odwołała certyfikat klienta, token usługi ADFS musi mieć następujące oświadczenia. Usługa Azure AD dodaje te oświadczenia do tokenu odświeżania, jeśli są one dostępne w tokenie usługi ADFS (lub innym tokenie SAML). Gdy token odświeżania musi zostać zweryfikowany, te informacje są używane do sprawdzania odwołania:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- dodaj numer seryjny certyfikatu klienta
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- dodaj ciąg dla wystawcy certyfikatu klienta

Najlepszym rozwiązaniem jest również zaktualizowanie stron błędów usługi ADFS w organizacji o następujące informacje:

* Wymaganie instalacji programu Microsoft Authenticator w systemie iOS.
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie strony logowania usług AD FS](https://technet.microsoft.com/library/dn280950.aspx).

## <a name="use-modern-authentication-with-office-apps"></a>Korzystanie z nowoczesnego uwierzytelniania w aplikacjach pakietu Office

Niektóre aplikacje pakietu Office `prompt=login` z nowoczesnym uwierzytelnianiem włączone wysyłać do usługi Azure AD w ich żądaniu. Domyślnie usługa Azure `prompt=login` AD tłumaczy w żądaniu do usługi ADFS jako `wauth=usernamepassworduri` (prosi `wfresh=0` ADFS zrobić U/P Auth) i (prosi ADFS zignorować stan SSO i zrobić nowe uwierzytelnianie). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, zmodyfikuj domyślne zachowanie usługi Azure AD.

Aby zaktualizować zachowanie domyślne, ustaw na wyłączone zachowanie *' PromptLoginBehavior*' w ustawieniach domeny federacyjnej. *Disabled* Do wykonania tego zadania można użyć polecenia cmdlet [MSOLDomainFederationSettings,](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) jak pokazano w poniższym przykładzie:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Obsługa klientów programu Exchange ActiveSync

W systemu iOS 9 lub nowszym obsługiwany jest natywny klient poczty systemu iOS. Aby ustalić, czy ta funkcja jest obsługiwana dla wszystkich innych aplikacji Programu Exchange ActiveSync, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować uwierzytelnianie oparte na certyfikatach w swoim środowisku, zobacz [Wprowadzenie do uwierzytelniania opartego](active-directory-certificate-based-authentication-get-started.md) na certyfikatach, aby uzyskać instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
