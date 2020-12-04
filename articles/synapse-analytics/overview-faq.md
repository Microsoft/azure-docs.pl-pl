---
title: Często zadawane pytania — Azure Synapse Analytics
description: Często zadawane pytania dotyczące usługi Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a427c77ec23bb933f96d8aec54ca33169aee84d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576030"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Usługa Azure Synapse Analytics — często zadawane pytania

W tym przewodniku znajdziesz najczęściej zadawane pytania dotyczące usługi Azure Synapse Analytics.

## <a name="general"></a>Ogólne

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>P: Jak mogę użyć ról RBAC do zabezpieczenia mojego obszaru roboczego?

Odp.: usługa Azure Synapse wprowadza wiele ról i zakresów, które umożliwiają ich przypisanie, co upraszcza ochronę obszaru roboczego.

Synapse role RBAC:
* Synapse administrator
* Synapse administratora SQL
* Synapse Spark — administrator
* Współautor Synapse (wersja zapoznawcza)
* Wydawca artefaktu Synapse (wersja zapoznawcza)
* Synapse artefaktu (wersja zapoznawcza)
* Synapse COMPUTE — operator (wersja zapoznawcza)
* Użytkownik poświadczeń Synapse (wersja zapoznawcza)

Aby zabezpieczyć obszar roboczy Synapse, przypisz role RBAC do tych zakresów RBAC:
* Obszary robocze
* Pule platformy Spark
* Środowisko Integration Runtime
* Połączone usługi
* Poświadczenia

Ponadto w przypadku dedykowanych pul SQL masz wszystkie te same funkcje zabezpieczeń, które znasz i lubisz.

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>P: Jak mogę kontroluje dedykowane pule SQL, bezserwerowe pule SQL i bezserwerowe pule platformy Spark?

Odp.: jako punkt początkowy, usługa Azure Synapse współpracuje z wbudowaną analizą kosztów i alertami kosztów dostępnymi na poziomie subskrypcji platformy Azure.

- Dedykowane pule SQL — masz bezpośredni wgląd w koszt i kontrolę nad kosztem, ponieważ tworzysz i określisz rozmiary dedykowanych pul SQL. Możesz w dalszej części kontrolować, którzy użytkownicy mogą tworzyć i skalować dedykowane pule SQL przy użyciu ról RBAC platformy Azure.

- Pule SQL bezserwerowe — monitorowanie i zarządzanie kosztami są kontrolowane, co pozwala na codzienne, cotygodniowe i comiesięczne wykonywanie wydatków. Aby uzyskać więcej informacji, [Zobacz Zarządzanie kosztami dla puli SQL bezserwerowej](./sql/data-processed.md) . 

- Pule Spark bezserwerowe — można ograniczyć, kto może tworzyć pule platformy Spark przy użyciu ról RBAC Synapse.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>P: czy program Synapse Workspace obsługuje organizowanie folderów obiektów i stopnia szczegółowości?

Odp.: obszary robocze Synapse obsługują foldery zdefiniowane przez użytkownika.

### <a name="q-can-i-link-more-than-one-power-bi-workspaces-to-a-single-azure-synapse-workspace"></a>P: Czy mogę połączyć więcej niż jeden obszar roboczy Power BI z pojedynczym obszarem roboczym usługi Azure Synapse?
    
Odp.: obecnie można połączyć tylko jeden obszar roboczy Power BI z obszarem roboczym usługi Azure Synapse. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>P: czy Synapse link do Cosmos DB GA?

Odp.: Synapse łącze dla Apache Spark jest GA. Link Synapse dla puli SQL bezserwerowej jest w publicznej wersji zapoznawczej.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>P: czy obszar roboczy platformy Azure Synapse obsługuje CI/CD? 

Odp.: tak! Wszystkie artefakty potoku, notesy, skrypty SQL i definicje zadań platformy Spark będą przechowywane w usłudze GIT. Wszystkie definicje puli będą przechowywane w usłudze GIT jako szablony ARM. Dedykowane obiekty puli SQL (schematy, tabele, widoki itp.) będą zarządzane przy użyciu projektów bazy danych z obsługą ciągłej integracji/ciągłego wdrażania.

## <a name="pipelines"></a>Potoki

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>P: Jak mogę upewnij się, że poświadczenie jest używane do uruchamiania potoku? 

Odp.: każde działanie w potoku Synapse jest wykonywane przy użyciu poświadczeń określonych wewnątrz połączonej usługi.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>P: czy urząd SSIS jest obsługiwany w ramach integracji Synapse?

Odp.: w tej chwili nie. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>P: Jak mogę zmigrować istniejące potoki z Azure Data Factory do obszaru roboczego usługi Azure Synapse?

Odp.: w tym momencie trzeba ręcznie odtworzyć potoki Azure Data Factory i powiązane artefakty, eksportując kod JSON z oryginalnego potoku i importując go do obszaru roboczego Synapse.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>P: Jaka jest różnica między Apache Spark Synapse i Apache Spark?

