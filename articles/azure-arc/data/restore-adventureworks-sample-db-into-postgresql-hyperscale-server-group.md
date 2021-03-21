---
title: Importowanie przykładowej bazy danych AdventureWorks do usługi Azure ARC z włączonym skalowaniem PostgreSQL
description: Przywracanie przykładowej bazy danych AdventureWorks do usługi Azure ARC z włączonym skalowaniem PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a9efa17fb782d5a913493907b66973272e4e0356
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441792"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Importowanie przykładowej bazy danych AdventureWorks do usługi Azure ARC z włączonym skalowaniem PostgreSQL

[AdventureWorks](/sql/samples/adventureworks-install-configure) to przykładowa baza danych zawierająca bazę danych OLTP używaną w samouczkach i przykłady. Jest ona udostępniana i obsługiwana przez firmę Microsoft w ramach [repozytorium SQL Server Samples](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)w witrynie GitHub.

Projekt "open source" został przekonwertowany na bazę danych AdventureWorks, aby był zgodny z funkcją Azure Arc PostgreSQL.
- [Oryginalny projekt](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Postępuj zgodnie z projektem, który wstępnie konwertuje pliki CSV, aby były zgodne z PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

W tym dokumencie opisano prosty proces uzyskiwania przykładowej bazy danych AdventureWorks zaimportowanej do grupy serwerów PostgreSQL.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Pobierz plik kopii zapasowej AdventureWorks

Pobierz plik AdventureWorks. SQL do kontenera grupy serwerów PostgreSQL. W tym przykładzie użyjemy `kubectl exec` polecenia, aby zdalnie wykonać polecenie w kontenerze grupy serwerów PostgreSQL, aby pobrać plik do kontenera. Ten plik można pobrać z dowolnej lokalizacji dostępnej przez program `curl` . Użyj tej samej metody, jeśli masz inną kopię zapasową bazy danych, którą chcesz ściągnąć w kontenerze grupy serwerów PostgreSQL. Gdy znajduje się w kontenerze grupy serwerów PostgreSQL, można łatwo utworzyć bazę danych, schemat i wypełnić dane.

Uruchom polecenie podobne do tego, aby pobrać pliki Zastąp wartość nazwy pod nazwą i nazwą przestrzeni nazw przed jej uruchomieniem:

> [!NOTE]
>  Aby można było pobrać plik z serwisu GitHub, kontener musi mieć łączność z Internetem ponad 443.

> [!NOTE]
>  Użyj pod nazwą węzła koordynatora grupy serwerów Postgres. Jego nazwa to <server group name> c-0 (na przykład postgres01c-0, gdzie c oznacza węzeł koordynatora).  Jeśli nie masz pewności co do nazwy pod, uruchom polecenie `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>Krok 2. zaimportowanie bazy danych AdventureWorks

Podobnie można uruchomić polecenie polecenia kubectl exec, aby użyć narzędzia interfejsu wiersza polecenia PSQL dołączonego do kontenerów grupy serwerów PostgreSQL w celu utworzenia i załadowania bazy danych.

Uruchom polecenie podobne do tego, aby utworzyć pustą bazę danych, najpierw zastępując wartość nazwy pod i nazwą przestrzeni nazw przed jej uruchomieniem.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Następnie uruchom polecenie podobne do tego, aby zaimportować bazę danych zastępującą wartość Nazwa pod i nazwę przestrzeni nazw przed jej uruchomieniem.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Uwaga: nie zobaczysz tak dużej wydajności, która działa w przypadku usługi Azure ARC z włączonym skalowaniem PostgreSQL, dopóki nie zaczniesz skalowaniu w poziomie i fragmentu/rozpowszechnisz dane/tabele w węzłach procesu roboczego w grupie serwerów PostgreSQL. Zapoznaj się z [sugerowanymi następnymi krokami](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Sugerowane następne kroki
- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania, aby dystrybuować dane między wieloma Azure Database for PostgreSQL węzłami PostgreSQL. :
    * [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
    * [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
    * [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
    * [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* W powyższych dokumentach Pomiń sekcje **Zaloguj się do Azure Portal**, & **Utwórz Azure Database for PostgreSQL-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

- [Skalowanie w poziomie grupy serwerów usługi Azure Database for PostgreSQL — hiperskala](scale-out-postgresql-hyperscale-server-group.md)
