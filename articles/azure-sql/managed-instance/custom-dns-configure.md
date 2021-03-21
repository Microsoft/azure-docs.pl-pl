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
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831505"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurowanie niestandardowego serwera DNS dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure. Istnieje kilka scenariuszy (na przykład usługa db mail, serwery połączone z innymi wystąpieniami programu SQL Server w chmurze lub w środowisku hybrydowym), które wymagają rozpoznania prywatnych nazw hostów z poziomu usługi SQL Managed Instance. W takim przypadku należy skonfigurować niestandardowy system DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane SQL używa tego samego systemu DNS do obsługi wewnętrznych, należy skonfigurować niestandardowy serwer DNS, aby można było rozpoznać nazwy domen publicznych.

> [!IMPORTANT]
> Zawsze używaj w pełni kwalifikowanej nazwy domeny (FQDN) dla serwera poczty, wystąpienia SQL Server i innych usług, nawet jeśli znajdują się w prywatnej strefie DNS. Na przykład, użyj `smtp.contoso.com` dla serwera poczty, ponieważ `smtp` nie zostanie poprawnie rozwiązany. Tworzenie połączonego serwera lub replikacji odwołującej się do SQL Server maszyn wirtualnych w ramach tej samej sieci wirtualnej wymaga również nazwy FQDN i domyślnego sufiksu DNS. Na przykład `SQLVM.internal.cloudapp.net`. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizacja serwerów DNS sieci wirtualnej nie będzie miała natychmiast wpływu na wystąpienie zarządzane SQL. Aby uzyskać więcej informacji, zobacz, [jak zsynchronizować ustawienie serwerów DNS sieci wirtualnej w klastrze wirtualnym usługi SQL Managed Instance](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](connectivity-architecture-overview.md).
