---
title: Przydziały i limity w Azure Media Services
description: W tym temacie opisano limity przydziału i limity w Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: d6ca7a444f2a3d4babe220548edb10bd37784be7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92678119"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Przydziały i limity Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule wymieniono niektóre z najczęstszych limitów Microsoft Azure Media Services, które są również czasami nazywane przydziałami.

> [!NOTE]
> W przypadku zasobów, które nie zostały naprawione, należy otworzyć bilet pomocy technicznej w celu poproszenia o zwiększenie limitów przydziału. Nie należy tworzyć dodatkowych kont Azure Media Services próbujących uzyskać wyższe limity.

## <a name="account-limits"></a>Limity kont

| Zasób | Limit domyślny |
| --- | --- |
| [Media Services kont](media-services-account-concept.md) w ramach jednej subskrypcji | 100 (stałe) |

## <a name="asset-limits"></a>Limity zasobów

| Zasób | Limit domyślny |
| --- | --- |
| [Zasoby](assets-concept.md) na konto Media Services | 1 000 000|

## <a name="storage-limits"></a>Limity magazynu

| Zasób | Limit domyślny | 
| --- | --- | 
| Rozmiar pliku| W niektórych scenariuszach obowiązuje limit maksymalnego rozmiaru pliku, który jest obsługiwany przez przetwarzanie w Media Services. <sup>jedno</sup> |
| [Konta magazynu](storage-account-concept.md) | 100<sup>(2)</sup> (stałe) |

<sup>1</sup> maksymalny rozmiar obsługiwany przez pojedynczy obiekt BLOB jest obecnie do 5 TB na platformie Azure Blob Storage. Dodatkowe limity są stosowane w Media Services na podstawie rozmiarów maszyn wirtualnych używanych przez usługę. Limit rozmiaru dotyczy przekazywanych plików, a także plików, które są generowane w wyniku przetwarzania Media Services (kodowanie lub analizowanie). Jeśli rozmiar pliku źródłowego przekracza 260 GB, prawdopodobnie zadanie zakończy się niepowodzeniem. 

W poniższej tabeli przedstawiono limity jednostek zarezerwowanych multimediów S1, S2 i S3. Jeśli plik źródłowy jest większy niż limity zdefiniowane w tabeli, zadanie kodowania kończy się niepowodzeniem. Jeśli zakodujesz źródła rozpoznawania 4 KB o długim czasie trwania, musisz użyć jednostek zarezerwowanych multimediów S3, aby osiągnąć potrzebną wydajność. Jeśli masz zawartość 4K o rozmiarze większym niż limit 260 GB w jednostkach zarezerwowanych multimediów S3, Otwórz bilet pomocy technicznej.

|Typ jednostki zarezerwowanej multimediów|Maksymalny rozmiar danych wejściowych (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> konta magazynu muszą znajdować się w tej samej subskrypcji platformy Azure.

## <a name="jobs-encoding--analyzing-limits"></a>Limity zadań (analizowanie & kodowania)

| Zasób | Limit domyślny | 
| --- | --- | 
| [Zadania](transforms-jobs-concept.md) na konto Media Services | 500 000 <sup>(3)</sup> (stałe)|
| Dane wejściowe zadania na zadanie | 50 (stałe)|
| Dane wyjściowe zadania na zadanie | 20 (stałe) |
| [Przekształcenia](transforms-jobs-concept.md) na konto Media Services | 100 (stałe)|
| Przekształcanie danych wyjściowych w transformacji | 20 (stałe) |
| Pliki na dane wejściowe zadania|10 (stałe)|

<sup>3</sup> ta liczba obejmuje zadania w kolejce, zakończone, aktywne i anulowane. Nie obejmuje ono usuniętych zadań. 

Każdy rekord zadania na koncie starszej niż 90 dni zostanie automatycznie usunięty, nawet jeśli łączna liczba rekordów jest mniejsza niż maksymalny limit przydziału. 

## <a name="live-streaming-limits"></a>Limity przesyłania strumieniowego na żywo

| Zasób | Limit domyślny | 
| --- | --- | 
| [Zdarzenia na żywo](live-events-outputs-concept.md) <sup>(4)</sup> na konto Media Services |5|
| Dane wyjściowe na żywo na żywo |3 <sup>(5)</sup> |
| Maksymalny czas trwania danych wyjściowych na żywo | [Rozmiar okna DVR](live-event-cloud-dvr.md) |

<sup>4</sup> Aby uzyskać szczegółowe informacje na temat limitów zdarzeń na żywo, zobacz [porównanie i limity typów zdarzeń na żywo](live-event-types-comparison.md).

<sup>5</sup> danych wyjściowych na żywo rozpocznie się po utworzeniu i Zatrzymaj po usunięciu.

## <a name="packaging--delivery-limits"></a>& limity dostarczania pakietów

| Zasób | Limit domyślny |
| --- | --- |
| [Punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md) (zatrzymane lub uruchomione) dla konta Media Services | 2 |
| Jednostki przesyłania strumieniowego Premium | 10 |
| [Filtry manifestów dynamicznych](filters-dynamic-manifest-overview.md)|100|
| [Zasady przesyłania strumieniowego](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Unikatowe [lokalizatory przesyłania strumieniowego](streaming-locators-concept.md) skojarzone z elementem zawartości w jednym momencie | 100<sup>(7)</sup> (stałe) |

<sup>6</sup> Jeśli używasz niestandardowych [zasad przesyłania strumieniowego](/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw takich zasad dla konta usługi Media Service i ponownie użyć ich do StreamingLocators za każdym razem, gdy potrzebne są te same opcje szyfrowania i protokoły. Nie należy tworzyć nowych zasad przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

<sup>7</sup> lokalizatorów przesyłania strumieniowego nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Aby nadać różne uprawnienia poszczególnym użytkownikom, użyj rozwiązań do zarządzania prawami cyfrowymi (DRM, Digital Rights Management).

## <a name="protection-limits"></a>Limity ochrony

| Zasób | Limit domyślny |
| --- | --- |
| Opcje dla [zasad klucza zawartości](content-key-policy-concept.md) |30 |
| Licencje na miesiąc dla każdego z typów DRM w usłudze Media Services Key Delivery na konto|1 000 000|

## <a name="support-ticket"></a>Bilet pomocy technicznej

W przypadku zasobów, które nie zostały naprawione, możesz poprosił o wypróbowanie przydziałów, otwierając [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Dołącz szczegółowe informacje w żądaniu dotyczące żądanych zmian przydziału, scenariuszy przypadków użycia i wymaganych regionów. <br/>**Nie** twórz dodatkowych kont usługi Azure Media Services w celu uzyskania wyższych limitów.

## <a name="next-steps"></a>Następne kroki

[Omówienie](media-services-overview.md)
