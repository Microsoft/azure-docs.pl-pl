---
title: Informacje o cenach udziałów danych platformy Azure
description: Dowiedz się, jak działa Cennik usługi Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88136923"
---
# <a name="understand-azure-data-share-pricing"></a>Informacje o cenach udziałów danych platformy Azure

W przypadku udostępniania opartego na migawce opłaty za udział danych platformy Azure są naliczane za migawki zestawu danych i wykonywanie migawek. W tym artykule wyjaśniono, jak oszacować migawki zestawu danych i wykonywanie migawek przy użyciu informacji o historii migawek. Obecnie dostawca danych jest rozliczany na potrzeby migawki zestawu danych i wykonania migawki.

## <a name="dataset-snapshot"></a>Migawka zestawu danych

Migawka zestawu danych jest operacją przenoszenia zestawu danych ze źródła do miejsca docelowego. Gdy migawka jest wykonywana dla udziału, migawka każdego zestawu danych w udziale jest operacją migawki zestawu danych. Wykonaj poniższe kroki, aby wyświetlić listę migawek zestawu danych. 

1. W Azure Portal przejdź do zasobu udział danych.

1. Wybierz udział wysłanych lub odebranych udziałów.

1. Kliknij kartę **historia** .

1. Kliknij pozycję czas rozpoczęcia migawki.
 
    ![Historia migawek](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Historia migawek") 

1. Wyświetl listę operacji migawek zestawu danych. Każdy element wiersza odpowiada operacji migawki zestawu danych. W tym przykładzie istnieją dwa migawki zestawu danych.

    ![Operacja migawki zestawu danych](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Operacja migawki zestawu danych")

## <a name="snapshot-execution"></a>Wykonanie migawki

Wykonanie migawki obejmuje zasoby wymagane do przeniesienia zestawu danych ze źródła do miejsca docelowego. Dla każdej operacji migawki zestawu danych wykonanie migawki jest obliczane jako liczba rdzeni wirtualnych pomnożona przez czas trwania migawki. Opłaty są naliczane proporcjonalnie do liczby minut i zaokrąglane w górę. Liczba rdzeń wirtualny jest dynamicznie wybierana na podstawie pary źródłowej i wzorca danych. Wykonaj poniższe kroki, aby wyświetlić czas rozpoczęcia migawki, godzinę zakończenia i rdzeni wirtualnych używane dla operacji migawki zestawu danych.

1. W Azure Portal przejdź do zasobu udział danych.

1. Wybierz udział wysłanych lub odebranych udziałów.

1. Kliknij kartę **historia** .

1. Kliknij pozycję czas rozpoczęcia migawki.

    ![Historia migawek](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Historia migawek") 

1. Kliknij pozycję stan operacji migawki zestawu danych.

    ![Stan migawki zestawu danych](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Stan migawki zestawu danych")

1. Wyświetl czas rozpoczęcia, godzinę zakończenia i rdzeni wirtualnych używany dla tej operacji migawki zestawu danych. 

    ![Wykonanie migawki](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Wykonanie migawki")

## <a name="next-steps"></a>Następne kroki

- Pobierz najnowsze informacje o cenach — [Cennik udziału danych platformy Azure](https://azure.microsoft.com/pricing/details/data-share/)
- Skorzystaj z kalkulatora cen platformy Azure, aby oszacować koszt — [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)
