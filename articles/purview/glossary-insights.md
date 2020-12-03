---
title: Słownik dotyczący danych przy użyciu usługi kontrolą Insights
description: W tym przewodniku opisano sposób wyświetlania i używania słownika usługi kontrolą Insights na potrzeby raportowania danych.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 9f7d81423c75c3e1a51f5b5d5f37c54307488eb3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554054"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Słownik informacji na temat danych na platformie Azure kontrolą

W tym przewodniku opisano, jak uzyskać dostęp do raportów kontrolą słownikowych i zapoznać się z tymi danymi.

W tym przewodniku krok po kroku dowiesz się, jak:

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

Na stronie **słownik szczegółowych** informacji są wyświetlane następujące obszary:
1. **Wskaźniki KPI wysokiego poziomu** umożliwiające wyświetlanie terminów i użytkowników katalogu

2. **Najważniejsze terminy i liczba elementów zawartości** przedstawiają 5 najważniejszych terminów z dołączonymi do nich zasobami. Wszystkie inne zasoby są uwzględniane w kategorii "inne" na grafie.

3. **Status słownika — terminy według terminu** przedstawia rozkład terminów słownika według stanu, takich jak "wersja robocza", "zatwierdzone", "Alert" i "wygasłe". 

1. Umieść kursor lub kliknij na wycinku wykresu stan i zanotuj liczbę warunków mających ten stan.

1. **Rozkład ról według liczby użytkowników** pokazuje rozkład ról według liczby użytkowników na rolę w kontrolą.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Wyświetl szczegółowe informacje o słowniku":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights za pomocą szczegółowych informacji o [zasobach](./asset-insights.md)