---
title: Łączenie się z usługą Azure Cosmos DB przy użyciu narzędzi analizy biznesowej
description: Dowiedz się, jak używać sterownika Azure Cosmos DB ODBC do tworzenia tabel i widoków, aby można było wyświetlać znormalizowane dane w oprogramowaniu analizy biznesowej i danych.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339788"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Łączenie się z usługą Azure Cosmos DB przy użyciu narzędzi analizy biznesowej ze sterownikiem ODBC
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Sterownik Azure Cosmos DB ODBC umożliwia łączenie się z Azure Cosmos DB przy użyciu narzędzi analizy biznesowej, takich jak SQL Server Integration Services, Power BI Desktop i Tableau, dzięki czemu można analizować i tworzyć wizualizacje danych Azure Cosmos DB w tych rozwiązaniach.

Sterownik ODBC usługi Azure Cosmos DB jest zgodny ze sterownikiem ODBC 3.8 i obsługuje składnię ANSI SQL-92. Sterownik udostępnia zaawansowane funkcje ułatwiające ponowne normalizowanie danych w usłudze Azure Cosmos DB. Przy użyciu sterownika można przedstawiać dane w usłudze Azure Cosmos DB w formie tabel i widoków. Sterownik umożliwia wykonywanie operacji SQL dotyczących tabel i widoków, w tym grupowanie według zapytań, wstawień, aktualizacji i usunięć.

> [!NOTE]
> Nawiązywanie połączenia z Azure Cosmos DB za pomocą sterownika ODBC jest obecnie obsługiwane tylko dla Azure Cosmos DB kont interfejsu API SQL.

## <a name="why-do-i-need-to-normalize-my-data"></a>Dlaczego muszę znormalizować dane?
Azure Cosmos DB to bezschematowa baza danych, która umożliwia szybkie opracowywanie aplikacji i możliwość wykonywania iteracji w modelach danych bez ograniczania do ścisłego schematu. Pojedyncza baza danych usługi Azure Cosmos może zawierać dokumenty JSON o różnych strukturach. Jest to doskonałe rozwiązanie do szybkiego tworzenia aplikacji, ale jeśli chcesz analizować i tworzyć raporty dotyczące danych przy użyciu narzędzi do analizy danych i analizy biznesowej, dane często muszą być spłaszczone i zgodne z określonym schematem.

Jest to miejsce, w którym znajduje się sterownik ODBC. Za pomocą sterownika ODBC, można teraz ponownie znormalizować dane w Azure Cosmos DB do tabel i widoków, które pasują do potrzeb związanych z analizą danych i raportowaniem. Nieznormalizowane schematy nie mają wpływu na dane bazowe i nie ograniczają deweloperów do ich przestrzegania. Zamiast tego umożliwiają dostęp do danych za pomocą narzędzi zgodnych ze standardem ODBC. W związku z tym teraz Twoja baza danych Azure Cosmos nie będzie ulubiona dla Twojego zespołu programistycznego, ale Twoje analityki danych pozostaną zbyt.

Zacznijmy od sterownika ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Krok 1. Instalowanie sterownika Azure Cosmos DB ODBC

1. Pobierz sterowniki dla środowiska:

    | Instalator | Obsługiwane systemy operacyjne| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) dla 64-bitowego systemu Windows| 64-bitowe wersje Windows 8.1 lub nowszych, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) dla 32-bitowego w 64-bitowej wersji systemu Windows| 64-bitowe wersje Windows 8.1 lub nowszych, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 i Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) dla 32-bitowego systemu Windows|32-bitowe wersje Windows 8.1 lub nowszych, Windows 8, Windows 7, Windows XP i Windows Vista.|

    Uruchom lokalnie plik msi, co spowoduje uruchomienie **Kreatora instalacji sterownika ODBC Microsoft Azure Cosmos DB**. 

1. Ukończ pracę Kreatora instalacji, używając domyślnych danych wejściowych, aby zainstalować sterownik ODBC.

1. Otwórz na komputerze aplikację **administratora źródła danych ODBC** . Można to zrobić, wpisując **źródła danych ODBC** w polu wyszukiwania systemu Windows. 
    Zainstalowanie sterownika można potwierdzić, klikając kartę **sterowniki** i upewniając się, że na liście znajduje się **Microsoft Azure Cosmos DB sterownik ODBC** .

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Administrator źródła danych Azure Cosmos DB ODBC":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Krok 2. nawiązanie połączenia z bazą danych Azure Cosmos

