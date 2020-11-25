---
title: Uruchamianie Azure Data Lake skryptów U-SQL na komputerze lokalnym
description: Dowiedz się, jak używać Azure Data Lake Tools for Visual Studio do uruchamiania zadań U-SQL na komputerze lokalnym.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018991"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Uruchamianie skryptów U-SQL na komputerze lokalnym

Podczas tworzenia skryptów U-SQL możesz zaoszczędzić czas i koszty, uruchamiając skrypty lokalnie. Azure Data Lake Tools for Visual Studio obsługuje uruchamianie skryptów U-SQL na komputerze lokalnym. 

## <a name="basic-concepts-for-local-runs"></a>Podstawowe pojęcia dotyczące uruchomień lokalnych

Poniższy wykres przedstawia składniki uruchamiania lokalnego oraz sposób mapowania tych składników do uruchomienia w chmurze.

|Składnik|Uruchamianie lokalne|Uruchomienie w chmurze|
|---------|---------|---------|
|Storage|Lokalny folder danych głównych|Domyślne konto Azure Data Lake Store|
|Compute|Lokalny aparat uruchamiania U-SQL|Usługa Azure Data Lake Analytics|
|Uruchom środowisko|Katalog roboczy na komputerze lokalnym|Klaster Azure Data Lake Analytics|

Poniższe sekcje zawierają więcej informacji na temat składników uruchamiania lokalnego.

### <a name="local-data-root-folders"></a>Lokalne foldery główne danych

Lokalny folder główny danych to **Magazyn lokalny** dla lokalnego konta obliczeniowego. Każdy folder w lokalnym systemie plików na komputerze lokalnym może być lokalnym folderem głównym danych. Jest to takie samo, jak domyślne konto Azure Data Lake Store konta Data Lake Analytics. Przełączenie do innego folderu głównego danych jest takie samo jak przełączanie na inne domyślne konto magazynu. 

Folder główny danych jest używany w następujący sposób:
- Metadane magazynu. Przykłady są bazami danych, tabelami, funkcjami zwracającymi tabele i zestawami.
- Wyszukaj ścieżki wejściowe i wyjściowe, które są zdefiniowane jako ścieżki względne w skryptach U-SQL. Przy użyciu ścieżek względnych łatwiej jest wdrożyć skrypty U-SQL na platformie Azure.

### <a name="u-sql-local-run-engines"></a>Lokalne aparaty uruchamiania języka U-SQL

