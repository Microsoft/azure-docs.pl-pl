---
title: Zarządzanie bibliotekami Scala & Java dla Apache Spark
description: Dowiedz się, jak dodawać biblioteki Scala i Java w usłudze Azure Synapse Analytics oraz zarządzać nimi.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098710"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie pakietami Scala i Java dla Apache Spark w usłudze Azure Synapse Analytics

Biblioteki zapewniają kod wielokrotnego użytku, który może być dołączany do programów lub projektów. 

Może być konieczne zaktualizowanie środowiska puli Apache Spark bezserwerowej z kilku powodów. Na przykład może się okazać, że:
- jedno z podstawowych zależności wydano nową wersję.
- potrzebujesz dodatkowego pakietu do uczenia modelu uczenia maszynowego lub przygotowywania danych.
- znaleziono lepszy pakiet i nie jest już potrzebny starszy pakiet.

Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, możesz zainstalować bibliotekę na jednej z Apache Spark pul lub sesji notesu. W tym artykule omówiono sposób zarządzania pakietami Scala i Java.

## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w usłudze Azure Synapse Analytics zawiera pełen zestaw bibliotek dla typowych zadań dotyczących inżynierii danych, przygotowywania danych, uczenia maszynowego i wizualizacji danych. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md). 

Po uruchomieniu wystąpienia platformy Spark te biblioteki zostaną automatycznie uwzględnione. Dodatkowe pakiety Scala/Java można dodać w puli platformy Spark i na poziomie sesji.

## <a name="workspace-packages"></a>Pakiety obszaru roboczego
Pakiety obszaru roboczego mogą być niestandardowymi lub prywatnymi plikami jar. Te pakiety można przekazać do obszaru roboczego, a następnie przypisać je do określonej puli platformy Spark.

Aby dodać pakiety obszaru roboczego:
1. Przejdź do karty **Zarządzanie**  >  **pakietami obszarów roboczych** .
2. Przekaż pliki jar przy użyciu selektora plików.
3. Po przekazaniu plików do obszaru roboczego usługi Azure Synapse można dodać te pliki jar do danej puli Apache Spark.

