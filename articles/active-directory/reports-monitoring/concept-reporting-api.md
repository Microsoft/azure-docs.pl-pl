---
title: Wprowadzenie do interfejsu API raportowania usługi Azure AD | Microsoft Docs
description: Jak rozpocząć pracę z interfejsem API Azure Active Directory raportowania
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e5a095c87e46839c7c120bdd6d8db1595164e57
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532516"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Wprowadzenie do interfejsu API Azure Active Directory raportowania

Azure Active Directory udostępnia różne raporty [zawierające](overview-reports.md)przydatne informacje dla aplikacji, takich jak systemy SIEM, narzędzia inspekcji i analizy biznesowej. 

Korzystając z interfejsu API Microsoft Graph dla raportów usługi Azure AD, można uzyskać dostęp programowy do danych za pośrednictwem zestawu interfejsów API opartych na interfejsie REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera omówienie interfejsu API raportowania, w tym sposobów uzyskiwania do niego dostępu.

W przypadku problemów zobacz, jak [uzyskać pomoc techniczną Azure Active Directory.](../fundamentals/active-directory-troubleshooting-support-howto.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do interfejsu API raportowania z interwencją użytkownika lub bez tej interwencji, należy:

1. Przypisywanie ról (czytelnik zabezpieczeń, administrator zabezpieczeń, administrator globalny)
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbieranie ustawień konfiguracji

Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API Azure Active Directory raportowania.](howto-configure-prerequisites-for-reporting-api.md) 

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API 

Punkt Microsoft Graph API dla dzienników inspekcji to , a punkt końcowy Microsoft Graph API logowania `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` to `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Aby uzyskać więcej informacji, zobacz dokumentacja [interfejsu API inspekcji](/graph/api/resources/directoryaudit) i [dokumentacja interfejsu API logowania](/graph/api/resources/signIn).

Interfejs API wykrywania [ryzyka usługi Identity Protection](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) umożliwia uzyskanie programowego dostępu do wykrywania zabezpieczeń przy użyciu Microsoft Graph. Aby uzyskać więcej informacji, [zobacz Get started with Azure Active Directory Identity Protection and Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)(Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph ). 
  
Możesz również użyć interfejsu [API dzienników aprowizowania,](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) aby uzyskać dostęp programowy do zdarzeń aprowrowi w dzierżawie. 

## <a name="apis-with-microsoft-graph-explorer"></a>Interfejsy API w Microsoft Graph Explorer

Możesz użyć eksploratora [Microsoft Graph,](https://developer.microsoft.com/graph/graph-explorer) aby zweryfikować dane interfejsu API logowania i inspekcji. Zaloguj się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika eksploratora programu Graph i ustaw uprawnienia **AuditLog.Read.All** i **Directory.Read.All** dla dzierżawy, jak pokazano poniżej.   

![Eksplorator programu Graph](./media/concept-reporting-api/graph-explorer.png)

![Interfejs użytkownika modyfikowania uprawnień](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Uzyskiwanie dostępu do interfejsu API raportowania usługi Azure AD przy użyciu certyfikatów 

Użyj interfejsu API raportowania usługi Azure AD z certyfikatami, jeśli planujesz pobrać dane raportowania bez interwencji użytkownika.

Aby uzyskać szczegółowe instrukcje, zobacz Get data using the Azure AD Reporting API with certificates (Uzyskiwanie danych [przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami).](tutorial-access-api-with-certificates.md)

## <a name="next-steps"></a>Następne kroki

 * [Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API raportowania](howto-configure-prerequisites-for-reporting-api.md) 
 * [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure AD z certyfikatami](tutorial-access-api-with-certificates.md)
 * [Rozwiązywanie problemów z błędami w interfejsie API raportowania usługi Azure AD](troubleshoot-graph-api.md)