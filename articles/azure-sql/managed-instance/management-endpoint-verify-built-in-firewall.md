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
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853275"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Reguły zabezpieczeń ruchu przychodzącego](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) dotyczące wystąpienia zarządzanego Azure SQL wymagają, aby porty zarządzania 9000, 9003, 1438, 1440 i 1452 były otwarte z **dowolnego źródła** w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która chroni wystąpienie zarządzane SQL. Chociaż te porty są otwarte na poziomie sieciowej grupy zabezpieczeń, są one chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Weryfikuj zaporę

Aby sprawdzić te porty, użyj dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak używać jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wystąpieniem i łącznością SQL, zobacz [Architektura łączności wystąpienia zarządzanego Azure SQL](connectivity-architecture-overview.md).
