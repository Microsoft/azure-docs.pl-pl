---
title: Tworzenie kopii zapasowej aplikacji
description: Dowiedz się, jak tworzyć kopie zapasowe aplikacji w usłudze Azure App Service. Uruchom ręczne lub zaplanowane kopie zapasowe. Dostosuj kopie zapasowe, dołączając dołączoną bazę danych.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b812ae10b3462dbeff05c8a67e7ebb725281e7e8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535761"
---
# <a name="back-up-your-app-in-azure"></a>Tworzenie kopii zapasowej aplikacji na platformie Azure
Funkcja tworzenia kopii zapasowych i przywracania w [usłudze Azure App Service](overview.md) umożliwia łatwe tworzenie kopii zapasowych aplikacji ręcznie lub zgodnie z harmonogramem. Można skonfigurować kopie zapasowe, które mają być przechowywane do nieokreślonej ilości czasu. Możesz przywrócić aplikację do migawki poprzedniego stanu, zastępując istniejącą aplikację lub przywracając ją do innej aplikacji.

Aby uzyskać informacje na temat przywracania aplikacji z kopii zapasowej, zobacz Przywracanie aplikacji na [platformie Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Co zostanie utworzone z kopii zapasowej
Usługa App Service można wyw kopii zapasowej następujących informacji do konta magazynu platformy Azure i kontenera, który został skonfigurowany aplikacji do użycia. 

* Konfiguracja aplikacji
* Zawartość pliku
* Baza danych połączona z aplikacją

Następujące rozwiązania bazy danych są obsługiwane z funkcją tworzenia kopii zapasowych: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL w aplikacji](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Każda kopia zapasowa jest kompletną kopią aplikacji w trybie offline, a nie aktualizacją przyrostową.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Wymagania i ograniczenia
* Funkcja tworzenia kopii zapasowych i przywracania wymaga, aby plan usługi aplikacji był w warstwie **Standardowa** lub **Warstwa Premium.** Aby uzyskać więcej informacji na temat skalowania planu usługi App Service w celu użycia wyższej warstwy, zobacz [Skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md). **Warstwa Premium** umożliwia większą liczbę dziennych kopii zapasowych niż **warstwa standardowa.**
* Potrzebujesz konta magazynu platformy Azure i kontenera w tej samej subskrypcji co aplikacja, której chcesz wykonać. Aby uzyskać więcej informacji na temat kont magazynu platformy Azure, zobacz [omówienie konta magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Kopie zapasowe mogą mieć do 10 GB zawartości aplikacji i bazy danych. Jeśli rozmiar kopii zapasowej przekracza ten limit, zostanie wyświetlony błąd.
* Kopie zapasowe usługi Azure Database z włączoną usługą Azure Database dla mysql nie są obsługiwane. Jeśli kopia zapasowa jest skonfigurowana, otrzymasz nieudane kopie zapasowe.
* Kopie zapasowe usługi Azure Database z włączoną usługą TLS dla postgreSQL nie są obsługiwane. Jeśli kopia zapasowa jest skonfigurowana, otrzymasz nieudane kopie zapasowe.
* W aplikacji bazy danych MySQL są automatycznie archiwizowane bez żadnej konfiguracji. Jeśli ręcznie ustawienia baz danych MySQL w aplikacji, takie jak dodawanie ciągów połączeń, kopie zapasowe mogą nie działać poprawnie.
* Użycie konta magazynu z włączoną zaporą jako miejsca docelowego kopii zapasowych nie jest obsługiwane. Jeśli kopia zapasowa jest skonfigurowana, otrzymasz nieudane kopie zapasowe.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej
1. W [witrynie Azure portal](https://portal.azure.com)przejdź do strony aplikacji wybierz pozycję **Kopie zapasowe**. Zostanie wyświetlona strona **Kopie zapasowe.**

    ![Strona Kopia zapasowa](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Jeśli zostanie wyświetlony następujący komunikat, kliknij go, aby uaktualnić plan usługi app service, zanim będzie można kontynuować tworzenie kopii zapasowych.
    > Aby uzyskać więcej informacji, zobacz [Skalowanie aplikacji na platformie Azure](manage-scale-up.md).
    > ![Wybieranie konta magazynu](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. Na stronie **Kopia zapasowa** wybierz **pozycję Kopia zapasowa nie jest skonfigurowana. Kliknij tutaj, aby skonfigurować kopię zapasową aplikacji**.

    ![Kliknij pozycję Konfiguruj.](./media/manage-backup/configure-start.png)

3. Na stronie **Konfiguracja kopii zapasowej** kliknij pozycję **Magazyn nie skonfigurowany** do konfigurowania konta magazynu.

    ![Wybieranie konta magazynu](./media/manage-backup/configure-storage.png)

4. Wybierz miejsce docelowe kopii zapasowej, wybierając **konto magazynu** i **kontener**. Konto magazynu musi należeć do tej samej subskrypcji co aplikacja, której chcesz skonsuwować. Jeśli chcesz, możesz utworzyć nowe konto magazynu lub nowy kontener na odpowiednich stronach. Po zakończeniu kliknij przycisk **Wybierz**.

5. Na stronie **Konfiguracja kopii zapasowej,** która pozostaje otwarta, można skonfigurować **bazę danych kopii zapasowych,** a następnie wybrać bazy danych, które chcesz uwzględnić w kopiach zapasowych (baza danych SQL lub MySQL), a następnie kliknij przycisk **OK**.

    ![Wybieranie konta magazynu](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > Aby baza danych pojawiła się na tej liście, jej parametry połączenia muszą istnieć w sekcji **Parametry połączenia** na stronie **Ustawienia aplikacji** dla aplikacji. 
    >
    > W aplikacji bazy danych MySQL są automatycznie archiwizowane bez żadnej konfiguracji. Jeśli ręcznie ustawienia baz danych MySQL w aplikacji, takie jak dodawanie ciągów połączeń, kopie zapasowe mogą nie działać poprawnie.
    > 
    > 

6. Na stronie **Konfiguracja kopii zapasowej** kliknij pozycję **Zapisz**.
7. Na stronie **Kopie zapasowe** kliknij pozycję **Kopia zapasowa**.

    ![Przycisk BackUpNow](./media/manage-backup/manual-backup.png)

    Podczas procesu tworzenia kopii zapasowej zostanie wyświetlony komunikat o postępie.

Po skonfigurowaniu konta magazynu i kontenera można w dowolnym momencie zainicjować ręczną kopię zapasową.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurowanie automatycznych kopii zapasowych
1. Na stronie **Konfiguracja kopii zapasowej** ustaw **zaplanowaną kopię zapasową** **na Wł.** 

    ![Włączanie automatycznych kopii zapasowych](./media/manage-backup/scheduled-backup.png)

2. Skonfiguruj harmonogram tworzenia kopii zapasowych zgodnie z potrzebami i wybierz **przycisk OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurowanie częściowych kopii zapasowych
Czasami nie chcesz bić na wszystkie zapasy wszystkich łóżek w aplikacji. Oto kilka przykładów:

* [Skonfiguruj cotygodniowe kopie zapasowe](#configure-automated-backups) aplikacji, które zawierają zawartość statyczną, która nigdy się nie zmienia, na przykład stare wpisy w blogu lub obrazy.
* Aplikacja ma ponad 10 GB zawartości (to maksymalna kwota, którą można wyw.
* Nie chcesz, aby kopii zapasowej plików dziennika.

Częściowe kopie zapasowe umożliwiają wybranie dokładnie plików, które mają zostać utworzone.

> [!NOTE]
> Poszczególne bazy danych w kopii zapasowej mogą mieć maksymalnie 4 GB, ale całkowity maksymalny rozmiar kopii zapasowej wynosi 10 GB

### <a name="exclude-files-from-your-backup"></a>Wykluczanie plików z kopii zapasowej
Załóżmy, że masz aplikację, która zawiera pliki dziennika i obrazy statyczne, które zostały kopii zapasowej raz i nie zostaną zmienić. W takich przypadkach można wykluczyć te foldery i pliki z przechowywania w przyszłych kopiach zapasowych. Aby wykluczyć pliki i foldery z `_backup.filter` kopii zapasowych, utwórz plik w folderze `D:\home\site\wwwroot` aplikacji. Określ listę plików i folderów, które chcesz wykluczyć w tym pliku. 

Dostęp do plików można uzyskać, przechodząc do pliku `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Jeśli zostanie wyświetlony monit, zaloguj się do konta platformy Azure.

Zidentyfikuj foldery, które chcesz wykluczyć z kopii zapasowych. Na przykład chcesz odfiltrować wyróżniony folder i pliki.

![Folder Obrazy](./media/manage-backup/kudu-images.png)

Utwórz plik `_backup.filter` o nazwie i umieścić poprzednią `D:\home`listę w pliku, ale usunąć . Wyświetl jeden katalog lub plik w wierszu. Tak więc zawartość pliku powinna być:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

`_backup.filter` Prześlij `D:\home\site\wwwroot\` plik do katalogu witryny za pomocą [ftp](deploy-ftp.md) lub innej metody. Jeśli chcesz, możesz utworzyć plik bezpośrednio za `DebugConsole` pomocą Kudu i wstawić tam zawartość.

Uruchamiaj kopie zapasowe w taki sam sposób, jak zwykle, [ręcznie](#create-a-manual-backup) lub [automatycznie](#configure-automated-backups). Teraz wszystkie pliki i foldery, `_backup.filter` które są określone w jest wykluczone z przyszłych kopii zapasowych zaplanowane lub zainicjowane ręcznie. 

> [!NOTE]
> Częściowe kopie zapasowe witryny można przywrócić w taki sam sposób, w jaki [można przywrócić zwykłą kopię zapasową](web-sites-restore.md). Proces przywracania robi to, co należy.
> 
> Po przywróceniu pełnej kopii zapasowej cała zawartość witryny jest zastępowana tym, co znajduje się w kopii zapasowej. Jeśli plik znajduje się w witrynie, ale nie w kopii zapasowej zostanie usunięty. Ale po przywróceniu częściowej kopii zapasowej, każda zawartość, która znajduje się w jednym z katalogów na czarnej liście lub dowolnego pliku z czarnej listy, pozostaje taka, jaka jest.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Sposób przechowywania kopii zapasowych
Po wykonaniu co najmniej jednej kopii zapasowej dla aplikacji kopie zapasowe są widoczne na stronie **Kontenery** konta magazynu i aplikacji. Na koncie magazynu każda kopia`.zip` zapasowa składa się `.xml` z pliku zawierającego `.zip` dane kopii zapasowej i pliku zawierającego manifest zawartości pliku. Możesz rozpakować i przeglądać te pliki, jeśli chcesz uzyskać dostęp do kopii zapasowych bez faktycznie wykonywania przywracania aplikacji.

Kopia zapasowa bazy danych dla aplikacji jest przechowywana w katalogu głównym pliku zip. W przypadku bazy danych SQL jest to plik BACPAC (bez rozszerzenia pliku) i można go zaimportować. Aby utworzyć bazę danych SQL na podstawie eksportu BACPAC, zobacz [Importowanie pliku BACPAC w celu utworzenia nowej bazy danych użytkowników](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Zmiana dowolnego pliku w kontenerze **programu webbackups** może spowodować, że kopia zapasowa stanie się nieprawidłowa i w związku z tym nie można go przywrócić.
> 
> 

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Zarządzanie kopiami zapasowymi można zautomatyzować za pomocą skryptów przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub programu Azure [PowerShell.](/powershell/azure/overview)

Próbki można znaleźć w:

- [Przykłady interfejsu wiersza polecenia platformy Azure](samples-cli.md)
- [Przykłady programu Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat przywracania aplikacji z kopii zapasowej, zobacz Przywracanie aplikacji na [platformie Azure](web-sites-restore.md). 
