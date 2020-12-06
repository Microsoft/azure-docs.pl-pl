---
title: Uwierzytelnianie oparte na certyfikacie systemu Android — Azure Active Directory
description: Poznaj obsługiwane scenariusze i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach w rozwiązaniach z urządzeniami z systemem Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd9f59dd75620f3a7b5c9142a4b8f73f75c5ee7b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744486"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory uwierzytelniania opartego na certyfikatach w systemie Android

Urządzenia z systemem Android mogą uwierzytelniać się za pomocą uwierzytelniania opartego na certyfikatach (OBSŁUGIWANIE) w celu Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu podczas nawiązywania połączenia z:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzenia kombinacji nazwy użytkownika i hasła do określonych aplikacji poczty i Microsoft Office na urządzeniu przenośnym.

Ten temat zawiera wymagania i obsługiwane scenariusze dotyczące konfigurowania OBSŁUGIWANIE na urządzeniu z systemem Android dla użytkowników dzierżaw w pakiecie Office 365 Enterprise, Business, Education, USA, Chinach i Niemczech.

Ta funkcja jest dostępna w wersji zapoznawczej w pakiecie Office 365 Stany obrony i plany federalne dla instytucji rządowych USA.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Aplikacje | Pomoc techniczna |
| --- | --- |
| Aplikacja Azure Information Protection |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Intune Portal firmy |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Microsoft Teams |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneNote |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneDrive |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Outlook |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Power BI |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Skype dla firm |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Word/Excel/PowerPoint |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Yammer |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |

### <a name="implementation-requirements"></a>Wymagania dotyczące implementacji

Wersja systemu operacyjnego urządzenia musi mieć system Android 5,0 (lizak) i nowsze.

Należy skonfigurować serwer federacyjny.

Aby Azure Active Directory odwołać certyfikat klienta, token ADFS musi mieć następujące oświadczenia:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Numer seryjny certyfikatu klienta)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Ciąg dla wystawcy certyfikatu klienta)

Azure Active Directory dodaje te oświadczenia do tokenu odświeżania, jeśli są dostępne w tokenie ADFS (lub dowolnym innym tokenie SAML). Gdy token odświeżania musi być zweryfikowany, te informacje są używane do sprawdzania odwołania.

Najlepszym rozwiązaniem jest zaktualizowanie stron błędów organizacji ADFS z następującymi informacjami:

* Wymagania dotyczące instalowania Microsoft Authenticator w systemie Android.
* Instrukcje dotyczące pobierania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie stron logowania AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Niektóre aplikacje pakietu Office (z włączonym współczesnym uwierzytelnianiem) wysyłają jako żądanie "*Prompt = login*" do usługi Azure AD. Domyślnie usługa Azure AD tłumaczy wartość "*Prompt = login*" w żądaniu na usługi AD FS jako "*wauth = usernamepassworduri*" (żąda od usług AD FS uwierzytelniania U/P) i "*wfresh = 0*" (poprosi ADFS o zignorowanie stanu logowania jednokrotnego i przeprowadzenie nowego uwierzytelniania). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikacie dla tych aplikacji, musisz zmodyfikować domyślne zachowanie usługi Azure AD. Ustaw wartość "*PromptLoginBehavior*" w ustawieniach domeny federacyjnej na wartość "*Disabled*".
Aby wykonać to zadanie, można użyć polecenia cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Obsługa klientów programu Exchange ActiveSync

Obsługiwane są niektóre aplikacje Exchange ActiveSync w systemie Android 5,0 (lizak) lub nowszym. Aby określić, czy aplikacja poczty e-mail obsługuje tę funkcję, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować uwierzytelnianie oparte na certyfikacie w środowisku, zobacz Wprowadzenie do [uwierzytelniania opartego na certyfikatach w systemie Android](active-directory-certificate-based-authentication-get-started.md) , aby uzyskać instrukcje.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png