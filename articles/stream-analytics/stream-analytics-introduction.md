---
title: Omówienie usługi Azure Stream Analytics
description: Poznaj usługę Stream Analytics — zarządzaną usługę, która pomaga analizować dane przesyłane strumieniowo z Internetu rzeczy (IoT) w czasie rzeczywistym.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 673c6e2ed0aedcc186baf8c7e1884dd537f9567d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459852"
---
# <a name="what-is-azure-stream-analytics"></a>Co to jest usługa Azure Stream Analytics?

Usługa Azure Stream Analytics to aparat analizy i skomplikowanych procesów przetwarzania zdarzeń w czasie rzeczywistym, który jest przeznaczony do analizowania i przetwarzania dużych ilości szybkich danych przesyłania strumieniowego z wielu źródeł jednocześnie. Wzorce i relacje można zidentyfikować w informacjach pochodzących z wielu źródeł wejściowych, w tym urządzeń, czujników, strumieni kliknięć, kanałów mediów społecznościowych i aplikacji. Te wzorce mogą służyć do wyzwalania akcji i inicjowania przepływów pracy, takich jak tworzenie alertów, podawanie informacji do narzędzia raportowania lub przechowywanie przekształconych danych do późniejszego użycia. Ponadto usługa Stream Analytics jest dostępna w czasie pracy usługi Azure IoT Edge i obsługuje ten sam dokładnie język lub składnię co chmura. 

Poniższe scenariusze są przykładami, kiedy można użyć usługi Azure Stream Analytics:

* Analizowanie strumieni telemetrii w czasie rzeczywistym z urządzeń IoT
* Analiza strumienia kliknięć w internecie oraz analiza dzienników internetowych
* Geoprzestrzenna analiza na potrzeby zarządzania flotą i korzystania z pojazdów nie wymagających kierowcy
* Zdalne monitorowanie i przewidywanie konserwacji zasobów o wysokiej wartości
* Analiza danych punktu sprzedaży w czasie rzeczywistym na potrzeby kontroli zapasów i wykrywania anomalii

## <a name="how-does-stream-analytics-work"></a>Jak działa usługa Stream Analytics?

Zadanie usługi Azure Stream Analytics składa się z danych wejściowych, kwerendy i danych wyjściowych. Usługa Stream Analytics pobuje dane z usługi Azure Event Hubs, Usługi Azure IoT Hub lub usługi Azure Blob Storage. Kwerenda, która jest oparta na języku zapytań SQL, może służyć do łatwego filtrowania, sortowania, agregowania i dołączania do danych przesyłania strumieniowego przez pewien czas. Można również rozszerzyć ten język SQL za pomocą funkcji zdefiniowanych przez użytkownika języka JavaScript i C#(UDF). Opcje zamawiania zdarzeń i czas trwania okien czasu można łatwo dostosować podczas tworzenia operacji agregacji za pomocą prostych konstrukcji języka i/lub konfiguracji.

Każde zadanie ma dane wyjściowe dla przekształconych danych i można kontrolować, co się dzieje w odpowiedzi na informacje, które zostały przeanalizowane. Można na przykład:

* Wysyłaj dane do usług, takich jak usługi Usługi Azure, Tematy usługi Service Bus lub Kolejki, aby wyzwolić komunikację lub niestandardowe przepływy pracy w dół.
* Wysyłanie danych do pulpitu nawigacyjnego usługi Power BI w celu pulpitu nawigacyjnego w czasie rzeczywistym.
* Przechowuj dane w innych usługach magazynu platformy Azure, aby szkolić model uczenia maszynowego na podstawie danych historycznych lub przeprowadzać analizy wsadowe.

Na poniższej ilustracji przedstawiono sposób wysyłania danych do usługi Stream Analytics, analizowania i wysyłania do innych działań, takich jak magazyn lub prezentacja:

