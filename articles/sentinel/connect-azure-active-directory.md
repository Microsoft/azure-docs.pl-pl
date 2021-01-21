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
ms.openlocfilehash: 9700e5d9179f7c1e33b2371eea89be9bb1c8d08f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621366"
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

1. Zaznacz pola wyboru obok dzienników, które chcesz przesłać do usługi Azure wskaźnikowej, a następnie kliknij przycisk **Połącz**.

1. Możesz wybrać, czy alerty z usługi Azure AD mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analizy, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Aby użyć odpowiedniego schematu w Log Analytics do wykonywania zapytań dotyczących alertów usługi Azure AD, wpisz `SigninLogs` lub `AuditLogs` w oknie zapytania.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Active Directory z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
