---
title: Skanuj szczegółowe dane na platformie Azure kontrolą
description: W tym przewodniku opisano sposób wyświetlania i używania raportów skanowania kontrolą Insights na danych.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575792"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Skanuj szczegółowe dane na platformie Azure kontrolą

W tym przewodniku opisano sposób uzyskiwania dostępu do raportów usługi Azure kontrolą Scan Insights, wyświetlania ich i filtrowania danych.

W tym przewodniku krok po kroku dowiesz się, jak:

> [!div class="checklist"]
> * Wyświetl szczegółowe informacje na podstawie konta usługi kontrolą.
> * Uzyskaj wgląd w oczy dotyczące skanowania przez ptaka.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

* Skonfiguruj zasoby platformy Azure i wypełnij konto danymi.
* Skonfiguruj i Ukończ skanowanie źródła danych.

Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Korzystanie z usługi kontrolą Scan Insights

W usłudze Azure kontrolą można rejestrować i skanować typy źródłowe. Możesz wyświetlić stan skanowania w czasie w usłudze skanowania szczegółowych informacji. Szczegółowe informacje informują o tym, jak wiele skanów zakończyło się niepowodzeniem, powiodło się lub anulowane w określonym przedziale czasu.

### <a name="view-scan-insights"></a>Wyświetl szczegółowe informacje o skanowaniu

1. Przejdź do ekranu wystąpienia **usługi Azure kontrolą** w Azure Portal i wybierz swoje konto kontrolą.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Otwórz program kontrolą Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Uruchom kontrolą z Azure Portal":::

1. Na stronie **głównej** kontrolą wybierz kafelek **Wyświetl szczegółowe** dane, aby uzyskać dostęp do obszaru **wglądu** w dane :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Wyświetlanie szczegółowych informacji w Azure Portal":::

1. W obszarze usługi **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Wybierz pozycję **skany** , aby wyświetlić raport kontrolą **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Wyświetlanie kluczowych wskaźników wydajności do wyświetlania liczby skanów według stanu
 
Wskaźniki KPI wysokiego poziomu pokazują łączne uruchomienia skanowania w danym okresie. Czas jest domyślnie określony w ciągu ostatnich 30 dni. Można jednak wybrać również opcję ostatnich siedmiu dni. W oparciu o filtr czasu wartości wskaźnika KPI odzwierciedlają odpowiednio liczbę skanów.


W oparciu o wybraną wartość filtru czasu można zobaczyć rozkład udanych, niepowodzeniem i anulowanych skanów według tygodnia lub według dnia na wykresie.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Wyświetl szczegółowe informacje o skanowaniu":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights ze [szczegółowymi informacjami o zasobach](./asset-insights.md)
