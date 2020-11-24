---
title: Terminologia — Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)
description: Przewodnik dotyczący przechodzenia przez usługę Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 20e635d606676a526fac4c3720931525beb66876
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95790423"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologia dotycząca usługi Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

W tym dokumencie przedstawiono podstawowe pojęcia dotyczące usługi Azure Synapse Analytics.

## <a name="basics"></a>Podstawy

**Obszar roboczy Synapse** to zabezpieczana granica współpracy służąca do przeprowadzania analiz przedsiębiorstw opartych na chmurze na platformie Azure. Obszar roboczy jest wdrażany w określonym regionie i ma skojarzone konto ADLS Gen2 i system plików (do przechowywania danych tymczasowych). Obszar roboczy znajduje się w grupie zasobów.

Obszar roboczy umożliwia wykonywanie analiz przy użyciu programów SQL i Apache Spark. Zasoby dostępne dla usługi SQL i programu Spark Analytics są zorganizowane w **Pule** SQL i Spark. 

## <a name="linked-services"></a>Połączone usługi

Obszar roboczy może zawierać dowolną liczbę **połączonych usług**, czyli parametry połączenia, które definiują informacje o połączeniu potrzebne do nawiązania połączenia z zasobami zewnętrznymi w obszarze roboczym.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** to możliwość wykonywania analiz opartych na języku T-SQL w obszarze roboczym Synapse. Program Synapse SQL ma dwa modele użycia: dedykowany i bezserwerowy.  W przypadku modelu dedykowanego Użyj **dedykowanych pul SQL**. Obszar roboczy może mieć dowolną liczbę tych pul. Aby użyć modelu bezserwerowego, użyj **bezserwerowych pul SQL**. Każdy obszar roboczy ma jedną z tych pul.

* Operacja **żądania SQL** , taka jak zapytanie działa za pomocą dedykowanej puli SQL lub bezserwerowej puli SQL.
* **Skrypt SQL** — zestaw poleceń SQL zapisywanych w pliku. Skrypt SQL może zawierać jedną lub więcej instrukcji SQL. Może służyć do uruchamiania żądań SQL za poorednictwem dedykowanej puli SQL lub bezserwerowej puli SQL.

## <a name="apache-spark-for-synapse"></a>Apache Spark Synapse

Aby korzystać z usługi Spark Analytics, Utwórz i Użyj **bezserwerowych pul Apache Spark** w obszarze roboczym usługi Synapse.

* W obszarze roboczym można wdrażać zasoby **puli Apache Spark** — od 0 do N platformy Spark z odpowiednimi bazami danych. Pulę platformy Spark można automatycznie wstrzymać, wznowić i skalować.  
* **Aplikacja Spark** — składa się z procesu sterownika i zestawu procesów wykonujących testy. Aplikacja Spark działa w puli Spark bezserwerowej.            
* **Sesja platformy Spark**— Zunifikowany punkt wejścia aplikacji platformy Spark. Zapewnia sposób współpracy z różnymi funkcjami platformy Spark i z mniejszą liczbą konstrukcji. Aby uruchomić Notes, należy utworzyć sesję. Sesję można skonfigurować do uruchamiania na określonej liczbie wykonawców o określonym rozmiarze. Domyślna konfiguracja sesji notesu jest uruchamiana w przypadku programów wykonawczych o rozmiarze 2 średnim.
* **Notes** — interaktywny i reaktywny interfejs nauki i inżynierii danych obsługujący Scala, PySpark, C# i SparkSQL.
* **Definicja zadania platformy Spark** — interfejs umożliwiający przesłanie zadania platformy Spark przez zestaw jar zawierający kod i jego zależności.

Obsługa wersji:
* Platforma Spark 2,4
* 3.6.1 Python
* Scala 2.11.12
* .NET dla Apache Spark 1,0
* Różnicowa Lake 0,3.  

## <a name="pipelines"></a>Potoki

* **Integracja danych** — umożliwia pozyskiwanie danych między różnymi źródłami i organizowanie działań uruchomionych w obszarze roboczym lub poza obszarem roboczym.
* **Przepływ danych** — udostępnia w pełni wizualizację, bez konieczności kodowania danych Big Data. Wszystkie optymalizacje i wykonywanie są obsługiwane w sposób bezserwerowy.
* **Potok** — logiczne grupowanie działań, które wspólnie wykonują zadanie.
* **Działanie** — definiuje akcje do wykonania na danych, takich jak kopiowanie danych, uruchamianie notesu lub skryptu SQL.
* **Trigger** -wykonuje potok. Może być uruchamiany ręcznie lub automatycznie (harmonogram, wirowania okno lub zdarzenia)
* **Zestaw danych integracji** — nazwany widok zawierający dane, które po prostu wskazują lub odwołują się do danych, które mają być używane w działaniu jako dane wejściowe i wyjściowe. Należy do połączonej usługi.

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)

