---
title: Skonfiguruj ciągłe tworzenie kopii zapasowych i przywracanie do punktu w czasie za pomocą Azure Portal w Azure Cosmos DB.
description: Dowiedz się, jak zidentyfikować punkt przywracania i skonfigurować ciągłą kopię zapasową przy użyciu Azure Portal. Pokazuje sposób przywrócenia aktywnego i usuniętego konta.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: a5be4c16b93f22037152147ada8c4ba2b4443eba
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527574"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-portal"></a>Konfigurowanie i zarządzanie ciągłymi kopiami zapasowymi oraz przywracanie do punktu w czasie za pomocą Azure Portal
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Funkcja przywracania do punktu w czasie Azure Cosmos DBa ułatwia odzyskanie od przypadkowej zmiany w kontenerze, przywrócenie usuniętego konta, bazy danych lub kontenera lub przywrócenie do dowolnego regionu (w którym istnieją kopie zapasowe). Tryb ciągłej kopii zapasowej umożliwia przywracanie w dowolnym momencie w ciągu ostatnich 30 dni.

W tym artykule opisano sposób identyfikowania punktu przywracania i konfigurowania ciągłej kopii zapasowej przy użyciu Azure Portal.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Inicjowanie obsługi administracyjnej konta przy użyciu ciągłej kopii zapasowej

Podczas tworzenia nowego konta Azure Cosmos DB, dla opcji **zasady tworzenia kopii zapasowych** wybierz tryb **ciągły** , aby włączyć funkcję Przywracanie do punktu w czasie dla nowego konta. Po włączeniu tej funkcji dla konta wszystkie bazy danych i kontenery są dostępne dla ciągłej kopii zapasowej. Po przywróceniu do punktu w czasie dane są zawsze przywracane do nowego konta. obecnie nie można przywrócić istniejącego konta.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Zainicjuj obsługę konta Azure Cosmos DB przy użyciu konfiguracji ciągłej kopii zapasowej." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Przywracanie na żywo konta przed przypadkową modyfikacją

