---
title: Szybki Start — Tworzenie puli SQL Synapse
description: Utwórz nową pulę SQL Synapse dla obszaru roboczego Synapse usługi Azure Synapse Analytics, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a50f203b8057dd4ea51eff1cf09fc97fb598cb3e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096439"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Szybki Start: Tworzenie puli SQL Synapse

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie, analizowanie i obsługiwanie danych. Pula SQL oferuje możliwości obliczeniowe i magazynowe oparte na języku T-SQL. Po utworzeniu puli SQL w obszarze roboczym Synapse dane mogą być ładowane, modelowane, przetwarzane i obsługiwane w celu uzyskania szczegółowych informacji.

W tym przewodniku szybki start dowiesz się, jak utworzyć pulę SQL w obszarze roboczym Synapse przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

[Obszar roboczy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Utwórz nową pulę SQL

1. W obszarze roboczym Synapse, w którym chcesz utworzyć pulę SQL, kliknij polecenie **Nowa pula SQL** na górnym pasku.
![Omówienie obszaru roboczego Synapse z czerwonym prostokątem wokół polecenia tworzenia nowej puli SQL.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Wprowadź następujące szczegóły na karcie **podstawowe** :

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli SQL** | Dowolna prawidłowa nazwa | Nazwa puli SQL. |
    | **Poziom wydajności** | DW100c | Ustaw najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |
    ||||
  
    ![Karta podstawowe informacje o usłudze SQL Pool.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, które mogą być używane przez pule SQL. Nazwy nie mogą zawierać znaków specjalnych, muszą składać się z co najwyżej 15 znaków, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

3. Kliknij przycisk **Dalej: Ustawienia dodatkowe**.
4. Wybierz opcję **Brak** , aby zainicjować obsługę administracyjną puli SQL bez danych. Pozostaw wybrane domyślne sortowanie.
![Utwórz przepływ puli SQL — karta Ustawienia dodatkowe.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Kliknij pozycję **Przegląd + utwórz**.
6. Upewnij się, że szczegóły są poprawne na podstawie wprowadzonych wcześniej informacji, a następnie kliknij przycisk **Utwórz**.
![Utwórz przepływ w puli SQL — Przejrzyj kartę Ustawienia.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. W tym momencie przepływ aprowizacji zasobów zostanie uruchomiony.
 ![Przepływ tworzenia puli SQL — Inicjowanie obsługi zasobów.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Po zakończeniu aprowizacji przechodzenie z powrotem do obszaru roboczego będzie zawierać nowy wpis dla nowo utworzonej puli SQL.
 ![Przepływ tworzenia puli SQL — Inicjowanie obsługi zasobów.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wykonaj poniższe kroki, aby usunąć pulę SQL z obszaru roboczego.
> [!WARNING]
> Usunięcie puli SQL spowoduje usunięcie aparatu analitycznego i danych przechowywanych w bazie danych usuniętej puli SQL z obszaru roboczego. Nie będzie już możliwe nawiązywanie połączenia z pulą SQL, a wszystkie zapytania, potoki i notesy, które odczytują lub zapisują do tej puli SQL, nie będą już działać.

Jeśli chcesz usunąć pulę SQL, wykonaj następujące czynności:

1. Przejdź do bloku pule SQL w bloku obszaru roboczego
1. Wybierz pulę SQL do usunięcia (w tym przypadku **contosoedw**)
1. Zaznacz ją i naciśnij klawisz **delete**.
1. Potwierdź usunięcie i naciśnij przycisk **Usuń** .
 ![Przegląd puli SQL — wyróżnianie potwierdzenia usunięcia.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Po pomyślnym zakończeniu procesu Pula SQL nie będzie już wyświetlana w obszarze zasoby obszaru roboczego.

Po utworzeniu puli SQL będzie ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytywania z usługi Lake itp.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki Start: Tworzenie puli Apache Spark w programie Synapse Studio przy użyciu narzędzi sieci Web](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie puli Apache Spark przy użyciu Azure Portal](quickstart-create-apache-spark-pool.md).
