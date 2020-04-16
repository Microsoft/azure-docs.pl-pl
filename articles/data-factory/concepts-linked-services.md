---
title: Połączone usługi w usłudze Azure Data Factory
description: Dowiedz się więcej o połączonych usługach w fabryce danych. Połączone usługi łączą zasoby obliczeniowe/magazyny danych z fabryką danych.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: a6002ed173ca5358df4257f4c8b41c88bcf60ad8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418383"
---
# <a name="linked-services-in-azure-data-factory"></a>Połączone usługi w usłudze Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jakie są połączone usługi, jak są definiowane w formacie JSON i jak są używane w potokach usługi Azure Data Factory.

Jeśli jesteś nowy w fabryce danych, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md) dla przeglądu.

## <a name="overview"></a>Omówienie

Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logiczne grupowanie **działań,** które razem wykonać zadanie. Działania w potoku definiują akcje do wykonania na danych. Na przykład można użyć działania kopiowania do kopiowania danych z lokalnego programu SQL Server do magazynu obiektów Blob platformy Azure. Następnie można użyć działania gałęzi, który uruchamia skrypt hive w klastrze usługi Azure HDInsight do przetwarzania danych z magazynu obiektów Blob do tworzenia danych wyjściowych. Na koniec można użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure SQL Data Warehouse, na którym są tworzone rozwiązania raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania](concepts-pipelines-activities.md) w usłudze Azure Data Factory.

Teraz **zestaw danych** jest nazwany widok danych, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w **działaniach** jako dane wejściowe i wyjściowe.

Przed utworzeniem zestawu danych należy utworzyć **połączony serwis,** aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Pomyśl o tym w ten sposób; zestaw danych reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład usługa połączona usługi Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, który zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do bazy danych SQL, należy utworzyć dwie połączone usługi: Usługi Azure Storage i Azure SQL Database. Następnie utwórz dwa zestawy danych: zestaw danych obiektów Blob platformy Azure (który odwołuje się do usługi połączonej usługi Azure Storage) i zestaw danych tabeli SQL platformy Azure (który odwołuje się do połączonej usługi Azure SQL Database). Usługi połączone w usłudze Azure Storage i Azure SQL Database zawierają parametry połączenia używane przez usługę Data Factory w czasie wykonywania do łączenia się odpowiednio z usługą Azure Storage i azure SQL Database. Zestaw danych obiektów Blob platformy Azure określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów Blob. Zestaw danych tabeli SQL platformy Azure określa tabelę SQL w bazie danych SQL, do której mają być kopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, aktywnością, zestawem danych i usługą połączony w usłudze Data Factory:

![Relacja między rurociągiem, aktywnością, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Usługa połączona JSON

Połączona usługa w fabryce danych jest zdefiniowana w formacie JSON w następujący sposób:

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

W poniższej tabeli opisano właściwości w powyższym JSON:

Właściwość | Opis | Wymagany |
-------- | ----------- | -------- |
name | Nazwa połączonej usługi. Zobacz [Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Tak |
type | Typ połączonej usługi. Na przykład: Usługa Azure Storage (magazyn danych) lub AzureBatch (obliczenia). Zobacz opis właściwości typuProperties. | Tak |
typeProperties | Właściwości typu są różne dla każdego magazynu danych lub obliczeń. <br/><br/> W przypadku obsługiwanych typów magazynu danych i ich właściwości typu zobacz tabelę [typów zestawów danych](concepts-datasets-linked-services.md#dataset-type) w tym artykule. Przejdź do artykułu łącznika magazynu danych, aby dowiedzieć się więcej o właściwościach typu specyficznych dla magazynu danych. <br/><br/> Aby zapoznać się z obsługiwanymi typami obliczeń i ich właściwościami, zobacz [Obliczanie połączonych usług](compute-linked-services.md). | Tak |
connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie

## <a name="linked-service-example"></a>Przykład usługi połączonej

Poniższa usługa połączona jest usługą połączony usługi Azure Storage. Należy zauważyć, że typ jest ustawiony na usługę Azure Storage. Właściwości typu usługi połączonej usługi Azure Storage obejmują parametry połączenia. Usługa Data Factory używa tego ciągu połączenia do łączenia się z magazynem danych w czasie wykonywania.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

Połączone usługi można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Szablon usługi Azure Resource Manager i Witryna Azure portal

## <a name="data-store-linked-services"></a>Usługi połączone z magazynem danych

Listę magazynów danych obsługiwanych przez fabrykę danych z artykułu [Omówienie łącznika.](copy-activity-overview.md#supported-data-stores-and-formats) Kliknij magazyn danych, aby poznać właściwości obsługiwanego połączenia.

## <a name="compute-linked-services"></a>Usługi połączone usługi Compute

Odwołaj się do [obsługiwanych środowisk obliczeniowych,](compute-linked-services.md) aby uzyskać szczegółowe informacje na temat różnych środowisk obliczeniowych, z którymi można się połączyć z fabryki danych, a także różnych konfiguracji.

## <a name="next-steps"></a>Następne kroki

Zobacz poniższy samouczek, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawów SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki start: tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki start: tworzenie fabryki danych przy użyciu interfejsu REST API](quickstart-create-data-factory-rest-api.md)
- [Szybki start: tworzenie fabryki danych przy użyciu witryny Azure portal](quickstart-create-data-factory-portal.md)