Odp.: Apache Spark dla Synapse jest Apache Spark z dodaną obsługą integracji z innymi usługami (AAD, Azure itp.) i dodatkowymi bibliotekami (mssparktuils, Hummingbird) i wstępnie dostrojonymi konfiguracjami wydajności.

Każde obciążenie, które jest aktualnie uruchomione na Apache Spark, będzie działać na platformie MSFT Spark bez zmian. 

### <a name="q-what-versions-of-spark-are-available"></a>P: Jakie wersje platformy Spark są dostępne?

Odp.: usługa Azure Synapse Apache Spark w pełni obsługuje platformę Spark 2,4. Aby zapoznać się z pełną listą podstawowych składników i obecnie obsługiwanej wersji, zobacz [Obsługa wersji Apache Spark ](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>P: czy w usłudze Azure Synapse Spark są odpowiednikami?

Odp.: tak, usługa Azure Synapse Apache Spark udostępnia bibliotekę **mssparkutils** . Aby zapoznać się z pełną dokumentacją narzędzia, zobacz [wprowadzenie do narzędzi Microsoft Spark Utilities](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>P: Jak mogę ustawić parametry sesji w Apache Spark?

Odp.: Aby ustawić parametry sesji, użyj opcji%% Configure Magic. Aby parametry zaczęły obowiązywać, wymagane jest ponowne uruchomienie sesji. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>P: Jak mogę ustawić parametry poziomu klastra w puli platformy Spark bezserwerowej?

Odp.: Aby ustawić parametry poziomu klastra, można dostarczyć plik Spark. conf dla puli platformy Spark. Następnie ta pula będzie przestrzegać parametrów w pliku konfiguracji. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>P: Czy można uruchomić klaster Spark z obsługą kilku użytkowników w usłudze Azure Synapse Analytics?
 
Odp.: usługa Azure Synapse udostępnia wbudowane aparaty do określonych przypadków użycia. Apache Spark dla Synapse jest zaprojektowana jako usługa zadań, a nie model klastra. Istnieją dwa scenariusze, w których ludzie proszą o model klastra z obsługą różnych użytkowników.

**#1 scenariusza: wielu użytkowników uzyskujących dostęp do klastra w celu zapewnienia obsługi danych na potrzeby analizy biznesowej.**

Najprostszym sposobem osiągnięcia tego zadania jest zapełnienie danych za pomocą platformy Spark, a następnie skorzystanie z funkcji Synapse SQL, aby można było łączyć Power BI z tymi zestawami danych.

**#2 scenariusza: posiadanie wielu deweloperów w jednym klastrze pozwala zaoszczędzić pieniądze.**
 
Aby zrealizować ten scenariusz, należy dać każdemu deweloperowi bezserwerową pulę platformy Spark, która jest ustawiona do korzystania z niewielkiej liczby zasobów platformy Spark. Ponieważ pule Spark bezserwerowe nie są kosztowne, do momentu ich aktywnego zminimalizowania kosztu, gdy istnieje wielu deweloperów. Pule udostępniają metadane (tabele Spark), dzięki czemu mogą łatwo współpracować ze sobą.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>P: Jak mogę dołączania i instalowania bibliotek oraz zarządzania nimi 

Odp.: pakiety zewnętrzne można zainstalować za pomocą pliku requirements.txt podczas tworzenia puli platformy Spark, z obszaru roboczego Synapse lub z Azure Portal. Zobacz [Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Dedykowane pule SQL

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>P: Jakie są różnice funkcjonalne między dedykowanymi pulami SQL i pulami bezserwerowymi

Odp.: można znaleźć pełną listę różnic w [różnicach funkcji T-SQL w programie Synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>P: teraz, gdy usługa Azure Synapse jest dostępna, jak przenieść dedykowane pule SQL, które wcześniej były autonomiczne w usłudze Azure Synapse? 

Odp.: nie ma "przenoszenia" ani "migracji". Możesz włączyć nowe funkcje obszaru roboczego w istniejących pulach. Jeśli to zrobisz, nie ma żadnych istotnych zmian, a nie będziesz w stanie korzystać z nowych funkcji, takich jak Synapse Studio, Spark i bezserwerowe pule SQL.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>P: co to jest domyślne wdrożenie dedykowanych pul SQL? 

Odp.: Domyślnie wszystkie nowe dedykowane pule SQL zostaną wdrożone w obszarze roboczym; Jednak w razie potrzeby nadal można utworzyć dedykowaną pulę SQL (dawniej SQL DW) w postaci autonomicznej. 


### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-sql-pool"></a>P: Jakie są różnice funkcjonalne między dedykowanymi pulami SQL i bezserwerową pulą SQL 

Odp.: można znaleźć pełną listę różnic w [różnicach funkcji T-SQL w programie Synapse SQL](./sql/overview-features.md).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)
