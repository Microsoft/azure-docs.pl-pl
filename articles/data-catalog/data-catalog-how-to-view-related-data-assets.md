---
title: Jak wyświetlić powiązane zasoby danych w Azure Data Catalog
description: W tym artykule wyjaśniono, jak wyświetlać powiązane zasoby danych wybranego zasobu danych w Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: c1c5ddcacfc529f8fc4ab9a70cea540c44da9fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674788"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Jak wyświetlić powiązane zasoby danych w Azure Data Catalog?

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Data Catalog umożliwia wyświetlanie zasobów danych związanych z wybranym zasobem danych i wyświetlanie relacji między nimi. 

## <a name="supported-data-sources"></a>Obsługiwane źródła danych 
Po zarejestrowaniu zasobów danych z następujących źródeł danych Azure Data Catalog automatycznie rejestruje metadane dotyczące relacji sprzężenia między wybranymi zasobami danych. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Aby Data Catalog do importowania relacji między dwoma zasobami danych, należy zarejestrować oba zasoby jednocześnie. Jeśli jeden z nich został dodany osobno, należy dodać go ponownie i drugi zasób danych, aby zaimportować relację między nimi.

## <a name="view-related-data-assets"></a>Wyświetlanie powiązanych zasobów danych
Aby wyświetlić zasoby danych, które są powiązane z wybranym zestawem danych, Użyj karty **relacje** , jak pokazano na poniższej ilustracji: 

![Azure Data Catalog — Wyświetl powiązane zasoby danych](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

W tym przykładzie istnieją dwie relacje dla wybranego zasobu danych **ProductSubcategory** : 

- Kolumna ProductSubcategoryID tabeli Product zawiera relację klucza obcego z kolumną ProductSubcategoryID zaznaczonej tabeli ProductSubcategory. 
- Kolumna ProductCategoryID tabeli ProductSubCategory zawiera relację klucza obcego z kolumną ProductCategoryID wybranej tabeli ProductCategory.

> [!NOTE]
> Zwróć uwagę na kierunek strzałki w widoku drzewa relacje.  

Aby wyświetlić więcej szczegółów, takich jak w pełni kwalifikowana nazwa kolumny, przesuń wskaźnik myszy nad i zobaczysz podręczny komunikat podobny do następującego: 

![Azure Data Catalog — okno podręczne relacji](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Aby uwzględnić relacje między elementami zawartości, które zostały już zarejestrowane, należy ponownie zarejestrować te zasoby.

## <a name="next-steps"></a>Następne kroki
- [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)