![Wprowadzenie do potoku usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Najważniejsze funkcje i korzyści

Usługa Stream Analytics została zaprojektowana tak, aby była łatwa w użyciu, elastyczna, niezawodna i skalowalna do dowolnej wielkości zadania. Jest ona dostępna w wielu regionach platformy Azure. Na poniższej ilustracji przedstawiono kluczowe możliwości usługi Azure Stream Analytics:

![Kluczowe możliwości usługi Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Łatwość rozpoczynania pracy

Usługa Azure Stream Analytics jest łatwa do uruchomienia. Wystarczy kilka kliknięć, aby połączyć się z wieloma źródłami i pochłaniaczami, tworząc potok end-to-end. Usługa Stream Analytics może łączyć się z [usługą Azure Event Hubs](/azure/event-hubs/) i [centrum Azure IoT Hub](/azure/iot-hub/) w celu przesyłania strumieniowego pozyskiwania danych, a także magazynu obiektów [Blob platformy Azure](/azure/storage/common/storage-introduction) w celu pozyskiwania danych historycznych. Wprowadzanie zadań może również zawierać statyczne lub wolno zmieniające się dane referencyjne z magazynu obiektów Blob platformy Azure lub [bazy danych SQL,](stream-analytics-use-reference-data.md#azure-sql-database) które można dołączyć do przesyłania strumieniowego danych w celu wykonania operacji odnośnych.

Usługa Stream Analytics może kierować dane wyjściowe zadań do wielu systemów magazynowania, takich jak [Magazyn obiektów Blob azure,](/azure/storage/common/storage-introduction) [usługa Azure SQL Database,](/azure/sql-database/) [Azure Data Lake Store](/azure/data-lake-store/)i [Azure CosmosDB](/azure/cosmos-db/introduction). Można uruchomić analizę wsadową na przechowywanych danych wyjściowych za pomocą usługi Azure HDInsight lub można wysłać dane wyjściowe do innej usługi, takich jak Usługi Event Hubs do zużycia lub [Usługi Power BI](https://docs.microsoft.com/power-bi/) do wizualizacji w czasie rzeczywistym.

Aby uzyskać całą listę wyjść usługi Stream Analytics, zobacz [Opis wyników z usługi Azure Stream Analytics.](stream-analytics-define-outputs.md)

## <a name="programmer-productivity"></a>Produktywność programisty

Usługa Azure Stream Analytics używa prostego języka zapytań opartego na języku SQL, który został rozszerzony o zaawansowane ograniczenia czasowe do analizowania danych w ruchu. Aby zdefiniować przekształcenia zadań, należy użyć prostego, deklaratywnego [języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference), który umożliwia tworzenie złożonych zapytań czasowych i analizy przy użyciu prostych konstrukcji języka SQL. Ponieważ język zapytań usługi Stream Analytics jest spójny z językiem SQL, znajomość języka SQL jest wystarczająca do rozpoczęcia tworzenia zadań. Można również tworzyć zadania przy użyciu narzędzi deweloperskich, takich jak narzędzia Azure PowerShell, [Narzędzia programu Stream Analytics Visual Studio,](stream-analytics-tools-for-visual-studio-install.md)rozszerzenie kodu programu Stream Analytics Visual [Studio](quick-create-vs-code.md)lub szablony usługi Azure Resource Manager. Narzędzia dla deweloperów pozwalają tworzyć zapytania przekształceń w trybie offline i używać [potoków ciągłej integracji/ciągłego wdrażania](stream-analytics-tools-for-visual-studio-cicd.md) w celu przesłania zadań na platformę Azure.

Język zapytań usługi Stream Analytics oferuje szeroką gamę funkcji do analizowania i przetwarzania danych strumieniowych. Ten język kwerendy obsługuje proste manipulowanie danymi, funkcje agregacji i złożone funkcje geoprzestrzenne. Można edytować zapytania w portalu i przetestować je przy użyciu przykładowych danych, które są wyodrębniane ze strumienia na żywo.

Możliwości języka zapytań można rozszerzyć, definiując i wywołując dodatkowe funkcje. Można zdefiniować wywołania funkcji w usłudze Azure Machine Learning, aby korzystać z rozwiązań usługi Azure Machine Learning i zintegrować funkcje zdefiniowane przez użytkownika JavaScript lub C# (UDFs) lub agregaty zdefiniowane przez użytkownika, aby wykonać złożone obliczenia jako część zapytania usługi Stream Analytics.

## <a name="fully-managed"></a>Pełne zarządzanie

Usługa Azure Stream Analytics to bezserwerowe w pełni zarządzane rozwiązanie typu „platforma jako usługa” (PaaS) platformy Azure. Nie trzeba aprowizować żadnego sprzętu ani zarządzać klastrami, aby uruchamiać zadania. Usługa Azure Stream Analytics w pełni zarządza twoim zadaniem, konfigurując złożone klastry obliczeniowe w chmurze i dbając o dostrajanie wydajności niezbędne do uruchomienia zadania. Integracja z usługą Azure Event Hubs i usługą Azure IoT Hub umożliwia zadanie pozyskiwanie milionów zdarzeń na sekundę pochodzących z wielu źródeł, aby uwzględnić podłączone urządzenia, strumienie kliknięć i pliki dziennika. Za pomocą funkcji partycjonowania Event Hubs, można podzielić obliczenia na logiczne kroki, każdy z możliwością dalszej partycjonowania w celu zwiększenia skalowalności.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Biegaj w chmurze lub na inteligentnej krawędzi

Usługa Azure Stream Analytics może działać w chmurze, w celu analizy na dużą skalę lub działać w usłudze IoT Edge w celu analizy o bardzo małych opóźnieniach. Usługa Azure Stream Analytics używa tego samego języka zapytań zarówno w chmurze, jak i na krawędzi, umożliwiając deweloperom tworzenie prawdziwie hybrydowych architektur do przetwarzania strumienia. 

## <a name="low-total-cost-of-ownership"></a>Niski całkowity koszt posiadania

Jako usługa w chmurze usługa Stream Analytics jest zoptymalizowana pod kątem niskiego kosztu. Nie ma żadnych kosztów początkowych - płacisz tylko za [jednostki przesyłania strumieniowego, które zużywasz,](stream-analytics-streaming-unit-consumption.md)i ilość przetwarzanych danych. Nie jest wymagane żadne zobowiązanie lub aprowizacji klastra i można skalować zadanie w górę lub w dół w zależności od potrzeb biznesowych.

## <a name="mission-critical-ready"></a>Gotowość na sytuacje krytyczne dla działalności firmy

Usługa Azure Stream Analytics jest dostępna w wielu regionach na całym świecie i jest przeznaczona do uruchamiania obciążeń o kluczowym znaczeniu dzięki obsłudze wymagań dotyczących niezawodności, bezpieczeństwa i zgodności.

### <a name="reliability"></a>Niezawodność

Poza tym usługa Azure Stream Analytics gwarantuje przeprowadzenie dokładnie jednej aprowizacji zdarzenia oraz co najmniej jednokrotnego dostarczenia zdarzeń, więc nigdy nie dochodzi do utraty zdarzeń. Przetwarzanie dokładnie raz jest gwarantowane z wybranym wyjściem, jak opisano w [gwarancjach dostarczania zdarzeń.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

Usługa Azure Stream Analytics ma wbudowane funkcje odzyskiwania na wypadek awarii podczas dostarczania zdarzeń. Usługa Stream Analytics udostępnia również wbudowane punkty kontrolne, aby zachować stan zadania i zapewnia powtarzalne wyniki.

Jako usługa zarządzana usługa Stream Analytics gwarantuje przetwarzanie zdarzeń z dostępnością 99,9% na poziomie szczegółowości. Aby uzyskać więcej informacji, zobacz stronę [Umowy SLA usługi Stream Analytics.](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 

### <a name="security"></a>Zabezpieczenia

Z punktu widzenia zabezpieczeń usługa Azure Stream Analytics szyfruje całą komunikację przychodzącą i wychodzącą oraz obsługuje protokół TLS 1.2. Wbudowane punkty kontrolne również są szyfrowane. Usługa Stream Analytics nie przechowuje danych przychodzących, ponieważ całe przetwarzanie odbywa się w pamięci.

### <a name="compliance"></a>Zgodność

Usługa Azure Stream Analytics zapewnia dostosowanie do wielu certyfikatów zgodności, co zostało opisane w [przeglądzie zgodności platformy Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Wydajność

Usługa Stream Analytics może przetwarzać miliony zdarzeń co sekundę i dostarczać wyniki przy bardzo niskich opóźnieniach. Umożliwia skalowanie w górę lub zwiększanie skali w poziomie w celu obsługi dużych aplikacji z przetwarzaniem złożonych zdarzeń oraz przetwarzaniem w czasie rzeczywistym. Usługa Stream Analytics obsługuje wyższą wydajność przez partycjonowanie, umożliwiając równoległość złożonych zapytań i wykonywanie ich w wielu węzłach przesyłania strumieniowego. Usługa Azure Stream Analytics jest oparta na [Trill](https://github.com/Microsoft/Trill), aparacie analizy przesyłania strumieniowego w pamięci o wysokiej wydajności opracowanym we współpracy z Microsoft Research.

## <a name="next-steps"></a>Następne kroki

Masz już podstawowe informacje o usłudze Azure Stream Analytics. Teraz możesz utworzyć pierwsze zadanie w usłudze Stream Analytics:

* [Tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md).
* [Utwórz zadanie usługi Stream Analytics przy użyciu programu Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Tworzenie zadania usługi Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).
* [Utwórz zadanie usługi Stream Analytics przy użyciu programu Visual Studio Code](quick-create-vs-code.md).
