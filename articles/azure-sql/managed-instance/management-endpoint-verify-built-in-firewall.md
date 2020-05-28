---
title: Sprawdzanie zabezpieczeń portów w zaporze wbudowanej
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045074"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Reguły zabezpieczeń ruchu przychodzącego](connectivity-architecture-overview.md#mandatory-inbound-security-rules) dotyczące wystąpienia zarządzanego Azure SQL wymagają, aby porty zarządzania 9000, 9003, 1438, 1440, 1452 były otwarte z **dowolnego źródła** w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która chroni wystąpienie zarządzane SQL. Chociaż te porty są otwarte na poziomie sieciowej grupy zabezpieczeń, są one chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Weryfikuj zaporę

Aby sprawdzić te porty, użyj dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak używać jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o wystąpieniach i połączeniach zarządzanych przez usługę SQL, zobacz [Architektura łączności wystąpienia zarządzanego Azure SQL](connectivity-architecture-overview.md).
