---
title: Odtwarzanie nagrań — platforma Azure
description: Korzystając z usługi Analiza filmów wideo na żywo, możesz IoT Edge na potrzeby ciągłego nagrywania wideo, dzięki czemu możesz nagrać wideo w chmurze przez tygodnie lub miesiące. Możesz również ograniczyć nagranie do interesujących klipów za pośrednictwem rejestrowania opartego na zdarzeniach. W tym artykule omówiono sposób odtwarzania nagrań.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 6222d2c05b2fe05945d4bcbef6dbb0d64bd4726a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84261079"
---
# <a name="playback-of-recordings"></a>Odtwarzanie nagrań 

## <a name="pre-read"></a>Przed odczytem  

* [Odtwarzanie wideo](video-playback-concept.md)
* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)

## <a name="background"></a>Tło  

Korzystając z usługi Analiza filmów wideo na żywo w IoT Edge na potrzeby [ciągłego nagrywania wideo](continuous-video-recording-concept.md) (CVR), możesz nagrać wideo w chmurze przez tygodnie lub miesiące. Możesz również ograniczyć nagranie do interesujących klipów za pośrednictwem [rejestrowania wideo opartego na zdarzeniach](event-based-video-recording-concept.md) (EVR). 

Twoje konto usługi multimediów jest połączone z kontem magazynu platformy Azure, a po zarejestrowaniu wideo w chmurze zawartość jest zapisywana w obszarze [zasobów usługi Media](terminology.md#asset). Media Services umożliwia [przesyłanie strumieniowe zawartości](terminology.md#streaming)po zakończeniu nagrywania lub trwającym nagrywaniu. Media Services udostępnia funkcje niezbędne do dostarczania strumieni za pośrednictwem protokołów HLS lub MPEG do odtwarzania urządzeń (klientów). Aby uzyskać więcej informacji, zobacz artykuł [odtwarzanie wideo](video-playback-concept.md) . Za pomocą interfejsów API usługi multimediów można generować wymagane adresy URL przesyłania strumieniowego — używane przez klientów do odtwarzania wideo & audio. Aby utworzyć adres URL przesyłania strumieniowego dla elementu zawartości, zobacz [Tworzenie lokalizatora przesyłania strumieniowego i adresów URL kompilacji](../latest/create-streaming-locator-build-url.md). Po utworzeniu adresu URL przesyłania strumieniowego dla elementu zawartości można go zapisać ze źródłem wideo (czyli kamerą) w systemie zarządzania zawartością.

Na przykład podczas przesyłania strumieniowego za pośrednictwem HLS adres URL przesyłania strumieniowego będzie wyglądać następująco `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . W przypadku formatu MPEG-KRESKa będzie wyglądać podobnie `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` .

Zwraca plik manifestu, który między innymi zawiera opis całkowitego czasu dostarczania strumienia oraz to, czy reprezentuje on wstępnie zarejestrowany plik, czy też źródło danych "Live".

### <a name="live-vs-vod"></a>Live a VoD  

Protokoły przesyłania strumieniowego, takie jak HLS i MPEG-KRESKa, zostały utworzone w celu obsługi scenariuszy, takich jak przesyłanie strumieniowe wideo na żywo, a także przesyłanie strumieniowe zawartości na żądanie/ze wstępnie zarejestrowanym obrazem, np. programów telewizyjnych i filmów. W przypadku wideo na żywo komputery z HLS i MPEG-PAUZ są zaprojektowane tak, aby rozpoczynać odtwarzanie od "ostatniego" czasu. W przypadku filmów, osoby przeglądające oczekują, że mogą zacząć od początku i przeskoczyć do tego miejsca, jeśli zdecydują się na to. Manifesty HLS i MPEG mają flagi wskazujące klientów, czy film wideo reprezentuje strumień na żywo, czy też jest wstępnie zarejestrowanym zawartością.
Pojęcie to dotyczy również strumieni HLS i MPEG z zasobów, które zawierają wideo nagrane przy użyciu usługi Live Video Analytics na IoT Edge.

## <a name="browsing-and-selective-playback-of-recordings"></a>Przeglądanie i selektywne odtwarzanie nagrań  

Rozważmy scenariusz, w którym korzystasz z usługi Analiza wideo na żywo w IoT Edge, aby nagrać wideo tylko z 8:00 do od 10:00 w dniach w przypadku otwarcia szkoły przez cały rok akademicki. Lub prawdopodobnie nagrywasz wideo tylko z 7AM do 7PM na świętach narodowych. W każdym z tych dwóch scenariuszy podczas próby przeglądania i wyświetlania nagrania wideo potrzebne są:

* Sposób określenia dat/godzin nagrania wideo, które ma nagranie.
* Sposób wybierania części (na przykład 9:00 do 11AM w ciągu nowych lat) tego nagrania do odtwarzania.

Media Services udostępnia interfejs API zapytania (availableMedia) do rozwiązywania pierwszego problemu i filtry zakresu czasu (startTime, endTime), aby zająć się drugim.

## <a name="query-api"></a>Interfejs API zapytań 

W przypadku korzystania z programu CVR urządzenia odtwarzania nie mogą zażądać manifestu obejmującego odtwarzanie całego nagrania.  Nagrywanie wieloletnie spowoduje utworzenie pliku manifestu, który był zbyt duży na potrzeby odtwarzania i przeanalizowanie go do przydatnych części po stronie klienta.  Klient musi wiedzieć, które zakresy DateTime mają dane w rejestrowaniu, dzięki czemu mogą wprowadzać prawidłowe żądania bez dużo miejsca pracy. Jest to miejsce, w którym znajduje się nowy interfejs API zapytań — klienci mogą teraz korzystać z tego interfejsu API po stronie serwera w celu odnajdywania zawartości.

Wartość precyzji może być jedną z: Year, month, Day lub Full (jak pokazano poniżej). 

|Precyzja|rok|miesiąc|dzień|szczegółowe|
|---|---|---|---|---|
|Zapytanie|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Reakcja|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|Pełna wierność odpowiedzi. Jeśli wszystkie przerwy w sobie nie były dostępne, rozpoczęcie powinno wynosić startTime, a koniec endTime.|
|Ogranicza|&#x2022;startTime <= endTime<br/>&#x2022;obu powinny mieć format rrrr, w przeciwnym razie zwracany jest błąd.<br/>Wartości &#x2022;mogą zawierać dowolną liczbę lat.<br/>Wartości &#x2022;są włącznie.|&#x2022;startTime <= endTime<br/>&#x2022;obu powinny mieć format RRRR-MM, w przeciwnym razie zwracany jest błąd.<br/>Wartości &#x2022;mogą się składać z maksymalnie 12 miesięcy od siebie.<br/>Wartości &#x2022;są włącznie.|&#x2022;startTime <= endTime<br/>&#x2022;obu powinny mieć format RRRR-MM-DD, w przeciwnym razie zwracany jest błąd.<br/>Wartości &#x2022;mogą się składać z maksymalnie 31 dni.<br/>Wartości są włącznie.|&#x2022;startTime < endTime<br/>Wartości &#x2022;mogą się składać z maksymalnie 25 godzin.<br/>Wartości &#x2022;są włącznie.|

#### <a name="additional-request-format-considerations"></a>Dodatkowe zagadnienia dotyczące formatu żądań

* Wszystkie czasy są w formacie UTC
* Wymagany jest parametr ciągu zapytania dokładności.  
* Parametry ciągu zapytania startTime i endTime są wymagane przez wartości miesiąc, dzień i pełną precyzję.  
* Parametry ciągu zapytania startTime i endTime są opcjonalne w przypadku wartości dokładności roku (brak, jedna lub obie są obsługiwane).  

    * Jeśli czas rozpoczęcia zostanie pominięty, zakłada się, że jest to pierwszy rok w nagraniu.
    * W przypadku pominięcia endTime zakłada się, że jest to ostatni rok w nagraniu.
    * Na przykład, jeśli nagrywanie zostało rozpoczęte w 2011 i nadal trwa do 2020, wówczas:

        * /availableMedia? Precision = Year jest taka sama jak/availableMedia? Precision = Year&startTime = 2011&endTime = 2020
        * /availableMedia? Precision = Year&startTime = 2015 jest taka sama jak/availableMedia? Precision = Year&startTime = 2015&endTime = 2020
        * /availableMedia? Precision = Year&endTime = 2018 r jest taka sama jak/availableMedia? Precision = Year&startTime = 2011&endTime = 2018 r

Jeśli żadne dane multimedialne nie są dostępne dla zakresów czasu, zwracana jest pusta lista.

Jeśli zasób nie zawiera nagrania z grafu multimediów, odpowiedź HTTP 400 zostanie zwrócona z komunikatem o błędzie z informacją, że ta funkcja jest dostępna tylko dla zawartości zapisanej za pośrednictwem grafu multimediów.

Jeśli czas określony przez parametry jest większy niż dozwolony przez maksymalny zakres czasu dla danego typu zapytania, wówczas HTTP 400 zostanie zwrócone z komunikatem o błędzie z wyjaśnieniem maksymalnego dozwolonego zakresu czasu dla żądanego typu zapytania.

Przy założeniu, że zakres czasu jest prawidłowy dla określonych parametrów, nie zostaną zwrócone żadne błędy dla zapytań, które znajdują się poza oknem czasu danych w nagraniu.  Znaczenie, jeśli nagrywanie zostało rozpoczęte 7 godzin temu, ale klient poprosi o dostęp do nośnika z {UtcNow – 24 godziny} do UtcNow, a następnie zwrócimy po {UtcNow – 7} godz.

### <a name="response-format"></a>Format odpowiedzi  

Wywołanie availableMedia zwraca zestaw wartości czasu.

Odpowiedź będzie treścią JSON, a wartości timeRanges są tablicą ISO 8601 dat UTC dla roku (w formacie RRRR), miesiąc (w formacie RRRR-MM) lub dzień (RRRR-MM-DD) w zależności od żądanej precyzji.  Pełny timeRanges będzie zawierać wartość początkową i końcową sformatowaną jako czas UTC ISO 8601 (w formacie RRRR-MM-DDTgg: mm: SS. sssZ).

W przypadku zapytania availableMedia interfejs API przeprowadzi klucz z osi czasu wideo. Wszelkie przerwy na osi czasu będą widoczne jako luki w odpowiedzi.

#### <a name="response-example-for-availablemedia"></a>Przykład odpowiedzi dla availableMedia

##### <a name="example-1-live-recording-with-no-gaps"></a>Przykład 1: nagrywanie na żywo bez przerw

Poniżej znajduje się odpowiedź przedstawiająca wszystkie dostępne nośniki 21 grudnia 2019, w przypadku których nagrywanie zakończyło się 100%. Odpowiedź ma tylko jedną parę początkową/końcową.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>Przykład 2: nagrywanie na żywo z 2-sekundową przerwą

Załóżmy z jakiegoś powodu, że aparat nie mógł wysłać prawidłowego wideo dla 2-sekundowego interwału na dzień. Poniżej znajduje się odpowiedź przedstawiająca wszystkie dostępne nośniki 21 grudnia 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Przykład 3: nagrywanie na żywo z przerwą 8 godzin

Załóżmy, że aparat i/lub lokalne urządzenie utraciło moc przez 8 godzin w ciągu dnia, od 4 czasu UTC do 12:00 czasu UTC i nie było kopii zapasowej źródła. Oto odpowiedź pokazująca wszystkie dostępne multimedia na ten dzień

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>Przykład 4: nagrywanie na żywo, gdzie żadne wideo nie jest rejestrowane w świętach świątecznych

Załóżmy, że nagrywasz wideo tylko wtedy, gdy Szkoła była w trakcie sesji, a Rejestracja została zatrzymana od 17 czerwca do 6 września. Zapytanie o dostępne miesiące będzie wyglądać następująco:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

Jeśli w czerwcu zostanie wyświetlony monit o dostęp do dostępnych dni, zobaczysz:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>Przykład 5: nagrywanie na żywo, gdzie żadne wideo nie jest rejestrowane w weekendy lub święta

Załóżmy, że nagrywasz wideo tylko w godzinach pracy. Zapytanie dla dostępnych dni będzie wyglądać następująco

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>Filtry zakresu czasu

Jak wspomniano powyżej, te filtry umożliwiają wybranie fragmentów nagrania (na przykład od 9:00 do 11AM w ciągu nowych lat) na potrzeby odtwarzania. W przypadku przesyłania strumieniowego za pośrednictwem HLS adres URL przesyłania strumieniowego będzie wyglądać następująco `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` . Aby można było wybrać część nagrania, należy dodać parametr startTime i endTime, taki jak: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8` . W rezultacie filtry zakresu czasu są modyfikatorami adresów URL służącymi do opisania części osi czasu rejestrowania, która jest uwzględniona w manifeście przesyłania strumieniowego:

* `starttime` jest sygnaturą typu DateTime ISO 8601 opisującą żądany czas rozpoczęcia osi czasu wideo w zwracanym manifeście.
* `endtime` jest sygnaturą typu DateTime ISO 8601 opisującą żądany czas zakończenia dla osi czasu wideo zwróconej w manifeście.

Maksymalna długość tego manifestu nie może przekroczyć 24 godzin.

Poniżej przedstawiono ograniczenia dotyczące filtrów.

|startTime| endTime |Wynik|
|---|---|---|
|Poza |Poza |Zwraca najnowszą część filmu wideo w elemencie zawartości, do maksymalnie 4 godzin.<br/><br/>Jeśli element zawartości nie został jeszcze zapisany (dane wideo nie są odbierane) ostatnio, zwracany jest manifest na żądanie (VoD). W przeciwnym razie zwracany jest manifest na żywo.|
|Aktualny|Poza|    Zwróć manifest z dowolnymi klipami wideo, które są nowsze niż godzina rozpoczęcia, jeśli taki manifest będzie krótszy niż 24 godziny.<br/>Jeśli długość manifestu przekroczy 24 godziny, zwracany jest błąd.<br/>Jeśli element zawartości nie został jeszcze zapisany (dane wideo nie są odbierane) ostatnio, zwracany jest manifest na żądanie (VoD). W przeciwnym razie zwracany jest manifest na żywo.
|Poza|Aktualny |Błąd — Jeśli parametr startTime jest obecny, endTime jest obowiązkowy.|
|Aktualny|Aktualny|Zwróć manifest VoD z dowolnym wideo dostępnym między parametrami startTime i endTime.<br/>Zakres (startTime, endTime) nie może być dłuższy niż 24 godziny.|

### <a name="response-examples"></a>Przykłady odpowiedzi  

#### <a name="example-1-live-recording-with-no-gaps"></a>Przykład 1: nagrywanie na żywo bez przerw

Poniżej znajduje się odpowiedź przedstawiająca wszystkie dostępne nośniki 21 grudnia 2019, w przypadku których nagrywanie zakończyło się 100%. Odpowiedź ma tylko jedną parę początkową/końcową.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Gdy nagrywanie jest ciągłe, można zażądać HLS lub manifestów PAUZy dla dowolnej części czasu w obrębie tej pary początku/końca — tak długo, jak zakres wynosi 24 godziny. Przykładem dla powyżej 4-godzinnego żądania manifestu HLS jest:

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>Przykład 2: nagrywanie na żywo z 2-sekundową przerwą

Załóżmy z jakiegoś powodu, że aparat nie mógł wysłać prawidłowego wideo dla 2-sekundowego interwału na dzień. Poniżej przedstawiono odpowiedź przedstawiającą dostępne nośniki 21 grudnia 2019:

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Po nagraniu takim jak powyżej, można zażądać manifestów HLS i ŁĄCZNIKów z dowolnymi parami startTime/endTime, tak długo, jak zakres był krótszy niż 24 godziny. Jeśli te wartości są rozmieszczone w czasie 04:01:08AM UTC, zwracany manifest mógłby sygnalizować nieciągłość na osi czasu nośnika, zgodnie z odpowiednimi wartościami dla tych protokołów.

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>Przykład 3: nagrywanie na żywo z użyciem 8-godzinnej przerwy

Załóżmy, że aparat i/lub lokalne urządzenie utraciło moc przez 8 godzin w ciągu dnia, od 4 czasu UTC do 12:00 czasu UTC i nie było kopii zapasowej źródła. Poniżej znajduje się odpowiedź pokazująca wszystkie dostępne multimedia na ten dzień.

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

Dzięki takiemu rejestrowaniu:

* Jeśli żądasz manifestu, w którym filtry zakresu startTime/endTime znajdowały się wewnątrz części "dostępne" osi czasu, a mianowicie od północy do 4AM lub od 12:00 do północy, usługa zwróci manifest, który obejmuje czas od rozpoczęcia do endTime, na przykład:

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* Jeśli zażądasz manifestu, gdzie startTime i endTime znajdowały się wewnątrz "otworu" w środku — powiedzmy od 8:00 do od 10:00 UTC, a następnie usługa będzie działać tak samo, jakby filtr zasobów miał wynik pusty.

    [Jest to żądanie, które pobiera pustą odpowiedź] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* Jeśli zażądasz manifestu, w którym tylko jeden z wartości startTime lub endTime znajduje się w "dziurie", zwrócony manifest będzie zawierać tylko część tego przedziału. Wartość startTime lub endTime można przyciągnąć do najbliższej prawidłowej granicy. Na przykład jeśli zostanie wyświetlony monit o przesłanie strumienia 3-HR z od 10:00 do 1PM, odpowiedź będzie zawierać 1-HR wartość multimedialną dla 12 12:00 do 1PM

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    Zwrócony manifest zaczyna się od 2019-12-21T12:00:00.000 Z, mimo że żądanie zażądało rozpoczęcia od 10:00. Podczas kompilowania systemu zarządzania wideo przy użyciu wtyczki odtwarzacza należy zachować ostrożność w celu sygnalizowania tej przeglądarki. Nieznane podglądy byłyby mylone z tym, dlaczego oś czasu odtwarzania była od południa, a nie od 10:00 zgodnie z żądaniem

## <a name="recording-and-playback-latencies"></a>Czasy rejestrowania i odtwarzania

W przypadku korzystania z usługi Analiza filmów wideo na żywo w IoT Edge, aby zarejestrować się w elemencie zawartości, należy określić właściwość segmentLength, która informuje moduł, aby zagregł minimalny czas trwania wideo (w sekundach) przed jego zarejestrowaniem w chmurze. Na przykład jeśli segmentLength jest ustawiony na 300, moduł będzie zbierać zawartość wideo 5 minut przed przekazaniem 1 5 minut "fragment", a następnie przejdź do trybu akumulacji w ciągu następnych 5 minut i przekaż ponownie. Zwiększenie segmentLengthu może obniżyć koszty transakcji usługi Azure Storage, ponieważ liczba operacji odczytu i zapisu nie będzie częściej niż raz na segmentLength sekund.

W związku z tym przesyłanie strumieniowe wideo z Media Services zostanie opóźnione o co najmniej tyle czasu. 

Innym czynnikiem, który określa opóźnienie odtwarzania (opóźnienie między czasem wystąpienia zdarzenia przed kamerą, do momentu, w którym można je wyświetlić na urządzeniu odtwarzającego), jest [grupę GOP](https://en.wikipedia.org/wiki/Group_of_pictures) czasu trwania grupy obrazów. [Zmniejszenie opóźnień strumieni na żywo przy użyciu 3 prostych technik](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) wyjaśnia, dłuższy czas trwania grupę GOP, dłuższy czas oczekiwania. Często istnieje możliwość, że aparaty IP są używane w scenariuszach nadzoru i zabezpieczeń skonfigurowanych do korzystania z GOPs dłużej niż 30 sekund. Ma to duży wpływ na ogólne opóźnienia.

## <a name="next-steps"></a>Następne kroki

[Samouczek dotyczący ciągłego nagrywania wideo](continuous-video-recording-tutorial.md)