![Zrzut ekranu, który wyróżnia pakiety obszaru roboczego.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Wyświetl pakiety obszaru roboczego")

## <a name="pool-libraries"></a>Biblioteki puli
Po zidentyfikowaniu pakietów Scala i Java, które mają być używane dla aplikacji platformy Spark, można je zainstalować w puli platformy Spark. Biblioteki na poziomie puli są dostępne dla wszystkich notesów i zadań uruchomionych w tej puli.

Biblioteki puli platformy Spark można zaktualizować, przechodząc do witryny Azure Synapse Studio lub Azure Portal. W tym miejscu można wybrać biblioteki obszarów roboczych do zainstalowania. 

Po zapisaniu zmian zadanie Spark uruchomi instalację i buforuje wynikowe środowisko do późniejszego ponownego użycia. Po zakończeniu zadania nowe zadania platformy Spark lub sesje notesu będą używały zaktualizowanych bibliotek puli. 

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Zarządzanie pakietami z poziomu usługi Azure Synapse Studio lub Azure Portal
Bibliotekami puli platformy Spark można zarządzać przy użyciu usługi Azure Synapse Studio lub Azure Portal. 

Aby zaktualizować lub dodać biblioteki do puli platformy Spark:
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.

    Jeśli aktualizujesz **Azure Portal**:

    - W sekcji **zasoby Synapse** wybierz kartę **Pule Apache Spark** i wybierz z listy pulę platformy Spark.
     
    - Wybierz **pakiety** z sekcji **Ustawienia** w puli platformy Spark.
  
    ![Zrzut ekranu, który podświetla przycisk pliku konfiguracji środowiska przekazywania.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Dodaj biblioteki języka Python")
   
    Jeśli aktualizujesz **Synapse Studio**:
    - Wybierz pozycję **Zarządzaj** w głównym panelu nawigacyjnym, a następnie wybierz pozycję **Pule Apache Spark**.

    - Wybierz sekcję **pakiety** dla określonej puli platformy Spark.
    ![Zrzut ekranu, który wyróżnia opcję konfiguracji środowiska przekazywania z Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Dodawanie bibliotek języka Python z programu Studio")
   
2. Aby dodać pliki jar, przejdź do sekcji **pakiety obszaru roboczego** , aby dodać ją do puli. 
3. Po zapisaniu zmian zadanie systemowe zostanie wyzwolone w celu zainstalowania i przebuforowania określonych bibliotek. Ten proces ułatwia skrócenie czasu uruchamiania sesji. 
4. Po pomyślnym zakończeniu zadania wszystkie nowe sesje będą pobierać zaktualizowane biblioteki puli.

> [!IMPORTANT]
> Wybierając opcję **Wymuś nowe ustawienia**, zostaną zakończone wszystkie bieżące sesje dla wybranej puli platformy Spark. Po zakończeniu sesji należy poczekać na ponowne uruchomienie puli. 
>
> Jeśli to ustawienie nie jest zaznaczone, trzeba będzie poczekać na zakończenie bieżącej sesji platformy Spark lub zatrzymać ją ręcznie. Po zakończeniu sesji należy pozwolić na ponowne uruchomienie puli.

#### <a name="track-installation-progress-preview"></a>Śledź postęp instalacji (wersja zapoznawcza)
Zarezerwowane przez system zadanie Spark jest inicjowane za każdym razem, gdy pula jest aktualizowana przy użyciu nowego zestawu bibliotek. To zadanie platformy Spark pomaga monitorować stan instalacji biblioteki. Jeśli instalacja nie powiedzie się z powodu konfliktów biblioteki lub innych problemów, Pula platformy Spark powróci do stanu poprzedniego lub domyślnego. 

Ponadto użytkownicy mogą również sprawdzić dzienniki instalacji, aby zidentyfikować konflikty zależności lub zobaczyć, które biblioteki zostały zainstalowane podczas aktualizacji puli.

Aby wyświetlić te dzienniki:
1. Przejdź do listy aplikacji platformy Spark na karcie **monitorowanie** . 
2. Wybierz zadanie aplikacji system Spark, które odpowiada aktualizacji puli. Te zadania systemowe są uruchamiane w tytule *SystemReservedJob-LibraryManagement* .
   ![Zrzut ekranu, który podświetla zadanie zastrzeżonej biblioteki systemowej.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Wyświetl zadanie biblioteki systemowej")
3. Przełącz, aby wyświetlić **sterowniki** i dzienniki **stdout** . 
4. W wynikach zostaną wyświetlone dzienniki powiązane z instalacją pakietów.
    ![Zrzut ekranu przedstawiający wyniki zadań zarezerwowanych dla biblioteki systemowej.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Wyświetl postęp zadania biblioteki systemowej")

## <a name="session-scoped-libraries"></a>Biblioteki o zakresie sesji 
Oprócz bibliotek na poziomie puli można także określić biblioteki o zakresie sesji na początku sesji notesu.  Biblioteki o zakresie sesji umożliwiają określanie i używanie pakietów jar wyłącznie w ramach sesji notesu. 

W przypadku korzystania z bibliotek o zakresie sesji należy pamiętać o następujących kwestiach:
   - W przypadku instalowania bibliotek z zakresem sesji tylko bieżący Notes ma dostęp do określonych bibliotek. 
   - Te biblioteki nie wpłyną na inne sesje ani zadania korzystające z tej samej puli platformy Spark. 
   - Te biblioteki są instalowane w oparciu o podstawowe biblioteki środowiska uruchomieniowego i na poziomie puli. 
   - Biblioteki notesów będą mieć najwyższy priorytet.

Aby określić pakiety Java lub Scala z zakresem sesji, można użyć ```%%configure``` opcji:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Zalecamy uruchomienie konfiguracji%% na początku Twojego notesu. Aby uzyskać pełną listę prawidłowych parametrów, można zapoznać się z tym [dokumentem](https://github.com/cloudera/livy#request-body) .

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
- Rozwiązywanie problemów z błędami instalacji biblioteki: [Rozwiązywanie problemów z bibliotekami](apache-spark-troubleshoot-library-errors.md)
