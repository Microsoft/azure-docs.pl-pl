---
title: Uzyskaj punkty końcowe połączenia i ciągi połączeń formularzy dla grupy serwerów PostgreSQL na potrzeby skalowania w poziomie
titleSuffix: Azure Arc enabled data services
description: Uzyskaj punkty końcowe połączenia i ciągi połączeń formularzy dla grupy serwerów PostgreSQL na potrzeby skalowania w poziomie
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4087d618209ab4db46f89ef4e6db7ac87ca4cf57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331016"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Uzyskaj punkty końcowe połączenia i ciągi połączeń formularzy dla grupy serwerów PostgreSQL na potrzeby skalowania w poziomie

W tym artykule wyjaśniono, jak można pobrać punkty końcowe połączenia dla grupy serwerów oraz sposób tworzenia parametrów połączenia, które będą używane z aplikacjami i/lub narzędziami.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Pobierz punkty końcowe połączenia:

### <a name="from-cli-with-azdata"></a>Z interfejsu wiersza polecenia z azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Połącz się z kontrolerem danych ARC:
- Jeśli masz już otwartą sesję na hoście kontrolera danych ARC: Uruchom następujące polecenie:
```console
azdata login
```

- Jeśli nie masz otwartej sesji na hoście kontrolera danych ARC: Uruchom następujące polecenie 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. Pokaż punkty końcowe połączenia
Uruchom następujące polecenie:
```console
azdata arc postgres endpoint list -n <server group name>
```
Zwraca dane wyjściowe podobne do:
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
Użyj następujących punktów końcowych:
- Formularz połączenia i nawiązywanie połączenia z narzędziami lub aplikacjami klienckimi
- Uzyskiwanie dostępu do pulpitów nawigacyjnych Grafana i Kibana z przeglądarki

Na przykład można użyć punktu końcowego o nazwie _PostgreSQL wystąpienie_ do nawiązania połączenia z PSQL z grupą serwerów. Na przykład:
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Hasło użytkownika _Postgres_ wskazane w punkcie końcowym o nazwie "_PostgreSQL instance_" to hasło wybrane podczas wdrażania grupy serwerów.
> - Informacje o azdata: dzierżawa skojarzona z połączeniem trwa około 10 godzin. Po tym celu należy ponownie nawiązać połączenie. Jeśli Twoja Dzierżawa wygasła, podczas próby wykonania polecenia z azdata (innego niż azdata login) zostanie wyświetlony następujący komunikat o błędzie: _błąd: (401)_ 
>  _Przyczyna: nieautoryzowane_ 
>  _nagłówki odpowiedzi http: HTTPHeaderDict ({"date": "Sun, 06 SEP 2020 16:58:38 GMT", "Content-Length": "0", "www-Authenticate": "_ 
>  _podstawowe obszary ="_ wymagane poświadczenia logowania ", błąd okaziciela =" invalid_token ", error_description =" token wygasł ""}) _ w takiej sytuacji należy ponownie nawiązać połączenie z usługą azdata, jak wyjaśniono powyżej.

## <a name="from-cli-with-kubectl"></a>Z interfejsu wiersza polecenia z polecenia kubectl
- Jeśli Grupa serwerów ma Postgres w wersji 12 (domyślnie), należy wykonać następujące polecenie:
```console
kubectl get postgresql-12/<server group name>
```
- Jeśli Grupa serwerów ma Postgres w wersji 11, należy wykonać następujące polecenie:
```console
kubectl get postgresql-11/<server group name>
```

Te polecenia spowodują wygenerowanie danych wyjściowych, takich jak przedstawione poniżej. Te informacje służą do tworzenia parametrów połączenia:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Parametry połączenia formularza:
Skorzystaj z poniższej tabeli szablonów ciągów połączeń dla grupy serwerów. Następnie można skopiować/wkleić i dostosować je w razie konieczności:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>PSQL

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>Aplikacja internetowa

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>Następne kroki
- Przeczytaj o [skalowaniu (Dodawanie węzłów procesu roboczego)](scale-out-postgresql-hyperscale-server-group.md) do grupy serwerów
- Przeczytaj o [skalowaniu w górę lub w dół (Zwiększanie/zmniejszanie ilości pamięci/rdzeni wirtualnych)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) grupy serwerów


