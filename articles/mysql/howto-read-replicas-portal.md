---
title: Zarządzanie replikami odczytu — Azure Database for MySQL Azure Portal
description: Dowiedz się, jak skonfigurować repliki odczytu i zarządzać nimi w Azure Database for MySQL przy użyciu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 17771cfa9ffa73d22d2e136836dcf0f19e68f8cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707212"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL przy użyciu Azure Portal

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi w usłudze Azure Database for MySQL przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) , który będzie używany jako serwer główny.

> [!IMPORTANT]
> Funkcja odczytu repliki jest dostępna tylko dla serwerów Azure Database for MySQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer główny znajduje się w jednej z tych warstw cenowych.

## <a name="create-a-read-replica"></a>Tworzenie repliki odczytu

> [!IMPORTANT]
> Gdy tworzysz replikę dla wzorca, który nie ma istniejących replik, wzorzec zostanie najpierw uruchomiony ponownie w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejący serwer Azure Database for MySQL, który ma być używany jako główny. Ta akcja powoduje otwarcie strony **Przegląd** .

3. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

4. Wybierz pozycję **Dodaj replikę**.

   ![Azure Database for MySQL — replikacja](./media/howto-read-replica-portal/add-replica.png)

5. Wprowadź nazwę serwera repliki.

    ![Azure Database for MySQL — nazwa repliki](./media/howto-read-replica-portal/replica-name.png)

6. Wybierz lokalizację serwera repliki. Lokalizacja domyślna jest taka sama jak w przypadku serwera głównego.

    ![Azure Database for MySQL — lokalizacja repliki](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

7. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co serwer główny. Konfigurację serwera repliki można zmienić po jego utworzeniu. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów i tej samej subskrypcji co serwer główny. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż wzorzec, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

Po utworzeniu serwera repliki można go wyświetlić w bloku **replikacja** .

   ![Repliki list Azure Database for MySQL](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między serwerem głównym a repliką nie można jej cofnąć. Serwer repliki stał się serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Nie można ponownie wykonać tego serwera w replice.

Aby zatrzymać replikację między wzorcem i serwerem repliki z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MySQL. 

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   ![Azure Database for MySQL — zatrzymywanie replikacji wybierz serwer](./media/howto-read-replica-portal/stop-replication-select.png)

4. Wybierz pozycję **Zatrzymaj replikację**.

   ![Azure Database for MySQL — zatrzymywanie replikacji](./media/howto-read-replica-portal/stop-replication.png)

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   ![Azure Database for MySQL — potwierdzenie zatrzymywania replikacji](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MySQL.

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   ![Azure Database for MySQL — usuwanie repliki wybierz serwer](./media/howto-read-replica-portal/delete-replica-select.png)

4. Wybierz pozycję **Usuń replikę**

   ![Azure Database for MySQL-Usuń replikę](./media/howto-read-replica-portal/delete-replica.png)

5. Wpisz nazwę repliki, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie repliki.  

   ![Potwierdzenie usunięcia repliki Azure Database for MySQL](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Usuwanie serwera głównego

> [!IMPORTANT]
> Usunięcie serwera głównego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera głównego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer główny z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz serwer główny Azure Database for MySQL.

2. W obszarze **Przegląd**wybierz pozycję **Usuń**.

   ![Azure Database for MySQL-Usuń wzorzec](./media/howto-read-replica-portal/delete-master-overview.png)

3. Wpisz nazwę serwera głównego, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie serwera głównego.  

   ![Azure Database for MySQL-Usuń wzorzec](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer repliki Azure Database for MySQL, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **metryki**:

3. Wybierz pozycję **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   ![Wybierz opóźnienie replikacji](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obraz wybiera 30-minutowy zakres czasu.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Wyświetl zwłokę replikacji dla wybranego zakresu czasu. Na poniższej ilustracji przedstawiono ostatnie 30 minut.

   ![Wybierz zakres czasu](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)
