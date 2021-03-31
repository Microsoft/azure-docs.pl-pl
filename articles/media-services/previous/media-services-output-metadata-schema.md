---
title: Schemat metadanych danych wyjściowych Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie schematu metadanych danych wyjściowych Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008296"
---
# <a name="output-metadata"></a>Metadane wyjściowe

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie
Zadanie kodowania jest skojarzone z wejściowym zasobem (lub zasobami), na którym mają zostać wykonane pewne zadania kodowania. Na przykład Koduj plik MP4 do H. 264 zestawy adaptacyjnej szybkości transmisji bitów. Utwórz miniaturę; Utwórz nakładki. Po zakończeniu zadania tworzony jest wyjściowy element zawartości.  Element zawartości wyjściowej zawiera wideo, audio, miniatury itp. Wyjściowy element zawartości zawiera również plik z metadanymi dotyczącymi wyjściowego elementu zawartości. Nazwa pliku XML metadanych ma następujący format: &lt; source_file_name &gt;_manifest.xml (na przykład BigBuckBunny_manifest.xml).  

Media Services nie skanuje wstępnie emptively zasobów wejściowych w celu wygenerowania metadanych. Metadane wejściowe są generowane tylko jako artefakt, gdy wejściowy element zawartości jest przetwarzany w zadaniu. W związku z tym ten artefakt jest zapisywana w elemencie zawartości wyjściowej. Do generowania metadanych dla wejściowych zasobów i elementów zawartości wyjściowej są używane różne narzędzia. W związku z tym metadane wejściowe mają nieco inny schemat niż metadane wyjściowe.

Jeśli chcesz przejrzeć plik metadanych, możesz utworzyć lokalizator **sygnatury dostępu współdzielonego** i pobrać plik na komputer lokalny.  

W tym artykule omówiono elementy i typy schematu XML, na których bazuje dane wyjściowe metada ( &lt; source_file_name &gt;_manifest.xml). Aby uzyskać informacje o pliku zawierającym metadane dotyczące danych wejściowych, zobacz metadane wejściowe.  

