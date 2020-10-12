---
title: Opóźnij następną akcję w przepływach pracy
description: Zaczekaj na uruchomienie następnej akcji w przepływach pracy aplikacji logiki przy użyciu opóźnienia lub opóźnienia do działania w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74787340"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Opóźnienie uruchamiania następnej akcji w Azure Logic Apps

Aby aplikacja logiki czekała czas przed uruchomieniem następnej akcji, możesz dodać wbudowaną akcję **harmonogramu opóźnień** przed akcją w przepływie pracy aplikacji logiki. Można też dodać wbudowane **opóźnienie do momentu** zaczekania na określoną datę i godzinę przed uruchomieniem następnej akcji. Aby uzyskać więcej informacji o wbudowanych akcjach i wyzwalaczach harmonogramu, zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych, zadań i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Opóźnienie**: Poczekaj na określoną liczbę jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące, przed uruchomieniem następnej akcji.

* **Opóźnij do**: Poczekaj na określoną datę i godzinę przed uruchomieniem następnej akcji.

Oto kilka przykładowych sposobów korzystania z tych akcji:

* Poczekaj, aż dzień tygodnia wyśle aktualizację stanu za pośrednictwem poczty e-mail.

* Opóźnij przepływ pracy do momentu zakończenia wywołania protokołu HTTP przed wznowieniem i pobraniem danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [zarejestrować się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [aplikacji logiki](../logic-apps/logic-apps-overview.md). Aby można było korzystać z akcji, aplikacja logiki musi rozpoczynać się od wyzwalacza. Możesz użyć dowolnego wyzwalacza i dodać inne akcje przed dodaniem akcji opóźnienia. Ten temat używa wyzwalacza programu Outlook pakietu Office 365. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Dowiedz się, [jak utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Dodaj akcję opóźnienia

1. W Projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję Opóźnij, wybierz pozycję **nowy krok**.

   Aby dodać akcję opóźnienia między krokami, przesuń wskaźnik myszy nad strzałkę, która łączy kroki. Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "opóźnienie" jako filtr. Z listy Akcje wybierz tę akcję: **opóźnienie**

   ![Dodaj akcję "Opóźnij"](./media/connectors-native-delay/add-delay-action.png)

1. Określ ilość czasu oczekiwania przed uruchomieniem następnej akcji.

   ![Ustaw czas opóźnienia](./media/connectors-native-delay/delay-time-intervals.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Liczba | count | Tak | Liczba całkowita | Liczba jednostek czasu do opóźnienia |
   | Jednostka | unit | Tak | Ciąg | Jednostka czasu, na przykład:,,,, `Second` `Minute` `Hour` `Day` `Week` lub `Month` |
   ||||||

1. Dodaj inne akcje, które mają być uruchamiane w przepływie pracy.

1. Gdy skończysz, Zapisz aplikację logiki.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Dodaj akcję Opóźnij do momentu

1. W Projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję Opóźnij, wybierz pozycję **nowy krok**.

   Aby dodać akcję opóźnienia między krokami, przesuń wskaźnik myszy nad strzałkę, która łączy kroki. Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "opóźnienie" jako filtr. Z listy Akcje wybierz tę akcję: **opóźnienie do**

   ![Dodaj akcję "Opóźnij do"](./media/connectors-native-delay/add-delay-until-action.png)

1. Podaj datę i godzinę zakończenia okresu, dla którego chcesz wznowić przepływ pracy.

   ![Określ sygnaturę czasową dla momentu zakończenia opóźnienia](./media/connectors-native-delay/delay-until-timestamp.png)

   | Właściwość | Nazwa JSON | Wymagany | Typ | Opis |
   |----------|-----------|----------|------|-------------|
   | Timestamp | sygnatura czasowa | Tak | Ciąg | Data i godzina zakończenia przepływu pracy przy użyciu tego formatu: <p>RRRR-MM-DDTgg: mm: SSS <p>Na przykład jeśli chcesz, aby 18 września 2017 o 2:00 PM, określ "2017-09-18T14:00:00Z". <p>**Uwaga:** Ten format czasu musi być zgodny ze [specyfikacją daty i godziny ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty/godziny](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Bez strefy czasowej należy dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Dodaj inne akcje, które mają być uruchamiane w przepływie pracy.

1. Gdy skończysz, Zapisz aplikację logiki.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy przy użyciu wyzwalacza cykl](../connectors/connectors-native-recurrence.md)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)