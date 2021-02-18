---
title: Ciągły eksport danych telemetrycznych z Application Insights | Microsoft Docs
description: Wyeksportuj dane diagnostyczne i użycia do magazynu w Microsoft Azure i Pobierz je stamtąd.
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 23405faeb7d2151ce0f6492c0d522e0a7f9b84a8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584245"
---
# <a name="export-telemetry-from-application-insights"></a>Eksportowanie telemetrii z usługi Application Insights
Chcesz utrzymać dane telemetryczne dłużej niż w przypadku standardowego okresu przechowywania? Lub przetwarzać je w sposób wyspecjalizowany? Eksport ciągły jest idealnym rozwiązaniem. Zdarzenia wyświetlane w portalu Application Insights mogą zostać wyeksportowane do magazynu w Microsoft Azure w formacie JSON. Z tego miejsca możesz pobrać dane i napisać dowolny kod, który jest potrzebny do jego przetworzenia.  

> [!NOTE]
> Eksport ciągły jest obsługiwany tylko w przypadku klasycznych zasobów usługi Application Insights. [Zasoby usługi Application Insights na podstawie obszaru roboczego](./create-workspace-resource.md) muszą używać [ustawień diagnostycznych](./create-workspace-resource.md#export-telemetry).
>

Przed skonfigurowaniem eksportu ciągłego należy wziąć pod uwagę pewne alternatywy:

* Przycisk Eksportuj w górnej części metryk lub karty wyszukiwania umożliwia transfer tabel i wykresów do arkusza kalkulacyjnego programu Excel.

* [Analiza](../logs/log-query-overview.md) zapewnia zaawansowany język zapytań na potrzeby telemetrii. Może również eksportować wyniki.
* Jeśli chcesz [eksplorować dane w Power BI](./export-power-bi.md), możesz to zrobić bez korzystania z eksportu ciągłego.
* [Interfejs API REST dostępu do danych](https://dev.applicationinsights.io/) umożliwia programistyczne uzyskiwanie dostępu do telemetrii.
* Możesz również uzyskać dostęp do ustawień [eksport ciągły za pomocą programu PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Gdy eksport ciągły kopiuje dane do magazynu (o ile będzie to możliwe), nadal jest dostępny w Application Insights w przypadku normalnego [okresu przechowywania](./data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Konfiguracja ciągłego magazynu zaawansowanego eksportu

Eksport ciągły **nie obsługuje** następujących funkcji/konfiguracji usługi Azure Storage:

* Korzystanie z [zapór sieci wirtualnej/Azure Storage](../../storage/common/storage-network-security.md) w połączeniu z usługą Azure Blob Storage.

* [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Tworzenie eksportu ciągłego

> [!NOTE]
> Aplikacja nie może eksportować więcej niż NADMIAROWE danych dziennie. Jeśli eksportowany jest ponad NADMIAROWE na dzień, eksport zostanie wyłączony. Aby wyeksportować bez limitu, użyj [eksportu ustawień diagnostycznych](#diagnostic-settings-based-export).

1. W Application Insights zasób dla aplikacji w obszarze Konfigurowanie po lewej stronie Otwórz eksport ciągły i wybierz polecenie **Dodaj**:

2. Wybierz typy danych telemetrycznych, które chcesz wyeksportować.

3. Utwórz lub wybierz [konto usługi Azure Storage](../../storage/common/storage-introduction.md) , na którym chcesz przechowywać dane. Aby uzyskać więcej informacji na temat opcji cennika magazynu, odwiedź [oficjalną stronę cennika](https://azure.microsoft.com/pricing/details/storage/).

     Kliknij przycisk Dodaj, Eksportuj miejsce docelowe, konto magazynu, a następnie utwórz nowy magazyn lub wybierz istniejący magazyn.

    > [!Warning]
    > Domyślnie lokalizacja magazynu zostanie ustawiona na ten sam region geograficzny co zasób Application Insights. Jeśli przechowujesz w innym regionie, mogą zostać naliczone opłaty za transfer.

4. Utwórz lub wybierz kontener w magazynie.

> [!NOTE]
> Po utworzeniu eksportu nowo wprowadzone dane zaczynają przepływać do usługi Azure Blob Storage. Eksport ciągły będzie przesyłał tylko nowe dane telemetryczne, które zostały utworzone/pozyskane po włączeniu eksportu ciągłego. Wszelkie dane, które istniały przed włączeniem eksportu ciągłego, nie będą eksportowane i nie ma żadnego sposobu na wsteczne wyeksportowanie wcześniej utworzonych danych przy użyciu eksportu ciągłego.

Dane w magazynie mogą być opóźnione o godzinę.

Po zakończeniu pierwszego eksportu znajdziesz strukturę podobną do następującej w kontenerze usługi Azure Blob Storage: (będzie to się różnić w zależności od zbieranych danych).

|Nazwa | Opis |
|:----|:------|
| [Dostępność](export-data-model.md#availability) | Raportuje [testy sieci Web dostępności](./monitor-web-app-availability.md).  |
| [Zdarzenie](export-data-model.md#events) | Zdarzenia niestandardowe wygenerowane przez [poleceń trackEvent ()](./api-custom-events-metrics.md#trackevent). 
| [Wyjątki](export-data-model.md#exceptions) |Zgłasza [wyjątki](./asp-net-exceptions.md) na serwerze i w przeglądarce.
| [Komunikaty](export-data-model.md#trace-messages) | Wysyłane przez [TrackTrace](./api-custom-events-metrics.md#tracktrace)i [karty rejestrowania](./asp-net-trace-logs.md).
| [Metryki](export-data-model.md#metrics) | Generowane przez wywołania interfejsu API metryk.
| [Liczniki wydajności](export-data-model.md) | Liczniki wydajności zbierane przez Application Insights.
| [Żądania](export-data-model.md#requests)| Wysłane przez [TrackRequest](./api-custom-events-metrics.md#trackrequest). Moduły standardowe używają tego do raportowania czasu odpowiedzi serwera, mierzoną na serwerze.| 

### <a name="to-edit-continuous-export"></a>Aby edytować eksport ciągły

Kliknij pozycję eksport ciągły i wybierz konto magazynu do edycji.

### <a name="to-stop-continuous-export"></a>Aby zatrzymać eksport ciągły

Aby zatrzymać eksport, kliknij przycisk Wyłącz. Kliknięcie przycisku Włącz ponownie spowoduje ponowne uruchomienie eksportu z nowymi danymi. Nie zostaną pobrane dane, które dotarły do portalu, podczas gdy eksport został wyłączony.

Aby zatrzymać eksport trwale, usuń go. Wykonanie tej czynności nie powoduje usunięcia danych z magazynu.

### <a name="cant-add-or-change-an-export"></a>Nie można dodać lub zmienić eksportu?
* Aby dodać lub zmienić eksporty, musisz mieć prawa dostępu Właściciel, Współautor lub Współautor w usłudze Application Insights. [Poznaj role][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Jakie zdarzenia są uzyskiwane?
Wyeksportowane dane są nieoczyszczoną telemetrią otrzymywaną z aplikacji, z tą różnicą, że dodawane są dane lokalizacji, które są obliczane na podstawie adresu IP klienta.

Dane, które zostały odrzucone przez [pobranie próbek](./sampling.md) nie są uwzględniane w wyeksportowanych danych.

Inne metryki obliczeniowe nie są uwzględniane. Na przykład nie eksportuje średniego użycia procesora CPU, ale eksportuje się nieprzetworzoną telemetrię, z której jest obliczana średnia.

Dane obejmują również wyniki wszelkich skonfigurowanych [testów dostępności sieci Web](./monitor-web-app-availability.md) .

> [!NOTE]
> **Sond.** Jeśli Twoja aplikacja wysyła dużo danych, funkcja próbkowania może działać i wysyłać tylko część wygenerowanej telemetrii. [Dowiedz się więcej na temat próbkowania.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Inspekcja danych
Magazyn można sprawdzić bezpośrednio w portalu. Kliknij pozycję Strona główna w menu z lewej strony, w górnej części strony "usługi platformy Azure" Wybierz pozycję **konta magazynu**, wybierz nazwę konta magazynu, na stronie Przegląd wybierz pozycję **obiekty blob** w obszarze usługi, a następnie wybierz nazwę kontenera.

Aby sprawdzić usługę Azure Storage w programie Visual Studio, Otwórz **Widok**, **Eksplorator chmury**. (Jeśli nie masz tego polecenia menu, musisz zainstalować zestaw Azure SDK: Otwórz okno dialogowe **Nowy projekt** , rozwiń pozycję Visual C#/Cloud i wybierz polecenie **Pobierz zestaw Microsoft Azure SDK dla platformy .NET**.)

Po otwarciu magazynu obiektów BLOB zobaczysz kontener z zestawem plików obiektów BLOB. Identyfikator URI każdego pliku pochodzącego z nazwy zasobu Application Insights, jego klucza instrumentacji, typu telemetrii/daty/godziny. (Nazwa zasobu jest mała, a klucz Instrumentacji pomija łączniki).

![Sprawdzanie magazynu obiektów BLOB przy użyciu odpowiedniego narzędzia](./media/export-telemetry/04-data.png)

Data i godzina są UTC i są, gdy dane telemetryczne zostały zdeponowane w sklepie — nie czas, który został wygenerowany. Dlatego jeśli napiszesz kod w celu pobrania danych, może on być przenoszony liniowo przez dane.

Oto postać ścieżki:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Lokalizacja

* `blobCreationTimeUtc` to czas, w którym obiekt BLOB został utworzony w wewnętrznym magazynie przemieszczania
* `blobDeliveryTimeUtc` to czas, po jakim obiekt BLOB jest kopiowany do magazynu docelowego eksportu

## <a name="data-format"></a><a name="format"></a> Format danych
* Każdy obiekt BLOB jest plikiem tekstowym zawierającym wiele wierszy "\n", które są oddzielone. Zawiera dane telemetryczne przetwarzane w przedziale czasu wynoszącym około pół minuty.
* Każdy wiersz reprezentuje punkt danych telemetrii, taki jak żądanie lub widok strony.
* Każdy wiersz jest niesformatowanym dokumentem JSON. Jeśli chcesz wyświetlić wiersze, Otwórz obiekt BLOB w programie Visual Studio i wybierz polecenie **Edytuj**  >    >  **plik formatu** zaawansowanego:

   ![Wyświetlanie telemetrii za pomocą odpowiedniego narzędzia](./media/export-telemetry/06-json.png)

Czas trwania jest w taktach, gdzie 10 000 Takty = 1 ms. Na przykład te wartości przedstawiają czas 1 ms, aby wysłać żądanie z przeglądarki, 3 MS w celu otrzymania go i 1,8 s do przetworzenia strony w przeglądarce:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Szczegółowe informacje o modelu danych dla typów i wartości właściwości.](export-data-model.md)

## <a name="processing-the-data"></a>Przetwarzanie danych
Na małą skalę można napisać kod, aby ściągnąć dane, odczytać je w arkuszu kalkulacyjnym itd. Na przykład:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Aby uzyskać większy przykład kodu, zobacz [Używanie roli proces roboczy][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Usuń stare dane
W razie potrzeby użytkownik jest odpowiedzialny za zarządzanie pojemnością magazynu i usunięciem starych danych.

## <a name="if-you-regenerate-your-storage-key"></a>W przypadku ponownego wygenerowania klucza magazynu...
Jeśli zmienisz klucz do magazynu, eksport ciągły przestanie działać. Na Twoim koncie platformy Azure zostanie wyświetlone powiadomienie.

Otwórz kartę Eksport ciągły i edytuj eksport. Edytuj Miejsce docelowe eksportu, ale po prostu pozostaw wybrany ten sam magazyn. Kliknij przycisk OK, aby potwierdzić.

Eksport ciągły zostanie uruchomiony ponownie.

## <a name="export-samples"></a>Eksportuj przykłady

* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Przykład Stream Analytics 2](export-stream-analytics.md)

W przypadku większych skal należy wziąć pod uwagę klastry usługi [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop w chmurze. Usługa HDInsight oferuje różne technologie umożliwiające zarządzanie i analizowanie danych Big Data oraz używanie ich do przetwarzania danych, które zostały wyeksportowane z Application Insights.

## <a name="q--a"></a>Pytania i odpowiedzi
* *Ale wszystko, czego chcę, jest jednorazowym pobraniem wykresu.*  

    Tak, możesz to zrobić. W górnej części karty kliknij pozycję **Eksportuj dane**.
* *Skonfiguruję eksport, ale nie ma żadnych danych w sklepie.*

    Czy Application Insights odbierać dane telemetryczne z aplikacji od momentu skonfigurowania eksportu? Otrzymasz tylko nowe dane.
* *Podjęto próbę skonfigurowania eksportu, ale odmówiono dostępu*

    Jeśli konto należy do organizacji, musisz być członkiem grupy Właściciele lub Współautorzy.
* *Czy mogę wyeksportować prosty do własnego magazynu lokalnego?*

    Nie, przepraszamy. Nasz aparat eksportu obecnie działa tylko z usługą Azure Storage.  
* *Czy istnieje ograniczenie ilości danych, które zostały umieszczone w moim sklepie?*

    Nie. Będziemy utrzymywać dane wypychane do momentu usunięcia eksportu. Jeśli osiągnięto limity zewnętrzne usługi BLOB Storage, zatrzymamy się, ale jest to dość ogromne. Pozwala to kontrolować ilość używanej pamięci masowej.  
* *Ile obiektów BLOB powinna się znaleźć w magazynie?*

  * Dla każdego typu danych, który został wybrany do eksportowania, co minutę tworzony jest nowy obiekt BLOB (Jeśli dane są dostępne).
  * Ponadto w przypadku aplikacji o dużym natężeniu ruchu są przydzielone dodatkowe jednostki partycji. W takim przypadku każda jednostka tworzy obiekt BLOB co minutę.
* *Ponownie wygenerowano klucz do magazynu lub zmieniono nazwę kontenera, a teraz eksportowanie nie działa.*

    Edytuj eksport i Otwórz kartę eksport docelowy. Pozostaw ten sam magazyn wybrany jak wcześniej, a następnie kliknij przycisk OK, aby potwierdzić. Eksport zostanie uruchomiony ponownie. Jeśli zmiana była w ciągu ostatnich kilku dni, utracisz dane.
* *Czy mogę wstrzymać eksport?*

    Tak. Kliknij przycisk Wyłącz.

## <a name="code-samples"></a>Przykłady kodu

* [Przykład Stream Analytics](export-stream-analytics.md)
* [Eksportowanie do bazy danych SQL przy użyciu usługi Stream Analytics][exportasa]
* [Szczegółowe informacje o modelu danych dla typów i wartości właściwości.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Eksport oparty na ustawieniach diagnostycznych

Eksport oparty na ustawieniach diagnostycznych używa innego schematu niż eksport ciągły. Obsługuje ona również funkcje, które nie są eksportowane w sposób ciągły:

* Konta usługi Azure Storage z siecią wirtualną, zaporami i łączami prywatnymi.
* Eksportuj do centrum zdarzeń.

Aby przeprowadzić migrację na eksport oparty na ustawieniach diagnostycznych:

1. Wyłącz bieżący eksport ciągły.
2. [Migrowanie aplikacji do obszaru roboczego](convert-classic-resource.md).
3. [Włącz eksport ustawień diagnostycznych](create-workspace-resource.md#export-telemetry). Wybierz pozycję **Ustawienia diagnostyczne > dodać ustawienia diagnostycznego** z poziomu zasobu Application Insights.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

