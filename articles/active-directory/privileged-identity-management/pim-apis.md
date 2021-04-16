---
title: Microsoft Graph API dla usługi PIM (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Zawiera informacje dotyczące korzystania z interfejsów API Microsoft Graph usługi Azure AD Privileged Identity Management (PIM) (wersja zapoznawcza).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce1900ca1ca04923f98642f0783925f2dd06619b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533570"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph API dla Privileged Identity Management (wersja zapoznawcza)

Zadania Privileged Identity Management można wykonywać przy użyciu [interfejsów API Microsoft Graph dla](/graph/overview) Azure Active Directory. W tym artykule opisano ważne pojęcia dotyczące używania interfejsów API Microsoft Graph dla Privileged Identity Management.

Szczegółowe informacje na temat interfejsów API programu Microsoft Graph można znaleźć w [dokumentacji interfejsu API usługi Azure AD Privileged Identity Management](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true).

> [!IMPORTANT]
> Interfejsy API w wersji beta w programie Microsoft Graph są w wersji zapoznawczej i mogą ulec zmianie. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby wywołać Microsoft Graph API dla Privileged Identity Management, musisz **mieć** co najmniej jedno z następujących uprawnień:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Ustawianie uprawnień

Aby aplikacje mogą wywołać interfejsy API Microsoft Graph dla Privileged Identity Management, muszą mieć wymagane uprawnienia. Najprostszym sposobem określenia wymaganych uprawnień jest użycie [platformy wyrażania zgody w usłudze Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Ustawianie uprawnień w Eksploratorze programu Graph

Jeśli do testowania wywołań używasz Eksploratora programu Graph, możesz określić uprawnienia w tym narzędziu.

1. Zaloguj się do [Eksploratora programu Graph](https://developer.microsoft.com/graph/graph-explorer) jako administrator globalny.

1. Kliknij pozycję **Modyfikuj uprawnienia**.

    ![Zrzut ekranu przedstawiający stronę "Eksplorator programu Graph" z wybraną akcją "Modyfikuj uprawnienia".](./media/pim-apis/graph-explorer.png)

1. Zaznacz pola wyboru obok uprawnień, które chcesz uwzględnić. Uprawnienia `PrivilegedAccess.ReadWrite.AzureAD` nie są jeszcze dostępne w Eksploratorze programu Graph.

    ![Eksplorator programu Graph — modyfikowanie uprawnień](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kliknij pozycję **Modyfikuj uprawnienia**, aby zastosować zmiany uprawnień.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja interfejsu API usługi Azure AD Privileged Identity Management](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)
