---
title: Korzystanie z pakietów SQL Database DACPAC i BACPAC — Azure SQL Edge
description: Dowiedz się więcej o korzystaniu z dacpacs i BacPacs w usłudze Azure SQL Edge
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392252"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database pakietów DACPAC i BACPAC w programie SQL Edge

Usługa Azure SQL Edge to aparat relacyjnej bazy danych zoptymalizowany pod kątem wdrożeń IoT i brzegowych. Jest on oparty na najnowszych wersjach aparatu Microsoft SQL Database, który zapewnia wiodące w branży funkcje, zabezpieczenia i przetwarzanie zapytań. Dzięki wiodącym w branży funkcjom zarządzania relacyjnymi bazami danych SQL Server usługa Azure SQL Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i złożonych zdarzeń.

Usługa Azure SQL Edge zapewnia natywny mechanizm umożliwiający wdrażanie [SQL Database dacpac i BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) pakietu w trakcie lub po wdrożeniu programu SQL Edge.

Pakiety SQL Database dacpac i BACPAC można wdrażać w usłudze SQL Edge przy użyciu `MSSQL_PACKAGE` zmiennej środowiskowej. Zmienna środowiskowa można skonfigurować przy użyciu dowolnego z następujących elementów.  
- Lokalizacja folderu lokalnego w kontenerze SQL zawierającym pliki dacpac i BACPAC. Ten folder można zamapować na wolumin hosta przy użyciu punktów instalacji lub kontenerów woluminów danych. 
- Lokalna ścieżka pliku w ramach mapowania kontenera SQL do pliku dacpac lub BACPAC. Tę ścieżkę pliku można zamapować na wolumin hosta przy użyciu punktów instalacji lub kontenerów woluminów danych. 
- Lokalna ścieżka pliku w ramach mapowania kontenera SQL do pliku zip zawierającego pliki dacpac lub BACPAC. Tę ścieżkę pliku można zamapować na wolumin hosta przy użyciu punktów instalacji lub kontenerów woluminów danych. 
- Adres URL sygnatury dostępu współdzielonego usługi Azure BLOB do pliku zip zawierającego pliki dacpac i BACPAC.
- Adres URL sygnatury dostępu współdzielonego usługi Azure BLOB do pliku dacpac lub BACPAC. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Używanie pakietu SQL Database DAC z programem SQL Edge

Aby wdrożyć (lub zaimportować) SQL Database pakiet DAC `(*.dacpac)` lub plik BACPAC `(*.bacpac)` za pomocą usługi Azure Blob Storage i pliku zip, wykonaj poniższe kroki. 

1. Utwórz/Wyodrębnij pakiet DAC lub wyeksportuj plik BACPAC przy użyciu mechanizmu wymienionego poniżej. 
    - Utwórz lub Wyodrębnij SQL Database pakiet DAC. Zobacz [wyodrębnianie aplikacji DAC z bazy danych,](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) Aby uzyskać informacje na temat generowania pakietu DAC dla istniejącej bazy danych SQL Server.
    - Eksportowanie wdrożonego pakietu DAC lub bazy danych. Zapoznaj się z tematem [Eksportowanie aplikacji warstwy danych](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) , aby uzyskać informacje na temat generowania pliku BACPAC dla istniejącej bazy danych SQL Server.

2. Kod zip `*.dacpac` lub `*.bacpac` plik i przekaż go do konta usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat przekazywania plików do usługi Azure Blob Storage, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Wygeneruj sygnaturę dostępu współdzielonego dla pliku zip przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

4. Zaktualizuj konfigurację modułu programu SQL Edge w taki sposób, aby zawierała identyfikator URI dostępu współdzielonego dla pakietu DAC. Aby zaktualizować moduł programu SQL Edge, wykonaj następujące kroki:

    1. W Azure Portal przejdź do wdrożenia IoT Hub.

    2. W lewym okienku wybierz pozycję **IoT Edge**.

    3. Na stronie **IoT Edge** Znajdź i wybierz IoT Edge, w którym wdrożono moduł programu SQL Edge.

    4. Na stronie urządzenie **IoT Edge** wybierz pozycję **Ustaw moduł**.

    5. Na stronie **Ustawianie modułów** i kliknij moduł Azure SQL Edge.

    6. W okienku **Aktualizowanie modułu IoT Edge** wybierz pozycję **zmienne środowiskowe**. Dodaj `MSSQL_PACKAGE` zmienną środowiskową i określ adres URL sygnatury dostępu współdzielonego wygenerowany w kroku 3 powyżej jako wartość zmiennej środowiskowej. 

    7. Wybierz pozycję **Aktualizuj**.

    8. Na stronie **Ustawianie modułów** wybierz pozycję **Przegląd + Utwórz**.

    9. Na stronie **Ustawianie modułów** wybierz pozycję **Utwórz**.

5. Po aktualizacji modułu pliki pakietu są pobierane, rozpakowane i wdrażane względem wystąpienia programu SQL Edge.

Po każdym ponownym uruchomieniu kontenera usługi Azure SQL Edge program SQL Edge próbuje pobrać spakowany pakiet plików i oszacować zmiany. Jeśli zostanie napotkana Nowa wersja pliku dacpac, zmiany zostaną wdrożone w bazie danych programu SQL Edge.

## <a name="known-issue"></a>Znany problem

Podczas niektórych wdrożeń DACPAC lub BACPAC użytkownicy mogą napotkać limity czasu polecenia, co spowodowało niepowodzenie operacji wdrażania dacpac. Jeśli wystąpi ten problem, użyj SQLPackage.exe (lub narzędzi klienta SQL), aby zastosować DACPAC lub BACPAC maually. 

## <a name="next-steps"></a>Następne kroki

- [Wdróż program SQL Edge za Azure Portal](deploy-portal.md).
- [Przesyłanie strumieniowe danych](stream-data.md)
- [Uczenie maszynowe i AI z ONNX w usłudze SQL Edge](onnx-overview.md)