---
title: Wstawianie reklam po stronie klienta | Microsoft Docs
description: W tym artykule pokazano, jak wstawiać reklamy do multimediów po stronie klienta.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014965"
---
# <a name="inserting-ads-on-the-client-side"></a>Wstawianie reklam po stronie klienta

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ten artykuł zawiera informacje na temat wstawiania różnych typów reklam po stronie klienta.

Aby uzyskać informacje na temat napisów i pomocy technicznej usługi AD w filmach wideo na żywo, zobacz [obsługiwane napisy i standardy wstawiania do usługi AD](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player obecnie nie obsługuje reklam.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Wstawianie reklam do multimediów
Azure Media Services zapewnia obsługę wstawiania do usługi AD za pomocą platformy Windows Media platform: Player Frameworks. Platformy odtwarzacza z obsługą usług AD są dostępne dla urządzeń z systemami Windows 8, Silverlight, Windows Phone 8 i iOS. Każda platforma odtwarzacza zawiera przykładowy kod, który pokazuje, jak wdrożyć aplikację odtwarzacza. Istnieją trzy różne rodzaje reklam, które można wstawić do listy Media:.

* **Liniowa** — reklamy zawierające całą ramkę, która wstrzymuje główne wideo.
* **Nieliniowe** — oferty, które są wyświetlane jako główne wideo, są odtwarzane, zwykle logo lub innym obrazem statycznym umieszczonym w odtwarzaczu.
* **Pomocnik** — reklamy, które są wyświetlane poza odtwarzaczem.

Reklamy można umieścić w dowolnym miejscu w linii czasu głównego wideo. Musisz poinformować odtwarzacz, kiedy ma być odtwarzany AD i które reklamy mają być odtwarzane. Jest to realizowane przy użyciu zestawu standardowych plików opartych na języku XML: szablonów usługi AD wideo (ogromny), cyfrowego wideo z wieloma listami (VMAP), abstrakcyjnych szablonów sekwencji (MAST) i Digital Video Player AD Definition (VPAID). W przypadku dużych plików można określić, które reklamy mają być wyświetlane. Pliki VMAP określają, kiedy mają być odtwarzane różne reklamy i zawierają OGROMNą zawartość XML. Pliki MAST są kolejną metodą sekwencjonowania reklam, które również mogą zawierać OGROMNą zawartość XML. Pliki VPAID definiują interfejs między odtwarzaczem wideo a serwerem AD lub AD.

Każda platforma odtwarzacza działa inaczej, a każdy z nich zostanie uwzględniony w jego własnym artykule. W tym artykule opisano podstawowe mechanizmy służące do wstawiania reklam. Aplikacje odtwarzacza wideo żądają reklam z serwera usługi AD. Serwer usługi AD może odpowiadać na wiele sposobów:

* Zwracanie OGROMNEgo pliku
* Zwróć plik VMAP (z osadzoną OGROMNą)
* Zwróć plik MAST (z osadzoną OGROMNą)
* Zwracanie OGROMNEgo pliku za pomocą VPAID ads

### <a name="using-a-video-ad-service-template-vast-file"></a>Korzystanie z pliku szablonu usługi AD wideo (ogromny)
OLBRZYMI plik określa, jakie usługi AD lub reklamy mają być wyświetlane. Poniższy kod XML jest przykładem OGROMNEgo pliku dla liniowej usługi AD:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Liniowy AD jest opisywany przez <**liniowy**> elementu. Określa czas trwania usługi AD, śledzenie zdarzeń, klikanie przez, śledzenie i liczbę elementów **MediaFile** . Zdarzenia śledzenia są określone w elemencie <**TrackingEvents**> i umożliwiają serwerowi AD śledzenie różnych zdarzeń, które wystąpiły podczas wyświetlania usługi AD. W takim przypadku śledzone są zdarzenia uruchamiania, punktu środkowego, ukończenia i rozwinięcia. Zdarzenie uruchamiania występuje, gdy zostanie wyświetlona wartość AD. Zdarzenie punktu środkowego występuje, gdy została wyświetlona co najmniej 50% czasu. Pełne zdarzenie występuje po uruchomieniu usługi AD na końcu. Zdarzenie expando występuje, gdy użytkownik poszerzy odtwarzacz wideo na pełny ekran. Przeglądy są określane przy **użyciu <ego**>ego elementu **w <elementu**> i określa identyfikator URI dla zasobu, który będzie wyświetlany po kliknięciu przez użytkownika usługi AD. ClickTracking jest określony w elemencie <**ClickTracking**>, również w elemencie <**VideoClicks**> i określa zasób śledzenia dla odtwarzacza, gdy użytkownik kliknie w usłudze AD. Elementy <**MediaFile**> określają informacje o konkretnym kodowaniu usługi AD. Jeśli istnieje więcej niż jeden <**MediaFile**>, odtwarzacz wideo może wybrać najlepsze kodowanie dla danej platformy.

Oferty liniowe mogą być wyświetlane w określonej kolejności. W tym celu należy dodać dodatkowe `<Ad>` elementy do ogromnego pliku i określić kolejność przy użyciu atrybutu Sequence. Zostało to przedstawione w poniższym przykładzie:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Reklamy nieliniowe są również określone w `<Creative>` elemencie. Poniższy przykład pokazuje `<Creative>` element, który opisuje nieliniowe AD.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Element <**NonLinearAds**> może zawierać co najmniej jeden <**nieliniowych** elementów>, z których każdy może opisywać nieliniowe AD. Element <**Nieliniowy**> określa zasób dla nieliniowej usługi AD. Zasób może być <**StaticResource**>, <**IFrameResource**> lub <**HTMLResource**>. \<**StaticResource**> Opisuje zasób niebędący w formacie HTML i definiuje atrybut creativetype, który określa sposób wyświetlania zasobu:

Image/GIF, Image/JPEG, Image/PNG — zasób jest wyświetlany w tagu <**img**> języka HTML.

Aplikacja/x-JavaScript — zasób jest wyświetlany w tagu> **skryptu** HTML <.

Application/x-Shockwave-Flash — zasób jest wyświetlany w odtwarzaczu Flash.

**IFrameResource** OPISUJE zasób HTML, który może być wyświetlany w elemencie iframe. **HTMLResource** opisuje fragment kodu HTML, który można wstawić do strony sieci Web. **TrackingEvents** Określ zdarzenia śledzenia i identyfikator URI do żądania, gdy wystąpi zdarzenie. W tym przykładzie zdarzenia acceptInvitation i zwijania są śledzone. Aby uzyskać więcej informacji na temat elementu **NonLinearAds** i jego elementów podrzędnych, zobacz IAB.NET/VAST. Należy zauważyć, że element **TrackingEvents** znajduje się w elemencie **NonLinearAds** , a nie w elemencie **nieliniowym** .

Reklamy towarzyszące są zdefiniowane w obrębie `<CompanionAds>` elementu. `<CompanionAds>`Element może zawierać jeden lub więcej `<Companion>` elementów. Każdy `<Companion>` element zawiera opis pomocnika i może zawierać `<StaticResource>` , `<IFrameResource>` , lub, `<HTMLResource>` które są określone w taki sam sposób jak w przypadku nieliniowej usługi AD. OLBRZYMI plik może zawierać wiele reklam, a aplikacja odtwarzacza może wybrać najbardziej odpowiednią reklamę do wyświetlenia. Aby uzyskać więcej informacji na temat OGROMNYch, zobacz artykuł [ogromny 3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Używanie pliku listy odtwarzania (VMAP) cyfrowego wideo
Plik VMAP umożliwia określenie czasu, w którym występują przerwy w usłudze AD, czas trwania każdego przerwania, liczbę reklam, które mogą być wyświetlane w ramach przerwy, oraz typy reklam, które mogą być wyświetlane w trakcie przerwy. Poniżej znajduje się przykład pliku VMAP, który definiuje pojedynczy podział usługi AD:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Plik VMAP rozpoczyna się od `<VMAP>` elementu, który zawiera jeden lub więcej `<AdBreak>` elementów, z których każda definiuje podział usługi AD. Każdy podział usługi AD określa typ przerwania, identyfikator przerwania i przesunięcie czasu. Atrybut breaktype określa typ usługi AD, która może być odtwarzana w trakcie przerwy: liniowe, nieliniowe lub wyświetlanie. Wyświetl mapę ads w postaci OGROMNYch reklam. Więcej niż jeden typ AD można określić na liście rozdzielanej przecinkami (bez spacji). BreakID jest opcjonalnym identyfikatorem dla usługi AD. TimeOffset określa, kiedy powinna być wyświetlana wartość AD. Można ją określić w jeden z następujących sposobów:

1. Time — w formacie gg: mm: SS lub hh: mm: SS. mmm format, gdzie. mmm jest milisekundą. Wartość tego atrybutu określa czas od początku osi czasu wideo do początku przerwy w usłudze AD.
2. Wartość procentowa — w formacie n%, gdzie n jest wartością procentową osi czasu wideo do odtwarzania przed rozpoczęciem korzystania z usługi AD
3. Początek/koniec — określa, że AD ma być wyświetlana przed lub po wyświetleniu wideo
4. Position — określa kolejność przerw w usłudze AD, gdy czas trwania przerw w usłudze AD jest nieznany, na przykład w przypadku przesyłania strumieniowego na żywo. Kolejność każdej przerwy w usłudze AD jest określona w formacie #n, gdzie n jest liczbą całkowitą 1 lub większą. 1 oznacza, że usługi AD powinny być odtwarzane w pierwszej okazji, 2 oznacza, że reklama powinna być odtwarzana w drugiej szansie i tak dalej.

W obrębie `<AdBreak>` elementu może istnieć jeden <**AdSource**> elementu. Element <**AdSource**> zawiera następujące atrybuty:

1. ID — określa identyfikator źródła usługi AD
2. allowMultipleAds — wartość logiczna określająca, czy wiele reklam może być wyświetlanych podczas przerwy w usłudze AD
3. followRedirects — opcjonalna wartość logiczna określająca, czy odtwarzacz wideo powinien przestrzegać przekierowań w odpowiedzi usługi AD

Element <**AdSource**> Umożliwia odtwarzaczowi wbudowaną odpowiedź usługi AD lub odwołanie do odpowiedzi usługi AD. Może zawierać jeden z następujących elementów:

* `<VASTAdData>` wskazuje, że OBSZERNa odpowiedź usługi AD jest osadzona w pliku VMAP
* `<AdTagURI>` Identyfikator URI, który odwołuje się do odpowiedzi usługi AD z innego systemu
* `<CustomAdData>` -dowolny ciąg, który reprezentuje nieogromną odpowiedź

W tym przykładzie jest określona w wierszu odpowiedź usługi AD z `<VASTAdData>` elementem, który zawiera ogromną odpowiedź usługi AD. Aby uzyskać więcej informacji o innych elementach, zobacz [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Element <**AdBreak**> może również zawierać jeden <elementu> **TrackingEvents** . Element <**TrackingEvents**> umożliwia śledzenie rozpoczęcia lub zakończenia przerwy w usłudze AD lub tego, czy wystąpił błąd podczas przerwy w usłudze AD. Element <**TrackingEvents**> zawiera co najmniej jeden <**śledzenia** elementów>, z których każdy określa zdarzenie śledzenia i identyfikator URI śledzenia. Możliwe są następujące zdarzenia śledzenia:

1. breakStart — śledzi początek przerwy w usłudze AD
2. breakEnd — śledzenie ukończenia przerwy w usłudze AD
3. Error — śledzi błąd, który wystąpił podczas przerwania usługi AD

W poniższym przykładzie przedstawiono plik VMAP, który określa zdarzenia śledzenia

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Aby uzyskać więcej informacji na temat <**TrackingEvents**> elementu i jego elementów podrzędnych, zobacz http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Używanie pliku szablonu (MAST) abstrakcyjnej zawartości multimedialnej
Plik MAST pozwala określić wyzwalacze, które definiują, kiedy zostanie wyświetlona wartość AD. Poniżej znajduje się przykładowy plik MAST, który zawiera wyzwalacze dla wstępnej kompilacji usługi AD, usługi AD i usługi AD po rozwinięcia.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Plik MAST rozpoczyna się od elementu **Mast** , który zawiera jeden element **Triggers** . `<triggers>`Element zawiera jeden lub więcej elementów **wyzwalacza** , które definiują, kiedy ma być odtwarzany AD.

Element **wyzwalacza** zawiera element **startConditions** , który określa, kiedy ma się rozpoczynać odtwarzanie AD. Element **startConditions** zawiera jeden lub więcej `<condition>` elementów. Gdy każda `<condition>` z nich szacuje wartość true, wyzwalacz jest inicjowany lub odwołany w zależności od tego, czy `<condition>` jest zawarty w elemencie **startConditions** czy **endConditions** odpowiednio. Gdy `<condition>` są obecne wiele elementów, są one traktowane jako niejawne lub, dowolny warunek oceniający wartość true spowoduje, że wyzwalacz zostanie zainicjowany. `<condition>` elementy mogą być zagnieżdżane. Gdy `<condition>` elementy podrzędne są wstępnie zdefiniowane, są traktowane jako niejawne i, wszystkie warunki muszą mieć wartość true, aby można było zainicjować wyzwalacz. `<condition>`Element zawiera następujące atrybuty, które definiują warunek:

1. **Typ** — określa typ warunku, zdarzenia lub właściwości.
2. **name** — nazwa właściwości lub zdarzenia, które mają być używane podczas obliczania
3. **Value** — wartość, względem której zostanie oceniona Właściwość
4. **operator** — operacja do użycia podczas obliczania: EQ (równe), NEQ (nie równe), GTR (większe), GEQ (większe lub równe), lt (mniejsze niż), LEQ (mniejsze niż lub równe), mod (modulo)

**endConditions** również zawiera `<condition>` elementy. Gdy warunek zwróci wartość true, wyzwalacz jest resetowany. `<trigger>`Element zawiera również `<sources>` element, który zawiera jeden lub więcej `<source>` elementów. `<source>`Elementy definiują identyfikator URI odpowiedzi usługi AD i typ odpowiedzi usługi AD. W tym przykładzie identyfikator URI jest przydzielany do ogromnej odpowiedzi.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Korzystanie z definicji interfejsu Player-Ad wideo (VPAID)
VPAID to interfejs API służący do włączania funkcji wykonywalnych jednostek usługi AD do komunikowania się z odtwarzaczem wideo. Pozwala to na wysoce interaktywne środowiska usługi AD. Użytkownik może korzystać z usługi AD, a usługi AD mogą reagować na akcje wykonywane przez przeglądarkę. Na przykład w usłudze AD mogą być wyświetlane przyciski umożliwiające użytkownikowi wyświetlanie większej ilości informacji lub dłuższej wersji usługi AD. Odtwarzacz wideo musi obsługiwać interfejs API VPAID, a plik wykonywalny musi implementować interfejs API. Gdy odtwarzacz żąda usługi AD z serwera usługi AD, serwer może odpowiedzieć z dużą odpowiedzią, która zawiera VPAID usługi AD.

Plik wykonywalny usługi AD jest tworzony w kodzie, który musi być wykonywany w środowisku uruchomieniowym, takim jak Adobe Flash™ lub JavaScript, które można wykonać w przeglądarce internetowej. Gdy serwer usługi AD zwraca OGROMNą odpowiedź zawierającą VPAID AD, wartość atrybutu apiFramework w `<MediaFile>` elemencie musi być równa "VPAID". Ten atrybut określa, że zawarta AD jest VPAID pliku wykonywalnego. Atrybut type musi być ustawiony na typ MIME pliku wykonywalnego, na przykład "application/x-Shockwave-Flash" lub "application/x-JavaScript". Poniższy fragment kodu XML przedstawia `<MediaFile>` element z rozległej odpowiedzi zawierającej VPAID plik wykonywalny usługi AD.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Plik wykonywalny usługi AD można zainicjować przy użyciu `<AdParameters>` elementu w obrębie `<Linear>` lub w ramach `<NonLinear>` ogromnej odpowiedzi. Aby uzyskać więcej informacji na temat `<AdParameters>` elementu, zobacz artykuł [ogromny 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). Aby uzyskać więcej informacji na temat interfejsu API VPAID, zobacz [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementowanie programu Windows lub Windows Phone 8 Player z obsługą usługi AD
Platforma Microsoft Media platform: Player Framework dla systemów Windows 8 i Windows Phone 8 zawiera kolekcję przykładowych aplikacji, które pokazują, jak wdrożyć aplikację odtwarzacza wideo przy użyciu struktury. Możesz pobrać platformę odtwarzacza i przykłady z programu [Player Framework dla systemu Windows 8 i Windows Phone 8](https://playerframework.codeplex.com).

Po otwarciu rozwiązania Microsoft. PlayerFramework. XAML. Samples w projekcie zostanie wyświetlona liczba folderów. Folder anons zawiera przykładowy kod przydatny do tworzenia odtwarzacza wideo z obsługą usługi AD. W folderze anonsów jest wiele plików XAML/cs, z których każdy pokazuje, jak wstawiać reklamy w inny sposób. Na poniższej liście opisano każdy z nich:

* AdPodPage. XAML pokazuje, jak wyświetlić usługi AD pod.
* AdSchedulingPage. XAML pokazuje, jak planować reklamy.
* FreeWheelPage. XAML pokazuje, jak planować reklamy przy użyciu wtyczki FreeWheel.
* MastPage. XAML pokazuje, jak planować reklamy przy użyciu pliku MAST.
* ProgrammaticAdPage. XAML pokazuje, jak programowo zaplanować reklamy do wideo.
* ScheduleClipPage. XAML pokazuje, jak zaplanować usługi AD bez OBSZERNego pliku.
* VastLinearCompanionPage. XAML pokazuje, jak wstawiać liniowe i pomocnicze usługi AD.
* VastNonLinearPage. XAML pokazuje, jak wstawić nieliniowe AD.
* VmapPage. XAML pokazuje, jak określać reklamy przy użyciu pliku VMAP.

Każdy z tych przykładów używa klasy MediaPlayer zdefiniowanej przez platformę odtwarzacza. Większość przykładów używa wtyczek, które dodają obsługę różnych formatów odpowiedzi usługi AD. Przykład ProgrammaticAdPage programowo współdziała z wystąpieniem MediaPlayer.

### <a name="adpodpage-sample"></a>Przykład AdPodPage
Ten przykład używa AdSchedulerPlugin, aby zdefiniować, kiedy ma być wyświetlana wartość AD. W tym przykładzie zaplanowano odtworzenie anonsu w postaci rzutu po upływie pięciu sekund. AD pod (Grupa reklam do wyświetlania w kolejności) jest określona w OLBRZYMIm pliku zwracanym z serwera usługi AD. Identyfikator URI dla OGROMNEgo pliku jest określony w `<RemoteAdSource>` elemencie.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Aby uzyskać więcej informacji na temat AdSchedulerPlugin, zobacz [reklamy w środowisku odtwarzacza w systemie Windows 8 i Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Ten przykład używa również AdSchedulerPlugin. Planuje ona trzy reklamy, wstępnie rzutowane usługi AD, usługi AD i przewinięcie usługi AD. Identyfikator URI dla każdej usługi AD jest określony w `<RemoteAdSource>` elemencie.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Ten przykład używa FreeWheelPlugin, który określa atrybut źródłowy, który określa identyfikator URI wskazujący plik SmartXML, który określa zawartość usługi AD, a także informacje o planowaniu usługi AD.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Ten przykład używa MastSchedulerPlugin, który umożliwia korzystanie z pliku MAST. Atrybut source określa lokalizację pliku MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Ten przykład programowo współdziała z MediaPlayer. Plik ProgrammaticAdPage. XAML tworzy wystąpienie MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Plik ProgrammaticAdPage.xaml.cs tworzy AdHandlerPlugin, dodaje TimelineMarker, aby określić, kiedy powinna być wyświetlana wartość AD, a następnie dodaje procedurę obsługi dla zdarzenia MarkerReached, które ładuje RemoteAdSource, określając identyfikator URI dla OGROMNEgo pliku, a następnie odtwarza AD.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Ten przykład używa AdSchedulerPlugin do zaplanowania rzutu usługi AD przy użyciu pliku. wmv, który zawiera usługi AD.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Ten przykład ilustruje sposób użycia AdSchedulerPlugin do zaplanowania rzutu liniowego usługi AD przy użyciu pomocnika usługi AD. `<RemoteAdSource>`Element określa lokalizację ogromnego pliku.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Ten przykład używa AdSchedulerPlugin do zaplanowania liniowej i nieliniowej usługi AD. OGROMNa lokalizacja pliku jest określana za pomocą `<RemoteAdSource>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
W tym przykładzie użyto VmapSchedulerPlugin do zaplanowania reklam przy użyciu pliku VMAP. Identyfikator URI do pliku VMAP jest określony w atrybucie Source `<VmapSchedulerPlugin>` elementu.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Wdrażanie odtwarzacza wideo dla systemu iOS z obsługą usługi AD
Platforma Microsoft Media: Player Framework dla systemu iOS zawiera kolekcję przykładowych aplikacji, które pokazują, jak zaimplementować aplikację odtwarzacza wideo przy użyciu struktury. Możesz pobrać platformę odtwarzacza i przykłady z programu [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework). Na stronie usługi GitHub znajduje się link do witryny typu wiki, która zawiera dodatkowe informacje na temat platformy odtwarzacza i wprowadzenie do przykładu odtwarzacza: [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Planowanie reklam przy użyciu VMAP
Poniższy przykład pokazuje, jak zaplanować reklamy przy użyciu pliku VMAP.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Planowanie reklam przy użyciu OGROMNYch
Poniższy przykład pokazuje, jak zaplanować ogromny czas powiązania usługi AD.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Poniższy przykład pokazuje, jak zaplanować wczesne powiązanie z OGROMNą usługą AD.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Poniższy przykład pokazuje, jak wstawić AD przy użyciu niesztywnej edycji (RÓDŁOWY)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak zaplanować usługi AD pod.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

W poniższym przykładzie pokazano, jak zaplanować planowanie usługi AD w postaci nietrwałej. Niedziałający program jest odtwarzany tylko raz, bez względu na to, co wykonuje przeglądarka.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

W poniższym przykładzie pokazano, jak zaplanować zaplanowanie usługi AD w środku. Program Sticky WebAD jest wyświetlany za każdym razem, gdy zostanie osiągnięty określony punkt na osi czasu wideo.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak zaplanować ogłoszenie usługi AD.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak zaplanować wstępną wersję usługi AD.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Poniższy przykład pokazuje, jak zaplanować nakładanie się usługi AD.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
