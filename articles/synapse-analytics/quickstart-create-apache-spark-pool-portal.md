---
title: 'Szybki Start: Tworzenie puli Apache Spark bezserwerowej przy użyciu Azure Portal'
description: Utwórz bezserwerową pulę Apache Spark przy użyciu Azure Portal, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461599"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Szybki Start: Tworzenie nowej puli Apache Spark bezserwerowej przy użyciu Azure Portal

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie, analizowanie i dystrybuowanie danych. Pula Apache Spark zapewnia możliwości obliczeniowe Big Data dla programu Open Source. Po utworzeniu puli Apache Spark w obszarze roboczym usługi Synapse dane można ładować, modelować, przetwarzać i rozpowszechniać w celu uzyskania szybszych szczegółowych informacji analitycznych.

W tym przewodniku szybki start dowiesz się, jak za pomocą Azure Portal utworzyć pulę Apache Spark w obszarze roboczym Synapse.

> [!IMPORTANT]
> Opłaty za wystąpienia platformy Spark są naliczane proporcjonalnie za minutę, niezależnie od tego, czy są one używane. Upewnij się, że wystąpienie platformy Spark zostało zamknięte po zakończeniu korzystania z niego, lub ustaw krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Obszar roboczy analizy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Przejdź do obszaru roboczego Synapse 
1. Przejdź do obszaru roboczego Synapse, w którym zostanie utworzona Pula Apache Spark, wpisując nazwę usługi (lub nazwę zasobu bezpośrednio) na pasku wyszukiwania.
![Azure Portal pasku wyszukiwania z obszarami roboczymi Synapse, które zostały wpisane.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Z listy obszarów roboczych wpisz nazwę (lub część nazwy) obszaru roboczego, który ma zostać otwarty. W tym przykładzie użyjemy obszaru roboczego o nazwie **contosoanalytics**.
![Lista przefiltrowanych obszarów roboczych Synapse, które zawierają nazwę contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Utwórz nową pulę Apache Spark

1. W obszarze roboczym Synapse, w którym chcesz utworzyć pulę Apache Spark wybierz pozycję **Nowa pula Apache Spark**.
    ![Omówienie obszaru roboczego Synapse z czerwonym prostokątem wokół polecenia w celu utworzenia nowej puli Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Wprowadź następujące szczegóły na karcie **podstawowe** :

    |Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli Apache Spark** | Prawidłowa nazwa puli | Jest to nazwa, która będzie miała pulę Apache Spark. |
    | **Rozmiar węzła** | Mały (4 vCPU/32 GB) | Ustaw wartość na najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |
    | **Automatyczne skalowanie** | Disabled | Nie potrzebujemy automatycznego skalowania dla tego przewodnika Szybki Start |
    | **Liczba węzłów** | 5 | Aby ograniczyć koszty dla tego przewodnika Szybki Start, Użyj małego rozmiaru |


    ![Pula Apache Spark Create Flow — podstawowe karty.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, których mogą używać pule Apache Spark. Nazwy muszą zawierać tylko litery lub cyfry, muszą zawierać co najmniej 15 znaków, muszą zaczynać się literą, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

3. Wybierz pozycję **Dalej: Ustawienia dodatkowe** i przejrzyj ustawienia domyślne. Nie należy modyfikować żadnych ustawień domyślnych.
    ![Zrzut ekranu przedstawiający stronę "Tworzenie puli Apache Spark" z wybraną kartą "Ustawienia dodatkowe".](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Wybierz pozycję **Dalej: Tagi**. Nie dodawaj żadnych tagów.
    ![Utwórz przepływ Apache Spark puli — karta Ustawienia dodatkowe.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Wybierz pozycję **Przeglądanie + tworzenie**.

6. Upewnij się, że szczegóły są poprawne w zależności od tego, co zostało wcześniej wprowadzone, i wybierz pozycję **Utwórz**.
    ![Tworzenie przepływu Apache Spark puli — przegląd ustawień.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. W tym momencie przepływ aprowizacji zasobów zostanie uruchomiony, co oznacza, że zostanie on ukończony.
    ![Zrzut ekranu pokazujący stronę "przegląd" z wyświetlonym komunikatem "wdrożenie zostało zakończone".](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Po zakończeniu aprowizacji przechodzenie z powrotem do obszaru roboczego będzie zawierać nowy wpis dla nowo utworzonej puli Apache Spark.
    ![Tworzenie przepływu Apache Spark puli — Inicjowanie obsługi zasobów.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. W tym momencie nie ma żadnych zasobów uruchomionych, nie są naliczane opłaty za usługę Spark, utworzono metadane dotyczące wystąpień platformy Spark, które chcesz utworzyć.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj poniższe kroki, aby usunąć pulę Apache Spark z obszaru roboczego.
> [!WARNING]
> Usunięcie puli Apache Spark spowoduje usunięcie aparatu analitycznego z obszaru roboczego. Nie będzie już możliwe nawiązywanie połączenia z pulą, a wszystkie zapytania, potoki i notesy korzystające z tej puli Apache Spark nie będą już działać.

Jeśli chcesz usunąć pulę Apache Spark, wykonaj następujące czynności:

1. Przejdź do bloku pule Apache Spark w obszarze roboczym.
2. Wybierz pulę Apache Spark do usunięcia (w tym przypadku **contosospark**).
3. Naciśnij klawisz **delete**.
 ![Lista pul Apache Spark, dla których wybrano ostatnio utworzoną pulę.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Potwierdź usunięcie i naciśnij przycisk **Usuń** .
 ![Okno dialogowe potwierdzenia usuwania wybranej puli Apache Spark.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Po pomyślnym zakończeniu procesu Pula Apache Spark nie będzie już wyświetlana w obszarze zasoby obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki Start: Tworzenie notesu Apache Spark](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie dedykowanej puli SQL przy użyciu Azure Portal](quickstart-create-sql-pool-portal.md).
