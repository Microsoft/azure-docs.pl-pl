---
title: Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
description: Włącz funkcję RBAC, aby autoryzować dostęp do wystąpienia konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472624"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
Usługa Azure App Configuration obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań do wystąpień konfiguracji aplikacji.  Usługa Azure AD umożliwia korzystanie z kontroli dostępu opartej na rolach (RBAC) do przyznawania uprawnień podmiotowi zabezpieczeń.  Podmiot zabezpieczeń może być użytkownikiem lub [podmiotem usługi aplikacji](../active-directory/develop/app-objects-and-service-principals.md).  Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Żądania wysyłane przez podmiot zabezpieczeń (użytkownika lub aplikacje) w celu uzyskania dostępu do zasobu konfiguracji aplikacji muszą być autoryzowane.  W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym.
1. Tożsamość podmiotu zabezpieczeń jest uwierzytelniana i zwracany jest token OAuth 2,0.  Nazwa zasobu do żądania tokenu jest `https://login.microsoftonline.com/{tenantID}` `{tenantID}` zgodna z identyfikatorem dzierżawy Azure Active Directory, do której należy jednostka usługi.
2. Token jest przesyłany w ramach żądania do usługi konfiguracji aplikacji w celu autoryzowania dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania.  Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja Azure Functions, aplikacja internetowa platformy Azure lub maszyna wirtualna platformy Azure, może korzystać z tożsamości zarządzanej w celu uzyskania dostępu do zasobów.  Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Azure App Configuration, zobacz temat [uwierzytelnianie dostępu do zasobów konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](howto-integrate-azure-managed-service-identity.md).

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Usługa Azure App Configuration oferuje role RBAC, które obejmują zestawy uprawnień dla zasobów konfiguracji aplikacji. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia podane do podmiotu. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [wbudowane role RBAC dla konfiguracji aplikacji platformy Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md).

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp jest objęty zakresem do zasobu konfiguracji aplikacji. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Wbudowane role RBAC dla konfiguracji aplikacji platformy Azure
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- Właściciel danych konfiguracji aplikacji platformy Azure: Użyj tej roli, aby udzielić dostępu do odczytu i zapisu do zasobów konfiguracji aplikacji.
- Czytnik danych konfiguracji aplikacji platformy Azure: Użyj tej roli, aby zapewnić dostęp do odczytu do zasobów konfiguracji aplikacji.
- Współautor: Ta rola umożliwia uzyskanie dostępu administratora do usługi bez udzielania dostępu do danych przechowywanych w wystąpieniu konfiguracji aplikacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o używaniu [tożsamości zarządzanych](howto-integrate-azure-managed-service-identity.md) do administrowania usługą konfiguracji aplikacji.