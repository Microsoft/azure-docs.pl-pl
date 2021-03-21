---
title: Uwierzytelnianie pulpitu wirtualnego systemu Windows — Azure
description: Metody uwierzytelniania dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04a4366bfee6b1d9c5f52d649910163269962684
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709261"
---
# <a name="supported-authentication-methods"></a>Obsługiwane metody uwierzytelniania

W tym artykule przedstawiono krótkie omówienie rodzajów uwierzytelniania, których można użyć na pulpicie wirtualnym systemu Windows.

## <a name="session-host-authentication"></a>Uwierzytelnianie hosta sesji

Pulpit wirtualny systemu Windows obsługuje uwierzytelnianie hosta sesji zarówno NT LAN Manager (NTLM), jak i protokołu Kerberos. Aby jednak korzystać z protokołu Kerberos, klient musi uzyskać bilety zabezpieczeń Kerberos z usługi centrum dystrybucji kluczy (KDC) działającej na kontrolerze domeny. Aby uzyskać bilety, klient potrzebuje bezpośredniej linii wglądu do kontrolera domeny. Możesz uzyskać bezpośredni wgląd w szczegółowe informacje przy użyciu sieci firmowej. Można również użyć połączenia sieci VPN z siecią firmową lub skonfigurować [serwer proxy centrum dystrybucji kluczy](key-distribution-center-proxy.md).

Są to obecnie obsługiwane metody logowania:

- Klient klasyczny systemu Windows
    - Nazwa użytkownika i hasło
    - Kart
    - Windows Hello dla firm (tylko zaufanie do certyfikatów)
- Klient sklepu Windows
    - Nazwa użytkownika i hasło
- Klient internetowy
    - Nazwa użytkownika i hasło
- Android
    - Nazwa użytkownika i hasło
- iOS
    - Nazwa użytkownika i hasło
- macOS
    - Nazwa użytkownika i hasło

>[!NOTE]
>Karty inteligentne i Windows Hello dla firm mogą korzystać tylko z protokołu Kerberos do logowania. Zalogowanie się przy użyciu protokołu Kerberos wymaga linii wglądu do kontrolera domeny lub [serwera proxy centrum dystrybucji kluczy](key-distribution-center-proxy.md).

## <a name="hybrid-identity"></a>Tożsamość hybrydowa

Pulpit wirtualny systemu Windows obsługuje [tożsamości hybrydowe](../active-directory/hybrid/whatis-hybrid-identity.md) za pośrednictwem Azure Active Directory (AD), w tym federacyjnych przy użyciu Active Directory Federation Services (AD FS). Ponieważ użytkownicy muszą mieć możliwość odnajdywania za pomocą usługi Azure AD, pulpit wirtualny systemu Windows nie obsługuje wdrożeń autonomicznych Active Directory z usługami ADFS.

## <a name="single-sign-on-sso"></a>Logowanie jednokrotne

Pulpit wirtualny systemu Windows obecnie nie obsługuje Active Directory Federation Services (ADFS) na potrzeby logowania jednokrotnego.

Jedynym sposobem, aby uniknąć wyświetlania monitu o podanie poświadczeń dla hosta sesji jest zapisanie ich na kliencie. Zalecamy wykonanie tej czynności tylko z bezpiecznymi urządzeniami, aby uniemożliwić innym użytkownikom dostęp do zasobów.

## <a name="next-steps"></a>Następne kroki

Chcesz wiedzieć o innych sposobach zapewnienia bezpieczeństwa wdrożenia? Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń](security-guide.md).
