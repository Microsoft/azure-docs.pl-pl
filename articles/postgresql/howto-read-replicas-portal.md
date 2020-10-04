---
title: Zarządzanie replikami odczytu — Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak zarządzać replikami odczytu Azure Database for PostgreSQL-pojedynczym serwerem z Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 08d1d393b4ba52e6feeb36c0538f2664e1407d38
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708292"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Tworzenie replik odczytu i zarządzanie nimi w Azure Database for PostgreSQL-pojedynczym serwerze z Azure Portal

W tym artykule dowiesz się, jak tworzyć repliki odczytu i zarządzać nimi w Azure Database for PostgreSQL z Azure Portal. Aby dowiedzieć się więcej o replikach odczytu, zobacz [Omówienie](concepts-read-replicas.md).


## <a name="prerequisites"></a>Wymagania wstępne
[Serwer Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) być serwerem podstawowym.

## <a name="azure-replication-support"></a>Obsługa replikacji platformy Azure

[Odczytywanie replik](concepts-read-replicas.md) i [dekodowanie logiczne](concepts-logical.md) są zależne od dziennika Postgres zapisu (WAL) w celu uzyskania informacji. Te dwie funkcje wymagają różnych poziomów rejestrowania z Postgres. Dekodowanie logiczne wymaga wyższego poziomu rejestrowania niż repliki odczytu.

Aby skonfigurować odpowiedni poziom rejestrowania, użyj parametru Obsługa replikacji platformy Azure. Obsługa replikacji platformy Azure ma trzy opcje ustawień:

* **Wyłączone** — umieszcza najniższe informacje w Wal. To ustawienie nie jest dostępne na większości serwerów Azure Database for PostgreSQL.  
* **Replika** — większa niż **wyłączona**. Jest to minimalny poziom rejestrowania, który jest wymagany do działania [replik odczytu](concepts-read-replicas.md) . To ustawienie jest domyślne na większości serwerów.
* **Logiczne** — więcej informacji niż **replika**. Jest to minimalny poziom rejestrowania kodu logicznego do pracy. Odczytaj repliki również działają w tym ustawieniu.

Po zmianie tego parametru należy ponownie uruchomić serwer. Wewnętrznie, ten parametr ustawia parametry Postgres `wal_level` , `max_replication_slots` i `max_wal_senders` .

## <a name="prepare-the-primary-server"></a>Przygotuj serwer podstawowy

1. W Azure Portal wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako główny.

2. Z menu serwer wybierz pozycję **replikacja**. Jeśli Obsługa replikacji platformy Azure została ustawiona na co najmniej **replikę**, można utworzyć repliki odczytu. 

3. Jeśli Obsługa replikacji platformy Azure nie jest ustawiona na co najmniej **replikę**, ustaw ją. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

4. Uruchom ponownie serwer, aby zastosować zmiany, wybierając opcję **tak**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

5. Po zakończeniu operacji otrzymasz dwie Azure Portal powiadomienia. Istnieje jedno powiadomienie dotyczące aktualizowania parametru serwera. Istnieje inne powiadomienie dotyczące ponownego uruchomienia serwera, które następuje natychmiast.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

6. Odśwież stronę Azure Portal, aby zaktualizować pasek narzędzi replikacji. Teraz można tworzyć repliki odczytu dla tego serwera.
   

## <a name="create-a-read-replica"></a>Tworzenie repliki do odczytu
Aby utworzyć replikę odczytu, wykonaj następujące kroki:

1. Wybierz istniejący serwer Azure Database for PostgreSQL, który ma być używany jako serwer podstawowy. 

2. Na pasku bocznym serwera w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz pozycję **Dodaj replikę**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

4. Wprowadź nazwę repliki do odczytu. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

5. Wybierz lokalizację dla repliki. Lokalizacja domyślna jest taka sama jak na serwerze podstawowym.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::

   > [!NOTE]
   > Aby dowiedzieć się więcej na temat regionów, w których można utworzyć replikę, zapoznaj się z [artykułem dotyczącym pojęć dotyczących repliki](concepts-read-replicas.md). 

6. Wybierz **przycisk OK** , aby potwierdzić utworzenie repliki.

Po utworzeniu repliki odczytu można ją wyświetlić w oknie **replikacja** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 

