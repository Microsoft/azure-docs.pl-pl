---
title: Rejestrowanie skanowania źródła i konfiguracji bazy danych Oracle (wersja zapoznawcza) w usłudze Azure Purview
description: W tym artykule opisano, jak zarejestrować źródło Oracle w usłudze Azure Purview i skonfigurować skanowanie.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517066"
---
# <a name="register-and-scan-oracle-source-preview"></a>Rejestrowanie i skanowanie źródła Oracle (wersja zapoznawcza)

W tym artykule opisano, jak zarejestrować bazę danych Oracle w aplikacji Purview i skonfigurować skanowanie.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Źródło Oracle obsługuje pełne **skanowanie w** celu wyodrębnienia metadanych z bazy danych Oracle i pobrania **pochodzenia między** zasobami danych.

## <a name="prerequisites"></a>Wymagania wstępne

1.  Skonfiguruj najnowsze własne [środowisko Integration Runtime.](https://www.microsoft.com/download/details.aspx?id=39717)
    Aby uzyskać więcej informacji, zobacz [Tworzenie i konfigurowanie własnego środowiska Integration Runtime.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Upewnij się, że na maszynie wirtualnej, na której zainstalowano własne środowisko Integration Runtime, jest zainstalowany dysk [JDK 11.](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)

3.  Upewnij się pakiet redystrybucyjny programu Visual C++ że na maszynie środowiska Integration Runtime (Self-hosted) jest zainstalowana aktualizacja \" 2012 Update \" 4. Jeśli nie masz \' jeszcze zainstalowanego pliku [](https://www.microsoft.com/download/details.aspx?id=30679), pobierz go stąd.

4.  Musisz ręcznie pobrać sterownik Oracle JDBC [](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) z tego miejsca na maszynie wirtualnej, na której jest uruchomione własne środowisko Integration Runtime.

    > [!Note] 
    > Sterownik powinien być dostępny dla wszystkich kont na maszynie wirtualnej. Nie instaluj go na koncie użytkownika.

5.  Obsługiwane wersje bazy danych Oracle to od 6i do 19c.

6.  Uprawnienie użytkownika: wymagany jest dostęp tylko do odczytu do tabel systemowych. Użytkownik powinien mieć uprawnienia do tworzenia sesji oraz przypisanej roli SELECT \_ \_ CATALOG ROLE. Alternatywnie użytkownik może mieć przyznane uprawnienie SELECT dla każdej tabeli systemowej, z poziomu których ten łącznik wysyła zapytanie o metadane:
       > przyznaj użytkownikowi sesję \[ tworzenia \] ;\
        ujmij wszystkich \_ użytkowników na \[ użytkownika \] ;\
        u grant select on dba \_ objects to \[ user \] ;\
        przyznaj użytkownikowi wybór w \_ \_ komentarzach na karcie dba \[ \] ;\
        grant select on dba \_ external \_ locations to \[ user \] ;\
        grant select on dba \_ directories to \[ user \] ;\
        grant select on dba \_ mviews to \[ user \] ;\
        grant select on dba \_ clu \_ columns to user \[ \] ;\
        grant select on dba \_ tab columns to user \_ \[ \] ;\
        grant select on dba \_ col comments to user \_ \[ \] ;\
        grant select on dba \_ constraints to \[ user \] ;\
        grant select on dba \_ cons columns to user \_ \[ \] ;\
        grant select on dba \_ indexes to \[ user \] ;\
        grant select on dba \_ ind \_ columns to user \[ \] ;\
        grant select on dba \_ procedures to \[ user \] ;\
        grant select on dba \_ synonyms to \[ user \] ;\
        grant select on dba \_ views to \[ user \] ;\
        grant select on dba \_ source to \[ user \] ;\
        grant select on dba \_ triggers to \[ user \] ;\
        grant select on dba \_ arguments to \[ user \] ;\
        grant select on dba \_ sequences to \[ user \] ;\
        grant select on dba \_ dependencies to \[ user \] ;\
        przyznaj użytkownikowi wybieranie \_ \$ wystąpienia \[ \] wirtualnego;\
        przyznaj użytkownikowi polecenie select on v \_ \$ \[ database \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania dla skanowania

Jedynym obsługiwanym uwierzytelnianiem dla źródła Oracle jest **uwierzytelnianie podstawowe.**

## <a name="register-an-oracle-source"></a>Rejestrowanie źródła Oracle

Aby zarejestrować nowe źródło Oracle w wykazie danych, wykonaj następujące czynności:

1.  Przejdź do konta programu Purview.
2.  Wybierz **pozycję Źródła** na lewym pasku nawigacyjnym.
3.  Wybierz **pozycję Zarejestruj**
4.  Na stronie Rejestrowanie źródeł wybierz pozycję **Oracle**. Wybierz opcję **Kontynuuj**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="rejestrowanie źródeł" border="true":::

Na **ekranie Rejestrowanie źródeł (Oracle)** wykonaj następujące czynności:

1.  Wprowadź nazwę **źródła** danych, które będzie wyświetlane w wykazie.

2.  Wprowadź nazwę **hosta,** aby nawiązać połączenie ze źródłem Oracle. Może to być:
    - Nazwa hosta używana przez JDBC do nawiązywania połączenia z serwerem bazy danych. Na przykład MyDatabaseServer.com lub
    - Adres IP. Na przykład 192.169.1.2 lub
    - Jej w pełni kwalifikowane ciągi połączenia JDBC. Na przykład\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521)))(CONNECT \_ DATA=(NAZWA \_ USŁUGI=orcl)))

