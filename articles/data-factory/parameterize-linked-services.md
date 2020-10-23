---
title: Sparametryzuj połączone usługi w Azure Data Factory
description: Dowiedz się, jak Sparametryzuj połączone usługi w Azure Data Factory i przekazywać wartości dynamiczne w czasie wykonywania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: e6c00a0d2b6ff8bbb4ba9e51110e995e93d6b558
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426965"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Sparametryzuj połączone usługi w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Teraz można Sparametryzuj połączonej usługi i przekazywać wartości dynamiczne w czasie wykonywania. Na przykład jeśli chcesz nawiązać połączenie z różnymi bazami danych na tym samym logicznym serwerze SQL, możesz teraz Sparametryzuj nazwę bazy danych w definicji połączonej usługi. Zapobiega to konieczności tworzenia połączonej usługi dla każdej bazy danych na serwerze logicznym programu SQL Server. Można Sparametryzuj inne właściwości w definicji połączonej usługi, jak również na przykład *Nazwa użytkownika.*

Aby Sparametryzuj połączone usługi, można użyć interfejsu użytkownika Data Factory w Azure Portal lub interfejsie programowania.

> [!TIP]
> Nie zaleca się Sparametryzuj haseł ani wpisów tajnych. W zamian Przechowuj wszystkie parametry połączenia w Azure Key Vault i Sparametryzuj *nazwę klucza tajnego*.

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Obsługiwane typy połączonych usług

Możesz Sparametryzuj dowolny typ połączonej usługi.
Podczas tworzenia połączonej usługi w interfejsie użytkownika Data Factory udostępnia wbudowane środowisko parametryzacja dla następujących typów połączonych usług. W bloku Tworzenie/edytowanie połączonej usługi można znaleźć opcje nowych parametrów i dodać zawartość dynamiczną.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (interfejs API SQL)
- Azure Database for MySQL
- Azure Databricks
- Azure SQL Database
- Wystąpienie zarządzane Azure SQL
- Azure Synapse Analytics (dawniej SQL DW)
- MySQL
- Oracle
- SQL Server
- Ogólne HTTP
- Ogólne REST

W przypadku innych typów połączonych usług, które nie znajdują się na liście, możesz Sparametryzuj połączoną usługę, edytując kod JSON w interfejsie użytkownika:

- W obszarze Tworzenie/edytowanie połączonej usługi — > rozwiń pozycję "Zaawansowane" w > polu wyboru "Określ zawartość dynamiczną w formacie JSON"-> Określ ładunek JSON połączonej usługi. 
- Lub po utworzeniu połączonej usługi bez parametryzacja w [centrum zarządzania](author-visually.md#management-hub) — > połączone usługi — > znaleźć określoną połączoną usługę-> kliknij "kod" (przycisk " {} "), aby edytować plik JSON. 

Zapoznaj się z [przykładem JSON](#json) , aby dodać ` parameters` sekcję do definiowania parametrów i odwoływania się do parametru przy użyciu ` @{linkedService().paraName} ` .

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
