---
title: 'Szybki start: zbiorcze ładowanie danych za pomocą dedykowanej puli SQL'
description: Użyj Synapse Studio, aby zbiorczo załadować dane do dedykowanej puli SQL w Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 838138fb6ca6f64b4296b54a81bc2764c3f1399c
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567914"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Szybki start: ładowanie zbiorcze za pomocą Synapse Studio

Ładowanie danych jest łatwe dzięki kreatorowi ładowania zbiorczego w Synapse Studio. Synapse Studio to funkcja Azure Synapse Analytics. Kreator ładowania zbiorczego przeprowadzi Cię przez proces tworzenia skryptu T-SQL z [instrukcjeem COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) w celu zbiorczego ładowania danych do dedykowanej puli SQL. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Punkty wejścia do kreatora ładowania zbiorczego

Możesz zbiorczo załadować dane, klikając prawym przyciskiem myszy następujący obszar w usłudze Synapse Studio: plik lub folder z konta usługi Azure Storage dołączonego do obszaru roboczego.

![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy pliku lub folderu z konta magazynu.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Kreator generuje instrukcji COPY, która używa Azure Active Directory (Azure AD) do uwierzytelniania. Użytkownik [usługi Azure AD musi mieć dostęp do](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) obszaru roboczego z co najmniej rolą Współautor danych obiektu blob usługi Storage na platformie Azure dla Azure Data Lake Storage Gen2 magazynu. 

- Jeśli tworzysz nową tabelę do załadowania, musisz mieć wymagane uprawnienia do korzystania z [instrukcji COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) i uprawnień utwórz tabelę.

- Połączona usługa skojarzona z kontem Data Lake Storage Gen2 musi *mieć dostęp do pliku* lub folderu do załadowania. Jeśli na przykład mechanizm uwierzytelniania dla połączonej usługi jest tożsamością zarządzaną, tożsamość zarządzana obszaru roboczego musi mieć co najmniej uprawnienie Czytelnik danych obiektu blob usługi Storage na koncie magazynu.

- Jeśli w obszarze roboczym jest włączona sieć wirtualna, upewnij się, że zintegrowane środowisko uruchomieniowe skojarzone z połączonymi usługami konta usługi Data Lake Storage Gen2 dla źródła danych i lokalizacji pliku błędu ma włączone tworzenie interakcyjne. Tworzenie interakcyjne jest wymagane do wykrywania autoschemii, wyświetlania podglądu zawartości pliku źródłowego i przeglądania Data Lake Storage Gen2 kont magazynu w kreatorze.

## <a name="steps"></a>Kroki

1. Na **panelu Lokalizacja magazynu źródłowego** wybierz konto magazynu i plik lub folder, z których jest ładowany plik. Kreator automatycznie próbuje wykryć pliki Parquet i rozdzielane pliki tekstowe (CSV), w tym mapować pola źródłowe z pliku na odpowiednie docelowe typy danych SQL. 

   ![Zrzut ekranu przedstawiający wybieranie lokalizacji źródłowej.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Wybierz ustawienia formatu pliku, w tym ustawienia błędów dotyczące sytuacji, w których podczas procesu ładowania zbiorczego istnieją odrzucone wiersze. Możesz również wybrać pozycję **Podgląd danych,** aby zobaczyć, jak instrukcja COPY będzie konfigurować plik w celu skonfigurowania ustawień formatu pliku. Wybierz **pozycję Podgląd danych** za każdym razem, gdy zmienisz ustawienie formatu pliku, aby zobaczyć, jak instrukcja COPY analizuje plik przy użyciu zaktualizowanego ustawienia.

   ![Zrzut ekranu przedstawiający podgląd danych.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - Kreator ładowania zbiorczego nie obsługuje wyświetlania podglądu danych za pomocą wieloetapowych terminatorów pól. Po określeniu wieloetapowego terminatora pól kreator wyświetla podgląd danych w jednej kolumnie. 
   > - Po wybraniu opcji **Wywnioskuj** nazwy kolumn kreator ładowania zbiorczego przejmie nazwy kolumn z pierwszego wiersza określonego przez **pole Pierwszy** wiersz. Kreator ładowania zbiorczego automatycznie zwiększa wartość instrukcji COPY o `FIRSTROW` 1, aby zignorować ten wiersz nagłówka. 
   > - Określanie wieloznakowych terminatorów wierszy jest obsługiwane w instrukcji COPY. Jednak kreator ładowania zbiorczego go nie obsługuje i zgłasza błąd.

3. Wybierz dedykowaną pulę SQL używaną do załadowania, w tym informacje o tym, czy obciążenie będzie przeznaczone dla istniejącej tabeli, czy nowej tabeli.
   ![Zrzut ekranu przedstawiający wybieranie lokalizacji docelowej.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Wybierz **pozycję Konfiguruj mapowanie kolumn,** aby upewnić się, że masz odpowiednie mapowanie kolumn. Uwaga Nazwy kolumn zostaną wykryte automatycznie po włączeniu opcji **Wywnioskuj nazwy kolumn**. W przypadku nowych tabel skonfigurowanie mapowania kolumn ma kluczowe znaczenie dla aktualizowania typów danych kolumn docelowych.

   ![Zrzut ekranu przedstawiający konfigurowanie mapowania kolumn.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Wybierz **pozycję Otwórz skrypt.** Skrypt języka T-SQL jest generowany wraz z instrukcjeą COPY w celu załadowania z data lake.
   ![Zrzut ekranu przedstawiający otwieranie skryptu SQL.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [artykułem instrukcja COPY,](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) aby uzyskać więcej informacji na temat możliwości kopiowania.
- Zapoznaj się [z artykułem omówieniem](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) ładowania danych, aby uzyskać informacje na temat korzystania z procesu wyodrębniania, przekształcania i ładowania (ETL).
