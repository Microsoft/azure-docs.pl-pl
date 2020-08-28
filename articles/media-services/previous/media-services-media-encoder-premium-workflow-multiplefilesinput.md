---
title: Wiele plików wejściowych i właściwości składników z koderem Premium Encoder — Azure | Microsoft Docs
description: W tym temacie wyjaśniono, jak używać setRuntimeProperties do używania wielu plików wejściowych i przekazywania danych niestandardowych do procesora multimediów Media Encoder Premium Workflow.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 70acbb239df3806437127e41510bfebc1358532a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015972"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Korzystanie z wielu plików wejściowych i właściwości składników przy użyciu kodera Premium
## <a name="overview"></a>Omówienie
Istnieją scenariusze, w których może być konieczne dostosowanie właściwości składnika, określenie zawartości XML listy klipów lub wysłanie wielu plików wejściowych podczas przesyłania zadania z procesorem multimediów **Media Encoder Premium Workflow** . Przykłady to:

* Nałóż tekst wideo i ustawiając wartość tekstową (na przykład bieżącą datę) w czasie wykonywania dla każdego wejściowego wideo.
* Dostosowywanie pliku XML listy klipów (w celu określenia jednego lub kilku plików źródłowych, z lub bez przycinania itp.).
* Nałożenie obrazu logo na wejściowym wideo podczas kodowania wideo.
* Kodowanie wielu języków audio.

Aby **Media Encoder Premium Workflow** wiedzieć, że zmieniasz niektóre właściwości w przepływie pracy podczas tworzenia zadania lub wysyłania wielu plików wejściowych, musisz użyć ciągu konfiguracji, który zawiera **setRuntimeProperties** i/lub **transcodeSource**. W tym temacie wyjaśniono, jak z nich korzystać.

## <a name="configuration-string-syntax"></a>Składnia ciągu konfiguracji
Ciąg konfiguracji do ustawienia w zadaniu kodowania używa dokumentu XML, który wygląda następująco:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Poniżej znajduje się kod C#, który odczytuje konfigurację XML z pliku, aktualizuje ją przy użyciu właściwej nazwy pliku wideo i przekazuje ją do zadania w zadaniu:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Dostosowywanie właściwości składnika
### <a name="property-with-a-simple-value"></a>Właściwość o prostej wartości
W niektórych przypadkach warto dostosować Właściwość składnika wraz z plikiem przepływu pracy, który ma być wykonywany przez Media Encoder Premium Workflow.

Załóżmy, że zaprojektowano przepływ pracy, który nakłada tekst na wideo, a tekst (na przykład bieżącą datę) ma być ustawiony w czasie wykonywania. Można to zrobić, wysyłając tekst do ustawienia jako nową wartość właściwości text składnika nakładki z zadania kodowania. Za pomocą tego mechanizmu można zmienić inne właściwości składnika w przepływie pracy (na przykład położenie lub kolor nakładki, szybkość transmisji bitów AVC itp.).

**setRuntimeProperties** służy do przesłonięcia właściwości w składnikach przepływu pracy.

Przykład:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Właściwość z wartością XML
Aby ustawić właściwość, która oczekuje wartości XML, Hermetyzuj przy użyciu `<![CDATA[ and ]]>` .

Przykład:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Upewnij się, że nie należy umieszczać powrotu karetki tuż po `<![CDATA[` .

### <a name="propertypath-value"></a>propertyPath wartość
W poprzednich przykładach propertyPath była "/Media pliku Input/filename" lub "/inactiveTimeout" lub "clipListXml".
Jest to, ogólnie rzecz biorąc, nazwa składnika, a następnie nazwa właściwości. Ścieżka może zawierać więcej lub mniej poziomów, takich jak "/primarySourceFile" (ponieważ właściwość znajduje się w katalogu głównym przepływu pracy) lub "/video processing/grafika/krycie" (ponieważ nakładka znajduje się w grupie).    

Aby sprawdzić ścieżkę i nazwę właściwości, użyj przycisku akcji, który znajduje się bezpośrednio obok każdej właściwości. Możesz kliknąć przycisk akcji i wybrać pozycję **Edytuj**. Spowoduje to wyświetlenie rzeczywistej nazwy właściwości i bezpośrednio nad nią, przestrzeni nazw.

