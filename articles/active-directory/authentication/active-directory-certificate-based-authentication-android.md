---
title: Uwierzytelnianie oparte na certyfikatach systemu Android — usługa Azure Active Directory
description: Dowiedz się więcej o obsługiwanych scenariuszach i wymaganiach dotyczących konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeniami z systemem Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9760624afec111a271ae5aa0ebbe5533d6ba8d6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680208"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Uwierzytelnianie oparte na certyfikatach usługi Azure Active Directory w systemie Android

Urządzenia z systemem Android mogą używać uwierzytelniania opartego na certyfikatach (CBA) do uwierzytelniania w usłudze Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu podczas nawiązywania połączenia z:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzania kombinacji nazwy użytkownika i hasła do niektórych aplikacji poczty i pakietu Microsoft Office na urządzeniu przenośnym.

W tym temacie przedstawiono wymagania i obsługiwane scenariusze konfigurowania analizy AKA na urządzeniu z systemem Android dla użytkowników dzierżawców w planach usługi Office 365 Enterprise, Business, Education, US Government, China i Germany.

Ta funkcja jest dostępna w wersji zapoznawczej w planach obrony i planów federalnych usługi Office 365 dla instytucji rządowych i federalnych.

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

### <a name="implementation-requirements"></a>Wymagania dotyczące wdrażania

Wersja systemu operacyjnego urządzenia musi być Android 5.0 (Lollipop) i powyżej.

Serwer federacyjny musi być skonfigurowany.

Aby usługa Azure Active Directory mogła odwołać certyfikat klienta, token usługi ADFS musi mieć następujące oświadczenia:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Ciąg wystawcy certyfikatu klienta)

Usługa Azure Active Directory dodaje te oświadczenia do tokenu odświeżania, jeśli są one dostępne w tokenie usługi ADFS (lub innym tokenie SAML). Gdy token odświeżania musi zostać zweryfikowany, te informacje są używane do sprawdzania odwołania.

Najlepszym rozwiązaniem jest zaktualizowanie stron błędów usługi ADFS w organizacji o następujące informacje:

* Wymóg instalacji programu Microsoft Authenticator w systemie Android.
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie stron logowania do usług AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Niektóre aplikacje pakietu Office (z włączonym nowoczesnym uwierzytelnianiem) wysyłają w swoim żądaniu '*prompt=login*' do usługi Azure AD. Domyślnie usługa Azure AD tłumaczy '*prompt=login*' w żądaniu do usługi ADFS jako '*wauth=usernamepassworduri*' (prosi ADFS o u/p Auth) i '*wfresh=0*' (prosi ADFS o zignorowanie stanu logowania sygnowanego i zrobienie nowego uwierzytelniania). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, musisz zmodyfikować domyślne zachowanie usługi Azure AD. Ustaw '*PromptLoginBehavior*' w ustawieniach domeny federacyjnej na "*Wyłączone*'.
Do wykonania tego zadania można użyć polecenia cmdlet [MSOLDomainFederationSettings:](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

Niektóre aplikacje Programu Exchange ActiveSync w systemie Android 5.0 (Lollipop) lub nowszym są obsługiwane. Aby ustalić, czy aplikacja poczty e-mail obsługuje tę funkcję, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikatach w swoim środowisku, zobacz [Wprowadzenie do uwierzytelniania opartego na certyfikatach w systemie Android,](active-directory-certificate-based-authentication-get-started.md) aby uzyskać instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
