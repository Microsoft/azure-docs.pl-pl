---
title: Autoryzacja dostępu za pomocą usługi Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące autoryzacji dostępu do zasobów usługi Azure Signal Service przy użyciu Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797580"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autoryzuj dostęp do zasobów usługi Azure sygnalizacyjnej przy użyciu Azure Active Directory
Usługa Azure Signal Service obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań do zasobów usługi Azure Signal Service. Za pomocą usługi Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem lub podmiotem usługi aplikacji. Aby dowiedzieć się więcej o rolach i przypisaniach ról, zobacz [opis różnych ról](../role-based-access-control/overview.md).

## <a name="overview"></a>Omówienie
Gdy podmiot zabezpieczeń (aplikacja) próbuje uzyskać dostęp do zasobu usługi Azure Signal, żądanie musi być autoryzowane. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. 

 1. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Nazwa zasobu do żądania tokenu to `https://signalr.azure.com/` .
 2. Następnie token jest przesyłany jako część żądania do usługi Azure Signal, aby autoryzować dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli serwer centralny jest uruchomiony w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja funkcji platformy Azure, może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów. Aby dowiedzieć się, jak uwierzytelniać żądania wysyłane przez zarządzaną tożsamość do usługi Azure Signaler, zobacz temat [uwierzytelnianie dostępu do zasobów usługi Azure sygnalizacyjnej przy użyciu Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](authenticate-managed-identity.md). 

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Usługa Azure Signal Service oferuje role RBAC, które obejmują zestawy uprawnień dla zasobów usługi Azure Signal. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [wbudowane role platformy Azure dla usługi Azure Signal Service](#azure-built-in-roles-for-azure-signalr-service). 

Serwer centrum sygnałów, który nie działa w ramach jednostki platformy Azure, może również autoryzować się z usługą Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzowania żądań dotyczących zasobów usługi Azure sygnalizującego, zobacz temat [uwierzytelnianie dostępu do usługi Azure sygnalizującej za pomocą aplikacji Azure AD](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Wbudowane role platformy Azure dla usługi Azure Signal Service

- [Serwer aplikacji sygnalizujących]
- [Czytnik usługi sygnalizującej]
- [Właściciel usługi sygnalizującej]

## <a name="assign-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu
Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md). Usługa Azure Signal definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do usługi Azure Signaler i można także definiować role niestandardowe na potrzeby uzyskiwania dostępu do zasobu.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów lub dowolnego zasobu usługi Azure Signal. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem lub aplikacją lub [zarządzaną tożsamością dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Wbudowane role usługi Azure Signal Service
Platforma Azure udostępnia następujące wbudowane role platformy Azure umożliwiające autoryzowanie dostępu do zasobu usługi Azure Signal Service przy użyciu usługi Azure AD i uwierzytelniania OAuth:

### <a name="signalr-app-server"></a>Serwer aplikacji sygnalizujący

Ta rola umożliwia dostęp do uzyskiwania tymczasowego klucza dostępu do podpisywania tokenów klienta.

### <a name="signalr-serverless-contributor"></a>Współautor bezserwerowy sygnalizujący

Ta rola umożliwia dostęp do uzyskiwania tokenu klienta z usługi Azure Signal Service bezpośrednio.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami:

- [Uwierzytelnianie aplikacji w usłudze Azure AD w celu uzyskania dostępu do usługi Azure Signal](authenticate-application.md)
- [Uwierzytelnianie zarządzanej tożsamości za pomocą usługi Azure AD w celu uzyskania dostępu do usług Azure Signal](authenticate-managed-identity.md)