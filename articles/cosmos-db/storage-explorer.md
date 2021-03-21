---
title: Zarządzanie zasobami Azure Cosmos DB przy użyciu Eksplorator usługi Azure Storage
description: Dowiedz się, jak nawiązać połączenie z usługą Azure Cosmos DB i zarządzać swoimi zasobami przy użyciu Eksplorator usługi Azure Storage.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348657"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Zarządzanie zasobami Azure Cosmos DB przy użyciu Eksplorator usługi Azure Storage
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Aby nawiązać połączenie z usługą Azure Cosmos DB, można użyć Eksploratora usługi Azure Storage. Umożliwia ona łączenie się z kontami Azure Cosmos DB hostowanymi na platformie Azure i w ramach suwerennych chmur z systemem Windows, macOS lub Linux.

Użyj tego samego narzędzia, aby zarządzać różnymi jednostkami platformy Azure w jednym miejscu. Możesz zarządzać jednostkami Azure Cosmos DB, manipulować danymi, aktualizować procedury składowane i wyzwalacze wraz z innymi jednostkami platformy Azure, takimi jak obiekty blob magazynu i kolejki. Eksplorator usługi Azure Storage obsługuje konta Cosmos skonfigurowane dla interfejsów API SQL, MongoDB, Graph i Table.

