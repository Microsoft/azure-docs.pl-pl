---
title: Łączenie Azure Active Directory danych z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć Azure Active Directory dane z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5ec8a37a0c782a5426b87f785af2d047ca35aa22
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374818"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Łączenie danych z Azure Active Directory (Azure AD)



Wskaźnik produkcji platformy Azure umożliwia zbieranie danych z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłanie ich strumieniowo do usługi Azure wskaźnikowej. Możesz wybrać opcję przesyłania strumieniowego [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować dane logowania z usługi Azure AD, musisz mieć licencję na usługę Azure AD P1 lub P2.

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której mają być przesyłane strumieniowo dzienniki.

- Aby można było zobaczyć stan połączenia, musisz mieć uprawnienia dostępu do dzienników diagnostycznych usługi Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Połącz z Azure Active Directory

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. W galerii łączniki danych wybierz pozycję **Azure Active Directory** a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. Zaznacz pola wyboru obok dzienników, które chcesz przesłać do usługi Azure wskaźnikowej, a następnie kliknij przycisk **Połącz**.

1. Możesz wybrać, czy alerty z usługi Azure AD mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Aby użyć odpowiedniego schematu w Log Analytics do wykonywania zapytań dotyczących alertów usługi Azure AD, wpisz `SigninLogs` lub `AuditLogs` w oknie zapytania.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia Azure Active Directory z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
