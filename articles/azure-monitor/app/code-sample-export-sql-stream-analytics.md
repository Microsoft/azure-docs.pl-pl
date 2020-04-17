---
title: Eksportowanie do języka SQL z usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Ciągłe eksportowanie danych usługi Application Insights do programu SQL przy użyciu usługi Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: e67365038b9a481bc0cacf079e5d197cc3139a5f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536917"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Przewodnik: eksportowanie do programu SQL z usługi Application Insights przy użyciu usługi Stream Analytics
W tym artykule pokazano, jak przenieść dane telemetryczne z [usługi Azure Application Insights][start] do bazy danych SQL platformy Azure przy użyciu [ciągłego eksportu][export] i usługi Azure Stream [Analytics.](https://azure.microsoft.com/services/stream-analytics/) 

Eksport ciągły przenosi dane telemetryczne do usługi Azure Storage w formacie JSON. Będziemy analizować obiekty JSON przy użyciu usługi Azure Stream Analytics i tworzyć wiersze w tabeli bazy danych.

(Bardziej ogólnie, eksport ciągły jest sposobem, aby zrobić własną analizę telemetrii aplikacje wysyłają do usługi Application Insights. Można dostosować ten przykład kodu do innych czynności z eksportowanych danych telemetrycznych, takich jak agregacja danych.)

Zaczniemy od założenia, że masz już aplikację, którą chcesz monitorować.

W tym przykładzie będziemy używać danych widoku strony, ale ten sam wzorzec można łatwo rozszerzyć na inne typy danych, takie jak zdarzenia niestandardowe i wyjątki. 

## <a name="add-application-insights-to-your-application"></a>Dodawanie statystyk aplikacji do aplikacji
Aby rozpocząć:

1. [Skonfiguruj statystyki aplikacji dla stron internetowych](../../azure-monitor/app/javascript.md). 
   
    (W tym przykładzie skupimy się na przetwarzaniu danych widoku strony z przeglądarek klienckich, ale można również skonfigurować usługę Application Insights po stronie serwera [w języku Java](../../azure-monitor/app/java-get-started.md) lub [ASP.NET](../../azure-monitor/app/asp-net.md) aplikacji i żądania przetwarzania, zależności i innych danych telemetrycznych serwera).
2. Publikowanie aplikacji i oglądanie danych telemetrycznych wyświetlanych w zasobie usługi Application Insights.

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Ciągły eksport zawsze wyprowadza dane do konta usługi Azure Storage, więc musisz najpierw utworzyć magazyn.

1. Utwórz konto magazynu w ramach subskrypcji w [witrynie Azure portal][portal].
   
    ![W witrynie Azure portal wybierz pozycję Nowy, Dane, Magazyn. Wybierz pozycję Klasyczny, wybierz pozycję Utwórz. Podaj nazwę magazynu.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz pozycję Kontenery, kliknij kafelek Kontenery, a następnie dodaj](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Wkrótce będzie potrzebny do skonfigurowania danych wejściowych do usługi analizy strumienia.
   
    ![W magazynie otwórz okno Ustawienia, klawisze i zrób kopię podstawowego klucza dostępu](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpoczynanie ciągłego eksportowania do magazynu platformy Azure
1. W witrynie Azure portal przejdź do zasobu usługi Application Insights utworzonego dla aplikacji.
   
    ![Wybierz przeglądanie, wgląd w aplikacje, aplikację](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Utwórz ciągły eksport.
   
    ![Wybierz ustawienia, eksportu ciągłego, dodaj](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Wybierz konto magazynu utworzone wcześniej:

    ![Ustawianie miejsca docelowego eksportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybieranie typów zdarzeń](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Niech niektóre dane gromadzą się. Usiądź wygodnie i pozwól ludziom korzystać z aplikacji na chwilę. Dane telemetryczne pojawią się, a zobaczysz wykresy statystyczne w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md) i poszczególnych zdarzeniach w [wyszukiwaniu diagnostycznym.](../../azure-monitor/app/diagnostic-search.md) 
   
    A także dane zostaną wyeksportowane do magazynu. 
2. Sprawdź wyeksportowane dane w portalu — wybierz pozycję **Przeglądaj**, wybierz konto magazynu, a następnie **kontenery** — lub w programie Visual Studio. W programie Visual Studio wybierz pozycję **Wyświetl / Cloud Explorer**i otwórz platformę Azure / Storage. (Jeśli nie masz tej opcji menu, musisz zainstalować zestaw SDK platformy Azure: Otwórz okno dialogowe Nowy projekt i otwórz visual c# / chmurę / Pobierz zestaw Microsoft Azure SDK dla platformy .NET.)
   
    ![W programie Visual Studio otwórz przeglądarkę serwera, platformę Azure, magazyn](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Zanotuj wspólną część nazwy ścieżki, która pochodzi od nazwy aplikacji i klucza instrumentacji. 

Zdarzenia są zapisywane w plikach obiektów blob w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Dlatego chcielibyśmy odczytać dane zdarzenia i odfiltrować pola, które chcemy. Istnieją różne rodzaje rzeczy, które możemy zrobić z danymi, ale nasz plan dzisiaj jest użycie usługi Stream Analytics, aby przenieść dane do bazy danych SQL. To ułatwi uruchamianie wielu interesujących zapytań.

## <a name="create-an-azure-sql-database"></a>Tworzenie bazy danych Azure SQL Database
Po raz kolejny, począwszy od subskrypcji w [witrynie Azure portal][portal], utworzyć bazę danych (i nowy serwer, chyba że masz już jeden), do którego będziesz zapisywać dane.

![Nowy, Dane, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Upewnij się, że serwer bazy danych zezwala na dostęp do usług platformy Azure:

![Przeglądaj, Serwery, Serwer, Ustawienia, Zapora, Zezwalaj na dostęp do platformy Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Tworzenie tabeli w usłudze Azure SQL DB
Połącz się z bazą danych utworzoną w poprzedniej sekcji za pomocą preferowanego narzędzia do zarządzania. W tym instruktażu będziemy używać [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Utwórz nową kwerendę i wykonaj następujący T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

W tym przykładzie używamy danych z wyświetleń strony. Aby wyświetlić inne dostępne dane, sprawdź dane wyjściowe JSON i zobacz [model danych eksportu](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia usługi Azure Stream Analytics
Z [witryny Azure Portal](https://portal.azure.com/)wybierz usługę Azure Stream Analytics i utwórz nowe zadanie usługi Stream Analytics:

![Ustawienia analizy strumienia](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Po utworzeniu nowego zadania wybierz pozycję **Przejdź do zasobu**.

![Ustawienia analizy strumienia](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Dodawanie nowego wejścia

![Ustawienia analizy strumienia](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ustaw go do podjęcia danych wejściowych z obiektu blob eksportu ciągłego:

![Ustawienia analizy strumienia](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teraz będziesz potrzebować podstawowego klucza dostępu z konta magazynu, który został odnotowany wcześniej. Ustaw to jako klucz konta magazynu.

#### <a name="set-path-prefix-pattern"></a>Ustawianie wzorca prefiksu ścieżki

**Pamiętaj, aby ustawić format daty na YYYY-MM-DD (z myślnikami).**

Wzorzec prefiksu ścieżki określa sposób, w jaki usługa Stream Analytics znajduje pliki wejściowe w magazynie. Należy ustawić go, aby odpowiadać, jak eksport ciągły przechowuje dane. Ustaw to w ten sposób:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27`to nazwa zasobu usługi Application Insights, **a wszystko to w przypadku mniejszych liter**. 
* `1234...`jest kluczem instrumentacji zasobu usługi Application Insights **z usuniętymi myślnikami**. 
* `PageViews`to rodzaj danych, które chcemy przeanalizować. Dostępne typy zależą od filtru ustawionego w trybie eksportu ciągłego. Sprawdź wyeksportowane dane, aby wyświetlić inne dostępne typy, i zobacz [model danych eksportu](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}`jest wzorem napisanym dosłownie.

Aby uzyskać nazwę i klawiaturę zasobu usługi Application Insights, otwórz program Essentials na stronie przeglądu lub otwórz okno Ustawienia.

> [!TIP]
> Użyj sample funkcji, aby sprawdzić, czy ścieżka wprowadzania została ustawiona poprawnie. Jeśli to się nie powiedzie: Sprawdź, czy w magazynie znajdują się dane dla wybranego zakresu czasu próbki. Edytuj definicję danych wejściowych i sprawdź, czy poprawnie ustawiono konto magazynu, prefiks ścieżki i format daty.

 
## <a name="set-query"></a>Ustaw kwerendę
Otwórz sekcję kwerendy:

Zastąp kwerendę domyślną:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Należy zauważyć, że kilka pierwszych właściwości są specyficzne dla danych widoku strony. Eksport innych typów danych telemetrycznych będzie miał różne właściwości. Zobacz [szczegółowe odwołanie do modelu danych dla typów właściwości i wartości.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurowanie danych wyjściowych do bazy danych
Wybierz SQL jako dane wyjściowe.

![W analizie strumienia wybierz pozycję Dane wyjściowe](./media/code-sample-export-sql-stream-analytics/SA006.png)

Określ bazę danych SQL.

![Wprowadź szczegóły swojej bazy danych](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zamknij kreatora i poczekaj na powiadomienie o skonfigurowaniu danych wyjściowych.

## <a name="start-processing"></a>Rozpocznij przetwarzanie
Rozpocznij zadanie od paska akcji:

![W analizie strumienia kliknij przycisk Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Można wybrać, czy rozpocząć przetwarzanie danych począwszy od teraz, czy zacząć od wcześniejszych danych. Ten ostatni jest przydatny, jeśli miałeś ciągły eksport już uruchomiony przez jakiś czas.

Po kilku minutach wróć do narzędzia zarządzania programem SQL Server i obserwuj przepływające dane. Na przykład użyj kwerendy w ten sposób:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Pokrewne artykuły:
* [Eksportowanie do usługi Power BI przy użyciu usługi Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Szczegółowe odwołanie do modelu danych dla typów właściwości i wartości.](../../azure-monitor/app/export-data-model.md)
* [Ciągły eksport w usłudze Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

