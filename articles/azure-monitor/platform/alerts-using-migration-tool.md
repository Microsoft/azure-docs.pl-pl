---
title: Migrowanie reguł alertów Azure Monitor
description: Dowiedz się, jak przeprowadzić migrację klasycznych reguł alertów za pomocą narzędzia do migracji dobrowolnej.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: e49525018a3e23ecbbf92d7a8b3f7c50804432b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358665"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Migrowanie klasycznych reguł alertów za pomocą narzędzia do migracji dobrowolnej

Jak [wcześniej ogłoszone](monitoring-classic-retirement.md), klasyczne alerty w Azure monitor są wycofywane, mimo że nadal są ograniczone dla zasobów, które jeszcze nie obsługują nowych alertów. Narzędzie do migracji było dostępne w Azure Portal do klientów, którzy korzystali z klasycznych reguł alertów i którzy chcą wyzwolić migrację. W tym artykule wyjaśniono, jak używać tego narzędzia do migracji, które będzie również używane do pozostałych alertów oczekujących na dalsze ogłoszenie.

## <a name="benefits-of-new-alerts"></a>Zalety nowych alertów

Alerty klasyczne są zastępowane przez nowe, ujednolicone alerty w Azure Monitor. Nowa platforma alertów ma następujące zalety:

- Możesz otrzymywać alerty dotyczące wielu wielowymiarowych metryk dla [wielu innych usług platformy Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Nowe alerty metryk obsługują [reguły alertów obejmujących wiele zasobów](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) , które znacznie zmniejszają obciążenie związane z zarządzaniem wieloma regułami.
- Mechanizm ujednoliconego powiadamiania, który obsługuje:
  - [Grupy akcji](action-groups.md), modułowy mechanizm powiadamiania, który działa ze wszystkimi nowymi typami alertów (metryki, dziennika i dziennika aktywności).
  - Nowe mechanizmy powiadamiania, takie jak SMS, Voice i łącznik ITSM.
- [Ujednolicone środowisko alertów](alerts-overview.md) powoduje, że wszystkie alerty dotyczące różnych sygnałów (metryk, dzienników i dzienników aktywności) w jednym miejscu.

## <a name="before-you-migrate"></a>Przed przeprowadzeniem migracji

Proces migracji umożliwia konwertowanie klasycznych reguł alertów na nowe, równoważne reguły alertów i tworzenie grup akcji. W przygotowaniu należy pamiętać o następujących kwestiach:

- Zarówno format ładunku, jak i interfejsy API służące do tworzenia nowych reguł alertów i zarządzania nimi, różnią się od tych reguł, ponieważ obsługują one więcej funkcji. [Dowiedz się, jak przygotować się do migracji](alerts-prepare-migration.md).

- Niektórych klasycznych reguł alertów nie można migrować za pomocą narzędzia. [Dowiedz się, których reguł nie można migrować i co należy zrobić z nimi](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > Proces migracji nie ma wpływu na ocenę klasycznych reguł alertów. Będą oni nadal uruchamiać i wysyłać alerty, dopóki nie zostaną zmigrowane i zaczną obowiązywać nowe reguły alertów.

## <a name="how-to-use-the-migration-tool"></a>Jak korzystać z narzędzia do migracji

Aby wyzwolić migrację klasycznych reguł alertów w Azure Portal, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Monitoruj**.

1. Wybierz pozycję **alerty**, a następnie wybierz pozycję **Zarządzaj regułami alertów** lub **Wyświetl alerty klasyczne**.

1. Wybierz pozycję **Migruj do nowych reguł** , aby przejść do strony docelowej migracji. Ta strona zawiera listę wszystkich subskrypcji i ich stan migracji:

    ![Zrzut ekranu przedstawia stronę Migrowanie reguł alertów.](media/alerts-migration/migration-landing.png "Migrowanie reguł")

    Wszystkie subskrypcje, które można migrować za pomocą narzędzia, są oznaczone jako **gotowe do migracji**.

    > [!NOTE]
    > Narzędzie migracji jest wdrażane w fazach dla wszystkich subskrypcji, które używają klasycznych reguł alertów. We wczesnych fazach wdrożenia mogą być widoczne pewne subskrypcje oznaczone jako niegotowe do migracji.

1. Wybierz co najmniej jedną subskrypcję, a następnie wybierz pozycję **Podgląd migracji**.

    Na stronie wyniki zostaną wyświetlone szczegóły klasycznych reguł alertów, które będą migrowane w ramach jednej subskrypcji w danym momencie. Możesz również wybrać pozycję **Pobierz szczegóły migracji dla tej subskrypcji** , aby uzyskać szczegółowe informacje w formacie CSV.

    ![Zrzut ekranu przedstawia stronę Migrowanie reguł alertów z linkiem umożliwiającym pobranie szczegółów migracji dla tej subskrypcji, a także określenie wiadomości e-mail do powiadomienia o migracji.](media/alerts-migration/migration-preview.png "Wersja zapoznawcza migracji")

1. Określ co najmniej jeden adres e-mail, aby otrzymywać powiadomienia o stanie migracji. Otrzymasz wiadomość e-mail po zakończeniu migracji lub gdy będzie potrzebna jakakolwiek akcja.

1. Wybierz pozycję **Rozpocznij migrację**. Przeczytaj informacje wyświetlane w oknie dialogowym potwierdzenia i Potwierdź, że wszystko jest gotowe do rozpoczęcia procesu migracji.

    > [!IMPORTANT]
    > Po zainicjowaniu migracji subskrypcji nie będzie można edytować ani tworzyć klasycznych reguł alertów dla tej subskrypcji. To ograniczenie gwarantuje, że zmiany w klasycznych regułach alertów nie zostaną utracone podczas migracji do nowych reguł. Mimo że nie będzie można zmienić klasycznych reguł alertów, nadal będą one działać i aby zapewnić alerty do momentu migracji. Po zakończeniu migracji subskrypcji nie można już używać klasycznych reguł alertów.

    ![Zrzut ekranu przedstawia monit o potwierdzenie migracji, w tym ważne informacje z linkami, aby dowiedzieć się więcej przed kontynuowaniem.](media/alerts-migration/migration-confirm.png "Potwierdź uruchomienie migracji")

1. Po zakończeniu migracji lub po wykonaniu akcji wymagane jest wysłanie wiadomości e-mail na podstawie podanych wcześniej adresów. Możesz również okresowo sprawdzać stan na stronie docelowej migracji w portalu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Dlaczego moja subskrypcja jest wyświetlana jako niegotowa do migracji?

Narzędzie migracji jest wdrażane na klientach w fazach. W wczesnych fazach większość lub wszystkie subskrypcje mogą zostać oznaczone jako **niegotowe do migracji**. 

Gdy subskrypcja stanie się gotowa do migracji, właściciel subskrypcji otrzyma wiadomość e-mail z informacją, że narzędzie jest dostępne. Zachowaj efekt dla tego komunikatu.

### <a name="who-can-trigger-the-migration"></a>Kto może wyzwolić migrację?

Użytkownicy, którzy mają przypisaną rolę współautor monitorowania na poziomie subskrypcji, mogą wyzwolić migrację. [Dowiedz się więcej na temat Access Control Role-Based na potrzeby procesu migracji](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak długo trwa migracja?

Migracja została zakończona dla większości subskrypcji w ciągu godziny. Postęp migracji można śledzić na stronie docelowej migracji. Podczas migracji należy mieć pewność, że alerty nadal działają w klasycznym systemie alertów lub w nowym.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mogę zrobić, jeśli wystąpi problem podczas migracji?

Zobacz [Przewodnik rozwiązywania problemów](alerts-understand-migration.md#common-problems-and-remedies) , aby uzyskać pomoc dotyczącą problemów, które można napotkać podczas migracji. Jeśli do ukończenia migracji jest wymagana jakakolwiek akcja, otrzymasz powiadomienie o adresach e-mail podanych podczas konfigurowania narzędzia.

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
