---
title: Jak działa buforowanie | Microsoft Docs
description: Buforowanie to proces przechowywania danych lokalnie, dzięki czemu przyszłe żądania dotyczące tych danych są dostępne szybciej.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: 7a4688c196551f3ab6b5713d8939f53af161d1e3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505012"
---
# <a name="how-caching-works"></a>Jak działa buforowanie

Ten artykuł zawiera omówienie ogólnych pojęć dotyczących buforowania i sposobu, w jaki [usługa Azure Content Delivery Network (CDN)](cdn-overview.md) używa buforowania w celu poprawy wydajności. Jeśli chcesz dowiedzieć się, jak dostosować zachowanie pamięci podręcznej w punkcie końcowym usługi CDN, zobacz [Azure CDN sterowania zachowaniem buforowania przy użyciu reguł buforowania](cdn-caching-rules.md) i [kontrolować zachowanie buforowania Azure CDN z użyciem ciągów zapytań](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Wprowadzenie do buforowania

Buforowanie to proces przechowywania danych lokalnie, dzięki czemu przyszłe żądania dotyczące tych danych są dostępne szybciej. W najbardziej typowym typie buforowania, buforowanie przeglądarki sieci Web, przeglądarka sieci Web przechowuje kopie danych statycznych lokalnie na lokalnym dysku twardym. Korzystając z buforowania, przeglądarka sieci Web może uniknąć wykonywania wielu operacji rundy na serwerze i zamiast tego uzyskiwać dostęp do tych samych danych lokalnie, co pozwala zaoszczędzić czas i zasoby. Buforowanie jest dobrze dostosowane do lokalnego zarządzania małymi, statycznymi danymi, takimi jak obrazy statyczne, pliki CSS i pliki JavaScript.

Podobnie buforowanie jest używane przez sieć dostarczania zawartości na serwerach brzegowych blisko użytkownika w celu uniknięcia przesyłania żądań z powrotem do źródła danych i skrócenia opóźnień użytkowników końcowych. W przeciwieństwie do pamięci podręcznej przeglądarki sieci Web, która jest używana tylko dla jednego użytkownika, Sieć CDN ma udostępnioną pamięć podręczną. W udostępnionej pamięci podręcznej usługi CDN plik, który jest żądany przez jednego użytkownika, może być dostępny później przez innych użytkowników, co znacznie zmniejsza liczbę żądań do serwera pochodzenia.

Zasoby dynamiczne, które zmieniają się często lub są unikatowe dla poszczególnych użytkowników, nie mogą być buforowane. Te typy zasobów mogą jednak korzystać z optymalizacji usługi Dynamic site Acceleration (DSA) w usłudze Azure Content Delivery Network na potrzeby ulepszeń wydajności.

Buforowanie może wystąpić na wielu poziomach między serwerem źródłowym a użytkownikiem końcowym:

- Serwer sieci Web: używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Content Delivery Network: używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Usługodawca internetowy: używa udostępnionej pamięci podręcznej (dla wielu użytkowników).
- Przeglądarka sieci Web: używa prywatnej pamięci podręcznej (dla jednego użytkownika).

Każda pamięć podręczna zazwyczaj zarządza własną Aktualności zasobów i sprawdza, czy plik jest przestarzały. To zachowanie jest zdefiniowane w specyfikacji buforowania HTTP [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Świeżość zasobów

Ponieważ buforowany zasób może być nieaktualny lub nieodświeżony (w porównaniu do odpowiadającego mu zasobu na serwerze źródłowym), ważne jest, aby mechanizm buforowania mógł kontrolować, kiedy zawartość jest odświeżana. Aby zaoszczędzić czas i użycie przepustowości, zasób pamięci podręcznej nie jest porównywany z wersją na serwerze źródłowym przy każdym uzyskaniu dostępu. Zamiast tego, pod warunkiem, że zasób pamięci podręcznej jest uznawany za świeży, zakłada się, że jest to Najnowsza wersja i jest wysyłana bezpośrednio do klienta. Zasób w pamięci podręcznej jest uznawany za świeży, gdy jego wiek jest mniejszy od wieku lub okresu zdefiniowanego przez ustawienie pamięci podręcznej. Na przykład gdy przeglądarka ponownie ładuje stronę sieci Web, sprawdza, czy każdy buforowany zasób na dysku twardym jest świeży i ładuje go. Jeśli zasób nie jest świeży (nieodświeżony), jest ładowana aktualna kopia z serwera.

### <a name="validation"></a>Walidacja

Jeśli zasób jest uznawany za przestarzały, zostanie wyświetlony monit o jego zweryfikowanie, czyli określenie, czy dane w pamięci podręcznej nadal pasują do tego, co znajduje się na serwerze źródłowym. Jeśli plik został zmodyfikowany na serwerze źródłowym, pamięć podręczna aktualizuje swoją wersję zasobu. W przeciwnym razie, jeśli zasób jest świeży, dane są dostarczane bezpośrednio z pamięci podręcznej bez weryfikowania ich w pierwszej kolejności.

### <a name="cdn-caching"></a>Buforowanie sieci CDN

Buforowanie jest integralną metodą działania sieci CDN w celu przyspieszenia dostarczania i zmniejszenia obciążenia pochodzenia dla statycznych zasobów, takich jak obrazy, czcionki i wideo. W obszarze buforowanie sieci CDN zasoby statyczne są selektywnie przechowywane na serwerach z strategicznym systemem, które są bardziej lokalne dla użytkownika i oferują następujące korzyści:

- Ponieważ większość ruchu internetowego jest statyczna (na przykład obrazy, czcionki i wideo), buforowanie usługi CDN zmniejsza opóźnienie sieci przez przeniesienie zawartości bliżej użytkownika, co zmniejsza odległość przesyłania danych.

- Dzięki przeciążeniu pracy do sieci CDN buforowanie może ograniczyć ruch sieciowy i obciążenie serwera źródłowego. Takie rozwiązanie zmniejsza wymagania dotyczące kosztów i zasobów aplikacji, nawet jeśli istnieje duża liczba użytkowników.

Podobnie jak w przypadku zaimplementowania buforowania w przeglądarce internetowej, można kontrolować sposób buforowania w sieci CDN przez wysyłanie nagłówków dyrektywy Cache-dyrektyw. Nagłówki dyrektywy Cache są nagłówkami HTTP, które są zazwyczaj dodawane przez serwer pochodzenia. Chociaż większość z tych nagłówków została pierwotnie zaprojektowana pod kątem buforowania adresów w przeglądarkach klientów, są one również używane przez wszystkie pośredniczące pamięci podręczne, takie jak sieci CDN. 

Dwa nagłówki mogą służyć do definiowania Aktualności pamięci podręcznej: `Cache-Control` i `Expires` . `Cache-Control` jest bardziej aktualny i ma pierwszeństwo przed `Expires` (jeśli istnieją). Istnieją również dwa typy nagłówków używane do walidacji (nazywane walidatorami): `ETag` i `Last-Modified` . `ETag` jest bardziej aktualny i ma pierwszeństwo przed `Last-Modified` , jeśli oba są zdefiniowane.  

## <a name="cache-directive-headers"></a>Pamięć podręczna — nagłówki dyrektywy

> [!IMPORTANT]
> Domyślnie punkt końcowy Azure CDN, który jest zoptymalizowany pod kątem DSA ignoruje nagłówki podręcznej dyrektywy i pomija buforowanie. W przypadku **Azure CDN Standard from Verizon** i **Azure CDN Standard z profilów Akamai** można dostosować, jak punkt końcowy Azure CDN traktuje te nagłówki przy użyciu [reguł buforowania usługi CDN](cdn-caching-rules.md) w celu włączenia buforowania. W przypadku **Azure CDN Premium tylko w** profilach Verizon należy użyć [aparatu reguł](./cdn-verizon-premium-rules-engine.md) , aby włączyć buforowanie.

Azure CDN obsługuje następujące nagłówki dyrektywy pamięci podręcznej HTTP, które definiują czas trwania pamięci podręcznej i udostępnianie pamięci podręcznej.

**Kontrola pamięci podręcznej:**
- Wprowadzono w protokole HTTP 1,1, aby umożliwić wydawcom sieci Web większą kontrolę nad zawartością i rozwiązywanie ograniczeń `Expires` nagłówka.
- Zastępuje `Expires` nagłówek, jeśli został on zdefiniowany i `Cache-Control` .
- W przypadku użycia w żądaniu HTTP z klienta do punktu POP usługi CDN domyślnie `Cache-Control` jest ignorowana przez wszystkie profile Azure CDN.
- W przypadku użycia w odpowiedzi HTTP z klienta do punktu POP usługi CDN:
     - **Azure CDN Standard/Premium z Verizon** i **Azure CDN standard firmy Microsoft** wspiera wszystkie `Cache-Control` dyrektywy.
     - **Azure CDN Standard/Premium od Verizon** i **Azure CDN standard firmy Microsoft** honoruje zachowania buforowania dla dyrektyw Cache-Control w [dokumencie RFC 7234-Hypertext Transfer Protocol (http/1.1): buforowanie (IETF.org)](https://tools.ietf.org/html/rfc7234#section-5.2.2.8).
     - **Azure CDN Standard from Akamai** obsługuje tylko następujące `Cache-Control` dyrektywy; wszystkie pozostałe są ignorowane:
         - `max-age`: Pamięć podręczna może przechowywać zawartość przez określoną liczbę sekund. Na przykład `Cache-Control: max-age=5`. Ta dyrektywa określa maksymalny czas, przez jaki zawartość jest uważana za świeżą.
         - `no-cache`: Buforuj zawartość, ale sprawdzaj zawartość za każdym razem przed dostarczeniem jej z pamięci podręcznej. Odpowiednik `Cache-Control: max-age=0` .
         - `no-store`: Nigdy nie Buforuj zawartości. Usuń zawartość, jeśli została wcześniej zapisana.

**Wygasł**
- Starsza wersja nagłówka wprowadzona w protokole HTTP 1,0; obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami.
- Używa czasu wygaśnięcia opartego na dacie z drugą dokładnością. 
- Podobnie jak w przypadku programu `Cache-Control: max-age` .
- Używany, gdy `Cache-Control` nie istnieje.

**Pragm**
   - Domyślnie nie są honorowane przez Azure CDN.
   - Starsza wersja nagłówka wprowadzona w protokole HTTP 1,0; obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami.
   - Używany jako nagłówek żądania klienta w następującej dyrektywie: `no-cache` . Ta dyrektywa nakazuje serwerowi dostarczenie nowej wersji zasobu.
   - `Pragma: no-cache` jest równoważne `Cache-Control: no-cache`.

## <a name="validators"></a>Modułów sprawdzania

Gdy pamięć podręczna jest nieodświeżona, moduły sprawdzania pamięci podręcznej HTTP są używane do porównywania buforowanej wersji pliku z wersją na serwerze źródłowym. **Azure CDN Standard/Premium z Verizon** obsługuje zarówno `ETag` moduł, jak i `Last-Modified` moduły walidacji, natomiast **Azure CDN standard z firmy Microsoft** i **Azure CDN Standard z Akamai** obsługuje tylko `Last-Modified` domyślnie.

**Element ETag**
- **Azure CDN Standard/Premium z Verizon** obsługuje domyślnie `ETag` , natomiast **Azure CDN Standard od firmy Microsoft** i **Azure CDN Standard from Akamai** .
- `ETag` definiuje ciąg, który jest unikatowy dla każdego pliku i wersji pliku. Na przykład `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Wprowadzone w protokole HTTP 1,1 i jest bardziej aktualne niż `Last-Modified` . Przydatne, gdy data ostatniej modyfikacji jest trudna do określenia.
- Obsługuje zarówno silną weryfikację, jak i słabą weryfikację. jednak Azure CDN obsługuje tylko silną weryfikację. Aby zapewnić silną weryfikację, dwie reprezentacje zasobów muszą być identyczne bajtowo. 
- Pamięć podręczna weryfikuje plik, który używa `ETag` przez wysłanie `If-None-Match` nagłówka z co najmniej jednym `ETag` modułem walidacji w żądaniu. Na przykład `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Jeśli wersja serwera jest zgodna z `ETag` walidacją na liście, wysyła kod stanu 304 (nie zmodyfikowano) w odpowiedzi. Jeśli wersja różni się, serwer odpowiada z kodem stanu 200 (OK) i zaktualizowanym zasobem.

**Ostatnio modyfikowane:**
- W przypadku **Azure CDN tylko standard/Premium z Verizon** , `Last-Modified` jest używany, jeśli `ETag` nie jest częścią odpowiedzi HTTP. 
- Określa datę i godzinę, o której serwer pierwotny ustalił, że zasób został ostatnio zmodyfikowany. Na przykład `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Pamięć podręczna weryfikuje plik przy użyciu `Last-Modified` przez wysłanie `If-Modified-Since` nagłówka z datą i godziną żądania. Serwer pierwotny porównuje tę datę z `Last-Modified` nagłówkiem najnowszego zasobu. Jeśli zasób nie został zmodyfikowany od określonego czasu, serwer zwraca kod stanu 304 (nie zmodyfikowano) w odpowiedzi. Jeśli zasób został zmodyfikowany, serwer zwraca kod stanu 200 (OK) i zaktualizowany zasób.

## <a name="determining-which-files-can-be-cached"></a>Określanie, które pliki mogą być buforowane

Nie wszystkie zasoby mogą być buforowane. W poniższej tabeli przedstawiono zasoby, które mogą być buforowane, na podstawie typu odpowiedzi HTTP. Nie można buforować zasobów dostarczanych z odpowiedziami HTTP, które nie spełniają wszystkich tych warunków. W przypadku **Azure CDN Premium tylko Verizon** , można użyć aparatu reguł, aby dostosować niektóre z tych warunków.

|                       | Azure CDN od firmy Microsoft          | Azure CDN z Verizon | Azure CDN z Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **Kody stanu HTTP** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **Metody HTTP**      | GET, SZEF                         | GET                    | GET                          |
| **Limity rozmiaru plików**  | 300 GB                            | 300 GB                 | — Optymalizacja ogólnej dostarczania sieci Web: 1,8 GB<br />— Optymalizacje przesyłania strumieniowego multimediów: 1,8 GB<br />— Optymalizacja dużych plików: 150 GB |

Aby można było korzystać **z usługi Azure CDN Standard z buforowania firmy Microsoft** w celu pracy nad zasobem, serwer pierwotny musi obsługiwać wszystkie nagłówki i odbierać żądania HTTP, a wartości długości zawartości muszą być takie same dla każdego elementu podrzędnego i odbierać odpowiedzi HTTP dla zasobu. W przypadku żądania HEAD serwer źródłowy musi obsługiwać żądanie HEAD i musi odpowiedzieć z tymi samymi nagłówkami, co w przypadku otrzymania żądania GET.

## <a name="default-caching-behavior"></a>Domyślne zachowanie pamięci podręcznej

W poniższej tabeli opisano domyślne zachowanie buforowania dla produktów Azure CDN i ich optymalizacje.

|    | Microsoft: ogólne dostarczanie w sieci Web | Verizon: ogólne dostarczanie w sieci Web | Verizon: DSA | Akamai: ogólne dostarczanie w sieci Web | Akamai: DSA | Akamai: pobieranie dużych plików | Akamai: ogólne lub VOD przesyłanie strumieniowe multimediów |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Pochodzenie honorowe**       | Tak    | Tak   | Nie   | Tak    | Nie   | Tak   | Tak    |
| **Czas trwania pamięci podręcznej usługi CDN** | 2 dni |7 dni | Brak | 7 dni | Brak | 1 dzień | 1 rok |

**Pochodzenie**: określa, czy w odpowiedzi HTTP z serwera pochodzenia mają być uznawane obsługiwane nagłówki dyrektywy Cache-dyrektyw.

**Czas trwania pamięci podręcznej usługi CDN**: określa ilość czasu, przez który zasób jest buforowany w Azure CDN. Jeśli jednak **pochodzenie Honor** ma wartość tak, a odpowiedź HTTP z serwera pochodzenia zawiera nagłówek dyrektywy Cache-dyrektywa `Expires` lub `Cache-Control: max-age` Azure CDN używa wartości czasu trwania określonej przez nagłówek zamiast tego. 

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak dostosować i zastąpić domyślne zachowanie pamięci podręcznej w usłudze CDN za pomocą zasad buforowania, zobacz [kontrola Azure CDN buforowania przy użyciu reguł buforowania](cdn-caching-rules.md). 
- Aby dowiedzieć się, jak za pomocą ciągów zapytań kontrolować zachowanie pamięci podręcznej, zobacz [sterowania zachowaniem buforowania Azure CDN przy użyciu ciągów zapytań](cdn-query-string.md).
