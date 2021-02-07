---
title: Połącz z Azure Data Factory
description: W tym artykule opisano sposób łączenia Azure Data Factory i usługi Azure kontrolą w celu śledzenia pochodzenia danych.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 010cfc307d2b2c10c31168fce73673fb1fb611b8
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807652"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Jak połączyć Azure Data Factory i usługę Azure kontrolą

W tym dokumencie opisano kroki wymagane do połączenia konta Azure Data Factory z kontem usługi Azure kontrolą w celu śledzenia elementów związanych z danymi. Dokument zawiera również szczegóły zakresu pokrycia i obsługiwane wzorce elementów podrzędnych.

## <a name="view-existing-data-factory-connections"></a>Wyświetlanie istniejących połączeń Data Factory

Wiele fabryk danych platformy Azure może nawiązać połączenie z pojedynczym Data Catalogem platformy Azure kontrolą, aby wypchnąć informacje o powiązaniu. Bieżący limit umożliwia łączenie dziesięciu Data Factory kont jednocześnie z centrum zarządzania kontrolą. Aby wyświetlić listę kont Data Factory podłączonych do Data Catalog kontrolą, wykonaj następujące czynności:

1. Wybierz pozycję **centrum zarządzania** w okienku nawigacji po lewej stronie.
2. W obszarze **połączenia zewnętrzne** wybierz pozycję **połączenie Data Factory**.
3. Zostanie wyświetlona lista połączeń Data Factory.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Zrzut ekranu przedstawiający listę połączeń usługi Data Factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. Zwróć uwagę na różne wartości **stanu** połączenia:

    - **Połączono**: Fabryka danych jest połączona z wykazem danych.
    - **Rozłączono**: Fabryka danych ma dostęp do wykazu, ale jest połączona z innym katalogiem. W związku z tym nie będzie automatycznie raportowany do wykazu.
    - **CannotAccess**: bieżący użytkownik nie ma dostępu do fabryki danych, więc stan połączenia to nieznany.
 >[!Note]
 >Aby wyświetlić Data Factory połączenia, należy przypisać jedną z ról kontrolą:
 >- Współautor
 >- Właściciel
 >- Czytelnik
 >- Administrator dostępu użytkowników

## <a name="create-new-data-factory-connection"></a>Utwórz nowe połączenie Data Factory

>[!Note]
>Aby dodać lub usunąć połączenia Data Factory, należy przypisać jedną z ról kontrolą:
>- Właściciel
>- Administrator dostępu użytkowników
>
> Oprócz tego wymaga, aby użytkownicy mieli wartość "właściciel" lub "Współautor". 

Wykonaj poniższe kroki, aby połączyć istniejące konta Data Factory z Data Catalogem kontrolą.

1. Wybierz pozycję **centrum zarządzania** w okienku nawigacji po lewej stronie.
2. W obszarze **połączenia zewnętrzne** wybierz pozycję **połączenie Data Factory**.
3. Na stronie **połączenie Data Factory** wybierz pozycję **Nowy**.

4. Wybierz z listy konto Data Factory i wybierz pozycję **OK**. Możesz również filtrować według nazwy subskrypcji, aby ograniczyć listę.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Zrzut ekranu przedstawiający sposób łączenia Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Niektóre wystąpienia Data Factory mogą być wyłączone, jeśli Fabryka danych jest już połączona z bieżącym kontem kontrolą lub Fabryka danych nie ma zarządzanej tożsamości.

    Zostanie wyświetlony komunikat ostrzegawczy, jeśli którykolwiek z wybranych fabryk danych jest już połączony z innym kontem kontrolą. Po wybraniu przycisku OK połączenie Data Factory z innym kontem kontrolą zostanie odłączone. Nie są wymagane żadne dodatkowe potwierdzenia.


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Zrzut ekranu przedstawiający ostrzeżenie, aby rozłączyć Azure Data Factory." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>Obecnie obsługujemy Dodawanie maksymalnie 10 fabryk danych. Jeśli chcesz dodać więcej niż 10 fabryk danych jednocześnie, Utwórz bilet pomocy technicznej.

### <a name="how-does-the-authentication-work"></a>Jak działa uwierzytelnianie?

Gdy użytkownik kontrolą rejestruje Data Factory, do którego mają dostęp, w zapleczu wystąpią następujące zdarzenia:

1. **Data Factory MSI** zostanie dodany do roli RBAC kontrolą: **kontrolą Curator danych**.

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Zrzut ekranu przedstawiający Azure Data Factory MSI." lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. Potoku Data Factory należy wykonać ponownie, aby można było wypchnąć metadane oddzielone do kontrolą.
3. Po wykonaniu Data Factory metadane są wypychane do kontrolą.

### <a name="remove-data-factory-connections"></a>Usuwanie połączeń fabryki danych
Aby usunąć połączenie usługi Fabryka danych, wykonaj następujące czynności:

