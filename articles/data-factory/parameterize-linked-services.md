---
title: Sparametryzuj połączone usługi w Azure Data Factory
description: Dowiedz się, jak Sparametryzuj połączone usługi w Azure Data Factory i przekazywać wartości dynamiczne w czasie wykonywania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 85689661e7f0d170cd88edde8985f46285e679c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987784"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Sparametryzuj połączone usługi w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Teraz można Sparametryzuj połączonej usługi i przekazywać wartości dynamiczne w czasie wykonywania. Na przykład jeśli chcesz nawiązać połączenie z różnymi bazami danych na tym samym logicznym serwerze SQL, możesz teraz Sparametryzuj nazwę bazy danych w definicji połączonej usługi. Zapobiega to konieczności tworzenia połączonej usługi dla każdej bazy danych na serwerze logicznym programu SQL Server. Można Sparametryzuj inne właściwości w definicji połączonej usługi, jak również na przykład *Nazwa użytkownika.*

Aby Sparametryzuj połączone usługi, można użyć interfejsu użytkownika Data Factory w Azure Portal lub interfejsie programowania.

> [!TIP]
> Nie zaleca się Sparametryzuj haseł ani wpisów tajnych. W zamian Przechowuj wszystkie parametry połączenia w Azure Key Vault i Sparametryzuj *nazwę klucza tajnego*.

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

W tej chwili parametryzacja połączonej usługi jest obsługiwany w interfejsie użytkownika Data Factory dla następujących magazynów danych. W przypadku wszystkich innych magazynów danych można Sparametryzuj połączoną usługę, wybierając ikonę **kodu** na karcie **połączenia** i używając edytora JSON.

- Amazon Redshift
- Azure Cosmos DB (interfejs API SQL)
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics (dawniej SQL DW)
- MySQL
- Oracle
- SQL Server

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

![Dodawanie zawartości dynamicznej do definicji połączonej usługi](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Tworzenie nowego parametru](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
