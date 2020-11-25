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
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030683"
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

Aby korzystać z usługi Spark Analytics, Utwórz i Użyj **bezserwerowych pul Apache Spark** w obszarze roboczym usługi Synapse. Po rozpoczęciu korzystania z puli platformy Spark obszary robocze tworzą **sesję platformy Spark** do obsługi zasobów assosociated z tą sesją. 

Istnieją dwa sposoby używania platformy Spark w Synapse:
* **Notesy platformy Spark** do tworzenia danych i inżynierii z użyciem danych Scala, PySpark, C# i SparkSQL
* **Definicje zadań platformy Spark** do uruchamiania zadań usługi Batch Spark przy użyciu plików jar.

Obsługa wersji:
* Platforma Spark 2,4
* 3.6.1 Python
* Scala 2.11.12
* .NET dla Apache Spark 1,0
* Różnicowa Lake 0,3.  

## <a name="pipelines"></a>Pipelines

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

