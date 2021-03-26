---
title: Rejestrowanie skanowania źródła Oracle i Instalatora (wersja zapoznawcza) w usłudze Azure kontrolą
description: W tym artykule opisano sposób rejestrowania źródła Oracle w usłudze Azure kontrolą i konfigurowania skanowania.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045505"
---
# <a name="register-and-scan-oracle-source-preview"></a>Rejestrowanie i skanowanie źródła Oracle (wersja zapoznawcza)

W tym artykule opisano sposób rejestrowania bazy danych Oracle w programie kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Źródło Oracle obsługuje **pełne skanowanie** w celu wyodrębnienia metadanych z bazy danych programu Oracle i  pobrania elementów zawartości z zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

1.  Skonfiguruj najnowsze [środowisko Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
    Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Upewnij się, że na maszynie wirtualnej jest zainstalowana funkcja [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) , w której jest zainstalowany własny środowisko Integration Runtime.

3.  Upewnij się \" , Visual C++ pakiet redystrybucyjny 2012 Update 4 \" jest zainstalowany na komputerze, na którym działa środowisko Integration Runtime. Jeśli \' nie masz jeszcze zainstalowanej usługi, pobierz ją z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Konieczne będzie ręczne pobranie sterownika Oracle JDBC z tego [miejsca](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) na maszynie wirtualnej, w której działa środowisko Integration Runtime.

    > [!Note] 
    > Sterownik powinien być dostępny dla wszystkich kont w maszynie wirtualnej. Nie należy instalować go na koncie użytkownika.

5.  Obsługiwane wersje bazy danych Oracle to 6i 19c.

6.  Uprawnienie użytkownika: aby zapewnić pomyślne skanowanie po raz pierwszy, wymagane jest pełne uprawnienie do typu administrator.

    W przypadku kolejnych operacji skanowania wymagany jest dostęp tylko do odczytu do tabel systemowych. Użytkownik powinien mieć uprawnienia do tworzenia sesji oraz roli Wybieranie \_ \_ przypisanej roli katalogu. Użytkownik może również mieć uprawnienie Wybieranie dla każdej oddzielnej tabeli systemowej, z której ten łącznik wysyła zapytania do metadanych:
       > Udziel użytkownikowi tworzenia sesji \[ \] ; \
        Przyznaj wybór wszystkim \_ użytkownikom \[ \] ; \
        Przyznaj wybór dla \_ obiektów dba do \[ użytkownika \] ; \
        przyznaj uprawnienie SELECT na \_ karcie administrator \_ do \[ użytkownika \] ; \
        Przyznaj wybranemu \_ \_ użytkownikowi lokalizacje zewnętrzne \[ \]
        Przyznaj wybór dla \_ katalogów dba do \[ użytkownika \] ; \
        Udziel opcji Select on dba \_ mviews to \[ User \] ; \
        Przyznaj wybrane \_ kolumny CLU dba \_ do \[ użytkownika \] ; \
        Udziel opcji wybierz na karcie administratorów baz wiedzy \_ \_ do \[ użytkownika \] ; \
        Przyznaj użytkownikowi uprawnienia do wyboru w \_ kolumnie administrator \_ \[ \]
        przyznaj uprawnienia SELECT na administratorach \_ dla \[ użytkownika \] ; \
        przyznaj uprawnienia SELECT \_ \_ do \[ użytkownika \] .
        Przyznaj wybór na \_ potrzeby indeksów administratorów do \[ użytkownika \] ; \
        Przyznaj wybór dla \_ \_ kolumn dba \[ \] :
        Udziel opcji SELECT na administratorach dla \_ \[ użytkownika \] ; \
        Przyznaj wybór \_ synonimów administratora dba \[ użytkownikowi \] ; \
        Przyznaj wybór dla \_ widoków dba do \[ użytkownika \] ; \
        Przyznaj wybranemu \_ użytkownikowi źródło danych administratora \[ \] : \
        Udziel opcji SELECT na \_ wyzwalaczach administratorów administratora \[ \] .
        Udziel opcji Select on on dba \_ dla \[ użytkownika \] ; \
        Udziel opcji SELECT na \_ sequences \[ użytkownika Administrator \] ; \
        Przyznaj wybranemu \_ użytkownikowi zależności od administratora \[ \] ; \
        Przyznaj wybór dla wystąpienia w wersji V \_ \$ \[ użytkownikowi \] ; \
        Przyznaj wybór dla bazy danych w wersji v \_ \$ do \[ użytkownika \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Jedynym obsługiwanym uwierzytelnianiem dla źródła Oracle jest **uwierzytelnianie podstawowe**.

## <a name="register-an-oracle-source"></a>Rejestrowanie źródła Oracle

Aby zarejestrować nowe źródło Oracle w wykazie danych, wykonaj następujące czynności:

1.  Przejdź do konta kontrolą.
2.  Wybierz pozycję **źródła** na lewym pasku nawigacyjnym.
3.  Wybierz pozycję **zarejestruj**
4.  W obszarze Rejestruj źródła wybierz pozycję **Oracle**. Wybierz opcję **Kontynuuj**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Rejestruj źródła" border="true":::

Na ekranie **Rejestr sources (Oracle)** wykonaj następujące czynności:

1.  Wprowadź **nazwę** , która zostanie wyświetlona na liście w wykazie.

2.  Wprowadź nazwę **hosta** , aby nawiązać połączenie ze źródłem Oracle. Może to być:
    - Nazwa hosta używana przez JDBC do nawiązywania połączenia z serwerem bazy danych. Na przykład MyDatabaseServer.com lub
    - Adres IP. Na przykład 192.169.1.2 lub
    - Jego w pełni kwalifikowane parametry połączenia JDBC. Na przykład \
        JDBC: Oracle: wąska: @ (DESCRIPTION = ( \_ równoważenie obciążenia = on) (Address = (protokół = TCP) (host = oracleserver1) (port = 1521)) (Address = (protokół = TCP) (host = oracleserver2) (port = 1521)) (Address = (protokół = TCP) (host = oracleserver3) (port = 1521)) (Połącz \_ dane = ( \_ nazwa usługi = ORCL)))

3.  Wprowadź **numer portu** używany przez JDBC do nawiązywania połączenia z serwerem bazy danych (domyślnie 1521 dla programu Oracle).

4.  Aby nawiązać połączenie z serwerem bazy danych, wprowadź **nazwę usługi Oracle** używaną przez JDBC.

5.  Wybierz kolekcję lub Utwórz nową (opcjonalnie)

6.  Zakończ, aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opcje rejestrowania źródeł" border="true":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1.  W centrum zarządzania kliknij pozycję Integration Runtimes. Upewnij się, że jest skonfigurowany własny środowisko Integration Runtime. Jeśli nie jest on skonfigurowany, wykonaj kroki opisane [tutaj](./manage-integration-runtimes.md) , aby utworzyć własne środowisko Integration Runtime.

2.  Przejdź do **źródeł**.

3.  Wybierz zarejestrowane źródło Oracle.

4.  Wybierz pozycję **+ Nowe skanowanie**.

5.  Podaj poniżej szczegóły:

    a.  **Name**: Nazwa skanowania

    b.  **Połącz za pośrednictwem środowiska Integration Runtime**: Wybierz skonfigurowane środowisko Integration Runtime (własne)

    c.  **Poświadczenie**: Wybierz poświadczenie, aby nawiązać połączenie ze źródłem danych. Upewnij się, że:
    - Podczas tworzenia poświadczenia wybierz pozycję Uwierzytelnianie podstawowe.        
    - Podaj nazwę użytkownika używaną przez JDBC do nawiązania połączenia z serwerem bazy danych w polu wejściowym nazwa użytkownika        
    - Przechowuj hasło użytkownika używane przez JDBC do nawiązywania połączenia z serwerem bazy danych w kluczu tajnym.

    d.  **Schemat**: Lista podzestawu schematów do zaimportowania wyrażona jako lista rozdzielonych średnikami. Na przykład Schema1; schema2. Wszystkie schematy użytkownika są importowane, jeśli lista jest pusta. Wszystkie schematy systemowe (na przykład sysadmin) i obiekty są domyślnie ignorowane. Gdy lista jest pusta, wszystkie dostępne schematy są importowane.
        Dozwolone wzorce nazw schematu przy użyciu składni wyrażeń, takich jak wyrażenia SQL, obejmują na przykład użycie%. %; B % C%; Wykres
       -   Zacznij od lub        
       -   kończy się na B lub        
       -   zawiera C lub        
       -   równe D

    Użycie znaków NOT i Special nie jest akceptowalne.

6.  **Lokalizacja sterownika**: Określ ścieżkę do lokalizacji sterownika JDBC na maszynie wirtualnej, w której działa środowisko Integration Runtime. Powinna to być ścieżka do prawidłowej lokalizacji folderu JAR.

7.  **Maksymalna dostępna pamięć**: Maksymalna ilość pamięci (w GB) dostępna na maszynie wirtualnej klienta do użycia przez procesy skanowania. Jest to zależne od rozmiaru źródła SAP S/4HANA, które ma zostać przeskanowane.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Skanuj oprogramowanie Oracle" border="true":::

8.  Kliknij przycisk **Kontynuuj**.

9.  Wybierz **wyzwalacz skanowania**. Można skonfigurować harmonogram lub przeprowadzić skanowanie raz.

10.  Przejrzyj skanowanie i kliknij pozycję **Zapisz i uruchom**.

## <a name="viewing-your-scans-and-scan-runs"></a>Wyświetlanie skanów i uruchomień skanowania

1. Przejdź do centrum zarządzania. Wybierz pozycję **źródła danych** w sekcji **źródła i Skanuj** .

2. Wybierz żądane źródło danych. Zostanie wyświetlona lista istniejących skanów w tym źródle danych.

3. Wybierz skanowanie, którego wyniki chcesz wyświetlić.

4. Na tej stronie zostaną wyświetlone wszystkie poprzednie uruchomienia skanowania wraz z metrykami i Stanami dla każdego przebiegu skanowania. Zostanie również wyświetlona, czy Twoje skanowanie zostało zaplanowane, czy ręczne, ile zasobów ma zastosowane klasyfikacje, ile łącznych zasobów zostało wykrytych, czasu rozpoczęcia i zakończenia skanowania oraz łącznego czasu trwania skanowania.

## <a name="manage-your-scans"></a>Zarządzanie skanami

Aby zarządzać skanowaniem lub usunąć je, wykonaj następujące czynności:

1. Przejdź do centrum zarządzania. Wybierz pozycję **źródła danych** w sekcji **źródła i skanowanie** , a następnie wybierz odpowiednie źródło danych.

2. Wybierz skanowanie, którymi chcesz zarządzać. Skanowanie można edytować, wybierając pozycję **Edytuj**.

3. Możesz usunąć skanowanie, wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie wykazu danych usługi Azure kontrolą](how-to-browse-catalog.md)
- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)