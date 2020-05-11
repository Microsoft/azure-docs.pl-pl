---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006466"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Znajdź kotwicę przestrzenną w chmurze

Możliwość lokalizowania wcześniej zapisanego zakotwiczenia w chmurze jest jednym z głównych powodów używania kotwic przestrzennych platformy Azure. Istnieje kilka różnych sposobów lokalizowania zakotwiczenia przestrzennego w chmurze. W danym momencie możesz użyć jednej strategii.
- Lokalizowanie kotwic według identyfikatora.
- Zlokalizuj kotwice połączone z wcześniej zlokalizowaną kotwicą. W [tym miejscu](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)możesz poznać relacje kotwic.
- Znajdź kotwicę przy użyciu bardzo [dużej lokalizacji](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Za każdym razem, gdy zlokalizujesz kotwicę, kotwice przestrzenne platformy Azure spróbują użyć zebranych danych środowiska, aby rozszerzyć informacje wizualne dotyczące zakotwiczenia. Jeśli masz problemy z lokalizowaniem kotwicy, może być przydatne utworzenie kotwicy, a następnie odnalezienie jej kilka razy z różnych kątów i warunków oświetlenia.

W przypadku lokalizowania kotwic w chmurze według identyfikatora, należy zapisać identyfikator kotwicy w chmurze w usłudze zaplecza aplikacji i udostępnić go wszystkim urządzeniom, które mogą prawidłowo uwierzytelniać się w nim. Aby zapoznać się z przykładem, zobacz [Samouczek: udostępnianie kotwic przestrzenny między urządzeniami](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Tworzenie wystąpienia `AnchorLocateCriteria` obiektu, Ustawianie identyfikatorów, których szukasz, i wywoływanie `CreateWatcher` metody w sesji przez udostępnienie. `AnchorLocateCriteria`