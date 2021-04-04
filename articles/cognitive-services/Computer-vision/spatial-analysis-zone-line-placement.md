---
title: Rozmieszczenie linii i strefy analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować strefy i linie z analizą przestrzenną
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90939411"
---
# <a name="zone-and-line-placement-guide"></a>Przewodnik umieszczania strefy i linii

Ten artykuł zawiera wskazówki dotyczące definiowania stref i linii dla operacji analizy przestrzennej w celu uzyskania dokładnej analizy ruchów osób w miejscu. Dotyczy to wszystkich operacji. 

Strefy i linie są definiowane za pomocą parametru SPACEANALYSIS_CONFIG JSON. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [operacji analizy przestrzennej](spatial-analysis-operations.md) .

## <a name="guidelines-for-drawing-zones"></a>Wskazówki dotyczące rysowania stref

Należy pamiętać, że każdy obszar jest różny; musisz zaktualizować pozycję lub rozmiar w zależności od potrzeb.

Jeśli chcesz zobaczyć określoną sekcję widoku aparatu, Utwórz największą strefę, którą możesz obsłużyć do określonego obszaru podłogi, ale nie z uwzględnieniem innych interesujących Cię obszarów. Zwiększa to dokładność zbieranych danych i zapobiega fałszywych dodatnich z obszarów, które nie mają być śledzone. Należy zachować ostrożność podczas umieszczania rogów wielokąta i upewnienia się, że nie znajdują się poza obszarem, który ma być śledzony.  

### <a name="example-of-a-well-shaped-zone"></a>Przykład strefy dobrze ukształtowanej

Strefa powinna być wystarczająco duża, aby pomieścić trzy osoby stojące wzdłuż każdej krawędzi i skupić się na interesującym obszarze. Analiza przestrzenna będzie identyfikować osoby, których stopy są umieszczane w strefie, dlatego podczas rysowania stref w obrazie 2D Załóżmy, że strefa jest przypięta do produkcji.

![Dokształcona strefa](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Przykłady stref, które nie są poprawnie sformułowane

W poniższych przykładach pokazano niewłaściwie ukształtowane strefy. W tych przykładach obszar zainteresowania jest miejscem przed wyświetlaczem *czasu gry* .

**Strefa nie znajduje się w obłodze.**

![Strefa o złej kształcie](./media/spatial-analysis/zone-not-on-floor.png) 

**Strefa jest za mała.**

![strefa jest za mała](./media/spatial-analysis/zone-too-small.png)

**Strefa nie przechwytuje w pełni obszaru wokół ekranu.**

![Strefa nie przechwytuje w pełni obszaru dookoła zakończenia](./media/spatial-analysis/zone-bad-capture.png)

**Strefa jest zbyt blisko krawędzi obrazu aparatu i nie przechwytuje odpowiedniego ekranu.**

![strefa jest zbyt blisko krawędzi obrazu aparatu i nie przechwytuje odpowiedniego ekranu](./media/spatial-analysis/zone-edge.png)

**Strefa jest częściowo blokowana przez półkę, więc ludzie i piętra nie są w pełni widoczne.**

![strefa jest częściowo zablokowana, przez co ludzie nie są w pełni widoczni](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Przykład prostej linii

Wiersz powinien być wystarczająco długi, aby pomieścić cały wejść. Analiza przestrzenna będzie identyfikować osoby, których stopnie przecinają linię, więc gdy linie rysowania na obrazie 2D Wyobraź sobie, że są one rysowane tak, jakby znajdowały się na podłogę. 

Jeśli to możliwe, należy rozszerzać wiersz szerszy niż rzeczywisty wejść. Jeśli to nie spowoduje dodatkowych przecięć (jak na poniższym obrazie, gdy linia jest względem ściany), należy ją zwiększyć.

![Dobrze sformułowany wiersz](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Przykłady wierszy, które nie są poprawnie sformułowane

W poniższych przykładach pokazano niewłaściwie zdefiniowane wiersze.

**Wiersz nie obejmuje całego sposobu wejścia na piętro.**

![Wiersz nie obejmuje całego sposobu wejścia na piętro](./media/spatial-analysis/zone-line-bad-coverage.png)

**Linia jest zbyt wysoka i nie obejmuje całości drzwi.**

![Linia jest zbyt wysoka i nie obejmuje całości drzwi](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Następne kroki

* [Wdróż aplikację sieci Web zliczania osób](spatial-analysis-web-app.md)
* [Konfigurowanie operacji analizy przestrzennej](./spatial-analysis-operations.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik umieszczania w aparacie](spatial-analysis-camera-placement.md)
