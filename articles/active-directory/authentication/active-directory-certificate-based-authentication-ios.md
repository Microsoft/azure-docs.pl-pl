---
title: Uwierzytelnianie oparte na certyfikatach w usłudze iOS — usługa Azure Active Directory
description: Dowiedz się więcej o obsługiwanych scenariuszach i wymaganiach dotyczących konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeniami z systemem iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd8a0c3688e5056c7941d334da8caee9f21ba82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407263"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Uwierzytelnianie oparte na certyfikatach usługi Azure Active Directory w usłudze iOS

Urządzenia z systemem iOS mogą używać uwierzytelniania opartego na certyfikatach (CBA) do uwierzytelniania w usłudze Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu podczas nawiązywania połączenia z:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzania kombinacji nazwy użytkownika i hasła do niektórych aplikacji poczty i pakietu Microsoft Office na urządzeniu przenośnym.

W tym temacie przedstawiono wymagania i obsługiwane scenariusze konfigurowania analizy APO na urządzeniu z systemem iOS dla użytkowników dzierżawców w planach usługi Office 365 Enterprise, Business, Education, US Government, China i Germany.

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

## <a name="requirements"></a>Wymagania

Wersja systemu operacyjnego urządzenia musi być iOS 9 lub wyższa

Serwer federacyjny musi być skonfigurowany.

Program Microsoft Authenticator jest wymagany dla aplikacji pakietu Office w systemie iOS.

Aby usługa Azure Active Directory mogła odwołać certyfikat klienta, token usługi ADFS musi mieć następujące oświadczenia:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Ciąg wystawcy certyfikatu klienta)

Usługa Azure Active Directory dodaje te oświadczenia do tokenu odświeżania, jeśli są one dostępne w tokenie usługi ADFS (lub innym tokenie SAML). Gdy token odświeżania musi zostać zweryfikowany, te informacje są używane do sprawdzania odwołania.

Najlepszym rozwiązaniem jest zaktualizowanie stron błędów usługi ADFS w organizacji o następujące informacje:

* Wymóg instalowania programu Microsoft Authenticator w systemie iOS
* Instrukcje dotyczące sposobu uzyskania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie stron logowania do usług AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Niektóre aplikacje pakietu Office (z włączonym nowoczesnym uwierzytelnianiem) wysyłają w swoim żądaniu '*prompt=login*' do usługi Azure AD. Domyślnie usługa Azure AD tłumaczy '*prompt=login*' w żądaniu do usługi ADFS jako '*wauth=usernamepassworduri*' (prosi ADFS o u/p Auth) i '*wfresh=0*' (prosi ADFS o zignorowanie stanu logowania sygnowanego i zrobienie nowego uwierzytelniania). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikatach dla tych aplikacji, musisz zmodyfikować domyślne zachowanie usługi Azure AD. Wystarczy ustawić '*PromptLoginBehavior*' w ustawieniach domeny federacyjnej na "*Wyłączone*".
Do wykonania tego zadania można użyć polecenia cmdlet [MSOLDomainFederationSettings:](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

W systemu iOS 9 lub nowszym obsługiwany jest natywny klient poczty systemu iOS. W przypadku wszystkich innych aplikacji programu Exchange ActiveSync, aby ustalić, czy ta funkcja jest obsługiwana, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikatach w swoim środowisku, zobacz [Wprowadzenie do uwierzytelniania opartego na certyfikatach w systemie Android,](../authentication/active-directory-certificate-based-authentication-get-started.md) aby uzyskać instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
