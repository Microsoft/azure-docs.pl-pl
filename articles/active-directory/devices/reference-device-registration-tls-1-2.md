---
title: Wymuszanie protokołu TLS 1,2 — usługa rejestracji Azure Active Directory
description: Usuwanie obsługi protokołów TLS 1,0 i 1,1 dla usługi rejestracji urządzeń w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268761"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Wymuś protokół TLS 1,2 dla usługi rejestracji w usłudze Azure AD

Usługa rejestracji urządzeń w usłudze Azure Active Directory (Azure AD) służy do łączenia urządzeń z chmurą przy użyciu tożsamości urządzenia. Usługa rejestracji urządzeń usługi Azure AD obecnie obsługuje używanie Transport Layer Security (TLS) 1,2 do komunikacji z platformą Azure. Aby zapewnić bezpieczeństwo i optymalne w swojej klasie szyfrowanie, firma Microsoft zaleca wyłączenie protokołów TLS 1,0 i 1,1. Ten dokument zawiera informacje o tym, jak upewnić się, że maszyny używane do ukończenia rejestracji i komunikowania się z usługą rejestracji urządzeń w usłudze Azure AD używają protokołu TLS 1,2.

Protokół TLS w wersji 1,2 to protokół kryptograficzny zaprojektowany w celu zapewnienia bezpiecznej komunikacji. Protokół TLS ma głównie zapewnić prywatność i integralność danych. Protokół TLS został przetworzony przez wiele iteracji, w których w wersji 1,2 jest zdefiniowana w [dokumencie RFC 5246 (Link zewnętrzny)](https://tools.ietf.org/html/rfc5246).

Bieżąca analiza połączeń pokazuje małe użycie protokołu TLS 1,1 i 1,0, ale udostępniamy te informacje, dzięki czemu można zaktualizować odpowiednich klientów lub serwery stosownie do potrzeb przed wsparciem protokołu TLS 1,1 i 1,0. W przypadku korzystania z infrastruktury lokalnej dla scenariuszy hybrydowych lub Active Directory Federation Services (AD FS) Upewnij się, że infrastruktura może obsługiwać połączenia przychodzące i wychodzące korzystające z protokołu TLS 1,2.

## <a name="update-windows-servers"></a>Aktualizowanie serwerów z systemem Windows

W przypadku serwerów z systemem Windows, które korzystają z usługi rejestracji urządzeń usługi Azure AD lub działają jako serwery proxy, wykonaj następujące kroki, aby zapewnić włączenie protokołu TLS 1,2:

> [!IMPORTANT]
> Po zaktualizowaniu rejestru należy ponownie uruchomić system Windows Server, aby zmiany zaczęły obowiązywać.

### <a name="enable-tls-12"></a>Włączenie protokołu TLS 1.2

Upewnij się, że następujące ciągi rejestru zostały skonfigurowane w sposób pokazany:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ klient
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ serwer
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "Schusestrongcrypto we" = DWORD: 00000001

## <a name="update-non-windows-proxies"></a>Aktualizowanie serwerów proxy spoza systemu Windows

Wszystkie maszyny, które działają jako serwery proxy między urządzeniami a usługą rejestracji urządzeń w usłudze Azure AD, muszą mieć pewność, że włączono protokół TLS 1,2. Postępuj zgodnie ze wskazówkami dostawcy, aby zapewnić pomoc techniczną.

## <a name="update-ad-fs-servers"></a>Aktualizowanie serwerów AD FS

Wszystkie serwery AD FS używane do komunikacji z usługą rejestracji urządzeń usługi Azure AD muszą mieć pewność, że protokół TLS 1,2 jest włączony. Aby uzyskać informacje na temat włączania/weryfikowania tej konfiguracji [, zobacz Zarządzanie protokołami SSL/TLS i mechanizmami szyfrowania dla AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) .

## <a name="client-updates"></a>Aktualizacje klienta

Ponieważ wszystkie kombinacje klient-serwer i przeglądarka-serwer muszą używać protokołu TLS 1,2 do nawiązywania połączenia z usługą rejestracji urządzeń usługi Azure AD, może być konieczne zaktualizowanie tych urządzeń.

Następujący klienci nie mogą obsługiwać protokołu TLS 1,2. Zaktualizuj klientów, aby zapewnić nieprzerwany dostęp.

- Android, wersja 4,3 i starsze
- Firefox w wersji 5,0 i starszej
- Internet Explorer w wersji 8-10 w systemie Windows 7 i starszych
- Internet Explorer 10 w Windows Phone 8,0
- Przeglądarka Safari w wersji 6.0.4 w systemie OS X 10.8.4 i starszych

## <a name="next-steps"></a>Następne kroki

[Omówienie protokołu TLS/SSL (Dostawca SSP Schannel)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)