1. Po [zainstalowaniu Azure Cosmos DB sterownika ODBC](#install), w oknie **Administrator źródła danych ODBC** kliknij przycisk **Dodaj**. Można utworzyć użytkownika lub systemową nazwę DSN. W tym przykładzie tworzysz nazwę DSN użytkownika.

1. W oknie **Tworzenie nowego źródła danych** wybierz pozycję **Microsoft Azure Cosmos DB sterownika ODBC** , a następnie kliknij przycisk **Zakończ**.

1. W oknie **instalatora Azure Cosmos DB ODBC Driver SDN** wprowadź następujące informacje: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Okno instalacji DSN sterownika ODBC Azure Cosmos DB":::
    - **Nazwa źródła danych** : przyjazna nazwa DSN ODBC. Ta nazwa jest unikatowa dla konta Azure Cosmos DB, więc nazwij ją odpowiednio, jeśli masz wiele kont.
    - **Opis** : Krótki opis źródła danych.
    - **Host** : identyfikator URI konta Azure Cosmos DB. Można go pobrać ze strony klucze Azure Cosmos DB w Azure Portal, jak pokazano na poniższym zrzucie ekranu. 
    - **Klucz dostępu** : podstawowy lub pomocniczy klucz do odczytu lub zapisu ze strony Azure Cosmos DB klucze w Azure Portal, jak pokazano na poniższym zrzucie ekranu. Zalecamy użycie klucza tylko do odczytu, jeśli nazwa DSN jest używana do przetwarzania i raportowania danych tylko do odczytu.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Strona kluczy Azure Cosmos DB":::
    - **Szyfruj klucz dostępu dla** : Wybierz najlepszy wybór na podstawie użytkowników tego komputera. 
    
1. Kliknij przycisk **Testuj** , aby upewnić się, że możesz nawiązać połączenie z kontem Azure Cosmos DB. 

1.  Kliknij pozycję **Opcje zaawansowane** i ustaw następujące wartości:
    *  **Wersja interfejsu API REST** : Wybierz [wersję interfejsu API REST](/rest/api/cosmos-db/) dla operacji. Wartość domyślna 2015-12-16. Jeśli masz kontenery z [dużymi kluczami partycji](large-partition-keys.md) i potrzebujesz interfejsu API REST w wersji 2018-12-31:
        - Wpisz **2018-12-31** dla wersji interfejsu API REST
        - W menu **Start** wpisz ciąg "regedit", aby znaleźć i otworzyć aplikację **Edytor rejestru** .
        - W Edytorze rejestru przejdź do ścieżki: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - Utwórz nowy podklucz o takiej samej nazwie jak nazwa DSN, np. "konto contoso ODBC DSN".
        - Przejdź do podklucza "konto contoso ODBC DSN".
        - Kliknij prawym przyciskiem myszy, aby dodać nową wartość **ciągu** :
            - Nazwa wartości: **IgnoreSessionToken**
            - Dane wartości: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="Ustawienia edytora rejestru":::
    - **Spójność zapytań** : Wybierz [poziom spójności](consistency-levels.md) dla operacji. Wartość domyślna to Session.
    - **Liczba ponownych prób** : Określ liczbę ponownych prób wykonania operacji, jeśli początkowe żądanie nie zostało ukończone z powodu ograniczenia szybkości usługi.
    - **Plik schematu** : masz tutaj kilka opcji.
        - Domyślnie pozostawienie tego wpisu jako (pustego) powoduje, że sterownik skanuje pierwszą stronę danych dla wszystkich kontenerów, aby określić schemat każdego kontenera. Jest to tzw. mapowanie kontenerów. Bez zdefiniowanego pliku schematu, Sterownik musi wykonać skanowanie dla każdej sesji sterownika i może skutkować większym czasem uruchamiania aplikacji przy użyciu nazwy DSN. Zalecamy, aby zawsze skojarzyć plik schematu dla nazwy DSN.
        - Jeśli masz już plik schematu (prawdopodobnie taki został utworzony za pomocą Edytora schematu), możesz kliknąć przycisk **Przeglądaj** , przejść do pliku, kliknąć przycisk **Zapisz** , a następnie kliknąć przycisk **OK**.
        - Jeśli chcesz utworzyć nowy schemat, kliknij przycisk **OK** , a następnie kliknij przycisk **Edytor schematu** w oknie głównym. Następnie przechodzenie do informacji Edytora schematu. Po utworzeniu nowego pliku schematu Pamiętaj, aby wrócić do okna **Opcje zaawansowane** , aby uwzględnić nowo utworzony plik schematu.

1. Po zakończeniu i zamknięciu okna **instalacji dsn Azure Cosmos DB ODBC** nowe DSN użytkownika zostanie dodane do karty DSN użytkownika.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Nowe Azure Cosmos DB ODBC DSN na karcie DSN użytkownika":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Krok 3. Tworzenie definicji schematu przy użyciu metody mapowania kontenera

Istnieją dwa typy metod próbkowania, których można użyć: **mapowania kontenerów** lub **ograniczników tabel**. Sesja próbkowania może wykorzystywać obydwie metody próbkowania, ale każdy kontener może korzystać tylko z określonej metody próbkowania. Poniższe kroki tworzą schemat dla danych w jednym lub większej liczbie kontenerów przy użyciu metody mapowania kontenera. Ta metoda próbkowania pobiera dane na stronie kontenera w celu określenia struktury danych. Powoduje to przełączenie kontenera do tabeli po stronie ODBC. Ta metoda próbkowania jest wydajna i szybka, gdy dane w kontenerze są jednorodne. Jeśli kontener zawiera niejednorodny typ danych, zalecamy użycie [metody mapowania tabeli ograniczników](#table-mapping) , ponieważ zapewnia ona bardziej niezawodną metodę próbkowania w celu określenia struktur danych w kontenerze. 

1. Po wykonaniu kroków 1-4 w [nawiązaniu połączenia z bazą danych Azure Cosmos](#connect)kliknij pozycję **Edytor schematu** w oknie **Konfiguracja DSN sterownika ODBC Azure Cosmos DB** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Przycisk Edytor schematu w oknie instalatora DSN Azure Cosmos DB ODBC":::
1. W oknie **Edytor schematu** kliknij pozycję **Utwórz nowy**.
    W oknie **Generuj schemat** są wyświetlane wszystkie kontenery na koncie Azure Cosmos DB. 

1. Wybierz co najmniej jeden kontener do próbkowania, a następnie kliknij pozycję **przykład**. 

1. Na karcie **Widok projektu** są reprezentowane bazy danych, schematu i tabeli. W widoku tabeli skanowanie Wyświetla zestaw właściwości skojarzonych z nazwami kolumn (nazwa SQL, nazwa źródła itp.).
    Dla każdej kolumny można zmodyfikować nazwę SQL kolumny, typ SQL, Długość SQL (jeśli ma to zastosowanie), skalować (jeśli dotyczy), dokładnooć (jeśli dotyczy) i dopuszczać wartość null.
    - Możesz ustawić **wartość true** dla opcji **Ukryj kolumnę** , jeśli chcesz wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Hide (Ukryj kolumnę = true) nie są zwracane do wyboru i projekcji, chociaż nadal są częścią schematu. Można na przykład ukryć wszystkie właściwości wymagane przez system Azure Cosmos DB, zaczynając od "_".
    - Kolumna **ID** jest jedynym polem, które nie może być ukryte, ponieważ jest używane jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania **schematu kliknij pozycję**  |  **Zapisz** , przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. Aby użyć tego schematu z DSN, Otwórz **okno instalacji Azure Cosmos DB ODBC Driver DSN** (za pomocą administratora źródła danych ODBC), kliknij przycisk **Opcje zaawansowane** , a następnie w polu **plik schematu** przejdź do zapisanego schematu. Zapisanie pliku schematu do istniejącej nazwy DSN powoduje modyfikację połączenia DSN do zakresu danych i struktury zdefiniowanej przez schemat.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Krok 4. Tworzenie definicji schematu przy użyciu metody mapowania tabeli ograniczników

Istnieją dwa typy metod próbkowania, których można użyć: **mapowania kontenerów** lub **ograniczników tabel**. Sesja próbkowania może wykorzystywać obydwie metody próbkowania, ale każdy kontener może korzystać tylko z określonej metody próbkowania. 

Poniższe kroki tworzą schemat dla danych w jednym lub większej liczbie kontenerów przy użyciu metody mapowania **ograniczników tabel** . Zalecamy używanie tej metody próbkowania, gdy kontenery zawierają heterogeniczny typ danych. Za pomocą tej metody można ograniczyć próbkowanie do zestawu atrybutów i odpowiednich wartości. Na przykład, jeśli dokument zawiera właściwość "Type", można ograniczyć próbkowanie do wartości tej właściwości. Końcowy wynik próbkowania będzie zestawem tabel dla każdej wartości określonego typu. Na przykład typ = samochód spowoduje utworzenie tabeli samochodu, podczas gdy typ = płaszczyzna utworzy tabelę płaszczyzny.

1. Po wykonaniu kroków 1-4 w [nawiązaniu połączenia z bazą danych Azure Cosmos](#connect)kliknij pozycję **Edytor schematu** w oknie konfiguracja DSN sterownika ODBC Azure Cosmos DB.

1. W oknie **Edytor schematu** kliknij pozycję **Utwórz nowy**.
    W oknie **Generuj schemat** są wyświetlane wszystkie kontenery na koncie Azure Cosmos DB. 

1. Wybierz kontener na karcie **Widok przykładowy** , w kolumnie **Definicja mapowania** dla kontenera, kliknij przycisk **Edytuj**. Następnie w oknie **Definicja mapowania** wybierz opcję **ograniczniki tabeli** . Następnie wykonaj poniższe czynności:

    a. W polu **atrybuty** wpisz nazwę właściwości ogranicznika. Jest to właściwość w dokumencie, do której chcesz określić zakres próbkowania, na przykład miasto i naciśnij klawisz ENTER. 

    b. Jeśli chcesz tylko określić zakres próbkowania do określonych wartości atrybutu wprowadzonego powyżej, zaznacz atrybut w polu zaznacz, wprowadź wartość w polu **wartość** (np. Seattle) i naciśnij klawisz ENTER. Można nadal dodawać wiele wartości dla atrybutów. Upewnij się, że podczas wprowadzania wartości jest wybierany poprawny atrybut.

    Na przykład jeśli dołączysz wartość **atrybutu** miasto i chcesz ograniczyć tabelę do uwzględnienia tylko wierszy z wartością miasto New York i Dubaj, wpisz miasto w polu atrybuty, a Nowy Jork, a następnie Dubaj w polu **wartości** .

1. Kliknij pozycję **OK**. 

1. Po zakończeniu definiowania mapowania dla kontenerów, które chcesz próbkować, w oknie **Edytor schematu** kliknij **przykład**.
     Dla każdej kolumny można zmodyfikować nazwę SQL kolumny, typ SQL, Długość SQL (jeśli ma to zastosowanie), skalować (jeśli dotyczy), dokładnooć (jeśli dotyczy) i dopuszczać wartość null.
    - Możesz ustawić **wartość true** dla opcji **Ukryj kolumnę** , jeśli chcesz wykluczyć tę kolumnę z wyników zapytania. Kolumny oznaczone jako Hide (Ukryj kolumnę = true) nie są zwracane do wyboru i projekcji, chociaż nadal są częścią schematu. Można na przykład ukryć wszystkie właściwości wymagane przez system Azure Cosmos DB, zaczynając od `_` .
    - Kolumna **ID** jest jedynym polem, które nie może być ukryte, ponieważ jest używane jako klucz podstawowy w znormalizowanym schemacie. 

1. Po zakończeniu definiowania **schematu kliknij pozycję**  |  **Zapisz** , przejdź do katalogu, aby zapisać schemat, a następnie kliknij przycisk **Zapisz**.

1. W oknie **Konfiguracja DSN sterownika ODBC Azure Cosmos DB** kliknij przycisk **Opcje zaawansowane**. Następnie w polu **plik schematu** przejdź do zapisanego pliku schematu, a następnie kliknij przycisk **OK**. Kliknij ponownie przycisk **OK** , aby zapisać nazwę DSN. Spowoduje to zapisanie schematu utworzonego w DSN. 

## <a name="optional-set-up-linked-server-connection"></a>Obowiązkowe Skonfiguruj połączenie połączonego serwera

Możesz wysyłać zapytania do Azure Cosmos DB z SQL Server Management Studio (SSMS) przez skonfigurowanie połączenia połączonego serwera.

1. Utwórz systemowe źródło danych zgodnie z opisem w [kroku 2](#connect), o nazwie na przykład `SDS Name` .

1. [Zainstaluj SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i nawiąż połączenie z serwerem. 

1. W edytorze zapytań programu SSMS Utwórz obiekt połączonego serwera `DEMOCOSMOS` dla źródła danych za pomocą poniższych poleceń. Zamień na `DEMOCOSMOS` nazwę połączonego serwera i `SDS Name` nazwę źródła danych systemu.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Aby wyświetlić nową nazwę połączonego serwera, Odśwież listę połączonych serwerów.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Połączony serwer w programie SSMS":::

### <a name="query-linked-database"></a>Kwerenda połączonej bazy danych

Aby wykonać zapytanie dotyczące połączonej bazy danych, wprowadź zapytanie programu SSMS. W tym przykładzie zapytanie wybiera się z tabeli w kontenerze o nazwie `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Wykonaj zapytanie. Wynik powinien wyglądać podobnie do tego:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Połączony serwer Cosmos DB nie obsługuje nazewnictwa czterech części. Zwracany jest błąd podobny do następującego:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>Obowiązkowe Tworzenie widoków
Możesz definiować i tworzyć widoki w ramach procesu pobierania próbek. Te widoki są równoważne z widokami SQL. Są one tylko do odczytu i określają zakres wyboru i projekcje Azure Cosmos DB zdefiniowanego zapytania SQL. 

Aby utworzyć widok dla danych, w oknie **Edytor schematu** w kolumnie **Definicje widoku** kliknij przycisk **Dodaj** w wierszu kontenera do przykładu. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Tworzenie widoku danych":::


Następnie w oknie **Definicje widoku** wykonaj następujące czynności:

1. Kliknij pozycję **Nowy** , wprowadź nazwę widoku, na przykład EmployeesfromSeattleView, a następnie kliknij przycisk **OK**.

1. W oknie **Edycja widoku** wprowadź zapytanie Azure Cosmos DB. Musi to być [Azure Cosmos DB zapytania SQL](./sql-query-getting-started.md), na przykład `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` , a następnie kliknąć przycisk **OK**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Dodaj zapytanie podczas tworzenia widoku":::


Możesz utworzyć wiele widoków. Po zakończeniu definiowania widoków można następnie próbkować dane. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5. Wyświetlanie danych w narzędziach analizy biznesowej, takich jak Power BI Desktop

Możesz użyć nowego DSN do łączenia się z Azure Cosmos DB przy użyciu dowolnego narzędzia zgodnego z ODBC — ten krok pokazuje, jak nawiązać połączenie z Power BI Desktop i utworzyć wizualizację Power BI.

1. Otwórz program Power BI Desktop.

1. Kliknij pozycję **Pobierz dane**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Pobieranie danych w Power BI Desktop":::

1. W oknie **pobieranie danych** kliknij pozycję **inne**  |  **ODBC**  |  **łączenie** ODBC.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Wybierz źródło danych ODBC w Power BI Pobierz dane":::

1. W oknie **z ODBC** Wybierz utworzoną nazwę źródła danych, a następnie kliknij przycisk **OK**. Możesz pozostawić puste pozycje **opcji zaawansowana** .

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Wybierz nazwę źródła danych (DSN) w Power BI Pobierz dane":::

1. W oknie **dostęp do źródła danych przy użyciu sterownika ODBC** wybierz opcję **domyślne lub niestandardowe** , a następnie kliknij przycisk **Połącz**. Nie trzeba dołączać **właściwości parametrów połączenia poświadczeń**.

1. W oknie **Nawigator** w lewym okienku rozwiń bazę danych, schemat, a następnie wybierz tabelę. Okienko wyników zawiera dane przy użyciu utworzonego schematu.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Wybierz tabelę w Power BI Pobierz dane":::

1. Aby wizualizować dane w Power BI pulpicie, zaznacz pole przed nazwą tabeli, a następnie kliknij przycisk **Załaduj**.

1. W Power BI Desktop po lewej stronie wybierz kartę dane, :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: Aby potwierdzić, że dane zostały zaimportowane. 

1. Możesz teraz tworzyć wizualizacje przy użyciu Power BI, klikając kartę Raport :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: , klikając pozycję **Nowa Wizualizacja** , a następnie dostosowując kafelek. Aby uzyskać więcej informacji na temat tworzenia wizualizacji w Power BI Desktop, zobacz [typy wizualizacji w Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony następujący błąd, upewnij się, że wartości **klucza** **hosta** i dostępu zostały skopiowane Azure Portal w [kroku 2](#connect) są poprawne, a następnie ponów próbę. Użyj przycisków kopiowania po prawej stronie wartości **hosta** i **klucz dostępu** w Azure Portal, aby skopiować wartości błędne.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [Azure Cosmos DB — Zapraszamy!](introduction.md)