Lokalny aparat uruchamiania U-SQL to **lokalne konto obliczeniowe** dla zadań U-SQL. Użytkownicy mogą uruchamiać zadania U-SQL lokalnie za Azure Data Lake Tools for Visual Studio. Lokalne uruchomienia są również obsługiwane za pomocą narzędzia wiersza polecenia i interfejsu programowania w Azure Data Lake U-SQL SDK. Dowiedz się więcej o [zestawie SDK U-SQL Azure Data Lake](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Katalogi robocze

Po uruchomieniu skryptu U-SQL, folder katalogu roboczego jest wymagany do buforowania wyników kompilacji, uruchamiania dzienników i wykonywania innych funkcji. W Azure Data Lake Tools for Visual Studio katalog roboczy jest katalogiem roboczym projektu U-SQL. Znajduje się w sekcji `<U-SQL project root path>/bin/debug>` . Katalog roboczy jest czyszczony za każdym razem, gdy zostanie wyzwolone nowe uruchomienie.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokalne uruchomienia w Microsoft Visual Studio

Azure Data Lake Tools for Visual Studio ma wbudowanego aparatu lokalnego uruchomienia. Narzędzia powierzchniją aparat jako lokalne konto obliczeniowe. Aby lokalnie uruchomić skrypt U-SQL, wybierz konto **komputera lokalnego** lub **lokalnego projektu** w menu rozwijanym marginesu edytora skryptu. Następnie wybierz pozycję **Prześlij**.

![Prześlij skrypt U-SQL do konta lokalnego](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokalne uruchomienia z kontem komputera lokalnego

Konto **komputera lokalnego** to udostępnione lokalne konto obliczeniowe z jednym lokalnym folderem głównym danych jako konto magazynu lokalnego. Domyślnie folder główny danych znajduje się w witrynie **C:\Users \<username> \AppData\Local\USQLDataRoot**. Jest on również konfigurowalny za poorednictwem **narzędzi**  >  **Data Lake**  >  **Opcje i ustawienia**.

![Konfigurowanie lokalnego folderu danych głównych](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt U-SQL jest wymagany do uruchomienia lokalnego. Katalog roboczy projektu U-SQL jest używany dla lokalnego uruchomienia skryptu U-SQL. Wyniki kompilacji, dzienniki uruchamiania i inne pliki związane z uruchomieniem zadania są generowane i przechowywane w folderze katalogu roboczego podczas lokalnego uruchomienia. Po każdym ponownym uruchomieniu skryptu wszystkie pliki w katalogu roboczym zostaną oczyszczone i ponownie wygenerowane.

## <a name="local-runs-with-a-local-project-account"></a>Lokalne uruchomienia z kontem w projekcie lokalnym

Konto **lokalnego projektu** jest odizolowane od projektu lokalne konto obliczeniowe dla każdego projektu z izolowanym lokalnym folderem danych głównych. Każdy aktywny projekt U-SQL, który zostanie otwarty w Eksplorator rozwiązań w programie Visual Studio, ma odpowiednie `(Local-project: <project name>)` konto. Konta są wymienione w obu Eksplorator serwera w programie Visual Studio i marginesie edytora skryptów U-SQL.  

Konto **lokalnego projektu** zapewnia czyste i izolowane środowisko programistyczne. Konto **komputera lokalnego** ma udostępniony lokalny folder główny danych, który przechowuje metadane i dane wejściowe i wyjściowe dla wszystkich zadań lokalnych. Jednak konto **lokalnego projektu** tworzy tymczasowy lokalny folder danych w katalogu roboczym u-SQL za każdym razem, gdy skrypt u-SQL jest uruchamiany. Ten tymczasowy folder główny danych jest czyszczony, gdy następuje ponowna kompilacja lub ponowne uruchomienie. 

Projekt U-SQL zarządza izolowanym lokalnym środowiskiem uruchomieniowym za pomocą odwołania do projektu i właściwości. Źródła danych wejściowych dla skryptów U-SQL można skonfigurować zarówno w projekcie, jak i w środowiskach baz danych, do których się odwołuje.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Zarządzanie wejściowym źródłem danych dla konta lokalnego projektu 

Projekt U-SQL tworzy lokalny folder główny danych i konfiguruje dane dla konta **lokalnego projektu** . Tymczasowy folder główny danych jest czyszczony i tworzony ponownie w katalogu roboczym projektu U-SQL za każdym razem, gdy następuje Odbudowywanie i lokalne uruchomienie. Wszystkie źródła danych, które są konfigurowane przez projekt U-SQL, są kopiowane do tego tymczasowego folderu głównego danych lokalnych przed uruchomieniem zadania lokalnego. 

Folder główny źródeł danych można skonfigurować. Kliknij prawym przyciskiem myszy **U-SQL project**  >  **Property**  >  **Źródło danych test** właściwości U-SQL. Po uruchomieniu skryptu U-SQL na koncie w **projekcie lokalnym** wszystkie pliki i podfoldery w folderze **źródła danych testowych** są kopiowane do tymczasowego folderu głównego danych lokalnych. Pliki znajdujące się w podfolderach są uwzględniane. Po uruchomieniu zadania lokalnego wyniki wyjściowe można także znaleźć w folderze tymczasowych lokalnych danych w katalogu roboczym projektu. Wszystkie te dane wyjściowe są usuwane i czyszczone, gdy projekt zostanie odbudowany i oczyszczony. 

![Konfigurowanie testowego źródła danych projektu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Zarządzaj przywoływanym środowiskiem bazy danych dla konta **lokalnego projektu** 

Jeśli zapytanie U-SQL używa programu lub kwerend z obiektami bazy danych U-SQL, przed rozpoczęciem lokalnego uruchamiania skryptu U-SQL należy przygotować środowiska bazy danych lokalnie. W przypadku konta **lokalnego projektu** zależności bazy danych u-SQL mogą być zarządzane przez odwołania projektu u-SQL. Możesz dodać odwołania do projektu bazy danych U-SQL do projektu U-SQL. Przed uruchomieniem skryptów U-SQL na koncie w **projekcie lokalnym** wszystkie bazy danych, do których istnieją odwołania, są wdrażane do tymczasowego folderu głównego danych lokalnych. Dla każdego przebiegu tymczasowy folder główny danych jest czyszczony jako nowe środowisko izolowane.

Zapoznaj się z tym artykułem powiązanym:
* Dowiedz się, jak zarządzać definicjami i odwołaniami bazy danych U-SQL w [projektach bazy danych u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Różnica między kontami **lokalnymi** i **lokalnymi w projekcie**

Konto **komputera lokalnego** symuluje konto Azure Data Lake Analytics na komputerach lokalnych użytkowników. Udostępnia ono to samo środowisko przy użyciu konta Azure Data Lake Analytics. Konto w **projekcie lokalnym** udostępnia przyjazne dla użytkownika środowisko programistyczne. To środowisko ułatwia użytkownikom wdrażanie odwołań do baz danych i danych wejściowych przed lokalnym uruchamianiem skryptów. Konto **komputera lokalnego** zapewnia udostępnione, trwałe środowisko, do którego można uzyskać dostęp za pomocą wszystkich projektów. Konto **lokalnego projektu** zapewnia izolowane środowisko programistyczne dla każdego projektu. Jest on odświeżany dla każdego przebiegu. Konto w **projekcie lokalnym** oferuje szybsze środowisko programistyczne dzięki szybkiemu zastosowaniu nowych zmian.

W poniższej tabeli przedstawiono więcej różnic między kontami **lokalnymi** i **lokalnymi projektu** .

|Kąt różnicy|Komputer lokalny|Projekt lokalny|
|----------------|---------------|---------------|
|Dostęp lokalny|Dostępne dla wszystkich projektów.|Tylko odpowiedni projekt może uzyskać dostęp do tego konta.|
|Lokalny folder danych głównych|Stały folder lokalny. Skonfigurowane za poorednictwem **narzędzi**  >  **Data Lake**  >  **Opcje i ustawienia**.|Folder tymczasowy utworzony dla każdego lokalnego uruchomienia w katalogu roboczym projektu U-SQL. Folder jest czyszczony, gdy następuje ponowne kompilowanie lub ponowne uruchomienie.|
|Dane wejściowe dla skryptu U-SQL|Ścieżka względna w folderze głównym stałego danych lokalnych.|Ustaw za poorednictwem źródła danych testu **właściwości projektu U-SQL**  >  **Test Data Source**. Wszystkie pliki i podfoldery są kopiowane do tymczasowego folderu głównego danych przed uruchomieniem lokalnym.|
|Dane wyjściowe dla skryptu U-SQL|Ścieżka względna w folderze głównym stałego danych lokalnych.|Dane wyjściowe do tymczasowego folderu głównego danych. Wyniki są czyszczone, gdy następuje ponowne kompilowanie lub ponowne uruchomienie.|
|Wdrożenie bazy danych, której dotyczy odwołanie|Bazy danych, do których występują odwołania, nie są wdrażane automatycznie podczas uruchamiania na koncie **komputera lokalnego** . Jest to taka sama dla przesyłania do konta Azure Data Lake Analytics.|Bazy danych, do których istnieją odwołania, są wdrażane automatycznie na koncie w **projekcie lokalnym** przed uruchomieniem lokalnym. Wszystkie środowiska bazy danych są czyszczone i ponownie wdrażane, gdy następuje ponowna kompilacja lub ponowna instalacja.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Lokalne uruchomienie z zestawem SDK U-SQL

Skrypty U-SQL można uruchamiać lokalnie w programie Visual Studio, a także używać Azure Data Lake U-SQL SDK do uruchamiania skryptów U-SQL lokalnie przy użyciu wiersza polecenia i interfejsów programowania. Korzystając z tych interfejsów, można zautomatyzować lokalne uruchomienia U-SQL i testy.

Dowiedz się więcej o [zestawie SDK U-SQL Azure Data Lake](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok](data-lake-analytics-cicd-overview.md)ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics.
- [Jak przetestować kod Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
