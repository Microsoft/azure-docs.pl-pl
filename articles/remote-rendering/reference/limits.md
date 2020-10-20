---
title: Ograniczenia
description: Ograniczenia dotyczące kodu dla funkcji zestawu SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b500a3777ee24d6615022dae2571d021bd0d675a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201175"
---
# <a name="limitations"></a>Ograniczenia

Niektóre funkcje mają rozmiar, liczbę lub inne ograniczenia.

## <a name="azure-frontend"></a>Fronton platformy Azure

* Łączna liczba wystąpień AzureFrontend na proces: 16.
* Łączna liczba wystąpień AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Obiekty

* Łączna liczba dozwolonych obiektów pojedynczego typu (Entity, CutPlaneComponent itp.): 16 777 215.
* Łączna liczba aktywnych płaszczyzn wycinania: 8.

## <a name="geometry"></a>Geometria

* **Animacja:** Animacje są ograniczone do animowania poszczególnych transformacji obiektów gry. Animacje szkieletowe z karnacjami lub animacjami wierzchołków nie są obsługiwane. Ścieżki animacji z pliku zasobów źródłowych nie są zachowywane. Zamiast tego animacje przekształcenia obiektów muszą być sterowane przez kod klienta.
* **Niestandardowe programy do cieniowania:** Tworzenie niestandardowych programów do cieniowania nie jest obsługiwane. Można używać tylko wbudowanych [materiałów kolorów](../overview/features/color-materials.md) lub [materiałów](../overview/features/pbr-materials.md) .
* **Maksymalna liczba odrębnych materiałów** w elemencie zawartości: 65 535. Aby uzyskać więcej informacji na temat automatycznego zmniejszania liczby materiałów, zobacz rozdział dotyczący [materiałów do usuwania duplikatów](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maksymalny wymiar pojedynczej tekstury**: 16 384 x 16 384. Rozmiar większych tekstur źródłowych zostanie zmniejszony przez proces konwersji.

### <a name="overall-number-of-polygons"></a>Ogólna liczba wielokątów

Dozwolona liczba wielokątów dla wszystkich załadowanych modeli zależy od rozmiaru maszyny wirtualnej, która została przeniesiona do [interfejsu API REST zarządzania sesją](../how-tos/session-rest-api.md#create-a-session):

| Rozmiar serwera | Maksymalna liczba wielokątów |
|:--------|:------------------|
|Standardowa| 20 000 000 |
|tytułu| bez limitu |

Aby uzyskać szczegółowe informacje na temat tego ograniczenia, zobacz rozdział [rozmiaru serwera](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Ograniczenia dotyczące platformy

**Windows 10 Desktop**

* Win32/x64 jest jedyną obsługiwaną platformą Win32. Win32/x86 nie jest obsługiwany.

**HoloLens 2**

* Funkcja [renderowania z aparatu PV](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) nie jest obsługiwana.