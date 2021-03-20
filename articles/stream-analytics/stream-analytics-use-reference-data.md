---
title: Używanie danych referencyjnych do wyszukiwania w Azure Stream Analytics
description: W tym artykule opisano sposób używania danych referencyjnych do wyszukiwania lub skorelowania danych w projekcie zapytania Azure Stream Analytics zadania.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99560209"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Używanie danych referencyjnych do wyszukiwania w Stream Analytics

Dane referencyjne (nazywane również tabelami odnośników) to zbiór danych, który jest statyczny lub wolno zmieniany w charakterze, używany do przeszukiwania lub rozszerzania strumieni danych. Na przykład w scenariuszu IoT można przechowywać metadane dotyczące czujników (które często nie zmieniają się) w danych referencyjnych i przyłączać je do strumieni danych IoT w czasie rzeczywistym. Azure Stream Analytics ładuje dane referencyjne w pamięci, aby osiągnąć Przetwarzanie strumienia o małym opóźnieniu. Aby korzystać z danych referencyjnych w zadaniu Azure Stream Analytics, zazwyczaj użyjesz [sprzężenia danych referencyjnych](/stream-analytics-query/reference-data-join-azure-stream-analytics) w zapytaniu. 

## <a name="example"></a>Przykład  
Możesz mieć strumień zdarzeń generowanych w czasie rzeczywistym, gdy samochody przechodzą w kabinę płatną. W przypadku połączeń płatnych można przechwycić płytę licencyjną w czasie rzeczywistym i dołączyć do statycznego zestawu danych, który zawiera szczegółowe informacje o rejestracji, aby identyfikować płytki licencyjne, które wygasły.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics obsługuje magazyn obiektów blob platformy Azure i Azure SQL Database jako warstwę magazynu dla danych referencyjnych. Możesz również przekształcić i/lub skopiować dane referencyjne do usługi BLOB Storage z Azure Data Factory, aby korzystać z [dowolnej liczby magazynów danych opartych na chmurze i lokalnych](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Dane referencyjne są modelowane jako sekwencja obiektów BLOB (zdefiniowanych w konfiguracji wejściowej) w kolejności rosnącej daty/godziny określonej w nazwie obiektu BLOB. Obsługuje on **tylko** Dodawanie do końca sekwencji przy użyciu daty/godziny **większej** niż określona przez ostatni obiekt BLOB w sekwencji. Aby uzyskać więcej informacji, zobacz [Korzystanie z danych referencyjnych z BLOB Storage dla zadania Azure Stream Analytics](data-protection.md).

### <a name="configure-blob-reference-data"></a>Skonfiguruj dane referencyjne obiektów BLOB

Aby skonfigurować dane referencyjne, musisz najpierw utworzyć dane wejściowe typu **dane referencyjne**. W poniższej tabeli objaśniono każdą właściwość, która będzie potrzebna podczas tworzenia danych referencyjnych, z opisem:

|**Nazwa właściwości**  |**Opis**  |
|---------|---------|
|Alias danych wejściowych   | Przyjazna nazwa, która zostanie użyta w zapytaniu zadania, aby odwołać się do tego danych wejściowych.   |
|Konto magazynu   | Nazwa konta magazynu, w którym znajdują się obiekty blob. Jeśli znajduje się w tej samej subskrypcji co zadanie Stream Analytics, możesz wybrać ją z listy rozwijanej.   |
|Klucz konta magazynu   | Klucz tajny skojarzony z kontem magazynu. Ta wartość zostanie wypełniona automatycznie, jeśli konto magazynu znajduje się w tej samej subskrypcji co zadanie Stream Analytics.   |
|Kontener magazynu   | Kontenery zapewniają logiczne grupowanie obiektów BLOB przechowywanych w Blob service Microsoft Azure. Po przekazaniu obiektu BLOB do Blob service należy określić kontener dla tego obiektu BLOB.   |
|Wzorzec ścieżki   | Jest to właściwość wymagana, która służy do lokalizowania obiektów BLOB w określonym kontenerze. W ścieżce możesz określić jedno lub więcej wystąpień następujących dwóch zmiennych:<BR>{Date}, {Time}<BR>Przykład 1: produkty/{Date}/{Time}/product-list.csv<BR>Przykład 2: produkty/{Date}/product-list.csv<BR>Przykład 3: product-list.csv<BR><br> Jeśli obiekt BLOB nie istnieje w określonej ścieżce, zadanie Stream Analytics będzie oczekiwać, że obiekt BLOB stanie się nieokreślony.   |
|Format daty [opcjonalnie]   | Jeśli używasz {Date} w określonym wzorcu ścieżki, możesz wybrać format daty, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: RRRR/MM/DD, MM/DD/RRRR itd.   |
|Format czasu [opcjonalnie]   | Jeśli użyto {Time} w określonym wzorcu ścieżki, można wybrać format czasu, w którym obiekty blob są zorganizowane z listy rozwijanej obsługiwanych formatów.<BR>Przykład: gg, HH/mm lub HH-mm.  |
|Format serializacji zdarzeń   | Aby upewnić się, że zapytania działają w oczekiwany sposób, Stream Analytics należy wiedzieć, który format serializacji jest używany w przypadku przychodzących strumieni danych. W przypadku danych referencyjnych obsługiwane formaty to CSV i JSON.  |
|Encoding   | W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8.  |

### <a name="static-reference-data"></a>Statyczne dane referencyjne

Jeśli dane referencyjne nie powinny być zmieniane, obsługa statycznych danych referencyjnych jest włączana przez określenie ścieżki statycznej w konfiguracji wejściowej. Azure Stream Analytics pobiera obiekt BLOB z określonej ścieżki. tokeny podstawiania {date} i {Time} nie są wymagane. Ponieważ dane referencyjne są niezmienne w Stream Analytics, zastępowanie statycznego obiektu BLOB danych odwołania nie jest zalecane.

### <a name="generate-reference-data-on-a-schedule"></a>Generuj dane referencyjne według harmonogramu

Jeśli dane referencyjne są wolnie zmienianym zestawem danych, to obsługa odświeżania danych referencyjnych jest włączana przez określenie wzorca ścieżki w konfiguracji wejściowej przy użyciu tokenów podstawiania {date} i {Time}. Stream Analytics pobiera zaktualizowane definicje danych referencyjnych na podstawie tego wzorca ścieżki. Na przykład wzorzec o `sample/{date}/{time}/products.csv` formacie daty **"rrrr-mm-dd"** i format czasu **"gg-mm"** instruuje Stream Analytics, aby pobrać zaktualizowany obiekt BLOB `sample/2015-04-16/17-30/products.csv` o godzinie 5:30 PM w 16 kwietnia 2015 strefie czasowej UTC.

Azure Stream Analytics automatycznie skanuje w poszukiwaniu odświeżonych obiektów BLOB danych referencyjnych w ciągu jednej minuty. Jeśli obiekt BLOB z sygnaturą czasową 10:30:00 jest przekazywany z niewielkim opóźnieniem (na przykład 10:30:30), zobaczysz małe opóźnienie w Stream Analytics zadania odwołującego się do tego obiektu BLOB. Aby uniknąć takich scenariuszy, zaleca się przekazanie obiektu BLOB wcześniej niż docelowy czas obowiązywania (10:30:00 w tym przykładzie), aby umożliwić Stream Analytics zadanie wystarczającą ilość czasu na odnalezienie i załadowanie go w pamięci oraz wykonanie operacji. 

> [!NOTE]
> Obecnie Stream Analytics zadania wyszukują odświeżanie obiektów BLOB tylko wtedy, gdy komputer przekroczy czas zakodowany w nazwie obiektu BLOB. Na przykład zadanie będzie wyglądało `sample/2015-04-16/17-30/products.csv` tak szybko, jak to możliwe, ale nie wcześniej niż 5:30 PM w 16 kwietnia 2015 strefie czasowej UTC. *Nigdy nie* będzie szukać obiektu BLOB z zakodowanym czasem wcześniejszym niż ostatni wykryty.
> 
> Na przykład po znalezieniu obiektu BLOB przez zadanie `sample/2015-04-16/17-30/products.csv` Funkcja zignoruje wszystkie pliki z zakodowaną datą wcześniejszą niż 5:30 PM kwietnia, 2015, więc jeśli zostanie utworzony opóźniony `sample/2015-04-16/17-25/products.csv` obiekt BLOB w tym samym kontenerze, zadanie nie będzie używać go.
> 
> Podobnie w przypadku `sample/2015-04-16/17-30/products.csv` , gdy jest on tworzony tylko o godzinie 10:03 2015, 16 kwietnia, ale w kontenerze nie występuje obiekt BLOB z wcześniejszą datą, zadanie użyje tego pliku, rozpoczynając od 2015 10:03.
> 
> Wyjątkiem jest to, kiedy zadanie musi ponownie przetworzyć dane w czasie lub podczas pierwszego uruchomienia zadania. W czasie uruchamiania zadanie szuka najnowszego obiektu BLOB wygenerowanego przed określonym czasem rozpoczęcia zadania. W tym celu upewnij się, że podczas uruchamiania zadania istnieje zestaw danych referencyjnych, które **nie są puste** . Jeśli nie można go znaleźć, zadanie Wyświetla następującą diagnostykę: `Initializing input without a valid reference data blob for UTC time <start time>` .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) może służyć do organizowania zadania tworzenia zaktualizowanych obiektów BLOB wymaganych przez Stream Analytics do aktualizowania definicji danych referencyjnych. Fabryka danych jest usługą integracji danych w chmurze, która służy do aranżacji i automatyzacji przenoszenia i przekształcania danych. Data Factory obsługuje [łączenie z dużą liczbą magazynów danych opartych na chmurze i lokalnymi](../data-factory/copy-activity-overview.md) oraz szybkie przeniesienie danych zgodnie z regularnym określonym harmonogramem. Aby uzyskać więcej informacji i wskazówki krok po kroku dotyczące konfigurowania potoku Data Factory w celu wygenerowania danych referencyjnych dla Stream Analytics, które odświeżają zgodnie ze wstępnie zdefiniowanym harmonogramem, zapoznaj się z tym [przykładem](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs)w witrynie GitHub.

