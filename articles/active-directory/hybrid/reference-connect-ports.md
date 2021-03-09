---
title: Porty i protokoły wymagane przez tożsamość hybrydową — platforma Azure | Microsoft Docs
description: Ta strona jest stroną dokumentacji technicznej dla portów, które są wymagane do otwarcia dla Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520034"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Wymagane porty i protokoły dotyczące tożsamości hybrydowej
Poniższy dokument to dokumentacja techniczna dotycząca wymaganych portów i protokołów w celu wdrożenia hybrydowego rozwiązania do tworzenia tożsamości. Skorzystaj z poniższej ilustracji i zapoznaj się z odpowiednią tabelą.

![Co to jest program Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 — Azure AD Connect i lokalna usługi AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem Azure AD Connect i lokalną usługą AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Wyszukiwania DNS w lesie docelowym. |
| Kerberos |88 (TCP/UDP) |Uwierzytelnianie Kerberos w lesie usługi AD. |
| MS-RPC |135 (TCP) |Używane podczas początkowej konfiguracji Kreatora Azure AD Connect, gdy jest on powiązany z lasem usługi AD, a także podczas synchronizacji haseł. |
| LDAP |389 (TCP/UDP) |Służy do importowania danych z usługi AD. Dane są szyfrowane przy użyciu podpisywania protokołu Kerberos & zapieczętować. |
| SMB | 445 (TCP) |Używane przez bezproblemowe logowanie jednokrotne w celu utworzenia konta komputera w lesie usługi AD. |
| LDAP/SSL |636 (TCP/UDP) |Służy do importowania danych z usługi AD. Transfer danych jest podpisany i szyfrowany. Używane tylko w przypadku korzystania z protokołu TLS. |
| Zdalne wywołanie procedury |49152 – 65535 (losowo wysoki port RPC) (TCP) |Używane podczas początkowej konfiguracji Azure AD Connect, gdy jest ona powiązana z lasami usług AD i podczas synchronizacji haseł. Jeśli port dynamiczny został zmieniony, należy otworzyć ten port. Aby uzyskać więcej informacji, zobacz [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)i [KB224196](https://support.microsoft.com/kb/224196) . |
|WinRM  | 5985 (TCP) |Używane tylko w przypadku instalowania AD FS przy użyciu kreatora gMSA przez Azure AD Connect|
|AD DS usług sieci Web | 9389 (TCP) |Używane tylko w przypadku instalowania AD FS przy użyciu kreatora gMSA przez Azure AD Connect |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 — Azure AD Connect i Azure AD
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem Azure AD Connect i usługą Azure AD.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP) |Służy do pobierania list CRL (list odwołania certyfikatów) do weryfikowania certyfikatów TLS/SSL. |
| HTTPS |443 (TCP) |Służy do synchronizacji z usługą Azure AD. |

Aby uzyskać listę adresów URL i adresów IP, które należy otworzyć w zaporze, zobacz [adresy URL i zakresy adresów IP pakietu Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) i [Rozwiązywanie problemów z Azure AD Connect łącznością](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabela 3 — Azure AD Connect i AD FS serwerów federacyjnych/WAP
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerem Azure AD Connect i AD FS serwery federacyjne/WAP.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTP |80 (TCP) |Służy do pobierania list CRL (list odwołania certyfikatów) do weryfikowania certyfikatów TLS/SSL. |
| HTTPS |443 (TCP) |Służy do synchronizacji z usługą Azure AD. |
| WinRM |5985 |Odbiornik usługi WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 — WAP i serwery federacyjne
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między serwerami federacyjnymi a serwerami WAP.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443 (TCP) |Używany do uwierzytelniania. |

## <a name="table-5---wap-and-users"></a>Tabela 5 — WAP i użytkownicy
W tej tabeli opisano porty i protokoły, które są wymagane do komunikacji między użytkownikami i serwerami WAP.

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443 (TCP) |Używany do uwierzytelniania urządzeń. |
| TCP |49443 (TCP) |Używany do uwierzytelniania certyfikatów. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabela 6a & i 6B — uwierzytelnianie przekazywane przy użyciu logowania jednokrotnego (SSO) i synchronizacji skrótów haseł z logowaniem jednokrotnym (SSO)
W poniższych tabelach opisano porty i protokoły, które są wymagane do komunikacji między Azure AD Connect i usługą Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabela 6a — uwierzytelnianie przekazywane za pomocą logowania jednokrotnego
|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTP|80|Włącz wychodzący ruch HTTP na potrzeby weryfikacji zabezpieczeń, takich jak SSL. Konieczne jest również, aby funkcja autoaktualizacji łącznika działała prawidłowo.
|HTTPS|443| Włącz wychodzący ruch HTTPS dla operacji, takich jak Włączanie i wyłączanie funkcji, rejestrowanie łączników, pobieranie aktualizacji łącznika i obsługa wszystkich żądań logowania użytkownika.

Ponadto Azure AD Connect musi mieć możliwość nawiązywania bezpośrednich połączeń IP z [zakresami adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabela 6B — synchronizacja skrótów haseł z logowaniem jednokrotnym

|Protokół|Numer portu|Opis
| --- | --- | ---
|HTTPS|443| Włącz rejestrację SSO (wymagane tylko dla procesu rejestracji SSO).

Ponadto Azure AD Connect musi mieć możliwość nawiązywania bezpośrednich połączeń IP z [zakresami adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Jest to wymagane tylko w przypadku procesu rejestracji rejestracji jednokrotnej.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a & 7b — Azure AD Connect Health agenta dla (AD FS/Sync) i usługi Azure AD
W poniższych tabelach opisano punkty końcowe, porty i protokoły, które są wymagane do komunikacji między agentami Azure AD Connect Health i usługą Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 7a — porty i protokoły dla agenta Azure AD Connect Health dla (AD FS/Sync) i usługi Azure AD
W tej tabeli opisano następujące porty i protokoły wychodzące, które są wymagane do komunikacji między agentami Azure AD Connect Health i usługą Azure AD.  

| Protokół | Porty | Opis |
| --- | --- | --- |
| HTTPS |443 (TCP) |Wychodzący |
| Usługa Azure Service Bus |5671 (TCP) |Wychodzący |

Azure Service Bus port 5671 nie jest już wymagany dla najnowszej wersji agenta. Najnowsza wersja agenta Azure AD Connect Health wymagana tylko przez port 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b — punkty końcowe dla agenta Azure AD Connect Health dla (AD FS/Sync) i usługi Azure AD
Aby uzyskać listę punktów końcowych, zapoznaj się [z sekcją wymagania agenta Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).

