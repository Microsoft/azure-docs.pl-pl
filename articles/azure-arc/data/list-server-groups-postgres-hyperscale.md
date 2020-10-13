---
title: Wyświetl listę grup serwerów PostgreSQL z funkcją Azure ARC, które zostały utworzone w kontrolerze danych usługi Azure Arc
description: Wyświetl listę grup serwerów PostgreSQL z funkcją Azure ARC, które zostały utworzone w kontrolerze danych usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940790"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Wyświetl listę grup serwerów PostgreSQL z funkcją Azure ARC, które zostały utworzone w kontrolerze danych usługi Azure Arc

W tym artykule wyjaśniono, jak można pobrać listę grup serwerów utworzonych w kontrolerze danych Arc.

Aby pobrać tę listę, należy użyć jednej z następujących metod po nawiązaniu połączenia z kontrolerem danych ARC:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Z interfejsu wiersza polecenia z azdata
Ogólny format polecenia to:
```console
azdata arc postgres server list
```

Zwróci dane wyjściowe podobne do:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Aby uzyskać więcej informacji na temat parametrów dostępnych dla tego polecenia, uruchom polecenie:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Z interfejsu wiersza polecenia z polecenia kubectl
Uruchom jedno z następujących poleceń.

**Aby wyświetlić listę grup serwerów bez względu na wersję programu Postgres, uruchom polecenie:**
```console
kubectl get postgresqls
```
Zwróci dane wyjściowe podobne do:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Aby wyświetlić listę grup serwerów z określoną wersją Postgres, uruchom polecenie:**
```console
kubectl get postgresql-12
```

Aby wyświetlić listę grup serwerów z wersją 11 Postgres, Zastąp _PostgreSQL-12_ _PostgreSQL-11_.

## <a name="next-steps"></a>Następne kroki:

* [Zapoznaj się z artykułem jak uzyskać punkty końcowe połączenia i utworzyć parametry połączenia w celu nawiązania połączenia z grupą serwerów](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Zapoznaj się z artykułem dotyczącym wyświetlania konfiguracji grupy serwerów z PostgreSQLą w ramach usługi Azure Arc](show-configuration-postgresql-hyperscale-server-group.md)
