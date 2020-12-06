---
title: Weryfikacja dwuetapowa usługi Azure AD MFA i usług ADFS — Azure Active Directory
description: Jest to strona Multi-Factor Authentication usługi Azure AD, która opisuje, jak rozpocząć pracę z usługą Azure AD MFA i AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741409"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Wprowadzenie do usługi Azure AD Multi-Factor Authentication i Active Directory Federation Services

<center>

![Uruchamianie usługi Azure AD MFA i usług ADFS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Jeśli Twoja organizacja ma federacyjne Active Directory lokalnego z Azure Active Directory przy użyciu AD FS, dostępne są dwie opcje korzystania z usługi Azure AD Multi-Factor Authentication.

* Zabezpieczanie zasobów w chmurze za pomocą usługi Azure AD Multi-Factor Authentication lub Active Directory Federation Services
* Zabezpieczenie zasobów w chmurze i zasobów lokalnych przy użyciu serwera usługi Azure Multi-Factor Authentication

Poniższa tabela zawiera podsumowanie środowiska weryfikacji między zabezpieczaniem zasobów przy użyciu usługi Azure AD Multi-Factor Authentication i AD FS

| Proces weryfikacji — aplikacje korzystające z przeglądarki | Proces weryfikacji — aplikacje niekorzystające z przeglądarki |
|:--- |:--- |
| Zabezpieczanie zasobów usługi Azure AD za pomocą usługi Azure AD Multi-Factor Authentication |<li>Pierwszy etap weryfikacji odbywa się lokalnie przy użyciu usług AD FS.</li> <li>Drugi etap polega na użyciu metody telefonicznej, która obejmuje uwierzytelnianie w chmurze.</li> |
| Zabezpieczanie zasobów usługi Azure AD za pomocą usług Active Directory Federation Services |<li>Pierwszy etap weryfikacji odbywa się lokalnie przy użyciu usług AD FS.</li><li>Drugi etap odbywa się lokalnie i polega na uznaniu oświadczenia.</li> |

Zastrzeżenia dotyczące haseł aplikacji używanych przez użytkowników federacyjnych:

* Hasła aplikacji są weryfikowane przy użyciu uwierzytelniania w chmurze, co pozwala pominąć federację. Federacja jest aktywnie używana tylko podczas konfigurowania hasła aplikacji.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasła aplikacji.
* Nie będzie można lokalnie rejestrować uwierzytelniania haseł aplikacji.
* Wyłączenie/usunięcie konta przy użyciu narzędzia do synchronizacji katalogów może potrwać do 3 godzin, powodując opóźnienie wyłączenia/usunięcia haseł aplikacji w tożsamości w chmurze.

Aby uzyskać informacje na temat konfigurowania usługi Azure AD Multi-Factor Authentication lub Serwer Multi-Factor Authentication platformy Azure z AD FS, zobacz następujące artykuły:

* [Zabezpieczanie zasobów w chmurze za pomocą usługi Azure AD Multi-Factor Authentication i AD FS](howto-mfa-adfs.md)
* [Zabezpieczanie zasobów w chmurze i lokalnych przy użyciu serwera usługi Azure Multi-Factor Authentication i usług AD FS systemu Windows Server 2012 R2](howto-mfaserver-adfs-2012.md)
* [Zabezpieczanie zasobów w chmurze i zasobów lokalnych przy użyciu serwera usługi Azure Multi-Factor Authentication i usług AD FS 2.0](howto-mfaserver-adfs-2.md)
