---
title: Rejestrowanie w usłudze Azure kontrolą źródła programu Teradata i ustawień skanowania (wersja zapoznawcza)
description: W tym artykule opisano sposób rejestrowania źródła programu Teradata w usłudze Azure kontrolą i konfigurowania skanowania.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 2008e014e9f160b643ed5f591fff81c0b215e24a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175078"
---
# <a name="register-and-scan-teradata-source-preview"></a>Zarejestruj i Skanuj źródło programu Teradata (wersja zapoznawcza)

W tym artykule opisano sposób rejestrowania źródła programu Teradata w programie kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Źródło programu Teradata obsługuje **pełne skanowanie** w celu wyodrębnienia metadanych z bazy danych programu Teradata i pobrania elementów zależnych **od zasobów** danych.

## <a name="prerequisites"></a>Wymagania wstępne

1.  Skonfiguruj najnowsze [środowisko Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
    Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Upewnij się, że na maszynie wirtualnej zainstalowano środowisko Integration Runtime [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) .

3.  Upewnij się \" , Visual C++ pakiet redystrybucyjny 2012 Update 4 \" jest zainstalowany na komputerze, na którym działa środowisko Integration Runtime. Jeśli \' nie masz jeszcze zainstalowanej usługi, pobierz ją z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Konieczne będzie ręczne pobranie sterownika JDBC programu Teradata na maszynie wirtualnej, na którym działa środowisko Integration Runtime.
    Plik JAR można pobrać z [witryny sieci Web](https://downloads.teradata.com/)programu Teradata.

    > [!Note]
    > Sterownik powinien być dostępny dla wszystkich kont w maszynie wirtualnej. Nie należy instalować programu na koncie użytkownika.

5.  Obsługiwane wersje bazy danych programu Teradata to 12. x do 16. x. Upewnij się, że masz dostęp do odczytu do skanowanego źródła programu Teradata.

## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Jedyne obsługiwane uwierzytelnianie dla źródła programu Teradata jest **uwierzytelnianiem podstawowym**.

## <a name="register-a-teradata-source"></a>Rejestrowanie źródła programu Teradata

Aby zarejestrować nowe źródło programu Teradata w wykazie danych, wykonaj następujące czynności:

1.  Przejdź do konta kontrolą.
2.  Wybierz pozycję **źródła** na lewym pasku nawigacyjnym.
3.  Wybierz pozycję **zarejestruj**
4.  W obszarze Rejestruj źródła wybierz pozycję **Teradata**. Wybierz przycisk **Kontynuuj**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Rejestrowanie opcji programu Teradata" border="true":::

Na ekranie **Rejestr sources (Teradata)** wykonaj następujące czynności:

1.  Wprowadź **nazwę** , z którą zostanie wyświetlone źródło danych w wykazie.

2.  Wprowadź nazwę **hosta** , aby nawiązać połączenie ze źródłem programu Teradata. Może to być również adres IP lub w pełni kwalifikowane parametry połączenia z serwerem.

3.  Wybierz kolekcję lub Utwórz nową (opcjonalnie)

4.  Zakończ, aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Zarejestruj się w programie Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1.  W centrum zarządzania kliknij pozycję **Integration Runtimes**. Upewnij się, że jest skonfigurowany własny środowisko Integration Runtime. Jeśli nie jest on skonfigurowany, wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) , aby skonfigurować własne środowisko Integration Runtime

2.  Przejdź do **źródeł**

3.  Wybierz zarejestrowane źródło programu Teradata.

4.  Wybierz pozycję **+ Nowe skanowanie**

5.  Podaj poniżej szczegóły:

    a.  **Name**: Nazwa skanowania

    b.  **Połącz za pośrednictwem środowiska Integration Runtime**: Wybierz skonfigurowany własny środowisko Integration Runtime.

    c.  **Poświadczenie**: Wybierz poświadczenie, aby nawiązać połączenie ze źródłem danych. Upewnij się, że:

    -   Podczas tworzenia poświadczenia wybierz pozycję Uwierzytelnianie podstawowe.
    -   Podaj nazwę użytkownika, aby połączyć się z serwerem bazy danych w polu wejściowym nazwa użytkownika
    -   Zapisz hasło serwera bazy danych w kluczu tajnym.

        Aby dowiedzieć się więcej na temat poświadczeń, zapoznaj się z linkiem [tutaj](https://docs.microsoft.com/azure/purview/manage-credentials)

6.  **Schemat**: Lista podzestawu schematów do zaimportowania wyrażona jako lista rozdzielonych średnikami. np. Schema1; schema2. Wszystkie schematy użytkownika są importowane, jeśli lista jest pusta. Wszystkie schematy systemowe (na przykład sysadmin) i obiekty są domyślnie ignorowane. Gdy lista jest pusta, wszystkie dostępne schematy są importowane.

    Dozwolone wzorce nazw schematu przy użyciu składni wyrażeń, takich jak wyrażenia SQL, obejmują użycie%, np .%; B % C%; Wykres
    - Zacznij od lub    
    - kończy się na B lub    
    - zawiera C lub    
    - równe D

    Użycie znaków NOT i Special nie jest akceptowalne

7.  **Lokalizacja sterownika**: Określ ścieżkę do lokalizacji sterownika JDBC na maszynie wirtualnej, w której działa środowisko Integration Runtime. Powinna to być ścieżka do prawidłowej lokalizacji folderu JAR.

8.  **Maksymalna dostępna pamięć:** Maksymalna ilość pamięci (w GB) dostępna na maszynie wirtualnej klienta do użycia przez procesy skanowania. Jest to zależne od rozmiaru źródła programu Teradata, które ma zostać przeskanowane.

    > [!Note] 
    > Jako regułę przewijania należy udostępnić 2 GB pamięci dla każdej tabeli 1000

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Skanuj Instalatora" border="true":::

6.  Kliknij przycisk **Kontynuuj**.

7.  Wybierz **wyzwalacz skanowania**. Można skonfigurować harmonogram lub przeprowadzić skanowanie raz.

8.  Przejrzyj skanowanie i kliknij pozycję **Zapisz i uruchom**.

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