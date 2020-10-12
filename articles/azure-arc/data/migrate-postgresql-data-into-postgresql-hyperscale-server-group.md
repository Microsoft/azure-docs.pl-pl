---
title: Migrowanie danych z bazy danych PostgreSQL do grupy serwerów PostgreSQL w systemie Azure Arc
titleSuffix: Azure Arc enabled database services
description: Migrowanie danych z bazy danych PostgreSQL do grupy serwerów PostgreSQL w systemie Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 521fd61f18d6673e21c23dbca4cfc12d2ee4bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939643"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrowanie bazy danych PostgreSQL do usługi Azure ARC z włączoną grupą serwerów PostgreSQL do skalowania

W tym dokumencie opisano kroki umożliwiające uzyskanie istniejącej bazy danych PostgreSQL (jednej, która nie jest hostowana w usłudze Azure Arc Data Services) w grupie serwerów PostgreSQL z funkcją Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Zagadnienia do rozważenia

Usługa Azure ARC z włączoną obsługą PostgreSQL grupy serwerów to wersja społeczności PostgreSQL i jest uruchamiana z włączonym rozszerzeniem CitusData. Dlatego każde narzędzie, które działa na PostgreSQL poza usługą Azure ARC, powinno współpracować z grupą serwerów PostgreSQL z funkcją Azure Arc.


W związku z tym z zestawem narzędzi używanych dzisiaj do Postgres należy mieć możliwość:
1. Utwórz kopię zapasową bazy danych Postgres z wystąpienia hostowanego poza usługą Azure Arc
2. Przywróć je w grupie serwerów PostgreSQL z funkcją Azure Arc

Co pozostanie, aby to zrobić:
- Resetowanie parametrów serwera
- Zresetuj konteksty zabezpieczeń: Utwórz ponownie użytkowników, role i uprawnienia do resetowania...

Aby wykonać tę operację tworzenia kopii zapasowej/przywracania, można użyć dowolnego narzędzia, które może wykonywać operacje tworzenia kopii zapasowej/przywracania dla Postgres. Na przykład:
- Azure Data Studio i jego rozszerzenie Postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Przykład
Ilustrujemy te kroki przy użyciu `pgAdmin` Narzędzia.
Weź pod uwagę następujące ustawienia:
- **Zewnętrz**  
    Serwer Postgres działający lokalnie na serwerze bez systemu operacyjnego i o nazwie JEANYDSRV. Jest to wersja 12 i hostuje bazę danych o nazwie MyOnPremPostgresDB, która ma jedną tabelę T1 z 1 wierszem :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="Migrowanie źródła":::

- **Punktu**  
    Serwer Postgres działający w środowisku usługi Azure Arc i o nazwie postgres01. Jest to wersja 12. Nie ma żadnej bazy danych z wyjątkiem standardowej bazy danych Postgres.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrowanie źródła":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Utwórz kopię zapasową źródłowej bazy danych lokalnie

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrowanie źródła":::

Skonfiguruj:
1. Nadaj mu nazwę pliku: **MySourceBackup**
2. Ustaw format na **niestandardową** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migrowanie źródła":::

Tworzenie kopii zapasowej zostało ukończone pomyślnie:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrowanie źródła":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Utwórz pustą bazę danych w systemie docelowym w grupie serwerów PostgreSQL na platformie Azure z włączonym łukiem

> [!NOTE]
> Aby zarejestrować wystąpienie Postgres w `pgAdmin` narzędziu, należy używać publicznego adresu IP wystąpienia w klastrze Kubernetes i odpowiednio ustawiać port i kontekst zabezpieczeń. Te szczegóły znajdują się w `psql` wierszu punktu końcowego po uruchomieniu następującego polecenia:

```console
azdata arc postgres endpoint list -n postgres01
```
Zwraca dane wyjściowe, takie jak:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Nadaj nazwę docelowej bazie danych **RESTORED_MyOnPremPostgresDB**  
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrowanie-miejsce docelowe — DB — tworzenie"lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Przywracanie bazy danych w konfiguracji Arc
:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migrowanie źródła":::

Skonfiguruj przywracanie:
1. Wskaż plik zawierający kopię zapasową do przywrócenia: **MySourceBackup**
2. Zachowaj format ustawiony na **niestandardowy lub** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrowanie źródła":::

3. Kliknij przycisk **Przywróć**.  

   Przywracanie zakończyło się pomyślnie.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrowanie źródła":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Sprawdź, czy baza danych została pomyślnie przywrócona w grupie serwerów PostgreSQL na platformie Azure Arc

Użyj jednej z następujących metod:

**Z `pgAdmin` :**  

Rozwiń wystąpienie Postgres hostowane w konfiguracji usługi Azure Arc. Zostanie wyświetlona tabela w przywróconej bazie danych, a po wybraniu danych zostanie wyświetlony ten sam wiersz, co w wystąpieniu lokalnym:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrowanie źródła"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. Z parametrów `psql` połączenia użyj parametru, `-d` Aby wskazać nazwę bazy danych. Za pomocą poniższego polecenia zostanie wyświetlony monit o podanie hasła:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` łączy się z.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Wybierz tabelę, aby wyświetlić dane przywrócone z lokalnego wystąpienia Postgres:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Nie zobaczysz tak dużej wydajności, która działa w przypadku usługi Azure ARC z włączonym skalowaniem PostgreSQL, dopóki nie zaczniesz skalowanie i fragmentu/dystrybuujesz dane w węzłach procesu roboczego grupy serwerów PostgreSQL. Zobacz [następne kroki](#next-steps).
>
> - Obecnie nie jest to możliwe w przypadku "dołączania do usługi Azure ARC" do istniejącego wystąpienia Postgres, które będzie działać lokalnie lub w innej chmurze. Innymi słowy, nie jest możliwe zainstalowanie niektórych rodzajów "usługi Azure Arc Agent" na istniejącym wystąpieniu Postgres, aby było możliwe skonfigurowanie Postgres przez łuk systemu Azure. Zamiast tego należy utworzyć nowe wystąpienie Postgres i przesłać do niego dane. Możesz użyć techniki pokazanej powyżej, aby to zrobić, lub możesz użyć dowolnego wybranego narzędzia ETL.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania w celu rozproszenia danych między wieloma Azure Database for PostgreSQL węzłami PostgreSQL.
    * [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
    * [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
    * [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
    * [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * W tych dokumentach Pomiń sekcje Zaloguj się **do Azure Portal**i **Utwórz bazę danych Azure Database for Postgres-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

- [Skalowanie w poziomie grupy serwerów usługi Azure Database for PostgreSQL — hiperskala](scale-out-postgresql-hyperscale-server-group.md)
