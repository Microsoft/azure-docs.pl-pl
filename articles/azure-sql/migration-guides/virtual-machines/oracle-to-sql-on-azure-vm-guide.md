---
title: 'Oracle do SQL Server na maszynie wirtualnej platformy Azure: Przewodnik migracji'
description: W tym przewodniku pokazano, jak migrować schematy programu Oracle do SQL Server na maszynach wirtualnych platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 4173fe34c6966514ef4f150527cd712c9d95ab7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561716"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Przewodnik migracji: baza danych Oracle do SQL Server na maszynie wirtualnej platformy Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku pokazano, jak migrować schematy Oracle do SQL Server na maszynie wirtualnej platformy Azure przy użyciu Asystent migracji do programu SQL Server dla programu Oracle. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby migrować schemat Oracle do SQL Server na maszynie wirtualnej platformy Azure, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane.
- Aby pobrać [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Docelowa [maszyna wirtualna SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Odpowiednie [uprawnienia dla Asystencie migracji dla programu Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) i [dostawcy](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 


## <a name="pre-migration"></a>Przed migracją

Podczas przygotowywania do migracji do chmury Sprawdź, czy środowisko źródłowe jest obsługiwane i czy zostały spełnione wszystkie wymagania wstępne. Pomoże to zapewnić wydajną i pomyślną migrację.

Ta część procesu obejmuje przeprowadzenie spisu baz danych, które należy zmigrować, ocenianie tych baz danych pod kątem potencjalnych problemów z migracją lub blokowaniem, a następnie rozwiązanie wszystkich elementów, które mogły zostać odkryte. 

### <a name="discover"></a>Odnajdywanie

Użyj narzędzia [map Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) , aby zidentyfikować istniejące źródła danych i szczegółowe informacje o funkcjach, które są używane przez firmę do lepszego zrozumienia i zaplanowania migracji. Ten proces polega na skanowaniu sieci w celu zidentyfikowania wszystkich wystąpień programu Oracle w organizacji wraz z używaną wersją i funkcjami.

Aby użyć narzędzia MAP Toolkit do przeprowadzenia skanowania spisu, wykonaj następujące czynności: 

1. Otwórz [zestaw narzędzi mapy](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Wybierz pozycję **Utwórz/wybierz bazę danych**:

   ![Wybieranie bazy danych](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Wybierz pozycję **Utwórz bazę danych spisu**, wprowadź nazwę tworzonej nowej bazy danych spisu, podaj Krótki opis, a następnie wybierz przycisk **OK**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Tworzenie bazy danych spisu":::

1. Wybierz pozycję **Zbierz dane spisu** , aby otworzyć **Kreatora spisu i oceny**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Zbieranie danych spisu":::

1. W **Kreatorze spisu i oceny** wybierz pozycję **Oracle** , a następnie wybierz pozycję **dalej**:

   ![Wybierz Oracle](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Wybierz opcję wyszukiwania komputera, która najlepiej odpowiada wymaganiom biznesowym i środowisku, a następnie wybierz pozycję **dalej**: 

   ![Wybierz opcję wyszukiwania komputera, która najlepiej odpowiada potrzebom biznesowym](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Wprowadź poświadczenia lub Utwórz nowe poświadczenia dla systemów, które chcesz eksplorować, a następnie wybierz pozycję **dalej**:

    ![Wprowadź poświadczenia](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Ustaw kolejność poświadczeń, a następnie wybierz pozycję **dalej**:

   ![Ustaw kolejność poświadczeń](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Określ poświadczenia dla każdego komputera, który ma zostać odnajdywany. Możesz użyć unikatowych poświadczeń dla każdego komputera/maszyny lub wybrać opcję użycia listy **poświadczeń wszystkich komputerów** :


   ![Określ poświadczenia dla każdego komputera, który ma zostać odnajdowany](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Sprawdź podsumowanie wyboru, a następnie wybierz pozycję **Zakończ**:

   ![Podsumowanie przeglądu](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Po zakończeniu skanowania Wyświetl raport podsumowanie **zbierania danych** . Skanowanie może potrwać kilka minut i zależy od liczby baz danych. Po zakończeniu wybierz pozycję **Zamknij** :

   ![Raport z podsumowaniem kolekcji](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Wybierz **Opcje** , aby wygenerować raport dotyczący oceny i szczegółów bazy danych Oracle. Wybierz obie opcje (jeden według jednego), aby wygenerować raport.


### <a name="assess"></a>Ocena

Po zidentyfikowaniu źródeł danych Użyj [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) , aby ocenić wystąpienia Oracle migrowane do maszyny wirtualnej SQL Server, aby zrozumieć przerwy między nimi. Za pomocą Asystenta migracji można przeglądać obiekty i dane bazy danych, oceniać bazy danych do migracji, migrować obiekty bazy danych do SQL Server, a następnie migrować dane do SQL Server.

Aby utworzyć ocenę, wykonaj następujące kroki: 

1. Otwórz  [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację do zapisania projektu, a następnie wybierz SQL Server cel migracji z listy rozwijanej. Wybierz **przycisk OK**:

   ![Nowy projekt](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Wybierz pozycję **Połącz z bazą danych Oracle**. Wprowadź wartości w polach szczegóły połączenia Oracle w oknie dialogowym **łączenie z bazą danych Oracle** :

   ![Łączenie z bazą danych Oracle](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Wybierz schematy programu Oracle, które chcesz zmigrować: 

   ![Wybierz schemat Oracle](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle, który chcesz zmigrować, w **Eksploratorze metadanych Oracle**, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po wybraniu bazy danych:

   ![Utwórz raport](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. W **Eksploratorze metadanych Oracle** wybierz schemat Oracle, a następnie wybierz pozycję **Utwórz raport** , aby wygenerować raport HTML z statystykami konwersji i błędami/ostrzeżeniami, jeśli istnieje.
1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Oracle i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects. 

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`
    
   ![Raport konwersji](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)

### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typów** :

   ![Mapowania typów](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych Oracle**. 

### <a name="convert-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Aby skonwertować kwerendy dynamiczne lub ad hoc, kliknij prawym przyciskiem myszy węzeł i wybierz polecenie **Dodaj instrukcję**.
1. Wybierz pozycję **Połącz z SQL Serverą** z paska nawigacyjnego górnego wiersza. 
     1. Wprowadź szczegóły połączenia dla SQL Server na maszynie wirtualnej platformy Azure. 
     1. Wybierz docelową bazę danych z listy rozwijanej lub podaj nową nazwę. w takim przypadku baza danych zostanie utworzona na serwerze docelowym. 
     1. Podaj szczegóły uwierzytelniania. 
     1. Wybierz pozycję **Połącz**. 

   ![Nawiązywanie połączenia z serwerem SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle w **Eksploratorze metadanych Oracle** i wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego:

   ![Konwertuj schemat](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Po zakończeniu konwersji Porównaj i przejrzyj przekonwertowane obiekty do oryginalnych obiektów, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń:

   ![Zapoznaj się z zaleceniami](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnymi procedurami składowanymi i zapoznaj się z zaleceniami: 

   ![Przejrzyj kod rekomendacji](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Projekt można zapisać lokalnie dla ćwiczenia korygowania schematu w trybie offline. Możesz to zrobić, wybierając pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w SQL Server.

1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w celu SQL Server na maszynie wirtualnej platformy Azure.


## <a name="migrate"></a>Migrate

Po spełnieniu wymaganych wymagań wstępnych i ukończeniu zadań skojarzonych z etapem **sprzed migracji** można przystąpić do wykonania migracji schematu i danych. Migracja obejmuje dwa kroki — opublikowanie schematu i Migrowanie danych. 


Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki: 

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych SQL Server**  i wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja publikuje schemat Oracle do SQL Server na maszynie wirtualnej platformy Azure:

   ![Synchronizuj z bazą danych](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Przegląd stanu synchronizacji](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Migruj dane: kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych Oracle**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru:

   ![Migrowanie danych](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Podaj szczegóły połączenia dla programu Oracle i SQL Server na maszynie wirtualnej platformy Azure w oknie dialogowym.
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**:  

    ![Raport dotyczący migracji danych](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Połącz się z SQL Server w wystąpieniu maszyny wirtualnej platformy Azure przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat:

   ![Weryfikuj w ASYSTENCIE migracji](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)


Oprócz korzystania z usługi ASYSTENCIE migracji można również migrować dane przy użyciu SQL Server Integration Services (SSIS). Aby dowiedzieć się więcej, zobacz: 
- Artykuł [wprowadzenie z SQL Server Integration Services](//sql/integration-services/sql-server-integration-services).
- Oficjalny dokument [SQL Server Integration Services: SSIS dla platformy Azure i hybrydowego przenoszenia danych](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu **migracji** należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

[Zestaw narzędzi do migracji dostępu do danych](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) to rozszerzenie Visual Studio Code, które pozwala analizować kod źródłowy Java oraz wykrywać wywołania interfejsu API i zapytania dotyczące dostępu do danych, dostarczając widok z jednym okienkiem, co należy rozwiązać, aby zapewnić obsługę nowego zaplecza bazy danych. Aby dowiedzieć się więcej, zobacz blog [Migrowanie naszej aplikacji Java z firmy Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje wykonywanie następujących czynności:

1. **Opracowuj testy weryfikacyjne**. Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.

2. **Konfigurowanie środowiska testowego**. Środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.

3. **Uruchom testy weryfikacyjne**. Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.

4. **Uruchom testy wydajnościowe**. Uruchom test wydajności na źródłowym i docelowym, a następnie Przeanalizuj i Porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia.

> [!Note]
> Aby uzyskać dodatkowe informacje o tych problemach i konkretnych krokach, które należy rozwiązać, zobacz [Przewodnik po weryfikacji i optymalizacji po migracji](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w ramach obsługi projektu migracji rzeczywistej.

| **Tytuł/link**                                                                                                                                          | **Opis**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które znacznie ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.                                                          |
| [Artefakty skryptu spisu firmy Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ten element zawartości zawiera zapytanie PL/SQL, które trafi w tabele systemowe Oracle i udostępnia liczbę obiektów według typu schematu, typu obiektu i stanu. Zapewnia także przybliżone oszacowanie "nieprzetworzonych danych" w każdym schemacie oraz zmianę rozmiarów tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.                                                                                                               |
| [Automatyzowanie & konsolidowania danych oceny rozwiązań firmy Oracle ASYSTENCIE migracji](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ten zestaw zasobów używa pliku CSV jako wpisu (sources.csv w folderach projektu) do tworzenia plików XML, które są konieczne do uruchamiania oceny ASYSTENCIE migracji w trybie konsoli. source.csv jest udostępniana przez klienta w oparciu o spis istniejących wystąpień programu Oracle. Pliki wyjściowe to AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml i VariableValueFile.xml.|
| [ASYSTENCIE migracji dla typowych błędów Oracle i sposoby ich naprawiania](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Za pomocą programu Oracle można przypisać warunek nieskalarny w klauzuli WHERE. Jednak SQL Server nie obsługuje tego typu warunku. W związku z tym Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu Oracle nie konwertuje zapytań z warunkiem nieskalarnym w klauzuli WHERE zamiast generowania błędu O2SS0001. Ten oficjalny dokument zawiera więcej informacji o problemie i sposobach jego rozwiązania.          |
| [Podręcznik migracji firmy Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ten dokument koncentruje się na zadaniach skojarzonych z migrowaniem schematu programu Oracle do najnowszej wersji SQL Server. Jeśli migracja wymaga zmiany funkcji/funkcjonalności, można uważnie rozważyć ewentualne konsekwencje każdej zmiany w aplikacjach korzystających z bazy danych.                                                     |

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić dostępność usług mających zastosowanie do SQL Server zobacz [centrum infrastruktury globalnej platformy Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database) .

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Program SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać informacje o licencjonowaniu, zobacz
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).