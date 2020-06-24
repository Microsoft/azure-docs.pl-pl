---
title: Ograniczenia
description: Ograniczenia dotyczące kodu dla funkcji zestawu SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 17f98f452764abdc8458cdc38661d464ecb0a60d
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808543"
---
# <a name="limitations"></a>Ograniczenia

Niektóre funkcje mają rozmiar, liczbę lub inne ograniczenia.

## <a name="azure-frontend"></a>Fronton platformy Azure

* Łączna liczba wystąpień AzureFrontend na proces: 16.
* Łączna liczba wystąpień AzureSession na AzureFrontend: 16.

## <a name="objects"></a>Obiekty

* Łączna liczba dozwolonych obiektów pojedynczego typu (Entity, CutPlaneComponent itp.): 16 777 215.
* Łączna liczba aktywnych płaszczyzn wycinania: 8.

## <a name="geometry"></a>Geometrii

* Łączna liczba dozwolonych materiałów w elemencie zawartości: 65 535.
* Maksymalny wymiar pojedynczej tekstury: 16 384 x 16 384. Większe tekstury źródłowe będą skalowane w dół w procesie konwersji.

## <a name="overall-number-of-polygons"></a>Ogólna liczba wielokątów

Dozwolona liczba wielokątów dla wszystkich załadowanych modeli zależy od rozmiaru maszyny wirtualnej, która została przeniesiona do [interfejsu API REST zarządzania sesją](../how-tos/session-rest-api.md#create-a-session):

| Rozmiar maszyny wirtualnej | Maksymalna liczba wielokątów |
|:--------|:------------------|
|Standardowa| 20 000 000 |
|tytułu| bez limitu |

Aby uzyskać szczegółowe informacje na temat tego ograniczenia, zobacz rozdział [rozmiaru maszyny wirtualnej](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Ograniczenia dotyczące platformy

**Windows 10 Desktop**

* PLATFORMY UWP/x86 jest jedyną obsługiwaną platformą platformy UWP. PLATFORMY UWP/x64 nie jest obsługiwane.
* Win32/x64 jest jedyną obsługiwaną platformą Win32. Win32/x86 nie jest obsługiwany.

**Urządzenie HoloLens 2**

* Funkcja [renderowania z aparatu PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) nie jest obsługiwana.
