---
title: Azure Monitor skoroszytów — Przekształć dane JSON z wykryto
description: Jak używać wykryto w skoroszytach Azure Monitor do przekształcania wyników danych JSON zwracanych przez punkt końcowy z przeszukiwanym do żądanego formatu.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: efa54933ac7d57ec0dcff9ae11b6fb5c2c87a897
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081393"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Jak używać wykryto do przekształcania danych JSON w skoroszytach

Skoroszyty mogą wykonywać zapytania dotyczące danych z wielu źródeł. Niektóre punkty końcowe, takie jak [Azure Resource Manager](../../azure-resource-manager/management/overview.md) lub Custom Endpoint, mogą zwracać wyniki w formacie JSON. Jeśli dane JSON zwrócone przez przeszukiwany punkt końcowy nie są skonfigurowane w żądanym formacie, wykryto może służyć do przekształcania wyników.

Wykryto to język zapytania dla formatu JSON, który jest podobny do XPath dla XML. Podobnie jak XPath, wykryto umożliwia ekstrakcję i filtrowanie danych poza strukturą JSON.

Za pomocą transformacji wykryto, autorzy skoroszytu mogą konwertować kod JSON w strukturę tabeli. Tabeli można następnie użyć do wykreślania [wizualizacji skoroszytów](workbooks-visualizations.md).

## <a name="using-jsonpath"></a>Korzystanie z wykryto

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi *Edytowanie* .
2. Użyj linku *Dodaj*  >  *zapytanie Dodaj* , aby dodać kontrolkę zapytania do skoroszytu.
3. Wybierz źródło danych jako dane *JSON*.
4. Użyj edytora JSON, aby wprowadzić następujący fragment kodu JSON
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Załóżmy, że podano powyższy obiekt JSON jako reprezentację spisu magazynu. Nasze zadanie polega na utworzeniu tabeli dostępnych ksiąg sklepu, wymieniając ich tytuły, autorów i ceny.

1. Wybierz kartę *Ustawienia wyników* i Zmień format wyniku na *ścieżkę JSON*.
2. Zastosuj następujące ustawienia ścieżki JSON:

    Tabela ścieżki JSON: `$.store.books` . To pole reprezentuje ścieżkę katalogu głównego tabeli. W takim przypadku należy zadbać o spis księgowy sklepu. Ścieżka tabeli filtruje plik JSON do informacji o książce.

   | Identyfikatory kolumn | Ścieżka JSON kolumny |
   |:-----------|:-----------------|
   | Tytuł      | `$.title`        |
   | Autor     | `$.author`       |
   | Cena      | `$.price`        |

    Identyfikatory kolumn będą nagłówkami kolumn. Pola ścieżek JSON kolumny reprezentują ścieżkę z katalogu głównego tabeli do wartości kolumny.

1. Aby zastosować powyższe ustawienia, kliknij pozycję *Uruchom zapytanie* .

![ Edytowanie elementu zapytania ze źródłem danych JSON i formatem wyniku ścieżki JSON](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Następne kroki
- [Przegląd skoroszytów](workbooks-overview.md)
- [Grupy w Azure Monitor skoroszytach](workbooks-groups.md)
