---
title: Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model osoby za pomocą interfejsu API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: dced3cde1aae082bd64d785212b40e100013e0bf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312896"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Dostosowywanie modelu osoby za pomocą interfejsu API Video Indexer

Video Indexer obsługuje wykrywanie kroju i rozpoznawanie osobistości dla zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarzy na podstawie często żądanego źródła danych, takiego jak IMDB, Wikipedia i najpopularniejszych wpływów serwisu LinkedIn. Powierzchnie, które nie są rozpoznawane przez funkcję rozpoznawania osobistości, są wykrywane, ale pozostawione bez nazwy. Po przekazaniu wideo do Video Indexer i powrocie z powrotem możesz wrócić i nazwać powierzchnie, które nie zostały rozpoznane. Po oznaczeniu kroju z nazwą zostanie dodana do modelu osoby Twojego konta. Następnie Video Indexer rozpoznają Tę miarę w przyszłych wideo i poprzednich wideo.

Za pomocą interfejsu API Video Indexer można edytować powierzchnie wykryte w wideo, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web Video Indexer, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą witryny sieci web video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Zarządzanie wieloma modelami osób

Video Indexer obsługuje wiele modeli osób na konto. Ta funkcja jest obecnie dostępna tylko za pomocą interfejsów API Video Indexer.

Jeśli Twoje konto uwzględnia różne scenariusze przypadków użycia, można utworzyć wiele modeli osób dla każdego konta. Na przykład jeśli zawartość jest powiązana z sportem, można utworzyć osobny model dla każdej sportka (piłka, Basketball, piłka i tak dalej).

Po utworzeniu modelu można go użyć, dostarczając Identyfikator modelu określonego modelu osoby podczas przekazywania/indeksowania lub ponownego indeksowania wideo. Uczenie nowej kroju filmu wideo aktualizuje określony model niestandardowy, z którym zostało skojarzone wideo.

Każde konto ma limit 50 modeli osób. Jeśli nie potrzebujesz pomocy technicznej modelu wielu osób, nie przypisuj identyfikatora modelu osoby do wideo podczas przekazywania/indeksowania lub ponownego indeksowania. W takim przypadku Video Indexer używa domyślnego modelu osoby niestandardowej na Twoim koncie.

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

Aby utworzyć nowy model osoby na określonym koncie, użyj interfejsu API [Tworzenie modelu osoby](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) .

Odpowiedź zawiera nazwę i wygenerowany identyfikator modelu osoby, który właśnie został utworzony, zgodnie z poniższym formatem.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Następnie należy użyć wartości **identyfikatora** parametru **personModelId** podczas [przekazywania wideo do indeksu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) lub ponownego [indeksowania wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video).

## <a name="delete-a-person-model"></a>Usuń model osoby

Aby usunąć niestandardowy model osoby z określonego konta, użyj interfejsu API [usuwania modelu osoby](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) .

Po pomyślnym usunięciu modelu osoby indeks bieżących filmów wideo, przy użyciu którego usunięto model, pozostanie bez zmian do momentu ponownego indeksowania. Podczas ponownego indeksowania powierzchnie, które zostały nazwane w usuniętym modelu, nie będą rozpoznawane przez Video Indexer w bieżących filmach wideo, które były indeksowane przy użyciu tego modelu, ale twarze nadal będą wykrywane. Bieżące filmy wideo, które były indeksowane przy użyciu usuniętego modelu, będą teraz używały domyślnego modelu osoby Twojego konta. Jeśli twarze z usuniętego modelu są również nazwane w domyślnym modelu konta, te powierzchnie będą nadal rozpoznawane w filmach wideo.

Nie ma żadnej zwróconej zawartości, gdy model osoby został usunięty pomyślnie.

## <a name="get-all-person-models"></a>Pobierz wszystkie modele osób

Aby uzyskać wszystkie modele osób na określonym koncie, użyj interfejsu API [uzyskiwania modelu osoby](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) .

Odpowiedź zawiera listę wszystkich modeli osób w Twoim koncie (w tym domyślny model osoby na określonym koncie) oraz każdej z nazw i identyfikatorów, które są zgodne z poniższym formatem przykładu.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Można wybrać model, który ma być używany do wideo, przy użyciu `id` wartości modelu osoby dla `personModelId` parametru podczas [przekazywania wideo do indeksowania](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) lub ponownego [indeksowania wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video).

## <a name="update-a-face"></a>Aktualizowanie kroju

To polecenie umożliwia zaktualizowanie kroju obrazu w filmie wideo przy użyciu nazwy z IDENTYFIKATORem wideo i IDENTYFIKATORem kroju. Ta akcja spowoduje zaktualizowanie modelu osoby, z którym zostało skojarzone wideo, podczas przekazywania/indeksowania lub ponownego indeksowania. Jeśli żaden model osoby nie został przypisany, aktualizuje domyślny model osoby dla konta.

System rozpoznaje wystąpienia tej samej klasy z innymi bieżącymi filmami wideo, które współużytkują ten sam model osoby. Rozpoznawanie twarz w innych bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.

Możesz zaktualizować miarę, która Video Indexer rozpoznawana jako osobistości z nową nazwą. Nowa nazwa, która zostanie nadana, będzie mieć pierwszeństwo przed wbudowanym rozpoznawaniem osobistości.

Aby zaktualizować tę funkcję, użyj aktualizacji interfejsu API [rozpoznawania wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) .

Nazwy są unikatowe dla modeli osób, dlatego w przypadku udostępnienia dwóch różnych twarzy w tym samym modelu osoby te same `name` wartości parametrów Video Indexer wyświetlenia twarzy jako ta sama osoba i nastąpi ich zbieżność po ponownym przeprowadzeniu indeksowania wideo.

## <a name="next-steps"></a>Następne kroki

[Dostosuj model osoby za pomocą witryny sieci Web Video Indexer](customize-person-model-with-website.md)
