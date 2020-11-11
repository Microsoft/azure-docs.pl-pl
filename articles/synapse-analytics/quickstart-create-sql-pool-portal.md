---
title: 'Szybki Start: Tworzenie dedykowanej puli SQL (wersja zapoznawcza) przy użyciu Azure Portal'
description: Utwórz nową dedykowaną pulę SQL przy użyciu Azure Portal, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 2230784eedd044acd95b3087793ba664b544f76a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505074"
---
# <a name="quickstart-create-a-dedicated-sql-pool-preview-using-the-azure-portal"></a>Szybki Start: Tworzenie dedykowanej puli SQL (wersja zapoznawcza) przy użyciu Azure Portal

Usługa Azure Synapse Analytics oferuje różne aparaty analityczne, które ułatwiają pozyskiwanie, przekształcanie, modelowanie i analizowanie danych. Dedykowana Pula SQL oferuje oparte na języku T-SQL możliwości obliczeniowe i magazynowe. Po utworzeniu dedykowanej puli SQL w obszarze roboczym usługi Synapse dane można ładować, modelować, przetwarzać i dostarczać w celu szybszego wglądu w informacje analityczne.

W tym przewodniku szybki start dowiesz się, jak utworzyć dedykowaną pulę SQL w obszarze roboczym Synapse przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Obszar roboczy Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="navigate-to-the-synapse-workspace"></a>Przejdź do obszaru roboczego Synapse

1. Przejdź do obszaru roboczego Synapse, w którym zostanie utworzona dedykowana Pula SQL, wpisując nazwę usługi (lub nazwę zasobu bezpośrednio) na pasku wyszukiwania.
![Azure Portal pasek wyszukiwania z obszarami roboczymi Synapse wpisanych w. ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) . 
1. Z listy obszarów roboczych wpisz nazwę (lub część nazwy) obszaru roboczego, który ma zostać otwarty. W tym przykładzie użyjemy obszaru roboczego o nazwie **contosoanalytics**.
![Lista przefiltrowanych obszarów roboczych Synapse, które zawierają nazwę contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>Utwórz nową dedykowaną pulę SQL

1. W obszarze roboczym Synapse, w którym chcesz utworzyć dedykowaną pulę SQL, wybierz polecenie **Nowe dedykowane pule SQL** na górnym pasku.
![Omówienie obszaru roboczego Synapse z czerwonym prostokątem wokół polecenia w celu utworzenia nowej dedykowanej puli SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Wprowadź następujące szczegóły na karcie **podstawowe** :

    | Ustawienie | Sugerowana wartość | Opis |
    | :------ | :-------------- | :---------- |
    | **Nazwa dedykowanej puli SQL** | Dowolna prawidłowa nazwa | Nazwa dedykowanej puli SQL. |
    | **Poziom wydajności** | DW100c | Ustaw najmniejszy rozmiar, aby zmniejszyć koszty dla tego przewodnika Szybki Start |

  
    ![Dedykowana Pula SQL CREATE Flow — podstawowe karty.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > Należy zauważyć, że istnieją określone ograniczenia dotyczące nazw, które mogą być używane przez dedykowane pule SQL. Nazwy nie mogą zawierać znaków specjalnych, muszą składać się z co najwyżej 15 znaków, nie zawierać słów zarezerwowanych i być unikatowe w obszarze roboczym.

3. Wybierz pozycję **Dalej: Ustawienia dodatkowe**.
4. Wybierz opcję **Brak** , aby zainicjować obsługę dedykowanej puli SQL bez danych. Pozostaw wybrane domyślne sortowanie.

    Jeśli chcesz przywrócić dedykowaną pulę SQL z punktu przywracania, wybierz pozycję **punkt przywracania**. Aby uzyskać więcej informacji na temat przeprowadzania przywracania, zobacz [How to: Restore a existd SQL Pool](backuprestore/restore-sql-pool.md)

![dedykowany przepływ tworzenia puli SQL — karta Ustawienia dodatkowe.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Wybierz pozycję **Przejrzyj i utwórz**.
6. Upewnij się, że szczegóły są poprawne na podstawie wprowadzonych wcześniej informacji. Wybierz przycisk **Utwórz**.
![dedykowana Pula SQL CREATE Flow — przegląd ustawień.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. W tym momencie przepływ aprowizacji zasobów zostanie uruchomiony.
 ![Zrzut ekranu przedstawiający stronę "wdrożenie zostało zakończone".](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Po zakończeniu aprowizacji przechodzenie z powrotem do obszaru roboczego spowoduje wyświetlenie nowego wpisu dla nowo utworzonej dedykowanej puli SQL.
 ![Przepływ tworzenia puli SQL — Inicjowanie obsługi zasobów.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)



Po utworzeniu dedykowanej puli SQL będzie ona dostępna w obszarze roboczym do ładowania danych, przetwarzania strumieni, odczytywania z usługi Lake itp.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj poniższe kroki, aby usunąć dedykowaną pulę SQL z obszaru roboczego.
> [!WARNING]
> Usunięcie dedykowanej puli SQL spowoduje usunięcie aparatu analitycznego i danych przechowywanych w bazie danych usuniętej dedykowanej puli SQL z obszaru roboczego. Nie będzie już możliwe nawiązywanie połączenia z dedykowaną pulą SQL, a wszystkie zapytania, potoki i notesy, które odczytują lub zapisują do tej dedykowanej puli SQL, nie będą już działać.

Jeśli chcesz usunąć dedykowaną pulę SQL, wykonaj następujące czynności:

1. Przejdź do bloku pule SQL w bloku obszaru roboczego
1. Wybierz dedykowaną pulę SQL do usunięcia (w tym przypadku **contosowdw** )
1. Po wybraniu naciśnij klawisz **delete** .
1. Potwierdzenie usunięcia i naciśnięcie przycisku **Usuń** ![ dedykowanej puli SQL — wyróżnianie potwierdzenia usunięcia.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Gdy proces zakończy się pomyślnie, dedykowana Pula SQL nie będzie już wyświetlana w obszarze zasoby obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Szybki Start: Tworzenie puli Apache Spark bezserwerowej w programie Synapse Studio przy użyciu narzędzi sieci Web](quickstart-apache-spark-notebook.md).
- Zobacz [Szybki Start: Tworzenie puli Apache Spark bezserwerowej przy użyciu Azure Portal](quickstart-create-apache-spark-pool-portal.md).