1. Na stronie **połączenie Data Factory** wybierz przycisk **Usuń** obok jednego lub kilku połączeń usługi Fabryka danych.
2. Wybierz pozycję **Potwierdź** w menu podręcznym, aby usunąć wybrane połączenia fabryki danych.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania fabryk danych w celu usunięcia połączenia." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>Konfigurowanie własnego środowiska IR do zbierania elementów zależnych od Premium SQL

Elementy zależne dla działania kopiowania Data Factory są dostępne dla lokalnych baz danych SQL. Jeśli używasz własnego środowiska Integration Runtime do przenoszenia danych przy użyciu Azure Data Factory i chcesz przechwycić elementy zależne w usłudze Azure kontrolą, upewnij się, że wersja to 4.8.7418.1 lub nowsza. Aby uzyskać więcej informacji na temat samodzielnego środowiska Integration Runtime, zobacz [Tworzenie i Konfigurowanie własnego środowiska Integration Runtime](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="supported-azure-data-factory-activities"></a>Obsługiwane Azure Data Factory działania

Usługa Azure kontrolą przechwytuje środowisko uruchomieniowe z następujących Azure Data Factory działań:

- Kopiowanie danych
- Przepływ danych
- Wykonaj pakiet SSIS

> [!IMPORTANT]
> Usługa Azure kontrolą odrzuca składową, jeśli źródło lub miejsce docelowe używa nieobsługiwanego systemu magazynu danych.

Integracja między Data Factory i kontrolą obsługuje tylko podzestaw systemów danych obsługiwanych przez Data Factory, zgodnie z opisem w poniższych sekcjach.

### <a name="data-factory-copy-data-support"></a>Obsługa Kopiowanie danych Data Factory

| System magazynowania danych | Obsługiwane jako źródło | 
| ------------------- | ------------------- | 
| ADLS Gen1 | Tak | 
| ADLS Gen2 | Tak | 
| Obiekt bob Azure | Tak |
| Azure Cosmos DB (interfejs API SQL) | Tak | 
| Azure Cosmos DB (interfejs API Mongo) | Tak |
| Azure Cognitive Search | Tak | 
| Azure Data Explorer | Tak | 
| Baza danych platformy Azure dla programu Maria \* | Tak | 
| Usługa Azure Database for MYSQL \* | Tak | 
| Azure Database for PostgreSQL \* | Tak |
| Azure File Storage | Tak | 
| Azure Table Storage | Tak |
| Azure SQL Database \* | Tak | 
| Usługa Azure SQL MI \* | Tak | 
| Azure Synapse Analytics (dawniej SQL DW) \* | Tak | 
| SQL Server Premium  \* | Tak | 
| Amazon S3 | Tak | 
| Teradata | Tak | 
| Łącznik tabeli SAP | Tak |
| SAP ECC | Tak | 
| Hive | Tak | 

> [!Note]
> Funkcja współdziałania ma pewne narzuty wydajności w Data Factory działania kopiowania. W przypadku użytkowników, którzy konfigurują połączenia usługi Data Factory w programie kontrolą, mogą wystąpić pewne zadania kopiowania trwające dłużej. Przede wszystkim wpływanie nie ma znaczenia na znikomą. Skontaktuj się z pomocą techniczną, aby uzyskać porównanie czasu, jeśli zadania kopiowania trwają znacznie dłużej niż zwykle.

### <a name="data-factory-data-flow-support"></a>Obsługa przepływu danych Data Factory

| System magazynowania danych | Obsługiwane |
| ------------------- | ------------------- | 
| ADLS Gen1 | Tak |
| ADLS Gen2 | Tak |
| Obiekt bob Azure | Tak |
| Azure SQL Database \* | Tak |
| Azure Synapse Analytics (dawniej SQL DW) \* | Tak |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory wykonywania obsługi pakietów SSIS

| System magazynowania danych | Obsługiwane |
| ------------------- | ------------------- |
| Obiekt bob Azure | Tak |
| ADLS Gen1 | Tak |
| ADLS Gen2 | Tak |
| Azure SQL Database \* | Tak |
| Usługa Azure SQL MI \*| Tak |
| Azure Synapse Analytics (dawniej SQL DW) \* | Tak |
| SQL Server Premium \* | Tak |
| Azure File Storage | Tak |

*\* W przypadku scenariuszy SQL (platformy Azure i lokalnych) usługa Azure kontrolą nie obsługuje procedur składowanych ani skryptów dla elementów zależnych ani skanowanych. Elementy powiązane są ograniczone tylko do źródeł tabel i widoków.*

> [!Note]
> Usługa Azure Data Lake Storage Gen2 jest teraz ogólnie dostępna. Zalecamy, aby zacząć z niej korzystać już dziś. Aby uzyskać więcej informacji, zobacz [stronę produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="supported-lineage-patterns"></a>Obsługiwane wzorce elementów współużytkowania

Istnieje kilka wzorców, które są obsługiwane przez usługę Azure kontrolą. Wygenerowane dane elementów zależnych opierają się na typie źródła i ujścia używanym w działaniach Data Factory. Chociaż Data Factory obsługuje ponad 80 źródeł i ujścia, usługa Azure kontrolą obsługuje tylko podzestaw, zgodnie z opisem w temacie [obsługiwane Azure Data Factory działania](#supported-azure-data-factory-activities).

Aby skonfigurować Data Factory do wysyłania informacji o [pochodzeniem](catalog-lineage-user-guide.md#get-started-with-lineage), zobacz Wprowadzenie do elementów dodanych.

Dostępne są następujące dodatkowe sposoby znajdowania informacji w widoku elementu powiązanego:

- Na karcie **elementy** zależne Umieść kursor na kształtach, aby wyświetlić dodatkowe informacje o elemencie zawartości w etykietce narzędzia.
- Wybierz węzeł lub krawędź, aby wyświetlić typ zasobu, którego należy lub aby przełączyć zasoby.
- Kolumny zestawu danych są wyświetlane po lewej **stronie karty elementy** powiązane. Aby uzyskać więcej informacji na temat elementów autonomicznych na poziomie kolumny, zobacz temat elementy [autonomiczne na poziomie kolumny](catalog-lineage-user-guide.md#column-level-lineage).

### <a name="data-lineage-for-11-operations"></a>Odchodzenie danych dla operacji 1:1

Najbardziej typowym wzorcem przechwytywania danych jest przeniesienie danych z jednego wejściowego zestawu danych do pojedynczego wyjściowego zestawu danych, z procesem między.

Przykładem tego wzorca będzie:

- 1 źródło/dane wejściowe: *Klient* (tabela SQL)
- 1 ujścia/wyjście: *Customer1.csv* (obiekt blob platformy Azure)
- 1 proces: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory działania kopiowania)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="Zrzut ekranu przedstawiający element współdziałania dla jednej do Data Factory operacji kopiowania." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>Przenoszenie danych z obsługą symboli wieloznacznych i 1:1

Innym typowym scenariuszem przechwytywania elementów postanowień jest użycie symbolu wieloznacznego w celu skopiowania plików z pojedynczego wejściowego zestawu danych do pojedynczego wyjściowego zestawu danych. Symbol wieloznaczny umożliwia działanie kopiowania, aby można było dopasować wiele plików do kopiowania przy użyciu wspólnej części nazwy pliku. Usługa Azure kontrolą przechwytuje elementy autonomiczne na poziomie plików dla każdego pojedynczego pliku skopiowanego przez odpowiednie działanie kopiowania.

Przykładem tego wzorca będzie:

* Źródło/dane wejściowe: *CustomerCall \* . csv* (ADLS Gen2 ścieżka)
* Ujścia/wyjście: *CustomerCall \* . csv* (plik obiektów blob platformy Azure)
* 1 proces: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory działania kopiowania)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="Zrzut ekranu przedstawiający element oddzielny dla operacji kopiowania jeden do jednego z obsługą symboli wieloznacznych." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>Przenoszenie danych z elementem n:1

Działań przepływu danych można używać do wykonywania operacji na danych, takich jak scalanie, łączenie i tak dalej. Do utworzenia docelowego zestawu danych można użyć więcej niż jednego źródłowego zestawu danych. W tym przykładzie usługa Azure kontrolą przechwytuje elementy powiązane z plikami wejściowymi na poziomie pliku do tabeli SQL, która jest częścią działania przepływu danych.

Przykładem tego wzorca będzie:

* 2 źródła/dane wejściowe: *Customer.csv*, *Sales. parquet* (ścieżka ADLS Gen2)
* 1 ujścia/wyjście: *dane firmowe* (tabela SQL Azure)
* 1 proces: *DataFlowBlobsToSQL* (działanie przepływu danych Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="Zrzut ekranu przedstawiający element pochodzenie dla n do jednej operacji przepływu danych D F." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>Elementy powiązane z zestawami zasobów

Zestaw zasobów jest obiektem logicznym w katalogu, który reprezentuje wiele plików partycji w źródłowym magazynie. Aby uzyskać więcej informacji, zobacz [Omówienie zestawów zasobów](concept-resource-sets.md). Gdy usługa Azure kontrolą przechwytuje elementy powiązane z Azure Data Factory, stosuje reguły do normalizacji pojedynczych plików partycji i tworzy pojedynczy obiekt logiczny.

W poniższym przykładzie zestaw zasobów Azure Data Lake Gen2 jest tworzony na podstawie obiektu blob platformy Azure:

* 1 źródło/dane wejściowe *: \_management.csvpracownika* (obiekt blob platformy Azure)
* 1 ujścia/wyjście: *\_management.csvpracownika* (Azure Data Lake Gen 2)
* 1 proces: *CopyBlobToAdlsGen2 \_ RS* (działanie kopiowania Data Factory)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="Zrzut ekranu przedstawiający element oddzielny dla zestawu zasobów." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>Następne kroki

- [Podręcznik użytkownika z wykazem](catalog-lineage-user-guide.md)
- [Link do udziału danych platformy Azure dla elementu powiązanego](how-to-link-azure-data-share.md)
