---
title: Definiowanie filtrów w Azure Media Services
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł używać ich do przesyłania strumieniowego określonych sekcji strumienia. Media Services tworzy dynamiczne manifesty do osiągnięcia tego selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 6ba9a4d5ac427533c2ce005a74fe43b0ce80de79
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109445"
---
# <a name="filters"></a>Filtry

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Podczas dostarczania zawartości do klientów (zdarzeń przesyłania strumieniowego na żywo lub wideo na żądanie) klient może potrzebować większej elastyczności niż to, co opisano w pliku manifestu domyślnego elementu zawartości. Azure Media Services oferuje [dynamiczne manifesty](filters-dynamic-manifest-concept.md) na podstawie wstępnie zdefiniowanych filtrów. 

Filtry są reguł po stronie serwera, które umożliwiają klientom wykonywanie następujących czynności: 

- Odtwarzaj tylko sekcję filmu wideo (zamiast odtwarzać cały film wideo). Na przykład:
  - Zmniejsz manifest, aby pokazać podrzędny klip zdarzenia na żywo ("filtrowanie podklipu") lub
  - Przytnij początek filmu wideo ("przycinanie filmu wideo").
- Dostarczaj tylko określone wersje i/lub określone ścieżki języka, które są obsługiwane przez urządzenie używane do odtwarzania zawartości ("filtrowanie wersji"). 
- Dostosuj okno prezentacji (DVR), aby zapewnić ograniczoną długość okna DVR w odtwarzaczu ("Dostosowywanie okna prezentacji").

Media Services umożliwia tworzenie **filtrów kont** i **filtrów zasobów** dla zawartości. Ponadto można skojarzyć wstępnie utworzone filtry z **lokalizatorem przesyłania strumieniowego**.

## <a name="defining-filters"></a>Definiowanie filtrów

Istnieją dwa typy filtrów: 

* [Filtry kont](/rest/api/media/accountfilters) (globalne) — mogą być stosowane do każdego elementu zawartości w ramach konta Azure Media Services, które mają okres istnienia konta.
* [Filtry zasobów](/rest/api/media/assetfilters) (lokalne) — mogą być stosowane tylko do zasobu, z którym filtr został skojarzony podczas tworzenia, mają okres istnienia elementu zawartości. 

**Filtry konta** i typy **filtrów zasobów** mają dokładnie te same właściwości, które definiują/opisują filtr. Oprócz tworzenia **filtru zasobów** należy określić nazwę elementu zawartości, z którym ma zostać skojarzony filtr.

W zależności od danego scenariusza decyduje o tym, jaki typ filtru jest bardziej odpowiedni (filtr zasobów lub filtr konta). Filtry kont są odpowiednie dla profilów urządzeń (filtrowanie wersji), gdzie filtry zasobów mogą być używane do przycinania określonego elementu zawartości.

Do opisywania filtrów służą następujące właściwości. 

