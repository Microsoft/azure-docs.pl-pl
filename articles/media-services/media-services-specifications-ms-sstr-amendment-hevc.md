---
title: Poprawka protokołu Smooth Streaming Protocol (MS-SSTR) dla HEVC — Azure
description: Ta specyfikacja zawiera opis protokołu i formatu pofragmentowanych transmisji strumieniowych na żywo opartych na formacie MP4 z HEVC w Azure Media Services. Tylko zmiany wymagane do dostarczenia HEVC są określone w tym artykule, z wyjątkiem "(bez zmian)" oznacza, że tekst jest kopiowany tylko w celu wyjaśnienia.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6454bc863cb5fd628d581fff380c5ab61354f762
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87053045"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Poprawka protokołu Smooth Streaming Protocol (MS-SSTR) dla HEVC 

## <a name="1-introduction"></a>1 wprowadzenie 

Ten artykuł zawiera szczegółowe zmiany, które należy zastosować do specyfikacji protokołu Smooth Streaming [MS-SSTR] w celu włączenia Smooth Streaming zakodowanego wideo HEVC. W tej specyfikacji tworzymy tylko zmiany wymagane do dostarczenia kodera-dekoder wideo HEVC. Artykuł ten sam schemat numerowania jest zgodny ze specyfikacją [MS-SSTR]. Puste nagłówki przedstawione w artykule mają na celu ukierunkowanie czytelnika na ich położenie w specyfikacji [MS-SSTR].  "(Bez zmian)" oznacza, że tekst jest kopiowany tylko do celów wyjaśnień.

Artykuł zawiera wymagania techniczne dotyczące HEVC kodera-dekoder wideo (przy użyciu ścieżki formatu "hev1" lub "hvc1") w manifeście Smooth Streaming i odwołania normatywne są aktualizowane w celu uzyskania zgodności z najnowszymi standardami MPEG, które obejmują HEVC, Common Encryption of HEVC, a nazwy pól w formacie pliku nośnika ISO Base zostały zaktualizowane w taki sposób, aby były zgodne z najnowszych specyfikacji. 

Specyfikacja protokołu Smooth Streaming, do którego istnieje odwołanie [MS-SSTR] opisuje format sieci używany do dostarczania multimediów cyfrowych i na żądanie, takich jak audio i wideo, w następujący sposób: od kodera do serwera sieci Web, z serwera do innego serwera oraz z serwera do klienta HTTP.
Korzystanie z opartego na protokole MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)dostarczania struktury danych za pośrednictwem protokołu HTTP umożliwia bezproblemowe przełączanie się niemal w czasie rzeczywistym między różnymi poziomami jakości skompresowanej zawartości multimedialnej. Wynikiem tego jest stałe środowisko odtwarzania dla użytkownika końcowego klienta HTTP, nawet jeśli dla komputera klienckiego lub urządzenia zmienią się warunki renderowania sieci i wideo.

## <a name="11-glossary"></a>słownik 1,1 

Poniższe warunki są zdefiniowane w temacie *[MS-glos]*:

>   **unikatowy identyfikator globalny (GUID) unikatowy identyfikator uniwersalny (UUID)**

Następujące terminy są specyficzne dla tego dokumentu:

