---
title: Łączenie Azure Active Directory danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak zbierać dane z Azure Active Directory i przesyłać dzienniki logowania usługi Azure AD i dzienniki inspekcji do usługi Azure wskaźnikowej.
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
ms.openlocfilehash: 409a316bd9c4222dd9b8ff30e42e37d23805c38b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757766"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Łączenie danych z Azure Active Directory (Azure AD)

Korzystając z wbudowanego łącznika kontrolki Azure, można zbierać dane z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłać je strumieniowo do usługi Azure wskaźnikowej. Łącznik umożliwia przesyłanie strumieniowe [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Każda licencja usługi Azure AD (bezpłatna/O365/P1/P2) jest wystarczająca do pozyskiwania dzienników logowania do platformy Azure. Dodatkowe opłaty za gigabajty mogą dotyczyć Azure Monitor (Log Analytics) i platformy Azure.

- Użytkownik musi mieć przypisaną rolę współautora wskaźnikowego platformy Azure w obszarze roboczym.

- Użytkownik musi mieć przypisane role administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której chcesz przesyłać strumieniowo dzienniki.

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w ustawieniach diagnostycznych usługi Azure AD, aby można było zobaczyć stan połączenia. 

## <a name="connect-to-azure-active-directory"></a>Połącz z Azure Active Directory

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Z galerii łączniki danych wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **Otwórz stronę łącznika**.

1. Zaznacz pola wyboru obok typów dzienników, które chcesz przesłać strumieniowo do usługi Azure wskaźnikowej, a następnie kliknij przycisk **Połącz**. Oto typy dzienników, spośród których można wybrać:

    - **Dzienniki logowania**: informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji**: informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.
    - **Dzienniki logowania użytkowników nieinterakcyjnych**: informacje dotyczące logowania wykonywane przez klienta w imieniu użytkownika, które nie wymagają interakcji z użytkownikiem ani jej współczynników uwierzytelniania.
    - **Dzienniki logowania nazwy głównej usługi**: informacje dotyczące logowania przez aplikacje i jednostki usługi, które nie obejmują żadnego użytkownika. W tych logowaniach aplikacja lub usługa udostępnia poświadczenia w swoim imieniu w celu uwierzytelniania lub uzyskiwania dostępu do zasobów.
    - **Dzienniki logowania tożsamości zarządzanej**: logowania przez zasoby platformy Azure, które mają wpisy tajne zarządzane przez platformę Azure.
    - **Dzienniki aprowizacji**: informacje o aktywności systemu dotyczące użytkowników, grup i ról, które są obsługiwane przez usługę aprowizacji usługi Azure AD.

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
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
