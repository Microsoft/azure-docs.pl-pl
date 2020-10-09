---
title: Arkusz Ściągawka — Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)
description: Przewodnik dotyczący przechodzenia przez usługę Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 774e503bec3f1f8c4cc5b85bb599230a3397f811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858442"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Arkusz Ściągawka usługi Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Arkusz usługi Azure Synapse Analytics Ściągawka przeprowadzi Cię przez podstawowe pojęcia związane z usługą i ważnymi poleceniami. Ten artykuł jest pomocny w przypadku nowych osób, które chcą uzyskać najważniejsze tematy dotyczące usługi Azure Synapse.

## <a name="basics"></a>Podstawy

**Obszar roboczy Synapse** to zabezpieczana granica współpracy służąca do przeprowadzania analiz przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLS Gen2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów.

Obszar roboczy umożliwia wykonywanie analiz przy użyciu programów SQL i Apache Spark. Zasoby dostępne dla usługi SQL i programu Spark Analytics są zorganizowane w **Pule**SQL i Spark. 

## <a name="synapse-sql"></a>Synapse SQL
**Synapse SQL** to możliwość wykonywania analiz opartych na języku T-SQL w obszarze roboczym Synapse. Program Synapse SQL ma dwa modele użycia: dedykowany i bezserwerowy.  W przypadku modelu dedykowanego Użyj dedykowanych **pul SQL**. Obszar roboczy może mieć dowolną liczbę tych pul. Aby użyć modelu bezserwerowego, użyj bezserwerowej puli SQL o nazwie "SQL on-demand". Każdy obszar roboczy ma jedną z tych pul.

## <a name="apache-spark-for-synapse"></a>Apache Spark Synapse
Aby korzystać z usługi Spark Analytics, Utwórz **pulę Spark** i używaj ich w obszarze roboczym Synapse.

## <a name="sql-terminology"></a>Terminologia SQL
| Okres                         | Definicja      |
|:---                                 |:---                 |
| **Żądanie SQL**  |   Operacje, takie jak zapytanie, są wykonywane za pomocą puli SQL lub SQL na żądanie. |

## <a name="spark-terminology"></a>Terminologia platformy Spark
| Okres                         | Definicja      |
|:---                                 |:---                 |
|**Apache Spark Synapse** | Czas wykonywania platformy Spark używany w puli platformy Spark. Bieżącą obsługiwaną wersją jest platforma Spark 2,4 z 3.6.1 języka Python, Scala 2.11.12, obsługą platformy .NET dla Apache Spark 0,5 i delty Lake 0,3.  | 
| **Pula Apache Spark**  | w obszarze roboczym można wdrożyć zasoby z obsługą od 0 do N platformy Spark z odpowiednimi bazami danych. Pulę platformy Spark można automatycznie wstrzymać, wznowić i skalować.  |
| **Aplikacja platformy Spark**  |   Składa się z procesu sterownika i zestawu procesów wykonujących. Aplikacja platformy Spark działa w puli platformy Spark.            |
| **Sesja platformy Spark**  |   Zunifikowany punkt wejścia aplikacji Spark. Zapewnia sposób współpracy z różnymi funkcjami platformy Spark i z mniejszą liczbą konstrukcji. Aby uruchomić Notes, należy utworzyć sesję. Sesję można skonfigurować do uruchamiania na określonej liczbie wykonawców o określonym rozmiarze. Domyślną konfiguracją sesji notesu jest uruchamianie na 2 wykonawczych o średnim rozmiarze. |
|**Integracja danych**| Zapewnia możliwość pozyskiwania danych między różnymi źródłami i organizowania działań uruchomionych w obszarze roboczym lub poza obszarem roboczym.| 
|**Artifacts**| Koncepcja, która hermetyzuje wszystkie obiekty wymagane przez użytkownika do zarządzania źródłami danych, opracowywania, organizowania i wizualizacji.|
|**Notes**| Interaktywny i reaktywny interfejs nauki i inżynierii danych obsługujący Scala, PySpark, C# i SparkSQL. |
|**Definicja zadania platformy Spark**|Interfejs do przesyłania zadania platformy Spark przez zestaw jar zawierający kod i jego zależności.|
|**Przepływ danych**|  Zapewnia w pełni wizualizację, bez konieczności kodowania danych Big Data. Wszystkie optymalizacje i wykonywanie są obsługiwane w sposób bezserwerowy. |
|**Skrypt SQL**| Zestaw poleceń SQL zapisanych w pliku. Skrypt SQL może zawierać jedną lub więcej instrukcji SQL. Może służyć do uruchamiania żądań SQL za poorednictwem puli SQL lub SQL na żądanie.|
|**Potok**| Logiczne grupowanie działań, które wspólnie wykonują zadanie.|
|**Działanie**| Definiuje akcje do wykonania na danych, takich jak kopiowanie danych, uruchamianie notesu lub skryptu SQL.|
|**Wyzwalacz**| Wykonuje potok. Może być uruchamiany ręcznie lub automatycznie (harmonogram, wirowania okno lub zdarzenia).|
|**Połączona usługa**| Parametry połączenia, które definiują informacje o połączeniu, które są konieczne do nawiązania połączenia z zasobami zewnętrznymi.|
|**Zestaw danych**|  Nazwany widok danych, który po prostu wskazuje lub odwołuje się do danych, które mają być używane w działaniu jako dane wejściowe i wyjściowe. Należy do połączonej usługi.|

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
- [Korzystanie z programu Synapse Studio](quickstart-synapse-studio.md)
- [Tworzenie puli SQL](quickstart-create-sql-pool-portal.md)
- [Tworzenie puli Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)

