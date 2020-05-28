---
title: Odnajdź adres IP punktu końcowego zarządzania
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się, jak uzyskać publiczny adres IP punktu zarządzania wystąpienia zarządzanego usługi Azure SQL i zweryfikować jego wbudowaną ochronę zapory
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 88965c25702917f17a226cfa51de662703136aae
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045081"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Określanie adresu IP punktu końcowego zarządzania — wystąpienie zarządzane Azure SQL 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Klaster wirtualny wystąpienia zarządzanego Azure SQL zawiera punkt końcowy zarządzania wykorzystywany przez platformę Azure do operacji zarządzania. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory na poziomie sieci i weryfikacji certyfikatu wzajemnego na poziomie aplikacji. Możesz określić adres IP punktu końcowego zarządzania, ale nie możesz uzyskać dostępu do tego punktu końcowego.

Aby określić adres IP zarządzania, wykonaj [wyszukiwanie DNS](/windows-server/administration/windows-commands/nslookup) na nazwie FQDN wystąpienia zarządzanego SQL: `mi-name.zone_id.database.windows.net` . Spowoduje to zwrócenie wpisu DNS, który jest taki sam `trx.region-a.worker.vnet.database.windows.net` . Następnie można wykonać wyszukiwanie DNS dla tej nazwy FQDN z usuniętym elementem ". VNET". Spowoduje to zwrócenie adresu IP zarządzania. 

Ten program PowerShell wykona wszystkie czynności w przypadku zamienienia na \<MI FQDN\> wpis DNS wystąpienia zarządzanego SQL: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Aby uzyskać więcej informacji o wystąpieniach i połączeniach zarządzanych przez usługę SQL, zobacz [Architektura łączności wystąpienia zarządzanego Azure SQL](connectivity-architecture-overview.md).
