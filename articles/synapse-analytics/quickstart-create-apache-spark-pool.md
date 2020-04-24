---
title: Szybki Start Tworzenie puli Apache Spark (wersja zapoznawcza)
description: Utwórz nową pulę Apache Spark dla obszaru roboczego usługi Azure Synapse Analytics, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0fd8a379927396f2ae44d74c2d968d48fbd039c1
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096371"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Szybki Start: Tworzenie nowej puli Apache Spark (wersja zapoznawcza)

Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie, analizowanie i obsługiwanie danych. Pula Apache Spark oferuje możliwości obliczeniowe Big Data dla programu Open Source. Po utworzeniu puli Apache Spark w obszarze roboczym Synapse dane mogą być ładowane, modelowane, przetwarzane i obsługiwane w celu uzyskania szczegółowych informacji.

W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć pulę Apache Spark w obszarze roboczym Synapse.

> [!IMPORTANT]
> Opłaty za wystąpienia platformy Spark są naliczane proporcjonalnie za minutę, niezależnie od tego, czy są one używane. Upewnij się, że wystąpienie platformy Spark zostało zamknięte po zakończeniu korzystania z niego, lub ustaw krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https:/azure.microsoft.com/free/)
- [Obszar roboczy analizy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure Portal](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Utwórz nową pulę Apache Spark

1. W obszarze roboczym Synapse, w którym chcesz utworzyć pulę Apache Spark, kliknij pozycję **Nowa pula Apache Spark**.
![Omówienie obszaru roboczego Synapse z czerwonym prostokątem wokół polecenia w celu utworzenia nowej puli Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Wprowadź następujące szczegóły na karcie **podstawowe** :

    |Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli Apache Spark** | Prawidłowa nazwa puli | Jest to nazwa, która będzie miała pulę Apache Spark. |
    | **Rozmiar węzła** | Mały (4 vCPU/32 GB) | Ustaw wartość na najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |
    | **Automatyczne skalowanie** | Enabled (Włączony) | Pozostaw to ustawienie domyślne |
    | **Liczba węzłów** | 3 - 40 | Pozostaw to ustawienie domyślne |
    ||||

    ![Pula Apache Spark Create Flow — podstawowe karty.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, których mogą używać pule Apache Spark. Nazwy muszą zawierać tylko litery lub cyfry, muszą zawierać co najmniej 15 znaków, muszą zaczynać się literą, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

3. Kliknij przycisk **Dalej: dodatkowe ustawienia** i przejrzyj ustawienia domyślne. Nie należy modyfikować żadnych ustawień domyślnych.
![Utwórz przepływ Apache Spark puli — karta Ustawienia dodatkowe.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Kliknij przycisk **Dalej: Tagi**. Nie dodawaj żadnych tagów.
![Utwórz przepływ Apache Spark puli — karta Ustawienia dodatkowe.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Kliknij pozycję **Przegląd + utwórz**.

6. Upewnij się, że szczegóły są poprawne na podstawie wprowadzonych wcześniej informacji, a następnie kliknij przycisk **Utwórz**.
![Tworzenie przepływu Apache Spark puli — przegląd ustawień.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. W tym momencie przepływ aprowizacji zasobów zostanie uruchomiony, co oznacza, że po zakończeniu ![tworzenia puli Apache Spark Utwórz przepływ.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Po zakończeniu aprowizacji przechodzenie z powrotem do obszaru roboczego będzie zawierać nowy wpis dla nowo utworzonej puli Apache Spark.
 ![Tworzenie przepływu Apache Spark puli — Inicjowanie obsługi zasobów.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. W tym momencie nie ma żadnych zasobów uruchomionych, nie są naliczane opłaty za usługę Spark, utworzono metadane dotyczące wystąpień platformy Spark, które chcesz utworzyć.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wykonaj poniższe kroki, aby usunąć pulę Apache Spark z obszaru roboczego.
> [!WARNING]
> Usunięcie puli Apache Spark spowoduje usunięcie aparatu analitycznego z obszaru roboczego. Nie będzie już możliwe nawiązywanie połączenia z pulą, a wszystkie zapytania, potoki i notesy korzystające z tej puli Apache Spark nie będą już działać.

Jeśli chcesz usunąć pulę Apache Spark, wykonaj następujące czynności:

1. Przejdź do bloku pule Apache Spark w obszarze roboczym.
2. Wybierz pulę Apache do usunięcia (w tym przypadku **contosospark**)
3. Naciśnij klawisz **delete**.
 ![Lista pul Apache Spark, dla których wybrano ostatnio utworzoną pulę.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Potwierdź usunięcie i naciśnij przycisk **Usuń** .
 ![Okno dialogowe potwierdzenia usuwania wybranej puli Apache Spark.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Po pomyślnym zakończeniu procesu Pula Apache Spark nie będzie już wyświetlana w obszarze zasoby obszaru roboczego.

Po utworzeniu puli SQL jest ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytywania z usługi Lake i tak.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki Start: Tworzenie notesu Apache Spark](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie puli Synapse SQL przy użyciu Azure Portal](quickstart-create-sql-pool.md).
