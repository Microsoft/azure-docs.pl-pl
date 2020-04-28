---
title: Jak skonfigurować pojedynczą bazę danych
description: Informacje dotyczące konfigurowania Azure SQL Database-pojedynczej bazy danych i zarządzania nią
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76027723"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Jak korzystać z pojedynczej bazy danych w Azure SQL Database

W tej sekcji znajdują się różne przewodniki, skrypty i wyjaśnienia, które mogą pomóc w zarządzaniu i konfigurowaniu pojedynczej bazy danych w Azure SQL Database

## <a name="migrate"></a>Migrate (Migracja)

- [Migrowanie do SQL Database](sql-database-single-database-migrate.md) — informacje na temat zalecanego procesu migracji i narzędzi do migracji do wystąpienia zarządzanego.
- Dowiedz się, jak [zarządzać usługą SQL Database po migracji](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Konfigurowanie funkcji

- [Skonfiguruj replikację transakcyjną](replication-to-sql-database.md) , aby replikować datę między bazami danych.
- [Skonfiguruj wykrywanie zagrożeń](sql-database-threat-detection.md) , aby umożliwić Azure SQL Database identyfikowanie podejrzanych działań, takich jak iniekcja SQL lub dostęp z podejrzanych lokalizacji.
- [Skonfiguruj Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started-portal.md) , aby chronić poufne dane.
- [Skonfiguruj przechowywanie kopii zapasowych](sql-database-long-term-backup-retention-configure.md) bazy danych, aby zachować kopie zapasowe na platformie Azure Blob Storage. Alternatywą jest [skonfigurowanie przechowywania kopii zapasowych za pomocą podejścia do magazynu platformy Azure (przestarzałe)](sql-database-long-term-backup-retention-configure-vault.md) .
- [Skonfiguruj replikację geograficzną](sql-database-geo-replication-portal.md) , aby zachować replikę bazy danych w innym regionie.
- [Skonfiguruj zabezpieczenia dla replik geograficznych](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorowanie i dostrajanie bazy danych

- [Włącz automatyczne dostrajanie](sql-database-automatic-tuning-enable.md) , aby umożliwić Azure SQL Database optymalizację wydajności obciążeń.
- [Włącz powiadomienia e-mail na potrzeby dostrajania automatycznego](sql-database-automatic-tuning-email-notifications.md) , aby uzyskać informacje o zaleceniach dostrajania.
- [Stosuj zalecenia dotyczące wydajności](sql-database-advisor-portal.md) i Optymalizuj bazę danych.
- [Utwórz alerty](sql-database-insights-alerts-portal.md) , aby otrzymywać powiadomienia z Azure SQL Database.
- [Rozwiązywanie problemów z](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) łącznością, jeśli zauważysz problemy z łącznością między aplikacjami i bazą danych. Resource Health można także użyć [do problemów z łącznością](sql-database-resource-health.md).
- [Zarządzaj miejscem w pliku](sql-database-file-space-management.md) , aby monitorować użycie magazynu w bazie danych programu.

## <a name="query-distributed-data"></a>Wykonywanie zapytań względem danych rozproszonych

- [Wykonywanie zapytań o dane partycjonowane w pionie](sql-database-elastic-query-getting-started-vertical.md) w wielu bazach danych.
- [Raport w warstwie danych skalowanych w poziomie](sql-database-elastic-query-horizontal-partitioning.md).
- [Zapytania między tabelami z różnymi schematami](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Zadania Elastic Database

- [Tworzenie i zarządzanie](elastic-jobs-powershell.md) Elastic Database zadań przy użyciu programu PowerShell.
- [Tworzenie i zarządzanie](elastic-jobs-tsql.md) Elastic Database zadania przy użyciu języka Transact-SQL.
- [Migruj ze starego zadania elastycznego](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Dzielenie bazy danych na fragmenty

- [Uaktualnij bibliotekę klienta Elastic Database](sql-database-elastic-scale-upgrade-client-library.md).
- [Utwórz aplikację podzielonej na fragmenty](sql-database-elastic-scale-get-started.md).
- [Zapytanie w poziomie podzielonej na fragmenty dane](sql-database-elastic-query-getting-started.md).
- Uruchom [zapytania fragmentu](sql-database-elastic-scale-multishard-querying.md).
- [Przenieś dane podzielonej na fragmenty](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Skonfiguruj zabezpieczenia](sql-database-elastic-scale-split-merge-security-configuration.md) w bazie danych fragmentów.
- [Dodaj fragmentu](sql-database-elastic-scale-add-a-shard.md) do bieżącego zestawu bazy danych fragmentów.
- [Rozwiąż problemy dotyczące mapy fragmentu](sql-database-elastic-database-recovery-manager.md).
- [Migrowanie bazy danych podzielonej na fragmenty DB](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Utwórz liczniki](sql-database-elastic-database-perf-counters.md).
- [Użyj programu Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) do wykonywania zapytań dotyczących danych podzielonej na fragmenty.
- [Użyj platformy Dapper Framework](sql-database-elastic-scale-working-with-dapper.md) , aby wykonywać zapytania dotyczące danych podzielonej na fragmenty.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat przewodników dla wystąpienia zarządzanego](sql-database-howto-managed-instance.md)
