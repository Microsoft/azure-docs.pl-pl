---
title: Omówienie szablonów
description: Dowiedz się, jak szybko rozpocząć pracę z usługą Azure Data Factory za pomocą wstępnie zdefiniowanego szablonu.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 14f1d3b58996d438279bfa2a234c754a6d840c79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414734"
---
# <a name="templates"></a>Szablony
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Szablony są wstępnie zdefiniowane potoki usługi Azure Data Factory, które umożliwiają szybkie rozpoczęcie pracy z fabryką danych. Szablony są przydatne, gdy jesteś nowy w fabryce danych i chcesz szybko rozpocząć pracę. Szablony te skracają czas tworzenia projektów integracji danych, poprawiając w ten sposób produktywność deweloperów.

## <a name="create-data-factory-pipelines-from-templates"></a>Tworzenie potoków fabryki danych na podstawie szablonów

Możesz rozpocząć tworzenie potoku fabryki danych z szablonu na dwa sposoby:

1.  Wybierz **pozycję Utwórz potok z szablonu** na stronie Przegląd, aby otworzyć galerię szablonów.

    ![Otwieranie galerii szablonów na stronie Przegląd](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na karcie Autor w Eksploratorze zasobów wybierz pozycję **+** Potok z **szablonu,** aby otworzyć galerię szablonów.

    ![Otwieranie galerii szablonów na karcie Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria szablonów

![Galeria szablonów](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Szablony fabryki danych po wyjęciu z pudełka

Usługa Data Factory używa szablonów usługi Azure Resource Manager do zapisywania szablonów potoku fabryki danych. W oficjalnym repozytorezie [Usługi Azure Data Factory GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/templates)można wyświetlić wszystkie szablony Menedżera zasobów wraz z plikiem manifestu używanym do szablonów fabryki danych po wyjęciu z pudełka. Wstępnie zdefiniowane szablony dostarczone przez firmę Microsoft zawierają między innymi następujące elementy:

-   Kopiowanie szablonów:

    -   [Kopiowanie zbiorcze z bazy danych](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopiowanie nowych plików według daty ostatniej modyfikacji](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopiowanie wielu kontenerów plików między magazynami opartymi na plikach](solution-template-copy-files-multiple-containers.md)

    -   [Przenoszenie plików](solution-template-move-files.md)

    -   [Kopia delta z bazy danych](solution-template-delta-copy-with-control-table.md)

    -   Kopiowanie \<\> ze \<źródła do miejsca docelowego\>

        -   [Od Amazon S3 do usługi Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Od dużego zapytania Google do usługi Azure Data Lake Store Gen 2

        -   Od hdf do usługi Azure Data Lake Store Gen 2

        -   Od Netezzy do usługi Azure Data Lake Store Gen 1

        -   Od programu SQL Server lokalnie do bazy danych SQL Azure

        -   Od programu SQL Server lokalnie do usługi Azure SQL Data Warehouse

        -   Od oracle lokalnie do usługi Azure SQL Data Warehouse

-   Szablony SSIS

    -   Planowanie środowiska wykonawczego integracji platformy Azure-SSIS w celu wykonania pakietów SSIS

-   Przekształcanie szablonów

    -   [ETL z usługą Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje szablony

Potok można również zapisać jako szablon, wybierając pozycję **Zapisz jako szablon** na karcie Potok.

![Zapisywanie potoku jako szablonu](media/solution-templates-introduction/templates-intro-image4.png)

Potoki zapisane jako szablony można wyświetlić w sekcji Moje szablony w Galerii **szablonów.** Można je również wyświetlić w sekcji **Szablony** w Eksploratorze zasobów.

![Moje szablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Aby korzystać z funkcji Moje szablony, należy włączyć integrację GIT. Obsługiwane są zarówno narzędzia Azure DevOps GIT, jak i GitHub.
