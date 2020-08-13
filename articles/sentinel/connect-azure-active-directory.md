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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185693"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Łączenie danych z Azure Active Directory (Azure AD)



Wskaźnik produkcji platformy Azure umożliwia zbieranie danych z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłanie ich strumieniowo do usługi Azure wskaźnikowej. Możesz wybrać opcję przesyłania strumieniowego  [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna licencja usługi Azure AD (bezpłatna/O365/P1/P2) może być zintegrowana z Azure Monitor i pobierania dzienników logowania do platformy Azure. Dodatkowe opłaty za gigabajt będą dotyczyć Azure Monitor lub platformy Azure.

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
