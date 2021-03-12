---
title: Schemat metadanych wejścia Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie schematu metadanych danych wejściowych Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017413"
---
# <a name="input-metadata"></a>Metadane wejściowe

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Zadanie kodowania jest skojarzone z wejściowym zasobem (lub zasobami), na którym mają zostać wykonane pewne zadania kodowania.  Po zakończeniu zadania tworzony jest wyjściowy element zawartości.  Element zawartości wyjściowej zawiera wideo, audio, miniatury, manifest itd. Wyjściowy element zawartości zawiera również plik z metadanymi zawierającymi dane wejściowe. Nazwa pliku XML metadanych ma następujący format: &lt; asset_id &gt;_metadata.xml (na przykład 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), gdzie &lt; Asset_id &gt; jest wartością AssetID dla wejściowego elementu zawartości.  

Media Services nie skanuje wstępnie emptively zasobów wejściowych w celu wygenerowania metadanych. Metadane wejściowe są generowane tylko jako artefakt, gdy wejściowy element zawartości jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywana w elemencie zawartości wyjściowej. Do generowania metadanych dla wejściowych zasobów i elementów zawartości wyjściowej są używane różne narzędzia. W związku z tym metadane wejściowe mają nieco inny schemat niż metadane wyjściowe.

Jeśli chcesz przejrzeć plik metadanych, możesz utworzyć lokalizator **sygnatury dostępu współdzielonego** i pobrać plik na komputer lokalny. Przykład tworzenia lokalizatora SAS i pobierania pliku  [przy użyciu rozszerzeń zestawu SDK platformy .net Media Services](media-services-dotnet-get-started.md).  

W tym artykule omówiono elementy i typy schematu XML, na których oparto dane wejściowe metada ( &lt; asset_id &gt;_metadata.xml).  Aby uzyskać informacje o pliku zawierającym metadane dotyczące wyjściowego elementu zawartości, zobacz [metadane wyjściowe](media-services-output-metadata-schema.md).  

