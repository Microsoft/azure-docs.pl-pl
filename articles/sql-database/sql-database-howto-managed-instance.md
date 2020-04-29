---
title: Jak skonfigurować wystąpienie zarządzane
description: Informacje o konfigurowaniu Azure SQL Database wystąpienia zarządzanego i zarządzania nim.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: b56375388f6293d27bcd2f2548d8b20205a92b15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80638031"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Jak używać wystąpienia zarządzanego w Azure SQL Database

W tym artykule znajdują się różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu wystąpienia zarządzanego.

## <a name="migration"></a>Migracja

- [Migrowanie do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md) — informacje na temat zalecanego procesu migracji i narzędzi do migracji do wystąpienia zarządzanego.

- [Migruj certyfikat TDE do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md) — Jeśli baza danych SQL Server jest chroniona za pomocą funkcji transparent Data Encryption (TDE), należy przeprowadzić migrację certyfikatu, którego wystąpienie zarządzane może użyć do odszyfrowania kopii zapasowej, która ma zostać przywrócona na platformie Azure.

## <a name="network-configuration"></a>Konfiguracja sieci

- [Określanie rozmiaru podsieci wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) — wystąpienie zarządzane jest umieszczane w podsieci dedykowanych, których rozmiar nie może zostać zmieniony po dodaniu zasobów wewnątrz. W związku z tym należy obliczyć zakres adresów IP, który będzie wymagany dla podsieci, w zależności od liczby i typów wystąpień, które mają zostać wdrożone w podsieci.
- [Tworzenie nowej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-create-vnet-subnet.md) — sieci wirtualnej platformy Azure i podsieci, w której chcesz wdrożyć zarządzane wystąpienia, muszą być skonfigurowane zgodnie z [wymaganiami sieci opisanymi tutaj](sql-database-managed-instance-connectivity-architecture.md#network-requirements). W tym przewodniku można znaleźć najprostszy sposób tworzenia nowej sieci wirtualnej i podsieci prawidłowo skonfigurowanej dla wystąpień zarządzanych.
- [Konfigurowanie istniejącej sieci wirtualnej i podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md) — w celu skonfigurowania istniejącej sieci wirtualnej i podsieci w celu wdrożenia zarządzanych wystąpień wewnątrz programu można znaleźć skrypt sprawdzający [wymagania sieci](sql-database-managed-instance-connectivity-architecture.md#network-requirements) i skonfigurować podsieć zgodnie z wymaganiami.
- [Konfigurowanie niestandardowego systemu DNS](sql-database-managed-instance-custom-dns.md) — Jeśli chcesz uzyskać dostęp do zasobów zewnętrznych w domenach niestandardowych z wystąpienia zarządzanego za pośrednictwem połączonego serwera profilów poczty bazy danych, musisz skonfigurować niestandardowy serwer DNS.
- [Synchronizacja konfiguracji sieci](sql-database-managed-instance-sync-network-configuration.md) — może to być spowodowane tym, że mimo że [aplikacja została zintegrowana z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), możesz&#39;nawiązać połączenie z wystąpieniem zarządzanym. Jednym z nich może być próba odświeżenia konfiguracji sieci dla planu usługi.
- [Znajdź adres IP punktu końcowego zarządzania](sql-database-managed-instance-find-management-endpoint-ip-address.md) — wystąpienie zarządzane używa publicznego punktu końcowego do celów zarządzania. Adres IP punktu końcowego zarządzania można określić przy użyciu skryptu opisanego tutaj.
- [Weryfikowanie wbudowanej ochrony zapory](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) — wystąpienie zarządzane jest chronione za pomocą wbudowanej zapory, która zezwala na ruch tylko na niezbędnych portach. Możesz sprawdzić i sprawdzić wbudowane reguły zapory za pomocą skryptu opisanego w tym przewodniku.
- [Łączenie aplikacji](sql-database-managed-instance-connect-app.md) — wystąpienie zarządzane jest umieszczane w prywatnej sieci wirtualnej platformy Azure z prywatnym adresem IP. Poznaj różne wzorce łączenia aplikacji z wystąpieniem zarządzanym.

## <a name="feature-configuration"></a>Konfiguracja funkcji

- [Replikacja transakcyjna](replication-with-sql-database-managed-instance.md) umożliwia replikowanie danych między wystąpieniami zarządzanymi lub z SQL Server lokalnych do wystąpienia zarządzanego i na odwrót. Więcej informacji o sposobie używania i konfigurowania replikacji transakcji w tym przewodniku.
- [Konfigurowanie wykrywania zagrożeń](sql-database-managed-instance-threat-detection.md) — [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) to wbudowana funkcja Azure SQL Database, która wykrywa różne potencjalne ataki, takie jak iniekcja SQL lub dostęp z podejrzanych lokalizacji. W tym przewodniku można dowiedzieć się, jak włączyć i skonfigurować [wykrywanie zagrożeń](sql-database-threat-detection-overview.md) dla wystąpienia zarządzanego.
- [Tworzenie alertów](sql-database-managed-instance-alerts.md) umożliwia Konfigurowanie alertów dotyczących monitorowanych metryk, takich jak użycie procesora CPU, użycie miejsca do magazynowania, liczba operacji we/wy na sekundę oraz inne dla wystąpienia zarządzanego. W tym przewodniku dowiesz się, jak włączyć i skonfigurować alerty dla wystąpienia zarządzanego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat przewodników dla pojedynczych baz danych](sql-database-howto-single-database.md)
