---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597841"
---
Aby chronić dane w udziałach plików platformy Azure przed utratą lub uszkodzeniem danych, wszystkie udziały plików platformy Azure przechowują wiele kopii każdego pliku podczas ich pisania. W zależności od wymagań obciążenia można wybrać dodatkowe stopnie nadmiarowości. Azure Files obecnie obsługuje następujące opcje nadmiarowości danych:

- **Lokalnie nadmiarowy**: Magazyn lokalnie nadmiarowy, często określany jako LRS, oznacza, że każdy plik jest przechowywany trzy razy w ramach klastra usługi Azure Storage. Chroni to przed utratą danych z powodu błędów sprzętowych, takich jak nieprawidłowy dysk.
- **Strefa nadmiarowa**: Magazyn strefowo nadmiarowy, często określany jako ZRS, oznacza, że każdy plik jest przechowywany trzy razy w trzech różnych klastrach usługi Azure Storage. Trzy odrębne klastry usługi Azure Storage każdy przechowują plik trzy razy, podobnie jak w przypadku magazynu lokalnie nadmiarowego i są fizycznie izolowane w różnych *strefach dostępności*platformy Azure. Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Zapis w magazynie nie jest akceptowany, dopóki nie zostanie zapisany w klastrach magazynu we wszystkich trzech strefach dostępności. 
- **Geograficznie nadmiarowy**: Magazyn Geograficznie nadmiarowy, często określany jako GRS, jest taki sam jak Magazyn lokalnie nadmiarowy, w którym plik jest przechowywany trzy razy w klastrze usługi Azure Storage w regionie podstawowym. Wszystkie zapisy są następnie asynchronicznie replikowane do regionu pomocniczego zdefiniowanego przez firmę Microsoft. Magazyn Geograficznie nadmiarowy oferuje 6 kopii rozmieszczenia danych między dwoma regionami świadczenia usługi Azure. W przypadku poważnych awarii, takich jak stała utrata regionu świadczenia usługi Azure z powodu klęski żywiołowej lub innego podobnego zdarzenia, firma Microsoft wykona pracę w trybie failover, tak aby pomocniczy efekt stał się podstawowym, obsługującym wszystkie operacje. Ponieważ replikacja między regionami podstawowymi i pomocniczymi jest asynchroniczna, w przypadku poważnych awarii dane, które nie zostały jeszcze zreplikowane do regionu pomocniczego, zostaną utracone. Możesz również wykonać ręczną pracę awaryjną konta magazynu geograficznie nadmiarowego.
- **Strefa geograficzna nadmiarowa**: Magazyn stref Geograficznie nadmiarowy, często określany jako GZRS, jest taki sam jak magazyn strefowo nadmiarowy, w którym plik jest przechowywany dziewięć razy w 3 odrębnych klastrach magazynu w regionie podstawowym. Wszystkie zapisy są następnie asynchronicznie replikowane do regionu pomocniczego zdefiniowanego przez firmę Microsoft. Proces trybu failover dla magazynu stref geograficznie nadmiarowego działa tak samo, jak w przypadku magazynu geograficznie nadmiarowego.

Standardowe udziały plików platformy Azure obsługują wszystkie cztery typy nadmiarowości, podczas gdy Premium udziały plików platformy Azure obsługują tylko lokalnie nadmiarowy i magazyn strefowo nadmiarowy.

Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2) udostępniają dwie dodatkowe opcje nadmiarowości, które nie są obsługiwane przez Azure Files: dostęp do magazynu geograficznie nadmiarowego, często określany jako RA-GRS, i odczytywanie dostępnego magazynu geograficznie nadmiarowego, często określanego jako RA-GZRS. Można udostępnić udziały plików platformy Azure w ramach kont magazynu z tymi opcjami, jednak Azure Files nie obsługują odczytywania z regionu pomocniczego. Udziały plików platformy Azure wdrożone w geograficznie lub strefy geograficznej nadmiarowe konta magazynu są rozliczane jako magazyn Geograficznie nadmiarowy lub geograficznie nadmiarowy.