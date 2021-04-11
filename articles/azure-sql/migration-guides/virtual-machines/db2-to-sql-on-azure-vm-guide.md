---
title: 'DB2 do SQL Server na maszynie wirtualnej platformy Azure: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować bazy danych programu IBM DB2 do SQL Server na maszynie wirtualnej platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu DB2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: ff241f468db2e56b73ba10b5621e8a8ab40a19b6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554148"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Przewodnik migracji: IBM DB2 do SQL Server na maszynie wirtualnej platformy Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

W tym przewodniku przedstawiono sposób migrowania baz danych użytkowników z programu IBM DB2 do SQL Server na maszynie wirtualnej platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu DB2. 

Aby poznać inne przewodniki dotyczące migracji, zobacz [przewodniki dotyczące migracji bazy danych Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych DB2 do SQL Server, potrzebne są:

- Aby sprawdzić, czy [środowisko źródłowe jest obsługiwane](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites).
- [Asystent migracji do programu SQL Server (Asystencie migracji) dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Łączność](../../virtual-machines/windows/ways-to-connect-to-sql.md) między środowiskiem źródłowym a maszyną wirtualną SQL Server na platformie Azure. 
- Docelowa [SQL Server na maszynie wirtualnej platformy Azure](../../virtual-machines/windows/create-sql-vm-portal.md). 

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji. 

### <a name="assess"></a>Ocena 

Za pomocą ASYSTENCIE migracji dla programu DB2 można przeglądać obiekty i dane bazy danych oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące kroki:

1. Otwórz [Asystencie migracji dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Wybierz pozycję **plik**  >  **Nowy projekt**. 
1. Podaj nazwę projektu i lokalizację, w której ma być zapisany projekt. Następnie wybierz SQL Server cel migracji z listy rozwijanej, a następnie wybierz **przycisk OK**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Zrzut ekranu pokazujący szczegóły projektu do określenia.":::


1. W obszarze **Połącz z bazą danych DB2** wprowadź wartości w polu Szczegóły połączenia z bazą danych DB2.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Zrzut ekranu przedstawiający Opcje łączenia się z wystąpieniem bazy danych DB2.":::


1. Kliknij prawym przyciskiem myszy schemat DB2, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po zaznaczeniu schematu.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Zrzut ekranu pokazujący sposób tworzenia raportu.":::

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów DB2 i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w *SSMAProjects*.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Zrzut ekranu przedstawiający raport, który służy do identyfikowania błędów lub ostrzeżeń.":::


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typów danych i w razie potrzeby zmień je na podstawie wymagań. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** .

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Zrzut ekranu pokazujący wybór schematu i mapowania typu.":::

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych programu DB2**. 

### <a name="convert-schema"></a>Konwertuj schemat 

Aby przekonwertować schemat, wykonaj następujące kroki:

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz pozycję **Połącz z SQL Server**. 
    1. Wprowadź szczegóły połączenia w celu nawiązania połączenia z wystąpieniem SQL Server na maszynie wirtualnej platformy Azure. 
    1. Wybierz połączenie z istniejącą bazą danych na serwerze docelowym lub podaj nową nazwę, aby utworzyć nową bazę danych na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Zrzut ekranu pokazujący szczegóły dotyczące łączenia się z SQL Server na maszynie wirtualnej platformy Azure.":::

1. Kliknij prawym przyciskiem myszy schemat, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po zaznaczeniu schematu.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Zrzut ekranu, który pokazuje Wybieranie schematu i konwertowanie go.":::

1. Po zakończeniu konwersji Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy. Rozwiązywanie problemów na podstawie zaleceń. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Zrzut ekranu pokazujący porównanie i przeglądanie struktury schematu w celu zidentyfikowania potencjalnych problemów.":::

1. W okienku **danych wyjściowych** wybierz pozycję **Przejrzyj wyniki**. W okienku **Lista błędów** Przejrzyj błędy. 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Z menu **plik** wybierz polecenie **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline oraz przeprowadzić korygowanie, aby można było opublikować schemat w celu SQL Server na maszynie wirtualnej platformy Azure.

## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat. W **Eksploratorze metadanych SQL Server** w węźle **bazy danych** kliknij prawym przyciskiem myszy bazę danych. Następnie wybierz pozycję **Synchronizuj z bazą danych**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Zrzut ekranu przedstawiający opcję synchronizowania z bazą danych.":::

1. Migruj dane. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych programu DB2**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Zrzut ekranu pokazujący wybór schematu i wybór migracji danych.":::

1. Podaj szczegóły połączenia dla wystąpień bazy danych DB2 i SQL Server. 
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**:  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Zrzut ekranu pokazujący, gdzie przejrzeć raport dotyczący migracji danych.":::

1.  Połącz się z wystąpieniem SQL Server na maszynie wirtualnej platformy Azure przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Sprawdź poprawność migracji, przeglądając dane i schemat.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Zrzut ekranu pokazujący Porównanie schematu w SQL Server Management Studio.":::

## <a name="post-migration"></a>Po migracji 

Po zakończeniu migracji należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje 

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Testowanie obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe**: Uruchom testy wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane z myślą o obsłudze projektu migracji w rzeczywistości:

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Pakiet odnajdywania i oceny zasobów danych DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po uruchomieniu skryptu SQL w bazie danych można eksportować wyniki do pliku w systemie plików. Obsługiwane są różne formaty plików, w tym *. csv, dzięki czemu można przechwytywać wyniki w zewnętrznych narzędziach, takich jak arkusze kalkulacyjne. Ta metoda może być przydatna, jeśli chcesz łatwo współdzielić wyniki z zespołami, które nie mają zainstalowanego Workbench.|
|[Skrypty i artefakty spisu IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ten element zawartości obejmuje zapytanie SQL, które trafią z tabeli systemowej programu IBM DB2 LUW w wersji 11,1 i udostępnia liczbę obiektów według schematu i typu obiektu, przybliżone oszacowanie "nieprzetworzonych danych" w każdym schemacie oraz zmianę wielkości tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.|
|[Platforma DB2 LUW w czystej skali na platformie Azure — przewodnik konfigurowania](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/db2%20PureScale%20on%20Azure.pdf)|Ten przewodnik służy jako punkt wyjścia dla planu implementacji programu DB2. Chociaż wymagania biznesowe różnią się w zależności od tego, stosuje się ten sam wzorzec podstawowy. Ten wzorzec architektoniczny może być również używany w przypadku aplikacji OLAP na platformie Azure.|

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

Po migracji zapoznaj się z [przewodnikiem walidacji po migracji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

W przypadku usług i narzędzi firmy Microsoft i innych firm, które są dostępne, aby pomóc Ci w różnych scenariuszach migracji bazy danych i danych, zobacz temat [usługi i narzędzia migracji danych](../../../dms/dms-tools-matrix.md).

Aby uzyskać zawartość wideo, zobacz [Omówienie podróży migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