>  **czas kompozycji:** Czas, w którym jest wyświetlany przykład na kliencie, zgodnie z definicją w   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **Cenc**: Common Encryption, zgodnie z definicją w [ISO/IEC 23001-7] Second Edition.
> 
>   **czas dekodowania:** Czas wymagany do zdekodowania przykładu na kliencie, zgodnie z definicją w   [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Niezależnie do pobrania jednostka **nośnika** , która składa się z jednego lub większej liczby **przykładów**.

>   **HEVC:** Kodowanie wideo o wysokiej wydajności, zgodnie z definicją w [ISO/IEC 23008-2]
> 
>   **manifest:** Metadane dotyczące **prezentacji** , która umożliwia klientowi wykonywanie żądań dotyczących **nośnika**. **nośnik:** Skompresowane dane audio, wideo i tekstowe używane przez klienta do odtwarzania **prezentacji**. **format nośnika:** Dobrze zdefiniowany format służący do reprezentowania dźwięku lub wideo w postaci skompresowanej **próbki**.
> 
>   **Prezentacja:** Zestaw wszystkich **strumieni** i powiązanych metadanych wymaganych do odtworzenia pojedynczego filmu. **żądanie:** Komunikat HTTP wysłany z klienta do serwera, zgodnie z definicją w odpowiedzi [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **:** komunikat http wysłany z serwera do klienta, zgodnie z definicją w [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **przykład:** Najmniejsza jednostka podstawowa (na przykład ramka), w której   **multimedia** są przechowywane i przetwarzane.
> 
>   **może być** , musi, nie może być: Te warunki (we wszystkich wersalikach) są używane zgodnie z opisem w   [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) . wszystkie instrukcje dotyczące użycia funkcji OPCJONALNEj mogą być, powinny lub nie powinny być.

## <a name="12-references"></a>1,2 odwołań

>   Odwołania do dokumentacji Open Specification firmy Microsoft nie obejmują roku publikacji, ponieważ linki do najnowszej wersji dokumentów, które są często aktualizowane. Odwołania do innych dokumentów obejmują rok publikacji, gdy jest on dostępny.

### <a name="121-normative-references"></a>1.2.1 odwołania do normatywnych 

>  [MS-SSTR] *V20140502* protokołu Smooth Streaming [https://msdn.microsoft.com/library/ff469518.aspx](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)
> 
>   [ISO/IEC 14496-12] Międzynarodowa Organizacja Normalizacyjna (ISO) "technologia informacyjna — kodowanie obiektów dźwięk-Visual — część 12: Format pliku multimediów ISO Base", ISO/IEC 14496-12:2014, wydanie 4 i sprostowanie 1, zmiany 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Międzynarodowa Organizacja Normalizacyjna (ISO), "technologia informacyjna — kodowanie obiektów dźwięk-wizualizacji — część 15: przewóz wideo z strukturalną jednostką NAL w formacie pliku nośnika podstawowego ISO", ISO 14496-15:2015, wydanie 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Technologia informacyjna — wysoka wydajność kodowania i dostarczania multimediów w środowiskach heterogenicznych — część 2: kodowanie wideo o wysokiej wydajności: 2013 lub Najnowsza wersja   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Technologia informacyjna — technologie systemu MPEG — część 7: typowe szyfrowanie w plikach ISO Base File Format Files, CENC Edition 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381 "" kodery-dekoder "i" Profile "dla typów nośników" datazasobnik " <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Urząd rejestrowania MP4, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "kluczowe słowa do użycia w specyfikacjach RFC do wskazania poziomów wymagań", BCP 14, RFC 2119, marzec 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informacje informacyjne 

>   [MS-GLOS] Microsoft Corporation, "*Protokoły systemu Windows — Słownik główny*".
> 
>   [RFC3548] Josefsson, S., Ed., "kodowania danych Base16, Base32 i base64", RFC 3548, 2003 lipca, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., i Overell, P., "rozszerzone BNF dla specyfikacji składni: ABNF", STD 68, RFC 5234, styczeń 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1,3 — Omówienie 

>   Poniżej określono tylko zmiany specyfikacji Smooth Streaming wymaganej dla dostarczania HEVC. Niezmienione nagłówki sekcji są wyświetlane na liście, aby zachować lokalizację w specyfikacji Smooth Streaming, do której istnieje odwołanie [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1,4 relacje z innymi protokołami 

## <a name="15-prerequisitespreconditions"></a>wymagania wstępne dotyczące 1,5/warunków wstępnych 

## <a name="16-applicability-statement"></a>1,6 — Instrukcja zastosowania 

## <a name="17-versioning-and-capability-negotiation"></a>1,7 obsługa wersji i negocjowanie możliwości 

## <a name="18-vendor-extensible-fields"></a>1,8 — rozszerzone pola dostawcy 

>   Następująca metoda służy do identyfikowania strumieni przy użyciu formatu wideo HEVC:
> 
>   * **Niestandardowe kody opisowe formatów multimediów:** Ta funkcja jest dostarczana przez pole **FourCC** , jak określono w sekcji *2.2.2.5*.
>   Implementacje mogą zapewnić, że rozszerzenia nie będą powodować konfliktów przez rejestrowanie kodów rozszerzeń w MPEG4-RA, jak określono w [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1,9 — przypisania standardów 

## <a name="2-messages"></a>2 komunikaty 

## <a name="21-transport"></a>Transport 2,1

## <a name="22-message-syntax"></a>Składnia komunikatu 2,2 

### <a name="221-manifest-request"></a>2.2.1 żądanie manifestu 

### <a name="222-manifest-response"></a>2.2.2 odpowiedzi manifestu 

#### <a name="2221-smoothstreamingmedia"></a>SmoothStreamingMedia 2.2.2.1 

>   **MinorVersion (zmienna):** Wersja pomocnicza komunikatu odpowiedzi manifestu. MUSI być ustawiona na 2. (Bez zmian)
> 
>   **Skala czasu (zmienna):** Skala czasu atrybutu Duration określona jako liczba przyrostów w jednej sekundzie. Wartością domyślną jest .
> 1. (Bez zmian)
> 
>    Zalecaną wartością jest 90000 do reprezentowania dokładnego czasu trwania klatek wideo i fragmentów, które zawierają wideo w postaci ułamkowej klatek (na przykład 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 protectionelement 

Aby można było zastosować Common Encryption (CENC) do strumieni wideo lub audio, istnieje wartość Protectionelement. HEVC zaszyfrowane strumienie muszą być zgodne z Common Encryption wydanie 2 [ISO/IEC 23001-7]. Szyfrowane są tylko dane wycinków w jednostkach NAL VCL.

#### <a name="2223-streamelement"></a>2.2.2.3 strumienia 

>   **StreamTimeScale (zmienna):** Skala czasu dla wartości czasu trwania i godziny w tym strumieniu określona jako liczba przyrostów w jednej sekundzie. Dla strumieni HEVC zaleca się wartość 90000. Dla strumieni audio zaleca się wartość odpowiadającą częstotliwości próbkowania Wave (na przykład 48000 lub 44100).

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 trackelement 

>   **FourCC (zmienna):** Kod składający się z czterech znaków, który identyfikuje format nośnika używany dla każdego przykładu. Następujący zakres wartości jest zarezerwowany dla następujących średnich semantyki:
> 
> * "hev1": przykłady wideo dla tej ścieżki używają wideo HEVC przy użyciu formatu opisu przykładowego "hev1" określonego w [ISO/IEC-14496-15].
>
> * "hvc1": przykłady wideo dla tej ścieżki używają wideo HEVC przy użyciu formatu opisu przykładowego "hvc1" określonego w [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (zmienna):** Dane, które określają parametry specyficzne dla formatu multimediów i wspólne dla wszystkich próbek w ścieżce, reprezentowane jako ciąg bajtów kodowanych szesnastkowie. Format i semantyka znaczenia sekwencji bajtów różni się od wartości pola **FourCC** w następujący sposób:
> 
>   * Gdy element Trackelement opisuje HEVC wideo, pole **FourCC** jest równe **"hev1"** lub **"hvc1"**
> 
>   Pole **CodecPrivateData** zawiera reprezentację ciągu kodowanego szesnastkowo z następującej sekwencji bajtów określoną w ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (bez zmiany z MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField zawiera zestaw parametrów sekwencji (SPS).
> 
>   * PPSField zawiera zestaw parametrów wycinka (PPS).
> 
>   Uwaga: zestaw parametrów wideo (VPS) nie znajduje się w CodecPrivateData, ale powinien być zawarty w nagłówku pliku przechowywanych plików w polu "hvcC". Systemy używające protokołu Smooth Streaming muszą sygnalizować dodatkowe parametry dekodowania (na przykład warstwa HEVC) przy użyciu atrybutu niestandardowego "Codecs".

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Pole **MajorVersion SMOOTHSTREAMINGMEDIA** musi mieć wartość 2, a pole **MinorVersion** musi mieć wartość 2. (Bez zmian)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>Żądanie fragmentacji 

>   **Uwaga**: domyślny format nośnika żądany dla **MinorVersion** 2 i "hev1" lub "hvc1" jest formatem "ISO8" nośnika podstawowego (ISO/IEC 14496-12] ISO Base File Format) w czwartej wersji i [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 — odpowiedź fragmentu 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** jest przestarzała i jego funkcja jest zastępowana przez pole Czas dekodowania fragmentu śledzenia ("tfdt") określone w sekcji [ISO/IEC 14496-12] 8.8.12.
> 
>   **Uwaga**: Klient może obliczyć czas trwania fragmentu, sumując przykładowe czasy trwania wymienione w polu przebieg śledzenia ("Trun") lub mnożąc liczbę próbek pomnożonych przez domyślny czas trwania próbki. BaseMediaDecodeTime w "tfdt" i czas trwania fragmentu są równe parametrowi czasu URL dla następnego fragmentu.
> 
>   Pole czas referencyjny producenta ("prft") powinno zostać wstawione przed polem fragmentu filmu ("moof"), aby wskazać czas UTC odpowiadający czasowi dekodowania fragmentu pierwszej próbki, do którego odwołuje się pole fragmentu filmu, jak określono w sekcji [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** jest przestarzała i jego funkcja jest zastępowana przez pole Czas dekodowania fragmentu śledzenia ("tfdt") określone w sekcji [ISO/IEC 14496-12] 8.8.12.
> 
>   **Uwaga**: Klient może obliczyć czas trwania fragmentu, sumując przykładowe czasy trwania wymienione w polu przebieg śledzenia ("Trun") lub mnożąc liczbę próbek pomnożonych przez domyślny czas trwania próbki. BaseMediaDecodeTime w "tfdt" i czas trwania fragmentu są równe parametrowi czasu URL dla następnego fragmentu. Adresy wyszukiwania są przestarzałe, ponieważ opóźnią przesyłanie strumieniowe na żywo.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** i powiązane pola hermetyzują wartości domyślne dla każdej próbki metadanych w fragmencie. Składnia pola **TfhdBox** jest ścisłym podzbiorem składni pola nagłówka fragmentu śledzenia zdefiniowanego w sekcji [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 bajtów):** Przesunięcie, w bajtach, od początku pola **MdatBox** do przykładowego pola w polu **MdatBox** . Aby było możliwe sygnalizowanie tego ograniczenia, należy ustawić flagę default-Base-is-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** i powiązane pola hermetyzują na przykładowe metadane dla żądanego fragmentu. Składnia **TrunBox** jest ścisłym podzbiorem pola uruchomienia fragmentu śledzenia w wersji 1 zdefiniowanej w sekcji [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bajty):** Wartość próbki przesunięcia czasu dla każdej próbki skorygowana, tak aby czas prezentacji pierwszego prezentowanej próbki w fragmencie był równy czasowi zdekodowania pierwszego zdekodowanego przykładu. Należy użyć negatywnych przesunięć próbek wideo,
> 
>   zgodnie z definicją w   [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Uwaga: pozwala to uniknąć błędu synchronizacji wideo spowodowany przez wideo opóźnione audio równe największym opóźnionemu wykorzystaniu buforu obrazu, a zachowanie chronometrażu prezentacji między alternatywnymi fragmentami, które mogą mieć różne opóźnienia usuwania.
> 
>   Składnia pól zdefiniowanych w tej sekcji określona w ABNF   [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) pozostaje taka sama, z wyjątkiem następujących:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>Pola wspólne odpowiedzi 2.2.4.9 fragmentów 

### <a name="225-sparse-stream-pointer"></a>Wskaźnik strumienia rozrzedzonego 2.2.5 

### <a name="226-fragment-not-yet-available"></a>Fragment 2.2.6 nie jest jeszcze dostępny 

### <a name="227-live-ingest"></a>2.2.7 na żywo 

#### <a name="2271-filetype"></a>2.2.7.1 — typ 

>   **Typ_pliku (zmienna):** określa podtyp i zamierzone użycie pliku MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) i atrybutów wysokiego poziomu.
> 
>   **MajorBrand (zmienna):** Główna marka pliku multimedialnego. MUSI być ustawiona na wartość "ISML".
> 
>   **MinorVersion (zmienna):** Wersja pomocnicza pliku multimedialnego. MUSI być ustawiona na 1.
> 
>   **CompatibleBrands (zmienna):** Określa obsługiwane marki MPEG-4.
>   MUSI zawierać "ccff" i "ISO8".
> 
>   Składnia pól zdefiniowanych w tej sekcji określona w ABNF   [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) jest następująca:

```properties
FileType = MajorBrand MinorVersion CompatibleBrands
MajorBrand = STRING_UINT32
MinorVersion = STRING_UINT32
CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)
```

**Uwaga**: znaki zgodności "ccff" i "ISO8" wskazują, że fragmenty są zgodne z "typowym formatem pliku kontenera" i Common Encryption [ISO/IEC 23001-7] i ISO Base File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>Fragment 2.2.7.5 

##### <a name="22751-track-fragment-extended-header"></a>Rozszerzony nagłówek fragmentu śledzenia 2.2.7.5.1 

### <a name="228-server-to-server-ingest"></a>2.2.8 serwer-serwer 

## <a name="3-protocol-details"></a>3 szczegóły protokołu 


## <a name="31-client-details"></a>3,1 szczegóły klienta 

### <a name="311-abstract-data-model"></a>3.1.1 — abstrakcyjny model danych 

#### <a name="3111-presentation-description"></a>3.1.1.1 — Opis prezentacji 

>   Element danych opis prezentacji hermetyzuje wszystkie metadane prezentacji.
> 
>   Metadane prezentacji: zestaw metadanych, który jest wspólny dla wszystkich strumieni w prezentacji. Metadane prezentacji obejmują następujące pola, określone w sekcji *2.2.2.1*:
> 
> * **MajorVersion**
> * **MinorVersion**
> * **Dział**
> * **Czas trwania**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Prezentacje zawierające strumienie HEVC mają ustawioną wartość:

```properties
MajorVersion = 2
MinorVersion = 2
```

>   LookaheadCount = 0 (Uwaga: pola przestarzałe)
> 
>   Prezentacje należy również ustawić:

```properties
TimeScale = 90000
```

>   Kolekcja strumieni: Kolekcja elementów danych opisu strumienia, jak określono w sekcji *3.1.1.1.2*.
> 
>   Opis ochrony: Kolekcja elementów danych opisu metadanych systemu ochrony, jak określono w sekcji *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Opis metadanych systemu ochrony 3.1.1.1.1 

>   Element danych opisu metadanych systemu ochrony hermetyzuje metadane specyficzne dla pojedynczego systemu Content Protection. (Bez zmian)
> 
>   Opis nagłówka ochrony: metadane ochrony zawartości odnoszące się do pojedynczego systemu Content Protection. Opis nagłówka ochrony składa się z następujących pól, określonych w sekcji *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>Opis strumienia 3.1.1.1.2 

###### <a name="311121-track-description"></a>Opis śledzenia 3.1.1.1.2.1 

###### <a name="3111211-custom-attribute-description"></a>Opis atrybutu niestandardowego 3.1.1.1.2.1.1 

##### <a name="3113-fragment-reference-description"></a>Opis odwołania do fragmentu 3.1.1.3 

###### <a name="31131-track-specific-fragment-reference-description"></a>Opis odwołania do 3.1.1.3.1 Track-Specific 

#### <a name="3112-fragment-description"></a>Opis fragmentu 3.1.1.2 

##### <a name="31121-sample-description"></a>Opis przykładu 3.1.1.2.1 

### <a name="312-timers"></a>w przypadku czasomierzy 3.1.2 

### <a name="313-initialization"></a>Inicjacja 3.1.3 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer zdarzenia wyzwalane 

#### <a name="3141-open-presentation"></a>3.1.4.1 Otwórz prezentację 

#### <a name="3142-get-fragment"></a>3.1.4.2 Pobierz fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Zamknij prezentację 

### <a name="315-processing-events-and-sequencing-rules"></a>Reguła i reguły sekwencjonowania 3.1.5 

#### <a name="3151-manifest-request-and-manifest-response"></a>Żądanie manifestu 3.1.5.1 i odpowiedź manifestu 

#### <a name="3152-fragment-request-and-fragment-response"></a>Żądanie fragmentu 3.1.5.2 i odpowiedź fragmentu

## <a name="32-server-details"></a>Szczegóły serwera 3,2

## <a name="33-live-encoder-details"></a>Informacje o dynamicznym koderze 3,3 

## <a name="4-protocol-examples"></a>Przykłady dotyczące protokołu 4 

## <a name="5-security"></a>5 zabezpieczeń 

## <a name="51-security-considerations-for-implementers"></a>5,1 zagadnienia dotyczące zabezpieczeń dla realizatorów

>   Jeśli zawartość transportowana przy użyciu tego protokołu ma wysoką wartość komercyjną, należy użyć systemu Content Protection, aby uniemożliwić nieautoryzowane użycie zawartości. Obiekt **protectionelement** może służyć do przesyłania metadanych związanych z użyciem systemu Content Protection. Chroniona zawartość audio i wideo jest szyfrowana zgodnie z opisem w formacie MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Uwaga**: w przypadku wideo HEVC tylko dane wycinków w VCL NALs są szyfrowane. Nagłówki wycinków i inne NALs są dostępne dla aplikacji prezentacji przed odszyfrowaniem. w bezpiecznej ścieżce wideo zaszyfrowane informacje nie są dostępne dla aplikacji prezentacji.

## <a name="52-index-of-security-parameters"></a>5,2 indeks parametrów zabezpieczeń 


| **Parametr zabezpieczeń**  | **Sekcja**         |
|-------------------------|---------------------|
| Protectionelement       | *2.2.2.2*           |
| Pola Common Encryption | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 pól Common Encryption

Następujące pola mogą być obecne w odpowiedziach na fragmenty, gdy Common Encryption są stosowane i są określone w [ISO/IEC 23001-7] lub [ISO/IEC 14496-12]:

1.  Pole nagłówka specyficznego dla systemu ochrony ("PSSH")

2.  Przykładowe szyfrowanie ("senc")

3.  Pole przesunięcia informacji dodatkowych ("saio")

4.  Pole przykładowego rozmiaru informacji pomocniczych ("Saiz")

5.  Pole opisu grupy przykładowej ("sgpd")

6.  Przykład do grupy pola ("sbgp")

---

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
