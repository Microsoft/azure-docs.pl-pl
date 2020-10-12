---
title: Omówienie Data Manager Microsoft Azure StorSimple | Microsoft Docs
description: Dowiedz się więcej na temat rozwiązania StorSimple Data Manager i sposobu korzystania z tej usługi w celu pisania aplikacji korzystających z danych StorSimple i innych usług platformy Azure.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88183500"
---
# <a name="storsimple-data-manager-solution-overview"></a>Przegląd rozwiązania StorSimple Data Manager

## <a name="overview"></a>Omówienie

Microsoft Azure StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie warstwuje dane w lokalnym magazynie i w chmurze. Dane są przechowywane w chmurze w postaci deduplikowanej i skompresowanej w celu uzyskania maksymalnej wydajności i obniżenia kosztów. Ponieważ dane są przechowywane w formacie StorSimple, nie są one łatwo wykorzystywane przez inne aplikacje w chmurze, które mogą być używane.

StorSimple Data Manager pozwala bezproblemowo uzyskać dostęp do danych formatu StorSimple w chmurze i korzystać z nich. Robi to poprzez transformowanie formatu StorSimple do natywnych obiektów blob i plików, których można używać z innymi usługami, takimi jak Azure Media Services, Azure HDInsight i Azure Machine Learning.

Ten artykuł zawiera omówienie rozwiązania StorSimple Data Manager. Wyjaśniono również, jak za pomocą tej usługi pisać aplikacje korzystające z danych StorSimple i innych usług platformy Azure w chmurze.

## <a name="how-it-works"></a>Jak to działa?

Usługa StorSimple Data Manager identyfikuje dane StorSimple w chmurze z urządzenia lokalnego z serii StorSimple 8000. Dane StorSimple w chmurze są deduplikowane, skompresowany format StorSimple. Usługa Data Manager udostępnia interfejsy API do wyodrębniania danych formatu StorSimple i przekształcania ich w inne formaty, takie jak obiekty blob platformy Azure i Azure Files. Te przekształcone dane są następnie łatwo wykorzystywane przez usługę Azure HDInsight i usługę Azure Media Services. Dzięki temu te usługi mogą działać na podstawie przekształconych danych StorSimple z urządzeń lokalnych z serii StorSimple 8000. Ten przepływ przedstawiono na poniższym diagramie.

![Diagram wysokiego poziomu](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager przypadków użycia

Możesz użyć Data Manager z Azure Functions, Azure Automation i Azure Data Factory, aby uruchamiać przepływy pracy na danych tak samo jak w StorSimple. Możesz chcieć przetwarzać zawartość multimedialną przechowywaną w usłudze StorSimple przy użyciu Azure Media Services lub uruchamiać algorytm Machine Learning na tych danych albo Wywołaj klaster Hadoop w celu przeanalizowania danych przechowywanych w usłudze StorSimple. Dzięki rozległej macierzy usług dostępnych na platformie Azure połączonej z danymi w usłudze StorSimple można odblokować możliwości danych.


## <a name="region-availability"></a>Dostępność w danym regionie

StorSimple Data Manager jest dostępna w następujących 7 regionach:

 - Southeast Asia
 - East US
 - Zachodnie stany USA
 - Zachodnie stany USA 2
 - Zachodnio-środkowe stany USA
 - Europa Północna
 - West Europe

Jednak StorSimple Data Manager może służyć do przekształcania danych w następujących regionach. 

![Regiony dostępne dla danych](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ten zestaw jest większy, ponieważ wdrożenie zasobów w którymkolwiek z powyższych regionów jest w stanie wyłączać proces transformacji w poniższych regionach. Tak, jak długo dane znajdują się w jednym z 19 regionów, możesz przekształcić dane przy użyciu tej usługi.


## <a name="choosing-a-region"></a>Wybieranie regionu

Zalecenia:
 - Konto magazynu źródłowego (skojarzone z urządzeniem StorSimple) i docelowe konto magazynu (w którym dane są używane w formacie natywnym) powinny znajdować się w tym samym regionie świadczenia usługi Azure.
 - Data Manager i definicję zadania można wyświetlić w regionie zawierającym konto magazynu StorSimple. Jeśli nie jest to możliwe, wywołaj Data Manager w najbliższym regionie świadczenia usługi Azure, a następnie Utwórz definicję zadania w tym samym regionie, w którym znajduje się konto magazynu StorSimple. 

    Jeśli konto magazynu StorSimple nie znajduje się w 26 regionach, które obsługują tworzenie definicji zadania, zalecamy, aby nie uruchamiać StorSimple Data Manager, ponieważ widoczne są długie opóźnienia i potencjalne opłaty za ruch wychodzący.
    
Firma Microsoft dąży do zapewnienia, że usługi platformy Azure są zawsze dostępne we wszystkich regionach. Nieplanowana awaria usługi może jednak wystąpić przez krótkie okresy w określonym regionie. W takich przypadkach można wyświetlić Data Manager i definicję zadania w regionie, w którym nie ma to wpływ na awarię, i uruchomić zadanie transformacji. W takim scenariuszu może wystąpić kilka dodatkowych opóźnień, ale może to być strategią odzyskiwania w rzadkich przypadkach regionalnych awarii.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

StorSimple Data Manager wymaga klucza szyfrowania danych usługi do przekształcenia z formatu StorSimple na format natywny. Klucz szyfrowania danych usługi jest generowany, gdy pierwsze urządzenie jest rejestrowane w usłudze StorSimple. Aby uzyskać więcej informacji na temat tego klucza, przejdź do [StorSimple zabezpieczenia](storsimple-8000-security.md).

Klucz szyfrowania danych usługi podany jako dane wejściowe jest przechowywany w magazynie kluczy tworzonym podczas tworzenia Data Manager. Magazyn znajduje się w tym samym regionie świadczenia usługi Azure, w którym znajduje się StorSimple Data Manager. Ten klucz jest usuwany po usunięciu usługi Data Manager.

Ten klucz jest używany przez zasoby obliczeniowe do przeprowadzenia transformacji. Te zasoby obliczeniowe znajdują się w tym samym regionie świadczenia usługi Azure, w którym znajduje się definicja zadania. Ten region może lub nie jest taki sam jak region, w którym znajduje się Data Manager.

Jeśli region Data Manager różni się od regionu definicji zadania, ważne jest, aby zrozumieć, jakie dane/metadane znajdują się w poszczególnych regionach. Na poniższym diagramie przedstawiono wpływ różnych regionów na Data Manager i definicję zadania.

![Definicja usługi i zadania w różnych regionach](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Zarządzanie informacjami osobistymi

StorSimple Data Manager nie zbiera ani nie wyświetla żadnych danych osobowych. Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Znane ograniczenia

Usługa ma obecnie następujące ograniczenia:
- Obecnie StorSimple Data Manager nie działa z woluminami szyfrowanymi funkcją BitLocker. Jeśli spróbujesz uruchomić usługę za pomocą zaszyfrowanego dysku, zobaczysz błędy zadań.
- Niektóre metadane plików (w tym listy ACL) nie będą przechowywane w przekształconych danych.
- Ta usługa działa tylko z woluminami NTFS.
- Długość ścieżki pliku musi być krótsza niż 256 znaków, a zadanie zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

[Użyj StorSimple Data Manager interfejsu użytkownika do przekształcania danych](storsimple-data-manager-ui.md).