> [!NOTE]
> Funkcja integracji usługi Azure Cosmos DB z Eksploratorem usługi Storage Explorer została uznana za przestarzałą. Żadna istniejąca funkcjonalność nie zostanie usunięta przez minimum jeden rok od tego wydania. Zamiast tego należy użyć witryny [Azure](https://portal.azure.com/)Portal, [aplikacji klasycznej witryny Azure Portal](https://portal.azure.com/App/Download) lub autonomicznego [Eksploratora Azure Cosmos DB](data-explorer.md) . Opcje alternatywne obejmują wiele nowych funkcji, które nie są obecnie obsługiwane w Eksploratorze usługi Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Konto Cosmos z interfejsem API SQL lub interfejsem API Azure Cosmos DB dla MongoDB. Jeśli nie masz konta, możesz je utworzyć w Azure Portal. Zobacz [Azure Cosmos DB: Tworzenie aplikacji internetowej interfejsu API SQL za pomocą platformy .NET i Azure Portal,](create-sql-api-dotnet.md) Aby uzyskać więcej informacji.

## <a name="installation"></a>Instalacja

Aby zainstalować najnowszą Eksplorator usługi Azure Storage BITS, zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Obsługujemy wersje systemów Windows, Linux i macOS.

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

1. Po zainstalowaniu **Eksplorator usługi Azure Storage** wybierz ikonę **wtyczki** w okienku po lewej stronie.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Zrzut ekranu przedstawiający ikonę wtyczki w okienku po lewej stronie.":::

1. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie wybierz pozycję **Zaloguj się**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Zrzut ekranu przedstawiający okno łączenie z usługą Azure Storage z wybranym przyciskiem radiowym Dodaj konto platformy Azure i menu rozwijanego środowisko platformy Azure.":::

1. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Zrzut ekranu przedstawiający okno logowania pokazujący, gdzie wprowadzić poświadczenia dla subskrypcji platformy Azure.":::

1. Wybierz subskrypcję z listy, a następnie wybierz przycisk **Zastosuj**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Zrzut ekranu przedstawiający okienko Zarządzanie kontem z listą subskrypcji i przycisk Zastosuj.":::

    W okienku Eksploratora są aktualizowane i wyświetlane konta w wybranej subskrypcji.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Zrzut ekranu okienka Eksploratora, który został zaktualizowany, aby pokazać konta w wybranej subskrypcji.":::

    Twoje **konto Cosmos DB** jest połączone z subskrypcją platformy Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Użyj parametrów połączenia, aby nawiązać połączenie z Azure Cosmos DB

Możesz użyć parametrów połączenia, aby nawiązać połączenie z Azure Cosmos DB. Ta metoda obsługuje tylko interfejsy API SQL i tabele. Wykonaj następujące kroki, aby nawiązać połączenie z parametrami połączenia:

1. Znajdź **lokalne i dołączone** w lewym drzewie, kliknij prawym przyciskiem myszy pozycję **Cosmos DB konta**, a następnie wybierz pozycję **Połącz z Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Zrzut ekranu przedstawiający menu rozwijane po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Połącz z usługą Azure Cosmos D B.":::

2. W oknie **łączenie z Cosmos DB** :
   1. Wybierz interfejs API z menu rozwijanego.
   1. Wklej parametry połączenia w polu **Parametry połączenia** . Aby uzyskać informacje na temat pobierania podstawowych parametrów połączenia, zobacz [pobieranie parametrów połączenia](manage-with-powershell.md#list-keys).
   1. Wprowadź **etykietę konta**, a następnie wybierz pozycję **dalej** , aby sprawdzić podsumowanie.
   1. Wybierz pozycję **Połącz** , aby połączyć konto Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Zrzut ekranu przedstawiający okno łączenie z Cosmos D B z menu rozwijanego interfejs API, pole parametry połączenia i pole Etykieta konta.":::

> [!NOTE]
> Jeśli Eksplorator usługi Azure Storage pokazuje, że Azure Cosmos DB parametry połączenia mają nieprawidłowy format, upewnij się, że parametry połączenia mają średnik ( `;` ) na końcu. Przykładem prawidłowych parametrów połączenia Azure Cosmos DB będzie: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Użyj lokalnego emulatora, aby nawiązać połączenie z usługą Azure Cosmos DB

Wykonaj następujące kroki, aby nawiązać połączenie z Azure Cosmos DB z emulatorem. Ta metoda obsługuje tylko konta SQL.

1. Zainstaluj Cosmos DB emulator, a następnie otwórz go. Informacje na temat sposobu instalowania emulatora znajdują się w temacie [Cosmos DB emulator](./local-emulator.md).

1. Znajdź **lokalne i dołączone** w lewym drzewie, kliknij prawym przyciskiem myszy pozycję **Cosmos DB konta**, a następnie wybierz pozycję **Połącz z emulatorem Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy, z wyróżnioną pozycją Połącz z usługą Azure Cosmos D B.":::

1. W oknie **łączenie z Cosmos DB** :
   1. Wklej parametry połączenia w polu **Parametry połączenia** . Aby uzyskać informacje na temat pobierania podstawowych parametrów połączenia, zobacz [pobieranie parametrów połączenia](manage-with-powershell.md#list-keys).
   1. Wprowadź **etykietę konta**, a następnie wybierz pozycję **dalej** , aby sprawdzić podsumowanie.
   1. Wybierz pozycję **Połącz** , aby połączyć konto Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Zrzut ekranu przedstawiający okno łączenie z Cosmos D B z polem parametry połączenia i polem etykieta konta.":::

## <a name="azure-cosmos-db-resource-management"></a>Zarządzanie zasobami usługi Azure Cosmos DB

Aby zarządzać kontem Azure Cosmos DB, należy wykonać następujące operacje:

* Otwórz konto w Azure Portal.
* Dodaj zasób do listy szybki dostęp.
* Wyszukiwanie i odświeżanie zasobów.
* Tworzenie i usuwanie baz danych.
* Tworzenie i usuwanie kolekcji.
* Tworzenie, edytowanie, usuwanie i filtrowanie dokumentów.
* Zarządzaj procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika.

### <a name="quick-access-tasks"></a>Zadania szybkiego dostępu

Możesz kliknąć prawym przyciskiem myszy subskrypcję w okienku Eksploratora, aby wykonać wiele zadań szybkiego działania, na przykład:

* Kliknij prawym przyciskiem myszy konto Azure Cosmos DB lub bazę danych, a następnie wybierz polecenie **Otwórz w portalu** , aby zarządzać zasobem w przeglądarce na Azure Portal.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy, z wyróżnioną pozycją Otwórz w portalu.":::

* Kliknij prawym przyciskiem myszy konto Azure Cosmos DB, bazę danych lub kolekcję, a następnie wybierz polecenie **Dodaj do paska Szybki dostęp** , aby dodać je do menu szybkiego dostępu.

* Wybierz pozycję **Wyszukaj w tym miejscu** , aby włączyć wyszukiwanie za pomocą słowa kluczowego pod wybraną ścieżką.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Zrzut ekranu przedstawiający pole wyszukiwania wyróżnione.":::

### <a name="database-and-collection-management"></a>Zarządzanie bazą danych i kolekcją

#### <a name="create-a-database"></a>Tworzenie bazy danych

1. Kliknij prawym przyciskiem myszy konto Azure Cosmos DB, a następnie wybierz polecenie **Utwórz bazę danych**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Utwórz bazę danych.":::

1. Wprowadź nazwę bazy danych, a następnie naciśnij klawisz **Enter** , aby zakończyć.

#### <a name="delete-a-database"></a>Usuwanie bazy danych

1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Usuń bazę danych**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Usuń bazę danych.":::

1. W oknie podręcznym wybierz pozycję **tak** . Węzeł bazy danych zostanie usunięty, a konto usługi Azure Cosmos DB zostanie odświeżone automatycznie.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Zrzut ekranu okna potwierdzenia z wyróżnionym przyciskiem tak.":::

#### <a name="create-a-collection"></a>Tworzenie kolekcji

1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Utwórz kolekcję**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Utwórz kolekcję.":::

1. W oknie Tworzenie kolekcji wprowadź żądane informacje, takie jak **identyfikator kolekcji** i **pojemność magazynu** itd. Wybierz przycisk **OK**, aby zakończyć.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Zrzut ekranu przedstawiający okno tworzenia kolekcji, w którym znajduje się kolekcja i D przyciski i pojemność magazynu.":::

1. Wybierz pozycję **nieograniczone** , aby określić klucz partycji, a następnie wybierz przycisk **OK** , aby zakończyć.

   > [!NOTE]
   > Jeśli podczas tworzenia kolekcji jest używany klucz partycji, po zakończeniu tworzenia nie można zmienić wartości klucza partycji w kolekcji.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Zrzut ekranu przedstawiający okno Tworzenie kolekcji z nieograniczoną ilością wybraną dla pojemności magazynu i wyróżnioną ramką klucza partycji.":::

#### <a name="delete-a-collection"></a>Usuwanie kolekcji

- Kliknij prawym przyciskiem myszy kolekcję, wybierz pozycję **Usuń kolekcję**, a następnie wybierz pozycję **tak** w oknie podręcznym.

    Węzeł kolekcji zostanie usunięty, a baza danych zostanie odświeżona automatycznie.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Zrzut ekranu przedstawiający menu, które jest wyświetlane po kliknięciu prawym przyciskiem myszy z wyróżnioną pozycją Usuń kolekcję.":::

### <a name="document-management"></a>Zarządzanie dokumentami

#### <a name="create-and-modify-documents"></a>Tworzenie i modyfikowanie dokumentów

- Otwórz okno **dokumenty** w lewym okienku, wybierz pozycję **Nowy dokument**, Edytuj zawartość w okienku po prawej stronie, a następnie wybierz pozycję **Zapisz**.
- Możesz również zaktualizować istniejący dokument, a następnie wybrać pozycję **Zapisz**. Aby odrzucić zmiany, wybierz pozycję **Odrzuć**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Zrzut ekranu przedstawiający dokumenty wyróżnione w okienku po lewej stronie. W okienku po prawej stronie zostanie wyróżniony nowy dokument, Zapisz i Odrzuć.":::

#### <a name="delete-a-document"></a>Usuwanie dokumentu

* Wybierz przycisk **Usuń** , aby usunąć wybrany dokument.

#### <a name="query-for-documents"></a>Wykonywanie zapytań dotyczących dokumentów

* Aby edytować filtr dokumentu, wprowadź [zapytanie SQL](./sql-query-getting-started.md), a następnie wybierz pozycję **Zastosuj**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Zrzut ekranu przedstawiający okienko po prawej stronie, w którym są wyświetlane przyciski Filtruj i Zastosuj, numer IDENTYFIKACYJNy i pole zapytania.":::

### <a name="graph-management"></a>Zarządzanie programem Graph

#### <a name="create-and-modify-a-vertex"></a>Tworzenie i modyfikowanie wierzchołka

* Aby utworzyć nowy wierzchołek, Otwórz program **Graph** w okienku po lewej stronie, wybierz pozycję **nowy wierzchołek**, Edytuj zawartość, a następnie wybierz **przycisk OK**.
* Aby zmodyfikować istniejący wierzchołek, wybierz ikonę pióra w okienku po prawej stronie.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Zrzut ekranu przedstawiający wykres wybrany w okienku po lewej stronie i pokazywanie nowego wierzchołka oraz ikony pióra wyróżnionej w okienku po prawej stronie.":::

#### <a name="delete-a-graph"></a>Usuwanie wykresu

* Aby usunąć wierzchołek, wybierz ikonę kosza obok nazwy wierzchołka.

#### <a name="filter-for-graph"></a>Filtrowanie wykresów

* Aby edytować filtr wykresu, wprowadź [zapytanie Gremlin](gremlin-support.md), a następnie wybierz pozycję **Zastosuj filtr**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Zrzut ekranu przedstawiający wykres wybrany w okienku po lewej stronie i pokazujący Zastosuj filtr i pole zapytania wyróżnione w okienku po prawej stronie.":::

### <a name="table-management"></a>Zarządzanie tabelami

#### <a name="create-and-modify-a-table"></a>Tworzenie i modyfikowanie tabeli

* Aby utworzyć nową tabelę:
   1. W okienku po lewej stronie Otwórz pozycję **jednostki**, a następnie wybierz pozycję **Dodaj**.
   1. W oknie dialogowym **Dodawanie jednostki** Edytuj zawartość.
   1. Wybierz przycisk **Dodaj właściwość** , aby dodać właściwość.
   1. Wybierz pozycję **Wstaw**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Zrzut ekranu przedstawiający jednostki wyróżnione w okienku po lewej stronie i pokazywanie opcji Dodaj, Edytuj, Dodaj właściwość i Wstaw wyróżnione w okienku po prawej stronie.":::

* Aby zmodyfikować tabelę, wybierz pozycję **Edytuj**, zmodyfikuj zawartość, a następnie wybierz pozycję **Aktualizuj**.

   

#### <a name="import-and-export-table"></a>Importowanie i eksportowanie tabeli

* Aby zaimportować, wybierz przycisk **Importuj** , a następnie wybierz istniejącą tabelę.
* Aby wyeksportować, wybierz przycisk **Eksportuj** , a następnie wybierz miejsce docelowe.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Zrzut ekranu przedstawiający przyciski Importuj i Eksportuj wyróżnione w okienku po prawej stronie.":::

#### <a name="delete-entities"></a>Usuwanie jednostek

* Wybierz jednostki, a następnie wybierz przycisk **Usuń** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Zrzut ekranu przedstawiający przycisk Usuń wyróżniony w okienku po prawej stronie i okno podręczne potwierdzenia z wyróżnioną pozycją tak.":::

#### <a name="query-a-table"></a>Tworzenie zapytań względem tabeli

- Wybierz przycisk **zapytania** , wprowadź warunek zapytania, a następnie wybierz przycisk **wykonaj zapytanie** . Aby zamknąć okienko zapytania, wybierz przycisk **Zamknij zapytanie** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Zrzut ekranu przedstawiający okienko po prawej stronie z wyróżnionym przyciskiem wykonaj zapytanie i przyciskiem Zamknij zapytanie.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Zarządzanie procedurami składowanymi, wyzwalaczami i funkcjami definiowanymi przez użytkownika (UDF)

* Aby utworzyć procedurę przechowywaną:
  1. W lewym drzewie kliknij prawym przyciskiem myszy **procedury składowane**, a następnie wybierz polecenie **Utwórz procedurę składowaną**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Zrzut ekranu przedstawiający okienko po lewej stronie z menu wyświetlanym po kliknięciu prawym przyciskiem myszy z wyróżnioną procedurą składowaną Create.":::
  
  1. Wprowadź nazwę z lewej strony, w okienku po prawej stronie Wprowadź skrypty procedury składowanej, a następnie wybierz pozycję **Utwórz**.
  
* Aby edytować istniejącą procedurę składowaną, kliknij dwukrotnie procedurę, wprowadź aktualizację, a następnie wybierz pozycję **Aktualizuj** , aby zapisać. Możesz również wybrać pozycję **Odrzuć** , aby anulować zmianę.

* Operacje dotyczące **wyzwalaczy** i **UDF** są podobne do **procedur składowanych**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono rozwiązania typowych problemów występujących podczas korzystania z Azure Cosmos DB w programie Eksplorator usługi Storage.

### <a name="sign-in-issues"></a>Problemy z logowaniem

Najpierw należy ponownie uruchomić aplikację, aby sprawdzić, czy ten problem został rozwiązany. Jeśli problem będzie się powtarzał, kontynuuj rozwiązywanie problemów.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certyfikat z podpisem własnym w łańcuchu certyfikatów

Istnieje kilka powodów, dla których widzisz ten błąd, dwa Najczęstsze są następujące:

* Jesteś za *przezroczystym serwerem proxy*. Ktoś, podobnie jak dział IT, przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.

* Używasz oprogramowania, takiego jak oprogramowanie antywirusowe. Oprogramowanie wprowadza certyfikat TLS/SSL z podpisem własnym do odbieranych wiadomości HTTPS.

Gdy Eksplorator usługi Storage odnajdzie certyfikat z podpisem własnym, nie ma informacji o tym, czy odebrany komunikat HTTPS został naruszony. Jeśli masz kopię certyfikatu z podpisem własnym, możesz poinstruować Eksplorator usługi Storage, aby go zaufać. Jeśli nie masz pewności, kto wprowadza certyfikat, możesz wykonać następujące kroki, aby dowiedzieć się, jak to zrobić:

1. Zainstaluj OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): dowolne wersje oświetlenia są poprawne.
     - macOS i Linux: powinny być dołączone do systemu operacyjnego.

1. Uruchom OpenSSL:
    * Windows: Przejdź do katalogu instalacyjnego, a następnie **/bin/**, a następnie kliknij dwukrotnie przycisk **openssl.exe**.
    * Mac i Linux: wykonywanie **OpenSSL** z terminalu.
1. Wykonaj `s_client -showcerts -connect microsoft.com:443` .
1. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie masz pewności, co jest z podpisem własnym, poszukaj w dowolnym miejscu ("s:") i wystawcy ("i:").
1. Jeśli znajdziesz wszystkie certyfikaty z podpisem własnym, skopiuj i Wklej wszystko z i, w tym **-----Rozpocznij certyfikat-----** , aby **----------certyfikatów** do nowej. Plik CER dla każdej z nich.
1. Otwórz Eksplorator usługi Storage, a następnie przejdź do pozycji **Edytuj**  >  **Certyfikaty SSL**  >  **Importuj certyfikaty**. Użyj selektora plików, aby znaleźć, wybrać, a następnie otwórz. Utworzone pliki CER.

Jeśli nie znajdziesz żadnych certyfikatów z podpisem własnym, możesz wysłać opinię, aby uzyskać pomoc.

#### <a name="unable-to-retrieve-subscriptions"></a>Brak możliwości pobrania subskrypcji

Jeśli nie możesz pobrać subskrypcji po zalogowaniu, wypróbuj następujące sugestie:

* Sprawdź, czy Twoje konto ma dostęp do subskrypcji. W tym celu zaloguj się do [Azure Portal](https://portal.azure.com/).
* Upewnij się, że zalogowano się w prawidłowym środowisku:
  * [Azure](https://portal.azure.com/)
  * [Chiny platformy Azure](https://portal.azure.cn/)
  * [Azure (Niemcy)](https://portal.microsoftazure.de/)
  * [Wersja platformy Azure dla administracji USA](https://portal.azure.us/)
  * Środowisko niestandardowe/Azure Stack
* Jeśli jesteś za serwerem proxy, upewnij się, że serwer proxy Eksplorator usługi Storage jest prawidłowo skonfigurowany.
* Usuń konto, a następnie dodaj je ponownie.
* Usuń następujące pliki z katalogu macierzystego (na przykład: C:\Users\ContosoUser), a następnie ponownie Dodaj konto:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Naciśnij klawisz F12, aby otworzyć konsolę dewelopera. Po zalogowaniu się w konsoli programu Obejrzyj wszystkie komunikaty o błędach.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Zrzut ekranu przedstawiający konsolę narzędzia deweloperskie z wyróżnioną konsolą.":::

#### <a name="unable-to-see-the-authentication-page"></a>Nie można wyświetlić strony uwierzytelniania

Jeśli nie możesz wyświetlić strony uwierzytelniania:

* W zależności od szybkości połączenia może upłynąć trochę czasu na załadowanie strony logowania. Zaczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelnianie.
* Jeśli jesteś za serwerem proxy, upewnij się, że serwer proxy Eksplorator usługi Storage jest prawidłowo skonfigurowany.
* W konsoli narzędzia deweloperskie (F12) Obejrzyj odpowiedzi, aby zobaczyć, czy można znaleźć wskazówki dotyczące przyczyny niedziałania uwierzytelniania.

#### <a name="cant-remove-an-account"></a>Nie można usunąć konta

Jeśli nie możesz usunąć konta lub link ponownego uwierzytelnienia nie wykonuje żadnych czynności:

* Usuń następujące pliki z katalogu macierzystego, a następnie ponownie Dodaj konto:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Jeśli chcesz usunąć zasoby usługi Storage dołączone do systemu SAS, usuń:
  * Folder %AppData%/StorageExplorer dla systemu Windows
  * /Users/<your_name>/Library/Application Support SUpport/StorageExplorer for macOS
  * ~/.config/StorageExplorer dla systemu Linux
  
  > [!NOTE]
  > Po usunięciu tych plików **należy ponownie wprowadzić wszystkie poświadczenia**.

### <a name="httphttps-proxy-issue"></a>Problem z serwerem proxy HTTP/HTTPS

Nie można wyświetlić listy węzłów Azure Cosmos DB w lewym drzewie podczas konfigurowania serwera proxy HTTP/HTTPS w środowisku ASE. W Azure Portal jako obejście można użyć Eksploratora danych Azure Cosmos DB.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem z węzłem „Programowanie” w węźle „Lokalne i dołączone”

Po wybraniu węzła **programowanie** w węźle **lokalnym i podłączonym** w lewym drzewie nie ma odpowiedzi. Zachowanie jest oczekiwane.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Zrzut ekranu przedstawiający wybrany węzeł deweloperski.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Dołącz konto Azure Cosmos DB w przypadku błędu **lokalnego i dołączonego** węzła

Jeśli po dołączeniu konta Azure Cosmos DB w węźle **lokalnym i podłączonym** zostanie wyświetlony następujący błąd, upewnij się, że używasz prawidłowych parametrów połączenia.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Zrzut ekranu przedstawiający okno podręczne pobierania błędu zasobów podrzędnych, wskazujące getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Błąd rozwijania węzła usługi Azure Cosmos DB

Podczas próby rozszerzenia węzłów w lewym drzewie może zostać wyświetlony następujący błąd.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Zrzut ekranu przedstawiający okno podręczne pobierania błędu zasobów podrzędnych, wskazujący, że nie można nawiązać połączenia z tym kontem Cosmos D B.":::

Wypróbuj następujące sugestie:

* Sprawdź, czy konto Azure Cosmos DB jest w toku. Spróbuj ponownie, gdy konto zostanie utworzone pomyślnie.
* Jeśli konto znajduje się w obszarze **szybki dostęp** lub **lokalne i dołączone** węzły, sprawdź, czy konto zostało usunięte. Jeśli tak, należy ręcznie usunąć węzeł.

## <a name="next-steps"></a>Następne kroki

* Obejrzyj ten film wideo, aby dowiedzieć się, jak używać Azure Cosmos DB w Eksplorator usługi Azure Storage: [użyj Azure Cosmos DB w Eksplorator usługi Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Aby dowiedzieć się więcej o Eksploratorze usługi Storage i sposobach łączenia się z dodatkowymi usługami, zobacz [Wprowadzenie do Eksploratora usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).
