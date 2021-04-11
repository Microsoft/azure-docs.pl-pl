---
title: Przywracanie przykładowej bazy danych AdventureWorks do wystąpienia zarządzanego SQL
description: Przywracanie przykładowej bazy danych AdventureWorks do wystąpienia zarządzanego SQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641806"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>Przywracanie przykładowej bazy danych AdventureWorks do wystąpienia zarządzanego SQL — łuk platformy Azure

[AdventureWorks](/sql/samples/adventureworks-install-configure) to przykładowa baza danych zawierająca bazę danych OLTP, która jest często używana w samouczkach i przykładach. Jest ona dostarczana i obsługiwana przez firmę Microsoft w ramach [repozytorium SQL Server Samples](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)w witrynie GitHub.

W tym dokumencie opisano prosty proces pobierania przykładowej bazy danych AdventureWorks do wystąpienia zarządzanego SQL — Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Pobierz plik kopii zapasowej AdventureWorks

Pobierz plik kopii zapasowej AdventureWorks (. bak) do kontenera wystąpienia zarządzanego SQL. W tym przykładzie Użyj polecenia, `kubectl exec` Aby zdalnie wykonać polecenie wewnątrz kontenera wystąpienia zarządzanego SQL, aby pobrać plik BAK do kontenera. Pobierz ten plik z dowolnej lokalizacji dostępnej przez program, `wget` Jeśli masz inne pliki kopii zapasowej bazy danych, które mają być ściągane do kontenera wystąpienia zarządzanego SQL. Gdy znajduje się w kontenerze wystąpienia zarządzanego SQL, można go łatwo przywrócić przy użyciu standardowego `RESTORE DATABASE` języka T-SQL.

Uruchom polecenie podobne do tego, aby pobrać plik BAK, zastępując wartość nazwy pod nazwą i nazwą przestrzeni nazw przed jej uruchomieniem.
> [!NOTE]
>  Aby można było pobrać plik z serwisu GitHub, kontener musi mieć łączność z Internetem ponad 443.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Przykład

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>Przywracanie bazy danych AdventureWorks

Podobnie można uruchomić `kubectl` polecenie exec, aby użyć `sqlcmd` Narzędzia interfejsu wiersza polecenia, które znajduje się w kontenerze wystąpienia zarządzanego SQL do wykonania polecenia T-SQL w celu przywrócenia bazy danych.

Uruchom polecenie podobne do tego, aby przywrócić bazę danych. Przed uruchomieniem programu Zastąp wartość Nazwa pod, hasło i nazwę przestrzeni nazw.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Przykład

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