Za pomocą Azure Portal można przywrócić aktywne konto lub wybrane bazy danych i kontenery w ramach usługi. Wykonaj następujące kroki, aby przywrócić dane:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)
1. Przejdź do konta Azure Cosmos DB i Otwórz okienko **przywracanie do punktu w czasie** .

   > [!NOTE]
   > Okienko przywracania w Azure Portal jest wypełniane tylko wtedy, gdy masz odpowiednie `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnienia. Aby dowiedzieć się więcej na temat sposobu ustawiania tego uprawnienia, zobacz artykuł dotyczący [uprawnień do tworzenia kopii zapasowych i przywracania](continuous-backup-restore-permissions.md) .

1. Wypełnij następujące szczegóły, aby przywrócić:

   * **Punkt przywracania (UTC)** — sygnatura czasowa w ciągu ostatnich 30 dni. Konto powinno istnieć w tym znaczniku czasu. Punkt przywracania można określić w formacie UTC. Może to być zbliżone do drugiego, gdy chcesz go przywrócić. Wybierz link **kliknij tutaj** , aby uzyskać pomoc dotyczącą [identyfikowania punktu przywracania](#event-feed).

   * **Lokalizacja** — region docelowy, w którym konto zostanie przywrócone. Konto powinno istnieć w tym regionie pod daną sygnaturą czasową (np. Zachodnie stany USA lub Wschodnie stany USA). Konto można przywrócić tylko do regionów, w których istniało konto źródłowe.

   * **Przywróć zasób** — możesz wybrać **całe konto** lub **wybraną bazę danych/kontener** do przywrócenia. Bazy danych i kontenery powinny znajdować się w danym znaczniku czasu. Na podstawie wybranego punktu przywracania i lokalizacji są wypełniane zasoby, które umożliwiają użytkownikowi wybranie określonych baz danych lub kontenerów, które muszą zostać przywrócone.

   * **Grupa zasobów** — Grupa zasobów, w ramach której zostanie utworzone i przywrócone konto docelowe. Grupa zasobów musi już istnieć.

   * **Przywróć konto docelowe** — nazwa konta docelowego. Nazwa konta docelowego musi być zgodna z wytycznymi podczas tworzenia nowego konta. To konto zostanie utworzone przez proces przywracania w tym samym regionie, w którym istnieje konto źródłowe.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Przywróć konto na żywo na podstawie przypadkowej modyfikacji Azure Portal." border="true":::

1. Po wybraniu powyższych parametrów wybierz przycisk **Prześlij** , aby rozpocząć przywracanie. Koszt przywracania to opłata jednorazowa, która jest oparta na ilości danych i opłatach za magazyn w danym regionie. Aby dowiedzieć się więcej, zobacz sekcję dotyczącą [cen](continuous-backup-restore-introduction.md#continuous-backup-pricing) .

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Użyj kanału informacyjnego zdarzenia, aby zidentyfikować czas przywracania

W przypadku wypełniania czasu punktu przywracania w Azure Portal, jeśli potrzebna jest pomoc dotycząca identyfikacji punktu przywracania, wybierz link **kliknij tutaj** , aby przeprowadzić Cię do bloku źródła zdarzeń. Kanał informacyjny zdarzenia zawiera pełną listę elementów służących do tworzenia, zastępowania, usuwania zdarzeń w bazach danych i kontenerach konta źródłowego. 

Na przykład jeśli chcesz przywrócić do punktu przed usunięciem lub zaktualizowaniem określonego kontenera, sprawdź to źródło zdarzeń. Zdarzenia są wyświetlane w chronologicznie malejącym czasie, gdy wystąpią, z ostatnimi zdarzeniami w górnej części. Możesz przeglądać wyniki i wybierać czas przed lub po zdarzeniu, aby jeszcze bardziej zawęzić czas.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Użyj kanału informacyjnego zdarzenia, aby zidentyfikować czas punktu przywracania." border="true":::

> [!NOTE]
> W kanale informacyjnym zdarzenia nie są wyświetlane zmiany zasobów elementu. Można zawsze ręcznie określić wszystkie sygnatury czasowe w ciągu ostatnich 30 dni (tak długo, jak w tym czasie istnieje konto) do przywracania.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Przywracanie usuniętego konta

Za pomocą Azure Portal można całkowicie przywrócić usunięte konto w ciągu 30 dni od jego usunięcia. Wykonaj następujące kroki, aby przywrócić usunięte konto:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)
1. Wyszukaj zasoby "Azure Cosmos DB" na pasku wyszukiwania globalnego. Wyświetla on wszystkie istniejące konta.
1. Następnie wybierz przycisk **Przywróć** . W okienku przywracanie zostanie wyświetlona lista usuniętych kont, które mogą zostać przywrócone w okresie przechowywania, co oznacza 30 dni od czasu usunięcia.
1. Wybierz konto, które chcesz przywrócić.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Przywróć usunięte konto z Azure Portal." border="true":::

   > [!NOTE]
   > Uwaga: okienko przywracania w Azure Portal jest wypełniane tylko wtedy, gdy masz odpowiednie `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` uprawnienia. Aby dowiedzieć się więcej na temat sposobu ustawiania tego uprawnienia, zobacz artykuł dotyczący [uprawnień do tworzenia kopii zapasowych i przywracania](continuous-backup-restore-permissions.md) .

1. Wybierz konto do przywrócenia i wprowadź następujące szczegóły, aby przywrócić usunięte konto:

   * **Punkt przywracania (UTC)** — sygnatura czasowa w ciągu ostatnich 30 dni. Konto powinno znajdować się w tej sygnaturze czasowej. Określ punkt przywracania w formacie UTC. Może to być zbliżone do drugiego, gdy chcesz go przywrócić.

   * **Lokalizacja** — region docelowy, w którym konto musi zostać przywrócone. Konto źródłowe powinno istnieć w tym regionie pod daną sygnaturą czasową. Przykład zachodnie stany USA lub Wschodnie stany USA.  

   * **Grupa zasobów** — Grupa zasobów, w ramach której zostanie utworzone i przywrócone konto docelowe. Grupa zasobów musi już istnieć.

   * **Przywróć konto docelowe** — nazwa konta docelowego musi być taka sama, jak podczas tworzenia nowego konta. To konto zostanie utworzone przez proces przywracania w tym samym regionie, w którym istnieje konto źródłowe.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Śledzenie stanu operacji przywracania

Po zainicjowaniu operacji przywracania wybierz ikonę dzwonka **powiadomienia** w prawym górnym rogu portalu. Zawiera link do wyświetlania stanu przywracanego konta. Gdy przywracanie jest w toku, stan konta będzie "Tworzenie", po zakończeniu operacji przywracania stan konta zmieni się na "online".

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="Stan przywróconych zmian konta z tworzenia do trybu online po zakończeniu operacji." border="true":::

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md), [programu PowerShell](continuous-backup-restore-powershell.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