### <a name="tips-on-refreshing-blob-reference-data"></a>Porady dotyczące odświeżania danych referencyjnych obiektów BLOB

1. Nie należy zastępować obiektów BLOB danych referencyjnych, ponieważ są one niezmienne.
2. Zalecanym sposobem odświeżania danych referencyjnych jest:
    * Użyj {Date}/{Time} we wzorcu ścieżki
    * Dodaj nowy obiekt BLOB przy użyciu tego samego kontenera i wzorca ścieżki zdefiniowanego w danych wejściowych zadania
    * Użyj daty/godziny **większej** niż wartość określona przez ostatni obiekt BLOB w sekwencji.
3. Obiekty BLOB danych referencyjnych **nie** są uporządkowane według czasu "Ostatnia modyfikacja" obiektu BLOB, ale tylko według czasu i daty określonej w nazwie obiektu BLOB przy użyciu podstawiania {date} i {Time}.
3. Aby uniknąć konieczności wyświetlania listy dużej liczby obiektów blob, należy rozważyć usunięcie bardzo starych obiektów blob, dla których przetwarzanie nie zostanie już wykonane. Należy pamiętać, że w przypadku programu ASA może wystąpić konieczność ponownego przetworzenia niewielkiej ilości w niektórych scenariuszach, takich jak ponowne uruchomienie.

