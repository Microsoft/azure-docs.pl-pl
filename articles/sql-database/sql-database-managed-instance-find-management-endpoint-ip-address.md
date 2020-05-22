---
title: Odkryj punkt końcowy zarządzania wystąpieniami zarządzanymi
description: Dowiedz się, jak uzyskać Azure SQL Database publiczny adres IP punktu końcowego zarządzania wystąpieniami zarządzanymi i zweryfikować jego wbudowaną ochronę zapory
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
ms.openlocfilehash: ac4675be25cb5f2f65cc444e65bd25d65d3c3856
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770044"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Określ adres IP punktu końcowego zarządzania

Klaster wirtualny Azure SQL Database wystąpienia zarządzanego zawiera punkt końcowy zarządzania wykorzystywany przez firmę Microsoft do zarządzania operacjami. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory na poziomie sieci i weryfikacji certyfikatu wzajemnego na poziomie aplikacji. Możesz określić adres IP punktu końcowego zarządzania, ale nie możesz uzyskać dostępu do tego punktu końcowego.

Aby określić adres IP zarządzania, wykonaj wyszukiwanie DNS na nazwie FQDN wystąpienia zarządzanego: `mi-name.zone_id.database.windows.net` . Spowoduje to zwrócenie wpisu DNS, który jest taki sam `trx.region-a.worker.vnet.database.windows.net` . Następnie można wykonać wyszukiwanie DNS dla tej nazwy FQDN z usuniętym elementem ". VNET". Spowoduje to zwrócenie adresu IP zarządzania. 

Ten program PowerShell wykona wszystkie czynności, jeśli zamienisz \< nazwę FQDN \> z WPISem DNS w wystąpieniu zarządzanym: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Aby uzyskać więcej informacji na temat zarządzanych wystąpień i połączeń, zobacz [Azure SQL Database architektury łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
