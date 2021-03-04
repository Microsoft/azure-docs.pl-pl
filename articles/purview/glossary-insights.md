---
title: Słownik dotyczący danych przy użyciu usługi kontrolą Insights
description: W tym przewodniku opisano sposób wyświetlania i używania słownika usługi kontrolą Insights na potrzeby raportowania danych.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095854"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Słownik informacji na temat danych na platformie Azure kontrolą

W tym przewodniku opisano, jak uzyskać dostęp do raportów kontrolą słownikowych i zapoznać się z tymi danymi.

W tym przewodniku krok po kroku dowiesz się, jak:

> [!div class="checklist"]
> - Przejdź do szczegółowych informacji na podstawie konta usługi kontrolą
> - Uzyskiwanie wglądu w dane z lotu ptaka

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

- Konfigurowanie zasobów platformy Azure i wypełnianie konta danymi

- Konfigurowanie i kończenie skanowania typu źródłowego

- Konfigurowanie słownika i dołączanie zasobów do terminów słownika

Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Korzystanie ze szczegółowych informacji o słowniku kontrolą

W usłudze Azure kontrolą można tworzyć terminy słownika i dołączać je do zasobów. Później możesz zobaczyć rozkład słownika w szczegółowe informacje na temat słownika. Oznacza to, że stan słownika zawiera warunki dołączone do zasobów. Zawiera również informacje o stanie i dystrybucji ról według liczby użytkowników.

**Aby wyświetlić szczegółowe informacje o słowniku:**

1. Przejdź do ekranu wystąpienia **usługi Azure kontrolą** [w Azure Portal](https://aka.ms/purviewportal) i wybierz swoje konto kontrolą.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Uruchom konto kontrolą** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Uruchom kontrolą z Azure Portal":::

1. Na stronie **głównej** kontrolą wybierz kafelek **Wyświetl szczegółowe** dane, aby uzyskać dostęp do obszaru **wglądu** w dane :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Wyświetlanie szczegółowych informacji w Azure Portal":::

1. W obszarze usługi **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: Wybierz pozycję **słownik** , aby wyświetlić raport **szczegółowe informacje o słowniku** kontrolą.

**Szczegółowe** informacje są dostępne dla użytkowników firmowych i cennych informacji w celu utrzymania dobrze zdefiniowanego słownika dla organizacji.

1. Raport rozpoczyna się od **wskaźników KPI wysokiego poziomu** , które przedstawiają **_łączne warunki_*_ na koncie kontrolą, _*_zatwierdzone warunki bez zasobów_*_ i _*_utraciły ważność z zasobami_**. Każda z tych wartości pomoże w zidentyfikowaniu kondycji słownika.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Wyświetl wskaźnik KPI szczegółowych informacji o słowniku"::: 


2. **Migawka sekcji warunków** (wyświetlana powyżej) przedstawia stan terminu jako **_wersja robocza_*_, _*,*_**, a*** w przypadku warunków z użyciem zasobów i terminów bez zasobów.

3. Kliknij pozycję **Wyświetl więcej** , aby wyświetlić nazwy terminów z różnymi stanami i więcej szczegółów na temat **_Stewards_*_ i _*_ekspertów_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Migawka warunków zawierających i bez zasobów":::  

4. Po kliknięciu przycisku "Wyświetl więcej" dla ***zatwierdzone warunki przy użyciu elementów zawartości** _, szczegółowe informacje umożliwiają przejście do strony zawierającej informacje o wyrazie _ *słownik** termin, z którego można przejść do listy zasobów z dołączonymi warunkami. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Szczegółowe informacje na temat słownika"::: 

4. Na stronie informacje o słowniku Wyświetl dystrybucję **niekompletnych terminów** według typu informacji. Wykres pokazuje liczbę terminów z **_brakującą definicją_*_, _* nie ma żadnego _eksperta_*_, _* brakuje _Steward_*_ i _* nie _ma więcej_** pól.

1. Kliknij pozycję ***Wyświetl więcej** niż _ * niekompletne warunki * *, aby wyświetlić warunki, które mają brakujące informacje. Możesz przejść do strony szczegółów terminu słownika, aby wprowadzić brakujące informacje i upewnić się, że termin słownikowy został ukończony.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tworzenia terminów słownika za pomocą [słownika](./how-to-create-import-export-glossary.md)