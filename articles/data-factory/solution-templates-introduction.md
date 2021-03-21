---
title: Omówienie szablonów
description: Dowiedz się, jak używać wstępnie zdefiniowanego szablonu, aby szybko rozpocząć pracę z Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375395"
---
# <a name="templates"></a>Szablony

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Szablony są wstępnie zdefiniowanymi Azure Data Factory potoki, które umożliwiają szybkie rozpoczęcie pracy z Data Factory. Szablony są przydatne, gdy jesteś nowym do Data Factory i chcesz szybko rozpocząć pracę. Te szablony skracają czas opracowywania projektów integracji danych, a tym samym zwiększają produktywność deweloperów.

## <a name="create-data-factory-pipelines-from-templates"></a>Tworzenie potoków Data Factory z szablonów

Możesz rozpocząć tworzenie potoku Data Factory na podstawie szablonu na dwa sposoby:

1.  Wybierz pozycję **Utwórz potok na podstawie szablonu** na stronie Przegląd, aby otworzyć galerię szablonów.

    ![Otwórz galerię szablonów na stronie Przegląd](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na karcie autor w Eksplorator zasobów wybierz pozycję **+** , a następnie **potok z szablonu** , aby otworzyć galerię szablonów.

    ![Otwórz galerię szablonów na karcie autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria szablonów

![Galeria szablonów](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Z Data Factory szablonów

Data Factory używa szablonów Azure Resource Manager do zapisywania szablonów potoku usługi Fabryka danych. Wszystkie szablony Menedżer zasobów, wraz z plikiem manifestu używanym do wypełniania Data Factory szablonów, można zobaczyć w [oficjalnym repozytorium GitHub Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Wstępnie zdefiniowane szablony udostępniane przez firmę Microsoft obejmują, ale nie są ograniczone do następujących elementów:

-   Kopiuj szablony:

    -   [Kopiowanie masowe z bazy danych](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopiowanie nowych plików według daty ostatniej modyfikacji](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopiowanie wielu kontenerów plików między magazynami opartymi na plikach](solution-template-copy-files-multiple-containers.md)

    -   [Przenoszenie plików](solution-template-move-files.md)

    -   [Kopiowanie przyrostowe z bazy danych](solution-template-delta-copy-with-control-table.md)

    -   Kopiuj z \<source\> do \<destination\>

        -   [Z usługi Amazon S3 do Azure Data Lake Store generacji 2](solution-template-migration-s3-azure.md)

        -   Z usługi Google Big Query do Azure Data Lake Store Gen 2

        -   Od HDF do Azure Data Lake Store Gen 2

        -   Od Netezza do Azure Data Lake Store Gen 1

        -   Od SQL Server lokalnych do Azure SQL Database

        -   Od SQL Server lokalnie do usługi Azure Synapse Analytics

        -   Od firmy Oracle lokalnie do usługi Azure Synapse Analytics

-   Szablony usług SSIS

    -   Planowanie Azure-SSIS Integration Runtime wykonywania pakietów SSIS

-   Przekształć szablony

    -   [ETL z Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje szablony

Możesz również zapisać potok jako szablon, wybierając pozycję **Zapisz jako szablon** na karcie potok.

![Zapisywanie potoku jako szablonu](media/solution-templates-introduction/templates-intro-image4.png)

Potoki zapisane jako szablony można wyświetlić w sekcji **Moje szablony** galerii szablonów. Można je również znaleźć w sekcji **Szablony** w Eksplorator zasobów.

![Moje szablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Aby można było korzystać z funkcji My templates, należy włączyć integrację z usługą GIT. Obsługiwane są zarówno usługi Azure DevOps GIT, jak i GitHub.