## <a name="azure-sql-database"></a>Azure SQL Database

Dane referencyjne Azure SQL Database są pobierane przez zadanie Stream Analytics i są przechowywane jako migawka w pamięci do przetwarzania. Migawka danych referencyjnych jest również przechowywana w kontenerze na koncie magazynu określonym w ustawieniach konfiguracji. Kontener jest tworzony przy użyciu autostartu, gdy zadanie zostanie uruchomione. Jeśli zadanie zostanie zatrzymane lub przejdzie do stanu niepowodzenia, tworzone przez siebie kontenery zostaną usunięte po ponownym uruchomieniu zadania.  

Jeśli dane referencyjne są wolnie zmieniającymi się zestawami danych, należy okresowo odświeżyć migawkę używaną w danym zadaniu. Stream Analytics pozwala ustawić częstotliwość odświeżania podczas konfigurowania połączenia danych wejściowych Azure SQL Database. Środowisko uruchomieniowe Stream Analytics będzie wysyłać zapytania do Azure SQL Database w interwale określonym przez częstotliwość odświeżania. Częstotliwość odświeżania jest obsługiwana raz na minutę. Dla każdego odświeżenia Stream Analytics przechowuje nową migawkę w podanym koncie magazynu.

Stream Analytics dostępne są dwie opcje wykonywania zapytań dotyczących Azure SQL Database. Zapytanie migawki jest wymagane i musi zostać dołączone do każdego zadania. Stream Analytics uruchamia zapytanie migawek okresowo na podstawie interwału odświeżania i używa wyniku zapytania (migawka) jako zestawu danych referencyjnych. Zapytanie migawki powinno być zgodne z większością scenariuszy, ale w przypadku problemów z wydajnością z dużymi zestawami danych i szybkością szybkiego odświeżania można użyć opcji zapytania Delta. W przypadku zapytań, które mają więcej niż 60 sekund, aby zwrócić zestaw danych referencyjnych, spowoduje to przekroczenie limitu czasu.

