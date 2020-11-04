---
title: Monitorowanie aplikacji Apache Spark przy użyciu programu Synapse Studio
description: Monitoruj Apache Spark aplikacje za pomocą programu Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16b37258d922db59f520d4e30c45773f6d1108cf
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341074"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Monitorowanie aplikacji Apache Spark przy użyciu programu Synapse Studio

Za pomocą usługi Azure Synapse Analytics możesz używać Apache Spark do uruchamiania notesów, zadań i innych rodzajów aplikacji w pulach Apache Spark w Twoim obszarze roboczym.

W tym artykule wyjaśniono, jak monitorować aplikacje Apache Spark, co pozwala na śledzenie najnowszego stanu, problemów i postępu.

Ten samouczek obejmuje następujące zadania:

* Monitoruj uruchomioną aplikację Apache Spark
* Wyświetl ukończoną aplikację Apache Spark
* Wyświetl anulowaną aplikację Apache Spark
* Debugowanie nie powiodło się Apache Spark aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania:

- Obszar roboczy programu Synapse Studio. Aby uzyskać instrukcje, zobacz temat [Tworzenie obszaru roboczego programu Synapse Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Pula Apache Spark.

## <a name="view-apache-spark-applications"></a>Wyświetl Apache Spark aplikacje 
Wszystkie Apache Spark aplikacje można wyświetlić z poziomu **Monitor**  ->  **aplikacji monitorowanie Apache Spark**.
   ![aplikacje platformy Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Wyświetl ukończoną aplikację Apache Spark

Otwórz **monitor** , a następnie wybierz pozycję **Apache Spark aplikacje**. Aby wyświetlić szczegółowe informacje o ukończonych aplikacjach Apache Spark, wybierz aplikację Apache Spark i Wyświetl szczegóły.

  ![Wybierz zadanie zakończone](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Sprawdź **ukończone zadania** , **stan** i **łączny czas trwania**.

2. Odśwież zapytanie dziennika.

3. Otwórz łącze serwer historii Apache Spark, klikając opcję **serwer historii platformy Spark**.

4. Sprawdź informacje **podsumowujące** .

5. Sprawdź **dzienniki**. Możesz wybrać inny typ dzienników z listy rozwijanej, a następnie pobrać informacje dziennika, klikając pozycję **Pobierz dzienniki** , a następnie zaznacz pole wyboru **filtru błędy i ostrzeżenia** , aby odfiltrować wymagane błędy i ostrzeżenia.

6. Przegląd zadania można zobaczyć na wykresie wygenerowanym zadania. Domyślnie wykres przedstawia wszystkie zadania. Ten widok można filtrować według **identyfikatora zadania**.

7. Domyślnie jest wyświetlany ekran **postępu** . Przepływ danych można sprawdzić, wybierając pozycję **postęp** / **Read** / **zapisywana** / **czas trwania** na liście rozwijanej **wyświetlania** .

8. Aby odtworzyć zadanie, kliknij przycisk **Odtwórz** . Aby zatrzymać, możesz w dowolnym momencie kliknąć przycisk **Zatrzymaj** .

9. Użyj przewijania lub paska przewijania myszy, aby powiększyć i pomniejszyć wykres zadania, możesz również wybrać opcję **powiększenie, aby dopasować** ją do ekranu.

10. W węźle grafu zadania są wyświetlane następujące informacje dotyczące poszczególnych etapów:

    * #C1.

    * Nazwa lub opis.

    * Łączny numer zadania.

    * Odczytane dane: suma rozmiaru danych wejściowych i rozmiaru losowego odczytu.

    * Zapis danych: suma rozmiaru wyjściowego i rozmiaru losowego zapisu.

    * Czas wykonywania: czas między godziną rozpoczęcia pierwszej próby i czas zakończenia ostatniej próby.

    * Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, losowego odczytywania rekordów i losowego zapisu rekordów.

    * Wykonywane.

     ![Wyświetl ukończone zadanie](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Kliknij pozycję **Wyświetl szczegóły** na grafie, a następnie Pokaż szczegóły etapu.

    ![szczegóły etapu](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Monitoruj uruchomioną aplikację Apache Spark

Otwórz **monitor** , a następnie wybierz pozycję **Apache Spark aplikacje**. Aby wyświetlić szczegóły dotyczące Apache Spark działających aplikacji, wybierz aplikację do przesyłania Apache Spark i Wyświetl szczegóły. Jeśli aplikacja Apache Spark nadal działa, można monitorować postęp.

   ![Wybierz uruchomione zadanie](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Sprawdź **ukończone zadania** , **stan** i **łączny czas trwania**.

2. **Anuluj** aplikację Apache Spark.

3. **Odśwież** Zapytanie dziennika.

4. Kliknij przycisk **interfejsu użytkownika Spark** , aby przejść do strony zadań platformy Spark.

5. Wyświetl wykres. Przegląd zadania można zobaczyć na wykresie wygenerowanym zadania. Zapoznaj się z tematem krok 6, 7, 8, 9, 10 [Zakończono Apache Spark aplikacji](#view-completed-apache-spark-application).

6. Sprawdź informacje **podsumowujące** .

7. Sprawdź diagnostykę na karcie **Diagnostyka** .

8. Sprawdź **dzienniki** na tej karcie. Możesz wybrać inny typ dzienników z listy rozwijanej, a następnie pobrać informacje dziennika, klikając pozycję **Pobierz dzienniki** , a następnie zaznacz pole wyboru **filtru błędy i ostrzeżenia** , aby odfiltrować wymagane błędy i ostrzeżenia.

    ![Wyświetl uruchomione zadanie](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Wyświetl anulowaną aplikację Apache Spark

Otwórz **monitor** , a następnie wybierz pozycję **Apache Spark aplikacje**. Aby wyświetlić szczegóły dotyczące anulowanych aplikacji Apache Spark, wybierz aplikację Apache Spark i Wyświetl szczegóły.

 ![Wybierz zadanie anulowane](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Sprawdź **ukończone zadania** , **stan** i **łączny czas trwania**.

2. Odśwież zapytanie dziennika.

3. Otwórz łącze serwer historii Apache, klikając pozycję **serwer historii platformy Spark**.

4. Wyświetl wykres. Przegląd zadania można zobaczyć na wykresie wygenerowanym zadania. Zapoznaj się z tematem krok 6, 7, 8, 9, 10 [Zakończono Apache Spark aplikacji](#view-completed-apache-spark-application).

5. Sprawdź informacje **podsumowujące** .

6. Sprawdź **dzienniki**. Możesz wybrać inny typ dzienników z listy rozwijanej, a następnie pobrać informacje dziennika, klikając pozycję **Pobierz** dzienniki, a następnie zaznacz pole wyboru **filtru błędy i ostrzeżenia** , aby odfiltrować wymagane błędy i ostrzeżenia.

7. Kliknij pozycję **Wyświetl szczegóły** na grafie, a następnie Pokaż szczegóły etapu.

   ![Wyświetl anulowane zadanie](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Debugowanie nie powiodło się Apache Spark aplikacji

Otwórz **monitor** , a następnie wybierz pozycję **Apache Spark aplikacje**. Aby wyświetlić szczegóły dotyczące nieudanych aplikacji Apache Spark, wybierz aplikację Apache Spark i Wyświetl szczegóły.

![Wybierz zadanie zakończone niepowodzeniem](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Sprawdź **ukończone zadania** , **stan** i **łączny czas trwania**.

2. Odśwież zapytanie dziennika.

3. Otwórz łącze serwer historii Apache Spark, klikając opcję **serwer historii platformy Spark**.

4. Wyświetl wykres. Przegląd zadania można zobaczyć na wykresie wygenerowanym zadania. Zapoznaj się z artykułem krok 6, 7, 8, 9, 10 [Zakończono Apache Spark aplikacji](#view-completed-apache-spark-application)

5. Sprawdź informacje **podsumowujące** .

6. Sprawdź informacje o błędzie.

   ![Informacje o zadaniu zakończonym niepowodzeniem](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz artykuł [monitorowanie potoków przy użyciu programu Synapse Studio](how-to-monitor-pipeline-runs.md) .  
