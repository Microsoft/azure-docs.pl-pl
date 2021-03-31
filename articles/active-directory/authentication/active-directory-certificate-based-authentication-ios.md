---
title: Uwierzytelnianie oparte na certyfikatach w systemie iOS — Azure Active Directory
description: Poznaj obsługiwane scenariusze i wymagania dotyczące konfigurowania uwierzytelniania opartego na certyfikatach dla Azure Active Directory w rozwiązaniach z urządzeniami z systemem iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861327"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory uwierzytelniania opartego na certyfikatach w systemie iOS

Aby zwiększyć bezpieczeństwo, urządzenia z systemem iOS mogą uwierzytelniać się w usłudze Azure Active Directory (Azure AD) przy użyciu uwierzytelniania opartego na certyfikatach (OBSŁUGIWANIE), korzystając z certyfikatu klienta na urządzeniu podczas nawiązywania połączenia z następującymi aplikacjami lub usługami:

* Aplikacje mobilne pakietu Office, takie jak Microsoft Outlook i Microsoft Word
* Klienci programu Exchange ActiveSync (EAS)

Korzystanie z certyfikatów eliminuje konieczność wprowadzenia kombinacji nazwy użytkownika i hasła do określonych aplikacji poczty i Microsoft Office na urządzeniu przenośnym.

W tym artykule szczegółowo opisano wymagania i obsługiwane scenariusze dotyczące konfigurowania OBSŁUGIWANIE na urządzeniu z systemem iOS. OBSŁUGIWANIE dla systemu iOS jest dostępny w chmurach publicznych platformy Azure, w chmurze Microsoft dla instytucji rządowych, Microsoft Cloud Niemcy i Microsoft Azure Chinach 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Obsługa aplikacji mobilnych firmy Microsoft

| Apps | Pomoc techniczna |
| --- | --- |
| Aplikacja Azure Information Protection |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Intune Portal firmy |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Microsoft Teams |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Office (urządzenia przenośne) |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneNote |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| OneDrive |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Outlook |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Power BI |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Skype dla firm |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Word/Excel/PowerPoint |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |
| Yammer |![Znacznik wyboru oznaczający, że pomoc techniczna dla tej aplikacji][1] |

## <a name="requirements"></a>Wymagania

Aby używać OBSŁUGIWANIE z systemem iOS, obowiązują następujące wymagania i zagadnienia:

* Wersja systemu operacyjnego urządzenia musi działać pod kontrolą systemu iOS 9 lub nowszego.
* W przypadku aplikacji pakietu Office w systemie iOS jest wymagane Microsoft Authenticator.
* W pęku kluczy macOS należy utworzyć preferencję tożsamości, która zawiera adres URL uwierzytelniania serwera usług AD FS. Aby uzyskać więcej informacji, zobacz [Tworzenie preferencji tożsamości w dostępie łańcucha kluczy na komputerze Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Następujące zagadnienia dotyczące Active Directory Federation Services (ADFS) mają zastosowanie:

* Serwer usług AD FS musi mieć włączoną funkcję uwierzytelniania certyfikatu i używać uwierzytelniania federacyjnego.
* Certyfikat musi być musiał używać rozszerzonego użycia klucza (EKU) i zawierać nazwę UPN użytkownika w *alternatywnej nazwie podmiotu (nazwa główna NT)*.

## <a name="configure-adfs"></a>Konfigurowanie usług AD FS

Aby usługa Azure AD mogła odwołać certyfikat klienta, token ADFS musi mieć następujące oświadczenia. Usługa Azure AD dodaje te oświadczenia do tokenu odświeżania, jeśli są one dostępne w tokenie ADFS (lub dowolnym innym tokenie SAML). Gdy token odświeżania musi być zweryfikowany, te informacje są używane do sprawdzania odwołania:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -Dodaj numer seryjny certyfikatu klienta
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -Dodaj ciąg dla wystawcy certyfikatu klienta

Najlepszym rozwiązaniem jest również aktualizowanie stron błędów usługi ADFS w organizacji przy użyciu następujących informacji:

* Wymagania dotyczące instalowania Microsoft Authenticator w systemie iOS.
* Instrukcje dotyczące pobierania certyfikatu użytkownika.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie strony logowania AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Używanie nowoczesnego uwierzytelniania z aplikacjami pakietu Office

Niektóre aplikacje pakietu Office z nowoczesnym uwierzytelnianiem umożliwiały wysyłanie `prompt=login` do usługi Azure AD w ich żądaniu. Domyślnie usługa Azure AD tłumaczy `prompt=login` żądanie na usługi AD FS jako (pyta, czy uwierzytelnianie `wauth=usernamepassworduri` U/P zostanie wykonane, a także `wfresh=0` poprosi ADFS o zignorowanie stanu logowania jednokrotnego i przeprowadzenie nowego uwierzytelniania). Jeśli chcesz włączyć uwierzytelnianie oparte na certyfikacie dla tych aplikacji, zmodyfikuj domyślne zachowanie usługi Azure AD.

Aby zaktualizować zachowanie domyślne, ustaw wartość "*PromptLoginBehavior*" w ustawieniach domeny federacyjnej na *wyłączone*. Aby wykonać to zadanie, można użyć polecenia cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) , jak pokazano w następującym przykładzie:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Obsługa klientów programu Exchange ActiveSync

W systemie iOS 9 lub nowszym jest obsługiwany natywny klient poczty systemu iOS. Aby określić, czy ta funkcja jest obsługiwana dla wszystkich innych aplikacji programu Exchange ActiveSync, skontaktuj się z deweloperem aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku, zobacz Wprowadzenie do [uwierzytelniania opartego na certyfikatach](active-directory-certificate-based-authentication-get-started.md) w celu uzyskania instrukcji.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
