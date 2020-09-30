---
title: Zarządzanie replikami odczytu — Azure Database for MySQL Azure Portal
description: Dowiedz się, jak skonfigurować repliki odczytu i zarządzać nimi w Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 8f3c85bb6dab906528c3c442cbdd1b762d8145e0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533132"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL przy użyciu Azure Portal

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi w usłudze Azure Database for MySQL przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) , który będzie używany jako serwer źródłowy.

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MySQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer źródłowy znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

> [!IMPORTANT]
> Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejący serwer Azure Database for MySQL, który ma być używany jako główny. Ta akcja powoduje otwarcie strony **Przegląd** .

3. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

4. Wybierz pozycję **Dodaj replikę**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL — replikacja":::

5. Wprowadź nazwę serwera repliki.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL — replikacja":::

6. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera źródłowego.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL — replikacja":::

   > [!NOTE]
   > Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

7. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co serwer główny. Konfigurację serwera repliki można zmienić po jego utworzeniu. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów i tej samej subskrypcji co serwer źródłowy. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

Po utworzeniu serwera repliki można go wyświetlić w bloku **replikacja** .

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL — replikacja":::

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między źródłem a repliką nie można jej cofnąć. Serwer repliki stał się serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Nie można ponownie wykonać tego serwera w replice.

Aby zatrzymać replikację między źródłem a serwerem repliki z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MySQL. 

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL — replikacja":::

4. Wybierz pozycję **Zatrzymaj replikację**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL — replikacja":::

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL — replikacja":::

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MySQL.

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL — replikacja":::

4. Wybierz pozycję **Usuń replikę**

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL — replikacja":::

5. Wpisz nazwę repliki, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie repliki.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL — replikacja":::

## <a name="delete-a-source-server"></a>Usuwanie serwera źródłowego

> [!IMPORTANT]
> Usunięcie serwera źródłowego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera źródłowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer źródłowy z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz swój źródłowy serwer Azure Database for MySQL.

2. W obszarze **Przegląd**wybierz pozycję **Usuń**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL — replikacja":::

3. Wpisz nazwę serwera źródłowego, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie serwera źródłowego.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL — replikacja":::

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer repliki Azure Database for MySQL, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **metryki**:

3. Wybierz pozycję **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Azure Database for MySQL — replikacja":::

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obraz wybiera 30-minutowy zakres czasu.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Azure Database for MySQL — replikacja":::

5. Wyświetl zwłokę replikacji dla wybranego zakresu czasu. Na poniższej ilustracji przedstawiono ostatnie 30 minut.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Azure Database for MySQL — replikacja":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
