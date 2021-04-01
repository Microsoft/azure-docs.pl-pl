---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 59ff0ba854fa609e6d29f3473f662a89ab5f3dbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559168"
---
> [!NOTE]
> W przypadku zasobów, które nie zostały naprawione, należy otworzyć bilet pomocy technicznej w celu poproszenia o zwiększenie limitów przydziału. Nie należy tworzyć dodatkowych kont Azure Media Services próbujących uzyskać wyższe limity.

### <a name="account-limits"></a>Limity kont

| Zasób | Limit domyślny |
| --- | --- |
| Media Services kont w ramach jednej subskrypcji | 100 (stałe) |

### <a name="asset-limits"></a>Limity zasobów

| Zasób | Limit domyślny |
| --- | --- |
| Zasoby na konto Media Services | 1 000 000|

### <a name="storage-media-limits"></a>Limity magazynu (multimediów)

| Zasób | Limit domyślny |
| --- | --- |
| Rozmiar pliku| W niektórych scenariuszach obowiązuje limit maksymalnego rozmiaru pliku, który jest obsługiwany przez przetwarzanie w Media Services. <sup>jedno</sup> |
| Konta magazynu | 100<sup>(2)</sup> (stałe) |

<sup>1</sup> maksymalny rozmiar obsługiwany przez pojedynczy obiekt BLOB jest obecnie do 5 TB na platformie Azure Blob Storage. Dodatkowe limity są stosowane w Media Services na podstawie rozmiarów maszyn wirtualnych używanych przez usługę. Limit rozmiaru dotyczy przekazywanych plików, a także plików, które są generowane w wyniku przetwarzania Media Services (kodowanie lub analizowanie). Jeśli rozmiar pliku źródłowego przekracza 260 GB, prawdopodobnie zadanie zakończy się niepowodzeniem.

W poniższej tabeli przedstawiono limity jednostek zarezerwowanych multimediów S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli zakodujesz źródła rozpoznawania 4 KB o długim czasie trwania, musisz użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć potrzebną wydajność. Jeśli masz zawartość 4K o rozmiarze większym niż limit 260 GB w jednostkach zarezerwowanych multimediów S3, Otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej multimediów|Maksymalny rozmiar danych wejściowych (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> konta magazynu muszą znajdować się w tej samej subskrypcji platformy Azure.

### <a name="jobs-encoding--analyzing-limits"></a>Limity zadań (analizowanie & kodowania)

| Zasób | Limit domyślny |
| --- | --- |
| Zadania na konto Media Services | 500 000 <sup>(3)</sup> (stałe)|
| Dane wejściowe zadania na zadanie | 50 (stałe)|
| Dane wyjściowe zadania na zadanie | 20 (stałe) |
| Przekształcenia na konto Media Services | 100 (stałe)|
| Przekształcanie danych wyjściowych w transformacji | 20 (stałe) |
| Pliki na dane wejściowe zadania|10 (stałe)|

<sup>3</sup> ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje ono usuniętych zadań. 

Każdy rekord zadania na koncie starszej niż 90 dni zostanie automatycznie usunięty, nawet jeśli łączna liczba rekordów jest mniejsza niż maksymalny limit przydziału. 

### <a name="live-streaming-limits"></a>Limity przesyłania strumieniowego na żywo

| Zasób | Limit domyślny |
| --- | --- |
| Zdarzenia na żywo <sup>(4)</sup> na konto Media Services |5|
| Dane wyjściowe na żywo na żywo |3 <sup>(5)</sup> |
| Maksymalny czas trwania danych wyjściowych na żywo | [Rozmiar okna DVR](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> Aby uzyskać szczegółowe informacje na temat ograniczeń dotyczących zdarzeń na żywo, zobacz [porównanie i ograniczenia typów zdarzeń na żywo](../articles/media-services/latest/live-event-types-comparison.md).

<sup>5</sup> danych wyjściowych na żywo rozpocznie się po utworzeniu i Zatrzymaj po usunięciu.

### <a name="packaging--delivery-limits"></a>& limity dostarczania pakietów

| Zasób | Limit domyślny |
| --- | --- |
| Punkty końcowe przesyłania strumieniowego (zatrzymane lub uruchomione) dla konta Media Services| 2 |
| Filtry manifestów dynamicznych|100|
| Zasady przesyłania strumieniowego | 100 <sup>(6)</sup> |
| Unikatowe lokalizatory przesyłania strumieniowego skojarzone z elementem zawartości w jednym momencie | 100<sup>(7)</sup> (stałe) |

<sup>6</sup> Jeśli używasz niestandardowych [zasad przesyłania strumieniowego](/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie użyć ich do StreamingLocators za każdym razem, gdy potrzebne są te same opcje szyfrowania i protokoły. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>7</sup> lokalizatorów przesyłania strumieniowego nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

### <a name="protection-limits"></a>Limity ochrony

| Zasób | Limit domyślny |
| --- | --- |
| Opcje dla zasad klucza zawartości | 30 |
| Licencje na miesiąc dla każdego z typów DRM w usłudze Media Services Key Delivery na konto|1 000 000|

### <a name="support-ticket"></a>Bilet pomocy technicznej

W przypadku zasobów, które nie zostały naprawione, możesz poprosił o wypróbowanie przydziałów, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz szczegółowe informacje w żądaniu dotyczące żądanych zmian przydziału, scenariuszy przypadków użycia i wymaganych regionów. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.