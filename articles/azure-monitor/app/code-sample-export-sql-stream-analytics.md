---
title: Eksportowanie do bazy danych SQL z platformy Azure Application Insights | Microsoft Docs
description: Ciągle Eksportuj dane Application Insights do bazy danych SQL przy użyciu Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 25d28bb0fc35ef76231c085dc1d9d8a1234a264c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587750"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Przewodnik: Eksportowanie do bazy danych SQL z Application Insights przy użyciu Stream Analytics
W tym artykule przedstawiono sposób przenoszenia danych telemetrycznych z [usługi Azure Application Insights][start] do Azure SQL Database za pomocą [eksportu ciągłego][export] i [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Eksport ciągły przenosi dane telemetryczne do usługi Azure Storage w formacie JSON. Przeanalizuje obiekty JSON przy użyciu Azure Stream Analytics i utworzysz wiersze w tabeli bazy danych.

(Zazwyczaj eksport ciągły jest sposobem na przeprowadzenie własnej analizy danych telemetrycznych wysyłanych przez aplikacje do Application Insights. Możesz dostosować ten przykład kodu, aby wykonać inne czynności z wyeksportowaną telemetrią, taką jak agregacja danych.

Zaczniemy od założenia, że aplikacja ma być monitorowana.

W tym przykładzie będziemy używać danych widoku strony, ale ten sam wzorzec można łatwo rozszerzyć na inne typy danych, takie jak zdarzenia niestandardowe i wyjątki. 

## <a name="add-application-insights-to-your-application"></a>Dodawanie Application Insights do aplikacji
Aby rozpocząć:

1. [Skonfiguruj Application Insights dla stron sieci Web](./javascript.md). 
   
    (W tym przykładzie będziemy skupić się na przetwarzaniu danych widoku strony z przeglądarek klienta, ale można również skonfigurować Application Insights po stronie serwera aplikacji [Java](./java-get-started.md) lub [ASP.NET](./asp-net.md) oraz żądania przetwarzania, zależności i innych danych telemetrycznych serwera).
2. Opublikuj aplikację i obejrzyj dane telemetryczne wyświetlane w zasobie Application Insights.

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze wyprowadza dane do konta usługi Azure Storage, dlatego należy najpierw utworzyć magazyn.

1. Utwórz konto magazynu w ramach subskrypcji w [Azure Portal][portal].
   
    ![W Azure Portal wybierz kolejno pozycje nowy, dane i magazyn. Wybierz pozycję klasyczny, a następnie wybierz pozycję Utwórz. Podaj nazwę magazynu.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tworzenie kontenera
   
    ![W obszarze nowy magazyn wybierz pozycję kontenery, kliknij kafelek kontenery, a następnie pozycję Dodaj.](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Będzie to wkrótce potrzebne do skonfigurowania danych wejściowych usługi Stream Analytics.
   
    ![W obszarze magazyn Otwórz pozycję Ustawienia, klucze i wykonaj kopię podstawowego klucza dostępu](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocznij eksport ciągły do usługi Azure Storage
1. W Azure Portal przejdź do zasobu Application Insights utworzonego dla aplikacji.
   
    ![Wybierz kolejno opcje Przeglądaj, Application Insights, aplikacja](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Utwórz eksport ciągły.
   
    ![Wybieranie ustawień, eksport ciągły, Dodawanie](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Wybierz utworzone wcześniej konto magazynu:

    ![Ustaw miejsce docelowe eksportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybieranie typów zdarzeń](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Umożliwia gromadzenie danych. Powróć i pozwól, aby użytkownicy korzystali z aplikacji przez pewien czas. Dane telemetryczne będą dostępne, a wykresy statystyczne są wyświetlane w [Eksploratorze metryk](../essentials/metrics-charts.md) i w poszczególnych zdarzeniach w [przeszukiwaniu diagnostycznym](./diagnostic-search.md). 
   
    Ponadto dane zostaną wyeksportowane do magazynu. 
2. Sprawdź wyeksportowane dane w portalu — wybierz pozycję **Przeglądaj**, wybierz konto magazynu, a następnie **kontenery** — lub w programie Visual Studio. W programie Visual Studio wybierz pozycję **Widok/Eksplorator chmury** i Otwórz pozycję Azure/Storage. (Jeśli nie masz tej opcji menu, musisz zainstalować zestaw Azure SDK: Otwórz okno dialogowe Nowy projekt i otwórz Visual C#/Cloud/Get Zestaw Microsoft Azure SDK dla platformy .NET).
   
    ![W programie Visual Studio Otwórz przeglądarkę serwera, platformę Azure, magazyn](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Zanotuj wspólną część nazwy ścieżki, która jest pochodną nazwy aplikacji i klucza Instrumentacji. 

Zdarzenia są zapisywane w plikach obiektów BLOB w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Więc chcemy przeczytać dane zdarzenia i odfiltrować pola, które chcemy. Istnieją wszystkie rodzaje rzeczy, które możemy zrobić z danymi, ale naszym planem jest użycie Stream Analytics do przenoszenia danych do SQL Database. Dzięki temu będzie można łatwo uruchamiać wiele interesujących zapytań.

## <a name="create-an-azure-sql-database"></a>Tworzenie usługi Azure SQL Database
Po ponownym uruchomieniu z subskrypcji w [Azure Portal][portal]Utwórz bazę danych (i nowy serwer, chyba że już nie masz takiego komputera), na którym chcesz napisać dane.

![Nowe, dane, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Upewnij się, że serwer zezwala na dostęp do usług platformy Azure:

![Przeglądanie, serwery, serwer, ustawienia, Zapora, zezwalanie na dostęp do platformy Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Tworzenie tabeli w Azure SQL Database
Nawiąż połączenie z bazą danych utworzoną w poprzedniej sekcji za pomocą preferowanego narzędzia do zarządzania. W tym instruktażu będziemy używać [SQL Server Management Tools](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

![Łączenie z bazą danych Azure SQL Database](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Utwórz nowe zapytanie i wykonaj następujące polecenie T-SQL:

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

![Utwórz PageViewsTable](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

W tym przykładzie używamy danych z widoków strony. Aby zobaczyć inne dostępne dane, zbadaj dane wyjściowe JSON i zobacz [Eksportuj model danych](./export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia Azure Stream Analytics
W [Azure Portal](https://portal.azure.com/)wybierz usługę Azure Stream Analytics i Utwórz nowe zadanie Stream Analytics:

![Zrzut ekranu przedstawia stronę zadania usługi Stream Analytics z wyróżnionym przyciskiem Utwórz.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Nowe zadanie usługi Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA002.png)

Po utworzeniu nowego zadania wybierz pozycję **Przejdź do zasobu**.

![Zrzut ekranu przedstawia komunikat wdrożenia zakończony pomyślnie i przejdź do przycisku zasobów.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![Zrzut ekranu przedstawia stronę dane wejściowe z wybranym przyciskiem Dodaj.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ustaw, aby pobierał dane wejściowe z obiektu BLOB ciągłego eksportu:

![Zrzut ekranu przedstawia nowe okno wprowadzania z wybranymi opcjami w menu rozwijanym alias, źródło i konto magazynu.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teraz potrzebny jest podstawowy klucz dostępu z konta magazynu, który został zanotowany wcześniej. Ustaw tę wartość jako klucz konta magazynu.

#### <a name="set-path-prefix-pattern"></a>Ustaw wzorzec prefiksu ścieżki

**Upewnij się, że ustawiono format daty RRRR-MM-DD (z kreskami).**

Wzorzec prefiksu ścieżki Określa, w jaki sposób Stream Analytics znajdować pliki wejściowe w magazynie. Należy ustawić tę wartość, aby odpowiadała, jak eksport ciągły przechowuje dane. Ustaw go następująco:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

W tym przykładzie:

* `webapplication27` jest nazwą zasobu Application Insights, **w małych przypadkach**. 
* `1234...` jest kluczem Instrumentacji zasobu Application Insights **z usuniętymi kreskami**. 
* `PageViews` to typ danych, które chcemy analizować. Dostępne typy zależą od filtru ustawionego w ramach eksportu ciągłego. Sprawdź wyeksportowane dane, aby zobaczyć inne dostępne typy i zobacz [Eksportuj model danych](./export-data-model.md).
* `/{date}/{time}` jest wzorcem zapisanym dosłownie.

Aby uzyskać nazwę i iKey zasobu Application Insights, Otwórz program Essentials na stronie przeglądu lub Otwórz pozycję Ustawienia.

> [!TIP]
> Użyj funkcji Sample, aby sprawdzić, czy ścieżka wejściowa została ustawiona poprawnie. Jeśli to się nie powiedzie: Sprawdź, czy w magazynie znajdują się dane dla wybranego zakresu czasu. Edytuj definicję danych wejściowych i sprawdź poprawność ustawień konta magazynu, prefiksu ścieżki i formatu daty.

 
## <a name="set-query"></a>Ustaw zapytanie
Otwórz sekcję zapytania:

Zamień domyślne zapytanie na:

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

Zauważ, że pierwsze kilka właściwości jest specyficznych dla danych widoku strony. Eksporty innych typów telemetrii będą mieć różne właściwości. Zobacz [szczegółowe informacje o modelu danych dla typów i wartości właściwości.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Skonfiguruj dane wyjściowe do bazy danych
Wybierz pozycję SQL jako dane wyjściowe.

![W usłudze Stream Analytics wybierz pozycję dane wyjściowe.](./media/code-sample-export-sql-stream-analytics/SA006.png)

Określ bazę danych.

![Wprowadź szczegóły bazy danych](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zamknij kreatora i poczekaj na powiadomienie, że dane wyjściowe zostały skonfigurowane.

## <a name="start-processing"></a>Rozpocznij przetwarzanie
Uruchom zadanie na pasku akcji:

![W usłudze Stream Analytics kliknij pozycję Uruchom.](./media/code-sample-export-sql-stream-analytics/SA008.png)

Możesz wybrać, czy chcesz rozpocząć przetwarzanie danych od razu, czy zacząć od wcześniejszych danych. Ta ostatnia jest przydatna, jeśli masz już uruchomiony eksport ciągły przez pewien czas.

Po kilku minutach Wróć do SQL Server Management Tools i obejrzyj dane przepływające w programie. Na przykład użyj zapytania w następujący sposób:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Pokrewne artykuły:
* [Eksportuj do Power BI przy użyciu Stream Analytics](./export-power-bi.md)
* [Szczegółowe informacje o modelu danych dla typów i wartości właściwości.](./export-data-model.md)
* [Eksport ciągły w Application Insights](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

