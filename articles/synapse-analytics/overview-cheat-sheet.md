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
ms.openlocfilehash: 0e4b96092107e3411f33411f1044fd7cc839f132
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052000"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Arkusz Ściągawka usługi Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Arkusz usługi Azure Synapse Analytics Ściągawka przeprowadzi Cię przez podstawowe pojęcia związane z usługą i ważnymi poleceniami. Ten artykuł jest pomocny w przypadku nowych osób, które chcą uzyskać najważniejsze tematy dotyczące usługi Azure Synapse.

## <a name="basics"></a>Podstawy

**Obszar roboczy Synapse** to zabezpieczana granica współpracy służąca do przeprowadzania analiz przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLS Gen2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów.

Obszar roboczy umożliwia wykonywanie analiz przy użyciu programów SQL i Apache Spark. Zasoby dostępne dla usługi SQL i platformy Spark Analytics są zorganizowane w **Pule**. Istnieją trzy rodzaje pul.
* **Pule SQL**. Zapewniają one **dedykowaną** pojemność analityczną SQL.
* **Pula na żądanie SQL**. Każdy obszar roboczy ma dokładnie jedną pulę na żądanie SQL. Zapewnia to **bezserwerową** wydajność analizy SQL dla poszczególnych zapytań.
* **Pule platformy Spark**. Zapewniają one wydajność analizy platformy Spark.
## <a name="terminology"></a>Terminologia
| Okres                         | Definicja      |
|:---                                 |:---                 |
| **Obszar roboczy Synapse** | Jest to zabezpieczana granica współpracy na potrzeby przeprowadzania analiz przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLS Gen2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów. |
| **Synapse SQL**   | Uruchom analizę z pulami lub z możliwościami na żądanie.  |
| **Pula SQL**   | w obszarze roboczym można wdrożyć zasoby od 0 do N z przydziałem SQL z odpowiednimi bazami danych. Każda pula SQL ma skojarzoną bazę danych. Pulę SQL można skalować, wstrzymywać i wznawiać ręcznie lub automatycznie. Pula SQL może być skalowana z 100 jednostek dwu do 30 000 jednostek dwu.       |
| **SQL na żądanie**   | System rozproszonego przetwarzania danych zbudowany na potrzeby danych na dużą skalę, który umożliwia uruchamianie zapytań T-SQL za pośrednictwem danych w usłudze Data Lake. Nie trzeba zarządzać infrastrukturą.       |
|**Apache Spark Synapse** | Czas wykonywania platformy Spark używany w puli platformy Spark. Bieżącą obsługiwaną wersją jest platforma Spark 2,4 z 3.6.1 języka Python, Scala 2.11.12, obsługą platformy .NET dla Apache Spark 0,5 i delty Lake 0,3.  | 
| **Pula Apache Spark**  | w obszarze roboczym można wdrożyć zasoby z obsługą od 0 do N platformy Spark z odpowiednimi bazami danych. Pulę platformy Spark można automatycznie wstrzymać, wznowić i skalować.  |
| **Aplikacja platformy Spark**  |   Składa się z procesu sterownika i zestawu procesów wykonujących. Aplikacja platformy Spark działa w puli platformy Spark.            |
| **Sesja platformy Spark**  |   Zunifikowany punkt wejścia aplikacji Spark. Zapewnia sposób współpracy z różnymi funkcjami platformy Spark i z mniejszą liczbą konstrukcji. Aby uruchomić Notes, należy utworzyć sesję. Sesję można skonfigurować do uruchamiania na określonej liczbie wykonawców o określonym rozmiarze. Domyślną konfiguracją sesji notesu jest uruchamianie na 2 wykonawczych o średnim rozmiarze. |
| **Żądanie SQL**  |   Operacje, takie jak zapytanie, są wykonywane za pomocą puli SQL lub SQL na żądanie. |
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

