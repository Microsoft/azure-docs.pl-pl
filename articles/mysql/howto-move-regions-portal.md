---
title: Przenoszenie regionów platformy Azure — Azure Portal — Azure Database for MySQL
description: Przenieś serwer Azure Database for MySQL z jednego regionu świadczenia usługi Azure do innego przy użyciu repliki odczytu i Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 87c44e5d56e59b0bf003e8e7d25f3351f58a8984
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065484"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Przenoszenie serwera Azure Database for MySQL do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze dotyczące przechodzenia istniejącego serwera Azure Database for MySQL z jednego regionu do innego. Na przykład możesz chcieć przenieść serwer produkcyjny do innego regionu w ramach planowania odzyskiwania po awarii.

Do przechodzenia do innego regionu można użyć Azure Database for MySQL [replik odczytu między regionami](concepts-read-replicas.md#cross-region-replication) . W tym celu należy najpierw utworzyć replikę odczytu w regionie docelowym. Następnie Zatrzymaj replikację do serwera repliki odczytu, aby udostępnić go serwerowi Autonomicznemu, który akceptuje ruch odczytu i zapisu. 

> [!NOTE]
> Ten artykuł koncentruje się na przenoszeniu serwera do innego regionu. Jeśli chcesz przenieść serwer do innej grupy zasobów lub subskrypcji, zapoznaj się z artykułem dotyczącym [przenoszenia](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MySQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

- Upewnij się, że serwer źródłowy Azure Database for MySQL znajduje się w regionie świadczenia usługi Azure, z którego chcesz przejść.

## <a name="prepare-to-move"></a>Przygotuj do przeniesienia

Aby utworzyć serwer repliki odczytu między regionami w regionie docelowym przy użyciu Azure Portal, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Wybierz istniejący serwer Azure Database for MySQL, który ma być używany jako serwer źródłowy. Ta akcja powoduje otwarcie strony **Przegląd** .
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz pozycję **Dodaj replikę**.
1. Wprowadź nazwę serwera repliki.
1. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera źródłowego. Sprawdź, czy została wybrana lokalizacja docelowa, w której ma zostać wdrożona replika.
1. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki. Podczas tworzenia repliki dane są kopiowane z serwera źródłowego do repliki. Czas utworzenia może trwać kilka minut lub dłużej, proporcjonalnie do rozmiaru serwera źródłowego.

>[!NOTE]
> Podczas tworzenia repliki nie są dziedziczone punkty końcowe usługi sieci wirtualnej serwera źródłowego. Te reguły należy skonfigurować niezależnie dla repliki.

## <a name="move"></a>Move

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Zatrzymywanie replikacji na serwerze repliki powoduje, że staje się ona serwerem autonomicznym. Aby zatrzymać replikację do repliki z Azure Portal, wykonaj następujące czynności:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for MySQL. 
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz serwer repliki.
1. Wybierz pozycję **Zatrzymaj replikację**.
1. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

## <a name="clean-up-source-server"></a>Oczyść serwer źródłowy

Możesz chcieć usunąć źródłowy serwer Azure Database for MySQL. Aby to zrobić, wykonaj następujące kroki:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for MySQL.
1. W oknie **Przegląd** wybierz pozycję **Usuń**.
1. Wpisz nazwę serwera źródłowego, aby potwierdzić, że chcesz usunąć.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono serwer Azure Database for MySQL z jednego regionu do innego przy użyciu Azure Portal a następnie oczyszczono niepotrzebne zasoby źródłowe. 

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
- Dowiedz się więcej o [zarządzaniu odczytanymi replikami w Azure Portal](howto-read-replicas-portal.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)