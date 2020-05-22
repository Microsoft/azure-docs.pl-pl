---
title: Odkryj wbudowaną zaporę wystąpienia zarządzanego
description: Dowiedz się, jak sprawdzić wbudowaną ochronę zapory w Azure SQL Database wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 91b8b37de69a3f08bf11650e0328b616d8633e7a
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769925"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Weryfikowanie wbudowanej zapory wystąpienia zarządzanego

[Reguły zabezpieczeń ruchu przychodzącego](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) dla wystąpień zarządzanych wymagają portów zarządzania 9000, 9003, 1438, 1440, 1452, aby można było otworzyć z **dowolnego źródła** w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń), która chroni wystąpienie zarządzane. Chociaż te porty są otwarte na poziomie sieciowej grupy zabezpieczeń, są one chronione na poziomie sieci przez wbudowaną zaporę.

## <a name="verify-firewall"></a>Weryfikuj zaporę

Aby sprawdzić te porty, użyj dowolnego narzędzia skanera zabezpieczeń do przetestowania tych portów. Poniższy zrzut ekranu pokazuje, jak używać jednego z tych narzędzi.

![Weryfikowanie wbudowanej zapory](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzanych wystąpień i połączeń, zobacz [Azure SQL Database architektury łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).