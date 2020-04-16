---
title: Parametryzacja połączonych usług w usłudze Azure Data Factory
description: Dowiedz się, jak parametryzować połączone usługi w usłudze Azure Data Factory i przekazywać wartości dynamiczne w czasie wykonywania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8fa8603f2bee7e42db0f085d78117d61bd14ce5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419395"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametryzacja połączonych usług w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Teraz można sparametryzować połączoną usługę i przekazywać wartości dynamiczne w czasie wykonywania. Na przykład jeśli chcesz połączyć się z różnymi bazami danych na tym samym serwerze usługi Azure SQL Database, możesz teraz parametryzować nazwę bazy danych w definicji połączonej usługi. Zapobiega to konieczności tworzenia połączonej usługi dla każdej bazy danych na serwerze bazy danych SQL platformy Azure. Można parametryzować inne właściwości w definicji usługi połączonej, jak również — na przykład *nazwa użytkownika.*

Interfejs użytkownika fabryki danych w witrynie Azure portal lub interfejs programowania można sparametryzować połączone usługi.

> [!TIP]
> Zaleca się, aby nie parametryzować haseł lub wpisów tajnych. Zamiast tego przechowuj wszystkie parametry połączenia w usłudze Azure Key Vault i parametryzuj *nazwę tajną*.

Aby zapoznać się z siedmiominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

W tej chwili parametryzacja połączonej usługi jest obsługiwana w interfejsie użytkownika fabryki danych w witrynie Azure portal dla następujących magazynów danych. W przypadku wszystkich innych magazynów danych można sparametryzować połączoną usługę, wybierając ikonę **Kod** na karcie **Połączenia** i używając edytora JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Interfejs użytkownika usługi Data Factory

![Dodawanie zawartości dynamicznej do definicji usługi połączonej](media/parameterize-linked-services/parameterize-linked-services-image1.png)

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
