---
title: Uaktualnienie wersji głównej w Azure Database for MySQL-pojedynczym serwerze
description: W tym artykule opisano, jak uaktualnić wersję główną dla Azure Database for MySQL-pojedynczego serwera
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 13cf315291cdf788951e352e430976851b30ce0c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216721"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Uaktualnienie wersji głównej w Azure Database for MySQL pojedynczym serwerze

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
>

> [!IMPORTANT]
> Uaktualnienie wersji głównej dla pojedynczego serwera usługi Azure Database for MySQL jest w publicznej wersji zapoznawczej.

W tym artykule opisano, jak można uaktualnić wersję główną programu MySQL w miejscu Azure Database for MySQL jednym serwerze.

Ta funkcja umożliwi klientom wykonywanie uaktualnień w miejscu serwerów MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu kliknięcia przycisku bez przenoszenia danych lub konieczności zmiany parametrów połączenia aplikacji.

> [!Note]
> * Uaktualnienie wersji głównej jest dostępne tylko w przypadku uaktualnienia wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7.
> * Serwer będzie niedostępny podczas całej operacji uaktualniania. W związku z tym zaleca się przeprowadzanie uaktualnień podczas planowanego okna obsługi. Można rozważyć [przeprowadzenie minimalnego przestoju wersji głównej z programu mysql 5,6 do bazy danych mysql 5,7 przy użyciu repliki odczytu.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Wykonaj uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu Azure Portal

Wykonaj następujące kroki, aby przeprowadzić uaktualnienie wersji głównej usługi Azure Database of MySQL 5,6 przy użyciu Azure Portal

> [!IMPORTANT]
> Zalecamy najpierw przeprowadzić uaktualnienie na przywróconej kopii serwera, a nie bezpośrednio uaktualnianiu produkcji. Zobacz [, jak wykonać przywracanie do punktu w czasie](howto-restore-server-portal.md#point-in-time-restore).

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer Azure Database for MySQL 5,6.

2. Na stronie **Przegląd** kliknij przycisk **Uaktualnij** na pasku narzędzi.

3. W sekcji **Uaktualnij** wybierz pozycję **OK** , aby uaktualnić serwer usługi Azure Database for MySQL 5,6 do wersji 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL — Omówienie — uaktualnienie":::

4. Powiadomienie zostanie potwierdzone, że uaktualnienie zakończyło się pomyślnie.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Wykonaj uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące kroki, aby przeprowadzić uaktualnienie wersji głównej serwera usługi Azure Database of MySQL 5,6 przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Zalecamy najpierw przeprowadzić uaktualnienie na przywróconej kopii serwera, a nie bezpośrednio uaktualnianiu produkcji. Zobacz [, jak wykonać przywracanie do punktu w czasie](howto-restore-server-cli.md#server-point-in-time-restore).

1. Zainstaluj [interfejs wiersza polecenia platformy Azure dla systemu Windows](/cli/azure/install-azure-cli) lub użyj interfejsu wiersza polecenia platformy azure w [Azure Cloud Shell](../cloud-shell/overview.md) , aby uruchomić polecenia uaktualniania. 
 
   To uaktualnienie wymaga wersji 2.16.0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. Uruchom polecenie az version, aby znaleźć zainstalowane wersje i biblioteki zależne. Aby uaktualnić do najnowszej wersji, uruchom polecenie az upgrade.

2. Po zalogowaniu Uruchom polecenie [AZ MySQL Server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   W wierszu polecenia zostanie wyświetlony komunikat "-Runningd". Gdy ten komunikat nie zostanie już wyświetlony, uaktualnienie wersji zostanie ukończone.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Wykonaj uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7 w odniesieniu do repliki odczytu przy użyciu Azure Portal

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejący serwer repliki Azure Database for MySQL 5,6 odczytu.

2. Na stronie **Przegląd** kliknij przycisk **Uaktualnij** na pasku narzędzi.

3. W sekcji **Uaktualnij** wybierz pozycję **OK** , aby uaktualnić serwer repliki usługi Azure Database for MySQL 5,6 do serwera 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL — Omówienie — uaktualnienie":::

4. Powiadomienie zostanie potwierdzone, że uaktualnienie zakończyło się pomyślnie.

5. Na stronie **Przegląd** Sprawdź, czy wersja serwera repliki usługi Azure Database for MySQL jest w wersji 5,7.

6. Teraz przejdź do serwera podstawowego i wykonaj na nim [uaktualnienie wersji głównej](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) .

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Przeprowadzaj minimalny czas przestoju uaktualnienie wersji głównej z programu MySQL 5,6 do bazy danych MySQL 5,7 przy użyciu replik odczytu

Za pomocą replik odczytu można przeprowadzić uaktualnienie wersji głównej oprogramowania MySQL 5,6 do bazy danych MySQL 5,7. Pomysłem jest uaktualnienie repliki serwera do 5,7 pierwszej i późniejszej pracy w trybie failover w celu odczytania repliki i utworzenia nowej głównej.

1. W [Azure Portal](https://portal.azure.com/)wybierz istniejące Azure Database for MySQL 5,6.

2. Utwórz [replikę odczytu](./concepts-read-replicas.md#create-a-replica) z serwera podstawowego.

3. [Uaktualnij replikę Read](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) do wersji 5,7.

4. Po upewnieniu się, że serwer repliki działa w wersji 5,7, Zatrzymaj połączenie aplikacji z serwerem podstawowym.
 
5. Sprawdź stan replikacji i upewnij się, że replika została przechwycona z podstawową, aby wszystkie dane były zsynchronizowane i upewnij się, że nie ma żadnych nowych operacji w elemencie podstawowym.

   Wywołaj [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) polecenie na serwerze repliki, aby wyświetlić stan replikacji.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Jeśli stan i ma `Slave_IO_Running` `Slave_SQL_Running` wartość "yes", a wartością `Seconds_Behind_Master` jest "0", replikacja działa prawidłowo. `Seconds_Behind_Master` wskazuje, jak późna jest replika. Jeśli wartość nie jest równa "0", oznacza to, że replika przetwarza aktualizacje. Po potwierdzeniu `Seconds_Behind_Master` "0" można bezpiecznie zatrzymać replikację.

6. Przekształć replikę odczytu na podstawową, [zatrzymując replikację](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server).

7. Wskaż aplikację nową podstawową (wcześniejszą replikę), na której działa serwer 5,7. Każdy serwer ma unikatowe parametry połączenia. Zaktualizuj swoją aplikację, tak aby wskazywała na (dawniej) replikę, a nie źródłową.

> [!Note]
> Ten scenariusz będzie miał przestoje w przypadku kroków 4, 5 i 6.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Kiedy ta funkcja uaktualnienia będzie dostępna w wersji zaciągającej się na platformie MySQL w środowisku produkcyjnym, którą będziemy musieli uaktualnić?

Ta funkcja jest planowana przed wycofaniem z programu MySQL v 5.6. Jednak funkcja jest gotowa do produkcji i jest w pełni obsługiwana przez platformę Azure, więc należy ją uruchamiać z zachowaniem zaufania do środowiska. Zalecanym najlepszym rozwiązaniem jest wykonanie i przetestowanie go najpierw w przywróconej kopii serwera, aby można było oszacować przestoje podczas uaktualniania i przeprowadzić test zgodności aplikacji przed uruchomieniem go w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [jak wykonać przywracanie do punktu w czasie](howto-restore-server-portal.md#point-in-time-restore) , aby utworzyć kopię punktu w czasie dla serwera. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Czy spowoduje to przestoje serwera, a jeśli tak, jak długo?

Tak. serwer będzie niedostępny w trakcie procesu uaktualniania, dlatego zalecamy wykonanie tej operacji podczas planowanego okna obsługi. Szacowany czas przestoju zależy od rozmiaru bazy danych, obsługiwanego rozmiaru magazynu (alokowanej liczby operacji we/wy) i liczbie tabel w bazie danych. Czas uaktualnienia jest bezpośrednio proporcjonalny do liczby tabel na serwerze. Uaktualnienia podstawowych serwerów SKU powinny trwać dłużej niż w przypadku standardowej platformy magazynu. Aby oszacować przestoje środowiska serwera, zalecamy najpierw przeprowadzić uaktualnienie na przywróconej kopii serwera. Rozważ [przeprowadzenie minimalnego czasu przestoju uaktualnienie wersji głównej z programu mysql 5,6 do bazy danych mysql 5,7 przy użyciu repliki odczytu.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Co się stanie, jeśli nie zdecydujesz się na uaktualnienie naszego serwera MySQL v 5.6 przed 5 lutego 2021?

Nadal możesz korzystać z serwera MySQL v 5.6 jako wcześniej. Na serwerze Azure **nigdy nie zostanie** wymuszone uaktualnienie. Zostaną jednak zastosowane ograniczenia udokumentowane w [zasadach dotyczących wersji Azure Database for MySQL](concepts-version-policy.md) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zasadach dotyczących wersji Azure Database for MySQL](concepts-version-policy.md).