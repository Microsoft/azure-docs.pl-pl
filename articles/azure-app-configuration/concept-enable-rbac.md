---
title: Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
description: Włącz funkcję RBAC, aby autoryzować dostęp do wystąpienia konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: c2812219e689cb42fd871f85300239a10ab0da0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116721"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
Oprócz używania kod uwierzytelniania wiadomości opartego na skrótach (HMAC) Konfiguracja aplikacji platformy Azure obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań do wystąpień konfiguracji aplikacji.  Usługa Azure AD umożliwia korzystanie z kontroli dostępu opartej na rolach (RBAC) do przyznawania uprawnień podmiotowi zabezpieczeń.  Podmiotem zabezpieczeń może być użytkownik, [tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) lub nazwa [główna usługi aplikacji](../active-directory/develop/app-objects-and-service-principals.md).  Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Żądania wysyłane przez podmiot zabezpieczeń w celu uzyskania dostępu do zasobu konfiguracji aplikacji muszą być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym:
1. Tożsamość podmiotu zabezpieczeń jest uwierzytelniana i zwracany jest token OAuth 2,0.  Nazwa zasobu do żądania tokenu jest `https://login.microsoftonline.com/{tenantID}` `{tenantID}` zgodna z identyfikatorem dzierżawy Azure Active Directory, do której należy jednostka usługi.
2. Token jest przesyłany w ramach żądania do usługi konfiguracji aplikacji w celu autoryzowania dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania.  Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja Azure Functions, aplikacja internetowa platformy Azure lub maszyna wirtualna platformy Azure, może korzystać z tożsamości zarządzanej w celu uzyskania dostępu do zasobów.  Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Azure App Configuration, zobacz temat [uwierzytelnianie dostępu do zasobów konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](howto-integrate-azure-managed-service-identity.md).

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Usługa Azure App Configuration oferuje role RBAC, które obejmują zestawy uprawnień dla zasobów konfiguracji aplikacji. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia podane do podmiotu. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [wbudowane role RBAC dla konfiguracji aplikacji platformy Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md).

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp jest objęty zakresem do zasobu konfiguracji aplikacji. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Wbudowane role RBAC dla konfiguracji aplikacji platformy Azure
Platforma Azure udostępnia następujące wbudowane role RBAC do autoryzowania dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- **Właściciel danych konfiguracji aplikacji**: Użyj tej roli, aby przyznać dostęp do odczytu/zapisu/usuwania do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.
- **Czytnik danych konfiguracji aplikacji**: Użyj tej roli, aby zapewnić dostęp do odczytu do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.
- **Współautor**: Ta rola służy do zarządzania zasobem konfiguracji aplikacji. Podczas gdy dane konfiguracji aplikacji są dostępne przy użyciu kluczy dostępu, ta rola nie udziela bezpośredniego dostępu do danych za pomocą usługi Azure AD.
- **Czytelnik**: Ta rola umożliwia dostęp do odczytu do zasobu konfiguracji aplikacji. Nie powoduje to przyznania dostępu do kluczy dostępu zasobu ani do danych przechowywanych w konfiguracji aplikacji.

> [!NOTE]
> Obecnie Azure Portal i interfejs wiersza polecenia obsługują uwierzytelnianie HMAC tylko w celu uzyskania dostępu do danych konfiguracji aplikacji. Uwierzytelnianie usługi Azure AD nie jest obsługiwane. W związku z tym użytkownicy Azure Portal i interfejsu wiersza polecenia wymagają, aby rola *współautor* mogła pobrać klucze dostępu do zasobu konfiguracji aplikacji. Udzielanie dostępu do *czytnika danych konfiguracji aplikacji* lub roli *właściciela danych konfiguracji aplikacji* nie ma wpływu na dostęp za pomocą portalu i interfejsu wiersza polecenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o używaniu [tożsamości zarządzanych](howto-integrate-azure-managed-service-identity.md) do administrowania usługą konfiguracji aplikacji.