|Nazwa|Opis|
|---|---|
|firstQuality|Pierwsza szybkość transmisji bitów filtru.|
|presentationTimeRange|Zakres czasu prezentacji. Ta właściwość służy do filtrowania punktów początkowych i końcowych manifestu, długości okna prezentacji i pozycji początkowej na żywo. <br/>Aby uzyskać więcej informacji, zobacz [PresentationTimeRange](#presentationtimerange).|
|służąc|Śledzi warunki wyboru. Aby uzyskać więcej informacji, zobacz [ścieżki](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Użyj tej właściwości z **filtrami zasobów**. Nie zaleca się ustawiania właściwości przy użyciu **filtrów konta**.

|Nazwa|Opis|
|---|---|
|**endTimestamp**|Dotyczy wideo na żądanie (VoD).<br/>W przypadku prezentacji przesyłania strumieniowego na żywo jest ona dyskretnie ignorowana i stosowana, gdy prezentacja się zakończy, a strumień zostaje VoD.<br/>Jest to długa wartość reprezentująca bezwzględny punkt końcowy prezentacji, zaokrąglona do najbliższego następnego uruchomienia grupę GOP. Jednostką jest skala czasu, więc endTimestamp 1800000000 będzie przez 3 minuty.<br/>Użyj startTimestamp i endTimestamp, aby przyciąć fragmenty, które będą znajdować się na liście odtwarzania (manifest).<br/>Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 przy użyciu domyślnej skali czasu spowoduje wygenerowanie listy odtwarzania zawierającej fragmenty od 4 sekund do 10 sekund prezentacji VoD. Jeśli fragment znajduje się na granicy, cały fragment zostanie uwzględniony w manifeście.|
|**forceEndTimestamp**|Dotyczy tylko przesyłania strumieniowego na żywo.<br/>Wskazuje, czy właściwość endTimestamp musi być obecna. Jeśli wartość jest równa true, endTimestamp musi być określony lub zwracany jest nieprawidłowy kod żądania.<br/>Dozwolone wartości: false, true.|
|**liveBackoffDuration**|Dotyczy tylko przesyłania strumieniowego na żywo.<br/> Ta wartość definiuje najnowszą pozycję na żywo, do której może się odbiegać klient.<br/>Za pomocą tej właściwości można opóźnić położenie odtwarzania na żywo i utworzyć bufor po stronie serwera dla graczy.<br/>Jednostką tej właściwości jest skala czasu (patrz poniżej).<br/>Maksymalny czas trwania wycofywania na żywo wynosi 300 sekund (3000000000).<br/>Na przykład wartość 2000000000 oznacza, że najnowsza dostępna zawartość wynosi 20 sekund opóźnionych od rzeczywistej aktywnej krawędzi.|
|**presentationWindowDuration**|Dotyczy tylko przesyłania strumieniowego na żywo.<br/>Użyj presentationWindowDuration, aby zastosować przedziały ruchome fragmentów do dołączenia do listy odtwarzania.<br/>Jednostką tej właściwości jest skala czasu (patrz poniżej).<br/>Na przykład ustaw presentationWindowDuration = 1200000000, aby zastosować dwuminutowe okno przesuwania. Multimedia w ciągu 2 minut od aktywnej krawędzi zostaną uwzględnione na liście odtwarzania. Jeśli fragment znajduje się na granicy, cały fragment zostanie uwzględniony na liście odtwarzania. Minimalny czas trwania okna prezentacji wynosi 60 sekund.|
|**startTimestamp**|Dotyczy wideo na żądanie (VoD) lub przesyłania strumieniowego na żywo.<br/>Jest to długa wartość, która reprezentuje bezwzględny punkt początkowy strumienia. Wartość jest zaokrąglana do najbliższego następnego uruchomienia grupę GOP. Jednostką jest skala czasu, więc startTimestamp 150000000 będzie wynosić 15 sekund.<br/>Użyj startTimestamp i endTimestampp, aby przyciąć fragmenty, które będą znajdować się na liście odtwarzania (manifest).<br/>Na przykład startTimestamp = 40000000 i endTimestamp = 100000000 przy użyciu domyślnej skali czasu spowoduje wygenerowanie listy odtwarzania zawierającej fragmenty od 4 sekund do 10 sekund prezentacji VoD. Jeśli fragment znajduje się na granicy, cały fragment zostanie uwzględniony w manifeście.|
|**Dział**|Dotyczy wszystkich sygnatur czasowych i czasów trwania w przedziale czasu prezentacji, określony jako liczba przyrostów w jednej sekundzie.<br/>Wartość domyślna to 10000000-10 000 000 przyrosty w jednej sekundie, gdzie każdy przyrost może być przy100 padać w nanosekundach.<br/>Na przykład jeśli chcesz ustawić startTimestamp o 30 sekund, użyj wartości 300000000 przy użyciu domyślnej skali czasu.|

### <a name="tracks"></a>Służąc

Należy określić listę warunków właściwości śledzenia filtru (FilterTrackPropertyConditions), na podstawie których ścieżki strumienia (przesyłanie strumieniowe na żywo lub wideo na żądanie) powinny być włączone do dynamicznie utworzonego manifestu. Filtry są łączone przy użyciu operacji logicznych **i** i **.**

Filtrowanie warunków właściwości śledzenia opisują typy śledzenia, wartości (opisane w poniższej tabeli) i operacje (równe, NotEqual). 

|Nazwa|Opis|
|---|---|
|**Multimedia**|Użyj szybkości transmisji bitów ścieżki do filtrowania.<br/><br/>Zalecana wartość jest zakresem szybkości transmisji bitów w bitach na sekundę. Na przykład "0-2427000".<br/><br/>Uwaga: w przypadku użycia określonej wartości szybkości transmisji, takiej jak 250000 (bity na sekundę), takie podejście nie jest zalecane, ponieważ dokładne szybkości transmisji bitów mogą ulegać zmianom z jednego zasobu na inny.|
|**FourCC**|Użyj wartości FourCC ścieżki do filtrowania.<br/><br/>Wartość jest pierwszym elementem w formacie koderów-dekoder, zgodnie z opisem w [dokumencie RFC 6381](https://tools.ietf.org/html/rfc6381). Obecnie obsługiwane są następujące kodeki: <br/>Dla filmu wideo: "avc1", "hev1", "hvc1"<br/>Dla dźwięku: "mp4a", "EC-3"<br/><br/>Aby określić wartości FourCC dla ścieżek w elemencie zawartości, Pobierz i sprawdź plik manifestu.|
|**Język**|Użyj języka ścieżki do filtrowania.<br/><br/>Wartość jest tagiem języka, który ma zostać uwzględniony, zgodnie z opisem w dokumencie RFC 5646. Na przykład "en".|
|**Nazwa**|Użyj nazwy ścieżki do filtrowania.|
|**Typ**|Użyj typu ścieżki do filtrowania.<br/><br/>Dozwolone są następujące wartości: "wideo", "audio" lub "text".|

### <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano filtr przesyłania strumieniowego na żywo: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego

Możesz określić listę [filtrów zasobów lub kont](filters-concept.md) w [lokalizatorze przesyłania strumieniowego](/rest/api/media/streaminglocators/create#request-body). [Pakowarka dynamiczna](encode-dynamic-packaging-concept.md) stosuje tę listę filtrów razem z tymi, które są określone przez klienta w adresie URL. Ta kombinacja generuje [manifest dynamiczny](filters-dynamic-manifest-concept.md), który jest oparty na filtrach w adresach URL i filtrach określonych w lokalizatorze przesyłania strumieniowego. 

Zobacz poniższe przykłady:

* [Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego — .NET](filters-dynamic-manifest-dotnet-how-to.md#associate-filters-with-streaming-locator)
* [Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego — interfejs wiersza polecenia](filters-dynamic-manifest-cli-how-to.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Aktualizowanie filtrów
 
**Lokalizatory przesyłania strumieniowego** nie są aktualizowane, gdy można aktualizować filtry. 

Nie zaleca się aktualizowania definicji filtrów skojarzonych z aktywnie opublikowanym **lokalizatorem przesyłania strumieniowego**, szczególnie w przypadku włączenia sieci CDN. Serwery przesyłania strumieniowego i sieci CDN mogą mieć wewnętrznej pamięci podręcznej, co może spowodować zwrócenie starych danych z pamięci. 

Jeśli należy zmienić definicję filtru, rozważ utworzenie nowego filtru i dodanie go do adresu URL **lokalizatora przesyłania strumieniowego** lub opublikowanie nowego **lokalizatora przesyłania strumieniowego** , który odwołuje się bezpośrednio do filtru.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak programowo utworzyć filtry.  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-how-to.md)
