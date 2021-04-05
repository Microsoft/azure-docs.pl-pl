---
title: Łączenie Azure Active Directory danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak zbierać dane z Azure Active Directory i przesyłać dzienniki rejestracji, inspekcji i aprowizacji usługi Azure AD do usługi Azure wskaźnikowej.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99251985"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Łączenie danych Azure Active Directory (Azure AD) z platformą Azure — wskaźnikiem

Korzystając z wbudowanego łącznika kontrolki Azure, można zbierać dane z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłać je strumieniowo do usługi Azure wskaźnikowej. Łącznik umożliwia przesyłanie strumieniowe następujących typów dzienników:

- [**Dzienniki logowania**](../active-directory/reports-monitoring/concept-all-sign-ins.md), które zawierają informacje dotyczące [logowania użytkowników interakcyjnych](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) , w przypadku których użytkownik udostępnia czynnik uwierzytelniania.

    Łącznik usługi Azure AD zawiera teraz następujące trzy dodatkowe kategorie dzienników logowania, które są obecnie dostępne w **wersji zapoznawczej**:
    
    - [**Dzienniki logowania użytkowników nieinterakcyjnych**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), które zawierają informacje o logowania wykonywane przez klienta w imieniu użytkownika bez żadnej interakcji z użytkownikiem.
    
    - [**Dzienniki logowania nazwy głównej usługi**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), które zawierają informacje dotyczące logowania przez aplikacje i jednostki usługi, które nie obejmują żadnego użytkownika. W tych logowaniach aplikacja lub usługa udostępnia poświadczenia w swoim imieniu w celu uwierzytelniania lub uzyskiwania dostępu do zasobów.
    
    - [**Dzienniki logowania tożsamości zarządzanej**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), które zawierają informacje dotyczące logowania za pomocą zasobów platformy Azure, które mają wpisy tajne zarządzane przez platformę Azure. Aby uzyskać więcej informacji, zobacz [co to są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Dzienniki inspekcji**](../active-directory/reports-monitoring/concept-audit-logs.md), które zawierają informacje o działaniu systemu związane z zarządzaniem użytkownikami i grupami, zarządzanymi aplikacjami i działaniami katalogu.

- [**Dzienniki aprowizacji**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (również w **wersji zapoznawczej**), które zawierają informacje o działaniu systemu dotyczące użytkowników, grup i ról, które są obsługiwane przez usługę aprowizacji usługi Azure AD. 

> [!IMPORTANT]
> Jak wskazano powyżej, niektóre z dostępnych typów dzienników są obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
## <a name="prerequisites"></a>Wymagania wstępne

- Każda licencja usługi Azure AD (bezpłatna/O365/P1/P2) jest wystarczająca do pozyskiwania dzienników logowania do platformy Azure. Dodatkowe opłaty za gigabajty mogą dotyczyć Azure Monitor (Log Analytics) i platformy Azure.

- Użytkownik musi mieć przypisaną rolę współautora wskaźnikowego platformy Azure w obszarze roboczym.

- Użytkownik musi mieć przypisane role administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której chcesz przesyłać strumieniowo dzienniki.

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w ustawieniach diagnostycznych usługi Azure AD, aby można było zobaczyć stan połączenia. 

## <a name="connect-to-azure-active-directory"></a>Połącz z Azure Active Directory

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Z galerii łączniki danych wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **Otwórz stronę łącznika**.

1. Zaznacz pola wyboru obok typów dzienników, które chcesz przesłać strumieniowo do usługi Azure wskaźnikowej (patrz powyżej), a następnie kliknij przycisk **Połącz**.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **LogManagement** w następujących tabelach:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Aby wykonać zapytanie dotyczące dzienników usługi Azure AD, wprowadź odpowiednią nazwę tabeli w górnej części okna zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Active Directory z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
