---
title: Przesyłanie strumieniowe metadanych z Azure Media Servicesami
description: Ta specyfikacja zawiera opis metod sygnalizujących metadane czasowe podczas pozyskiwania i przesyłania strumieniowego do Azure Media Services. Obejmuje to obsługę ogólnych sygnałów metadanych (ID3), a także SCTE-35 sygnalizowanie dla operacji wstawiania AD i nałączenia warunków.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336398"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Sygnalizowanie metadanych w czasie przesyłania strumieniowego na żywo 

Ostatnia aktualizacja: 2019-08-22

### <a name="conformance-notation"></a>Notacja zgodności

Kluczowe słowa "musi", "nie może być", "wymagane", ",", ",", "," powinny "," nie powinno być "," zalecane "," maj "i" opcjonalne "w tym dokumencie są interpretowane zgodnie z opisem w dokumencie RFC 2119

## <a name="1-introduction"></a>1. Wprowadzenie 

Aby sygnalizować Wstawianie anonsów lub niestandardowych zdarzeń metadanych w odtwarzaczu klienta, nadawcy często używają metadanych czasowych osadzonych w filmie wideo. Aby włączyć te scenariusze, Media Services zapewnia obsługę transportu metadanych czasowych z punktu pozyskiwania kanału przesyłania strumieniowego na żywo do aplikacji klienckiej.
Ta specyfikacja zawiera kilka trybów, które są obsługiwane przez Media Services dla metadanych czasowych w ramach sygnałów przesyłania strumieniowego na żywo.

1. [SCTE-35] sygnalizowanie zgodne ze standardami opisanymi przez [SCTE-35], [SCTE-214-1], [SCTE-214-3] i [RFC8216]

2. [SCTE-35] sygnalizowanie zgodne ze specyfikacją starszej wersji [Adobe-Primetime] dla sygnałów usługi AD RTMP.
   
3. Ogólny tryb sygnalizowania metadanych w przypadku wiadomości, które **nie** są [SCTE-35] i mogą zawierać [ID3v2] lub inne niestandardowe schematy zdefiniowane przez dewelopera aplikacji.

## <a name="11-terms-used"></a>1,1 warunków użytkowania

| Okres                | Definicja                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Przerwanie usługi AD            | Lokalizacja lub punkt w czasie, w którym co najmniej jedna usługa ads może zostać zaplanowana do dostarczenia; taka sama jak w przypadku możliwości dostępn i umieszczania.                                                                                                                     |
| Usługa decyzji usługi AD | Usługa zewnętrzna, która decyduje, które reklamy i czas trwania będą widoczne dla użytkownika. Usługi są zwykle udostępniane przez partnera i poza zakresem tego dokumentu.                                                                    |
| Kontrol                 | Wskazanie czasu i parametrów nadchodzącego przerwania usługi AD. Należy zauważyć, że wskaźniki mogą wskazywać oczekujące przełączenie do przerwania usługi AD, oczekujące przełączenie do następnej usługi AD w ramach przerwy w usłudze AD i oczekujące przełączenie z usługi AD do głównej zawartości.           |
| Pakowarki            | Azure Media Services "punkt końcowy przesyłania strumieniowego" zapewnia dynamiczne możliwości tworzenia pakietów dla ŁĄCZNIKów i HLS oraz jest określana jako "Pakowarka" w branży multimedialnej.                                                                              |
| Godzina prezentacji   | Godzina, o której zdarzenie jest prezentowane w przeglądarce. Czas przedstawia chwilę na osi czasu nośnika, w której podgląd zobaczy zdarzenie. Na przykład godzina prezentacji SCTE-35 splice_info () komunikat polecenia jest splice_time (). |
| Czas przybycia        | Godzina nadejścia komunikatu o zdarzeniu. Czas jest zwykle różny od czasu prezentacji zdarzenia, ponieważ komunikaty zdarzeń są wysyłane przed czasem prezentacji zdarzenia.                                                    |
| Ścieżka rozrzedzona        | ścieżka nośnika, która nie jest ciągła i jest czas synchronizowana z ścieżką nadrzędną lub śledzeniem kontrolek.                                                                                                                                                  |
| Origin              | Usługa przesyłania strumieniowego multimediów Azure                                                                                                                                                                                                             |
| Ujścia kanału        | Usługa Azure Media Live Streaming                                                                                                                                                                                                        |
| HLS                 | Protokół Apple HTTP Live Streaming                                                                                                                                                                                                            |
| OBRAMOWAN                | Dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP                                                                                                                                                                                                          |
| Równomier              | Protokół Smooth Streaming                                                                                                                                                                                                                     |
| MPEG2 — TS            | Strumienie transportowe MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Real-Time protokół multimedialny                                                                                                                                                                                                                 |
| uimsbf              | Liczba całkowita bez znaku, najbardziej znaczący bit.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 odwołania normatywne

Następujące dokumenty zawierają postanowienia, które za pomocą odwołania w tym tekście stanowią postanowienia tego dokumentu. Wszystkie dokumenty podlegają zmianom w treści standardów, a czytelnicy są zachęcani do zbadania możliwości zastosowania najnowszych wersji dokumentów wymienionych poniżej. Czytelnicy są również przypomnili, że nowsze wersje dokumentów, do których istnieją odwołania, mogą nie być zgodne z tą wersją specyfikacji metadanych czasowych dla Azure Media Services.


