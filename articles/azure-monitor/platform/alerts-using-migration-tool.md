---
title: Migrowanie reguł alertów Azure Monitor
description: Dowiedz się, jak przeprowadzić migrację klasycznych reguł alertów za pomocą narzędzia do migracji dobrowolnej.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: ab5c16995a2d2bad6e44f0f9d1187ca3d66be1b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114267"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Migrowanie klasycznych reguł alertów za pomocą narzędzia do migracji dobrowolnej

Zgodnie z [dotychczas ogłoszone](monitoring-classic-retirement.md), klasyczne alerty w Azure monitor są wycofywane we wrześniu 2019 (wersja była pierwotnie 2019 lipca). Narzędzie do migracji jest dostępne w Azure Portal do klientów korzystających z klasycznych reguł alertów i którzy chcą wyzwolić migrację. W tym artykule wyjaśniono, jak za pomocą narzędzia migracji dobrowolnie przeprowadzić migrację klasycznych reguł alertów przed rozpoczęciem automatycznej migracji we wrześniu 2019.

> [!NOTE]
> Z powodu opóźnienia w wycofywaniu narzędzia migracji Data wycofania migracji klasycznych alertów została [rozszerzona do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od pierwotnie ogłoszonej daty 30 czerwca 2019.

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

- Niektórych klasycznych reguł alertów nie można migrować za pomocą narzędzia. [Dowiedz się, których reguł nie można migrować i co należy zrobić z nimi](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > Proces migracji nie ma wpływu na ocenę klasycznych reguł alertów. Będą oni nadal uruchamiać i wysyłać alerty, dopóki nie zostaną zmigrowane i zaczną obowiązywać nowe reguły alertów.

## <a name="how-to-use-the-migration-tool"></a>Jak korzystać z narzędzia do migracji

Aby wyzwolić migrację klasycznych reguł alertów w Azure Portal, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Monitoruj**.

1. Wybierz pozycję **alerty**, a następnie wybierz pozycję **Zarządzaj regułami alertów** lub **Wyświetl alerty klasyczne**.

1. Wybierz pozycję **Migruj do nowych reguł** , aby przejść do strony docelowej migracji. Ta strona zawiera listę wszystkich subskrypcji i ich stan migracji:

    ![Migracja — spocznik](media/alerts-migration/migration-landing.png "Migrowanie reguł")

    Wszystkie subskrypcje, które można migrować za pomocą narzędzia, są oznaczone jako **gotowe do migracji**.

    > [!NOTE]
    > Narzędzie migracji jest wdrażane w fazach dla wszystkich subskrypcji, które używają klasycznych reguł alertów. We wczesnych fazach wdrożenia mogą być widoczne pewne subskrypcje oznaczone jako niegotowe do migracji.

1. Wybierz co najmniej jedną subskrypcję, a następnie wybierz pozycję **Podgląd migracji**.

    Na stronie wyniki zostaną wyświetlone szczegóły klasycznych reguł alertów, które będą migrowane w ramach jednej subskrypcji w danym momencie. Możesz również wybrać pozycję **Pobierz szczegóły migracji dla tej subskrypcji** , aby uzyskać szczegółowe informacje w formacie CSV.

    ![Migracja — wersja zapoznawcza](media/alerts-migration/migration-preview.png "Wersja zapoznawcza migracji")

1. Określ co najmniej jeden adres e-mail, aby otrzymywać powiadomienia o stanie migracji. Otrzymasz wiadomość e-mail po zakończeniu migracji lub gdy będzie potrzebna jakakolwiek akcja.

1. Wybierz pozycję **Rozpocznij migrację**. Przeczytaj informacje wyświetlane w oknie dialogowym potwierdzenia i Potwierdź, że wszystko jest gotowe do rozpoczęcia procesu migracji.

    > [!IMPORTANT]
    > Po zainicjowaniu migracji subskrypcji nie będzie można edytować ani tworzyć klasycznych reguł alertów dla tej subskrypcji. To ograniczenie gwarantuje, że zmiany w klasycznych regułach alertów nie zostaną utracone podczas migracji do nowych reguł. Mimo że nie będzie można zmienić klasycznych reguł alertów, nadal będą one działać i aby zapewnić alerty do momentu migracji. Po zakończeniu migracji subskrypcji nie można już używać klasycznych reguł alertów.

    ![Migracja — Potwierdź](media/alerts-migration/migration-confirm.png "Potwierdź uruchomienie migracji")

1. Po zakończeniu migracji lub po wykonaniu akcji wymagane jest wysłanie wiadomości e-mail na podstawie podanych wcześniej adresów. Możesz również okresowo sprawdzać stan na stronie docelowej migracji w portalu.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Dlaczego moja subskrypcja jest wyświetlana jako niegotowa do migracji?

Narzędzie migracji jest wdrażane na klientach w fazach. W wczesnych fazach większość lub wszystkie subskrypcje mogą zostać oznaczone jako **niegotowe do migracji**. 

Gdy subskrypcja stanie się gotowa do migracji, właściciel subskrypcji otrzyma wiadomość e-mail z informacją, że narzędzie jest dostępne. Zachowaj efekt dla tego komunikatu.

### <a name="who-can-trigger-the-migration"></a>Kto może wyzwolić migrację?

Użytkownicy, którzy mają przypisaną rolę współautor monitorowania na poziomie subskrypcji, mogą wyzwolić migrację. [Dowiedz się więcej na temat Access Control opartych na rolach dla procesu migracji](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Jak długo trwa migracja?

Migracja została zakończona dla większości subskrypcji w ciągu godziny. Postęp migracji można śledzić na stronie docelowej migracji. Podczas migracji należy mieć pewność, że alerty nadal działają w klasycznym systemie alertów lub w nowym.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Co mogę zrobić, jeśli wystąpi problem podczas migracji?

Zobacz [Przewodnik rozwiązywania problemów](alerts-understand-migration.md#common-problems-and-remedies) , aby uzyskać pomoc dotyczącą problemów, które można napotkać podczas migracji. Jeśli do ukończenia migracji jest wymagana jakakolwiek akcja, otrzymasz powiadomienie o adresach e-mail podanych podczas konfigurowania narzędzia.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do migracji](alerts-prepare-migration.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
