---
title: Serwer usługi Azure MFA i sieci VPN innych firm — Azure Active Directory
description: Przewodniki konfiguracyjne krok po kroku dla serwera usługi Azure MFA do integracji z usługami Cisco, Citrix i Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 116446c2ca6512ac39b907e95a2c1ccdf70befda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949290"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Zaawansowane scenariusze z serwerem usługi Azure MFA i rozwiązaniami sieci VPN innych firm

Usługa Azure Serwer Multi-Factor Authentication (serwer usługi Azure MFA) umożliwia bezproblemowe łączenie się z różnymi rozwiązaniami sieci VPN innych firm. Ten artykuł koncentruje się na &reg; urządzeniu sieci VPN Cisco ASA, urządzeniu sieci VPN protokołu SSL Citrix i sieci Juniper Networks bezpieczny dostęp/puls Secure Connect Secure SSL. Utworzyliśmy przewodniki konfiguracyjne, aby rozwiązać te trzy typowe urządzenia. Serwer usługi Azure MFA można również zintegrować z większością innych systemów, które używają usługi RADIUS, LDAP, IIS lub uwierzytelniania opartego na oświadczeniach do AD FS. Więcej szczegółów można znaleźć w [konfiguracjach serwera usługi Azure MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera MFA dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego (MFA) podczas zdarzeń logowania, powinni używać opartych na chmurze Multi-Factor Authentication platformy Azure.
>
> Aby rozpocząć pracę z usługą MFA opartą na chmurze, zobacz [Samouczek: Zabezpieczanie zdarzeń logowania użytkowników przy użyciu usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Jeśli używasz usługi MFA opartej na chmurze, zobacz [integrowanie infrastruktury sieci VPN z usługą Azure MFA](howto-mfa-nps-extension-vpn.md).
>
> Istniejący klienci, którzy aktywowali serwer usługi MFA przed 1 lipca 2019, mogą pobrać najnowszą wersję, przyszłe aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Urządzenie sieci VPN Cisco ASA i serwer usługi Azure MFA
Serwer usługi Azure MFA integruje się z &reg; urządzeniem sieci VPN Cisco ASA w celu zapewnienia dodatkowych zabezpieczeń na potrzeby &reg; logowania do sieci VPN Cisco AnyConnect i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z poniższych opcji, aby pobrać szczegółowe przewodniki dotyczące konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Cisco ASA z AnyConnect VPN i konfiguracją usługi Azure MFA dla protokołu LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integruj urządzenie sieci VPN Cisco ASA z usługą Azure MFA przy użyciu protokołu LDAP |
| [Cisco ASA z AnyConnect VPN i konfiguracją usługi Azure MFA dla usługi RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrowanie urządzenia sieci VPN Cisco ASA z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Serwer sieci VPN protokołu SSL Citrix i Azure MFA
Serwer usługi Azure MFA integruje się z urządzeniem sieci VPN protokołu SSL firmy Citrix, aby zapewnić dodatkowe zabezpieczenia dla logowań sieci VPN protokołu SSL Citrix i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z poniższych opcji, aby pobrać szczegółowe przewodniki dotyczące konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Konfiguracja sieci VPN SSL Citrix i Azure MFA dla LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrowanie sieci VPN z protokołem SSL firmy Citrix przy użyciu urządzenia MFA platformy Azure |
| [Konfiguracja sieci VPN SSL Citrix i Azure MFA dla RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrowanie urządzenia sieci VPN z protokołem SSL programu Citrix, za pomocą usługi Azure MFA |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL i serwer usługi Azure MFA
Serwer usługi Azure MFA integruje się z urządzeniem sieci VPN Secure SSL Juniper/pulsem w celu zapewnienia dodatkowych zabezpieczeń dla Juniper/pulsu bezpiecznych logowań sieci VPN i dostępu do portalu.  Można użyć protokołu LDAP lub RADIUS.  Wybierz jedną z poniższych opcji, aby pobrać szczegółowe przewodniki dotyczące konfiguracji krok po kroku.

| Przewodnik konfiguracji | Opis |
| --- | --- |
| [Juniper/Pulse Secure SSL sieci VPN i konfiguracja usługi Azure MFA dla LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integruj swoją Juniper/puls Secure SSL VPN z urządzeniem Azure MFA przy użyciu protokołu LDAP |
| [Juniper/Pulse Secure SSL sieci VPN i konfiguracja usługi Azure MFA dla usługi RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integruj swoje urządzenie sieci VPN Secure SSL Juniper/puls z usługą Azure MFA przy użyciu usługi RADIUS |

## <a name="next-steps"></a>Następne kroki

- [Rozszerzanie istniejącej infrastruktury uwierzytelniania przy użyciu rozszerzenia serwera NPS dla platformy Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Konfigurowanie ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
