---
title: Funkcja autokolekcje Application Insights platformy Azure | Microsoft Docs
description: Application Insights automatycznie zbierać i wizualizować zależności
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77665820"
---
# <a name="dependency-auto-collection"></a>Automatyczne zbieranie zależności

Poniżej znajduje się obecnie obsługiwana Lista wywołań zależności, które są automatycznie wykrywane jako zależności bez konieczności wprowadzania jakichkolwiek dodatkowych modyfikacji w kodzie aplikacji. Te zależności są wizualizowane w Application Insights [mapy aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) i [diagnostyki transakcji](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) . Jeśli zależność nie znajduje się na poniższej liście, nadal możesz ją ręcznie śledzić przy użyciu [wywołania zależności śledzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Struktury aplikacji| Wersje |
| ------------------------|----------|
| ASP.NET WebForms | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b>Biblioteki komunikacji</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [Zestaw SDK klienta EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Zestaw SDK klienta ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienci magazynu</b>|  |
| ADO.NET | 4.5 + |

## <a name="java"></a>Java
| Serwery aplikacji | Wersje |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Struktury aplikacji</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Serwletu Java | 3.1 + |
| <b>Biblioteki komunikacji</b> |  |
| [Klient Apache http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Klienci magazynu</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (obsługa wersji beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Biblioteki rejestrowania</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4J](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Biblioteki metryk</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> * Z wyjątkiem obsługi programu Reactive Programs.
> <br>† Wymaga instalacji [agenta JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Biblioteki komunikacji | Wersje |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0.10 + |
| <b>Klienci magazynu</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [MongoDB rdzeń](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 — 2.16. x |
| [PostgreSQL](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG — Pula](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Biblioteki rejestrowania</b> | |
| [konsoli](https://nodejs.org/api/console.html) | 0.10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Biblioteki komunikacji | Wersje |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Wszystkie |

## <a name="next-steps"></a>Następne kroki

- Skonfiguruj niestandardowe Śledzenie zależności dla [platformy .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Skonfiguruj niestandardowe Śledzenie zależności dla [języka Java](../../azure-monitor/app/java-agent.md).
- Skonfiguruj niestandardowe Śledzenie zależności dla języka [Python OpenCensus](../../azure-monitor/app/opencensus-python-dependency.md).
- [Napisz niestandardową telemetrię zależności](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