Przy użyciu opcji zapytania Delta Stream Analytics początkowo uruchamia zapytanie migawki w celu pobrania zestawu danych referencyjnych punktu odniesienia. Po uruchomieniu Stream Analytics okresowo wykonywać zapytania Delta na podstawie interwału odświeżania, aby pobrać zmiany przyrostowe. Te zmiany przyrostowe są ciągle stosowane do zestawu danych referencyjnych, aby zachować jego aktualizację. Użycie zapytania różnicowego może pomóc w zmniejszeniu kosztów magazynu i operacji we/wy sieci.

### <a name="configure-sql-database-reference"></a>Skonfiguruj odwołanie SQL Database

Aby skonfigurować dane referencyjne SQL Database, musisz najpierw utworzyć dane **referencyjne** . W poniższej tabeli objaśniono każdą właściwość, która będzie potrzebna podczas tworzenia danych referencyjnych, z opisem. Aby uzyskać więcej informacji, zobacz [Korzystanie z danych referencyjnych z SQL Database dla zadania Azure Stream Analytics](sql-reference-data.md).

[Wystąpienia zarządzanego usługi Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) można użyć jako danych wejściowych referencyjnych. Należy [skonfigurować publiczny punkt końcowy w wystąpieniu zarządzanym SQL](../azure-sql/managed-instance/public-endpoint-configure.md) , a następnie ręcznie skonfigurować poniższe ustawienia w Azure Stream Analytics. Maszyna wirtualna platformy Azure z systemem SQL Server z dołączoną bazą danych jest również obsługiwana przez ręczne skonfigurowanie ustawień poniżej.

