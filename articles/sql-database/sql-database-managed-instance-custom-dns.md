---
title: Niestandardowe wystąpienie DNS wystąpienia zarządzanego
description: W tym temacie opisano opcje konfiguracji niestandardowego systemu DNS z wystąpieniem zarządzanym Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 1b27d316114b84434a84fc8226eba9328a803dc8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774382"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurowanie niestandardowego systemu nazw domen dla wystąpienia zarządzanego usługi Azure SQL Database

Wystąpienie zarządzane Azure SQL Database musi być wdrożone w ramach [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure. Istnieje kilka scenariuszy (na przykład poczta DB, połączone serwery do innych wystąpień SQL w środowisku chmury lub hybrydowej), które wymagają rozpoznawania prywatnych nazw hostów z wystąpienia zarządzanego. W takim przypadku należy skonfigurować niestandardowy serwer DNS na platformie Azure. 

Ponieważ wystąpienie zarządzane używa tego samego systemu DNS do obsługi wewnętrznych elementów roboczych, należy skonfigurować niestandardowy serwer DNS, aby można było rozpoznać nazwy domen publicznych.

> [!IMPORTANT]
> Zawsze używaj w pełni kwalifikowanej nazwy domeny (FQDN) dla serwera poczty, wystąpienia SQL Server i innych usług, nawet jeśli znajdują się w prywatnej strefie DNS. Na przykład, użyj `smtp.contoso.com` dla serwera poczty, ponieważ `smtp` nie zostanie poprawnie rozwiązany. Tworzenie połączonego serwera lub replikacji odwołującej się do maszyn wirtualnych SQL w tej samej sieci wirtualnej wymaga również nazwy FQDN i domyślnego sufiksu DNS. Na przykład `SQLVM.internal.cloudapp.net`. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizowanie serwerów DNS sieci wirtualnej nie będzie miało wpływu na wystąpienie zarządzane. Konfiguracja usługi DNS wystąpienia zarządzanego jest aktualizowana po wygaśnięciu dzierżawy DHCP lub po uaktualnieniu platformy, w zależności od tego, co się dzieje. **Przed utworzeniem pierwszego wystąpienia zarządzanego użytkownicy są zalecani do ustawienia konfiguracji DNS sieci wirtualnej.**

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md)
- Samouczek pokazujący, jak utworzyć nowe wystąpienie zarządzane, można znaleźć w temacie [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać informacje o konfigurowaniu sieci wirtualnej dla wystąpienia zarządzanego, zobacz [Konfiguracja sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md)
