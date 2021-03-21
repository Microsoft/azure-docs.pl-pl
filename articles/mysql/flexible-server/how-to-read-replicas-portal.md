---
title: Zarządzanie replikami odczytu — Azure Portal-Azure Database for MySQL-elastyczny serwer
description: Informacje na temat konfigurowania replik odczytu i zarządzania nimi w Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: d33734dc7404e49aed94dffae8644b2bc4386925
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492832"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal

> [!IMPORTANT]
> Odczytaj repliki w Azure Database for MySQL-elastycznym serwerze jest w wersji zapoznawczej.

W tym artykule przedstawiono sposób tworzenia replik odczytu i zarządzania nimi na Azure Database for MySQL elastycznym serwerze przy użyciu Azure Portal.

> [!Note]
> Replika nie jest obsługiwana na serwerze z włączonym wysoką dostępnością. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Elastyczny serwer Azure Database for MySQL](quickstart-create-server-portal.md) serwera, który będzie używany jako serwer źródłowy.

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu

> [!IMPORTANT]
> Podczas tworzenia repliki dla źródła, które nie ma istniejących replik, źródło zostanie najpierw ponownie uruchomione w celu przygotowania się do replikacji. Należy wziąć pod uwagę i wykonać te operacje w okresie poza szczytem.

Serwer repliki odczytu można utworzyć, wykonując następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

2. Wybierz istniejący Azure Database for MySQL elastyczny serwer, który ma być używany jako źródło. Ta akcja powoduje otwarcie strony **Przegląd** .

3. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

4. Wybierz pozycję **Dodaj replikę**.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL — replikacja":::

5. Wprowadź nazwę serwera repliki.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL — nazwa repliki":::

6. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

> [!NOTE]
> Repliki odczytu są tworzone z tą samą konfiguracją serwera co źródło. Konfigurację serwera repliki można zmienić po jego utworzeniu. Serwer repliki jest zawsze tworzony w tej samej grupie zasobów, w tej samej lokalizacji i tej samej subskrypcji co serwer źródłowy. Jeśli chcesz utworzyć serwer repliki w innej grupie zasobów lub innej subskrypcji, możesz [przenieść serwer repliki](../../azure-resource-manager/management/move-resource-group-and-subscription.md) po utworzeniu. Zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie zachować ze źródłem.

Po utworzeniu serwera repliki można go wyświetlić w bloku **replikacja** .

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Repliki list Azure Database for MySQL":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Zatrzymaj replikację do serwera repliki

> [!IMPORTANT]
> Zatrzymywanie replikacji na serwerze jest nieodwracalne. Po zatrzymaniu replikacji między źródłem a repliką nie można jej cofnąć. Serwer repliki stał się serwerem autonomicznym i obsługuje teraz zarówno odczyt, jak i zapis. Nie można ponownie wykonać tego serwera w replice.

Aby zatrzymać replikację między źródłem a serwerem repliki z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz źródło Azure Database for MySQL serwerze elastycznym. 

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL — zatrzymywanie replikacji wybierz serwer":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Wybierz pozycję **Zatrzymaj replikację**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL — zatrzymywanie replikacji":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Potwierdź, że chcesz zatrzymać replikację, klikając przycisk **OK**.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL — potwierdzenie zatrzymywania replikacji":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Usuwanie serwera repliki

Aby usunąć serwer repliki odczytu z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz źródło Azure Database for MySQL serwerze elastycznym.

2. Wybierz opcję **replikacja** z menu, w obszarze **Ustawienia**.

3. Wybierz serwer repliki, który chcesz usunąć.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL — usuwanie repliki wybierz serwer":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Wybierz pozycję **Usuń replikę**

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL-Usuń replikę":::

5. Wpisz nazwę repliki, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie repliki.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Potwierdzenie usunięcia repliki Azure Database for MySQL":::

## <a name="delete-a-source-server"></a>Usuwanie serwera źródłowego

> [!IMPORTANT]
> Usunięcie serwera źródłowego powoduje zatrzymanie replikacji do wszystkich serwerów repliki i usunięcie samego serwera źródłowego. Serwery repliki stają się serwerami autonomicznymi, które teraz obsługują zarówno odczyt, jak i zapis.

Aby usunąć serwer źródłowy z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz źródło Azure Database for MySQL serwerze elastycznym.

2. W obszarze **Przegląd** wybierz pozycję **Usuń**.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL-Usuń Źródło":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Wpisz nazwę serwera źródłowego, a następnie kliknij przycisk **Usuń** , aby potwierdzić usunięcie serwera źródłowego.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL — Potwierdź usunięcie źródła":::

## <a name="monitor-replication"></a>Monitorowanie replikacji

1. W [Azure Portal](https://portal.azure.com/)wybierz replikę Azure Database for MySQL serwerze elastycznym, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **metryki**:

3. Wybierz pozycję **opóźnienie replikacji w sekundach** z listy rozwijanej dostępnych metryk.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Wybierz opóźnienie replikacji":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Wybierz zakres czasu, który chcesz wyświetlić. Poniższy obraz wybiera 30-minutowy zakres czasu.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Wybierz zakres czasu":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Wyświetl zwłokę replikacji dla wybranego zakresu czasu. Na poniższej ilustracji przedstawiono ostatnie 30 minut.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Wybierz zakres czasu 30 minut":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [odczytu replik](concepts-read-replicas.md)