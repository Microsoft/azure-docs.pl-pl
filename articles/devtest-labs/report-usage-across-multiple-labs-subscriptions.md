---
title: Azure DevTest Labs użycie w wielu laboratoriach i subskrypcjach
description: Dowiedz się, jak raportować użycie Azure DevTest Labs w wielu laboratoriach i subskrypcjach.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d2663113e929145308f5a5712b968f3551668c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287271"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Raportowanie użycia Azure DevTest Labs w wielu laboratoriach i subskrypcjach

Większość dużych organizacji chce śledzić użycie zasobów, aby była bardziej skuteczna dzięki tym zasobom, wizualizowając trendy i wartości odstających w użyciu. Na podstawie użycia zasobów właściciele laboratorium lub menedżerowie mogą dostosować laboratoria, aby [zwiększyć użycie i koszty zasobów](../cost-management-billing/manage/getting-started.md). W Azure DevTest Labs można pobrać użycie zasobów na laboratorium, co pozwoli uzyskać dokładniejszy historyczny wygląd wzorców użytkowania. Te wzorce użycia mogą pomóc w identyfikowaniu zmian w celu zwiększenia wydajności. Większość przedsiębiorstw chce użyć indywidualnych zastosowań laboratoryjnych i ogólnego użycia w [wielu laboratoriach i subskrypcjach](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

W tym artykule omówiono sposób obsługi informacji o użyciu zasobów w wielu laboratoriach i subskrypcjach.

![Raportowanie użycia](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Indywidualne użycie laboratorium

W tej sekcji omówiono sposób eksportowania użycia zasobów dla jednego laboratorium.

Przed wyeksportowaniem użycia zasobów DevTest Labs należy skonfigurować konto usługi Azure Storage, aby umożliwić przechowywanie różnych plików zawierających dane użycia. Istnieją dwa typowe sposoby wykonywania eksportu danych:

* [Interfejs API REST usługi DevTest Labs](/rest/api/dtl/labs/exportresourceusage) 
* Program PowerShell AZ. Resource module [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) z akcją `exportResourceUsage` , identyfikatorem zasobu laboratorium i wymaganymi parametrami. 

    Artykuł [Eksportuj lub Usuń dane osobowe](personal-data-delete-export.md) zawiera przykładowy skrypt programu PowerShell ze szczegółowymi informacjami na temat eksportowanych danych. 

    > [!NOTE]
    > Parametr Date nie zawiera sygnatury czasowej, dlatego dane obejmują wszystko z północy na podstawie strefy czasowej, w której znajduje się laboratorium.

Po zakończeniu eksportowania w magazynie obiektów BLOB będzie wiele plików CSV z różnymi informacjami o zasobach.
  
Obecnie istnieją dwa pliki CSV:

* *virtualmachines.csv* — zawiera informacje o maszynach wirtualnych w laboratorium
* *disks.csv* — zawiera informacje o różnych dyskach w laboratorium 

Te pliki są przechowywane w kontenerze obiektów BLOB *labresourceusage* pod nazwą laboratorium, UNIKATOWYm identyfikatorem laboratorium, datą wykonania oraz datą pełną lub początkową opartą na żądaniu eksportu. Przykładowa struktura obiektów blob:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Eksportowanie użycia dla wszystkich laboratoriów

Aby wyeksportować informacje o użyciu dla wielu laboratoriów, rozważ użycie 

* [Azure Functions](../azure-functions/index.yml), dostępne w wielu językach, w tym program PowerShell lub 
* [Azure Automation elementu Runbook](../automation/index.yml), użyj programu PowerShell, języka Python lub niestandardowego projektanta graficznego, aby napisać kod eksportu.

Korzystając z tych technologii, można wykonać poszczególne eksporty laboratoryjne na wszystkich laboratoriach w określonym dniu i o określonej godzinie. 

Funkcja platformy Azure powinna wypchnąć dane do magazynu o dłuższym okresie. Podczas eksportowania danych dla wielu laboratoriów eksport może zająć trochę czasu. Aby pomóc w wydajności i zmniejszeniu możliwości duplikowania informacji, zalecamy wykonanie każdego laboratorium równolegle. Aby osiągnąć równoległość, uruchom Azure Functions asynchronicznie. Skorzystaj również z wyzwalacza czasomierza, który Azure Functions ofercie.

## <a name="using-a-long-term-storage"></a>Korzystanie z magazynu długoterminowego

Długoterminowy magazyn konsoliduje informacje o eksporcie z różnych laboratoriów w jednym źródle danych. Kolejną zaletą korzystania z magazynu długoterminowego jest możliwość usuwania plików z konta magazynu w celu ograniczenia duplikacji i kosztów. 

Długoterminowe przechowywanie danych może służyć do wykonywania operacji związanych z tekstem, na przykład: 

* Dodawanie przyjaznych nazw
* tworzenie złożonych grup
* agregowanie danych.

Poniżej wymieniono niektóre typowe rozwiązania magazynu: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)i [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Wybór długoterminowego rozwiązania do magazynowania zależy od preferencji. Możesz rozważyć wybranie narzędzia w zależności od tego, co jest dostępne w zakresie dostępności interakcji podczas wizualizacji danych.

## <a name="visualizing-data-and-gathering-insights"></a>Wizualizowanie danych i gromadzenie szczegółowych informacji

Użyj wybranego narzędzia do wizualizacji danych, aby nawiązać połączenie z magazynem długoterminowym, aby wyświetlić dane użycia i zebrać szczegółowe informacje w celu sprawdzenia wydajności użycia. Na przykład [Power BI](/power-bi/power-bi-overview) może służyć do organizowania i wyświetlania danych użycia. 

Za pomocą [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) można tworzyć i łączyć zasoby oraz zarządzać nimi w ramach jednego interfejsu lokalizacji. Jeśli jest wymagana większa kontrola, poszczególne zasoby mogą być tworzone w ramach jednej grupy zasobów i zarządzane niezależnie od usługi Data Factory.  

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu systemu i przeniesieniu danych do magazynu długoterminowego następnym krokiem jest uzyskanie odpowiedzi na pytania, do których dane muszą odpowiedzieć. Na przykład: 

-   Co to jest użycie rozmiaru maszyny wirtualnej?

    Czy użytkownicy wybierają Wysokie rozmiary maszyn wirtualnych?
-   Które obrazy z portalu Marketplace są używane?

    Są obrazami niestandardowymi najbardziej typową bazą maszyn wirtualnych, w przypadku których wspólny magazyn obrazów jest kompilowany jako [Galeria obrazów udostępnionych](../virtual-machines/windows/shared-image-galleries.md) lub [fabryka obrazów](image-factory-create.md).
-   Które obrazy niestandardowe są używane lub nie są używane?
