---
title: Łączenie danych usługi Azure AD z usługą Azure wskaźnikowego | Microsoft Docs
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588624"
---
# <a name="connect-data-from-azure-active-directory"></a>Łączenie danych z Azure Active Directory



Wskaźnik produkcji platformy Azure umożliwia zbieranie danych z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) i przesyłanie ich strumieniowo do usługi Azure wskaźnikowej. Możesz wybrać opcję przesyłania strumieniowego [dzienników logowania](../active-directory/reports-monitoring/concept-sign-ins.md) i [dzienników inspekcji](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli chcesz wyeksportować dane logowania z Active Directory, musisz mieć licencję na usługę Azure AD P1 lub P2.

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń w dzierżawie, z której mają być przesyłane strumieniowo dzienniki.

- Aby można było zobaczyć stan połączenia, musisz mieć uprawnienia dostępu do dzienników diagnostycznych usługi Azure AD. 


## <a name="connect-to-azure-ad"></a>Łączenie z usługą Azure AD

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Azure Active Directory** .

1. Obok dzienników, które chcesz przesłać strumieniowo do usługi Azure wskaźnikowej, kliknij pozycję **Połącz**.

1. Możesz wybrać, czy alerty z usługi Azure AD mają automatycznie generować zdarzenia na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Azure AD, wyszukaj ciąg **SigninLogs** i **AuditLogs**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia usługi Azure AD z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