|**Nazwa właściwości**|**Opis**  |
|---------|---------|
|Alias danych wejściowych|Przyjazna nazwa, która zostanie użyta w zapytaniu zadania, aby odwołać się do tego danych wejściowych.|
|Subskrypcja|Wybierz subskrypcję|
|baza danych|Azure SQL Database, który zawiera dane referencyjne. W przypadku wystąpienia zarządzanego SQL wymagane jest określenie portu 3342. Na przykład *sampleserver. Public. Database. Windows. NET, 3342*|
|Nazwa użytkownika|Nazwa użytkownika skojarzona z Azure SQL Database.|
|Hasło|Hasło skojarzone z Twoim Azure SQL Database.|
|Odświeżaj okresowo|Ta opcja pozwala wybrać częstotliwość odświeżania. Wybranie opcji "włączone" pozwoli określić częstotliwość odświeżania w DD: HH: MM.|
|Zapytanie migawki|Jest to domyślna opcja zapytania, która pobiera dane referencyjne z SQL Database.|
|Zapytanie różnicowe|W przypadku zaawansowanych scenariuszy z dużymi zestawami danych i krótką częstotliwością odświeżania wybierz opcję dodania zapytania różnicowego.|

## <a name="size-limitation"></a>Ograniczenie rozmiaru

Zaleca się użycie zestawów danych referencyjnych o rozmiarze mniejszym niż 300 MB w celu uzyskania najlepszej wydajności. Zestawy danych referencyjnych 5 GB lub Lower są obsługiwane w zadaniach mających 6 usług SUs lub więcej. Korzystanie z bardzo dużych danych referencyjnych może mieć wpływ na kompleksowe opóźnienie zadania. W miarę wzrostu złożoności zapytania w celu uwzględnienia przetwarzania stanowego, takiego jak agregacje okienkowe, sprzężenia czasowe i funkcje analityczne czasowe, oczekuje się, że maksymalny obsługiwany rozmiar danych referencyjnych jest zmniejszany. Jeśli Azure Stream Analytics nie może załadować danych referencyjnych i wykonać złożonych operacji, zadanie wygaśnie za mało pamięci i zakończy się niepowodzeniem. W takich przypadkach Metryka użycia SU% osiągnie 100%.    

|**Liczba jednostek przesyłania strumieniowego**  |**Zalecany rozmiar**  |
|---------|---------|
|1   |50 MB lub niższy   |
|3   |150 MB lub niższy   |
|6 i więcej   |5 GB lub mniej.    |

Obsługa kompresji nie jest dostępna dla danych referencyjnych.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Sprzęganie wielu zestawów danych referencyjnych w zadaniu
W jednym kroku zapytania można przyłączyć tylko jedno dane wejściowe strumienia z jednym wejściem danych referencyjnych. Można jednak dołączyć wiele zestawów danych referencyjnych, dzieląc zapytanie na wiele kroków. Przykład przedstawiono poniżej.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>Zadania IoT Edge

Tylko lokalne dane referencyjne są obsługiwane na potrzeby zadań Stream Analytics Edge. Gdy zadanie zostanie wdrożone na urządzeniu IoT Edge, ładuje dane referencyjne ze ścieżki pliku zdefiniowanej przez użytkownika. Przygotuj plik danych referencyjnych na urządzeniu. W przypadku kontenera systemu Windows Umieść plik dane referencyjne na dysku lokalnym i Udostępnij dysk lokalny z kontenerem Docker. W przypadku kontenera systemu Linux Utwórz wolumin platformy Docker i uzupełnij plik danych do woluminu.

Dane referencyjne dotyczące aktualizacji IoT Edge są wyzwalane przez wdrożenie. Po wyzwoleniu moduł Stream Analytics pobiera zaktualizowane dane bez zatrzymywania uruchomionego zadania.

Istnieją dwa sposoby aktualizowania danych referencyjnych:

* Zaktualizuj ścieżkę danych referencyjnych w ramach zadania Stream Analytics z Azure Portal.

* Zaktualizuj wdrożenie IoT Edge.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
