---
title: Obliczanie liczby obiektów blob i rozmiaru przy użyciu spisu usługi Azure Storage
description: Dowiedz się, jak obliczyć liczbę i łączny rozmiar obiektów BLOB w kontenerze.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: d1aa91ea0f698e609e786d87a0072e6a07c143a3
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047321"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Oblicz liczbę obiektów blob i łączny rozmiar na kontener przy użyciu spisu usługi Azure Storage

W tym artykule jest stosowana funkcja spisu Blob Storage platformy Azure i usługa Azure Synapse do obliczania liczby obiektów blob i łącznego rozmiaru obiektów BLOB na kontener. Te wartości są przydatne podczas optymalizowania użycia obiektów BLOB na kontener.

Metadane obiektu BLOB nie są uwzględnione w tej metodzie. Funkcja spisu Blob Storage platformy Azure używa interfejsu API REST [obiektów BLOB](/rest/api/storageservices/list-blobs) z domyślnymi parametrami. Dlatego przykład nie obsługuje migawek, kontenerów "$" i tak dalej.

> [!IMPORTANT]
> Spis obiektów BLOB jest obecnie w **wersji zapoznawczej**. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.

## <a name="enable-inventory-reports"></a>Włącz raporty spisu

Pierwszym krokiem w tej metodzie jest [włączenie raportów spisu](blob-inventory.md#enable-inventory-reports) na koncie magazynu. Może być konieczne odczekanie do 24 godzin od momentu włączenia raportów spisu w celu wygenerowania pierwszego raportu.

Jeśli masz raport spisu do przeanalizowania, udziel sobie dostępu do odczytu obiektu BLOB do kontenera, w którym znajduje się plik CSV raportu.

1. Przejdź do kontenera za pomocą pliku raportu CSV spisu.
1. Wybierz **Access Control (IAM)**, a następnie **Dodaj przypisania ról**

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="Wybierz pozycję Dodaj przypisania ról":::

1. Wybierz z listy rozwijanej **rola** **czytnik danych magazynu obiektów BLOB** .

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="Dodaj rolę czytnika danych obiektów blob magazynu z listy rozwijanej":::

1. Wprowadź adres e-mail konta, którego używasz do uruchamiania raportu w polu **Wybierz** .

## <a name="create-an-azure-synapse-workspace"></a>Tworzenie obszaru roboczego usługi Azure Synapse

Następnie [Utwórz obszar roboczy usługi Azure Synapse](../../synapse-analytics/get-started-create-workspace.md) , w którym zostanie wykonane zapytanie SQL w celu zgłoszenia wyników spisu.

## <a name="create-the-sql-query"></a>Tworzenie zapytania SQL

Po utworzeniu obszaru roboczego usługi Azure Synapse wykonaj następujące czynności.

1. Przejdź do [https://web.azuresynapse.net](https://web.azuresynapse.net) .
1. Wybierz kartę **programowanie** na lewej krawędzi.
1. Wybierz znak plus (+), aby dodać element.
1. Wybierz pozycję **skrypt SQL**.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="Wybierz skrypt SQL, aby utworzyć nowe zapytanie":::

## <a name="run-the-sql-query"></a>Uruchamianie zapytania SQL

1. Dodaj następujące zapytanie SQL w obszarze roboczym usługi Azure Synapse [, aby odczytać plik CSV spisu](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    Dla `bulk` parametru Użyj adresu URL pliku CSV raportu spisu, który ma zostać przeanalizowany.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. Nazwij zapytanie SQL w okienku właściwości po prawej stronie.

1. Opublikuj zapytanie SQL, naciskając klawisze CTRL + S lub wybierając przycisk **Opublikuj wszystko** .

1. Wybierz przycisk **Run (Uruchom** ), aby wykonać zapytanie SQL. Liczba obiektów blob i łączny rozmiar na kontener są raportowane w okienku **wyników** .

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="Wyjście z uruchamiania skryptu, aby obliczyć liczbę obiektów blob i łączny rozmiar.":::

## <a name="next-steps"></a>Następne kroki

- [Używanie spisu obiektów BLOB usługi Azure Storage do zarządzania danymi obiektów BLOB](blob-inventory.md)
- [Obliczanie łącznego rozmiaru kontenera obiektów blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)