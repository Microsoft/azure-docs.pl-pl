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
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802257"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Łączenie danych z Azure Active Directory (Azure AD)

Korzystając z wbudowanego łącznika kontrolki Azure, można zbierać dane z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłać je strumieniowo do usługi Azure wskaźnikowej. Łącznik umożliwia przesyłanie strumieniowe [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć subskrypcję [Azure AD — wersja Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) , aby pozyskiwanie dzienników logowania do platformy Azure — wskaźnik. Dodatkowe opłaty za gigabajty mogą dotyczyć Azure Monitor (Log Analytics) i platformy Azure.

- Użytkownik musi mieć przypisaną rolę współautora wskaźnikowego platformy Azure w obszarze roboczym.

- Użytkownik musi mieć przypisane role administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której chcesz przesyłać strumieniowo dzienniki.

- Użytkownik musi mieć uprawnienia do odczytu i zapisu w ustawieniach diagnostycznych usługi Azure AD, aby można było zobaczyć stan połączenia. 

## <a name="connect-to-azure-active-directory"></a>Połącz z Azure Active Directory

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Z galerii łączniki danych wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **Otwórz stronę łącznika**.

1. Zaznacz pola wyboru obok typów dzienników, które chcesz przesłać strumieniowo do usługi Azure wskaźnikowej, a następnie kliknij przycisk **Połącz**. Oto typy dzienników, spośród których można wybrać:

    - **Dzienniki logowania**: informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji**: informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **LogManagement** w następujących tabelach:

- `SigninLogs`
- `AuditLogs`

Aby wykonać zapytanie dotyczące dzienników usługi Azure AD, wprowadź odpowiednią nazwę tabeli w górnej części okna zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Active Directory z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
