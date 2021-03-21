---
title: Połączone usługi w usłudze Azure Data Factory
description: Dowiedz się więcej o połączonych usługach w Data Factory. Połączone usługi łączą magazyny obliczeniowe z danymi do fabryki danych.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 768af9e8568a3842b642a3ebd447d9df8064e840
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389930"
---
# <a name="linked-services-in-azure-data-factory"></a>Połączone usługi w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jakie połączone usługi są zdefiniowane w formacie JSON i jak są używane w potokach Azure Data Factory.

Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](introduction.md) przegląd.

## <a name="overview"></a>Omówienie

Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku określają akcje do wykonania na danych. Można na przykład użyć działania kopiowania w celu skopiowania danych z SQL Server do magazynu obiektów blob platformy Azure. Następnie możesz użyć działania programu Hive, które uruchamia skrypt programu Hive w klastrze usługi Azure HDInsight, aby przetwarzać dane z magazynu obiektów BLOB w celu utworzenia danych wyjściowych. Na koniec możesz użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure Synapse Analytics, na podstawie których są tworzone rozwiązania do raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania](concepts-pipelines-activities.md) w Azure Data Factory.

Teraz **zestaw** danych to nazwany widok zawierający dane, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe.

Przed utworzeniem zestawu danych należy utworzyć **połączoną usługę** , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zastanów się to w ten sposób; DataSet reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, które zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do SQL Database, należy utworzyć dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: DataSet usługi Azure BLOB (który odwołuje się do połączonej usługi Azure Storage) i zestawu danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Azure Storage i Azure SQL Database zawierają parametry połączenia, które Data Factory używane w środowisku uruchomieniowym do nawiązywania połączenia z usługą Azure Storage i Azure SQL Database. Zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB. Zestaw danych tabeli SQL Azure określa tabelę SQL w SQL Database, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>KOD JSON połączonej usługi

Połączona usługa w Data Factory jest definiowana w formacie JSON w następujący sposób:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa połączonej usługi. Zobacz [reguły nazewnictwa Azure Data Factory](naming-rules.md). |  Tak |
typ | Typ połączonej usługi. Na przykład: AzureBlobStorage (magazyn danych) lub AzureBatch (COMPUTE). Zobacz opis typeProperties. | Tak |
typeProperties | Właściwości typu są różne dla poszczególnych magazynów danych lub obliczeń. <br/><br/> W przypadku obsługiwanych typów magazynów danych i ich właściwości typów Zobacz artykuł [Omówienie łącznika](copy-activity-overview.md#supported-data-stores-and-formats) . Przejdź do artykułu łącznika magazynu danych, aby dowiedzieć się więcej o właściwościach typu specyficznych dla magazynu danych. <br/><br/> W przypadku obsługiwanych typów obliczeniowych i ich właściwości typu zobacz [połączone usługi obliczeniowe](compute-linked-services.md). | Tak |
Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie

## <a name="linked-service-example"></a>Przykład połączonej usługi

Następująca połączona usługa to połączona usługa Azure Blob Storage. Należy zauważyć, że typ jest ustawiony na usługę Azure Blob Storage. Właściwości typu dla połączonej usługi Azure Blob Storage obejmują parametry połączenia. Usługa Data Factory używa tych parametrów połączenia do nawiązywania połączenia z magazynem danych w czasie wykonywania.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Tworzenie połączonych usług

Połączone usługi można tworzyć w Azure Data Factory środowisku użytkownika za pośrednictwem [centrum zarządzania](author-management-hub.md) oraz wszelkich działań, zestawów danych lub przepływów, które odwołują się do nich.

Połączone usługi można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [interfejsów API platformy .NET](quickstart-create-data-factory-dot-net.md), [programu PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), szablonu Azure Resource Manager i Azure Portal.


## <a name="data-store-linked-services"></a>Połączone usługi magazynu danych

Listę magazynów danych obsługiwanych przez Data Factory można znaleźć w artykule [Omówienie łączników](copy-activity-overview.md#supported-data-stores-and-formats) . Kliknij magazyn danych, aby poznać obsługiwane właściwości połączenia.

## <a name="compute-linked-services"></a>Usługi połączone usługi Compute

[Środowiska obliczeniowe](compute-linked-services.md) referencyjne obsługują szczegółowe informacje o różnych środowiskach obliczeniowych, z którymi można nawiązać połączenie z fabryki danych oraz w różnych konfiguracjach.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawu SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu Azure Portal](quickstart-create-data-factory-portal.md)
