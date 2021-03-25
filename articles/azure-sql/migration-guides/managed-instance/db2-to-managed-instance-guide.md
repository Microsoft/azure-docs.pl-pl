---
title: 'Wystąpienie zarządzane programu DB2 z usługą Azure SQL: Przewodnik migracji'
description: Ten przewodnik zawiera wskazówki dotyczące migrowania baz danych programu DB2 do wystąpienia zarządzanego usługi Azure SQL przy użyciu Asystent migracji do programu SQL Server dla programu DB2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 7ac3518e0d27be6b6a18790b9fcbdbce0f6f8fef
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025054"
---
# <a name="migration-guide-db2-to-azure-sql-managed-instance"></a>Przewodnik migracji: wystąpienie zarządzane bazy danych DB2 z usługą Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

W tym przewodniku nauczysz się migrować bazy danych DB2 do wystąpienia zarządzanego Azure SQL przy użyciu Asystent migracji do programu SQL Server dla programu DB2. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby przeprowadzić migrację bazy danych DB2 do wystąpienia zarządzanego SQL, potrzebne są:

- Aby sprawdzić, czy [środowisko źródłowe jest obsługiwane](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Aby pobrać [Asystent migracji do programu SQL Server (Asystencie migracji) dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- docelowe [wystąpienie zarządzane Azure SQL](../../managed-instance/instance-create-quickstart.md).
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 



## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji. 

### <a name="assess-and-convert"></a>Oceń i Konwertuj



Utwórz ocenę przy użyciu Asystent migracji do programu SQL Server (ASYSTENCIE migracji). 

Aby utworzyć ocenę, wykonaj następujące kroki:

1. Otwórz Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla bazy danych DB2. 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację, w której ma zostać zapisany projekt, a następnie wybierz wystąpienie zarządzane Azure SQL jako miejsce docelowe migracji z listy rozwijanej. Wybierz **przycisk OK**:

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Podaj szczegóły projektu i wybierz pozycję OK, aby zapisać.":::


1. Wprowadź wartości w polu Szczegóły połączenia bazy danych DB2 w oknie dialogowym **łączenie z bazą danych DB2** :

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Nawiązywanie połączenia z wystąpieniem bazy danych DB2":::


1. Kliknij prawym przyciskiem myszy schemat DB2, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po zaznaczeniu schematu:

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Utwórz raport":::

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów DB2 i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Przejrzyj raport, aby zidentyfikować błędy lub ostrzeżenia":::


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typów** :

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Wybierz schemat, a następnie mapowanie typu":::

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych programu DB2**. 

### <a name="schema-conversion"></a>Konwersja schematu 

Aby przekonwertować schemat, wykonaj następujące kroki:

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad-hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz pozycję **Połącz z wystąpieniem zarządzanym usługi Azure SQL**. 
    1. Wprowadź szczegóły połączenia w celu nawiązania połączenia z wystąpieniem zarządzanym usługi Azure SQL. 
    1. Wybierz docelową bazę danych z listy rozwijanej lub podaj nową nazwę. w takim przypadku baza danych zostanie utworzona na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**:

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Wypełnij szczegóły, aby połączyć się z SQL Server":::


1. Kliknij prawym przyciskiem myszy schemat, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po wybraniu schematu:

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Konwertuj schemat":::

1. Po zakończeniu konwersji Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń.":::

1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w wystąpieniu zarządzanym SQL.


## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w węźle **bazy danych** w **Eksploratorze metadanych wystąpienia zarządzanego Azure SQL** i wybierz polecenie **Synchronizuj z bazą danych**:

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie Synchronizuj z bazą danych.":::

1. Migruj dane: kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych programu DB2**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru:

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Kliknij prawym przyciskiem myszy schemat i wybierz polecenie Migruj dane":::

1. Podaj szczegóły połączenia dla wystąpienia zarządzanego w programie DB2 i SQL. 
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**:  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Przejrzyj raport dotyczący migracji danych":::

1. Połącz się z wystąpieniem zarządzanym usługi Azure SQL przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i Zweryfikuj migrację, przeglądając dane i schemat:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Porównanie schematu w programie SSMS":::

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


## <a name="leverage-advanced-features"></a>Korzystanie z zaawansowanych funkcji 

Korzystaj z zaawansowanych funkcji opartych na chmurze oferowanych przez wystąpienie zarządzane usługi Azure SQL, takich jak [wbudowana wysoka dostępność](../../database/high-availability-sla.md), [wykrywanie zagrożeń](../../database/azure-defender-for-sql.md)i [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). 


Niektóre funkcje SQL Server są dostępne tylko wtedy, gdy [poziom zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) zostanie zmieniony na najnowszy poziom zgodności (150). 

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane z myślą o obsłudze projektu migracji w rzeczywistości:

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Pakiet odnajdywania i oceny zasobów danych DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po uruchomieniu skryptu SQL w bazie danych można eksportować wyniki do pliku w systemie plików. Obsługiwane są różne formaty plików, w tym *. csv, dzięki czemu można przechwytywać wyniki w zewnętrznych narzędziach, takich jak arkusze kalkulacyjne. Ta metoda może być przydatna, jeśli chcesz łatwo współdzielić wyniki z zespołami, które nie mają zainstalowanego Workbench.|
|[Skrypty i artefakty spisu IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ten element zawartości obejmuje zapytanie SQL, które trafią z tabeli systemowej programu IBM DB2 LUW w wersji 11,1 i udostępnia liczbę obiektów według schematu i typu obiektu, przybliżone oszacowanie wartości danych pierwotnych w każdym schemacie oraz rozmiar tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.|
|[Platforma DB2 LUW w czystej skali na platformie Azure — przewodnik konfigurowania](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Ten przewodnik służy jako punkt wyjścia dla planu implementacji programu DB2. Wymagania biznesowe różnią się w zależności od tego, jaki jest ten sam wzorzec podstawowy. Ten wzorzec architektoniczny może być również używany w przypadku aplikacji OLAP na platformie Azure.|

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.


## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat wystąpienia zarządzanego Azure SQL, zobacz:
   - [Omówienie wystąpienia zarządzanego SQL](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).