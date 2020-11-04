---
title: Zmień port PostgreSQL
description: Zmień port, na którym nasłuchuje Grupa serwerów PostgreSQL z funkcją Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328754"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Zmień port, na którym nasłuchuje Grupa serwerów 

Zmiana portu jest standardową operacją edycji grupy serwerów. Aby zmienić port, uruchom następujące polecenie:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Załóżmy na przykład, że nazwa grupy serwerów to _postgres01_ i że chcesz nasłuchiwać na porcie _866_. Uruchom następujące polecenie:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Sprawdź, czy port został zmieniony

Aby sprawdzić, czy port został zmieniony, uruchom następujące polecenie, aby wyświetlić konfigurację grupy serwerów:
```console
azdata arc postgres server show -n <server group name>
```

W danych wyjściowych tego polecenia Sprawdź numer portu wyświetlany dla elementu "Port" w sekcji "usługa" specyfikacji grupy serwerów.
Alternatywnie można sprawdzić w elemencie externalEndpoint w sekcji stan w specyfikacji grupy serwerów, do której należy adres IP, po którym następuje numer portu, który został skonfigurowany.

W przypadku kontynuowania powyższego przykładu należy uruchomić polecenie:
```console
azdata arc postgres server show -n postgres01
```

a zobaczysz port 866 tutaj:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
i tutaj.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Następne kroki
- Przeczytaj [, jak nawiązać połączenie z grupą serwerów](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Informacje o sposobie konfigurowania innych aspektów grupy serwerów można znaleźć w sekcji How-to\Manage\Configure & Scale w dokumentacji.
