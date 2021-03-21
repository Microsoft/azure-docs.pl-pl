---
title: 'Szybki Start: ładowanie zbiorcze danych za pomocą dedykowanej puli SQL'
description: Użyj programu Synapse Studio do zbiorczego ładowania danych do dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: be15a37a9a2965da36f7e8f884a0a3112106b9ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586730"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Szybki Start: ładowanie zbiorcze za pomocą programu Synapse Studio

Ładowanie danych jest łatwe w Kreatorze ładowania zbiorczego w programie Synapse Studio. Synapse Studio to funkcja analizy usługi Azure Synapse. Kreator ładowania zbiorczego prowadzi użytkownika przez proces tworzenia skryptu T-SQL za pomocą [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) do zbiorczego ładowania danych do dedykowanej puli SQL. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Punkty wejścia do kreatora ładowania zbiorczego

Dane można ładować zbiorczo, klikając prawym przyciskiem myszy w obszarze Synapse Studio: plik lub folder z konta usługi Azure Storage, które jest dołączone do obszaru roboczego.

![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy pliku lub folderu na koncie magazynu.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Kreator generuje instrukcję COPY, która używa usługi Azure Active Directory (Azure AD) do uwierzytelniania. [Użytkownik usługi Azure AD musi mieć dostęp](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) do obszaru roboczego z co najmniej rolą współautor danych obiektów blob magazynu dla konta usługi Azure Data Lake Storage Gen2. 

- Musisz mieć wymagane [uprawnienia do używania instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) i CREATE TABLE, jeśli tworzysz nową tabelę do załadowania do programu.

- Połączona usługa skojarzona z kontem Data Lake Storage Gen2 *musi mieć dostęp do pliku lub folderu* do załadowania. Na przykład jeśli mechanizm uwierzytelniania dla połączonej usługi jest tożsamością zarządzaną, tożsamość zarządzana obszaru roboczego musi mieć co najmniej uprawnienie czytnika danych obiektów blob magazynu na koncie magazynu.

- Jeśli sieć wirtualna jest włączona w obszarze roboczym, upewnij się, że zintegrowane środowisko uruchomieniowe skojarzone z połączonymi usługami konta Data Lake Storage Gen2 dla danych źródłowych i lokalizacji pliku błędu ma włączone Tworzenie interaktywne. Aby można było wykryć autoschemat, należy wyświetlić podgląd zawartości pliku źródłowego i przeglądać Data Lake Storage Gen2 konta magazynu w kreatorze.

## <a name="steps"></a>Kroki

1. W panelu **źródłowa lokalizacja magazynu** wybierz konto magazynu i plik lub folder, z którego są ładowane. Kreator automatycznie podejmuje próbę wykrycia plików Parquet i rozdzielonych plików tekstowych (CSV), w tym mapowania pól źródłowych z pliku do odpowiednich docelowych typów danych SQL. 

   ![Zrzut ekranu, który pokazuje Wybieranie lokalizacji źródłowej.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Wybierz ustawienia formatu pliku, w tym ustawienia błędów w przypadku odrzucenia wierszy w procesie ładowania zbiorczego. Możesz również wybrać pozycję **Podgląd danych** , aby zobaczyć, jak instrukcja Copy przeanalizuje plik w celu ułatwienia konfigurowania ustawień formatu pliku. Wybierz pozycję **Podgląd danych** przy każdym zmianie ustawienia formatu pliku, aby zobaczyć, jak instrukcja Copy przeanalizuje plik ze zaktualizowanym ustawieniem.

   ![Zrzut ekranu pokazujący Podgląd danych.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Kreator ładowania zbiorczego nie obsługuje podglądu danych za pomocą terminatorów pól o wiele znaków. Po określeniu terminatora pola o kilku znakach Kreator wyświetli podgląd danych w jednej kolumnie. 
   > - Po wybraniu opcji **wywnioskowanie nazw kolumn** Kreator ładowania zbiorczego przeanalizuje nazwy kolumn z pierwszego wiersza określonego przez **pierwsze pole wiersza** . Kreator ładowania zbiorczego automatycznie zwiększy `FIRSTROW` wartość w instrukcji Copy o 1, aby zignorować ten wiersz nagłówka. 
   > - Określanie wieloznakowych terminatorów wierszy jest obsługiwane w instrukcji COPY. Jednak Kreator ładowania zbiorczego nie obsługuje go i zgłosi błąd.

3. Wybierz dedykowaną pulę SQL, która ma być używana do ładowania, włącznie z tym, czy obciążenie będzie dotyczyć istniejącej tabeli czy nowej tabeli.
   ![Zrzut ekranu, który pokazuje Wybieranie lokalizacji docelowej.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Wybierz pozycję **Konfiguruj Mapowanie kolumn** , aby upewnić się, że masz odpowiednie mapowanie kolumn. Nazwy kolumn notatek będą wykrywane automatycznie po włączeniu **nazw kolumn**. W przypadku nowych tabel Konfigurowanie mapowania kolumn ma kluczowe znaczenie dla aktualizowania typów danych kolumny docelowej.

   ![Zrzut ekranu pokazujący Konfigurowanie mapowania kolumn.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Wybierz pozycję **Otwórz skrypt**. Skrypt T-SQL jest generowany przy użyciu instrukcji COPY do załadowania z usługi Data Lake.
   ![Zrzut ekranu pokazujący otwieranie skryptu SQL.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z artykułem [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) , aby uzyskać więcej informacji na temat możliwości kopiowania.
- Zapoznaj się z artykułem [Omówienie ładowania danych](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) , aby uzyskać informacje na temat korzystania z procesu wyodrębniania, przekształcania i ładowania (ETL).