| Standardowa          | Definicja                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Specyfikacja sygnalizowania Primetime Digital Programs 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash AS]  | [Dokumentacja języka FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Format komunikatu akcji AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [ŁĄCZNIK-IF-REDUKCJA]     | KRESKOWANY forum branżowe wskazówki dotyczące międzyoperacyjności v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadane czasu dla HTTP Live Streaming- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadane czasowe w formacie Common Media Application (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | 2.4.0 tag ID3 wersja  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: część 12 ISO Base plik multimedialny format, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Technologia informacyjna — dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (KRESKa) — część 1: Opis prezentacji multimediów i formaty segmentów. 2014 maja. Publikacj. Adres URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Technologia informacyjna — format aplikacji multimedialnej (MPEG-A) — część 19: Common Media Application Format (CMAF) dla nośnika z segmentacją. Styczeń 2018. Publikacj. Adres URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Technologia informacyjna--— część 7: typowe szyfrowanie w plikach formatu plików multimediów ISO Base. Luty 2016. Publikacj. Adres URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 maja 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-pozyskiwanie]  | [Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | ®. Pantos, Ed.; W. mogą. HTTP Live Streaming. 2017 sierpnia. Informacyjną. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Kodowanie danych Base16, Base32 i base64 — [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Protokół Real-Time Messaging firmy Adobe", 21 grudnia 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019 — cyfrowy Wstawianie programu cueing wiadomość dla kabla https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 — KRESKa MPEG dla IP-Based usług telewizji kablowej część 1: MPD ograniczenia i rozszerzenia                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG PAUZy dla usług kabli IP-Based część 3: profil PAUZy/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 — planowanie zdarzeń i interfejs powiadamiania                                                                                                                                                  |
| [SCTE-250]        | Interfejs API zarządzania zdarzeniami i sygnalizacją (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Przekroczono limit czasu pozyskiwania metadanych

Azure Media Services obsługuje metadane w czasie rzeczywistym dla protokołów [RTMP] i Smooth Streaming [MS-SSTR-pozyskiwania]. Metadane w czasie rzeczywistym mogą służyć do definiowania niestandardowych zdarzeń, z własnymi unikatowymi schematami niestandardowymi (JSON, Binary, XML), a także w formatach branżowych, takich jak ID3, lub SCTE-35 dla sygnałów AD w strumieniu emisji. 

Ten artykuł zawiera szczegółowe informacje dotyczące sposobu wysyłania niestandardowych sygnałów do metadanych czasowych przy użyciu obsługiwanych protokołów pozyskiwania Azure Media Services. W artykule wyjaśniono również, w jaki sposób manifesty dla HLS, ŁĄCZNIKa i Smooth Streaming są uzupełnione o czasowe sygnały metadanych, a także jak są przenoszone w paśmie, gdy zawartość jest dostarczana przy użyciu CMAF (fragmentów MP4) lub segmentów usługi Transport Stream (TS) dla HLS. 

Typowe scenariusze przypadków użycia dla metadanych czasowych obejmują:

 - SCTE-35 — sygnały usługi AD wyzwalające przerwy w usłudze AD w zdarzeniu na żywo lub w emisji liniowej
 - Niestandardowe metadane ID3, które mogą wyzwalać zdarzenia w aplikacji klienckiej (przeglądarce, systemu iOS lub Android)
 - Niestandardowe zdefiniowane dane JSON, dane binarne lub metadane XML do wyzwalania zdarzeń w aplikacji klienckiej
 - Dane telemetryczne z kodera na żywo, aparatu IP lub drona
 - Zdarzenia z kamery IP, takie jak ruch, wykrywanie czołowe itp.
 - Informacje o położeniach geograficznych z aparatu akcji, drona lub przenoszone urządzenie
 - Teksty utworów
 - Granice programu dla liniowego kanału informacyjnego
 - Obrazy lub rozszerzone metadane do wyświetlania na żywo kanału informacyjnego
 - Wyniki sportowe lub informacje o zegarze gier
 - Interaktywne pakiety reklamowe, które mają być wyświetlane obok filmu wideo w przeglądarce
 - Kwizy lub sondy
  
Azure Media Services zdarzenia na żywo i Pakowarka mogą odbierać te sygnały metadanych czasowych i przekształcać je w strumień metadanych, które mogą uzyskiwać dostęp do aplikacji klienckich przy użyciu protokołów opartych na standardach, takich jak HLS i łącznik.


## <a name="21-rtmp-timed-metadata"></a>2,1 metadane czasu RTMP

Protokół [RTMP] umożliwia wysyłanie sygnałów do metadanych czasowych dla różnych scenariuszy, w tym niestandardowych metadanych, i sygnałów AD SCTE-35. 

Sygnały anonsowania (komunikaty kontrolne) są wysyłane jako [AMF0] komunikaty kontrolne osadzone w strumieniu [RTMP]. Komunikaty kontrolne mogą być wysyłane jakiś czas przed wystąpieniem zdarzenia "AD splice" lub "[SCTE35]". W celu obsługi tego scenariusza rzeczywista sygnatura czasowa prezentacji zdarzenia jest wysyłana w ramach komunikatu wskaźnika. Aby uzyskać więcej informacji, zobacz [AMF0].

Następujące polecenia [AMF0] są obsługiwane przez Azure Media Services na potrzeby pozyskiwania RTMP:

- **onUserDataEvent** — używany na potrzeby metadanych niestandardowych lub [ID3v2]
- **onAdCue** — używane przede wszystkim do sygnalizowania możliwości rozmieszczenia anonsu w strumieniu na żywo. Obsługiwane są dwie formy wskaźnika, tryb prosty i tryb "SCTE-35". 
- **onCuePoint** — obsługiwane przez niektóre lokalne kodery sprzętowe, takie jak element kodera na żywo, do sygnałów [SCTE35] komunikatów. 
  

W poniższej tabeli opisano format ładunku komunikatu AMF, który Media Services zostanie wyzyskany dla trybów komunikatów "Simple" i [SCTE35].

Nazwa komunikatu [AMF0] może być używana do rozróżniania wielu strumieni zdarzeń tego samego typu.  W przypadku komunikatów [SCTE-35] i trybu "Simple" nazwa komunikatu AMF musi być typu "onAdCue", jak jest to wymagane w specyfikacji [Adobe-Primetime].  Wszelkie pola, które nie są wymienione poniżej, zostaną zignorowane przez Azure Media Services w trakcie pozyskiwania.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP z niestandardowymi metadanymi przy użyciu "onUserDataEvent"

Jeśli chcesz dostarczyć niestandardowe źródła metadanych z kodera-strumienia, aparatu IP, drona lub urządzenia przy użyciu protokołu RTMP, użyj polecenia "onUserDataEvent" [AMF0] komunikatu o danych.

Polecenie komunikatu danych **"onUserDataEvent"** musi zawierać ładunek komunikatu z następującą definicją, która ma zostać przechwycona przez Media Services i spakowane w formacie pliku w paśmie, a także manifesty dla HLS, łączników i Smooth Streaming.
Zalecane jest wysyłanie komunikatów o przekroczonym czasie — nie częściej niż co 0,5 sekund (500 ms) lub problemy ze stabilnością w strumieniu na żywo mogą wystąpić. Każdy komunikat może agregować metadane z wielu ramek, jeśli konieczne jest dostarczenie metadanych na poziomie ramki. W przypadku wysyłania strumieni o większej szybkości transmisji bitów zaleca się również dostarczenie metadanych pojedynczej szybkości transmisji bitów tylko w celu zmniejszenia przepustowości i uniknięcia zakłóceń w przetwarzaniu wideo/audio. 

Ładunek dla **"onUserDataEvent"** powinien być komunikatem formatu XML [MPEGDASH] EventStream. Dzięki temu można łatwo przekazywać niestandardowe zdefiniowane schematy, które mogą być przenoszone do emsg ' ładunków w paśmie dla zawartości CMAF [MPEGCMAF], która jest dostarczana za pośrednictwem protokołów HLS lub PAUZy. Każdy komunikat strumienia zdarzeń PAUZy zawiera schemeIdUri, który działa jako identyfikator schematu komunikatu URN i definiuje ładunek wiadomości. Niektóre schematy, takie jak " https://aomedia.org/emsg/ID3 " dla [ID3v2] lub **urn: SCTE: scte35:2013: bin** dla [SCTE-35] są standardowe dla konsorcjów branżowych na potrzeby współdziałania. Każdy dostawca aplikacji może zdefiniować własny schemat niestandardowy przy użyciu adresu URL, który kontroluje (domena własnością) i może podać specyfikację w tym adresie URL. Jeśli gracz ma procedurę obsługi dla zdefiniowanego schematu, to jest jedynym składnikiem, który musi zrozumieć ładunek i protokół.

Schemat dla ładunku [MPEG-myślnik] EventStream został zdefiniowany jako (wyciąg z KRESKi ISO-IEC-23009-1-trzecie wydanie). Należy zauważyć, że w tym momencie jest obsługiwane tylko jedno "EventType" na "EventStream". Tylko pierwszy element **zdarzenia** zostanie przetworzony, jeśli w **EventStream** podano wiele zdarzeń.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Przykładowy strumień zdarzeń XML z IDENTYFIKATORem schematu ID3 i ładunkiem danych zakodowanym w formacie base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Przykładowy strumień zdarzeń z niestandardowym IDENTYFIKATORem schematu i danymi binarnymi szyfrowanymi algorytmem Base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Przykładowy strumień zdarzeń z niestandardowym IDENTYFIKATORem schematu i niestandardowym kodem JSON  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Wbudowane identyfikatory URI obsługiwanych schematów
| Identyfikator URI identyfikatora schematu                 | Opis                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/emsg/ID3 | Opisuje, w jaki sposób metadane [ID3v2] mogą być przenoszone jako metadane z przekroczeniem czasu CMAF w postaci "MPEGCMAF], które są pofragmentowane. Aby uzyskać więcej informacji, zobacz [metadane czasowe w formacie Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Przetwarzanie zdarzeń i sygnalizowanie manifestu

Po odebraniu prawidłowego zdarzenia **"onUserDataEvent"** Azure Media Services szuka prawidłowego ładunku XML zgodnego z EventStreamType (zdefiniowanego w [MPEGDASH]), Przeanalizuj ładunek XML i przekonwertuj go na [MPEGCMAF] fragment MP4 "emsg" w wersji 1 dla magazynu w archiwum na żywo i transmisja do Pakowarki Media Services.   Pakowarka wykryje pole "emsg" w strumieniu na żywo i będzie:

- (a) "dynamicznie Pakuj" do segmentów TS w celu dostarczenia do klientów HLS zgodnych ze specyfikacją metadanych HLS czas [HLS-TMD] lub
- (b) przekazać go do dostarczania w fragmentach CMAF za pośrednictwem HLS lub KRESKi lub 
- (c) Konwertuj go na sygnał ścieżki rozrzedzonej na potrzeby dostarczania za pośrednictwem Smooth Streaming [MS-SSTR].

Oprócz zestawu emsg ' CMAF lub pakietów PES serwera terminali dla HLS, manifesty dla ŁĄCZNIKa (MPD) i Smooth Streaming będą zawierać odwołanie do strumieni zdarzeń w paśmie (nazywanych również ścieżką strumienia rozrzedzonego w Smooth Streaming).

Pojedyncze zdarzenia lub ich ładunki danych nie są wyprowadzane bezpośrednio w HLS, MYŚLNIKu lub gładkich manifestach. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Dodatkowe ograniczenia informacyjne i wartości domyślne dla zdarzeń onUserDataEvent

- Jeśli skala czasu nie jest ustawiona w elemencie EventStream, domyślnie jest używana Skala czasu RTMP 1 kHz
- Dostarczenie komunikatu onUserDataEvent jest ograniczone do co 500 MS max. Jeśli zdarzenia są wysyłane częściej, może to mieć wpływ na przepustowość i stabilność kanału informacyjnego na żywo

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 "onAdCue" sygnalizujący sygnał "{0}"

Azure Media Services może nasłuchiwać i odpowiadać na kilka typów komunikatów [AMF0], które mogą służyć do sygnalizowania różnych metadanych zsynchronizowanych w czasie rzeczywistym w strumieniu na żywo.  Specyfikacja [Adobe-Primetime] definiuje dwa typy sygnalizacji o nazwie "Simple" i "SCTE-35". W przypadku trybu "Simple" Media Services obsługuje pojedyncze AMF komunikat o nazwie "onAdCue" przy użyciu ładunku zgodnego z tabelą poniżej zdefiniowanej dla sygnału "tryb prosty".  

W poniższej sekcji przedstawiono ładunek protokołu RTMP "Simple" Mode, który może służyć do sygnalizowania podstawowego sygnału usługi AD "spliceOut", który będzie przenoszony do manifestu klienta dla HLS, ŁĄCZNIKa i Microsoft Smooth Streaming. Jest to bardzo przydatne w scenariuszach, w których klient nie ma złożonego wdrożenia usługi AD lub systemu wstawiania opartego na SCTE-35 i używa podstawowego kodera lokalnego do wysyłania wiadomości kontrolnej za pośrednictwem interfejsu API. Zwykle koder lokalny będzie obsługiwał interfejs API oparty na protokole REST, aby wyzwolić ten sygnał, który również będzie "odfiltrować" strumień wideo, wstawiając ramkę IDR do filmu wideo i rozpoczynając nową grupę GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 "onAdCue" sygnalizowanie za pomocą usługi AD Cue

| Nazwa pola | Typ pola | Wymagane? | Znajduje                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| typ       | Ciąg     | Wymagane  | Komunikat zdarzenia.  Powinien być "SpliceOut", aby wyznaczyć Metoda łączenia w trybie prostym.                                                                                                                                                                                                         |
| identyfikator         | Ciąg     | Wymagane  | Unikatowy identyfikator opisujący metody łączenia lub segmentów. Identyfikuje to wystąpienie komunikatu                                                                                                                                                                                       |
| czas trwania   | Liczba     | Wymagane  | Czas trwania łączenia. Jednostki są ułamkami sekund.                                                                                                                                                                                                                           |
| elapsed    | Liczba     | Opcjonalne  | Gdy sygnał jest powtarzany w celu obsługi dostrajania w, to pole jest ilością czasu prezentacji, który upłynął od początku metody łączenia. Jednostki są ułamkami sekund. W przypadku korzystania z trybu prostego ta wartość nie powinna przekroczyć oryginalnego czasu trwania łączenia. |
| time       | Liczba     | Wymagane  | Jest to godzina łączenia w czasie prezentacji. Jednostki są ułamkami sekund.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Przykład danych wyjściowych manifestu MPEG PAUZy podczas korzystania z trybu prostego Adobe RTMP

Zobacz przykład [3.3.2.1 MPEG pauz. mpd EventStream przy użyciu trybu prostego Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Zobacz przykład [3.3.3.1 kreskowany z pojedynczym kropką i trybem prostym Adobe ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Przykład danych wyjściowych manifestu HLS w przypadku korzystania z trybu prostego Adobe RTMP

Zobacz przykład [3.2.2 HLS manifestu przy użyciu programu Adobe Simple Mode i EXT-X-Cue tag](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>zasygnalizowanie w trybie "onAdCue"-SCTE-35

Podczas pracy z bardziej zaawansowanym przepływem pracy produkcyjnym, który wymaga przeprowadzenia pełnego komunikatu ładunku SCTE-35 do manifestu HLS lub PAUZy, najlepiej jest używać trybu "SCTE-35" w specyfikacji [Adobe-Primetime].  Ten tryb obsługuje w-paśmie sygnały SCTE-35 wysyłane bezpośrednio do lokalnego kodera na żywo, który następnie koduje sygnały w strumieniu RTMP przy użyciu trybu "SCTE-35" określonego w specyfikacji [Adobe-Primetime]. 

Zazwyczaj komunikaty SCTE-35 mogą być wyświetlane tylko w danych wejściowych strumienia (TS) w formacie MPEG-2 w koderie lokalnym. Skontaktuj się z producentem kodera, aby uzyskać szczegółowe informacje na temat sposobu konfigurowania pozyskiwania strumienia transportowego, który zawiera SCTE-35 i włącz go na potrzeby przekazywania do usługi RTMP w trybie Adobe SCTE-35.

W tym scenariuszu należy wysłać następujący ładunek z kodera lokalnego przy użyciu typu komunikatu **"onAdCue"** [AMF0].

| Nazwa pola | Typ pola | Wymagane? | Znajduje                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| kontrol        | Ciąg     | Wymagane  | Komunikat zdarzenia.  W przypadku komunikatów [SCTE-35] musi to być plik binarny szyfrowany algorytmem Base64 [RFC4648] splice_info_section (), aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych.                                                                                                                                                                                                                               |
| typ       | Ciąg     | Wymagane  | Nazwa URN lub adres URL identyfikujący schemat komunikatów. W przypadku komunikatów [SCTE-35] **powinno** to być **"scte35"** , aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych, w zgodności z [Adobe-Primetime]. Opcjonalnie można również użyć nazwy URN: SCTE: scte35:2013: bin, aby sygnalizować komunikat [SCTE-35].                                                                                                        |
| identyfikator         | Ciąg     | Wymagane  | Unikatowy identyfikator opisujący metody łączenia lub segmentów. Identyfikuje to wystąpienie komunikatu.  Komunikaty o równoważnej semantyce mają tę samą wartość.                                                                                                                                                                                                                                                       |
| czas trwania   | Liczba     | Wymagane  | Czas trwania zdarzenia lub segmentu łączenia AD, jeśli jest znany. Jeśli nieznany, wartość **powinna** być równa 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Liczba     | Opcjonalne  | Gdy sygnał usługi AD [SCTE-35] jest powtarzany w celu dostrojenia, to pole jest ilością czasu prezentacji, który upłynął od rozpoczęcia łączenia. Jednostki są ułamkami sekund. W trybie [SCTE-35] Ta wartość może przekroczyć pierwotny określony czas trwania łączenia lub segmentu.                                                                                                                   |
| time       | Liczba     | Wymagane  | Czas prezentacji zdarzenia lub łączenia z usługą AD.  Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]. Dla ruchu wychodzącego HLS, czas i czas trwania **powinny być** wyrównane z granicami segmentów. Czas prezentacji i czas trwania różnych komunikatów o zdarzeniach w ramach tego samego strumienia zdarzeń nie mogą nakładać się na siebie. Jednostki są ułamkami sekund. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Przykład HLS manifest. M3U8 z sygnałem trybu SCTE-35
Zobacz [przykładowe 3.2.1.1 przykład HLS manifestu z SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 "onCuePoint" przy użyciu protokołu RTMP dla elementów na żywo

Dynamiczny koder on-premises Encoder obsługuje znaczniki usługi AD w sygnale RTMP. Azure Media Services obecnie obsługuje tylko typ znacznika "onCuePoint" dla protokołu RTMP.  Tę funkcję można włączyć w ustawieniach grupy firmy Adobe RTMP w ustawieniach usługi Media Encoder na żywo lub w interfejsie API, ustawiając wartość "**ad_markers**" na "onCuePoint".  Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją na żywo. Włączenie tej funkcji w grupie RTMP spowoduje przekazanie sygnałów SCTE-35 do danych wyjściowych Adobe RTMP do przetworzenia przez Azure Media Services.

Typ komunikatu "onCuePoint" jest zdefiniowany w [Adobe-Flash-AS] i ma następującą strukturę ładunku, gdy jest wysyłana z elementów danych wyjściowych na żywo.


| Właściwość   | Opis                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Nazwa powinna mieć wartość "**scte35**" przez element aktywny.                                                                                                                                                                              |
| time       | Czas (w sekundach), po którym punkt kontrolny wystąpił w pliku wideo podczas osi czasu                                                                                                                                           |
| typ       | Typ punktu kontrolnego powinien mieć ustawioną wartość "**Event**".                                                                                                                                                                             |
| parameters | Tablica asocjacyjna ciągów par nazwa/wartość zawierająca informacje z komunikatu SCTE-35, w tym identyfikator i czas trwania. Te wartości są analizowane przez Azure Media Services i zawarte w znaczniku dekoracji manifestu. |


Gdy jest używany ten tryb znacznika AD, dane wyjściowe manifestu HLS są podobne do trybu "Simple" programu Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Przykładowa MPEG PAUZy, Single kropka, sygnały w trybie prostym Adobe

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Przykładowa lista odtwarzania HLS, sygnalizowanie w trybie prostym firmy Adobe przy użyciu tagu EXT-X-CUE (obcięty "..." dla zwięzłości)

Poniższy przykład przedstawia dane wyjściowe z Media Servicesego dynamicznego Pakowarki dla strumienia pozyskiwania RTMP przy użyciu sygnałów trybu "Simple" i starszej wersji [Adobe-Primetime].  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 Anulowanie i aktualizacje

Komunikaty można anulować lub zaktualizować, wysyłając wiele komunikatów z tym samym czasem prezentacji i IDENTYFIKATORem. Czas prezentacji i identyfikator jednoznacznie identyfikują zdarzenie, a ostatni komunikat odebrany dla określonego czasu prezentacji, który spełnia ograniczenia przed przystąpieniem, jest komunikatem, na którym działa. Zaktualizowane zdarzenie zastępuje wszystkie wcześniej odebrane komunikaty. Ograniczenie przed przyrzutem to cztery sekundy. Komunikaty odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną podjęte.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 fragmentacja MP4 (Smooth Streaming)

Zapoznaj się z artykułem [MS-SSTR-pozyskiwanie], aby uzyskać wymagania dotyczące pozyskiwania strumieniowego na żywo. Poniższe sekcje zawierają szczegółowe informacje dotyczące pozyskiwania metadanych prezentacji czasowej.  Metadane prezentacji czasowej są pozyskiwane jako ścieżka rozrzedzona, która jest definiowana w polu manifestu serwera na żywo (zobacz MS-SSTR) i pole filmu ("Moov").  

Każdy fragment rozrzedzony składa się z pola fragmentu filmu ("moof") i nośnika urządzenie Data Box ("MDAT"), gdzie pole "MDAT" jest wiadomością binarną.

Aby osiągnąć dokładne wstawianie reklam, koder musi podzielić fragment w czasie prezentacji, w którym należy wstawić wskaźnik.  NALEŻY utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR, lub do strumienia punktów dostępu (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12].
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>Pole manifestu aktywnego serwera

Ścieżka rozrzedzona **musi** być zadeklarowana w polu manifestu serwera na żywo z **\<textstream\>** wpisem i **musi** mieć ustawione następujące atrybuty:

| **Nazwa atrybutu** | **Typ pola** | **Wymagane?** | **Opis**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Liczba         | Wymagane      | **Musi** mieć wartość "0", co oznacza, że ścieżka z nieznaną zmienną szybkością transmisji bitów.                                                                                                                                                          |
| parentTrackName    | Ciąg         | Wymagane      | **Musi** być nazwą ścieżki nadrzędnej, do której są wyrównane Skala czasu ścieżki rozrzedzonej. Ścieżka nadrzędna nie może być ścieżką rozrzedzoną.                                                                             |
| manifestOutput     | Wartość logiczna        | Wymagane      | **Musi** mieć wartość "true", aby wskazać, że ścieżka rozrzedzona zostanie osadzona w niezakłóconym manifeście klienta.                                                                                                                        |
| Podtyp            | Ciąg         | Wymagane      | **Musi** to być czterocyfrowy kod znaku "Data".                                                                                                                                                                                  |
| Schemat             | Ciąg         | Wymagane      | **Musi** być identyfikatorem urn lub adresem URL identyfikującym schemat komunikatów. W przypadku komunikatów [SCTE-35] **musi** to być "urn: SCTE: scte35:2013: bin", aby komunikaty były wysyłane do programu HLS, gładkie i przerywane klientów zgodne z programem [SCTE-35]. |
| ścieżka śledzenia          | Ciąg         | Wymagane      | **Musi** to być nazwa ścieżki rozrzedzonej. Wartość trackname może być używana do rozróżniania wielu strumieni zdarzeń z tym samym schematem. Każdy unikatowy strumień zdarzeń **musi** mieć unikatową nazwę ścieżki.                                |
| Dział          | Liczba         | Opcjonalne      | **Musi** to być Skala czasu ścieżki nadrzędnej.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 pole filmu

Pole filmu ("Moov") następuje po polu manifestu serwera na żywo jako część nagłówka strumienia dla ścieżki rozrzedzonej.

Pole "Moov" **powinno** zawierać pole **TrackHeaderBox ("tkhd")** zdefiniowane w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| czas trwania       | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Powinna** mieć wartość 0, ponieważ pole śledzenia ma zero próbek i łączny czas trwania próbek w polu śledzenia wynosi 0. |

---

Pole "Moov" **powinno** zawierać element **HandlerBox ("HDLR")** , jak zdefiniowano w [ISO-14496-12] z następującymi ograniczeniami:

| **Nazwa pola** | **Typ pola**          | **Wymagane?** | **Opis**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bitowa liczba całkowita bez znaku | Wymagane      | **Powinien** być "meta". |

---

Pole "stsd" **powinno** zawierać pole MetaDataSampleEntry z nazwą kodową zdefiniowaną w [ISO-14496-12].  Na przykład w przypadku komunikatów SCTE-35 nazwa kodowania **powinna** mieć wartość "SCTE".

### <a name="223-movie-fragment-box-and-media-data-box"></a>Pole fragmentu filmu i urządzenie Data Box multimediów

Fragmenty ścieżki rozrzedzonej składają się z pola fragmentu filmu ("moof") i nośnika urządzenie Data Box ("MDAT").

> [!NOTE]
> Aby osiągnąć dokładne wstawianie reklam, koder musi podzielić fragment w czasie prezentacji, w którym należy wstawić wskaźnik.  NALEŻY utworzyć nowy fragment, który rozpoczyna się od nowo utworzonej ramki IDR, lub do strumienia punktów dostępu (SAP) typu 1 lub 2, jak zdefiniowano w [ISO-14496-12] załącznik I
> 

Pole MovieFragmentBox ("moof") **musi** zawierać pole **TrackFragmentExtendedHeaderBox ("UUID")** zdefiniowane w [MS-SSTR] z następującymi polami:

| **Nazwa pola**         | **Typ pola**          | **Wymagane?** | **Opis**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Musi** być czasem przybycia zdarzenia. Ta wartość wyrównuje komunikat z ścieżką nadrzędną.           |
| fragment_duration      | 64-bitowa liczba całkowita bez znaku | Wymagane      | **Musi** to być czas trwania zdarzenia. Czas trwania może być równy zero, aby wskazać, że czas trwania jest nieznany. |

---


Pole MediaDataBox ("MDAT") **musi** mieć następujący format:

| **Nazwa pola**          | **Typ pola**                   | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Wersja                 | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Określa format zawartości pola "MDAT". Nierozpoznane wersje zostaną zignorowane. Obecnie jedyną obsługiwaną wersją jest 1.                                                                                                                                                                                                                                                                                                                                                                      |
| identyfikator                      | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Identyfikuje to wystąpienie komunikatu. Komunikaty o równoważnej semantyce muszą mieć taką samą wartość; oznacza to, że przetwarzanie dowolnego jednego okna komunikatu o tym samym identyfikatorze jest wystarczające.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitowa liczba całkowita bez znaku (uimsbf) | Wymagane      | Suma fragment_absolute_time, określona w TrackFragmentExtendedHeaderBox, a presentation_time_delta **musi** być czasem prezentacji zdarzenia. Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12]. Dla ruchu wychodzącego HLS, czas i czas trwania **powinny być** wyrównane z granicami segmentów. Czas prezentacji i czas trwania różnych komunikatów o zdarzeniach w ramach tego samego strumienia zdarzeń nie **mogą** nakładać się na siebie. |
| message                 | tablica bajtów                       | Wymagane      | Komunikat zdarzenia. W przypadku komunikatów [SCTE-35] komunikat jest binarny splice_info_section (). W przypadku komunikatów [SCTE-35] **musi** to być splice_info_section (), aby komunikaty były wysyłane do klientów HLS, gładkich i kresowych z zachowaniem zgodności z [SCTE-35]. W przypadku komunikatów [SCTE-35] splice_info_section binarny () jest ładunkiem pola "MDAT" i **nie** jest zakodowana w formacie base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>Aktualizacja 2.2.4 i aktualizacje

Komunikaty można anulować lub zaktualizować, wysyłając wiele komunikatów z tym samym czasem prezentacji i IDENTYFIKATORem.  Godzina i identyfikator prezentacji jednoznacznie identyfikują zdarzenie. Ostatnia wiadomość odebrana dla określonego czasu prezentacji, która spełnia ograniczenia wstępnego przyrzutu, jest komunikatem, na którym jest wykonywane działanie. Zaktualizowany komunikat zastępuje wszystkie odebrane wcześniej komunikaty.  Ograniczenie przed przyrzutem to cztery sekundy. Komunikaty odebrane co najmniej cztery sekundy przed czasem prezentacji zostaną podjęte. 


## <a name="3-timed-metadata-delivery"></a>3 czasowe dostarczanie metadanych

Dane strumienia zdarzeń są nieprzezroczyste dla Media Services. Media Services jedynie przekazuje trzy informacje między punktem końcowym pozyskiwania a punktem końcowym klienta. Następujące właściwości są dostarczane do klienta programu zgodnie z przepisami [SCTE-35] i/lub protokołem przesyłania strumieniowego klienta:

1.  Schemat — nazwa URN lub adres URL identyfikujący schemat komunikatu.
2.  Czas prezentacji — czas prezentacji zdarzenia na osi czasu nośnika.
3.  Czas trwania — czas trwania zdarzenia.
4.  ID — opcjonalny unikatowy identyfikator zdarzenia.
5.  Message — dane zdarzenia.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 manifest Smooth Streaming firmy Microsoft  

Aby uzyskać szczegółowe informacje na temat formatowania ścieżki komunikatów rozrzedzonych, zapoznaj się z tematem obsługa ścieżki rozrzedzonej [MS-SSTR]. W przypadku komunikatów [SCTE35] Smooth Streaming będzie wyprowadzać zakodowane w formacie base64 splice_info_section () do fragmentu rozrzedzonego.
StreamIndex **musi** mieć PODTYP "Data", a CustomAttributes — **musi** zawierać atrybut o nazwie "Schema" i value = "urn: SCTE: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Przykładowy przykład manifestu klienta z kodowaniem Base64 [SCTE35] splice_info_section ()
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>HLS 3,2

Azure Media Services obsługuje następujące Tagi manifestu HLS do sygnalizowania informacji o Dostie usługi AD podczas zdarzenia na żywo lub na żądanie. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE zgodnie z definicją w [Adobe-Primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

Dane wyjściowe danych do poszczególnych tagów będą się różnić w zależności od używanego trybu pozyskiwania sygnału. Na przykład tryb pozyskiwania RTMP przy użyciu rozwiązania Adobe Simple nie zawiera pełnego SCTE-35 zakodowanego w formacie base64.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 przykład HLS manifest. M3U8 pokazujący sygnalizowanie EXT-X-CUE of SCTE-35

Poniższy przykład danych wyjściowych manifestu HLS Media Services z dynamicznego Pakowarki zawiera tag EXT-X-CUE dla [Adobe-Primetime] w trybie SCTE35. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS z Adobe Primetime EXT-X-CUE

Media Services (interfejs API w wersji 2 i 3) obsługuje dane wyjściowe taga EXT-X-CUE, jak zdefiniowano w [Adobe-Primetime] "SCTE-35 Mode". W tym trybie Azure Media Services osadzi zakodowane algorytmem Base64 [SCTE-35] splice_info_section () w tagu EXT-X.  

"Stary" "starszy" tag "EXT-X-CUE" jest zdefiniowany poniżej, a także może być przywoływany w specyfikacji [Adobe-Primetime]. Ta wartość powinna być używana tylko w przypadku starszych sygnałów SCTE35, gdy jest to możliwe. w przeciwnym razie zalecany tag jest zdefiniowany w [RFC8216] jako EXT-X-DATERANGE. 

| **Nazwa atrybutu** | **Typ**                      | **Wymagane?**                             | **Opis**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| KONTROL                | ciąg w cudzysłowie                 | Wymagane                                  | Komunikat zakodowany jako ciąg zakodowany algorytmem Base64, zgodnie z opisem w [RFC4648]. W przypadku komunikatów [SCTE-35] jest to splice_info_section zakodowane w formacie base64 ().                                                                                                                                      |
| TYP               | ciąg w cudzysłowie                 | Wymagane                                  | Nazwa URN lub adres URL identyfikujący schemat komunikatów. W przypadku komunikatów [SCTE-35] typ przyjmuje wartość specjalną "scte35".                                                                                                                                                                          |
| ID (Identyfikator)                 | ciąg w cudzysłowie                 | Wymagane                                  | Unikatowy identyfikator zdarzenia. Jeśli identyfikator nie zostanie określony podczas pozyskiwania wiadomości, Azure Media Services wygeneruje unikatowy identyfikator.                                                                                                                                              |
| DURATION           | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Czas trwania zdarzenia. Jeśli nieznany, wartość **powinna** być równa 0. Jednostki są factional s.                                                                                                                                                                                           |
| KTÓRY upłynął            | dziesiętna liczba zmiennoprzecinkowa | Opcjonalne, ale wymagane dla przesuwanego okna | Gdy sygnał jest powtarzany do obsługi okna prezentacji ruchomej, to pole **musi** być ilością czasu prezentacji, która upłynęła od momentu rozpoczęcia zdarzenia. Jednostki są ułamkami sekund. Ta wartość może przekroczyć pierwotny określony czas trwania łączenia lub segmentu. |
| TIME               | dziesiętna liczba zmiennoprzecinkowa | Wymagane                                  | Czas prezentacji zdarzenia. Jednostki są ułamkami sekund.                                                                                                                                                                                                                        |

Warstwa aplikacji HLS Player użyje typu, aby zidentyfikować format wiadomości, zdekodować komunikat, zastosować wymagane konwersje czasu i przetworzyć zdarzenie.  Zdarzenia są synchronizowane czasowo na liście odtwarzania segmentu ścieżki nadrzędnej zgodnie z sygnaturą czasową zdarzenia.  Są one wstawiane przed najbliższym segmentem (#EXTINF tagu).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 HLS. M3U8 — przykład manifestu przy użyciu Adobe Primetime EXT-X-CUE

Poniższy przykład przedstawia dekorację manifestu HLS przy użyciu znacznika Adobe Primetime EXT-X-CUE.  Parametr "CUE" zawiera tylko właściwości typu i czasu trwania, co oznacza, że jest to źródło RTMP korzystające z sygnałów trybu "proste" w trybie Adobe.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 Obsługa komunikatów HLS dla elementu Adobe Primetime EXT-X-CUE

Zdarzenia są sygnalizowane w postaci listy odtwarzania segmentów dla każdej ścieżki audio i wideo. Pozycja taga EXT-X-CUE **musi** zawsze być bezpośrednio przed pierwszym segmentem HLS (w celu oddzielenia lub rozpoczęcia segmentu) lub bezpośrednio po ostatnim segmencie HLS (w przypadku metody splice w lub segmencie), do której odwołują się atrybuty czasu i czasu trwania, zgodnie z wymaganiami [Adobe-Primetime].

Po włączeniu okna prezentacji ruchomej, tag "EXT-X-CUE" **musi** być powtarzany na tyle często, że element splice lub segment jest zawsze w pełni opisany na liście odtwarzania segmentu, a atrybut, który upłynął, **musi** być używany do wskazywania czasu, w którym jest aktywny lub segment, zgodnie z wymaganiami [Adobe-Primetime].

Po włączeniu okna prezentacji ruchomej Tagi EXT-X są usuwane z listy odtwarzania segmentów, gdy czas, do którego się odwołuje, została wyniesiona z okna prezentacji przesuwanej.

## <a name="33-dash-manifest-decoration-mpd"></a>Dekoracja manifestu 3,3 (MPD)

[MPEGDASH] oferuje trzy sposoby sygnalizowania zdarzeń:

1.  Zdarzenia sygnalizowane w EventStream MPD
2.  Zdarzenia sygnalizowane w paśmie przy użyciu okna komunikatu o zdarzeniu ("emsg")
3.  Kombinacja wartości 1 i 2

Zdarzenia sygnalizowane w EventStream MPD są przydatne w przypadku przesyłania strumieniowego VOD, ponieważ klienci mają dostęp do wszystkich zdarzeń, natychmiast po pobraniu elementu MPD. Jest on również przydatny do sygnalizowania SSAI, gdzie podrzędny dostawca SSAI musi analizować sygnały z manifestu MPD i dynamicznie wstawiać zawartość usługi AD.  Rozwiązanie w paśmie ("emsg") jest przydatne w przypadku przesyłania strumieniowego na żywo, gdzie klienci nie muszą ponownie pobierać elementu MPD lub nie ma możliwości manipulowania manifestem SSAI między klientem a pochodzeniem. 

Azure Media Services domyślnym zachowaniem ŁĄCZNIKa jest sygnalizowanie zarówno w EventStream MPD, jak i w paśmie przy użyciu okna komunikatu zdarzenia ("emsg").

Komunikaty wskaźnika pozyskiwane za pośrednictwem protokołu [RTMP] lub [MS-SSTR-pozyskiwania] są zamapowane na zdarzenia PAUZy, korzystając z pól emsg ' i/lub w EventStreams. 

W paśmie SCTE-35 sygnalizowanie dla ŁĄCZNIKa następuje po definicji i wymaganiach zdefiniowanych w [SCTE-214-3], a także w sekcji [PAUZa-IF-redukcja] 13.12.2 ("SCTE35 Events"). 

W przypadku korzystania z programu w paśmie [SCTE-35] pole komunikatu o zdarzeniu ("emsg") używa schemeId = "urn: SCTE: scte35:2013: bin". W przypadku dekoracji manifestu MPD EventStream schemeId używa "urn: SCTE: scte35:2014: XML + bin".  Ten format jest reprezentacją XML zdarzenia, która zawiera dane wyjściowe binarnej kodowanej algorytmem Base64 kompletnego komunikatu SCTE-35, który dotarł do pozyskania. 

Definicje odwołania normatywnego przewozu [SCTE-35] są dostępne w ramach [SCTE-214-1] sek 6.7.4 (MPD) i [SCTE-214-3] sek 7.3.2 (przewóz z SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 sygnalizowanie MPEG PAUZy (MPD) EventStream

W manifeście (MPD) dekoracja zdarzeń zostanie zasygnalizowania przy użyciu elementu EventStream, który pojawia się w ramach elementu period. Używany schemeId to "urn: SCTE: scte35:2014: XML + bin".


> [!NOTE]
> Dla celów zwięzłości [SCTE-35] umożliwia użycie sekcji kodowanej algorytmem Base64 w elemencie Signal. Binary (a nie w elemencie Signal. SpliceInfoSection) jako alternatywę dla przewozu całkowicie przeanalizowanego komunikatu wskaźnika.
> Azure Media Services używa tego podejścia "XML + bin" do sygnalizowania w manifeście MPD.
> Jest to również zalecana metoda użyta w [łącznik-IF-redukcja]-Zobacz sekcję zatytułowaną ["strumienie zdarzeń wstawiania usługi AD" kreski, jeśli wytyczne redukcja](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


Element EventStream ma następujące atrybuty:

| **Nazwa atrybutu** | **Typ**                | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | ciąg                  | Wymagane      | Identyfikuje schemat komunikatu. Schemat jest ustawiony na wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość **powinna** być identyfikatorem urn lub adresem URL identyfikującym schemat komunikatów; Obsługiwane wyjściowe schemeId powinny mieć wartość "urn: SCTE: scte35:2014: XML + bin" na [SCTE-214-1] s 6.7.4 (MPD), ponieważ usługa obsługuje tylko "XML + bin" w tej chwili dla zwięzłości w MPD. |
| wartość              | ciąg                  | Opcjonalne      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby można było rozróżnić wiele strumieni zdarzeń z tym samym schematem, wartość **musi** być ustawiona na nazwę strumienia zdarzeń (trackname dla [MS-SSTR-pozyskiwania] lub AMF nazwę komunikatu dla [RTMP] pozyskiwania).                                                                         |
| Skala czasu          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skala czasu w taktach na sekundę.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 przykładowe strumienie zdarzeń dla KRESKi MPEG

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 przykładowy kod MPEG PAUZy, sygnalizowanie przesyłania strumieniowego RTMP przy użyciu trybu prostego Adobe

Poniższy przykład przedstawia fragment EventStream z Media Servicesego dynamicznego Pakowarki dla strumienia RTMP przy użyciu funkcji Sygnalizowanie w trybie prostym firmy Adobe.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 przykładowy skrypt MPEG PAUZy. mpd, sygnalizowanie strumienia RTMP przy użyciu trybu Adobe SCTE-35

Poniższy przykład przedstawia fragment EventStream z Media Services dynamicznego Pakowarki dla strumienia RTMP przy użyciu funkcji Sygnalizowanie w trybie Adobe SCTE-35.

Przykład EventStream elementu przy użyciu kodu XML + bin sygnalizowanie na [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Należy pamiętać, że presentationTime to czas prezentacji dla zdarzenia [SCTE-35] przetłumaczony na czas rozpoczęcia okresu, a nie czas przybycia komunikatu.
> [MPEGDASH] definiuje Event@presentationTime jako "określa czas prezentacji zdarzenia względem początku okresu.
> Wartość czasu prezentacji w sekundach to podział wartości tego atrybutu i wartość EventStream@timescale atrybutu.
> Jeśli nie istnieje, wartość czasu prezentacji wynosi 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 przykład: manifest PAUZy MPEG (MPD) z pojedynczym okresem, EventStream, przy użyciu sygnałów w trybie prostym firmy Adobe

W poniższym przykładzie przedstawiono dane wyjściowe z Media Servicesego dynamicznego Pakowarki dla źródłowego strumienia RTMP przy użyciu metody "Simple" trybu AD. Dane wyjściowe to manifest pojedynczego okresu przedstawiający EventStream przy użyciu identyfikatora URI schemeId ustawionego na "urn: com: Adobe: dpi: Simple: 2015" i Właściwość Value o wartości "simplesignal".
Każdy prosty sygnał jest udostępniany w elemencie zdarzenia z @presentationTime @duration właściwościami, i @id wypełnianymi na podstawie przychodzących sygnałów prostych.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>Sygnalizowanie okna komunikatów zdarzeń 3.3.4 PAUZy MPEG

Strumień zdarzeń znajdujących się w paśmie wymaga, aby element MPD miał obiekt InbandEventStream na poziomie zestawu adaptacji.  Ten element ma obowiązkowy atrybut schemeIdUri i opcjonalny parametr czasu, który jest również wyświetlany w oknie komunikatu zdarzenia ("emsg").  Pola komunikatów zdarzeń z identyfikatorami schematów, które nie są zdefiniowane w liście dozwolonych, nie **powinny** być obecne.

W przypadku przewozu w paśmie [SCTE-35] sygnały **muszą** używać schemeId = "urn: SCTE: scte35:2013: bin".
Normatywne definicje przewozu [SCTE-35] komunikatów w pasmie są zdefiniowane w [SCTE-214-3] sek 7.3.2 (przewóz komunikatów kontrolnych SCTE 35).

Poniższe szczegóły zawierają informacje o określonych wartościach, które klient powinien oczekiwać w "emsg" w zgodności z [SCTE-214-3]:

| **Nazwa pola**          | **Typ pola**          | **Wymagane?** | **Opis**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | ciąg                  | Wymagane      | Identyfikuje schemat komunikatu. Schemat jest ustawiony na wartość atrybutu schematu w polu manifestu serwera na żywo. Wartość **musi** być identyfikatorem urn identyfikującym schemat komunikatów. W przypadku komunikatów [SCTE-35] **musi** to być "urn: SCTE: scte35:2013: bin" w zgodności z [SCTE-214-3]          |
| Wartość                   | ciąg                  | Wymagane      | Dodatkowa wartość ciągu używana przez właścicieli schematu do dostosowywania semantyki wiadomości. Aby można było rozróżnić wiele strumieni zdarzeń z tym samym schematem, wartość zostanie ustawiona na nazwę strumienia zdarzeń (trackname dla wiadomości w przypadku pozyskiwania RTMP lub AMF). |
| Skala czasu               | 32-bitowa liczba całkowita bez znaku | Wymagane      | Skala czasu (w taktach na sekundę) pól czas i czas trwania w polu "emsg".                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bitowa liczba całkowita bez znaku | Wymagane      | Różnica czasu prezentacji nośnika w czasie prezentacji zdarzenia i Najwcześniejszy czas prezentacji w tym segmencie. Czas prezentacji i czas trwania **powinny być** wyrównane z punktami dostępu strumienia (SAP) typu 1 lub 2, zgodnie z definicją w załączniku I [ISO-14496-12].                                  |
| event_duration          | 32-bitowa liczba całkowita bez znaku | Wymagane      | Czas trwania zdarzenia lub 0xFFFFFFFF w celu wskazania nieznanego czasu trwania.                                                                                                                                                                                                                              |
| Id                      | 32-bitowa liczba całkowita bez znaku | Wymagane      | Identyfikuje to wystąpienie komunikatu. Komunikaty o równoważnej semantyce mają tę samą wartość. Jeśli identyfikator nie zostanie określony podczas pozyskiwania wiadomości, Azure Media Services wygeneruje unikatowy identyfikator.                                                                                        |
| Message_data            | tablica bajtów              | Wymagane      | Komunikat zdarzenia. W przypadku komunikatów [SCTE-35] dane komunikatu są binarną splice_info_section () w zgodności z [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Przykładowa jednostka InBandEvenStream dla trybu prostego Adobe
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Obsługa komunikatów PAUZy 3.3.5

Zdarzenia są sygnalizowane w paśmie w polu "emsg" dla ścieżek wideo i audio.  Sygnalizowanie występuje dla wszystkich żądań segmentów, dla których presentation_time_delta wynosi 15 sekund lub mniej. 

Po włączeniu okna prezentacji przewijania, komunikaty o zdarzeniach są usuwane z elementu MPD, gdy suma czasu i czasu trwania komunikatu o zdarzeniu jest mniejsza niż godzina danych nośnika w manifeście.  Innymi słowy, komunikaty o zdarzeniach są usuwane z manifestu, gdy czas, do którego się odwołuje, została wyniesiona z okna prezentacji przesuwanej.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 wskazówki dotyczące implementacji dla dostawców koderów

Poniższe wskazówki to typowe problemy, które mogą mieć wpływ na implementację tej specyfikacji przez dostawcę kodera.  Poniższe wskazówki zostały zebrane w oparciu o prawdziwe, ogólnoświatowe Opinie partnerów, aby ułatwić wdrożenie tej specyfikacji dla innych osób. 

[SCTE-35] komunikaty są pozyskiwane w formacie binarnym przy użyciu schematu **"urn: SCTE: scte35:2013: bin"** dla [MS-SSTR-pozyskiwania] i typu **"scte35"** dla [RTMP] pozyskiwania. Aby ułatwić konwersję czasu [SCTE-35], który jest oparty na sygnaturach czasowych prezentacji strumienia w formacie MPEG-2, mapowanie między znakami (pts_time + pts_adjustment splice_time ()) i osią czasu na nośniku jest zapewniane przez czas prezentacji zdarzenia (pole fragment_absolute_time dla bezproblemowego pozyskiwania i pola czasowego dla protokołu RTMP.). Mapowanie jest konieczne, ponieważ wartość 33-bitowej wartości "roll" przekracza około 26,5 godzin.

Smooth Streaming pozyskiwania [MS-SSTR-pozyskiwanie] wymaga, aby urządzenie Data Box nośnika ("MDAT") **musi** zawierać **splice_info_section ()** zdefiniowane w [SCTE-35]. 

W przypadku pozyskiwania RTMP atrybut wskaźnika komunikatu AMF jest ustawiany na splice_info_section szyfrowany algorytmem Base64 **()** zdefiniowany w [SCTE-35].  


Po powyższym formacie wiadomości są wysyłane do klientów HLS, gładkich i KRESek, zgodnie z definicją powyżej.  

Podczas testowania implementacji przy użyciu platformy Azure Media Services, najpierw Rozpocznij testowanie przy użyciu LiveEvent "pass-through" przed przejściem do testowania na LiveEvent kodowania.

---

## <a name="change-history"></a>Historia zmian

| Date (Data)     | Zmiany                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Poprawiono obsługę przyjmowania RTMP, dodano "onCuePoint" dla elementów na żywo                                            |
| 08/22/19 | Zaktualizowano w celu dodania OnUserDataEvent do protokołu RTMP dla metadanych niestandardowych                                                          |
| 1/08/20  | Naprawiono błąd w trybie Simple i SCTE35 RTMP. Zmieniono z "onCuePoint" na "onAdCue". Zaktualizowano tabelę trybu prostego. |
| 08/4/20  | Usunięto obsługę tagu DATERANGE w celu dopasowania do implementacji w usłudze produkcyjnej.    |

## <a name="next-steps"></a>Następne kroki
Wyświetl ścieżki uczenia Media Servicesowego.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