3.  Wprowadź numer **portu używany** przez połączenie JDBC do nawiązywania połączenia z serwerem bazy danych (domyślnie 1521 dla programu Oracle).

4.  Wprowadź nazwę **usługi Oracle używaną** przez JDBC do nawiązywania połączenia z serwerem bazy danych.

5.  Wybierz kolekcję lub utwórz nową (opcjonalnie)

6.  Zakończ, aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opcje rejestrowania źródeł" border="true":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1.  W Centrum zarządzania kliknij pozycję Środowiska Integration Runtime. Upewnij się, że jest ustawione własne środowisko Integration Runtime. Jeśli nie jest ono ustawione, skorzystaj z kroków wymienionych [tutaj,](./manage-integration-runtimes.md) aby utworzyć własne środowisko Integration Runtime.

2.  Przejdź do **źródła**.

3.  Wybierz zarejestrowane źródło Oracle.

4.  Wybierz **pozycję + Nowe skanowanie.**

5.  Podaj poniższe szczegóły:

    a.  **Nazwa:** nazwa skanowania

    b.  **Nawiązywanie połączenia za pośrednictwem środowiska Integration Runtime:** wybierz skonfigurowane własne środowisko Integration Runtime

    c.  Poświadczenie: wybierz poświadczenie, aby nawiązać połączenie ze źródłem danych. Upewnij się, że:
    - Wybierz pozycję Uwierzytelnianie podstawowe podczas tworzenia poświadczeń.        
    - Podaj nazwę użytkownika używaną przez interfejs JDBC do nawiązywania połączenia z serwerem bazy danych w polu danych wejściowych Nazwa użytkownika        
    - Przechowuj hasło użytkownika używane przez interfejs JDBC do nawiązywania połączenia z serwerem bazy danych w kluczu tajnym.

    d.  **Schemat:** Lista podzbiorów schematów do zaimportowania wyrażonych jako lista rozdzielona średnikami. Na przykład schema1; schema2. Wszystkie schematy użytkowników są importowane, jeśli ta lista jest pusta. Wszystkie schematy systemu (na przykład SysAdmin) i obiekty są domyślnie ignorowane. Gdy lista jest pusta, importowane są wszystkie dostępne schematy.
        Dopuszczalne wzorce nazw schematów używające składni wyrażeń SQL LIKE obejmują na przykład użycie %. A%; %B; %C%; D
       -   rozpoczynanie od A lub        
       -   kończy się na B lub        
       -   zawierają C lub        
       -   równe D

    Użycie znaków NOT i znaków specjalnych jest niedopuszczalne.

6.  **Lokalizacja sterownika:** określ ścieżkę do lokalizacji sterownika JDBC na maszynie wirtualnej, na której działa środowisko Integration Runtime samodzielnego hosta. Powinna to być ścieżka do prawidłowej lokalizacji folderu JAR.

7.  **Maksymalna dostępna pamięć:** maksymalna ilość pamięci (w GB) dostępna na maszynie wirtualnej klienta do użycia przez procesy skanowania. Zależy to od rozmiaru skanowanego źródła SAP S/4HANA.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="scan oracle" border="true":::

8.  Kliknij pozycję **Kontynuuj.**

9.  Wybierz wyzwalacz **skanowania.** Możesz skonfigurować harmonogram lub raz uruchomić skanowanie.

10.  Przejrzyj skanowanie, a następnie kliknij pozycję **Zapisz i uruchom**.

## <a name="viewing-your-scans-and-scan-runs"></a>Wyświetlanie skanowań i przebiegów skanowania

1. Przejdź do centrum zarządzania. Wybierz **pozycję Źródła danych** w sekcji Źródła i **skanowanie.**

2. Wybierz odpowiednie źródło danych. Zostanie wyświetlona lista istniejących skanowań dla tego źródła danych.

3. Wybierz skanowanie, którego wyniki chcesz wyświetlić.

4. Na tej stronie będą wyświetlane wszystkie poprzednie uruchomienia skanowania wraz z metrykami i stanem każdego uruchomienia skanowania. Będzie również wyświetlana, czy skanowanie zostało zaplanowane, czy ręczne, ilu zasobów ma zastosowane klasyfikacje, ile łącznej liczby odnalezionych zasobów, godzina rozpoczęcia i zakończenia skanowania oraz łączny czas trwania skanowania.

## <a name="manage-your-scans"></a>Zarządzanie skanowaniami

Aby zarządzać skanowaniem lub je usunąć, wykonaj następujące czynności:

1. Przejdź do centrum zarządzania. Wybierz **pozycję Źródła** danych w sekcji Źródła i **skanowanie,** a następnie wybierz żądane źródło danych.

2. Wybierz skanowanie, które chcesz zarządzać. Możesz edytować skanowanie, wybierając pozycję **Edytuj**.

3. Możesz usunąć skanowanie, wybierając pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie katalogu danych usługi Azure Purview](how-to-browse-catalog.md)
- [Wyszukaj w witrynie Azure Purview Data Catalog](how-to-search-catalog.md)