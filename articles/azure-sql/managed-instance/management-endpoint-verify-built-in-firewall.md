---
title: Sprawdzanie zabezpieczeń portów w wbudowanej zaporze
description: Dowiedz się, jak sprawdzić wbudowaną ochronę zapory w wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 561aba31bfd1cc37173f3ee41625ba3687f5e657
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660917"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Reguły zabezpieczeń ruchu przychodzącego](connectivity-architecture-overview.md#mandatory-inbound-security-rules) dotyczące wystąpienia zarządzanego Azure SQL wymagają, aby porty zarządzania 9000, 9003, 1438, 1440 i 1452 były otwarte z **dowolnego źródła** w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która chroni wystąpienie zarządzane SQL. Chociaż te porty są otwarte na poziomie sieciowej grupy zabezpieczeń, są one chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Weryfikuj zaporę

Aby sprawdzić te porty, użyj dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak używać jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania wystąpieniem i łącznością SQL, zobacz [Architektura łączności wystąpienia zarządzanego Azure SQL](connectivity-architecture-overview.md).
