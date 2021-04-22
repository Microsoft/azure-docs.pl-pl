---
title: Łączenie Azure Active Directory danych z Azure Sentinel | Microsoft Docs
description: Dowiedz się, jak zbierać dane z Azure Active Directory i przesyłać strumieniowo dzienniki logowania, inspekcji i inicjowania obsługi administracyjnej usługi Azure AD Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872375"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Łączenie Azure Active Directory (Azure AD) z Azure Sentinel

Wbudowanego łącznika Azure Sentinel można użyć do zbierania [](../active-directory/fundamentals/active-directory-whatis.md) danych z Azure Active Directory przesyłania strumieniowego do Azure Sentinel. Łącznik umożliwia przesyłanie strumieniowe następujących typów dzienników:

- [**Dzienniki logowania ,**](../active-directory/reports-monitoring/concept-all-sign-ins.md)które zawierają informacje [o](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) interakcyjnych logowaniach użytkowników, w których użytkownik udostępnia współczynnik uwierzytelniania.

    Łącznik usługi Azure AD zawiera teraz trzy dodatkowe kategorie dzienników logowania— wszystkie obecnie dostępne w wersji **ZAPOZNAWCZEJ:**
    
    - [**Dzienniki logowania użytkownika**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)nieinterakcyjnego, które zawierają informacje o logowaniach wykonywanych przez klienta w imieniu użytkownika bez interakcji ani uwierzytelniania użytkownika.
    
    - [**Dzienniki logowania jednostki usługi**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), które zawierają informacje o logowaniach przez aplikacje i jednostki usługi, które nie obejmują żadnego użytkownika. W tych logowaniach aplikacja lub usługa udostępnia poświadczenia we własnym imieniu, aby uwierzytelnić zasoby lub uzyskać do nich dostęp.
    
    - [**Dzienniki logowania tożsamości zarządzanej**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), które zawierają informacje o logowaniach przez zasoby platformy Azure, które mają wpisy tajne zarządzane przez platformę Azure. Aby uzyskać więcej informacji, [zobacz Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Dzienniki inspekcji**](../active-directory/reports-monitoring/concept-audit-logs.md), które zawierają informacje o działaniach systemowych związanych z zarządzaniem użytkownikami i grupą, aplikacjami zarządzanymi i działaniami katalogu.

- [**Dzienniki aprowizowania**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (również w wersji **zapoznawczej),** które zawierają informacje o aktywności systemu dotyczące użytkowników, grup i ról aprowowanych przez usługę aprowiowania Azure AD. 

> [!IMPORTANT]
> Jak wskazano powyżej, niektóre z dostępnych typów dzienników są obecnie dostępne w wersji **zapoznawczej**. Aby uzyskać [Microsoft Azure dodatkowe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) postanowienia prawne dotyczące funkcji platformy Azure, które są dostępne w wersji beta, wersji zapoznawczej lub w inny sposób, które nie zostały jeszcze wydane w wersji ogólnie dostępnej, zobacz Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych.
## <a name="prerequisites"></a>Wymagania wstępne

- Do Azure Active Directory logowania do aplikacji jest wymagana licencja P1 Azure Sentinel lub P2. Każda licencja usługi Azure AD (Bezpłatna/O365/P1/P2) jest wystarczająca do pozyskania innych typów dzienników. Za każdy gigabajt mogą być naliczane dodatkowe opłaty Azure Monitor (Log Analytics) i Azure Sentinel.

- Użytkownik musi mieć przypisaną rolę współautora Azure Sentinel w obszarze roboczym.

- Użytkownik musi mieć przypisane role administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której chcesz przesyłać strumieniowo dzienniki.

- Aby można było wyświetlić stan połączenia, użytkownik musi mieć uprawnienia do odczytu i zapisu w ustawieniach diagnostycznych usługi Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Nawiązywanie połączenia z Azure Active Directory

1. W Azure Sentinel wybierz pozycję **Łączniki danych** z menu nawigacji.

1. W galerii łączników danych wybierz pozycję **Azure Active Directory** a następnie wybierz **pozycję Otwórz stronę łącznika.**

1. Zaznacz pola wyboru obok typów dzienników, które chcesz przesyłać strumieniowo do Azure Sentinel (patrz powyżej), a następnie kliknij przycisk **Połącz**.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w obszarze **Dzienniki** w sekcji **LogManagement** w następujących tabelach:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Aby odpytować dzienniki usługi Azure AD, wprowadź odpowiednią nazwę tabeli w górnej części okna zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie opisano sposób nawiązywania połączenia Azure Active Directory z Azure Sentinel. Aby dowiedzieć się więcej o Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, [jak uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpoczynanie [wykrywania zagrożeń za pomocą Azure Sentinel.](tutorial-detect-threats-built-in.md)
