---
title: 'Oracle do SQL Server na platformie Azure Virtual Machines: Przewodnik migracji'
description: W tym przewodniku przedstawiono, jak migrować schematy programu Oracle do SQL Server na platformie Azure Virtual Machines przy użyciu Asystent migracji do programu SQL Server dla programu Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961255"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Przewodnik migracji: baza danych Oracle do SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku przedstawiono, jak migrować schematy programu Oracle do SQL Server na platformie Azure Virtual Machines przy użyciu Asystent migracji do programu SQL Server dla programu Oracle. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby migrować schemat Oracle do SQL Server na platformie Azure Virtual Machines, potrzebne są:

- Obsługiwane środowisko źródłowe.
- [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Docelowa [maszyna wirtualna SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Odpowiednie [uprawnienia dla Asystencie migracji dla programu Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) i [dostawcy](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 


## <a name="pre-migration"></a>Przed migracją

Aby przygotować się do migracji do chmury, sprawdź, czy środowisko źródłowe jest obsługiwane i czy zostały spełnione wszystkie wymagania wstępne. Wykonanie tej czynności pomoże zapewnić wydajną i pomyślną migrację.

Ta część procesu obejmuje: 
- Przeprowadzenie spisu baz danych, które należy zmigrować.
- Ocenianie tych baz danych pod kątem potencjalnych problemów z migracją lub zablokowanych. 
- Rozwiązywanie wszelkich problemów, które są odkrywane. 

### <a name="discover"></a>Odnajdywanie

Użyj narzędzia [map Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) , aby zidentyfikować istniejące źródła danych i szczegółowe informacje o funkcjach używanych przez firmę. Dzięki temu będzie można lepiej zrozumieć migrację i ułatwić jej planowanie. Ten proces polega na skanowaniu sieci w celu zidentyfikowania wystąpień firmy Oracle i wersji i funkcji, które są używane.

Aby użyć narzędzia MAP Toolkit do przeprowadzenia skanowania spisu, wykonaj następujące czynności: 


1. Otwórz [zestaw narzędzi mapy](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Wybierz pozycję **Utwórz/wybierz bazę danych**:

   ![Zrzut ekranu pokazujący opcję Utwórz/wybierz bazę danych.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Wybierz pozycję **Utwórz bazę danych spisu**. Wprowadź nazwę nowej tworzonej bazy danych spisu, podaj Krótki opis, a następnie wybierz przycisk **OK**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Zrzut ekranu pokazujący interfejs do tworzenia bazy danych spisu.":::

1. Wybierz pozycję **Zbierz dane spisu** , aby otworzyć **Kreatora spisu i oceny**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Zrzut ekranu przedstawiający link zbierania danych spisu.":::


1. W **Kreatorze spisu i oceny** wybierz pozycję **Oracle**, a następnie wybierz pozycję **dalej**:

   ![Zrzut ekranu przedstawiający stronę scenariusze spisu w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Wybierz opcję wyszukiwania komputera, która najlepiej odpowiada wymaganiom biznesowym i środowisku, a następnie wybierz pozycję **dalej**: 

   ![Zrzut ekranu przedstawiający stronę metody odnajdywania w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Wprowadź poświadczenia lub Utwórz nowe poświadczenia dla systemów, które chcesz eksplorować, a następnie wybierz pozycję **dalej**:

    ![Zrzut ekranu przedstawiający stronę wszystkie poświadczenia komputerów w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Ustaw kolejność poświadczeń, a następnie wybierz pozycję **dalej**: 

   ![Zrzut ekranu przedstawiający stronę kolejność poświadczeń w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Wprowadź poświadczenia dla każdego komputera, który chcesz wykryć. Możesz użyć unikatowych poświadczeń dla każdego komputera/maszyny lub użyć listy poświadczeń wszystkie komputery.  

   ![Zrzut ekranu przedstawiający stronę Określanie komputerów i poświadczeń w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Sprawdź wybrane opcje, a następnie wybierz pozycję **Zakończ**:

   ![Zrzut ekranu przedstawiający stronę Podsumowanie w Kreatorze spisu i oceny.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. Po zakończeniu skanowania Wyświetl podsumowanie **zbierania danych** . Skanowanie może potrwać kilka minut, w zależności od liczby baz danych. Gdy skończysz, wybierz pozycję **Zamknij** : 

   ![Zrzut ekranu pokazujący podsumowanie zbierania danych.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Wybierz **Opcje** , aby wygenerować raport dotyczący oceny i szczegółów bazy danych Oracle. Wybierz obie opcje, pojedynczo, aby wygenerować raport.


### <a name="assess"></a>Ocena

Po zidentyfikowaniu źródeł danych Użyj [Asystent migracji do programu SQL Server dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) , aby ocenić wystąpienia Oracle migrowane do SQL Server maszyny wirtualnej. Asystent pomoże zrozumieć luki między źródłową i docelową bazą danych. Można przejrzeć obiekty bazy danych i dane, ocenić bazy danych do migracji, przeprowadzić migrację obiektów bazy danych do SQL Server, a następnie zmigrować dane do SQL Server.

Aby utworzyć ocenę, wykonaj następujące kroki: 


1. Otwórz [Asystent migracji do programu SQL Server dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. W menu **plik** wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu i lokalizację projektu, a następnie wybierz z listy SQL Server cel migracji. Wybierz **przycisk OK**: 

   ![Zrzut ekranu przedstawiający okno dialogowe Nowy projekt.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Wybierz pozycję **Połącz z bazą danych Oracle**. Wprowadź wartości dla połączenia Oracle w oknie dialogowym **Połącz z bazą danych Oracle** :

   ![Zrzut ekranu przedstawiający okno dialogowe łączenie z bazą danych Oracle.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Wybierz schematy programu Oracle, które chcesz zmigrować: 

   ![Zrzut ekranu przedstawiający listę schematów programu Oracle, które można migrować.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. W **Eksploratorze metadanych Oracle** kliknij prawym przyciskiem myszy schemat Oracle, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać bazę danych, a następnie wybrać pozycję **Utwórz raport** w górnym menu.

   ![Zrzut ekranu pokazujący sposób tworzenia raportu.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Przejrzyj raport HTML dotyczący statystyk konwersji, błędów i ostrzeżeń. Analizuj go, aby zrozumieć problemy z konwersją i rozwiązania.

    Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Oracle i nakład pracy wymagany do ukończenia konwersji schematu. Domyślną lokalizacją raportu jest folder raportu w SSMAProjects. 

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Zrzut ekranu przedstawiający raport konwersji.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, w razie potrzeby. W tym celu wykonaj następujące czynności: 


1. W menu **Narzędzia** wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** . 

   ![Zrzut ekranu przedstawiający kartę mapowania typów.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych Oracle**. 

### <a name="convert-the-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Aby skonwertować kwerendy dynamiczne lub ad hoc, kliknij prawym przyciskiem myszy węzeł i wybierz polecenie **Dodaj instrukcję**.

1. Wybierz pozycję **Połącz z SQL Server** w górnym menu. 
     1. Wprowadź szczegóły połączenia dla SQL Server na maszynie wirtualnej platformy Azure. 
     1. Wybierz docelową bazę danych z listy lub podaj nową nazwę. W przypadku podania nowej nazwy baza danych zostanie utworzona na serwerze docelowym. 
     1. Podaj szczegóły uwierzytelniania. 
     1. Wybierz pozycję **Połącz**. 


   ![Zrzut ekranu pokazujący sposób nawiązywania połączenia z SQL Server.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle w **Eksploratorze metadanych Oracle** i wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** w menu górnym:

   ![Zrzut ekranu pokazujący sposób konwersji schematu.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. Po zakończeniu konwersji schematu Przejrzyj skonwertowane obiekty i porównaj je z oryginalnymi obiektami, aby zidentyfikować potencjalne problemy. Aby rozwiązać wszelkie problemy, należy wykonać zalecenia:

   ![Zrzut ekranu przedstawiający porównanie dwóch schematów.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnymi procedurami składowanymi i zapoznaj się z zaleceniami: 

   ![Zrzut ekranu, który pokazuje instrukcje języka Transact-SQL, procedury składowane i ostrzeżenie.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Projekt można zapisać lokalnie dla ćwiczenia korygowania schematu w trybie offline. W tym celu wybierz pozycję **Zapisz projekt** w menu **plik** . Zapisanie projektu lokalnie pozwala oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem schematu w celu SQL Server.

1. Wybierz pozycję **przegląd wyników** w okienku **dane wyjściowe** , a następnie przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** w menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem schematu w celu SQL Server na platformie Azure Virtual Machines.


## <a name="migrate"></a>Migrate

Po spełnieniu wymaganych wymagań wstępnych i ukończeniu zadań skojarzonych z etapem sprzed migracji można rozpocząć migrację schematu i danych. Migracja obejmuje dwie czynności: publikowanie schematu i Migrowanie danych. 


Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki: 

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych SQL Server** i wybierz polecenie **Synchronizuj z bazą danych**. W tym celu program publikuje schemat Oracle w celu SQL Server na platformie Azure Virtual Machines. 

   ![Zrzut ekranu pokazujący polecenie Synchronizuj z bazą danych.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Zrzut ekranu pokazujący stan synchronizacji.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migruj dane: kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych Oracle** , a następnie wybierz pozycję **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** w górnym menu.

   Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść odpowiednie pola wyboru.

   ![Zrzut ekranu przedstawiający polecenie Migrowanie danych.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. W oknie dialogowym Podaj szczegóły połączenia dla programu Oracle i SQL Server na platformie Azure Virtual Machines.
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**:

    ![Zrzut ekranu przedstawiający raport dotyczący migracji danych.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Połącz się z SQL Server w wystąpieniu usługi Azure Virtual Machines przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Sprawdź poprawność migracji, przeglądając dane i schemat:


   ![Zrzut ekranu, który pokazuje wystąpienie SQL Server w ASYSTENCIE migracji.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

Zamiast korzystać z ASYSTENCIE migracji, można użyć SQL Server Integration Services (SSIS) do migrowania danych. Aby dowiedzieć się więcej, zobacz: 
- [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services)artykułu.
- Oficjalny dokument [SSIS dla platformy Azure i hybrydowego przenoszenia danych](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>Po migracji 

Po zakończeniu etapu migracji należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. Wprowadzanie tych zmian może wymagać wprowadzenia zmian w aplikacjach.

[Zestaw narzędzi do migracji dostępu do danych](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) jest rozszerzeniem dla Visual Studio Code. Pozwala on analizować kod źródłowy Java oraz wykrywać wywołania i zapytania interfejsu API dostępu do danych. Zestaw narzędzi zawiera jednookienkowy widok dotyczący tego, co należy rozwiązać, aby zapewnić obsługę nowego zaplecza bazy danych. Aby dowiedzieć się więcej, zobacz [Migrowanie aplikacji Java z programu Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Wykonaj testy

Aby przetestować migrację bazy danych, wykonaj następujące działania:

1. **Opracowuj testy weryfikacyjne**. Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Utwórz zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.

2. **Skonfiguruj środowisko testowe**. Środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.

3. **Uruchom testy weryfikacyjne**. Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.

4. **Uruchom testy wydajnościowe**. Uruchom test wydajności na źródłowym i docelowym, a następnie Przeanalizuj i Porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania problemów z dokładnością danych i weryfikowania kompletności. Jest to również krytyczne w przypadku rozwiązywania problemów z wydajnością przy użyciu obciążenia.

> [!Note]
> Więcej informacji o tych problemach i określonych krokach w celu ich rozwiązania można znaleźć w [przewodniku po sprawdzeniu poprawności po migracji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Zasoby dotyczące migracji 

Aby uzyskać pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w celu zapewnienia obsługi rzeczywistego projektu migracji.

| **Tytuł/link**                                                                                                                                          | **Opis**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane najlepsze optymalne Platformy docelowe, gotowość chmury oraz poziomy korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste Obliczanie i generowanie raportów z jednym kliknięciem, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany i jednolity proces podejmowania decyzji na platformie docelowej.                                                          |
| [Artefakty skryptu spisu firmy Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ten element zawartości zawiera zapytanie PL/SQL, które jest ukierunkowane na tabele systemowe Oracle i zawiera liczbę obiektów według typu schematu, typu obiektu i stanu. Zapewnia także przybliżone oszacowanie danych pierwotnych w każdym schemacie oraz zmianę rozmiarów tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.                                                                                                               |
| [Automatyzowanie & konsolidowania danych oceny rozwiązań firmy Oracle ASYSTENCIE migracji](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ten zestaw zasobów używa pliku CSV jako wpisu (sources.csv w folderach projektu) do tworzenia plików XML potrzebnych do uruchamiania oceny ASYSTENCIE migracji w trybie konsoli. Plik source.csv można dostarczyć, pobierając spis istniejących wystąpień programu Oracle. Pliki wyjściowe to AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml i VariableValueFile.xml.|
| [ASYSTENCIE migracji problemy i możliwe środki zaradcze podczas migrowania baz danych Oracle](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Za pomocą programu Oracle można przypisać warunek nieskalarny w klauzuli WHERE. SQL Server nie obsługuje tego typu warunku. ASYSTENCIE migracji for Oracle nie konwertuje zapytań, które mają warunek nieskalarny w klauzuli WHERE. Zamiast tego generuje błąd: O2SS0001. Ten oficjalny dokument zawiera szczegółowe informacje o problemie i sposobach jego rozwiązania.          |
| [Podręcznik migracji firmy Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ten dokument koncentruje się na zadaniach skojarzonych z migrowaniem schematu programu Oracle do najnowszej wersji SQL Server. Jeśli migracja wymaga zmiany funkcji/funkcjonalności, należy uważnie rozważyć możliwy efekt każdej zmiany w aplikacjach korzystających z bazy danych programu.                                                     |


Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma na celu odblokowanie i przyspieszenie złożonej modernizacji projektów migracji na platformie danych na platformie danych Microsoft Azure.


## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić dostępność usług mających zastosowanie do SQL Server, zobacz [centrum infrastruktury globalnej platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne dla różnych scenariuszy migracji danych i danych oraz wyspecjalizowanych zadań, można znaleźć w temacie [usługi i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Program SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/)


- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń migrowanych do platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać informacje o licencjonowaniu, zobacz:
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Aby ocenić warstwę dostępu do aplikacji, Skorzystaj z [wersji zapoznawczej narzędzia do migracji dostępu do danych](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy A/B w przypadku dostępu do danych, zobacz [omówienie asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).


