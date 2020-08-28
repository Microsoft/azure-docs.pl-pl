---
title: Uwierzytelnianie pulpitu wirtualnego systemu Windows — Azure
description: Metody uwierzytelniania dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038669"
---
# <a name="supported-authentication-methods"></a>Obsługiwane metody uwierzytelniania

W tym artykule przedstawiono krótkie omówienie rodzajów uwierzytelniania, których można użyć na pulpicie wirtualnym systemu Windows.

## <a name="session-host-authentication"></a>Uwierzytelnianie hosta sesji

Pulpit wirtualny systemu Windows obsługuje uwierzytelnianie hosta sesji zarówno NT LAN Manager (NTLM), jak i protokołu Kerberos. Aby jednak korzystać z protokołu Kerberos, klient musi uzyskać bilety zabezpieczeń Kerberos z usługi centrum dystrybucji kluczy (KDC) działającej na kontrolerze domeny. Aby uzyskać bilety, klient potrzebuje bezpośredniej linii wglądu do kontrolera domeny. Możesz uzyskać bezpośredni wgląd w szczegółowe informacje przy użyciu sieci firmowej. Możesz również użyć połączenia sieci VPN z siecią firmową.

Są to obecnie obsługiwane metody logowania:

- Klient klasyczny systemu Windows
    - Nazwa użytkownika i hasło
    - Kart
    - Windows Hello
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
>Karty inteligentne i Windows Hello mogą korzystać tylko z protokołu Kerberos do logowania. Zalogowanie się przy użyciu protokołu Kerberos wymaga linii wglądu do kontrolera domeny.

## <a name="single-sign-on-sso"></a>Logowanie jednokrotne (SSO)

Pulpit wirtualny systemu Windows obecnie nie obsługuje Active Directory Federation Services (ADFS) do uwierzytelniania i logowania jednokrotnego.

Jedynym sposobem, aby uniknąć wyświetlania monitu o podanie poświadczeń dla hosta sesji jest zapisanie ich na kliencie. Zalecamy wykonanie tej czynności tylko z bezpiecznymi urządzeniami, aby uniemożliwić innym użytkownikom dostęp do zasobów.

## <a name="next-steps"></a>Kolejne kroki

Chcesz wiedzieć o innych sposobach zapewnienia bezpieczeństwa wdrożenia? Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń](security-guide.md).
