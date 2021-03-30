---
title: Tworzenie alertów dla Azure Cosmos DB przy użyciu Azure Monitor
description: Dowiedz się, jak skonfigurować alerty dla Azure Cosmos DB przy użyciu Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339550"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Tworzenie alertów dla Azure Cosmos DB przy użyciu Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Alerty służą do konfigurowania testów cyklicznych w celu monitorowania dostępności i czasu odpowiedzi zasobów Azure Cosmos DB. Alerty mogą wysłać powiadomienie w formie wiadomości e-mail lub wykonać funkcję platformy Azure, gdy jedna z metryk osiągnie próg lub jeśli określone zdarzenie zostanie zarejestrowane w dzienniku aktywności.

Możesz otrzymywać alerty na podstawie metryk lub zdarzenia dziennika aktywności na koncie usługi Azure Cosmos:

* **Metryki** — alert wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Na przykład, gdy łączna liczba zużytych jednostek żądań przekracza 1000 RU/s. Ten alert jest wyzwalany w przypadku pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniany. Zobacz artykuł dotyczący [monitorowania danych](monitor-cosmos-db-reference.md#metrics) dla różnych metryk dostępnych w Azure Cosmos DB.

* **Zdarzenia dziennika aktywności** — ten alert jest wyzwalany po wystąpieniu określonego zdarzenia. Na przykład gdy są dostępne lub odświeżane są klucze konta usługi Azure Cosmos.

Alerty można konfigurować z okienka Azure Cosmos DB lub usługi Azure Monitor w Azure Portal. Oba interfejsy oferują te same opcje. W tym artykule opisano sposób konfigurowania alertów dla Azure Cosmos DB przy użyciu Azure Monitor.

## <a name="create-an-alert-rule"></a>Tworzenie reguły alertu

W tej sekcji przedstawiono sposób tworzenia alertu w przypadku otrzymania kodu stanu HTTP 429, który jest odbierany, gdy żądania są ograniczone. Przykładowo może być konieczne otrzymywanie alertu w przypadku 100 lub więcej żądań z ograniczoną szybkością. W tym artykule opisano sposób konfigurowania alertu na potrzeby takiego scenariusza przy użyciu kodu stanu HTTP. Możesz użyć podobnych kroków, aby skonfigurować również inne typy alertów. Wystarczy wybrać inny warunek w zależności od wymagań.

1. Zaloguj się do [Azure Portal.](https://portal.azure.com/)

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie i wybierz pozycję **alerty**.

1. Wybierz przycisk Nowa reguła alertu, aby otworzyć okienko Tworzenie reguły alertu.  

1. Wypełnij sekcję **zakres** :

   * Otwórz okienko **Wybierz zasób** i skonfiguruj następujące elementy:

   * Wybierz nazwę **subskrypcji** .

   * Wybierz **Azure Cosmos DB konta** dla **typu zasobu**.

   * **Lokalizacja** konta usługi Azure Cosmos.

   * Po wypełnieniu szczegółów zostanie wyświetlona lista kont usługi Azure Cosmos w wybranym zakresie. Wybierz jeden z nich, dla którego chcesz skonfigurować alerty, a następnie wybierz pozycję **gotowe**.

1. Wypełnij sekcję **warunek** :

   * Otwórz okienko **Wybierz warunek** , aby otworzyć stronę **Konfigurowanie logiki sygnałów** i skonfigurować następujące elementy:

   * Wybierz sygnał. **Typ sygnału** może być **metryką** lub **dziennikiem aktywności**. Wybierz **metryki** dla tego scenariusza. Ponieważ chcesz otrzymywać alerty w przypadku, gdy Metryka łącznej liczby jednostek żądań ogranicza liczbę problemów.

   * Zaznacz **wszystko** dla **usługi monitorowania**

   * Wybierz **nazwę sygnału**. Aby uzyskać alert dotyczący kodów stanu HTTP, wybierz **łączny sygnał jednostek żądania** .

   * Na następnej karcie można zdefiniować logikę służącą do wyzwalania alertu i używać wykresu do wyświetlania trendów konta usługi Azure Cosmos. Metryka **łączna liczba jednostek żądań** obsługuje wymiary. Wymiary te umożliwiają filtrowanie według metryki. Jeśli nie zostanie wybrany żaden wymiar, ta wartość zostanie zignorowana.

   * Wybierz wartość **StatusCode** jako **nazwę wymiaru**. Wybierz pozycję **Dodaj wartość niestandardową** i Ustaw kod stanu na 429.

   * W **logice alertu** ustaw wartość **próg** na **statyczny**. Próg statyczny używa wartości progowej zdefiniowanej przez użytkownika do oszacowania reguły, podczas gdy dynamiczne progi używają nieskompilowanych algorytmów uczenia maszynowego, aby stale uczyć się wzorca zachowania metryki i automatycznie obliczać progi.

   * Ustaw **operator** na wartość **większą niż**, **typ agregacji** na **łączny**, a **wartością progową** na **100**. W przypadku tej logiki klient widzi więcej niż 100 żądań, które mają kod stanu 429, zostanie wyzwolony alert. Można również skonfigurować typ agregacji, stopień szczegółowości agregacji oraz częstotliwość oceny w zależności od wymagań.

   * Po wypełnieniu formularza wybierz pozycję **gotowe**. Poniższy zrzut ekranu przedstawia szczegóły logiki alertu:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Konfigurowanie logiki w celu otrzymywania alertów dotyczących żądań o szybkości ograniczonej/429":::

1. Wypełnij sekcję **Grupa akcji** :

   * W okienku **Tworzenie reguły** wybierz istniejącą grupę akcji lub Utwórz nową grupę akcji. Grupa akcji umożliwia zdefiniowanie akcji, która ma zostać wykonana w przypadku wystąpienia warunku alertu. Na potrzeby tego przykładu Utwórz nową grupę akcji, aby otrzymać powiadomienie e-mail o wyzwoleniu alertu. Otwórz okienko **Dodawanie grupy akcji** i wypełnij następujące szczegóły:

   * **Nazwa grupy akcji** — nazwa grupy akcji musi być unikatowa w obrębie grupy zasobów.

   * **Krótka nazwa** — krótka nazwa grupy akcji, ta wartość jest uwzględniana w powiadomieniach E-mail i SMS w celu zidentyfikowania, która grupa akcji była źródłem powiadomienia.

   * Wybierz subskrypcję i grupę zasobów, w której zostanie utworzona ta grupa akcji.  

   * Podaj nazwę akcji, a następnie wybierz opcję **wiadomości e-mail/SMS/wypychanie/głos** jako **Typ akcji**. Poniższy zrzut ekranu przedstawia szczegóły typu akcji:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Skonfiguruj typ akcji, na przykład powiadomienie e-mail, aby otrzymać Alert":::

1. Wypełnij sekcję **szczegóły reguły alertu** :

   * Zdefiniuj nazwę reguły, podaj opcjonalny opis, poziom ważności alertu, określ, czy włączyć regułę przy tworzeniu reguły, a następnie wybierz pozycję **Utwórz alert reguły** , aby utworzyć alert reguły metryki.

Po utworzeniu alertu będzie on aktywny w ciągu 10 minut.

## <a name="common-alerting-scenarios"></a>Typowe scenariusze alertów

Poniżej przedstawiono kilka scenariuszy, w których można korzystać z alertów:

* Gdy zostaną zaktualizowane klucze konta usługi Azure Cosmos.
* Gdy użycie danych lub indeksów przez kontener, baza danych lub region przekracza określoną liczbę bajtów.
* Gdy znormalizowane użycie RU/s jest większe niż określona wartość procentowa. Znormalizowana Metryka użycia RU zapewnia maksymalne wykorzystanie przepływności w zestawie replik. Aby dowiedzieć się więcej, zobacz artykuł [Jak monitorować znormalizowany ru/s](monitor-normalized-request-units.md) .  
* Po dodaniu lub usunięciu regionu lub przejdzie do trybu offline.
* Po utworzeniu, usunięciu lub zaktualizowaniu bazy danych lub kontenera.
* Gdy przepływność bazy danych lub kontenera zostanie zmieniona.

## <a name="next-steps"></a>Następne kroki

* Jak [monitorować znormalizowany pomiar metryki ru/s](monitor-normalized-request-units.md) w kontenerze usługi Azure Cosmos.
* Jak [monitorować przepływność lub zażądać użycia jednostek](monitor-request-unit-usage.md) operacji w Azure Cosmos DB.