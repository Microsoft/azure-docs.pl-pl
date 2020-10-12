---
title: Dostosowywanie modelu osoby w Video Indexer — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie tego, co to jest model osoby w Video Indexer i jak go dostosować.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047043"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Dostosowywanie modelu osoby w Video Indexer

Video Indexer obsługuje rozpoznawanie osobistości w filmach wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarzy na podstawie często zażądanego źródła danych, takiego jak IMDB, Wikipedia i najpopularniejszych wpływów serwisu LinkedIn. Powierzchnie, które nie są rozpoznawane przez Video Indexer są nadal wykrywane, ale pozostawione bez nazwy. Klienci mogą tworzyć niestandardowe modele osób i umożliwiać Video Indexer rozpoznawania twarzy, które nie są rozpoznawane domyślnie. Klienci mogą tworzyć te modele osób, parowania nazwisko osoby za pomocą plików obrazów na stronie osoby.  

Jeśli Twoje konto uwzględnia różne przypadki użycia, możesz skorzystać z możliwości tworzenia wielu modeli osób na konto. Na przykład jeśli zawartość na koncie ma być posortowana w różnych kanałach, można utworzyć osobny model osoby dla każdego kanału. 

> [!NOTE]
> Każdy model osoby obsługuje do 1 000 000 osób, a każde konto ma limit 50 modeli osób. 

Po utworzeniu modelu można go użyć, dostarczając Identyfikator modelu określonego modelu osoby podczas przekazywania/indeksowania lub ponownego indeksowania wideo. Uczenie nowej kroju filmu wideo i aktualizowanie określonego modelu niestandardowego, z którym zostało skojarzone wideo. 

Jeśli nie potrzebujesz pomocy technicznej modelu wielu osób, nie przypisuj identyfikatora modelu osoby do wideo podczas przekazywania/indeksowania lub ponownego indeksowania. W takim przypadku Video Indexer będzie używać domyślnego modelu osoby na Twoim koncie. 

Za pomocą witryny sieci Web Video Indexer można edytować powierzchnie wykryte w filmie wideo i zarządzać wieloma niestandardowymi modelami osób na koncie, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą witryny sieci Web](customize-person-model-with-website.md) . Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą interfejsów API](customize-person-model-with-api.md).
