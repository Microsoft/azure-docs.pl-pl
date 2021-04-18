---
title: 'Db2 to SQL Server on Azure VM: Migration guide (Baza danych Db2 do SQL Server na maszynie wirtualnej platformy Azure: przewodnik migracji)'
description: W tym przewodniku nauczysz się migrować bazy danych IBM Db2 SQL Server maszynie wirtualnej platformy Azure przy użyciu Asystent migracji do programu SQL Server db2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600169"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Przewodnik migracji: program IBM Db2 do usługi SQL Server na maszynie wirtualnej platformy Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

W tym przewodniku nauczysz się migrować bazy danych użytkowników z bazy danych IBM Db2 SQL Server na maszynie wirtualnej platformy Azure przy użyciu interfejsu Asystent migracji do programu SQL Server db2. 

Aby uzyskać inne przewodniki dotyczące migracji, zobacz [Przewodniki po migracji bazy danych platformy Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych Db2 do SQL Server, potrzebne są:

- Aby sprawdzić, czy [środowisko źródłowe jest obsługiwane.](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)
- [Asystent migracji do programu SQL Server (SSMA) dla bazy danych Db2.](https://www.microsoft.com/download/details.aspx?id=54254)
- [Łączność](../../virtual-machines/windows/ways-to-connect-to-sql.md) między środowiskiem źródłowym i maszyną wirtualną SQL Server na platformie Azure. 
- Docelowy obiekt docelowy [SQL Server maszynie wirtualnej platformy Azure.](../../virtual-machines/windows/create-sql-vm-portal.md) 

## <a name="pre-migration"></a>Przed migracją

Po spełnianiu wymagań wstępnych możesz wykryć topologię środowiska i ocenić możliwość migracji. 

### <a name="assess"></a>Ocena 

Użyj funkcji SSMA dla bazy danych DB2, aby przejrzeć obiekty i dane bazy danych oraz ocenić bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące kroki:

1. Otwórz [program SSMA for Db2.](https://www.microsoft.com/download/details.aspx?id=54254) 
1. Wybierz **pozycję File** New Project  >  **(Plik nowego projektu).** 
1. Podaj nazwę projektu i lokalizację do zapisania projektu. Następnie wybierz docelowy SQL Server migracji z listy rozwijanej i wybierz przycisk **OK.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Zrzut ekranu przedstawiający szczegóły projektu do określenia.":::


1. Na **stronie Nawiązywanie połączenia z bazą** danych Db2 wprowadź wartości szczegółów połączenia Db2.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Zrzut ekranu przedstawiający opcje nawiązywania połączenia z wystąpieniem db2.":::


1. Kliknij prawym przyciskiem myszy schemat Db2, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport.** Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać pozycję **Utwórz raport** na pasku nawigacyjnym po wybraniu schematu.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia raportu.":::

1. Przejrzyj raport HTML, aby poznać statystyki konwersji i wszelkie błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Db2 i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportu w ramach *projektu SSMAProjects.*

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Zrzut ekranu przedstawiający przeglądany raport w celu zidentyfikowania błędów lub ostrzeżeń.":::


### <a name="validate-data-types"></a>Weryfikowanie typów danych

Zweryfikuj domyślne mapowania typów danych i w razie potrzeby zmień je na podstawie wymagań. W tym celu wykonaj następujące czynności: 

1. Wybierz **pozycję Narzędzia** z menu. 
1. Wybierz **pozycję Project Settings (Ustawienia projektu).** 
1. Wybierz **kartę Mapowania** typów.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Zrzut ekranu przedstawiający wybieranie mapowania schematu i typu.":::

1. Mapowanie typów dla każdej tabeli można zmienić, wybierając tabelę w Eksploratorze metadanych **Db2**. 

### <a name="convert-schema"></a>Konwertowanie schematu 

Aby przekonwertować schemat, wykonaj następujące kroki:

1. (Opcjonalnie) Dodawanie zapytań dynamicznych lub ad hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz **pozycję Połącz z SQL Server**. 
    1. Wprowadź szczegóły połączenia, aby nawiązać połączenie z wystąpieniem SQL Server na maszynie wirtualnej platformy Azure. 
    1. Wybierz połączenie z istniejącą bazą danych na serwerze docelowym lub podaj nową nazwę, aby utworzyć nową bazę danych na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Zrzut ekranu przedstawiający szczegóły wymagane do nawiązania połączenia z maszyną SQL Server na maszynie wirtualnej platformy Azure.":::

1. Kliknij prawym przyciskiem myszy schemat, a następnie wybierz polecenie **Konwertuj schemat.** Alternatywnie możesz wybrać pozycję **Konwertuj schemat** na górnym pasku nawigacyjnym po wybraniu schematu.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Zrzut ekranu przedstawiający wybieranie schematu i jego konwertowanie.":::

1. Po zakończeniu konwersji porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy. Rozwiązania problemów na podstawie zaleceń. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Zrzut ekranu przedstawiający porównanie i przeglądanie struktury schematu w celu zidentyfikowania potencjalnych problemów.":::

1. W **okienku Dane** wyjściowe wybierz pozycję **Przejrzyj wyniki.** W **okienku Lista błędów** przejrzyj błędy. 
1. Zapisz projekt lokalnie w celu skorygowania schematu w trybie offline. W menu **File (Plik)** wybierz pozycję **Save Project (Zapisz projekt).** Daje to możliwość oceny schematów źródłowych i docelowych w trybie offline i wykonania korygowania przed opublikowaniem schematu w usłudze SQL Server wirtualnej platformy Azure.

## <a name="migrate"></a>Migrate

Po zakończeniu oceny baz danych i rozwiązywaniu wszelkich niezgodności następnym krokiem jest wykonanie procesu migracji.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat. W **SQL Server Metadanych** w węźle **Bazy** danych kliknij prawym przyciskiem myszy bazę danych. Następnie wybierz pozycję **Synchronizuj z bazą danych**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Zrzut ekranu przedstawiający opcję synchronizacji z bazą danych.":::

1. Migrowanie danych. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w Eksploratorze metadanych **Db2,** a następnie wybierz polecenie **Migruj dane.** Alternatywnie możesz wybrać pozycję **Migruj dane** na pasku nawigacyjnym. Aby przeprowadzić migrację danych dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z poszczególnych tabel, rozwiń bazę danych, rozwiń pozycję **Tabele,** a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Zrzut ekranu przedstawiający wybieranie schematu i wybieranie migracji danych.":::

1. Podaj szczegóły połączenia dla wystąpień db2 i SQL Server db2. 
1. Po zakończeniu migracji wyświetl raport **migracji danych:**  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Zrzut ekranu przedstawiający miejsce przeglądania raportu migracji danych.":::

1.  Połącz się z wystąpieniem usługi SQL Server na maszynie wirtualnej platformy Azure przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Zweryfikuj migrację, przeglądając dane i schemat.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Zrzut ekranu przedstawiający porównanie schematu w SQL Server Management Studio.":::

## <a name="post-migration"></a>Po migracji 

Po zakończeniu migracji należy wykonać szereg zadań po migracji, aby upewnić się, że wszystko działa tak bezproblemowo i wydajnie, jak to możliwe.

### <a name="remediate-applications"></a>Korygowanie aplikacji 

Po migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej zużywały źródło, muszą zacząć korzystać z obiektu docelowego. W niektórych przypadkach będzie to wymagało zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonywanie testów

Testowanie składa się z następujących działań:

1. **Opracowywanie testów weryfikacyjnych:** aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania weryfikacji do uruchamiania zarówno dla źródłowej, jak i docelowej bazy danych. Zapytania weryfikacji powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego:** środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Pamiętaj, aby odizolować środowisko testowe.
1. **Uruchamianie testów walidacji:** uruchom testy weryfikacyjne względem źródła i obiektu docelowego, a następnie przeanalizuj wyniki.
1. **Uruchamianie testów wydajnościowych:** uruchom testy wydajnościowe względem źródła i obiektu docelowego, a następnie przeanalizuj i porównaj wyniki.

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane w celu obsługi rzeczywistego zaangażowania w projekt migracji:

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie do oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie udostępnia sugerowane "najlepsze dopasowanie" platform docelowych, gotowość do chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste obliczenia i generowanie raportów jednym kliknięciem, które pomagają przyspieszyć ocenę dużych nieruchomości, zapewniając zautomatyzowany i jednolity proces podejmowania decyzji na platformie docelowej.|
|[Pakiet odnajdywania i oceny zasobów danych db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po uruchomieniu skryptu SQL w bazie danych można wyeksportować wyniki do pliku w systemie plików. Obsługiwanych jest kilka formatów plików, w tym plik *.csv, dzięki czemu można przechwytywać wyniki w narzędziach zewnętrznych, takich jak arkusze kalkulacyjne. Ta metoda może być przydatna, jeśli chcesz łatwo udostępniać wyniki zespołom, które nie mają zainstalowanej aplikacji Workbench.|
|[Skrypty i artefakty spisu LUW programu IBM Db2](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ten zasób zawiera zapytanie SQL, które trafia do tabel systemowych IBM Db2 LUW w wersji 11.1 i udostępnia liczbę obiektów według schematu i typu obiektu, przybliżone oszacowanie "danych pierwotnych" w każdym schemacie oraz rozmiarów tabel w poszczególnych schematach, z wynikami przechowywanymi w formacie CSV.|
|[Czysty skala luw db2 na platformie Azure — przewodnik konfiguracji](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ten przewodnik służy jako punkt wyjścia dla planu implementacji Db2. Chociaż wymagania biznesowe będą się różnić, ma zastosowanie ten sam podstawowy wzorzec. Ten wzorzec architektury może być również używany dla aplikacji OLAP na platformie Azure.|

Zespół inżynierów ds. danych SQL opracował te zasoby. Podstawowym procesem tego zespołu jest odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych na platformę danych Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

Po migracji zapoznaj się z przewodnikiem [po weryfikacji i optymalizacji po migracji.](/sql/relational-databases/post-migration-validation-and-optimization-guide) 

W przypadku usług i narzędzi firmy Microsoft oraz innych firm, które są dostępne w celu pomocy w różnych scenariuszach migracji baz danych i danych, zobacz Usługi i narzędzia do migracji [danych](../../../dms/dms-tools-matrix.md).

Aby uzyskać zawartość wideo, [zobacz Omówienie podróży migracji.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
