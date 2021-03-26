---
title: Rejestrowanie skanowania źródła SAP ECC i instalacji (wersja zapoznawcza) w usłudze Azure kontrolą
description: W tym artykule opisano sposób rejestrowania źródła SAP ECC na platformie Azure kontrolą i konfigurowania skanowania.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: af3f54778882df9aaa06297f291c12a0f4b1577c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046049"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Rejestrowanie i skanowanie źródła SAP ECC (wersja zapoznawcza)

W tym artykule opisano sposób rejestrowania źródła SAP ECC w programie kontrolą i konfigurowania skanowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Źródło SAP ECC obsługuje **pełne skanowanie** w celu wyodrębnienia metadanych z wystąpienia SAP ECC i Pobiera elementy **powiązane między zasobami** danych.

## <a name="prerequisites"></a>Wymagania wstępne

1.  Skonfiguruj najnowsze [środowisko Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
    Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

2.  Upewnij się, że na maszynie wirtualnej zainstalowano środowisko Integration Runtime [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) .

3.  Upewnij się \" , Visual C++ pakiet redystrybucyjny 2012 Update 4 \" jest zainstalowany na komputerze, na którym działa środowisko Integration Runtime. Jeśli \' nie masz jeszcze zainstalowanej usługi, pobierz ją z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Pobierz 64-bitowy [Łącznik SAP dla Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) z witryny SAP \' s i zainstaluj go na komputerze, na którym działa środowisko Integration Runtime. Podczas instalacji upewnij się, że wybrano opcję **Zainstaluj zestawy do GAC** w oknie **opcjonalne kroki instalacji** .

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="wymagania wstępne" border="true":::

5.  Łącznik odczytuje metadane z platformy SAP przy użyciu interfejsu API programu Java Connector (JCo) 3,0. W związku z tym upewnij się, że łącznik Java jest dostępny na maszynie wirtualnej, na której jest zainstalowany własny środowisko Integration Runtime.
    Upewnij się, że używasz odpowiedniej dystrybucji JCo dla danego środowiska. Na przykład na komputerze z systemem Microsoft Windows upewnij się, że pliki sapjco3. jar i sapjco3.dll są dostępne.

    > [!Note] 
    > Sterownik powinien być dostępny dla wszystkich kont w maszynie wirtualnej. Nie należy instalować go na koncie użytkownika.

6.  Wdróż moduł funkcji ABAP wyodrębniania metadanych na serwerze SAP, wykonując czynności opisane w [przewodniku wdrażania funkcji ABAP](abap-functions-deployment-guide.md). Do utworzenia modułu funkcji RFC na serwerze SAP będzie potrzebne konto dewelopera ABAP. Konto użytkownika wymaga wystarczających uprawnień do nawiązania połączenia z serwerem SAP i wykonywania następujących modułów funkcji RFC:
    -   STFC_CONNECTION (sprawdź łączność)
    -   RFC_SYSTEM_INFO (Sprawdź informacje o systemie)


## <a name="setting-up-authentication-for-a-scan"></a>Konfigurowanie uwierzytelniania na potrzeby skanowania

Jedyne obsługiwane uwierzytelnianie dla źródła SAP ECC to **podstawowe uwierzytelnianie**.

## <a name="register-sap-ecc-source"></a>Rejestrowanie źródła SAP ECC

Aby zarejestrować nowe źródło SAP ECC w wykazie danych, wykonaj następujące czynności:

1.  Przejdź do konta kontrolą.
2.  Wybierz pozycję **źródła** na lewym pasku nawigacyjnym.
3.  Wybierz pozycję **zarejestruj**
4.  W obszarze Rejestruj źródła wybierz pozycję **SAP ECC**. Wybierz pozycję **Kontynuuj.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="Rejestrowanie opcji SAPECC" border="true":::

Na ekranie **Rejestr sources (SAP ECC)** wykonaj następujące czynności:

1.  Wprowadź **nazwę** , która zostanie wyświetlona na liście w wykazie.

2.  Wprowadź nazwę **serwera aplikacji** , aby nawiązać połączenie ze źródłem SAP ECC.
    Może być również adresem IP hosta serwera aplikacji SAP.

3.  Wprowadź **numer systemu** SAP. Jest to dwucyfrowa liczba całkowita z zakresu od 00 do 99.

4.  Wybierz kolekcję lub Utwórz nową (opcjonalnie)

5.  Zakończ, aby zarejestrować źródło danych.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="Zarejestruj SAPECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Tworzenie i Uruchamianie skanowania

Aby utworzyć i uruchomić nowe skanowanie, wykonaj następujące czynności:

1.  W centrum zarządzania kliknij pozycję Integration Runtimes. Upewnij się, że jest skonfigurowany własny środowisko Integration Runtime. Jeśli nie jest on skonfigurowany, wykonaj kroki opisane [tutaj](./manage-integration-runtimes.md) , aby utworzyć własne środowisko Integration Runtime.

2.  Przejdź do **źródeł**

3.  Wybierz zarejestrowane źródło SAP ECC.

4.  Wybierz pozycję **+ Nowe skanowanie**

5.  Podaj poniżej szczegóły:

    a.  **Name**: Nazwa skanowania

    b.  **Połącz za pośrednictwem środowiska Integration Runtime**: Wybierz skonfigurowane środowisko Integration Runtime (własne)

    c.  **Poświadczenie**: Wybierz poświadczenie, aby nawiązać połączenie ze źródłem danych. Upewnij się, że:

    -   Podczas tworzenia poświadczenia wybierz pozycję Uwierzytelnianie podstawowe.
    -   Podaj identyfikator użytkownika, aby połączyć się z serwerem SAP w polu wejściowym nazwa użytkownika.
    -   Zapisz hasło użytkownika używane do nawiązania połączenia z serwerem SAP w kluczu tajnym.

    d.  **Identyfikator klienta**: Wprowadź identyfikator klienta SAP. Jest to trzy cyfry liczb od 1000 do 999.

    e.  **Ścieżka biblioteki JCo**: ścieżka katalogu, w której znajdują się biblioteki JCo

    f.  **Maksymalna dostępna pamięć:** Maksymalna ilość pamięci (w GB) dostępna na maszynie wirtualnej klienta do użycia przez procesy skanowania. Jest to zależne od rozmiaru źródła SAP ECC, które ma zostać przeskanowane.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="Skanuj SAPECC" border="true":::

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