---
title: Tworzenie kopii zapasowych i przywracanie Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób przywracania serwera w Azure Database for MariaDB przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 4a5f2cc4cbf73f5c13533a94f1454022d3538880
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539629"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak utworzyć kopię zapasową i przywrócić serwer w Azure Database for MariaDB przy użyciu Azure Portal

## <a name="backup-happens-automatically"></a>Kopia zapasowa odbywa się automatycznie
Kopie zapasowe serwerów Azure Database for MariaDB są podejmowane okresowo w celu włączenia funkcji przywracania. Korzystając z tej funkcji, można przywrócić serwer i wszystkie jego bazy danych do wcześniejszego punktu w czasie na nowym serwerze.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, musisz:
- [Serwer Azure Database for MariaDB i baza danych](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Ustawianie konfiguracji kopii zapasowej

Należy wybrać między konfigurowaniem serwera do lokalnie nadmiarowych kopii zapasowych lub geograficznie nadmiarowych kopii zapasowych podczas tworzenia serwera, w oknie **warstwa cenowa** .

> [!NOTE]
> Po utworzeniu serwera, jego rodzaju nadmiarowości, czyli geograficznie nadmiarowej a lokalnie nadmiarowy, nie można przełączyć.
>

Podczas tworzenia serwera za pomocą Azure Portal okno **warstwy cenowej** to miejsce, w którym można wybrać **lokalnie nadmiarowe** lub **geograficznie nadmiarowe** kopie zapasowe dla serwera. To okno służy również do wybierania **okresu przechowywania kopii zapasowej** — jak długo (w dniach) mają być przechowywane kopie zapasowe serwera.

   ![Warstwa cenowa — Wybieranie nadmiarowości kopii zapasowej](./media/howto-restore-server-portal/pricing-tier.png)

Aby uzyskać więcej informacji na temat ustawiania tych wartości podczas tworzenia, zobacz [Azure Database for MariaDB Server — szybki start](quickstart-create-mariadb-server-database-using-azure-portal.md).

Okres przechowywania kopii zapasowej można zmienić na serwerze, wykonując następujące czynności:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz serwer Azure Database for MariaDB. Ta akcja powoduje otwarcie strony **Przegląd** .

3. Wybierz pozycję **warstwa cenowa** z menu, w obszarze **Ustawienia**. Za pomocą suwaka można zmienić **okres przechowywania kopii zapasowej** na preferencję od 7 do 35 dni.
Na poniższym zrzucie ekranu został zwiększony do 35 dni.
![Zwiększono okres przechowywania kopii zapasowej](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Kliknij przycisk **OK** , aby potwierdzić zmianę.

Okres przechowywania kopii zapasowej decyduje o tym, jak daleko w czasie można pobrać przywracanie do punktu w czasie, ponieważ jest ono oparte na dostępnych kopiach zapasowych. Przywracanie do określonego momentu zostało opisane w następnej sekcji. 

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu
Azure Database for MariaDB umożliwia przywrócenie serwera z powrotem do punktu w czasie i do nowej kopii serwera. Możesz użyć tego nowego serwera do odzyskania danych lub, aby aplikacje klienckie wskazywały na ten nowy serwer.

Na przykład jeśli tabela została przypadkowo porzucona dzisiaj, można przywrócić jej czas przed południem i pobrać brakującą tabelę i dane z tej nowej kopii serwera. Przywracanie do punktu w czasie jest na poziomie serwera, a nie na poziomie bazy danych.

Poniższe kroki umożliwiają przywrócenie przykładowego serwera do punktu w czasie:
1. W Azure Portal wybierz serwer Azure Database for MariaDB. 

2. Na pasku narzędzi na stronie **Przegląd** serwera wybierz pozycję **Przywróć**.

   ![Azure Database for MariaDB — przegląd — przycisk Przywróć](./media/howto-restore-server-portal/2-server.png)

3. Wypełnij formularz Przywracanie wymaganymi informacjami:

   ![Azure Database for MariaDB — przywracanie informacji](./media/howto-restore-server-portal/3-restore.png)
   - **Punkt przywracania** : Wybierz punkt w czasie, do którego chcesz wykonać przywracanie.
   - **Serwer docelowy** : Podaj nazwę nowego serwera.
   - **Lokalizacja** : nie można wybrać regionu. Domyślnie jest to taka sama jak w przypadku serwera źródłowego.
   - **Warstwa cenowa** : nie można zmienić tych parametrów podczas wykonywania przywracania do punktu w czasie. Jest taka sama jak w przypadku serwera źródłowego. 

4. Kliknij przycisk **OK** , aby przywrócić serwer do punktu w czasie. 

5. Po zakończeniu przywracania Znajdź nowy serwer, który został utworzony w celu zweryfikowania, że dane zostały przywrócone zgodnie z oczekiwaniami.

Nowy serwer utworzony przez Przywracanie do punktu w czasie ma taką samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w wybranym momencie. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="geo-restore"></a>Przywracanie geograficzne

Jeśli serwer został skonfigurowany pod kątem kopii zapasowych geograficznie nadmiarowych, można utworzyć nowy serwer z kopii zapasowej istniejącego serwera. Ten nowy serwer można utworzyć w dowolnym regionie, który Azure Database for MariaDB jest dostępny.  

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu. Wybierz pozycję **bazy danych**  >  **Azure Database for MariaDB**.

   :::image type="content" source="./media/howto-restore-server-portal/2_navigate-to-mariadb.png" alt-text="Przejdź do Azure Database for MariaDB.":::
 
2. Podaj subskrypcję, grupę zasobów i nazwę nowego serwera. 

3. Wybierz pozycję **kopia zapasowa** jako **Źródło danych**. Ta akcja powoduje załadowanie listy rozwijanej, która zawiera listę serwerów, na których włączono geograficznie nadmiarowe kopie zapasowe.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Wybierz pozycję źródło danych.":::
    
   > [!NOTE]
   > Gdy serwer jest tworzony po raz pierwszy, może nie być od razu dostępny do przywracania geograficznego. Wypełnienie wymaganych metadanych może potrwać kilka godzin.
   >

4. Wybierz listę rozwijaną **kopia zapasowa** .
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Wybierz listę rozwijaną kopia zapasowa.":::

5. Wybierz serwer źródłowy, z którego chcesz wykonać przywracanie.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Wybierz pozycję Kopia zapasowa.":::

6. Serwer będzie domyślnie mieć wartości dla liczby **rdzeni wirtualnych** , **okresu przechowywania kopii zapasowej** , **opcji nadmiarowości kopii zapasowej** , **wersji aparatu** i **poświadczeń administratora**. Wybierz opcję **Kontynuuj**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Kontynuuj tworzenie kopii zapasowej.":::

7. Wypełnij resztę formularza z preferencjami. Można wybrać dowolną **lokalizację**.

    Po wybraniu lokalizacji możesz wybrać pozycję **Konfiguruj serwer** , aby zaktualizować **generowanie obliczeń** (jeśli jest dostępna w wybranym regionie), liczbę **rdzeni wirtualnych** , **okres przechowywania kopii zapasowych** i **opcję nadmiarowości kopii zapasowych**. Zmiana **warstwy cenowej** (podstawowa, ogólnego przeznaczenia lub zoptymalizowana pod kątem pamięci) lub rozmiaru **magazynu** podczas przywracania nie jest obsługiwana.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Wypełnij formularz."::: 

8. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. 

9. Wybierz pozycję **Utwórz** , aby aprowizować serwer. Ta operacja może potrwać kilka minut.

Nowy serwer utworzony przy użyciu przywracania geograficznego ma tę samą nazwę logowania administratora serwera i hasło, które były prawidłowe dla istniejącego serwera w momencie zainicjowania przywracania. Hasło można zmienić na stronie **Przegląd** nowego serwera.

Nowy serwer utworzony podczas przywracania nie ma punktów końcowych usługi sieci wirtualnej, które istniały na oryginalnym serwerze. Te reguły należy skonfigurować oddzielnie dla tego nowego serwera. Przywrócono reguły zapory z oryginalnego serwera.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych](concepts-backup.md) usługi
- Informacje o [replikach](concepts-read-replicas.md)
- Dowiedz się więcej o opcjach [ciągłości biznesowej](concepts-business-continuity.md)