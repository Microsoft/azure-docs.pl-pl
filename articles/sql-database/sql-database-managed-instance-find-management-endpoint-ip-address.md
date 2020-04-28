---
title: Odkryj punkt końcowy zarządzania wystąpieniami zarządzanymi
description: Dowiedz się, jak uzyskać Azure SQL Database publiczny adres IP punktu końcowego zarządzania wystąpieniami zarządzanymi i zweryfikować jego wbudowaną ochronę zapory
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
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825717"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Określ adres IP punktu końcowego zarządzania

Klaster wirtualny Azure SQL Database wystąpienia zarządzanego zawiera punkt końcowy zarządzania wykorzystywany przez firmę Microsoft do zarządzania operacjami. Punkt końcowy zarządzania jest chroniony za pomocą wbudowanej zapory na poziomie sieci i weryfikacji certyfikatu wzajemnego na poziomie aplikacji. Możesz określić adres IP punktu końcowego zarządzania, ale nie możesz uzyskać dostępu do tego punktu końcowego.

Aby określić adres IP zarządzania, wykonaj wyszukiwanie DNS na nazwie FQDN wystąpienia zarządzanego: `mi-name.zone_id.database.windows.net`. Spowoduje to zwrócenie wpisu DNS, który jest taki `trx.region-a.worker.vnet.database.windows.net`sam. Następnie można wykonać wyszukiwanie DNS dla tej nazwy FQDN z usuniętym elementem ". VNET". Spowoduje to zwrócenie adresu IP zarządzania. 

Ten program PowerShell wykona wszystkie czynności, jeśli zamienisz \<nazwę FQDN\> z wpisem DNS w wystąpieniu zarządzanym: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Aby uzyskać więcej informacji na temat zarządzanych wystąpień i połączeń, zobacz [Azure SQL Database architektury łączności wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
