---
title: Szybki start — tworzenie obszaru roboczego
description: Utwórz obszar roboczy usługi Azure Synapse Analytics, wykonując kroki opisane w tym przewodniku.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1ccfb1b008a7e3e48f56b1af62087d303522ed73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676725"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Szybki start: tworzenie obszaru roboczego usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym przewodniku Szybki start opisano kroki tworzenia obszaru roboczego usługi Azure Synapse przy użyciu witryny Azure portal.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto przed rozpoczęciem](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Konto magazynu usługi Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Logowanie się do [witryny Azure portal](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Tworzenie obszaru roboczego Usługi Azure Synapse przy użyciu portalu Azure

1. W okienku wyszukiwania platformy Microsoft Azure wprowadź **obszar roboczy Synapse,** a następnie wybierz tę usługę.
![Pasek wyszukiwania witryny Azure portal z wpisanych](media/quickstart-create-synapse-workspace/workspace-search.png)obszarami roboczymi Usługi Azure Synapse. .
2. Na stronie **Obszary robocze Synapse** kliknij pozycję **+ Dodaj**.
![Polecenie, aby utworzyć nowy obszar roboczy Usługi Azure Synapse wyróżniony. ](media/quickstart-create-synapse-workspace/create-workspace-02.png).
3. Wypełnij formularz **obszaru roboczego Usługi Azure Synapse** następującymi informacjami:

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Subskrypcja** | *Twoja subskrypcja* | Aby uzyskać szczegółowe informacje o subskrypcjach, zobacz [Subskrypcje](https://account.windowsazure.com/Subscriptions). |
    | **Grupa zasobów** | *Dowolna grupa zasobów* | Prawidłowe nazwy grup zasobów opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Reguły i ograniczenia nazewnictwa). |
    | **Nazwa obszaru roboczego** | mysampleworkspace | Określa nazwę obszaru roboczego, który będzie również używany dla punktów końcowych połączenia.|
    | **Region** | Wschodnie stany USA 2 | Określa lokalizację obszaru roboczego.|
    | **Usługa Data Lake Storage 2. generacji** | Konta:`storage account name` </br> System plików:`root file system to use` | Określa nazwę konta magazynu ADLS Gen2, który ma być używany jako magazyn podstawowy, oraz system plików do użycia.|
    ||||

    ![Przepływ inicjowania obsługi administracyjnej obszaru roboczego — karta Podstawy.](media/quickstart-create-synapse-workspace/create-workspace-03.png).

    Konto magazynu można wybrać z:
    - Lista kont ADLS Gen2 dostępnych w ramach subskrypcji
    - Wprowadzone ręcznie przy użyciu nazwy konta

    > [!IMPORTANT]
    > Obszar roboczy Usługi Azure Synapse musi mieć możliwość odczytu i zapisu na wybranym koncie ADLS Gen2. Ponadto dla każdego konta magazynu, które łączysz jako podstawowe konto magazynu, podczas tworzenia konta magazynu musi być włączony **hierarchiczny obszar nazw.**
    >
    > Poniżej pól wyboru ADLS Gen2 znajduje się notatka informująca, że zarządzana tożsamość obszaru roboczego zostanie przypisana rola **Współautora danych obiektów Blob Storaqe** w wybranym systemie plików Data Lake Storage Gen2, przyznając mu pełny dostęp.

4. (Opcjonalnie) Zmodyfikuj dowolną kartę **Domyślne zabezpieczenia + sieć:**
5. (Opcjonalnie) Dodaj wszystkie znaczniki na karcie **Znaczniki.**
6. Karta **Podsumowanie** uruchomi niezbędne weryfikacje, aby upewnić się, że obszar roboczy może zostać pomyślnie utworzony. Po pomyślnym zakończeniu sprawdzania poprawności naciśnij klawisz **Create** ![](media/quickstart-create-synapse-workspace/create-workspace-05.png)Workspace provisioning flow - confirmation tab. .
7. Po pomyślnym zakończeniu procesu inicjowania obsługi administracyjnej zasobów na liście obszarów roboczych Synapse zostanie wyświetlony wpis utworzenia obszaru roboczego. ![Lista obszarów roboczych Synapse przedstawiających nowo aprowizowany obszar roboczy.](media/quickstart-create-synapse-workspace/create-workspace-07.png).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wykonaj poniższe czynności, aby usunąć obszar roboczy Usługi Azure Synapse.
> [!WARNING]
> Usunięcie obszaru roboczego Usługi Azure Synapse spowoduje usunięcie aparatów analitycznych i danych przechowywanych w bazie danych zawartych w pulach SQL i metadanych obszaru roboczego. Nie będzie już możliwe połączenie z punktami końcowymi SQL, punktami końcowymi Platformy Apache Spark. Wszystkie artefakty kodu zostaną usunięte (kwerendy, notesy, definicje zadań i potoki).
>
> Usunięcie obszaru roboczego **nie** wpłynie na dane w obszarze roboczym Magazynu usługi Data Lake Store połączone z obszarem roboczym.

Jeśli chcesz usunąć obszar roboczy Usługi Azure Synapse, wykonaj następujące kroki:

1. Przejdź do obszaru roboczego Usługi Azure Synapse, aby usunąć.
1. Naciśnij **klawisz delete** na pasku poleceń.
 ![Omówienie obszaru roboczego Usługi Azure Synapse — wyróżnione polecenie delete.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Potwierdź usunięcie i naciśnij przycisk **Usuń.**
 ![Omówienie obszaru roboczego Usługi Azure Synapse — okno dialogowe potwierdzenia usuwania obszaru roboczego.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Po pomyślnym zakończeniu procesu obszar roboczy Usługi Azure Synapse nie będzie już wyświetlany na liście obszarów roboczych.

## <a name="next-steps"></a>Następne kroki

Następnie można [utworzyć pule SQL](quickstart-create-sql-pool.md) lub [utworzyć pule Platformy Apache Spark,](quickstart-create-apache-spark-pool.md) aby rozpocząć analizowanie i eksplorowanie danych.
