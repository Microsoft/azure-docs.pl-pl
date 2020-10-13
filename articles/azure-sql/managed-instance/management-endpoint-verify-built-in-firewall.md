---
title: Sprawdzanie zabezpieczeń portów w wbudowanej zaporze
description: Dowiedz się, jak sprawdzić wbudowaną ochronę zapory w wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 35d0053d8c45547eaa5e89e6da15fcaf0343d6c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617302"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Reguły zabezpieczeń ruchu przychodzącego](connectivity-architecture-overview.md#mandatory-inbound-security-rules) dotyczące wystąpienia zarządzanego Azure SQL wymagają, aby porty zarządzania 9000, 9003, 1438, 1440 i 1452 były otwarte z **dowolnego źródła** w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która chroni wystąpienie zarządzane SQL. Chociaż te porty są otwarte na poziomie sieciowej grupy zabezpieczeń, są one chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Weryfikuj zaporę

Aby sprawdzić te porty, użyj dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak używać jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wystąpieniem i łącznością SQL, zobacz [Architektura łączności wystąpienia zarządzanego Azure SQL](connectivity-architecture-overview.md).
