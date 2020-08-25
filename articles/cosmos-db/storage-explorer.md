---
title: Zarządzanie zasobami Azure Cosmos DB przy użyciu Eksplorator usługi Azure Storage
description: Dowiedz się, jak nawiązać połączenie z usługą Azure Cosmos DB i zarządzać swoimi zasobami przy użyciu Eksplorator usługi Azure Storage.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: b892e4c5078b50bb865a715ddf12aebc1eb05f57
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799115"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Praca z danymi za pomocą Eksploratora usługi Azure Storage

Korzystanie z usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage pozwala użytkownikom zarządzać jednostkami usługi Azure Cosmos DB, wykonywać operacje na danych oraz aktualizować procedury składowane i wyzwalacze, a także inne jednostki platformy Azure, takie jak obiekty blob i kolejki usługi Storage. Za pomocą jednego narzędzia można teraz centralnie zarządzać różnymi jednostkami platformy Azure. W tej chwili Eksplorator usługi Azure Storage obsługuje konta Cosmos skonfigurowane dla interfejsów API SQL, MongoDB, Graph i Table.


## <a name="prerequisites"></a>Wymagania wstępne

Konto Cosmos z interfejsem API SQL lub interfejsem API Azure Cosmos DB dla MongoDB. Jeśli nie masz konta, możesz je utworzyć w witrynie Azure Portal, wykonując instrukcje podane w temacie [Azure Cosmos DB: Tworzenie aplikacji internetowej interfejsu API SQL za pomocą platformy .NET i witryny Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalacja

Zainstaluj najnowszą wersję Eksploratora usługi Azure Storage. Program [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest dostępny w wersji dla systemu Windows i Linux oraz dla komputerów MAC.

## <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

1. Po zainstalowaniu **Eksplorator usługi Azure Storage**wybierz ikonę **wtyczki** po lewej stronie, jak pokazano na poniższej ilustracji:

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Wybierz ikonę wtyczki, aby nawiązać połączenie":::

2. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie wybierz pozycję **Zaloguj się**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Nawiązywanie połączenia z wymaganą subskrypcją platformy Azure":::

2. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Zaloguj**, a następnie wprowadź swoje poświadczenia platformy Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Zaloguj się do subskrypcji platformy Azure":::

3. Wybierz subskrypcję z listy, a następnie wybierz pozycję **Zastosuj**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Wybierz identyfikator subskrypcji z listy do odfiltrowania":::

    Zawartość okienka Eksploratora zostanie zaktualizowana i pojawią się konta w wybranej subskrypcji.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Wybierz konto Azure Cosmos DB z listy dostępnych":::

    Pomyślnie nawiązano połączenie z **kontem usługi Cosmos DB** w ramach subskrypcji platformy Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Łączenie z usługą Azure Cosmos DB za pomocą parametrów połączenia

Alternatywna metoda połączenia się z usługą Azure Cosmos DB polega na użyciu parametrów połączenia. Aby nawiązać połączenie przy użyciu parametrów połączenia, wykonaj następujące kroki.

1. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Cosmos DB**, a następnie wybierz pozycję **Połącz z usługą Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Łączenie z usługą Azure Cosmos DB za pomocą parametrów połączenia":::

2. Aktualnie obsługiwana jest baza danych SQL i interfejs API tabel. Wybierz pozycję Interfejs API, wklej **ciąg połączenia**, wprowadź **etykietę konta**, wybierz pozycję **dalej** , aby sprawdzić podsumowanie, a następnie wybierz pozycję **Połącz** Azure Cosmos DB konto. Aby uzyskać informacje na temat pobierania podstawowych parametrów połączenia, zobacz [pobieranie parametrów połączenia](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Wprowadź parametry połączenia":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Nawiązywanie połączenia z usługą Azure Cosmos DB za pomocą emulatora lokalnego

Wykonaj następujące kroki, aby nawiązać połączenie z usługą Azure Cosmos DB za pomocą emulatora. Obecnie obsługiwane jest tylko konto bazy danych SQL.

1. Zainstaluj emulator i uruchom go. Aby uzyskać informacje dotyczące instalowania emulatora, zobacz [Emulator usługi Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Znajdź pozycję **Lokalne i dołączone** w drzewie po lewej stronie, kliknij prawym przyciskiem myszy pozycję **Konta usługi Cosmos DB**, a następnie wybierz pozycję **Połącz z emulatorem usługi Cosmos DB...**

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Nawiązywanie połączenia z Azure Cosmos DB z emulatora":::

3. Aktualnie obsługiwany jest tylko interfejs API SQL. Wklej **ciąg połączenia**, wprowadź **etykietę konta**, wybierz pozycję **dalej** , aby sprawdzić podsumowanie, a następnie wybierz pozycję **Połącz** , aby nawiązać Azure Cosmos DB konto. Aby uzyskać informacje na temat pobierania podstawowych parametrów połączenia, zobacz [pobieranie parametrów połączenia](manage-with-powershell.md#list-keys).

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Połącz z Cosmos DB z okna dialogowego emulatora":::


## <a name="azure-cosmos-db-resource-management"></a>Zarządzanie zasobami usługi Azure Cosmos DB

W ramach zarządzania kontem usługi Azure Cosmos DB można wykonywać następujące operacje:
* Otwieranie konta w witrynie Azure Portal
* Dodawanie zasobu do listy szybkiego dostępu
* Wyszukiwanie i odświeżanie zasobów
* Tworzenie i usuwanie baz danych
* Tworzenie i usuwanie kolekcji
* Tworzenie, edytowanie, usuwanie i filtrowanie dokumentów
* Zarządzanie procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika

### <a name="quick-access-tasks"></a>Zadania szybkiego dostępu

Po kliknięciu prawym przyciskiem myszy subskrypcji w okienku Eksploratora pojawią się polecenia, które pozwalają wykonywać wiele szybkich działań:

* Po kliknięciu prawym przyciskiem myszy konta lub bazy danych usługi Azure Cosmos DB można wybrać polecenie **Otwórz w portalu**, aby zarządzać zasobem w przeglądarce w witrynie Azure Portal.

     :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Otwórz w portalu":::

* Do paska **Szybki dostęp** można także dodać konto, bazę danych lub kolekcję usługi Azure Cosmos DB.
* Opcja **Wyszukaj od tego miejsca** umożliwia wyszukiwanie słów kluczowych w obrębie wybranej ścieżki.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Wyszukaj od tego miejsca":::

### <a name="database-and-collection-management"></a>Zarządzanie bazą danych i kolekcją

#### <a name="create-a-database"></a>Tworzenie bazy danych

-   Kliknij prawym przyciskiem myszy konto usługi Azure Cosmos DB, wybierz polecenie **Utwórz bazę danych**, wprowadź nazwę bazy danych, a następnie naciśnij klawisz **Enter**, aby zakończyć procedurę.

    :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Tworzenie bazy danych na koncie usługi Azure Cosmos":::

#### <a name="delete-a-database"></a>Usuwanie bazy danych

- Kliknij prawym przyciskiem myszy bazę danych, wybierz pozycję **Usuń bazę danych**, a następnie wybierz pozycję **tak** w oknie podręcznym. Węzeł bazy danych zostanie usunięty, a konto usługi Azure Cosmos DB zostanie odświeżone automatycznie.

    :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Usuwanie pierwszej bazy danych":::

    :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Usuń pozostałe bazy danych":::

#### <a name="create-a-collection"></a>Tworzenie kolekcji

1. Kliknij prawym przyciskiem myszy bazę danych, wybierz polecenie **Utwórz kolekcję**, a następnie podaj następujące informacje, takie jak **identyfikator kolekcji**, **pojemność magazynu**itp. Kliknij przycisk **OK** , aby zakończyć.

    :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Tworzenie pierwszej kolekcji w bazie danych":::

    :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Tworzenie drugiej kolekcji w bazie danych":::

2. Wybierz pozycję **nieograniczone** , aby określić klucz partycji, a następnie wybierz przycisk **OK** , aby zakończyć.

    Jeśli podczas tworzenia kolekcji jest używany klucz partycji, to po jej utworzeniu nie można zmienić wartości klucza partycji.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Konfigurowanie klucza partycji":::

#### <a name="delete-a-collection"></a>Usuwanie kolekcji

- Kliknij prawym przyciskiem myszy kolekcję, wybierz pozycję **Usuń kolekcję**, a następnie wybierz pozycję **tak** w oknie podręcznym.

    Węzeł kolekcji zostanie usunięty, a baza danych zostanie odświeżona automatycznie.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Usuń jedną z kolekcji":::

### <a name="document-management"></a>Zarządzanie dokumentami

#### <a name="create-and-modify-documents"></a>Tworzenie i modyfikowanie dokumentów

- Aby utworzyć nowy dokument, Otwórz **dokumenty** w lewym oknie, wybierz pozycję **Nowy dokument**, Edytuj zawartość w prawym okienku, a następnie wybierz pozycję **Zapisz**. Możesz również zaktualizować istniejący dokument, a następnie wybrać pozycję **Zapisz**. Aby odrzucić zmiany, kliknij przycisk **Odrzuć**.

    :::image type="content" source="./media/storage-explorer/document.png" alt-text="Utwórz nowy dokument":::

#### <a name="delete-a-document"></a>Usuwanie dokumentu

- Aby usunąć wybrany dokument, kliknij przycisk **Usuń**.

#### <a name="query-for-documents"></a>Wykonywanie zapytań dotyczących dokumentów

- Edytuj filtr dokumentu, wprowadzając [zapytanie SQL](how-to-sql-query.md) , a następnie wybierz pozycję **Zastosuj**.

    :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Zapytanie o określone dokumenty":::

### <a name="graph-management"></a>Zarządzanie programem Graph

#### <a name="create-and-modify-vertex"></a>Tworzenie i modyfikowanie wierzchołków

1. Aby utworzyć nowy wierzchołek, Otwórz program **Graph** w okienku po lewej stronie, wybierz pozycję **nowy wierzchołek**, Edytuj zawartość, a następnie wybierz **przycisk OK**.
2. Aby zmodyfikować istniejący wierzchołek, wybierz ikonę pióra w okienku po prawej stronie.

    :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Modyfikowanie wierzchołka wykresu":::

#### <a name="delete-a-graph"></a>Usuwanie wykresu

- Aby usunąć wierzchołek, wybierz ikonę kosza obok nazwy wierzchołka.

#### <a name="filter-for-graph"></a>Filtrowanie wykresów

- Edytuj filtr wykresu, wprowadzając [zapytanie Gremlin](gremlin-support.md) , a następnie wybierz pozycję **Zastosuj filtr**.

    :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Uruchamianie zapytania grafu":::

### <a name="table-management"></a>Zarządzanie tabelami

#### <a name="create-and-modify-table"></a>Tworzenie i modyfikowanie tabeli

1. Aby utworzyć nową tabelę, Otwórz **jednostki** w lewym oknie, wybierz pozycję **Dodaj**, Edytuj zawartość w oknie dialogowym **Dodawanie jednostki** , Dodaj właściwość, klikając przycisk **Dodaj właściwość**, a następnie wybierz pozycję **Wstaw**.
2. Aby zmodyfikować tabelę, wybierz pozycję **Edytuj**, zmodyfikuj zawartość, a następnie wybierz pozycję **Aktualizuj**.

    :::image type="content" source="./media/storage-explorer/table.png" alt-text="Tworzenie i modyfikowanie tabeli":::

#### <a name="import-and-export-table"></a>Importowanie i eksportowanie tabeli

1. Aby zaimportować, wybierz przycisk **Importuj** , a następnie wybierz istniejącą tabelę.
2. Aby wyeksportować, wybierz przycisk **Eksportuj** i wybierz miejsce docelowe.

    :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Importowanie lub eksportowanie tabeli":::

#### <a name="delete-entities"></a>Usuwanie jednostek

- Wybierz jednostki i wybierz przycisk **Usuń**.

    :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Usuwanie tabeli":::

#### <a name="query-table"></a>Wykonywanie zapytań dotyczących tabeli

- Kliknij przycisk **zapytania** , warunek zapytania wejściowego, a następnie wybierz przycisk **wykonaj zapytanie** . Zamknij okienko Zapytanie, klikając **Zamknij zapytanie**.

    :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Zapytanie danych z tabeli":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Zarządzanie procedurami składowanymi, wyzwalaczami i funkcjami definiowanymi przez użytkownika (UDF)

* Aby utworzyć procedurę przechowywaną, w lewym drzewie kliknij prawym przyciskiem myszy **procedurę przechowywaną**, wybierz polecenie **Utwórz procedurę składowaną**, wprowadź nazwę po lewej stronie, wpisz skrypty procedury składowanej w prawym oknie, a następnie wybierz pozycję **Utwórz**.
* Możesz również edytować istniejące procedury składowane, klikając dwukrotnie, wprowadzając aktualizację, a następnie klikając przycisk **Aktualizuj** , aby zapisać, lub wybrać pozycję **Odrzuć** , aby anulować zmianę.

    :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Tworzenie procedur składowanych i zarządzanie nimi":::
* Operacje dotyczące **wyzwalaczy** i **funkcji definiowanych przez użytkownika** są podobne do **procedur składowanych**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[Azure Cosmos DB w Eksploratorze usługi Azure Storage](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) jest aplikacją autonomiczną, która umożliwia nawiązanie połączenia z kontami usługi Azure Cosmos DB hostowanymi na platformie Azure i suwerennych chmurach w systemie Windows, macOS lub Linux. Umożliwia ona zarządzanie jednostkami usługi Azure Cosmos DB, wykonywanie operacji na danych oraz aktualizowanie procedur składowanych i wyzwalaczy, a także innych jednostek platformy Azure, takich jak obiekty blob i kolejki usługi Storage.

Są to rozwiązania typowych problemów występujących w ramach usługi Azure Cosmos DB w Eksploratorze usługi Storage.

### <a name="sign-in-issues"></a>Problemy z logowaniem

Przed kontynuacją spróbuj ponownie uruchomić aplikację i zobacz, czy problemy mogą zostać usunięte.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certyfikat z podpisem własnym w łańcuchu certyfikatów

Istnieje kilka przyczyn wyświetlenia tego błędu, przy czym dwoma najbardziej typowymi są:

+ Jesteś za *przezroczystym serwerem proxy*, co oznacza, że ktoś (na przykład dział IT) przechwytuje ruch https, odszyfrowuje go, a następnie szyfruje przy użyciu certyfikatu z podpisem własnym.

+ Używasz oprogramowania, takiego jak oprogramowanie antywirusowe, które wprowadza certyfikat TLS/SSL z podpisem własnym do odbieranych wiadomości HTTPS.

Gdy Eksplorator usługi Storage napotka jeden z tych „certyfikatów z podpisem własnym”, nie będzie już wiedział, czy odbierany komunikat HTTPS został naruszony. Jeśli jednak masz kopię certyfikatu z podpisem własnym, możesz poinformować Eksploratora usługi Storage, aby mu zaufał. Jeśli nie masz pewności, kto wprowadza certyfikat, możesz spróbować go znaleźć samodzielnie, wykonując następujące czynności:

1. Zainstaluj OpenSSL
     - [System Windows](https://slproweb.com/products/Win32OpenSSL.html) (dowolna z wersji uproszczonych jest OK)
     - Komputery Mac i system Linux: powinien być dołączony do systemu operacyjnego
2. Uruchom OpenSSL
    - System Windows: przejdź do katalogu instalacyjnego, a następnie **/bin/**, po czym kliknij dwukrotnie plik **openssl.exe**.
    - Komputery Mac i system Linux: wykonaj polecenie **openssl** z terminala
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie wiesz, które z nich są z podpisem własnym, wówczas poszukaj pozycji, gdzie podmiot („s:”) i wystawca („i:”) są identyczni.
5.  Po znalezieniu jakichkolwiek certyfikatów z podpisem własnym skopiuj i wklej wszystko, poczynając od **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---**, do nowego pliku cer dla każdego z nich.
6.  Otwórz Eksplorator usługi Storage a następnie przejdź do pozycji **Edytuj**  >  **Certyfikaty SSL**  >  **Importuj certyfikaty**. Za pomocą selektora plików znajdź, wybierz i otwórz utworzony plik cer.

Jeśli nie możesz odnaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, możesz wysłać opinię, aby uzyskać dalszą pomoc.

#### <a name="unable-to-retrieve-subscriptions"></a>Brak możliwości pobrania subskrypcji

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu:

- Sprawdź, czy Twoje konto ma dostęp do subskrypcji, logując się do [Azure Portal](https://portal.azure.com/)
- Upewnij się, że podczas logowania używasz poprawnego środowiska ([Azure](https://portal.azure.com/), [Azure — Chiny](https://portal.azure.cn/), [Azure — Niemcy](https://portal.microsoftazure.de/), [Azure — instytucje rządowe USA](https://portal.azure.us/) lub niestandardowe środowisko/usługa Azure Stack)
- Jeśli znajdujesz się za serwerem proxy, upewnij się, że masz poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage
- Spróbuj usunąć i ponownie dodać konto
- Spróbuj usunąć następujące pliki z katalogu macierzystego (takiego jak: C:\Users\ContosoUser), a następnie ponownie dodać konto:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Podczas logowania obejrzyj wszystkie komunikaty o błędach na konsoli narzędzi dla deweloperów (f12)

:::image type="content" source="./media/storage-explorer/console.png" alt-text="Sprawdź wszystkie błędy w konsoli narzędzia deweloperskie":::

#### <a name="unable-to-see-the-authentication-page"></a>Nie można wyświetlić strony uwierzytelniania

Jeśli nie możesz wyświetlić strony uwierzytelniania:

- W zależności od prędkości połączenia załadowanie strony logowania może potrwać trochę czasu, więc poczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelniania
- Jeśli znajdujesz się za serwerem proxy, upewnij się, że masz poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage
- Wywołaj konsolę dla deweloperów, naciskając klawisz F12. Obejrzyj odpowiedzi z konsoli dla deweloperów i zobacz, czy możesz znaleźć jakieś wskazówki dotyczące tego, dlaczego uwierzytelnianie nie działa

#### <a name="cannot-remove-account"></a>Nie można usunąć konta

Jeśli nie możesz usunąć konta lub jeśli link do ponownego uwierzytelniania nie działa

- Spróbuj usunąć następujące pliki ze swojego katalogu macierzystego, a następnie ponownie dodaj konto:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Jeśli chcesz usunąć zasoby usługi Storage dołączone do systemu SAS, usuń:
  - Folder %AppData%/StorageExplorer dla systemu Windows
  - /Users/<your_name>/Library/Application Support SUpport/StorageExplorer for Mac
  - ~/.config/StorageExplorer dla systemu Linux
  - Po usunięciu tych plików **trzeba będzie ponownie wprowadzić wszystkie poświadczenia**


### <a name="httphttps-proxy-issue"></a>Problem z serwerem proxy HTTP/HTTPS

Podczas konfigurowania serwera proxy HTTP/HTTPS w środowisku ASE nie możesz wyświetlać listy węzłów usługi Azure Cosmos DB w drzewie po lewej stronie. Jako obejścia w tej chwili możesz użyć eksploratora danych usługi Azure Cosmos DB w witrynie Azure Portal.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem z węzłem „Programowanie” w węźle „Lokalne i dołączone”

Po wybraniu węzła "Programowanie" w węźle "lokalne i dołączone" w lewym drzewie nie ma odpowiedzi.  Zachowanie jest oczekiwane. Emulator lokalny usługi Azure Cosmos DB będzie obsługiwany w następnej wersji.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Węzeł projektowania":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Błąd dołączania konta usługi Azure Cosmos DB w węźle „Lokalne i dołączone”

Jeśli poniższy błąd zostanie wyświetlony po dołączeniu konta usługi Azure Cosmos DB w węźle „Lokalne i dołączone”, sprawdź, czy używasz właściwych parametrów połączenia.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Błąd dołączania usługi Azure Cosmos DB w węźle Lokalne i dołączone":::

### <a name="expand-azure-cosmos-db-node-error"></a>Błąd rozwijania węzła usługi Azure Cosmos DB

Podczas próby rozwinięcia węzłów drzewa z lewej strony możesz zobaczyć poniższy błąd.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Błąd rozwijania węzła usługi Azure Cosmos DB":::

Spróbuj wykonać poniższe sugestie:

- Sprawdź, czy konto usługi Azure Cosmos DB nie jest aktualnie na etapie aprowizacji, a następnie ponów próbę po pomyślnym utworzeniu konta.
- Jeśli konto znajduje się w węźle „Szybki dostęp” lub węzłach „Lokalne i dołączone”, sprawdź, czy konto zostało usunięte. Jeśli tak, należy ręcznie usunąć węzeł.

## <a name="next-steps"></a>Następne kroki

* Obejrzyj ten film wideo, aby dowiedzieć się, jak używać usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage: [Use Azure Cosmos DB in Azure Storage Explorer (Używanie usługi Azure Cosmos DB w Eksploratorze usługi Azure Storage)](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Aby dowiedzieć się więcej o Eksploratorze usługi Storage i sposobach łączenia się z dodatkowymi usługami, zobacz [Wprowadzenie do Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