> [!IMPORTANT]
> Zapoznaj się z [sekcją zagadnienia w temacie Omówienie repliki odczytu](concepts-read-replicas.md#considerations).
>
> Przed zaktualizowaniem ustawienia serwera podstawowego do nowej wartości należy zaktualizować ustawienie repliki na wartość równą lub większą. Ta akcja ułatwia przeprowadzenie replikacji ze wszystkimi zmianami wprowadzonymi do wzorca.

## <a name="stop-replication"></a>Zatrzymywanie replikacji
Można zatrzymać replikację między serwerem podstawowym a repliką odczytu.

> [!IMPORTANT]
> Po zatrzymaniu replikacji na serwer podstawowy i replikę odczytu nie można jej cofnąć. Replika odczytu jest autonomicznym serwerem, który obsługuje zarówno operacje odczytu, jak i zapisu. Serwer autonomiczny nie może zostać ponownie utworzony w replice.

Aby zatrzymać replikację między serwerem podstawowym a repliką odczytu z Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz swój podstawowy serwer Azure Database for PostgreSQL.

2. W menu serwer w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz serwer repliki, dla którego ma zostać zatrzymana replikacja.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
4. Wybierz pozycję **Zatrzymaj replikację**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
5. Wybierz **przycisk OK** , aby zatrzymać replikację.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 

## <a name="delete-a-primary-server"></a>Usuwanie serwera podstawowego
Aby usunąć serwer podstawowy, należy wykonać te same czynności co w celu usunięcia autonomicznego serwera Azure Database for PostgreSQL. 

> [!IMPORTANT]
> Po usunięciu serwera podstawowego replikacja do wszystkich replik odczytu zostanie zatrzymana. Repliki odczytu stają się autonomicznymi serwerami, które teraz obsługują operacje odczytu i zapisu.

Aby usunąć serwer z Azure Portal, wykonaj następujące kroki:

1. W Azure Portal wybierz swój podstawowy serwer Azure Database for PostgreSQL.

2. Otwórz stronę **Przegląd** dla serwera. Wybierz pozycję **Usuń**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
3. Wprowadź nazwę serwera podstawowego do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie serwera podstawowego.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 

## <a name="delete-a-replica"></a>Usuwanie repliki
Replikę odczytu można usunąć podobnie jak w przypadku usuwania serwera podstawowego.

- W Azure Portal Otwórz stronę **Przegląd** repliki odczytu. Wybierz pozycję **Usuń**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
Możesz również usunąć replikę odczytu z okna **replikacji** , wykonując następujące czynności:

1. W Azure Portal wybierz swój podstawowy serwer Azure Database for PostgreSQL.

2. W menu serwer w obszarze **Ustawienia**wybierz pozycję **replikacja**.

3. Wybierz replikę do odczytania do usunięcia.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
4. Wybierz pozycję **Usuń replikę**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
5. Wprowadź nazwę repliki do usunięcia. Wybierz pozycję **Usuń** , aby potwierdzić usunięcie repliki.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 

## <a name="monitor-a-replica"></a>Monitorowanie repliki
Do monitorowania replik odczytu są dostępne dwie metryki.

### <a name="max-lag-across-replicas-metric"></a>Metryka maksymalnego opóźnienia między replikami
Metryka **maks. opóźnienie między replikami** pokazuje opóźnienie w bajtach między serwerem podstawowym a repliką najbardziej opóźnioną. 

1.  W Azure Portal Wybierz podstawowy serwer Azure Database for PostgreSQL.

2.  Wybierz pozycję **Metryki**. W oknie **metryki** wybierz pozycję **maks. opóźnienie między replikami**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
3.  Dla **agregacji**wybierz **wartość Max**.


### <a name="replica-lag-metric"></a>Metryka opóźnienia repliki
Metryka **opóźnienia repliki** przedstawia czas od ostatniego odtworzenia transakcji w replice. Jeśli na wzorcu nie ma żadnych transakcji, Metryka uwzględnia ten czas opóźnienia.

1. W Azure Portal wybierz Azure Database for PostgreSQL Odczytaj replikę.

2. Wybierz pozycję **Metryki**. W oknie **metryki** wybierz pozycję **opóźnienie repliki**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Azure Database for PostgreSQL — replikacja zestawu i zapisywanie":::
 
3. Dla **agregacji**wybierz **wartość Max**. 
 
## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [odczytaniu replik w Azure Database for PostgreSQL](concepts-read-replicas.md).
* Dowiedz się, jak [tworzyć repliki odczytu i zarządzać nimi w interfejsie wiersza polecenia platformy Azure i interfejsu API REST](howto-read-replicas-cli.md).