Pełny kod schematu i przykład XML można znaleźć na końcu tego artykułu.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> Element główny AssetFiles
Kolekcja wpisów AssetFile dla zadania kodowania.  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs = "0" maxOccurs = "1" |Element AssetFile, który jest częścią kolekcji AssetFiles. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile, element
[Przykład](#xml)XML XML przykładu.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagane |**XS: ciąg** |Nazwa pliku zasobów nośnika. |
| **Rozmiar**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: Long** |Rozmiar pliku zasobów w bajtach. |
| **Czas trwania**<br/><br/> Wymagane |**XS: czas trwania** |Czas trwania odtwarzania zawartości. |

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Źródła** |Kolekcja plików nośnika danych wejściowych/źródłowych, które zostały przetworzone w celu utworzenia tego AssetFile. Aby uzyskać więcej informacji, zobacz element źródłowy. |
| **VideoTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek wideo z przeplotem do odpowiedniego formatu kontenera. Aby uzyskać więcej informacji, zobacz VideoTracks element. |
| **AudioTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek audio przeplatanych w odpowiedni format kontenera. To jest kolekcja wszystkich ścieżek audio. Aby uzyskać więcej informacji, zobacz AudioTracks element. |

## <a name="sources-element"></a><a name="Sources"></a> Element sources
Kolekcja plików nośnika danych wejściowych/źródłowych, które zostały przetworzone w celu utworzenia tego AssetFile.  

[Przykład](#xml)XML XML przykładu.  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **Element źródłowy**<br/><br/> minOccurs = "1" maxOccurs = "niepowiązane" |Plik wejściowy/źródłowy używany podczas generowania tego elementu zawartości. Aby uzyskać więcej informacji, zobacz element źródłowy. |

## <a name="source-element"></a><a name="Source"></a> Element źródłowy
Plik wejściowy/źródłowy używany podczas generowania tego elementu zawartości.  

[Przykład](#xml)XML XML przykładu.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa**<br/><br/> Wymagane |**XS: ciąg** |Nazwa wejściowego pliku źródłowego. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks, element
Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek wideo z przeplotem do odpowiedniego formatu kontenera. Element **VideoTracks** reprezentuje kolekcję wszystkich ścieżek wideo.  

[Przykład](#xml)XML XML przykładu.  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs = "1" maxOccurs = "niepowiązane" |Określona ścieżka wideo w AssetFile nadrzędnym. Aby uzyskać więcej informacji, zobacz VideoTrack element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> VideoTrack, element
Określona ścieżka wideo w AssetFile nadrzędnym.  

[Przykład](#xml)XML XML przykładu.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **#C1**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Indeks tej ścieżki wideo liczony od zera. **Uwaga:**  Ten **Identyfikator** nie musi być TrackID używany w pliku MP4. |
| **FourCC**<br/><br/> Wymagane |**XS: ciąg** |Kod kodera-dekoder wideo FourCC. |
| **Profil** |**XS: ciąg** |Profil wielokrotna H264 (dotyczy tylko kodera-dekoder wielokrotna H264). |
| **Poziomie** |**XS: ciąg** |Poziom wielokrotna H264 (dotyczy tylko wielokrotna H264 Codec). |
| **Width**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Zakodowana Szerokość wideo (w pikselach). |
| **Height**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Zakodowana wysokość wideo w pikselach. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: Double** |Licznik współczynnik proporcji wyświetlania wideo. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: Double** |Mianownik współczynnika proporcji wyświetlania wideo. |
| **Framerate**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: decimal** |Mierzona szybkość klatek wideo w formacie. 3F. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: decimal** |Wstępnie ustawiona docelowa szybkość klatek wideo w formacie. 3F. |
| **Multimedia**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Średnia szybkość transmisji bitów wideo w kilobitach na sekundę, obliczona na podstawie AssetFile. Zlicza tylko podstawowy ładunek strumienia i nie uwzględnia obciążenia opakowania. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Przekieruj średnią szybkość transmisji bitów dla tej ścieżki wideo, zgodnie z żądaniem ustawienia wstępnego kodowania, w kilobitach na sekundę. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**XS: int** |Maksymalna grupę GOP średnia szybkość transmisji bitów dla tej ścieżki wideo (w kilobitach na sekundę). |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks, element
Każda AssetFile fizyczna może zawierać w niej zero lub więcej ścieżek audio przeplatanych w odpowiedni format kontenera. Element **AudioTracks** reprezentuje kolekcję wszystkich ścieżek audio.  

[Przykład](#xml)XML XML przykładu.  

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs = "1" maxOccurs = "niepowiązane" |Określona ścieżka audio w AssetFile nadrzędnym. Aby uzyskać więcej informacji, zobacz AudioTrack element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> AudioTrack, element
Określona ścieżka audio w AssetFile nadrzędnym.  

[Przykład](#xml)XML XML przykładu.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **#C1**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Indeks tej ścieżki audio liczony od zera. **Uwaga:**  Nie jest to konieczne TrackID w pliku MP4. |
| **Wymaga** |**XS: ciąg** |Ciąg Codec ścieżki audio. |
| **EncoderVersion** |**XS: ciąg** |Opcjonalny ciąg wersji kodera wymagany dla EAC3. |
| **Kanały**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Liczba kanałów audio. |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Częstotliwość próbkowania audio w próbkach/s lub Hz. |
| **Multimedia**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Średnia szybkość transmisji bitów audio w bitach na sekundę, obliczona na podstawie AssetFile. Zlicza tylko podstawowy ładunek strumienia i nie uwzględnia obciążenia opakowania. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Wymagane |**XS: int** |Bity na próbkę dla typu formatu wFormatTag. |

### <a name="child-elements"></a>Elementy podrzędne
| Nazwa | Opis |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs = "0" maxOccurs = "1" |Parametry wyniku pomiaru głośności. Aby uzyskać więcej informacji, zobacz LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters, element
Parametry wyniku pomiaru głośności.  

[Przykład](#xml)XML XML przykładu.  

### <a name="attributes"></a>Atrybuty
| Nazwa | Typ | Opis |
| --- | --- | --- |
| **DPLMVersionInformation** |**XS: ciąg** |Wersja zestawu do rozwoju pomiaru głośności w programie **Dolby** Professional. |
| **DialogNormalization**<br/><br/> minInclusive = "-31" maxInclusive = "-1"<br/><br/> Wymagane |**XS: int** |DialogNormalization generowany przez DPLM, wymagany, gdy ustawiono LoudnessMetering |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Wymagane |**XS: float** |Zintegrowane głośność |
| **IntegratedLoudnessUnit**<br/><br/> Wymagane |**XS: ciąg** |Jednostka zintegrowanego rozgłośności. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Wymagane |**XS: ciąg** |Identyfikator kontroli |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**XS: float** |Zawartość mowy w programie w postaci wartości procentowej. |
| **SamplePeak**<br/><br/> Wymagane |**XS: float** |Szczytowa wartość próbki bezwzględnej od momentu zresetowania lub od momentu ostatniego wyczyszczenia na kanał.  Jednostki są dBFS. |
| **SamplePeakUnit**<br/><br/> FIXED = "dBFS"<br/><br/> Wymagane |**XS: anySimpleType** |Przykładowa jednostka szczytu. |
| **TruePeak**<br/><br/> Wymagane |**XS: float** |Maksymalna prawdziwa wartość szczytowa, tak jak w przypadku ITU-R BS. 1770-2, od momentu zresetowania lub od momentu ostatniego wyczyszczenia na kanał. Jednostki są dBTP. |
| **TruePeakUnit**<br/><br/> FIXED = "dBTP"<br/><br/> Wymagane |**XS: anySimpleType** |Rzeczywista jednostka szczytu. |

## <a name="schema-code"></a>Kod schematu
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

Poniższy kod XML jest przykładem pliku metadanych danych wyjściowych.  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
