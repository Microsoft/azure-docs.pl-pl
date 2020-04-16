---
title: Szybki start Tworzenie puli platformy Spark Apache (wersja zapoznawcza)
description: Utwórz nową pulę platformy Spark apache dla obszaru roboczego usługi Azure Synapse Analytics, wykonując kroki opisane w tym przewodniku.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424433"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Szybki start: tworzenie nowej puli platformy Spark Apache (wersja zapoznawcza)

Synapse Analytics oferuje różne aparaty analityczne, które pomagają w pozyskiwania, przekształcaniu, modelowaniu, analizowaniu i obsłudze danych. Pula Apache Spark oferuje funkcje obliczeniowe dużych zbiorów danych typu open source. Po utworzeniu puli Platformy Spark Apache w obszarze roboczym Synapse dane mogą być ładowane, modelowane, przetwarzane i obsługiwane w celu uzyskania szczegółowych informacji.

W tym przewodniku Szybki start dowiesz się, jak utworzyć pulę platformy Spark usługi Apache w obszarze roboczym Synapse.

> [!IMPORTANT]
> Rozliczenia za wystąpienia platformy Spark są naliczane proporcjonalnie do liczby minut, niezależnie od tego, czy są używane, czy nie. Pamiętaj, aby zamknąć wystąpienie platformy Spark po zakończeniu korzystania z niego lub ustawić krótki limit czasu. Aby uzyskać więcej informacji, zobacz sekcję **Czyszczenie zasobów** w tym artykule.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto przed rozpoczęciem](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https:/azure.microsoft.com/free/)
- [Obszar roboczy Usługi Analizy Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Logowanie się do [witryny Azure portal](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Tworzenie nowej puli platformy Spark Apache

1. W obszarze roboczym Synapse, w którym chcesz utworzyć pulę Platformy Spark Apache, kliknij pozycję **Nowa platforma Iskrzna Apache**.
![Omówienie obszaru roboczego Synapse z czerwonym polem wokół polecenia, aby utworzyć nową pulę platformy Spark Apache](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Na karcie **Podstawy** wprowadź następujące szczegóły:

    |Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa puli Apache Spark** | Prawidłowa nazwa puli | Jest to nazwa, że Apache Spark puli będzie mieć. |
    | **Rozmiar węzła** | Mały (4 vCPU / 32 GB) | Ustaw ten rozmiar na najmniejszy rozmiar, aby obniżyć koszty tego przewodnika Szybki start |
    | **Automatyczne skalowanie** | Enabled (Włączony) | Pozostaw to ustawienie domyślne |
    | **Liczba węzłów** | 3 - 40 | Pozostaw to ustawienie domyślne |
    ||||

    ![Apache Spark puli tworzenia przepływu - podstawy kartę.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dla nazw, które pule Platformy Spark Apache można użyć. Nazwy muszą zawierać tylko litery lub cyfry, muszą mieć 15 lub mniej znaków, muszą zaczynać się od litery, nie zawierać słów zastrzeżonych i być unikatowe w obszarze roboczym.

3. Kliknij **przycisk Dalej: dodatkowe ustawienia** i przejrzyj ustawienia domyślne. Nie należy modyfikować żadnych ustawień domyślnych.
![Apache Spark puli utworzyć przepływ - dodatkowe ustawienia kartę.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Kliknij **przycisk Dalej: tagi**. Nie dodawaj żadnych tagów.
![Apache Spark puli utworzyć przepływ - dodatkowe ustawienia kartę.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Kliknij pozycję **Przegląd + utwórz**.

6. Upewnij się, że szczegóły wyglądają poprawnie na podstawie tego, co zostało wcześniej wprowadzone, a następnie kliknij przycisk **Utwórz**.
![Apache Spark puli tworzenia przepływu - przegląd ustawienia kartę.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. W tym momencie rozpocznie się przepływ inicjowania obsługi ![administracyjnej zasobów, wskazując po zakończeniu puli Apache Spark utworzyć przepływ — inicjowanie obsługi administracyjnej zasobów.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Po zakończeniu inicjowania obsługi administracyjnej przejście z powrotem do obszaru roboczego spowoduje wyświetlenie nowego wpisu dla nowo utworzonej puli platformy Spark apache.
 ![Pule platformy Spark Apache tworzą przepływ — inicjowanie obsługi administracyjnej zasobów.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. W tym momencie nie ma żadnych zasobów uruchomionych, żadnych opłat za spark, utworzono metadane dotyczące spark wystąpień, które chcesz utworzyć.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wykonaj poniższe czynności, aby usunąć pulę platformy Spark Apache z obszaru roboczego.
> [!WARNING]
> Usunięcie puli platformy Spark apache spowoduje usunięcie aparatu analitycznego z obszaru roboczego. Nie będzie już można połączyć się z pulą, a wszystkie zapytania, potoki i notesy korzystające z tej puli platformy Apache Spark nie będą już działać.

Jeśli chcesz usunąć pulę Platformy Spark Apache, wykonaj następujące czynności:

1. Przejdź do bloku Pukry Apache Spark w obszarze roboczym.
2. Wybierz pulę Apache do usunięcia (w tym przypadku **contosospark)**
3. Naciśnij **klawisz delete**.
 ![Lista pule Apache Spark z niedawno utworzoną pulą.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Potwierdź usunięcie i naciśnij przycisk **Usuń.**
 ![Okno dialogowe potwierdzenia, aby usunąć wybraną pulę platformy Spark Apache.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Po pomyślnym zakończeniu procesu pula platformy Spark apache nie będzie już wyświetlana w zasobach obszaru roboczego.

Po utworzeniu puli SQL jest ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytu z jeziora i tak.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki start: Tworzenie puli platformy Spark Apache w Synapse Studio przy użyciu narzędzi internetowych](spark/apache-spark-notebook-create-spark-use-sql.md).
- Zobacz [Szybki start: Tworzenie puli SQL Synapse przy użyciu portalu Azure](quickstart-create-sql-pool.md).
