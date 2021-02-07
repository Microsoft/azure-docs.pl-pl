---
title: Odkrywanie i Eksplorowanie danych w usłudze ADF przy użyciu kontrolą
description: Dowiedz się, jak odkrywać i eksplorować dane w Azure Data Factory przy użyciu kontrolą
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: accb9bbf195daa3d25e1aed109e36ef309083385
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805313"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Odkrywanie i Eksplorowanie danych w usłudze ADF przy użyciu kontrolą

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule zarejestrowano konto usługi Azure kontrolą do Data Factory. To połączenie umożliwia odnajdywanie zasobów usługi Azure kontrolą i współdziałanie z nimi za pomocą funkcji ADF. 

W usłudze ADF można wykonywać następujące zadania: 
- Użyj pola wyszukiwania u góry, aby znaleźć kontrolą zasoby na podstawie słów kluczowych 
- Informacje na temat metadanych, elementów zależnych, adnotacji 
- Połącz te dane z fabryką danych za pomocą połączonych usług lub zestawów DataSet 

## <a name="prerequisites"></a>Wymagania wstępne 
- [Konto usługi Azure kontrolą](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Połącz konto usługi Azure kontrolą w Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Korzystanie z usługi Azure kontrolą w Data Factory 

Korzystanie z usługi Azure kontrolą w Data Factory wymaga posiadania dostępu do tego konta kontrolą. Data Factory przekazują uprawnienia kontrolą. Jeśli masz na przykład rolę uprawnienia Curator, będziesz mieć możliwość edytowania metadanych przeskanowanych przez usługę Azure kontrolą. 

### <a name="data-discovery-search-datasets"></a>Odnajdywanie danych: Przeszukaj zbiory 

Aby odnajdywać dane zarejestrowane i przeskanowane przez usługę Azure kontrolą, można użyć paska wyszukiwania w górnej części portalu Data Factory. Upewnij się, że wybrano pozycję Azure kontrolą, aby wyszukać wszystkie dane organizacji. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Zrzut ekranu przedstawiający przeprowadzenie zestawu danych.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Akcje, które można wykonywać na zestawach danych przy użyciu zasobów Data Factory 
Możesz bezpośrednio utworzyć połączoną usługę, zestaw danych lub przepływu danych za pośrednictwem danych przeszukanych przez usługę Azure kontrolą.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Zrzut ekranu pokazujący, jak można bezpośrednio utworzyć połączoną usługę, zestaw danych lub przepływu danych za pośrednictwem danych przeszukanych przez usługę Azure kontrolą.":::

##  <a name="nextsteps"></a>Następne kroki 

- [Rejestrowanie i skanowanie Azure Data Factory zasobów na platformie Azure kontrolą](../purview/register-scan-azure-synapse-analytics.md)
- [Jak wyszukiwać dane w usłudze Azure kontrolą Data Catalog](../purview/how-to-search-catalog.md)