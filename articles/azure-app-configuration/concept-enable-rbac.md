---
title: Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
description: Włącz funkcję RBAC platformy Azure, aby autoryzować dostęp do wystąpienia konfiguracji aplikacji platformy Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 7e7b524f125eda406adc9be2300c94e5944e1819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715956"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autoryzuj dostęp do konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory
Oprócz używania kod uwierzytelniania wiadomości opartego na skrótach (HMAC) Konfiguracja aplikacji platformy Azure obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań do wystąpień konfiguracji aplikacji.  Usługa Azure AD umożliwia korzystanie z kontroli dostępu opartej na rolach (Azure RBAC) w celu udzielania uprawnień podmiotowi zabezpieczeń.  Podmiotem zabezpieczeń może być użytkownik, [tożsamość zarządzana](../active-directory/managed-identities-azure-resources/overview.md) lub nazwa [główna usługi aplikacji](../active-directory/develop/app-objects-and-service-principals.md).  Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Żądania wysyłane przez podmiot zabezpieczeń w celu uzyskania dostępu do zasobu konfiguracji aplikacji muszą być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym:
1. Tożsamość podmiotu zabezpieczeń jest uwierzytelniana i zwracany jest token OAuth 2,0.  Nazwa zasobu do żądania tokenu jest `https://login.microsoftonline.com/{tenantID}` `{tenantID}` zgodna z identyfikatorem dzierżawy Azure Active Directory, do której należy jednostka usługi.
2. Token jest przesyłany w ramach żądania do usługi konfiguracji aplikacji w celu autoryzowania dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania.  Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja Azure Functions, aplikacja internetowa platformy Azure lub maszyna wirtualna platformy Azure, może korzystać z tożsamości zarządzanej w celu uzyskania dostępu do zasobów.  Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Azure App Configuration, zobacz temat [uwierzytelnianie dostępu do zasobów konfiguracji aplikacji platformy Azure przy użyciu Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](howto-integrate-azure-managed-service-identity.md).

Krok autoryzacji wymaga, aby co najmniej jedna rola platformy Azure była przypisana do podmiotu zabezpieczeń. Usługa Azure App Configuration oferuje role platformy Azure, które obejmują zestawy uprawnień dla zasobów konfiguracji aplikacji. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia podane do podmiotu. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [wbudowane role platformy Azure dla usługi Azure App Configuration](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md).

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp jest objęty zakresem do zasobu konfiguracji aplikacji. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Wbudowane role platformy Azure dla usługi Azure App Configuration
Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do danych konfiguracji aplikacji przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- **Właściciel danych konfiguracji aplikacji**: Użyj tej roli, aby przyznać dostęp do odczytu/zapisu/usuwania do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.
- **Czytnik danych konfiguracji aplikacji**: Użyj tej roli, aby zapewnić dostęp do odczytu do danych konfiguracji aplikacji. Nie powoduje to przyznania dostępu do zasobu konfiguracji aplikacji.
- **Współautor**: Ta rola służy do zarządzania zasobem konfiguracji aplikacji. Podczas gdy dane konfiguracji aplikacji są dostępne przy użyciu kluczy dostępu, ta rola nie udziela bezpośredniego dostępu do danych za pomocą usługi Azure AD.
- **Czytelnik**: Ta rola umożliwia dostęp do odczytu do zasobu konfiguracji aplikacji. Nie powoduje to przyznania dostępu do kluczy dostępu zasobu ani do danych przechowywanych w konfiguracji aplikacji.

> [!NOTE]
> Obecnie Azure Portal i interfejs wiersza polecenia obsługują uwierzytelnianie HMAC tylko w celu uzyskania dostępu do danych konfiguracji aplikacji. Uwierzytelnianie usługi Azure AD nie jest obsługiwane. W związku z tym użytkownicy Azure Portal i interfejsu wiersza polecenia wymagają, aby rola *współautor* mogła pobrać klucze dostępu do zasobu konfiguracji aplikacji. Udzielanie dostępu do *czytnika danych konfiguracji aplikacji* lub roli *właściciela danych konfiguracji aplikacji* nie ma wpływu na dostęp za pomocą portalu i interfejsu wiersza polecenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o używaniu [tożsamości zarządzanych](howto-integrate-azure-managed-service-identity.md) do administrowania usługą konfiguracji aplikacji.
