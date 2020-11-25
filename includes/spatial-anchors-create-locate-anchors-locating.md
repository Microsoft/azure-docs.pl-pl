---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993054"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Możliwość lokalizowania wcześniej zapisanego zakotwiczenia w chmurze jest jednym z głównych powodów używania kotwic przestrzennych platformy Azure. Istnieje kilka różnych sposobów lokalizowania zakotwiczenia przestrzennego w chmurze. W danym momencie możesz użyć jednej strategii.
- Lokalizowanie kotwic według identyfikatora.
- Zlokalizuj kotwice połączone z wcześniej zlokalizowaną kotwicą. W [tym miejscu](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md)możesz poznać relacje kotwic.
- Znajdź kotwicę przy użyciu bardzo [dużej lokalizacji](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Za każdym razem, gdy zlokalizujesz kotwicę, kotwice przestrzenne platformy Azure spróbują użyć zebranych danych środowiska, aby rozszerzyć informacje wizualne dotyczące zakotwiczenia. Jeśli masz problemy z lokalizowaniem kotwicy, może być przydatne utworzenie kotwicy, a następnie odnalezienie jej kilka razy z różnych kątów i warunków oświetlenia.

W przypadku lokalizowania kotwic w chmurze według identyfikatora, należy zapisać identyfikator kotwicy w chmurze w usłudze zaplecza aplikacji i udostępnić go wszystkim urządzeniom, które mogą prawidłowo uwierzytelniać się w nim. Aby zapoznać się z przykładem, zobacz [Samouczek: udostępnianie kotwic przestrzenny między urządzeniami](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Tworzenie wystąpienia `AnchorLocateCriteria` obiektu, Ustawianie identyfikatorów, których szukasz, i wywoływanie `CreateWatcher` metody w sesji przez udostępnienie `AnchorLocateCriteria` .