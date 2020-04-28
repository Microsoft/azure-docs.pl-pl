---
title: Optymalizacja kolekcji statystyk zapytania — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można zoptymalizować zbieranie danych statystycznych zapytania na Azure Database for PostgreSQL-pojedynczym serwerze
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770173"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optymalizowanie zbierania statystyk zapytania na serwerze Azure Database for PostgreSQL-pojedynczym
W tym artykule opisano, jak zoptymalizować zbieranie statystyk zapytania na serwerze Azure Database for PostgreSQL.

## <a name="use-pg_stats_statements"></a>Użyj pg_stats_statements
**Pg_stat_statements** to rozszerzenie PostgreSQL, które jest domyślnie włączone w Azure Database for PostgreSQL. Rozszerzenie zapewnia metodę śledzenia statystyk wykonywania dla wszystkich instrukcji SQL wykonywanych przez serwer. Ten moduł jest podłączany do każdego wykonywania zapytania i ma nieuproszczony koszt wydajności. Włączenie **pg_stat_statements** wymusza zapisywanie tekstu zapytania do plików na dysku.

Jeśli masz unikatowe zapytania z długim tekstem zapytania lub nie masz aktywnego monitorowania **pg_stat_statements**, wyłącz **pg_stat_statements** w celu uzyskania najlepszej wydajności. Aby to zrobić, Zmień ustawienie na `pg_stat_statements.track = NONE`.

W przypadku niektórych obciążeń klientów zaobserwowano zwiększenie wydajności do 50%, gdy **pg_stat_statements** jest wyłączone. Przed wyłączeniem pg_stat_statements nie jest możliwe Rozwiązywanie problemów z wydajnością.

Aby ustawić `pg_stat_statements.track = NONE`:

- W Azure Portal przejdź do [strony zarządzania zasobami PostgreSQL i wybierz blok parametry serwera](howto-configure-server-parameters-using-portal.md).

  ![Blok parametrów serwera PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Użyj [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md) AZ Postgres Server Configuration `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`ustawionym na.

## <a name="use-the-query-store"></a>Korzystanie z magazynu zapytań 
Funkcja [magazynu zapytań](concepts-query-store.md) w Azure Database for PostgreSQL zapewnia bardziej efektywną metodę śledzenia statystyk zapytań. Firma Microsoft zaleca korzystanie z tej funkcji jako alternatywy w odniesieniu do *pg_stats_statements*. 

## <a name="next-steps"></a>Następne kroki
Rozważ ustawienie `pg_stat_statements.track = NONE` w [Azure Portal](howto-configure-server-parameters-using-portal.md) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

Aby uzyskać więcej informacji, zobacz: 
- [Scenariusze użycia magazynu zapytań](concepts-query-store-scenarios.md) 
- [Najlepsze rozwiązania dotyczące magazynu zapytań](concepts-query-store-best-practices.md) 
