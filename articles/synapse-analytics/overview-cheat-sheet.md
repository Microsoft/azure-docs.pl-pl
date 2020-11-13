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
ms.openlocfilehash: fbd113194aa904096425ee09b741cf693f89bba2
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591924"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Arkusz Ściągawka usługi Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Arkusz usługi Azure Synapse Analytics Ściągawka przeprowadzi Cię przez podstawowe pojęcia związane z usługą i ważnymi poleceniami. Ten artykuł jest pomocny w przypadku nowych osób, które chcą uzyskać najważniejsze tematy dotyczące usługi Azure Synapse.

## <a name="basics"></a>Podstawy

**Obszar roboczy Synapse** to zabezpieczana granica współpracy służąca do przeprowadzania analiz przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLS Gen2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów.

Obszar roboczy umożliwia wykonywanie analiz przy użyciu programów SQL i Apache Spark. Zasoby dostępne dla usługi SQL i programu Spark Analytics są zorganizowane w **Pule** SQL i Spark. 

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** to możliwość wykonywania analiz opartych na języku T-SQL w obszarze roboczym Synapse. Program Synapse SQL ma dwa modele użycia: dedykowany i bezserwerowy.  W przypadku modelu dedykowanego Użyj **dedykowanych pul SQL**. Obszar roboczy może mieć dowolną liczbę tych pul. Aby użyć modelu bezserwerowego, użyj **bezserwerowych pul SQL**. Każdy obszar roboczy ma jedną z tych pul.

## <a name="apache-spark-for-synapse"></a>Apache Spark Synapse

Aby korzystać z usługi Spark Analytics, Utwórz i Użyj **bezserwerowych pul Apache Spark** w obszarze roboczym usługi Synapse.

* Operacja **żądania SQL** , taka jak zapytanie działa za pomocą dedykowanej puli SQL lub bezserwerowej puli SQL.
* **Skrypt SQL** — zestaw poleceń SQL zapisywanych w pliku. Skrypt SQL może zawierać jedną lub więcej instrukcji SQL. Może służyć do uruchamiania żądań SQL za poorednictwem dedykowanej puli SQL lub bezserwerowej puli SQL.


## <a name="spark-terminology"></a>Terminologia platformy Spark

* **Apache Spark w przypadku** czasu wykonywania Synapse-Spark używanego w puli Spark bezserwerowej. Bieżącą obsługiwaną wersją jest platforma Spark 2,4 z 3.6.1 języka Python, Scala 2.11.12, obsługą platformy .NET dla Apache Spark 0,5 i delty Lake 0,3.  
* W obszarze roboczym można wdrażać zasoby **puli Apache Spark** — od 0 do N platformy Spark z odpowiednimi bazami danych. Pulę platformy Spark można automatycznie wstrzymać, wznowić i skalować.  
* **Aplikacja Spark** — składa się z procesu sterownika i zestawu procesów wykonujących testy. Aplikacja Spark działa w puli Spark bezserwerowej.            
* **Sesja platformy Spark** — Zunifikowany punkt wejścia aplikacji platformy Spark. Zapewnia sposób współpracy z różnymi funkcjami platformy Spark i z mniejszą liczbą konstrukcji. Aby uruchomić Notes, należy utworzyć sesję. Sesję można skonfigurować do uruchamiania na określonej liczbie wykonawców o określonym rozmiarze. Domyślna konfiguracja sesji notesu jest uruchamiana w przypadku programów wykonawczych o rozmiarze 2 średnim.
* **Notes** — interaktywny i reaktywny interfejs nauki i inżynierii danych obsługujący Scala, PySpark, C# i SparkSQL.
* **Definicja zadania platformy Spark** — interfejs umożliwiający przesłanie zadania platformy Spark przez zestaw jar zawierający kod i jego zależności.

## <a name="pipelines-terminology"></a>Terminologia potoku
* **Integracja danych** — umożliwia pozyskiwanie danych między różnymi źródłami i organizowanie działań uruchomionych w obszarze roboczym lub poza obszarem roboczym.
* **Przepływ danych** — udostępnia w pełni wizualizację, bez konieczności kodowania danych Big Data. Wszystkie optymalizacje i wykonywanie są obsługiwane w sposób bezserwerowy.
* **Potok** — logiczne grupowanie działań, które wspólnie wykonują zadanie.
* **Działanie** — definiuje akcje do wykonania na danych, takich jak kopiowanie danych, uruchamianie notesu lub skryptu SQL.
* **Trigger** -wykonuje potok. Może być uruchamiany ręcznie lub automatycznie (harmonogram, wirowania okno lub zdarzenia)
* **Zestaw danych integracji** — nazwany widok zawierający dane, które po prostu wskazują lub odwołują się do danych, które mają być używane w działaniu jako dane wejściowe i wyjściowe. Należy do połączonej usługi.

## <a name="general-terminology"></a>Ogólna terminologia

* **Artefakty** — koncepcja, która hermetyzuje wszystkie obiekty niezbędne dla użytkownika do zarządzania źródłami danych, opracowywania, organizowania i wizualizacji.
* **Połączone usługi** — parametry połączenia, które definiują informacje o połączeniu, które są konieczne, aby obszar roboczy mógł nawiązać połączenie z zasobami zewnętrznymi.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
- [Korzystanie z programu Synapse Studio](quickstart-synapse-studio.md)
- [Tworzenie dedykowanej puli SQL](quickstart-create-sql-pool-portal.md)
- [Utwórz bezserwerową pulę Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)

