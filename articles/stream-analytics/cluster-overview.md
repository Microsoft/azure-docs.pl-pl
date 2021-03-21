---
title: Omówienie klastrów Azure Stream Analytics
description: Dowiedz się więcej o dedykowanej ofercie Stream Analytics klastrze.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e5157a02f97370b20db85bf5e3e8aae98a2d8668
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709295"
---
# <a name="overview-of-azure-stream-analytics-cluster"></a>Omówienie klastra Azure Stream Analytics

Klaster Azure Stream Analytics oferuje wdrożenie pojedynczej dzierżawy dla złożonych i wymagających scenariuszy przesyłania strumieniowego. W pełnej skali klastry Stream Analytics mogą przetwarzać ponad 200 MB na sekundę w czasie rzeczywistym. Stream Analytics zadania uruchomione w dedykowanych klastrach mogą korzystać ze wszystkich funkcji w ramach standardowej oferty oraz obsługują połączenia prywatne z danymi wejściowymi i wyjściowymi.

W przypadku klastrów Stream Analytics są naliczane opłaty za jednostki przesyłania strumieniowego (SUs) reprezentujące ilość zasobów procesora i pamięci przydzielonej do klastra. Jednostka przesyłania strumieniowego jest taka sama w przypadku ofert standardowych i dedykowanych. W każdym klastrze można zakupić 36, 72, 108, 144, 180 lub 216. Klaster Stream Analytics może pełnić rolę platformy przesyłania strumieniowego dla Twojej organizacji i może być współużytkowany przez różne zespoły pracujące w różnych przypadkach użycia.

## <a name="what-are-stream-analytics-clusters"></a>Co to są klastry Stream Analytics

Klastry usługi Stream Analytics są obsługiwane przez ten sam aparat, który odpowiada za uruchamianie zadań usługi Stream Analytics w środowisku wielodostępnym. Pojedyncza dzierżawa, dedykowany klaster, ma następujące funkcje:

* Hosting pojedynczej dzierżawy bez szumów z innych dzierżaw. Zasoby są naprawdę „odizolowane” i działają lepiej w przypadku nagłego skoku ruchu w sieci.

* Skaluj klaster między 36 a 216, gdy użycie przesyłania strumieniowego rośnie z upływem czasu.

* Obsługa sieci wirtualnej, dzięki której zadania Stream Analytics mogą bezpiecznie łączyć się z innymi zasobami przy użyciu prywatnych punktów końcowych.

* Możliwość tworzenia zdefiniowanych przez użytkownika funkcji języka C# i deserializatorów niestandardowych w dowolnym regionie.

* Zero koszt konserwacji pozwala skoncentrować się na tworzeniu rozwiązań analitycznych w czasie rzeczywistym.

## <a name="how-to-get-started"></a>Jak zacząć

[Klaster Stream Analytics można utworzyć](create-cluster.md) za pomocą [Azure Portal](https://aka.ms/asaclustercreateportal). Jeśli masz jakieś pytania lub potrzebujesz pomocy przy dołączaniu, możesz skontaktować się z [zespołem Stream Analytics](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Jak mogę wybrać klaster Stream Analytics i zadanie Stream Analytics?

Najprostszym sposobem na rozpoczęcie pracy jest tworzenie i opracowywanie Stream Analyticsgo zadania w celu zapoznania się z usługą i sprawdzenie, jak może ona spełniać wymagania analizy.

Stream Analytics same zadania nie obsługują sieci wirtualnych. Jeśli dane wejściowe lub wyjściowe są chronione za zaporą lub Virtual Network platformy Azure, dostępne są następujące dwie opcje:

* Jeśli komputer lokalny ma dostęp do zasobów wejściowych i wyjściowych zabezpieczonych za pomocą sieci wirtualnej (na przykład Azure Event Hubs lub Azure SQL Database), można [zainstalować Azure Stream Analytics Narzędzia dla programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) na komputerze lokalnym. Możesz tworzyć i [testować Stream Analytics zadania lokalnie](stream-analytics-live-data-local-testing.md) na urządzeniu bez ponoszenia żadnych kosztów. Gdy wszystko będzie gotowe do użycia Stream Analytics w architekturze, można utworzyć klaster Stream Analytics, skonfigurować prywatne punkty końcowe i uruchamiać zadania w odpowiedniej skali.

* Można utworzyć klaster Stream Analytics, skonfigurować klaster z prywatnymi punktami końcowymi potrzebnymi dla potoku i uruchamiać zadania Stream Analytics w klastrze.

### <a name="what-performance-can-i-expect"></a>Jakiej wydajności można oczekiwać?

SU jest taka sama w przypadku standardowych i dedykowanych ofert. Pojedyncze zadanie, które wykorzystuje pełny klaster 36 SU, może osiągnąć około 36 MB/sekundę przepływności z opóźnieniem milisekund. Dokładna liczba zależy od formatu zdarzeń i typu analizy. Ponieważ jest on dedykowany, klaster Stream Analytics oferuje bardziej niezawodne gwarancje wydajności. Wszystkie zadania uruchomione w klastrze należą tylko do Ciebie.

### <a name="can-i-scale-my-cluster"></a>Czy mogę skalować klaster?

Tak. Możesz łatwo skonfigurować pojemność klastra, co pozwala na [skalowanie w górę lub w dół](scale-cluster.md) odpowiednio do potrzeb.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Czy mogę uruchamiać moje istniejące zadania w nowo utworzonych klastrach?

Tak. Istniejące zadania można połączyć z nowo utworzonym klastrem Stream Analytics i uruchamiać je w zwykły sposób. Nie trzeba ponownie tworzyć istniejących zadań Stream Analytics od podstaw.

### <a name="how-much-will-these-clusters-cost-me"></a>Ile będzie kosztować te klastry?

W przypadku klastrów Stream Analytics są naliczone opłaty na podstawie wybranej pojemności SU. Opłaty za klastry są naliczane co godzinę, a dla każdego zadania uruchomionego w tych klastrach nie ma dodatkowych opłat. Zapoznaj się z informacjami na [stronie cennika usługi link prywatny](https://azure.microsoft.com/pricing/details/private-link/) dla prywatnych aktualizacji rozliczeń punktów końcowych.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Które dane wejściowe i wyjściowe mogą łączyć się prywatnie z klastra usługi Stream Analytics?

Stream Analytics obsługuje różne typy danych wejściowych i wyjściowych. W klastrze można [tworzyć prywatne punkty końcowe](private-endpoints.md) , które umożliwiają wykonywanie zadań dostępu do zasobów wejściowych i wyjściowych. Obecnie Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub i Azure Service Bus są obsługiwanymi usługami, a inne typy zostaną dodane wkrótce. 

## <a name="next-steps"></a>Następne kroki

Masz teraz Omówienie klastra Azure Stream Analytics. Następnie możesz utworzyć klaster i uruchomić zadanie Stream Analytics: 

* [Tworzenie klastra Stream Analytics](create-cluster.md)
* [Zarządzanie prywatnymi punktami końcowymi w klastrze Azure Stream Analytics](private-endpoints.md)
* [Zarządzanie zadaniami Stream Analytics w klastrze Stream Analytics](manage-jobs-cluster.md)
* [Tworzenie zadania Stream Analytics](stream-analytics-quick-create-portal.md)
