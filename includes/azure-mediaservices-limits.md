---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: c315c071aeb36eea0bd1af84b1344cf2fef5f703
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329667"
---
>[!NOTE]
>W przypadku zasobów, które nie zostały naprawione, należy otworzyć bilet pomocy technicznej w celu poproszenia o zwiększenie limitów przydziału. Nie należy tworzyć dodatkowych kont Azure Media Services próbujących uzyskać wyższe limity.

| Zasób | Limit | 
| --- | --- | 
| Azure Media Services kont w ramach jednej subskrypcji | 25 (stały) |
| Jednostki zarezerwowane multimediów na konto Media Services |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Zadania na konto Media Services | 50 000<sup>2</sup> |
| Łańcuchowe zadania podrzędne na zadanie | 30 (stały) |
| Zasoby na konto Media Services | 1 000 000|
| Elementy zawartości na zadanie podrzędne | 50 |
| Elementy zawartości na zadanie | 100 |
| Unikatowe lokalizatory jednocześnie skojarzone z elementem zawartości | 5<sup>4</sup> |
| Kanały na żywo na konto Media Services |5|
| Programy w stanie zatrzymania na kanał |50|
| Programy w stanie działania na kanał |3|
| Punkty końcowe przesyłania strumieniowego, które są zatrzymane lub uruchomione dla konta Media Services|2|
| Jednostki przesyłania strumieniowego na punkt końcowy przesyłania strumieniowego |10 |
| Konta magazynu | 100<sup>5</sup> (stałe) |
| Zasady | 1 000 000<sup>6</sup> |
| Rozmiar pliku| W niektórych scenariuszach obowiązuje limit maksymalnego rozmiaru pliku, który jest obsługiwany przez przetwarzanie w Media Services. <sup>7</sup> |

<sup>1</sup> Jeśli zmienisz typ, na przykład z S2 na S1, zostaną zresetowane maksymalne zastrzeżone limity jednostek.

<sup>2</sup> Ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje ono usuniętych zadań. Stare zadania można usunąć za pomocą **IJob. Delete** lub żądania **delete** http.

Od 1 kwietnia 2017 każdy rekord zadania na koncie starszej niż 90 dni jest automatycznie usuwany wraz ze skojarzonymi z nim rekordami zadań. Automatyczne usuwanie występuje nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Aby zarchiwizować informacje o zadaniach i zadaniach, użyj kodu opisanego w temacie [Zarządzanie zasobami przy użyciu zestawu SDK programu Media Services .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> Po zażądaniu listy jednostek zadań dla każdego żądania jest zwracana maksymalnie 1 000 zadań. Aby śledzić wszystkie przesłane zadania, należy użyć zapytania Top lub Skip, zgodnie z opisem w [opcji zapytania systemu OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Lokalizatory nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Aby nadać różnym użytkownikom prawa dostępu, Skorzystaj z rozwiązań do zarządzania prawami cyfrowymi (DRM). Aby uzyskać więcej informacji, zobacz [Ochrona zawartości za pomocą Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> Konta magazynu muszą znajdować się w tej samej subskrypcji platformy Azure.

<sup>6</sup> Istnieje limit 1 000 000 zasad dla różnych zasad Media Services. Przykład dotyczy zasad lokalizatora lub ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Jeśli zawsze używasz tych samych dni i uprawnień dostępu, użyj tego samego identyfikatora zasad. Aby uzyskać informacje i zapoznać się z przykładem, zobacz [Zarządzanie zasobami za pomocą zestawu SDK Media Services .NET](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Maksymalny rozmiar obsługiwany przez pojedynczy obiekt BLOB jest obecnie do 5 TB na platformie Azure Blob Storage. Dodatkowe limity są stosowane w Media Services na podstawie rozmiarów maszyn wirtualnych używanych przez usługę. Limit rozmiaru dotyczy przekazywanych plików, a także plików, które są generowane w wyniku przetwarzania Media Services (kodowanie lub analizowanie). Jeśli rozmiar pliku źródłowego przekracza 260 GB, prawdopodobnie zadanie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity jednostek zarezerwowanych multimediów S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli zakodujesz źródła rozpoznawania 4 KB o długim czasie trwania, musisz użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć potrzebną wydajność. Jeśli masz zawartość 4K o rozmiarze większym niż limit 260 GB w jednostkach zarezerwowanych multimediów S3, Otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej multimediów    |Maksymalny rozmiar danych wejściowych (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
