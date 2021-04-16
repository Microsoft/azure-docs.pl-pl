---
title: Rozwiązywanie problemów z błędami Azure Active Directory interfejsu API raportowania | Microsoft Docs
description: Zapewnia rozwiązanie błędów podczas wywoływania interfejsów API Azure Active Directory raportowania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533696"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API Azure Active Directory raportowania

W tym artykule wymieniono typowe komunikaty o błędach, które mogą wystąpić podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API Microsoft Graph, oraz kroki rozwiązywania problemów.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Wewnętrzny błąd serwera HTTP 500 podczas uzyskiwania dostępu do punktu końcowego Microsoft Graph V2

Obecnie punkt końcowy usługi Microsoft Graph w wersji 2 — upewnij się, że uzyskujesz dostęp do dzienników aktywności przy użyciu punktu końcowego Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Żadna dzierżawa nie jest dzierżawą B2C lub dzierżawa nie ma licencji Premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji Azure Active Directory Premium 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa ma licencję usługi Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Błąd: Użytkownik nie znajduje się w dozwolonych rolach 

Jeśli ten komunikat o błędzie jest wyświetlany podczas próby uzyskania dostępu do dzienników inspekcji  lub logowania  przy użyciu interfejsu API, upewnij się, że Twoje konto jest częścią roli Czytelnik zabezpieczeń lub Czytelnik raportów w dzierżawie usługi Azure Active Directory dzierżawie. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: Aplikacja nie ma uprawnienia "Odczyt danych katalogu" w usłudze AAD 

Wykonaj kroki opisane w części Wymagania wstępne, aby uzyskać dostęp do interfejsu [API](howto-configure-prerequisites-for-reporting-api.md) raportowania Azure Active Directory, aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Błąd: Aplikacja nie ma uprawnienia Microsoft Graph API "Odczyt wszystkich danych dziennika inspekcji"

Wykonaj kroki opisane w części Wymagania wstępne, aby uzyskać dostęp do interfejsu [API](howto-configure-prerequisites-for-reporting-api.md) raportowania Azure Active Directory, aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

[Korzystanie z odwołania do interfejsu API inspekcji](/graph/api/resources/directoryaudit) 
 [Używanie odwołania interfejsu API raportu działań logowania](/graph/api/resources/signin)