Przykładowy [kod](media-services-input-metadata-schema.md#code) [XML](media-services-input-metadata-schema.md#xml) można znaleźć na końcu tego artykułu.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles — element (element główny)
Zawiera kolekcję [elementów AssetFile](media-services-input-metadata-schema.md#AssetFile)dla zadania kodowania.  

Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

| Nazwa | Opis |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "niepowiązane" |Pojedynczy element podrzędny. Aby uzyskać więcej informacji, zobacz [AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile, element
 Zawiera atrybuty i elementy opisujące plik elementu zawartości.  

 Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br /><br /> Wymagane |**XS: ciąg** |Nazwa pliku zasobu. |
| **Rozmiar**<br /><br /> Wymagane |**XS: Long** |Rozmiar pliku zasobów w bajtach. |
| **Czas trwania**<br /><br /> Wymagane |**XS: czas trwania** |Czas trwania odtwarzania zawartości. Przykład: duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Wymagane |**XS: int** |Liczba strumieni w pliku zasobów. |
| **FormatNames**<br /><br /> Wymagane |**XS: ciąg** |Nazwy formatów. |
| **FormatVerboseNames**<br /><br /> Wymagane |**XS: ciąg** |Sformatuj pełne nazwy. |
| **Rozpoczęcia** |**XS: czas trwania** |Godzina rozpoczęcia zawartości. Przykład: StartTime = "PT 2.669 S". |
| **OverallBitRate** |**XS: int** |Średnia szybkość transmisji pliku zasobów w KB/s. |

> [!NOTE]
> Następujące cztery elementy podrzędne muszą występować w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs = "0" | |Kolekcja wszystkich [programów](media-services-input-metadata-schema.md#Programs) , gdy plik zasobów jest w formacie MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Każdy fizyczny plik zasobów może zawierać co najmniej zero ścieżek wideo, które są przeplatane w odpowiednim formacie kontenera. Ten element zawiera kolekcję wszystkich [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) , które są częścią pliku zasobów. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Każdy fizyczny plik zasobów może zawierać co najmniej zero ścieżek audio przeplatanych w odpowiedni format kontenera. Ten element zawiera kolekcję wszystkich [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) , które są częścią pliku zasobów. |
| **Metadane**<br /><br /> minOccurs = "0" maxOccurs = "niepowiązane" |[Datadatatype](media-services-input-metadata-schema.md#MetadataType) |Metadane pliku zasobów reprezentowane jako ciągi key\value. Na przykład:<br /><br /> **&lt;Metadata Key = "Language" value = "aparatu"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> Ścieżka śledzenia
Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **#C1**<br /><br /> Wymagane |**XS: int** |Indeks tej ścieżki audio lub wideo liczony od zera.<br /><br /> Nie musi to być TrackID używany w pliku MP4. |
| **Wymaga** |**XS: ciąg** |Ciąg kodera-dekoder śledzenia wideo. |
| **CodecLongName** |**XS: ciąg** |Długa nazwa kodera-dekoder dla ścieżki audio lub wideo. |
| **Ujemną**<br /><br /> Wymagane |**XS: ciąg** |Podstawa czasu. Przykład: podstawa = "1/48000" |
| **NumberOfFrames** |**XS: int** |Liczba ramek (obecnych dla ścieżek wideo). |
| **Rozpoczęcia** |**XS: czas trwania** |Godzina rozpoczęcia śledzenia. Przykład: StartTime = "PT 2.669 S" |
| **Czas trwania** |**XS: czas trwania** |Czas trwania śledzenia. Przykład: duration = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> Następujące dwa elementy podrzędne muszą występować w sekwencji.  
> 
> 

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Dyspozycji**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Zawiera informacje o prezentacji (na przykład czy konkretna ścieżka dźwiękowa dotyczy wizualnie odwidzących osób). |
| **Metadane**<br /><br /> minOccurs = "0" maxOccurs = "niepowiązane" |[Datadatatype](media-services-input-metadata-schema.md#MetadataType) |Ogólne ciągi klucz/wartość, których można użyć do przechowywania różnych informacji. Na przykład klucz = "język", i wartość = "aparatu". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (dziedziczy z metody tracktype)
 **AudioTrackType** to globalny typ złożony, który dziedziczy z elementu [tracktype](media-services-input-metadata-schema.md#TrackType).  

 Typ reprezentuje konkretną ścieżkę audio w pliku zasobów.  

 Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **SampleFormat** |**XS: ciąg** |Przykładowy format. |
| **ChannelLayout** |**XS: ciąg** |Układ kanału. |
| **Kanały**<br /><br /> Wymagane |**XS: int** |Liczba (0 lub więcej) kanałów audio. |
| **SamplingRate**<br /><br /> Wymagane |**XS: int** |Częstotliwość próbkowania audio w próbkach/s lub Hz. |
| **Multimedia** |**XS: int** |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie pliku zasobów. Zliczany jest tylko podstawowy ładunek strumienia, a obciążenie opakowania nie jest uwzględniane w tej liczbie. |
| **BitsPerSample** |**XS: int** |Bity na próbkę dla typu formatu wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (dziedziczy z metody tracktype)
**VideoTrackType** to globalny typ złożony, który dziedziczy z elementu [tracktype](media-services-input-metadata-schema.md#TrackType).  

Typ reprezentuje konkretną ścieżkę wideo w pliku zasobów.  

Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **FourCC**<br /><br /> Wymagane |**XS: ciąg** |Kod kodera-dekoder wideo FourCC. |
| **Profil** |**XS: ciąg** |Profil ścieżki wideo. |
| **Poziomie** |**XS: ciąg** |Poziom ścieżki wideo. |
| **PixelFormat** |**XS: ciąg** |Format piksela ścieżki wideo. |
| **Width**<br /><br /> Wymagane |**XS: int** |Zakodowana Szerokość wideo (w pikselach). |
| **Height**<br /><br /> Wymagane |**XS: int** |Zakodowana wysokość wideo w pikselach. |
| **DisplayAspectRatioNumerator**<br /><br /> Wymagane |**XS: Double** |Licznik współczynnik proporcji wyświetlania wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**XS: Double** |Mianownik współczynnika proporcji wyświetlania wideo. |
| **DisplayAspectRatioDenominator**<br /><br /> Wymagane |**XS: Double** |Przykładowy współczynnik proporcji filmu wideo. |
| **SampleAspectRatioNumerator** |**XS: Double** |Przykładowy współczynnik proporcji filmu wideo. |
| **SampleAspectRatioNumerator** |**XS: Double** |Współczynnik proporcji próbkowania wideo. |
| **Szybkości**<br /><br /> Wymagane |**XS: decimal** |Mierzona szybkość klatek wideo w formacie. 3F. |
| **Multimedia** |**XS: int** |Średnia szybkość transmisji bitów wideo w kilobitach na sekundę, obliczona na podstawie pliku zasobów. Zliczane jest tylko podstawowy ładunek strumienia, a obciążenie opakowania nie jest uwzględniane. |
| **MaxGOPBitrate** |**XS: int** |Maksymalna grupę GOP średnia szybkość transmisji bitów dla tej ścieżki wideo (w kilobitach na sekundę). |
| **HasBFrames** |**XS: int** |Numer ścieżki wideo klatek B. |

## <a name="metadatatype"></a><a name="MetadataType"></a> Datadatatype
**Metadatatype to globalny** typ złożony, który opisuje metadane pliku zasobu jako ciągi klucza/wartości. Na przykład klucz = "język", i wartość = "aparatu".  

Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **głównych**<br /><br /> Wymagane |**XS: ciąg** |Klucz w parze klucz/wartość. |
| **wartość**<br /><br /> Wymagane |**XS: ciąg** |Wartość w parze klucz/wartość. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
Typ **programu** jest globalnym typem złożonym opisującym program.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **ProgramId**<br /><br /> Wymagane |**XS: int** |Identyfikator programu |
| **NumberOfPrograms**<br /><br /> Wymagane |**XS: int** |Liczba programów. |
| **PmtPid**<br /><br /> Wymagane |**XS: int** |Tabele mapy programu (PMTs) zawierają informacje o programach.  Aby uzyskać więcej informacji, zobacz [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Wymagane |**XS: int** |Używany przez dekoder. Aby uzyskać więcej informacji, zobacz [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**XS: Long** |Rozpoczynanie sygnatury czasowej prezentacji. |
| **EndPTS** |**XS: Long** |Końcowa sygnatura czasowa prezentacji. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** to globalny typ złożony, który opisuje strumień.  

Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Wartooć**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to prezentacja domyślna. |
| **Dub**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to prezentacja dubbed. |
| **Oryginalne**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to oryginalna prezentacja. |
| **Komentarz**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka zawiera komentarz. |
| **Muzycznych**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka zawiera teksty. |
| **Karaoke**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że reprezentuje śledzenie karaoke (muzyka w tle, bez vocals). |
| **Wymuszone**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że jest to wymuszona prezentacja. |
| **HearingImpaired**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka jest dla osób słabo słyszących. |
| **VisualImpaired**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka jest dla wizualizacji. |
| **CleanEffects**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka ma czyste efekty. |
| **AttachedPic**<br /><br /> Wymagane |**XS: int** |Ustaw ten atrybut na 1, aby wskazać, że ta ścieżka zawiera obrazy. |

## <a name="programs-element"></a><a name="Programs"></a> Element programy
Element otoki przechowujący wiele elementów **programu** .  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "niepowiązane" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |W przypadku plików zasobów w formacie MPEG-TS program zawiera informacje o programach w pliku zasobów. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks, element
 Element otoki przechowujący wiele elementów **VideoTrack** .  

 Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "niepowiązane" |[VideoTrackType (dziedziczy z metody tracktype)](media-services-input-metadata-schema.md#VideoTrackType) |Zawiera informacje o ścieżkach wideo w pliku zasobów. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks, element
 Element otoki przechowujący wiele elementów **AudioTrack** .  

 Zapoznaj się z przykładem XML na końcu tego artykułu: [przykład XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementy
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "niepowiązane" |[AudioTrackType (dziedziczy z metody tracktype)](media-services-input-metadata-schema.md#AudioTrackType) |Zawiera informacje o ścieżkach audio w pliku zasobów. |

## <a name="schema-code"></a><a name="code"></a> Kod schematu
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> Przykład XML
Poniżej znajduje się przykład pliku metadanych danych wejściowych.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

