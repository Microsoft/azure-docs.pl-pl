---
title: 'Szybki Start: Tworzenie puli Apache Spark bezserwerowej (wersja zapoznawcza) przy użyciu programu Synapse Studio'
description: Utwórz bezserwerową pulę Apache Spark przy użyciu programu Synapse Studio, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 313ad0c620fe06158e96c208ae265702134b58d1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324183"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-preview-using-synapse-studio"></a>Szybki Start: Tworzenie puli Apache Spark bezserwerowej (wersja zapoznawcza) przy użyciu programu Synapse Studio

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie, analizowanie i obsługiwanie danych. Pula Apache Spark oferuje możliwości obliczeniowe Big Data dla Open Source. Po utworzeniu puli Apache Spark w obszarze roboczym Synapse dane mogą być ładowane, modelowane, przetwarzane i obsługiwane w celu uzyskania szczegółowych informacji.  

W tym przewodniku szybki start opisano kroki tworzenia puli Apache Spark w obszarze roboczym Synapse przy użyciu programu Synapse Studio.

> [!IMPORTANT]
> Opłaty za wystąpienia platformy Spark są naliczane proporcjonalnie za minutę, niezależnie od tego, czy są one używane. Upewnij się, że wystąpienie platformy Spark zostało zamknięte po zakończeniu korzystania z niego, lub ustaw krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Obszar roboczy Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Przejdź do obszaru roboczego Synapse

1. Przejdź do obszaru roboczego Synapse, w którym zostanie utworzona Pula Apache Spark, wpisując nazwę usługi (lub nazwę zasobu bezpośrednio) na pasku wyszukiwania.

    ![Azure Portal pasku wyszukiwania z obszarami roboczymi Synapse, które zostały wpisane.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Z listy obszarów roboczych wpisz nazwę (lub część nazwy) obszaru roboczego, który ma zostać otwarty. W tym przykładzie użyjemy obszaru roboczego o nazwie **contosoanalytics**.

    ![Lista przefiltrowanych obszarów roboczych Synapse, które zawierają nazwę contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Uruchamianie programu Synapse Studio 

W obszarze roboczym przegląd wybierz **adres URL sieci Web obszaru roboczego** , aby otworzyć program Synapse Studio.

![Azure Portal Omówienie obszaru roboczego Synapse z wyróżnioną opcją Uruchom Synapse Studio.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Tworzenie puli Apache Spark w programie Synapse Studio

1. Na stronie głównej programu Synapse Studio przejdź do **centrum zarządzania** w lewym okienku nawigacji, wybierając ikonę **Zarządzaj** .

    ![Strona główna programu Synapse Studio z wyróżnioną sekcją Centrum zarządzania.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Raz w centrum zarządzania przejdź do sekcji **pule Apache Spark** , aby wyświetlić bieżącą listę pul Apache Spark dostępnych w obszarze roboczym.
    
    ![Synapse Studio Management Hub z wybraną nawigacją pul Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Wybierz pozycję **+ Nowy** i zostanie wyświetlony Kreator tworzenia nowej puli Apache Spark. 

1. Wprowadź następujące szczegóły na karcie **podstawowe** :

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli Apache Spark** | contosospark | Jest to nazwa, która będzie miała pulę Apache Spark. |
    | **Rozmiar węzła** | Mały (4 vCPU/32 GB) | Ustaw wartość na najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |
    | **Automatyczne skalowanie** | Disabled | W tym przewodniku Szybki Start nie będziemy potrzebować automatycznego skalowania |
    | **Liczba węzłów** | 8 | Aby ograniczyć koszty w tym przewodniku Szybki Start, Użyj małego rozmiaru|
       
    ![Podstawowe informacje dotyczące Synapse nowej puli Apache Spark Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, których mogą używać pule Apache Spark. Nazwy muszą zawierać tylko litery lub cyfry, muszą zawierać co najmniej 15 znaków, muszą zaczynać się literą, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

1. Na następnej karcie (Ustawienia dodatkowe) pozostaw wszystkie ustawienia jako domyślne.

1. Nie będziemy teraz dodawać żadnych tagów, więc wybierz pozycję **Recenzja + Utwórz**.

1. Na karcie **Recenzja + tworzenie** upewnij się, że szczegóły są poprawne na podstawie wprowadzonych wcześniej elementów, a następnie naciśnij pozycję **Utwórz**. 

    ![Utwórz nową pulę Apache Spark w programie Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Pula Apache Spark rozpocznie proces aprowizacji.

1. Po zakończeniu aprowizacji Nowa pula Apache Spark zostanie wyświetlona na liście.
    
    ![Lista nowej puli Apache Spark Synapse Studio](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Czyszczenie zasobów puli Apache Spark przy użyciu programu Synapse Studio

Wykonaj poniższe kroki, aby usunąć pulę Apache Spark z obszaru roboczego przy użyciu programu Synapse Studio.
> [!WARNING]
> Usunięcie puli platformy Spark spowoduje usunięcie aparatu analitycznego z obszaru roboczego. Nie będzie już możliwe nawiązywanie połączenia z pulą, a wszystkie zapytania, potoki i notesy korzystające z tej puli platformy Spark przestaną działać.

Jeśli chcesz usunąć pulę Apache Spark, wykonaj następujące czynności:

1. Przejdź do pul Apache Spark w centrum zarządzania w programie Synapse Studio.
1. Wybierz wielokropek obok puli Apache do usunięcia (w tym przypadku **contosospark** ), aby wyświetlić polecenia dla puli Apache Spark.

    ![Lista pul Apache Spark, dla których wybrano ostatnio utworzoną pulę.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Naciśnij klawisz **delete**.
1. Potwierdź usunięcie i naciśnij przycisk **Usuń** .
1. Po pomyślnym zakończeniu procesu Pula Apache Spark nie będzie już wyświetlana w obszarze zasoby obszaru roboczego. 

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki Start: Tworzenie puli Apache Spark w programie Synapse Studio przy użyciu narzędzi sieci Web](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie puli Apache Spark przy użyciu Azure Portal](quickstart-create-apache-spark-pool-portal.md).
