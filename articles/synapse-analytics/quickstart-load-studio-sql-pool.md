---
title: 'Szybki Start: ładowanie zbiorcze danych za pomocą dedykowanej puli SQL'
description: Użyj programu Synapse Studio do zbiorczego ładowania danych do dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 2a4740699d70601591645aa0d3183531a6687be6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324935"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Szybki Start: ładowanie zbiorcze przy użyciu języka SQL Synapse

Ładowanie danych nigdy nie było łatwiejsze w przypadku korzystania z Kreatora ładowania zbiorczego w programie Synapse Studio. Ten Kreator przeprowadzi Cię przez proces tworzenia skryptu T-SQL za pomocą [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) do ładowania zbiorczego danych. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Punkty wejścia do kreatora ładowania zbiorczego

Teraz można łatwo ładować dane przy użyciu dedykowanych pul SQL z prostym kliknięciem prawym przyciskiem myszy w następujących obszarach w programie Synapse Studio:

- Plik lub folder z konta usługi Azure Storage dołączonego do obszaru roboczego ![ kliknij prawym przyciskiem myszy plik lub folder na koncie magazynu](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Ten Kreator generuje instrukcję COPY, która używa usługi Azure AD Pass-through do uwierzytelniania. [Użytkownik usługi Azure AD musi mieć dostęp](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) do obszaru roboczego z co najmniej rolą współautor danych obiektów blob magazynu dla konta usługi ADLS Gen2. 

- Musisz mieć wymagane [uprawnienia do używania instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) i CREATE TABLE, jeśli tworzysz nową tabelę do załadowania do programu.

- Połączona usługa skojarzona z kontem ADLS Gen2 **musi mieć dostęp do** / **folderu** plików do załadowania. Na przykład jeśli mechanizm uwierzytelniania połączonej usługi jest tożsamością zarządzaną, tożsamość zarządzana w obszarze roboczym musi mieć co najmniej uprawnienie czytelnik magazynu obiektów BLOB na koncie magazynu.

- Jeśli sieć wirtualna jest włączona w obszarze roboczym, upewnij się, że zintegrowane środowisko uruchomieniowe skojarzone z usługami połączonymi ADLS Gen2 konta dla danych źródłowych i w lokalizacji pliku błędu ma włączoną funkcję interaktywnego tworzenia. Aby można było wykryć autoschemat, należy wyświetlić podgląd zawartości pliku źródłowego i przeglądać ADLS Gen2 konta magazynu w kreatorze.

### <a name="steps"></a>Kroki

1. Wybierz konto magazynu i plik lub folder, który jest ładowany w panelu źródłowa lokalizacja magazynu: ![ Wybieranie lokalizacji źródłowej](./sql/media/bulk-load/bulk-load-source-location.png)

2. Wybierz ustawienia formatu pliku, w tym konto magazynu, w którym chcesz napisać odrzucone wiersze (plik błędu). Obecnie obsługiwane są tylko pliki CSV i Parquet.

    ![Wybieranie ustawień formatu pliku](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Możesz wybrać opcję "Podgląd danych", aby zobaczyć, jak instrukcja COPY przeanalizuje plik w celu ułatwienia konfigurowania ustawień formatu pliku. Wybierz pozycję "Podgląd danych" za każdym razem, gdy zmienisz ustawienie formatu pliku, aby zobaczyć, jak instrukcja COPY przeanalizuje plik ze zaktualizowanym ustawieniem: ![ Podgląd danych](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Wybierz dedykowaną pulę SQL, która jest używana do ładowania, w tym czy obciążenie będzie dla istniejącej tabeli czy nowej tabeli: ![ Wybieranie lokalizacji docelowej](./sql/media/bulk-load/bulk-load-target-location.png)

5. Wybierz pozycję "Konfiguruj Mapowanie kolumn", aby upewnić się, że masz odpowiednie mapowanie kolumn. W przypadku nowych tabel Konfigurowanie mapowania kolumn ma krytyczne znaczenie dla aktualizowania typów danych kolumny docelowej: ![ Konfigurowanie mapowania kolumn](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Wybierz pozycję "Otwórz skrypt", a skrypt T-SQL zostanie wygenerowany przy użyciu instrukcji COPY do załadowania z usługi Data Lake: ![ otwieranie skryptu SQL](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z artykułem [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) , aby uzyskać więcej informacji na temat możliwości kopiowania
- Zapoznaj się z artykułem [Omówienie ładowania danych](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)
