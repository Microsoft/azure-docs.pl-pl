---
title: Przenoszenie regionów platformy Azure — Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Przenieś serwer Azure Database for PostgreSQL z jednego regionu świadczenia usługi Azure do innego przy użyciu repliki odczytu i Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: c7c095aa710d97292afc7d2d8f633058c21fa4d0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539235"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>Przenoszenie bazy danych platformy Azure dla Azure Database for PostgreSQL-pojedynczego serwera do innego regionu przy użyciu Azure Portal

Istnieją różne scenariusze dotyczące przechodzenia istniejącego serwera Azure Database for PostgreSQL z jednego regionu do innego. Na przykład możesz chcieć przenieść serwer produkcyjny do innego regionu w ramach planowania odzyskiwania po awarii.

Do przechodzenia do innego regionu można użyć Azure Database for PostgreSQL [replik odczytu między regionami](concepts-read-replicas.md#cross-region-replication) . W tym celu należy najpierw utworzyć replikę odczytu w regionie docelowym. Następnie Zatrzymaj replikację do serwera repliki odczytu, aby udostępnić go serwerowi Autonomicznemu, który akceptuje ruch odczytu i zapisu. 

> [!NOTE]
> Ten artykuł koncentruje się na przenoszeniu serwera do innego regionu. Jeśli chcesz przenieść serwer do innej grupy zasobów lub subskrypcji, zapoznaj się z artykułem dotyczącym [przenoszenia](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . 

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcja odczytu repliki między regionami jest dostępna tylko dla Azure Database for PostgreSQL-jednego serwera w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

- Upewnij się, że serwer źródłowy Azure Database for PostgreSQL znajduje się w regionie świadczenia usługi Azure, z którego chcesz przejść.

## <a name="prepare-to-move"></a>Przygotuj do przeniesienia

Aby przygotować serwer źródłowy do replikacji przy użyciu Azure Portal, wykonaj następujące czynności: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako serwer źródłowy. Ta akcja powoduje otwarcie strony **Przegląd** .
1. Z menu serwer wybierz pozycję **replikacja**. Jeśli Obsługa replikacji platformy Azure została ustawiona na co najmniej **replikę**, można utworzyć repliki odczytu. 
1. Jeśli Obsługa replikacji platformy Azure nie jest ustawiona na co najmniej **replikę**, ustaw ją. Wybierz pozycję **Zapisz**.
1. Uruchom ponownie serwer, aby zastosować zmiany, wybierając opcję **tak**.
1. Po zakończeniu operacji otrzymasz dwie Azure Portal powiadomienia. Istnieje jedno powiadomienie dotyczące aktualizowania parametru serwera. Istnieje inne powiadomienie dotyczące ponownego uruchomienia serwera, które następuje natychmiast.
1. Odśwież stronę Azure Portal, aby zaktualizować pasek narzędzi replikacji. Teraz można tworzyć repliki odczytu dla tego serwera.

Aby utworzyć serwer repliki odczytu między regionami w regionie docelowym przy użyciu Azure Portal, wykonaj następujące czynności:

1. Wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako serwer źródłowy.
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz pozycję **Dodaj replikę**.
1. Wprowadź nazwę serwera repliki.
1. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak na serwerze podstawowym. Sprawdź, czy została wybrana lokalizacja docelowa, w której ma zostać wdrożona replika.
1. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki. Podczas tworzenia repliki dane są kopiowane z serwera źródłowego do repliki. Czas utworzenia może trwać kilka minut lub dłużej, proporcjonalnie do rozmiaru serwera źródłowego.

>[!NOTE]
> Podczas tworzenia repliki nie są dziedziczone reguły zapory i punkty końcowe usługi sieci wirtualnej na serwerze podstawowym. Te reguły należy skonfigurować niezależnie dla repliki.

## <a name="move"></a>Move

> [!IMPORTANT]
> Serwer autonomiczny nie może zostać ponownie utworzony w replice.
> Przed zatrzymaniem replikacji w replice odczytu upewnij się, że replika ma wszystkie wymagane dane.

Aby zatrzymać replikację do repliki z Azure Portal, wykonaj następujące czynności:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for PostgreSQL. 
1. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.
1. Wybierz serwer repliki.
1. Wybierz pozycję **Zatrzymaj replikację**.
1. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

## <a name="clean-up-source-server"></a>Oczyść serwer źródłowy

Możesz chcieć usunąć źródłowy serwer Azure Database for PostgreSQL. Aby to zrobić, skorzystaj z poniższych instrukcji:

1. Po utworzeniu repliki Znajdź i wybierz serwer źródłowy Azure Database for PostgreSQL.
1. W oknie **Przegląd** wybierz pozycję **Usuń**.
1. Wpisz nazwę serwera źródłowego, aby potwierdzić, że chcesz usunąć.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono serwer Azure Database for PostgreSQL z jednego regionu do innego przy użyciu Azure Portal a następnie oczyszczono niepotrzebne zasoby źródłowe. 

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
- Dowiedz się więcej o [zarządzaniu odczytanymi replikami w Azure Portal](howto-read-replicas-portal.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)
