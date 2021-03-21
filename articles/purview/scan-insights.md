---
title: Skanuj szczegółowe dane na platformie Azure kontrolą
description: W tym przewodniku opisano sposób wyświetlania i używania raportów skanowania kontrolą Insights na danych.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548695"
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

### <a name="view-scan-insights"></a>Wyświetlanie analiz skanowania

1. Przejdź do ekranu wystąpienia **usługi Azure kontrolą** w Azure Portal i wybierz swoje konto kontrolą.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Otwórz program kontrolą Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Uruchom kontrolą z Azure Portal":::

1. Na stronie **głównej** kontrolą wybierz kafelek **Wyświetl szczegółowe** dane, aby uzyskać dostęp do obszaru **wglądu** w dane :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Wyświetlanie szczegółowych informacji w Azure Portal":::

1. W obszarze usługi **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Wybierz pozycję **skany** , aby wyświetlić raport kontrolą **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Wyświetl kluczowe wskaźniki wydajności, aby pokazać liczbę skanów według stanu i głębokiego szczegółowe w każdym skanowaniu
 
1. Wskaźniki KPI wysokiego poziomu pokazują łączne uruchomienia skanowania w danym okresie. Czas jest domyślnie określony w ciągu ostatnich 30 dni. Można jednak wybrać również opcję ostatnich siedmiu dni. W oparciu o filtr czasu wartości wskaźnika KPI odzwierciedlają odpowiednio liczbę skanów.


1. W oparciu o wybraną wartość filtru czasu można zobaczyć rozkład udanych, niepowodzeniem i anulowanych skanów według tygodnia lub według dnia na wykresie.

1. W dolnej części wykresu znajduje się link **Wyświetl więcej** , aby dowiedzieć się więcej. Link spowoduje otwarcie strony  **stanu skanowania** w ramach funkcji Skanuj szczegółowe dane. W tym miejscu możesz zobaczyć nazwę skanowania i liczbę wystąpień zakończonych powodzeniem, Niepowodzenie lub anulowane w ciągu ostatnich 30 dni.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Wyświetl stan skanowania w czasie":::

4. Aby bardziej szczegółowo zapoznać się z konkretnym skanem, kliknij **nazwę skanowania** , która spowoduje połączenie z historią skanowania w ramach środowiska ze **źródłami** platformy Azure kontrolą. Na stronie Historia uruchamiania można uzyskać identyfikator uruchomienia, który pomoże w dalszej analizie niepowodzeń.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Wyświetl szczegóły skanowania":::

5. Na koniec możesz wrócić do strony Skanuj szczegółowe informacje o **stanie skanowania** , wykonując następujące czynności: chleb Crumbs w lewym górnym rogu strony historia uruchamiania.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Wyświetl historię skanowania"::: 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o usłudze Azure kontrolą **Insights** za pomocą [szczegółowych](./concept-insights.md) informacji

* Dowiedz się więcej na temat **środowiska usługi** Azure kontrolą w zakresie [zarządzania źródłami danych](./manage-data-sources.md)