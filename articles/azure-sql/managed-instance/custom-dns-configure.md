---
title: Niestandardowe DNS
titleSuffix: Azure SQL Managed Instance
description: W tym temacie opisano opcje konfiguracji niestandardowego systemu DNS z wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 2ba5794ba647c28cde3b54a1afdfbd0201b23e8e
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84706158"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurowanie niestandardowego serwera DNS dla wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure. Istnieje kilka scenariuszy (na przykład Poczta usługi DB, połączone serwery z innymi wystąpieniami SQL Server w chmurze lub środowisku hybrydowym), które wymagają rozpoznawania prywatnych nazw hostów z wystąpienia zarządzanego SQL. W takim przypadku należy skonfigurować niestandardowy serwer DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane SQL używa tego samego systemu DNS do obsługi wewnętrznych, należy skonfigurować niestandardowy serwer DNS, aby można było rozpoznać nazwy domen publicznych.

> [!IMPORTANT]
> Zawsze używaj w pełni kwalifikowanej nazwy domeny (FQDN) dla serwera poczty, wystąpienia SQL Server i innych usług, nawet jeśli znajdują się w prywatnej strefie DNS. Na przykład, użyj `smtp.contoso.com` dla serwera poczty, ponieważ `smtp` nie zostanie poprawnie rozwiązany. Tworzenie połączonego serwera lub replikacji odwołującej się do SQL Server maszyn wirtualnych w ramach tej samej sieci wirtualnej wymaga również nazwy FQDN i domyślnego sufiksu DNS. Na przykład `SQLVM.internal.cloudapp.net`. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizacja serwerów DNS sieci wirtualnej nie będzie miała natychmiast wpływu na wystąpienie zarządzane SQL. Konfiguracja usługi DNS wystąpienie zarządzane SQL jest aktualizowana po wygaśnięciu dzierżawy DHCP lub po uaktualnieniu platformy, w zależności od tego, co się dzieje. **Przed utworzeniem pierwszego wystąpienia zarządzanego użytkownicy są zalecani do ustawienia konfiguracji DNS sieci wirtualnej.**

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](connectivity-architecture-overview.md).
