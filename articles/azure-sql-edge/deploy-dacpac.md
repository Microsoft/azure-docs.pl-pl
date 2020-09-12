---
title: Korzystanie z pakietów SQL Database DACPAC i BACPAC — Azure SQL Edge (wersja zapoznawcza)
description: Dowiedz się więcej o korzystaniu z dacpacs i BacPacs w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462763"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database pakietów DACPAC i BACPAC w programie SQL Edge

Usługa Azure SQL Edge (wersja zapoznawcza) to zoptymalizowany aparat relacyjnej bazy danych umożliwiający wdrażanie IoT i Edge. Jest on oparty na najnowszych wersjach aparatu bazy danych Microsoft SQL Server, który zapewnia wiodące w branży funkcje, zabezpieczenia i przetwarzanie zapytań. Dzięki wiodącym w branży funkcjom zarządzania relacyjnymi bazami danych SQL Server usługa Azure SQL Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i złożonych zdarzeń.

Usługa Azure SQL Edge oferuje również natywną implementację SqlPackage.exe, która umożliwia wdrożenie pakietu [SQL Database dacpac i BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) podczas wdrażania programu SQL Edge. SQL Database dacpacs można wdrożyć w usłudze SQL Edge przy użyciu parametru sqlpackage dostępnego za pośrednictwem `module twin's desired properties` opcji modułu programu SQL Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Pole | Opis |
|------|-------------|
| SqlPackage | Identyfikator URI magazynu obiektów blob platformy Azure dla pliku *zip* , który zawiera SQL Database pakiet DAC lub BACPAC. Plik zip może zawierać jednocześnie wiele pakietów DAC lub plików BACPAC.
| ASAJobInfo | Identyfikator URI magazynu obiektów blob platformy Azure dla zadania ASA Edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Używanie pakietu SQL Database DAC z programem SQL Edge

Aby użyć SQL Database pakietu DAC `(*.dacpac)` lub pliku BACPAC z programem `(*.bacpac)` SQL Edge, wykonaj następujące kroki:

1. Utwórz/Wyodrębnij pakiet DAC lub wyeksportuj plik BACPAC przy użyciu mechanizmu wymienionego poniżej. 
    - Utwórz lub Wyodrębnij SQL Database pakiet DAC. Zobacz [wyodrębnianie aplikacji DAC z bazy danych,](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) Aby uzyskać informacje na temat generowania pakietu DAC dla istniejącej bazy danych SQL Server.
    - Eksportowanie wdrożonego pakietu DAC lub bazy danych. Zapoznaj się z tematem [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) , aby uzyskać informacje na temat generowania pliku BACPAC dla istniejącej bazy danych SQL Server.

2. Kod zip `*.dacpac` lub `*.bacpac` plik i przekaż go do konta usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat przekazywania plików do usługi Azure Blob Storage, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Wygeneruj sygnaturę dostępu współdzielonego dla pliku zip przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

4. Zaktualizuj konfigurację modułu programu SQL Edge w taki sposób, aby zawierała identyfikator URI dostępu współdzielonego dla pakietu DAC. Aby zaktualizować moduł programu SQL Edge, wykonaj następujące kroki:

    1. W Azure Portal przejdź do wdrożenia IoT Hub.

    2. W lewym okienku wybierz pozycję **IoT Edge**.

    3. Na stronie **IoT Edge** Znajdź i wybierz IoT Edge, w którym wdrożono moduł programu SQL Edge.

    4. Na stronie urządzenie **IoT Edge** wybierz pozycję **Ustaw moduł**.

    5. Na stronie **Ustawianie modułów** wybierz pozycję **Konfiguruj** względem modułu SQL Edge.

    6. W okienku **IoT Edge modułów niestandardowych** wybierz opcję **Ustaw odpowiednie właściwości sznurka modułu**. Zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla `SQLPackage` opcji, jak pokazano w poniższym przykładzie.

        > [!NOTE]
        > Identyfikator URI sygnatury dostępu współdzielonego w poniższym kodzie JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Wybierz pozycję **Zapisz**.

    8. Na stronie **Ustawianie modułów** wybierz pozycję **dalej**.

    9. Na stronie **Ustawianie modułów** wybierz pozycję **dalej** , a następnie **Prześlij**.

5. Po aktualizacji modułu plik pakietu zostanie pobrany, rozpakowany i wdrożony względem wystąpienia programu SQL Edge.

Po każdym ponownym uruchomieniu kontenera usługi Azure SQL Edge `*.dacpac` pakiet plików jest pobierany i oceniany pod kątem zmian. Jeśli zostanie napotkana Nowa wersja pliku dacpac, zmiany zostaną wdrożone w bazie danych programu SQL Edge. Pliki BACPAC 

## <a name="next-steps"></a>Następne kroki

- [Wdróż program SQL Edge za Azure Portal](deploy-portal.md).
- [Przesyłanie strumieniowe danych](stream-data.md)
- [Uczenie maszynowe i AI z ONNX w usłudze SQL Edge (wersja zapoznawcza)](onnx-overview.md)
