---
title: Zarejestruj źródło programu Teradata i ustawienia skanowania (wersja zapoznawcza)
description: W tym artykule opisano sposób rejestrowania źródła programu Teradata w usłudze Azure kontrolą i konfigurowania skanowania.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841933"
---
# <a name="register-and-scan-teradata-source-preview"></a>Zarejestruj i Skanuj źródło programu Teradata (wersja zapoznawcza)

W tym artykule opisano sposób rejestrowania źródła programu Teradata w programie kontrolą i konfigurowania skanowania.

> [!IMPORTANT]
> To źródło danych jest obecnie w wersji zapoznawczej. Możesz ją wypróbować i przekazać nam swoją opinię.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Źródło programu Teradata obsługuje **pełne skanowanie** w celu wyodrębnienia metadanych z bazy danych programu Teradata i pobrania elementów zależnych **od zasobów** danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Łącznik obsługuje magazyn danych znajdujący się tylko w sieci lokalnej, w sieci wirtualnej platformy Azure lub w wirtualnej chmurze prywatnej Amazon. W związku z tym należy skonfigurować [własne środowisko Integration Runtime](manage-integration-runtimes.md) , aby nawiązać z nim połączenie.

- Upewnij się, że Java Runtime Environment (JRE) jest zainstalowany na maszynie wirtualnej, na której jest zainstalowany własny środowisko Integration Runtime.
 
- Upewnij się, że na komputerze, na którym jest zainstalowany własny środowisko Integration Runtime, zainstalowano "Visual C++ pakiet redystrybucyjny 2012 Update 4". Jeśli jeszcze tego nie zainstalowano, Pobierz go z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=30679).

- Konieczne będzie ręczne zainstalowanie sterownika o nazwie JDBC sterownika programu Teradata na lokalnej maszynie wirtualnej. Plik JAR można pobrać z [witryny sieci Web programu Teradata](https://downloads.teradata.com/).

    > [!Note] 
    > Sterownik powinien być dostępny dla wszystkich kont w maszynie wirtualnej. Nie należy instalować programu na koncie użytkownika.

- Obsługiwane wersje bazy danych programu Teradata to **12. x do 16. x**. Upewnij się, że masz dostęp do odczytu do skanowanego źródła programu Teradata.

### <a name="feature-flag"></a>Flaga funkcji

Rejestracja i skanowanie źródła programu Teradata jest dostępne za flagą funkcji. Dołącz następujący adres URL: *? feature. ext. DataSource =% 7b "Teradata": "true"% 7D* 

Na przykład pełny adres URL [ https://web.purview.azure.com/?feature.ext.datasource=%7b "Metadata": "true"% 7D](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania
Jedynym sposobem, aby można było skonfigurować uwierzytelnianie dla źródła programu Teradata, jest **podstawowe uwierzytelnianie bazy danych**

## <a name="register-a-teradata-source"></a>Rejestrowanie źródła programu Teradata

Aby zarejestrować nowe źródło programu Teradata w wykazie danych, wykonaj następujące czynności:

1. Przejdź do konta kontrolą
2. Wybierz **źródła** na lewym pasku nawigacyjnym
3. Wybierz pozycję **zarejestruj**
4. W obszarze **Rejestruj źródła** wybierz pozycję **Teradata**
5. Wybierz przycisk **Kontynuuj**

Na ekranie **Rejestr sources (Teradata)** wykonaj następujące czynności:

1. Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.
2. Wprowadź nazwę **hosta** , aby nawiązać połączenie ze źródłem programu Teradata. Może to być również adres IP lub w pełni kwalifikowane parametry połączenia z serwerem.
3. Wybierz kolekcję lub Utwórz nową (opcjonalnie)
4. **Zakończ** , aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="opcje rejestrowania źródeł" border="true":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:
1. W centrum zarządzania kliknij pozycję *Integration Runtimes*. Upewnij się, że jest skonfigurowany własny środowisko Integration Runtime. Jeśli nie jest on skonfigurowany, należy wykonać kroki wymienione [tutaj](manage-integration-runtimes.md) , aby utworzyć własne środowisko Integration Runtime do skanowania na maszynie lokalnej lub wirtualnej platformy Azure, która ma dostęp do sieci lokalnej.

2. Dalej, przejdź do **źródeł**

3. Wybierz zarejestrowane źródło programu Teradata.

4. Wybierz pozycję + Nowe skanowanie
 
5. Podaj poniżej szczegóły:

    - Name: Nazwa skanowania

    - Połącz za pośrednictwem środowiska Integration Runtime: Wybierz skonfigurowane środowisko Integration Runtime (własne)

    - Metoda uwierzytelniania: uwierzytelnianie bazy danych jest jedyną opcją obsługiwaną przez teraz. To ustawienie zostanie domyślnie wybrane

    - Nazwa użytkownika: Nazwa użytkownika służąca do nawiązywania połączenia z serwerem bazy danych. Ta nazwa użytkownika powinna mieć dostęp do odczytu do serwera

    - Hasło: hasło użytkownika używane do nawiązywania połączenia z serwerem bazy danych

    - Schemat: Lista podzestawu schematów do zaimportowania wyrażona jako lista rozdzielonych średnikami. np. Schema1; schema2. Wszystkie schematy użytkownika są importowane, jeśli lista jest pusta. Wszystkie schematy systemowe (na przykład sysadmin) i obiekty są domyślnie ignorowane.

        Dozwolone wzorce nazw schematu przy użyciu składni wyrażeń, takich jak wyrażenia SQL, obejmują użycie%, np .%; B % C%; Wykres
        - Zacznij od lub    
        - kończy się na B lub    
        - zawiera C lub    
        - równe D

        Użycie znaków NOT i Special nie jest akceptowalne

    - Lokalizacja sterownika: pełna ścieżka do lokalizacji sterownika programu Teradata na maszynie wirtualnej klienta. Nazwa sterownika programu Teradata JDBC musi być: com. Teradata. JDBC. TeraDriver

    - Maksymalna dostępna pamięć: Maksymalna ilość pamięci (w GB) dostępna na maszynie wirtualnej klienta do użycia przez procesy skanowania. Jest to zależne od rozmiaru źródła programu Teradata, które ma zostać przeskanowane.

    > [!Note] 
    > Jako regułę przewijania należy udostępnić 2 GB pamięci dla każdej tabeli 1000

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Skanuj Instalatora" border="true":::

6. Kliknij przycisk *Kontynuuj.*

7. Wybierz wyzwalacz skanowania. Można skonfigurować harmonogram lub przeprowadzić skanowanie raz.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="Skanuj wyzwalacz" border="true":::

8. Przejrzyj skanowanie i kliknij pozycję *Zapisz i Uruchom.*

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
