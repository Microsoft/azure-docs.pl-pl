---
title: 'Bazy danych DB2 do SQL Server na maszynach wirtualnych platformy Azure: Przewodnik migracji'
description: W tym przewodniku przedstawiono, jak przeprowadzić migrację bazy danych DB2 do SQL Server na maszynach wirtualnych platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu DB2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: aadfd2d96e2ed610ee1e3eaaec9276212e912b0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562233"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Przewodnik migracji: DB2 do SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ten przewodnik migracji zawiera szczegółowe instrukcje dotyczące migrowania baz danych użytkowników z programu DB2 do SQL Server na maszynach wirtualnych platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu DB2. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 


## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych DB2 do SQL Server, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane.
- [Asystent migracji do programu SQL Server (Asystencie migracji) dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Łączność](../../virtual-machines/windows/ways-to-connect-to-sql.md) między środowiskiem źródłowym a maszyną wirtualną SQL Server na platformie Azure. 



## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji. 

### <a name="assess"></a>Ocena 

Utwórz ocenę przy użyciu Asystent migracji do programu SQL Server (ASYSTENCIE migracji). 

Aby utworzyć ocenę, wykonaj następujące kroki:

1. Otwórz Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla bazy danych DB2. 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację do zapisania projektu, a następnie wybierz SQL Server cel migracji z listy rozwijanej. Wybierz przycisk **OK**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Podaj szczegóły projektu i wybierz pozycję OK, aby zapisać.":::


1. Wprowadź wartości w polu Szczegóły połączenia bazy danych DB2 w oknie dialogowym **łączenie z bazą danych DB2** . 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-db2.png" alt-text="Nawiązywanie połączenia z wystąpieniem bazy danych DB2":::


1. Kliknij prawym przyciskiem myszy schemat DB2, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po zaznaczeniu schematu. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Utwórz raport":::

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów DB2 i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Przejrzyj raport, aby zidentyfikować błędy lub ostrzeżenia":::


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** . 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Wybierz schemat, a następnie mapowanie typu":::

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych programu DB2**. 

### <a name="convert-schema"></a>Konwertuj schemat 

Aby przekonwertować schemat, wykonaj następujące kroki:

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad-hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz pozycję **Połącz z SQL Server**. 
    1. Wprowadź szczegóły połączenia w celu nawiązania połączenia z wystąpieniem SQL Server na maszynie wirtualnej platformy Azure. 
    1. Wybierz połączenie z istniejącą bazą danych na serwerze docelowym lub podaj nową nazwę, aby utworzyć nową bazę danych na serwerze docelowym. 
    1. Wybierz pozycję **Połącz**. 

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Nawiązywanie połączenia z SQL Server na maszynie wirtualnej platformy Azure":::


1. Kliknij prawym przyciskiem myszy schemat, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po zaznaczeniu schematu. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Konwertuj schemat":::

1. Po zakończeniu konwersji Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń.":::

1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . 


## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w węźle **bazy danych** w **Eksploratorze metadanych SQL Server** i wybierz polecenie **Synchronizuj z bazą danych**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie Synchronizuj z bazą danych.":::

1. Migruj dane: kliknij prawym przyciskiem myszy schemat w **Eksploratorze metadanych programu DB2** i wybierz polecenie **Migruj dane**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Migruj dane":::

1. Podaj szczegóły połączenia dla wystąpień bazy danych DB2 i SQL Server. 
1. Wyświetl **raport dotyczący migracji danych**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Przejrzyj raport dotyczący migracji danych":::

1. Połącz się z wystąpieniem SQL Server za pomocą SQL Server Management Studio i sprawdź poprawność migracji, przeglądając dane i schemat. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Porównanie schematu w programie SSMS":::

## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu migracji należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje 

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe**: Uruchom test wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.


## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane z myślą o obsłudze projektu migracji w rzeczywistości:

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Pakiet odnajdywania i oceny zasobów danych DB2 zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po uruchomieniu skryptu SQL w bazie danych można eksportować wyniki do pliku w systemie plików. Obsługiwane są różne formaty plików, w tym *. csv, dzięki czemu można przechwytywać wyniki w zewnętrznych narzędziach, takich jak arkusze kalkulacyjne. Ta metoda może być przydatna, jeśli chcesz łatwo współdzielić wyniki z zespołami, które nie mają zainstalowanego Workbench.|
|[Skrypty i artefakty spisu IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ten element zawartości obejmuje zapytanie SQL, które trafią z tabeli systemowej programu IBM DB2 LUW w wersji 11,1 i udostępnia liczbę obiektów według schematu i typu obiektu, przybliżone oszacowanie wartości danych pierwotnych w każdym schemacie oraz rozmiar tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.|
|[Platforma DB2 LUW w czystej skali na platformie Azure — przewodnik konfigurowania](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ten przewodnik służy jako punkt wyjścia dla planu implementacji programu DB2. Wymagania biznesowe różnią się w zależności od tego, jaki jest ten sam wzorzec podstawowy. Ten wzorzec architektoniczny może być również używany w przypadku aplikacji OLAP na platformie Azure.|

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki

Po migracji zapoznaj się z [przewodnikiem walidacji po migracji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

W przypadku macierzy usług i narzędzi firmy Microsoft i innych firm, które są dostępne, aby pomóc w różnych scenariuszach migracji bazy danych i danych, a także zadania specjalne, zobacz temat [usługi i narzędzia migracji danych](../../../dms/dms-tools-matrix.md).

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

Aby uzyskać zawartość wideo, zobacz:
- [Przegląd podróży migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)