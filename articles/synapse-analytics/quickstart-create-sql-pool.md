---
title: Szybki start — tworzenie puli SQL synapsy
description: Utwórz nową pulę SQL Synapse dla obszaru roboczego Usługi Azure Synapse Analytics Synapse Workspace, wykonując kroki opisane w tym przewodniku.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b3cd4ed280529efbde32fb89e6bac20640940fff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423908"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Szybki start: tworzenie puli SQL synapsy

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne ułatwiające pozyskiwania, przekształcanie, modelowanie, analizowanie i obsługiwanie danych. Pula SQL oferuje możliwości obliczeniowe i magazynowe oparte na języku T-SQL. Po utworzeniu puli SQL w obszarze roboczym Synapse dane mogą być ładowane, modelowane, przetwarzane i obsługiwane w celu uzyskania szczegółowych informacji.

W tym przewodniku Szybki start nauczysz się tworzyć pulę SQL w obszarze roboczym Synapse przy użyciu portalu Azure.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto przed rozpoczęciem](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

[Obszar roboczy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Logowanie się do [witryny Azure portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Tworzenie nowej puli SQL

1. W obszarze roboczym Synapse, w którym chcesz utworzyć pulę SQL, kliknij polecenie **Nowa pula SQL** na górnym pasku.
![Omówienie obszaru roboczego Synapse z czerwonym polem wokół polecenia, aby utworzyć nową pulę SQL.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Na karcie **Podstawy** wprowadź następujące szczegóły:

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli SQL** | Dowolna prawidłowa nazwa | Nazwa puli SQL. |
    | **Poziom wydajności** | DW100c | Ustaw najmniejszy rozmiar, aby obniżyć koszty tego przewodnika Szybki start |
    ||||
  
    ![Pula SQL tworzenie przepływu - podstawy kartę.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dla nazw, które pule SQL można użyć. Nazwy nie mogą zawierać znaków specjalnych, muszą zawierać 15 lub mniej znaków, nie zawierają słów zastrzeżonych i być unikatowe w obszarze roboczym.

3. Kliknij **przycisk Dalej: Dodatkowe ustawienia**.
4. Wybierz **opcję Brak,** aby aprowizować pulę SQL bez danych. Pozostaw zaznaczone domyślne sortowanie.
![Przepływ tworzenia puli SQL — karta ustawień dodatkowych.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Kliknij pozycję **Przegląd + utwórz**.
6. Upewnij się, że szczegóły wyglądają poprawnie na podstawie tego, co zostało wcześniej wprowadzone, a następnie kliknij przycisk **Utwórz**.
![Pula SQL tworzenie przepływu — przegląd ustawienia kartę.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. W tym momencie rozpocznie się przepływ inicjowania obsługi administracyjnej zasobów.
 ![Przepływ tworzenia puli SQL — inicjowanie obsługi administracyjnej zasobów.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Po zakończeniu inicjowania obsługi administracyjnej przejście z powrotem do obszaru roboczego spowoduje wyświetlenie nowego wpisu dla nowo utworzonej puli SQL.
 ![Przepływ tworzenia puli SQL — inicjowanie obsługi administracyjnej zasobów.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wykonaj poniższe czynności, aby usunąć pulę SQL z obszaru roboczego.
> [!WARNING]
> Usunięcie puli SQL spowoduje usunięcie aparatu analizy i danych przechowywanych w bazie danych usuniętej puli SQL z obszaru roboczego. Nie będzie już można połączyć się z pulą SQL, a wszystkie kwerendy, potoki i notesy, które odczytują lub zapisują w tej puli SQL, nie będą już działać.

Jeśli chcesz usunąć pulę SQL, wykonaj następujące kroki:

1. Przejdź do bloku pul SQL w bloku obszaru roboczego
1. Wybierz pulę SQL do usunięcia (w tym przypadku **contosoedw)**
1. Zaznacz go i naciśnij klawisz **delete**.
1. Potwierdź usunięcie i naciśnij przycisk **Usuń.**
 ![Omówienie puli SQL — wyróżnianie potwierdzenia usuwania.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Po pomyślnym zakończeniu procesu pula SQL nie będzie już wyświetlana w zasobach obszaru roboczego.

Po utworzeniu puli SQL będzie ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytu z jeziora itp.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki start: Tworzenie puli platformy Spark Apache w Synapse Studio przy użyciu narzędzi internetowych](spark/apache-spark-notebook-create-spark-use-sql.md).
- Zobacz [Szybki start: Tworzenie puli platformy Spark apache przy użyciu portalu Azure](quickstart-create-apache-spark-pool.md).
