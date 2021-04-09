---
title: 'Samouczek: rejestrowanie zasobów danych w Azure Data Catalog'
description: W tym samouczku opisano sposób rejestrowania zasobów danych w Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675009"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Samouczek: rejestrowanie zasobów danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

W tym samouczku użyjesz narzędzia rejestracji do zarejestrowania zasobów danych z przykładowej bazy danych w wykazie. Rejestracja to proces wyodrębniania kluczowych metadanych strukturalnych, takich jak nazwy, typy i lokalizacje, ze źródła danych i zasobów, które się w nim znajdują, oraz kopiowania tych metadanych do wykazu. Źródło danych i zasoby danych pozostają tam, gdzie się znajdowały, ale metadane są używane przez wykaz, aby można było je łatwiej odnaleźć i zrozumieć.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rejestrowanie zasobów danych 
> * Wyszukaj zasoby danych
> * Dodawanie adnotacji do zasobów danych
> * Łączenie z zasobami danych
> * Zarządzanie zasobami danych
> * Usuwanie zasobów danych

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz zakończyć pracę z [przewodnikiem Szybki Start](register-data-assets-tutorial.md).

* Subskrypcję platformy [Microsoft Azure](https://azure.microsoft.com/).
* Musisz mieć własną [dzierżawę Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Aby skonfigurować Data Catalog, musisz być właścicielem lub współwłaścicielem subskrypcji platformy Azure.

## <a name="register-data-assets"></a>Rejestrowanie zasobów danych

### <a name="register-a-data-source"></a>Rejestrowanie źródła danych

Zasoby danych (tabele) można rejestrować z [przykładu bazy danych](../azure-sql/database/single-database-create-quickstart.md) dla Azure SQL Database, ale można użyć dowolnego obsługiwanego źródła danych, jeśli wolisz pracować z danymi, które są znane i istotne dla Twojej roli. Aby uzyskać listę obsługiwanych źródeł danych, zobacz [Supported data sources](data-catalog-dsr.md) (Obsługiwane źródła danych).

Nazwa bazy danych, której używamy w tym samouczku, to *RLSTest*.

Teraz można rejestrować zasoby danych z przykładowej bazy danych przy użyciu Azure Data Catalog.

1. Przejdź do [strony głównej Azure Data Catalog](http://azuredatacatalog.com) i wybierz pozycję **Publikuj dane**.

   ![Usługa Azure Data Catalog — przycisk Publikuj dane](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Wybierz pozycję **Uruchom aplikację** , aby pobrać, zainstalować i uruchomić Narzędzie rejestracji na komputerze.

   ![Usługa Azure Data Catalog — przycisk Uruchom](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Na stronie **powitalnej** wybierz pozycję **Zaloguj** i wprowadź swoje poświadczenia.

    ![Usługa Azure Data Catalog — strona powitalna](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Na stronie **Microsoft Azure Data Catalog** wybierz pozycję **SQL Server** i **dalej**.

    ![Usługa Azure Data Catalog — źródła danych](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Wprowadź SQL Server właściwości połączenia dla przykładowej bazy danych w Azure SQL Database a następnie wybierz pozycję **Połącz**.

   ![Usługa Azure Data Catalog — ustawienia połączenia programu SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Zarejestruj metadane zasobów danych. W tym przykładzie należy zarejestrować obiekty **produktu** z przykładowej przestrzeni nazw:

   1. W drzewie **hierarchii serwera** rozwiń przykład bazy danych, a następnie wybierz pozycję **tabeli SalesLT**.

   2. Wybierz pozycję **Product**, **ProductCategory**, **ProductDescription** i **ProductModel** za pomocą kombinacji klawiszy CTRL + SELECT.

   3. Wybierz **strzałkę w dół** ( **>** ). Spowoduje to przeniesienie wszystkich wybranych obiektów na listę **Obiekty do zarejestrowania**.

      ![Samouczek dotyczący usługi Azure Data Catalog — przeglądanie i wybieranie obiektów](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. Wybierz pozycję **Dołącz podgląd**, aby dołączyć podgląd danych w postaci migawki. Migawka zawiera do 20 rekordów z każdej tabeli i jest kopiowana do wykazu.

   5. Wybierz pozycję **Dołącz profil danych**, aby dołączyć migawkę statystyk obiektów dla profilu danych (na przykład liczbę wierszy czy minimalną, maksymalną i średnią wartość w kolumnie).

   6. W polu **Dodaj Tagi** wprowadź wartość **Sales, Product i Azure SQL**. Spowoduje to dodanie tagów wyszukiwania dla tych zasobów danych. Tagi to doskonały sposób na ułatwienie użytkownikom znalezienia zarejestrowanego źródła danych.

   7. Podaj imię i nazwisko **eksperta** w zakresie przeznaczenia tych danych (opcjonalnie).

      ![Samouczek dotyczący usługi Azure Data Catalog — obiekty do zarejestrowania](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. Wybierz pozycję **zarejestruj**. Wybrane obiekty zostaną zarejestrowane za pomocą usługi Azure Data Catalog. W tym ćwiczeniu zarejestrowano wybrane obiekty z przykładowej bazy danych. Za pomocą narzędzia rejestracji metadane są wyodrębniane z zasobów danych i kopiowane do usługi Azure Data Catalog. Dane pozostają tam, gdzie są obecne. Dane pozostają pod kontrolą administratorów i zasad systemu pochodzenia.

      ![Usługa Azure Data Catalog — zarejestrowane obiekty](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Aby wyświetlić zarejestrowane obiekty źródła danych, wybierz pozycję **Wyświetl Portal**. Upewnij się, że w portalu Azure Data Catalog są widoczne wszystkie cztery tabele i baza danych w widoku siatki (Sprawdź, czy pasek wyszukiwania jest wyczyszczony).

       ![Obiekty w portalu usługi Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

W tym ćwiczeniu zarejestrowano obiekty z przykładu bazy danych dla Azure SQL Database, dzięki czemu mogą one być łatwo odnajdywane przez użytkowników w organizacji.

W następnym ćwiczeniu zostanie przedstawiony sposób odnajdowania zarejestrowanych zasobów danych.

## <a name="discover-data-assets"></a>Odnajdywanie zasobów danych

Odnajdywanie w usłudze Azure Data Catalog korzysta z dwóch głównych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Wybierając określone cechy, takie jak eksperci, typ źródła danych, typu obiektu i tagi, można wyświetlić pasujące zasoby danych i ograniczyć wyniki wyszukiwania do tych zasobów.

Korzystając z kombinacji wyszukiwania i filtrowania, można szybko nawigować po źródłach danych, które są zarejestrowane w Azure Data Catalog.

To ćwiczenie obejmuje korzystanie z portalu usługi Azure Data Catalog w celu odnalezienia zasobów danych zarejestrowanych w poprzednim ćwiczeniu. Aby uzyskać szczegółowe informacje o składni wyszukiwania, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

Poniżej przedstawiono kilka przykładów dotyczących odnajdywania zasobów danych w wykazie.  

### <a name="discover-data-assets-with-basic-search"></a>Odnajdywanie zasobów danych przy użyciu wyszukiwania podstawowego

Wyszukiwanie podstawowe ułatwia przeszukiwanie wykazu za pomocą co najmniej jednego wyszukiwanego terminu. Wyniki obejmują wszystkie zasoby, których dowolne właściwości są zgodne z co najmniej jednym terminem.

1. Wybierz pozycję **Strona główna** w portalu Azure Data Catalog. Jeśli zamknięto przeglądarkę internetową, przejdź do [strony głównej Azure Data Catalog](https://www.azuredatacatalog.com).

2. W polu wyszukiwania wpisz `product` i naciśnij klawisz **ENTER**.

   ![Usługa Azure Data Catalog — podstawowe wyszukiwanie tekstowe](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Upewnij się, że w wynikach są widoczne wszystkie cztery tabele i baza danych. Możesz przełączać się między **widokiem siatki** a **widokiem listy** , wybierając przyciski na pasku narzędzi, jak pokazano na poniższej ilustracji. Zwróć uwagę, że wyszukiwane słowo kluczowe jest wyróżnione w wynikach wyszukiwania, ponieważ opcja **Wyróżnienie** jest **WŁĄCZONA**. Dodatkowo można określić liczbę **wyników wyszukiwania wyświetlanych na stronie**.

   ![Usługa Azure Data Catalog — wyniki podstawowego wyszukiwania tekstowego](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   Panel **Wyszukiwania** jest wyświetlany po lewej stronie, a panel **Właściwości** — po prawej stronie. W panelu **Wyszukiwania** można zmienić kryteria wyszukiwania i wyfiltrować wyniki. W panelu **Właściwości** są wyświetlane właściwości obiektu wybranego w siatce lub na liście.

4. Wybierz pozycję **Product (produkt** ) w wynikach wyszukiwania. Wybierz karty **Podgląd**, **kolumny**, **profil danych** i **Dokumentacja** , lub wybierz strzałkę, aby rozwinąć dolne okienko.  

   ![Usługa Azure Data Catalog — dolne okienko](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   Na karcie **Podgląd** zostanie wyświetlony podgląd danych z tabeli **Product**.

5. Wybierz kartę **kolumny** , aby znaleźć szczegółowe informacje o kolumnach (takich jak **Nazwa** i **Typ danych**) w obszarze zawartości danych.

6. Wybierz kartę **profil danych** , aby zobaczyć Profilowanie danych (na przykład: liczba wierszy, rozmiar danych lub wartość minimalna w kolumnie) w obszarze zawartości danych.

### <a name="discover-data-assets-with-property-scoping"></a>Odnajdywanie zasobów danych za pomocą wyznaczania zakresu właściwości

Wyznaczanie zakresu właściwości ułatwia odnajdywanie zasobów danych, gdy wyszukiwany termin zostanie dopasowany do określonej właściwości.

1. Wyczyść filtr **Tabela** w obszarze **Typ obiektu** w sekcji **Filtry**.  

2. W polu wyszukiwania wpisz `tags:product` i naciśnij klawisz **ENTER**. Aby uzyskać listę wszystkich właściwości, których można użyć do przeszukiwania wykazu danych, zobacz artykuł [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (Dokumentacja dotycząca składni wyszukiwania w usłudze Data Catalog).

3. Upewnij się, że tabele i baza danych są widoczne w wynikach.  

   ![Usługa Data Catalog — wyniki wyszukiwania uzyskane z użyciem wyznaczania zakresu właściwości](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Zapisywanie wyszukiwania

1. W okienku **wyszukiwania** w sekcji **Bieżące wyszukiwanie** wprowadź nazwę wyszukiwania i wybierz pozycję **Zapisz**.

   ![Usługa Azure Data Catalog — zapisywanie wyszukiwania](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Upewnij się, że zapisane wyszukiwanie jest wyświetlane w sekcji **Zapisane wyszukiwania**.

3. Wybierz jedną z akcji, które można zastosować do zapisanego wyszukiwania (**Zmień nazwę**, **Usuń**, **Zapisz jako domyślne**).

### <a name="grouping-with-parentheses"></a>Grupowanie za pomocą nawiasów

Nawiasy umożliwiają grupowanie części zapytania w celu uzyskania izolacji logicznej, szczególnie w połączeniu z operatorami logicznymi.

1. W polu wyszukiwania wpisz `name:product AND (tags:product AND objectType:table)` i naciśnij klawisz **ENTER**.

2. Upewnij się, że wyniki wyszukiwania obejmują tylko wiersze z tabeli **Product**.

   ![Usługa Azure Data Catalog — wyszukiwanie przy użyciu grupowania](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operatory porównania

Operatory porównania pozwalają używać innych porównań niż równość dla właściwości, które mają numeryczne i datowe typy danych.

1. W polu wyszukiwania wpisz `lastRegisteredTime:>"06/09/2016"`.

2. Wyczyść filtr **Tabela** w obszarze **Typ obiektu**.

3. Naciśnij klawisz **Enter**.

4. Upewnij się, że są widoczne tabele **Product**, **ProductCategory** i **ProductDescription** oraz baza danych SQL zarejestrowana w wynikach wyszukiwania.

   ![Usługa Azure Data Catalog — wyniki wyszukiwania z użyciem porównania](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zobacz [jak odnajdywać zasoby danych,](data-catalog-how-to-discover.md) Aby uzyskać szczegółowe informacje na temat odnajdywania zasobów danych. Aby uzyskać więcej informacji na temat składni wyszukiwania, zobacz [Data Catalog informacje o składni wyszukiwania](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Dodawanie adnotacji do zasobów danych

W tym ćwiczeniu Portal Azure Data Catalog jest używany do dodawania adnotacji (Dodawanie informacji, takich jak opisy, Tagi lub eksperci) istniejących zasobów danych w wykazie. Adnotacje uzupełniają metadane strukturalne wyodrębnione ze źródła danych podczas rejestracji. Adnotacja znacznie ułatwia odnajdywanie i zrozumienie zasobów danych.

W tym ćwiczeniu adnotacje zostaną dodane do pojedynczego zasobu danych (tabeli ProductPhoto). Do zasobu danych ProductPhoto zostanie dodana przyjazna nazwa oraz opis.  

1. Przejdź do [strony głównej Azure Data Catalog](https://www.azuredatacatalog.com) i Wyszukaj w usłudze, `tags:product` Aby znaleźć zarejestrowane zasoby danych.

2. Wybierz pozycję **ProductModel** w wynikach wyszukiwania.  

3. W polu **Przyjazna nazwa** wpisz **Obrazy produktów**, a w polu **Opis** wpisz **Zdjęcia produktów do materiałów marketingowych**.

    ![Usługa Azure Data Catalog — opis tabeli ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    Pole **Opis** ułatwia innym osobom odnajdywanie wybranego zasobu danych i zrozumienie, dlaczego i w jaki sposób należy go używać. Możesz również dodać więcej tagów i wyświetlić kolumny. Źródła danych można wyszukiwać i filtrować przy użyciu metadanych opisowych, które zostały dodane do wykazu.

Na tej stronie można również wykonać następujące czynności:

* Dodawać ekspertów dla zasobu danych. Wybierz pozycję **Dodaj** w obszarze **eksperci** .

* Dodawać tagi na poziomie zestawu danych. Wybierz pozycję **Dodaj** w obszarze **Tagi** . Możesz dodawać tagi użytkownika lub tagi słownika. Wersja Standard Edition usługi Data Catalog zawiera słownik biznesowy, który umożliwia administratorom wykazu definiowanie centralnej taksonomii biznesowej. Użytkownicy wykazu mogą następnie dodać adnotacje do zasobów danych za pomocą terminów ze słownika. Aby uzyskać więcej informacji, zobacz [jak skonfigurować słownik biznesowy dla tagowania regulowanego](data-catalog-how-to-business-glossary.md)

* Dodawać tagi na poziomie kolumny. Wybierz pozycję Dodaj w obszarze **Tagi** dla kolumny, do której chcesz **dodać** adnotację.

* Dodawać opisy na poziomie kolumny. W polu **Opis** wprowadź opis kolumny. Możesz również wyświetlić metadane opisu wyodrębnione ze źródła danych.

* Dodawać informacje dotyczące **żądania dostępu**, które ułatwiają użytkownikom uzyskanie dostępu do zasobu danych.
  
* Na karcie **Dokumentacja** podaj informacje dotyczące dokumentacji zasobu danych. Dzięki funkcji dokumentacji usługi Azure Data Catalog możesz użyć wykazu danych jako repozytorium zawartości i stworzyć kompletny opis zasobów danych.
  
Ponadto możesz dodawać adnotacje do wielu zasobów danych. Na przykład możesz zaznaczyć wszystkie zarejestrowane zasoby danych i określić dla nich eksperta.

![Usługa Azure Data Catalog — dodawanie adnotacji do wielu zasobów danych](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Usługa Azure Data Catalog obsługuje dodawanie adnotacji przez społeczność. Dowolny użytkownik Data Catalog może dodawać Tagi (użytkownika lub słownika), opisy i inne metadane. Dzięki temu użytkownicy będą mogli dodać perspektywę do zasobu danych i jego użycie i udostępnić tę perspektywę innym użytkownikom.

Szczegółowe informacje dotyczące dodawania adnotacji do zasobów danych można znaleźć w artykule [How to annotate data assets](data-catalog-how-to-annotate.md) (Jak dodawać adnotacje do zasobów danych).

## <a name="connect-to-data-assets"></a>Łączenie z zasobami danych

To ćwiczenie obejmuje otwieranie zasobów danych za pomocą zintegrowanego narzędzia klienckiego (programu Excel) oraz narzędzia niezintegrowanego (programu SQL Server Management Studio) przy użyciu informacji o połączeniu.

> [!NOTE]
> Należy pamiętać, że usługa Azure Data Catalog nie zapewnia dostępu do rzeczywistego źródła danych, a tylko ułatwia użytkownikom odnalezienie źródła danych i zrozumienie jego przeznaczenia. Podczas nawiązywania połączenia ze źródłem danych wybrana aplikacja kliencka używa poświadczeń systemu Windows lub w razie potrzeby wyświetla monit o ich podanie. Jeśli nie masz udzielonego wcześniej dostępu do źródła danych, musisz go uzyskać, aby nawiązać połączenie.

### <a name="connect-to-a-data-asset-from-excel"></a>Nawiązywanie połączenia z zasobem danych przy użyciu programu Excel

1. Wybierz pozycję **Product** w wynikach wyszukiwania. Wybierz pozycję **Otwórz w** na pasku narzędzi i wybierz pozycję **Excel**.

   ![Usługa Azure Data Catalog — nawiązywanie połączenia z zasobem danych](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. W oknie podręcznym pobieranie wybierz pozycję **Otwórz** . Wygląd elementów interfejsu zależy od przeglądarki.

3. W oknie **powiadomienia o zabezpieczeniach programu Microsoft Excel** wybierz pozycję **Włącz**.

   ![Usługa Azure Data Catalog — okno podręczne zabezpieczeń programu Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. W oknie dialogowym **Importowanie danych** Zachowaj wartości domyślne i wybierz **przycisk OK**.

   ![Usługa Azure Data Catalog — importowanie danych w programie Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Wyświetl źródło danych w programie Excel.

   ![Usługa Azure Data Catalog — tabela produktów w programie Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

W tym ćwiczeniu zostało nawiązane połączenie z zasobami danych odnalezionymi za pomocą usługi Azure Data Catalog. Portal usługi Azure Data Catalog umożliwia bezpośrednie nawiązywanie połączenia przy użyciu aplikacji klienckich zintegrowanych z menu **Otwórz w**. Możesz również nawiązywać połączenia za pomocą dowolnej wybranej aplikacji przy użyciu informacji o lokalizacji połączenia zawartych w metadanych zasobów. Można na przykład użyć SQL Server Management Studio, aby nawiązać połączenie z Azure SQL Database, aby uzyskać dostęp do danych w zasobach danych zarejestrowanych w tym samouczku.

1. Otwórz **SQL Server Management Studio**.

2. W oknie dialogowym **Połączenie z serwerem** wprowadź nazwę serwera z okienka **Właściwości** w portalu usługi Azure Data Catalog.

3. Wybierz właściwą metodę uwierzytelniania i wpisz odpowiednie poświadczenia, aby uzyskać dostęp do zasobu danych. Jeśli nie masz dostępu, skorzystaj z informacji zawartych w polu **Żądanie dostępu**, aby go uzyskać.

   ![Usługa Azure Data Catalog — żądanie dostępu](media/register-data-assets-tutorial/data-catalog-request-access.png)

Wybierz pozycję **Wyświetl parametry połączenia** , aby wyświetlić i skopiować parametry połączenia ADO.NET, ODBC i OLEDB do schowka, które mają być używane w aplikacji.

## <a name="manage-data-assets"></a>Zarządzanie zasobami danych

Ta procedura obejmuje konfigurowanie zabezpieczeń zasobów danych. Data Catalog nie daje użytkownikom dostępu do samych danych. Zarządzanie dostępem do danych należy do właściciela źródła danych.

Usługa Data Catalog umożliwia odnajdywanie źródeł danych zarejestrowanych w wykazie i wyświetlanie metadanych powiązanych z tymi źródłami. W niektórych sytuacjach może jednak wystąpić konieczność udostępnienia źródeł danych tylko konkretnym użytkownikom lub członkom określonych grup. W tych scenariuszach można użyć Data Catalog, aby przejąć własność zarejestrowanych zasobów danych i kontrolować widoczność posiadanych zasobów.

> [!NOTE]
> Funkcje zarządzania opisane w tym ćwiczeniu są dostępne tylko w wersji Standard Edition usługi Azure Data Catalog, a nie w wersji Free Edition.
> W usłudze Azure Data Catalog można przejmować na własność zasoby danych, dodawać współwłaścicieli zasobów danych i ustawiać widoczność zasobów danych.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Przejmowanie własności do zasobów danych i ograniczanie ich widoczności

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com). W polu tekstowym **Wyszukaj** wpisz `tags:cycles` i naciśnij klawisz **Enter**.

2. Wybierz element na liście wyników i wybierz pozycję **Przejmij** na pasku narzędzi.

3. W sekcji **Zarządzanie** panelu **Właściwości** wybierz pozycję **Przejmij na własność**.

    ![Usługa Azure Data Catalog — przejmowanie własności](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Aby ograniczyć widoczność, wybierz pozycję **właściciele & tych użytkowników** w sekcji **widoczność** , a następnie wybierz pozycję **Dodaj**. Wprowadź adresy e-mail użytkowników w polu tekstowym i naciśnij klawisz **ENTER**.

    ![Usługa Azure Data Catalog — ograniczanie dostępu](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Usuwanie zasobów danych

W tym ćwiczeniu portal usługi Azure Data Catalog zostanie użyty do usunięcia danych podglądu z zarejestrowanych zasobów danych i usunięcia zasobów danych z wykazu.

W usłudze Azure Data Catalog można usuwać pojedyncze zasoby lub wiele zasobów.

1. Przejdź do [strony głównej usługi Azure Data Catalog](https://www.azuredatacatalog.com).

2. W polu tekstowym **Wyszukiwanie** wprowadź `tags:cycles` i wybierz pozycję **wprowadź**.

3. Wybierz element na liście wyników i wybierz pozycję **Usuń** na pasku narzędzi, jak pokazano na poniższej ilustracji:

   ![Usługa Azure Data Catalog — usuwanie elementu siatki](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   Jeśli używasz widoku listy, pole wyboru znajduje się na lewo od elementu, jak pokazano na poniższej ilustracji:

   ![Usługa Azure Data Catalog — usuwanie elementu listy](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   Można również wybierać i usuwać kilka zasobów danych, jak pokazano na poniższej ilustracji:

   ![Usługa Azure Data Catalog — usuwanie kilku zasobów danych](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Domyślnym zachowaniem katalogu jest umożliwianie każdemu użytkownikowi rejestrowania dowolnego źródła danych oraz usuwania wszystkich zarejestrowanych zasobów danych. Funkcje zarządzania dostępne w wersji Standard Edition usługi Azure Data Catalog dodatkowo umożliwiają przejmowania własności do zasobów i ograniczanie grona użytkowników, którzy mogą odnajdywać i usuwać zasoby.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj kroki [usuwania zasobów danych](#remove-data-assets) , aby wyczyścić wszystkie zasoby, które mogły zostać użyte podczas pracy z tym samouczkiem.

## <a name="summary"></a>Podsumowanie

W tym samouczku zostały przedstawione podstawowe funkcje usługi Azure Data Catalog, w tym rejestrowanie, dodawanie adnotacji, odnajdywanie i zarządzanie firmowymi zasobami danych. Po zakończeniu tego samouczka nadszedł czas na rozpoczęcie pracy. Możesz rozpocząć dzisiaj od zarejestrowania źródeł danych, na którym pracujesz Ty i Twój zespół, oraz od zaproszenia współpracowników do korzystania z wykazu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługiwane źródła danych](data-catalog-dsr.md)
