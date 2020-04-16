---
title: Ściągawka - Azure Synapse Analytics
description: Przewodnik pomocniczy dotyczący użytkowników przechodzących przez usługę Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424657"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Arkusz cheatów usługi Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Arkusz ściągawki usługi Azure Synapse Analytics przeprowadzi Cię przez podstawowe pojęcia usługi i ważne polecenia. Ten artykuł jest przydatny zarówno dla nowych uczniów, jak i tych, którzy chcą najważniejsze tematy dotyczącą podstawowych tematów platformy Azure Synapse.

## <a name="architecture"></a>Architektura

> [!div class="mx-imgBorder"]
>![Architektura Synapse](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Pojęcia
| Rzeczowniki i czasowniki                         | Wyniki działania       |
|:---                                 |:---                 |
| **Obszar roboczy Synapse (wersja zapoznawcza)** | Zabezpiecza granica współpracy do wykonywania analizy przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLSg2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów. |
| **Analiza SQL**   | Uruchamiaj analizy za pomocą pul lub funkcji na żądanie.  |
| **Pula SQL**   | 0-to-N SQL aprowizacji zasobów z ich odpowiednich baz danych można wdrożyć w obszarze roboczym. Każda pula SQL ma skojarzoną bazę danych. Pula SQL może być skalowana, wstrzymana i wznowiona ręcznie lub automatycznie. Pula SQL można skalować od 100 DWU do 30 000 DWU.       |
| **SQL na żądanie (wersja zapoznawcza)**   | Rozproszony system przetwarzania danych stworzony z myślą o dużych danych, który umożliwia uruchamianie zapytań T-SQL za pośrednictwem danych w układzie danych. Jest bezserwerowy, więc nie trzeba zarządzać infrastrukturą.       |
|**Apache Spark** | Czas wykonywania platformy Spark używany w puli platformy Spark. Obsługiwana jest bieżąca wersja spark 2.4 z Python 3.6.1, Scala 2.11.12, .NET wsparcie dla Apache Spark 0.5 i Delta Lake 0.3.  | 
| **Apache Spark pool (wersja zapoznawcza)**  | 0-to-N Spark aprowizacji zasobów z ich odpowiednich baz danych można wdrożyć w obszarze roboczym. Pulę platformy Spark można automatycznie wstrzymać, wznowić i skalować.  |
| **Aplikacja Spark**  |   Składa się z procesu sterownika i zestawu procesów egzekucyjnych. Aplikacja Spark jest uruchamiana na puli platformy Spark.            |
| **Sesja iskra**  |   Ujednolicony punkt wejścia aplikacji iskrowej. Zapewnia sposób interakcji z różnych funkcji Spark i z mniejszą liczbę konstrukcji. Aby uruchomić notes, należy utworzyć sesję. Sesję można skonfigurować tak, aby działała na określonej liczbie wykonawców o określonym rozmiarze. Domyślną konfiguracją sesji notesu jest uruchomienie 2 średnich executorów. |
| **Żądanie SQL**  |   Operacja, taka jak kwerenda uruchamiana za pośrednictwem puli SQL lub SQL na żądanie. |
|**Integracja danych**| Umożliwia pozyskiwanie danych między różnymi źródłami i organizowanie działań uruchomionych w obszarze roboczym lub poza obszarem roboczym.| 
|**Artefakty**| Koncepcja, która hermetyzuje wszystkie obiekty niezbędne dla użytkownika do zarządzania źródłami danych, opracowywania, organizowania i wizualizacji.|
|**Notes**| Interaktywny i reaktywny interfejs nauki o danych i inżynierii obsługujący scala, pyspark, c#i sparksql. |
|**Definicja zadania iskry**|Interfejs do przesyłania zadania Platformy Spark za pomocą zestawu jar zawierającego kod i jego zależności.|
|**Przepływ danych**|  Zapewnia w pełni wizualne środowisko bez kodowania wymagane do przetwarzania dużych zbiorów danych. Wszystkie optymalizacje i wykonanie są obsługiwane w sposób bezserwerowy. |
|**Skrypt SQL**| Zestaw poleceń SQL zapisanych w pliku. Skrypt SQL może zawierać co najmniej jedną instrukcję SQL. Może służyć do uruchamiania żądań SQL za pośrednictwem puli SQL lub SQL na żądanie.|
|**Rurociągu**| Logiczne grupowanie działań, które wykonują zadanie razem.|
|**Działanie**| Definiuje akcje do wykonania na danych, takich jak kopiowanie danych, uruchamianie notesu lub skryptu SQL.|
|**Wyzwalacz**| Wykonuje potok. Można go uruchomić ręcznie lub automatycznie (harmonogram, okno tumbling lub oparte na zdarzeniach).|
|**Usługa połączona**| Parametry połączenia definiujące informacje o połączeniu potrzebne do łączenia się z zasobami zewnętrznymi obszaru roboczego.|
|**Dataset**|  Nazwany widok danych, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w działaniu jako dane wejściowe i wyjściowe. Należy do usługi połączonej.|

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
- [Korzystanie z Synapse Studio](quickstart-synapse-studio.md)
- [Tworzenie puli SQL](quickstart-create-sql-pool.md)
- [Korzystanie z języka SQL na żądanie](quickstart-sql-on-demand.md)
- [Tworzenie puli platformy Spark Apache](quickstart-create-apache-spark-pool.md)

