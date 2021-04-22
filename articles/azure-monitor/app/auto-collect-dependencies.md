---
title: Azure Application Insights — automatyczne zbieranie zależności | Microsoft Docs
description: Application Insights automatycznie zbierać i wizualizować zależności
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: aa4d39ca8964e95ca787d236223e2b475a9597c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873833"
---
# <a name="dependency-auto-collection"></a>Automatyczne zbieranie zależności

Poniżej znajduje się obecnie obsługiwana lista wywołań zależności, które są automatycznie wykrywane jako zależności bez konieczności wprowadzania żadnych dodatkowych modyfikacji kodu aplikacji. Te zależności są wizualizowane w widokach mapy Application Insights [i](./app-map.md) [diagnostyki](./transaction-diagnostics.md) transakcji. Jeśli zależności nie ma na poniższej liście, nadal możesz śledzić ją ręcznie za pomocą wywołania [zależności śledzenia](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| Struktury aplikacji| Wersje |
| ------------------------|----------|
| ASP.NET formularzy internetowych | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Biblioteki komunikacyjne</b> |
| [HttpClient](https://dotnet.microsoft.com) | 4.5+, .NET Core 1.1+ |
| [Sqlclient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 — najnowsza stabilna wersja. (Zobacz uwaga poniżej).
| [Zestaw SDK klienta usługi EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienci magazynu</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Istnieje znany [problem ze starszymi](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) wersjami pakietu Microsoft.Data.SqlClient. Aby rozwiązać ten problem, zalecamy użycie programu 1.1.0 lub nowszego. Entity Framework Core nie musi być w najnowszej stabilnej wersji pakietu Microsoft.Data.SqlClient, dlatego zalecamy potwierdzenie, że używasz co najmniej wersji 1.1.0, aby uniknąć tego problemu.   


## <a name="java"></a>Java
| Serwery aplikacji | Wersje |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Struktury aplikacji </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Serwlet Java | 3.1+ |
| <b>Biblioteki komunikacyjne</b> |  |
| [Apache Http Client](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | Ponad 4,3<sup>†</sup> |
| <b>Klienci magazynu</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | Ponad 1<sup>†</sup> |
| [PostgreSQL (obsługa wersji beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | Ponad 1<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | Ponad 1<sup>†</sup> |
| <b>Rejestrowanie bibliotek</b> | |
| [Logback (Logback)](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Biblioteki metryk</b> |  |
| Jmx | 1.0+ |

> [!NOTE]
> *Z wyjątkiem obsługi programowania reaktywnego.
> <br>† Wymaga instalacji agenta [JVM.](./java-agent.md#install-the-application-insights-agent-for-java)

## <a name="nodejs"></a>Node.js

| Biblioteki komunikacyjne | Wersje |
| ------------------------|----------|
| [HTTP,](https://nodejs.org/api/http.html) [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Klienci magazynu</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb;](https://www.npmjs.com/package/mongodb) [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x–3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0–2.16.x |
| [PostgreSql;](https://www.npmjs.com/package/pg) | 6.x–7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x–2.x |
| <b>Biblioteki rejestrowania</b> | |
| [Konsoli](https://nodejs.org/api/console.html) | 0.10+ |
| [Łańka](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x–3.x |

## <a name="javascript"></a>JavaScript

| Biblioteki komunikacyjne | Wersje |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Wszystko |

## <a name="next-steps"></a>Następne kroki

- Skonfiguruj niestandardowe śledzenie zależności dla [.NET.](./asp-net-dependencies.md)
- Skonfiguruj niestandardowe śledzenie zależności dla języka [Java.](./java-agent.md)
- Skonfiguruj niestandardowe śledzenie zależności dla [zestawu OpenCensus Python.](./opencensus-python-dependency.md)
- [Pisanie niestandardowej telemetrii zależności](./api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych,](./data-model.md) Application Insights typy danych i model danych.
- Zapoznaj się [z platformami](./platforms.md) obsługiwanymi przez Application Insights.