![Akcja/Edycja](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Właściwość](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Wiele plików wejściowych
Każde zadanie przesyłane do **Media Encoder Premium Workflow** wymaga dwóch zasobów:

* Pierwszy z nich jest *zasobem przepływu pracy* , który zawiera plik przepływu pracy. Pliki przepływu pracy można zaprojektować za pomocą [Projektant przepływu pracy](media-services-workflow-designer.md).
* Drugi z nich jest *zasobem multimedialnym* zawierającym pliki multimedialne, które mają zostać zakodowane.

W przypadku wysyłania wielu plików multimedialnych do kodera **Media Encoder Premium Workflow** są stosowane następujące ograniczenia:

* Wszystkie pliki multimedialne muszą znajdować się w tym samym elemencie zawartości *multimedialnej*. Używanie wielu zasobów multimedialnych nie jest obsługiwane.
* Należy ustawić plik podstawowy w tym nośniku zawartości (najlepiej jest to główny plik wideo, do którego koder ma być przetwarzany).
* Konieczne jest przekazanie danych konfiguracyjnych obejmujących element **setRuntimeProperties** i/lub **transcodeSource** do procesora.
  * **setRuntimeProperties** służy do przesłonięcia właściwości filename lub innej właściwości w składnikach przepływu pracy.
  * **transcodeSource** służy do określania zawartości XML listy klipów.

Połączenia w przepływie pracy:

* Jeśli używasz jednego lub kilku składników danych wejściowych plików multimedialnych i planujesz użyć **setRuntimeProperties** , aby określić nazwę pliku, nie łącz do nich numeru PIN podstawowego składnika pliku. Upewnij się, że nie ma połączenia między obiektem pliku podstawowego i danymi wejściowymi pliku multimedialnego.
* Jeśli wolisz używać XML listy klipów i jednego składnika źródłowego nośnika, możesz połączyć oba jednocześnie.

![Brak połączenia z podstawowego pliku źródłowego do wejścia pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Nie ma połączenia z pliku podstawowego do składników wejściowych plików multimedialnych, jeśli używasz setRuntimeProperties do ustawiania właściwości filename.*

![Połączenie z listy klipów XML do źródła listy klipów](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Możesz połączyć plik XML z listą klipów ze źródłem multimediów i użyć transcodeSource.*

### <a name="clip-list-xml-customization"></a>Dostosowanie XML listy klipów
Możesz określić plik XML listy klipów w przepływie pracy w czasie wykonywania przy użyciu **transcodeSource** w formacie XML ciągu konfiguracji. Wymaga to połączenia kodu XML z listą wycinków ze składnikiem źródłowym nośnika w przepływie pracy.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Jeśli chcesz określić/primarySourceFile do używania tej właściwości do nazywania plików wyjściowych za pomocą wyrażenia "Expressions", zalecamy przekazanie pliku XML listy klipów jako właściwości *po* właściwości/primarySourceFile, aby uniknąć konieczności przesłania listy klipów przez ustawienie/primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Z dodatkowym przycinaniem dokładnej ramki:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Przykład 1: nakładanie obrazu na obraz wideo

### <a name="presentation"></a>Prezentacja
Rozważmy przykład, w którym chcesz nałożyć obraz logo na wejściowy film wideo podczas kodowania wideo. W tym przykładzie wejściowy film wideo ma nazwę "Microsoft_HoloLens_Possibilities_816p24.mp4", a logo ma nazwę "logo.png". Należy wykonać następujące czynności:

* Utwórz zasób przepływu pracy przy użyciu pliku przepływu pracy (zobacz Poniższy przykład).
* Utwórz zasób multimedialny, który zawiera dwa pliki: MyInputVideo.mp4 jako plik podstawowy i MyLogo.png.
* Wyślij zadanie do Media Encoder Premium Workflow procesora multimediów z powyższymi zasobami wejściowymi i określ następujący ciąg konfiguracyjny.

Konfiguracja:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

W powyższym przykładzie nazwa pliku wideo jest wysyłana do składnika danych wejściowych plików multimedialnych i właściwości primarySourceFile. Nazwa pliku logo jest wysyłana do innego danych wejściowych pliku multimedialnego, który jest połączony z składnikiem nakładki grafiki.

> [!NOTE]
> Nazwa pliku wideo jest wysyłana do właściwości primarySourceFile. Przyczyną tego problemu jest użycie tej właściwości w przepływie pracy w celu utworzenia prawidłowej nazwy pliku wyjściowego przy użyciu wyrażeń, na przykład.

### <a name="step-by-step-workflow-creation"></a>Tworzenie przepływu pracy krok po kroku
Poniżej przedstawiono procedurę tworzenia przepływu pracy, który pobiera dwa pliki jako dane wejściowe: wideo i obraz. Obraz zostanie nałożony na wierzchu wideo.

Otwórz **Projektant przepływu pracy** i wybierz pozycję **plik**  >  **Nowy obszar roboczy**  >  **transkodowanie plan**.

Nowy przepływ pracy pokazuje trzy elementy:

* Podstawowy plik źródłowy
* Lista klipów XML
* Plik wyjściowy/zasób  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nowy przepływ pracy kodowania*

Aby zaakceptować plik nośnika wejściowego, Zacznij od dodania składnika danych wejściowych pliku multimedialnego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania repozytorium i przeciągnij żądany wpis do okienka projektanta.

Następnie Dodaj plik wideo, który będzie używany do projektowania przepływu pracy. Aby to zrobić, kliknij okienko tło w Projektant przepływu pracy i Wyszukaj podstawową właściwość pliku źródłowego w okienku właściwości po prawej stronie. Kliknij ikonę folderu i wybierz odpowiedni plik wideo.

![Podstawowe źródło plików](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Podstawowe źródło plików*

Następnie określ plik wideo w składniku wejściowym pliku multimedialnego.   

![Źródło danych wejściowych pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Źródło danych wejściowych pliku multimedialnego*

Gdy tylko zostanie to zrobione, składnik wejściowy plików multimedialnych sprawdzi plik i zapełni jego numery PIN, aby odzwierciedlić plik, który sprawdził.

Następnym krokiem jest dodanie "Aktualizator danych typu wideo", aby określić przestrzeń kolorów do Rec. 709. Dodaj "konwerter formatu wideo", który jest ustawiony na układ danych/Typ układu = planarny element. Spowoduje to przekonwertowanie strumienia wideo do formatu, który może być traktowany jako źródło składnika nakładki.

![Aktualizator i konwerter formatów typu danych wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Aktualizator i konwerter formatów typu danych wideo*

![Typ układu = planarna konfigurowalna](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ układu jest konfigurowalny planarny*

Następnie Dodaj składnik nakładki wideo i podłącz (nieskompresowany) numer PIN wideo do (nieskompresowanego) numeru PIN wideo pliku multimedialnego.

Dodaj kolejne dane wejściowe pliku multimedialnego (aby załadować plik logo), kliknij ten składnik i zmień jego nazwę na "logo wejściowe plików multimedialnych" i wybierz obraz (na przykład plik. png) we właściwości pliku. Podłącz kod PIN nieskompresowanego obrazu do nieskompresowanego numeru PIN obrazu nakładki.

![Składnik nakładki i źródło pliku obrazu](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Składnik nakładki i źródło pliku obrazu*

Jeśli chcesz zmodyfikować pozycję logo w wideo (na przykład możesz chcieć umieścić ją na 10% w lewym górnym rogu filmu wideo), wyczyść pole wyboru "ręczne wprowadzanie". Można to zrobić, ponieważ do udostępnienia pliku logo do składnika nakładki jest używane dane wejściowe pliku multimedialnego.

![Pozycja nakładki](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Pozycja nakładki*

Aby zakodować strumień wideo do H. 264, Dodaj koder wideo AVC i składniki AAC Encoder do powierzchni projektanta. Połącz numery PIN.
Skonfiguruj koder AAC i wybierz opcję Konwersja formatu dźwięku/ustawienie wstępne: 2,0 (L, R).

![Kodery audio i wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Kodery audio i wideo*

Teraz Dodaj elementy **multipleksera ISO MPEG-4** i **pliki wyjściowe** , a następnie połącz numery PIN, jak pokazano.

![Multiplekser i wyjście pliku MP4](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplekser i wyjście pliku MP4*

Należy ustawić nazwę pliku wyjściowego. Kliknij składnik **dane wyjściowe pliku** i Edytuj wyrażenie dla tego pliku:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4`

![Nazwa wyjściowa pliku](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nazwa wyjściowa pliku*

Przepływ pracy można uruchomić lokalnie, aby sprawdzić, czy działa poprawnie.

Po zakończeniu można uruchomić ją w Azure Media Services.

Najpierw Przygotuj zasób w Azure Media Services z dwoma plikami w nim: plik wideo i logo. Można to zrobić za pomocą platformy .NET lub interfejsu API REST. Można to zrobić również przy użyciu Azure Portal lub [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

W tym samouczku pokazano, jak zarządzać zasobami za pomocą AMSE. Istnieją dwa sposoby dodawania plików do elementu zawartości:

* Utwórz folder lokalny, skopiuj do niego dwa pliki, a następnie przeciągnij i upuść folder na karcie element **zawartości** .
* Przekaż plik wideo jako element zawartości, Wyświetl informacje o zasobach, przejdź do karty pliki i przekaż dodatkowy plik (logo).

> [!NOTE]
> Upewnij się, że ustawiono plik podstawowy w elemencie zawartości (główny plik wideo).

![Pliki zasobów w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Pliki zasobów w AMSE*

Wybierz element zawartości i wybierz opcję kodowania go za pomocą kodera Premium. Przekaż przepływ pracy i wybierz go.

Kliknij przycisk, aby przekazać dane do procesora, a następnie Dodaj następujący kod XML, aby ustawić właściwości środowiska uruchomieniowego:

![Koder w warstwie Premium w AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Koder w warstwie Premium w AMSE*

Następnie wklej poniższe dane XML. Należy określić nazwę pliku wideo dla danych wejściowych i primarySourceFile plików multimedialnych. Należy również określić nazwę pliku logo.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

W przypadku tworzenia i uruchamiania zadania przy użyciu zestawu .NET SDK te dane XML należy przesłać jako ciąg konfiguracji.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Po zakończeniu zadania plik MP4 w wyjściowym elemencie zawartości wyświetla nakładkę!

![Nałóż na wideo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Nałóż na wideo*

Przykładowy przepływ pracy można pobrać z witryny [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Przykład 2: kodowanie wielu języków dźwięku

Przykład wielu przepływów pracy kodowania języka audio jest dostępny w serwisie [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ten folder zawiera przykładowy przepływ pracy, którego można użyć do zakodowania pliku MXF do zasobu z wieloma plikami MP4 z wieloma ścieżkami audio.

W tym przepływie pracy założono, że plik MXF zawiera jedną ścieżkę audio. dodatkowe ścieżki audio należy przesłać jako oddzielne pliki audio (WAV lub MP4...).

Aby zakodować, wykonaj następujące kroki:

* Utwórz element zawartości Media Services przy użyciu pliku MXF i plików audio (od 0 do 18 plików audio).
* Upewnij się, że plik MXF jest ustawiony jako plik podstawowy.
* Tworzenie zadania i zadania przy użyciu procesora kodera przepływu pracy w warstwie Premium. Użyj dostarczonego przepływu pracy (MultiMP4-1080p-19audio-v1. Workflow).
* Przekaż setruntime.xml dane do zadania (Jeśli korzystasz z Eksploratora Azure Media Services, użyj przycisku "Przekaż dane XML do przepływu pracy").
  * Zaktualizuj dane XML, aby określić prawidłowe nazwy plików i Tagi języka.
  * Przepływ pracy zawiera składniki audio o nazwie audio 1 do audio 18.
  * RFC5646 jest obsługiwany dla tagu języka.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Zakodowany element zawartości będzie zawierać ścieżki audio w wielu językach, a ścieżki powinny być wybierane w Azure Media Player.

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Jak używać kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kodowanie zawartości na żądanie za pomocą Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formaty Media Encoder Premium Workflow i kodeki](media-services-premium-workflow-encoder-formats.md)
* [Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples)
* [Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
