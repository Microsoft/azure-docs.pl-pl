---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282747"
---
Obsługa skompresowanego dźwięku jest implementowana przy użyciu [`GStreamer`](https://gstreamer.freedesktop.org) . W przypadku `GStreamer` plików binarnych przyczyn licencjonowania nie są kompilowane i połączone z zestawem Speech SDK. Deweloperzy muszą zainstalować kilka zależności i wtyczek, zobacz [Instalowanie w systemie Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` pliki binarne muszą znajdować się w ścieżce systemowej, aby zestaw mowy SDK mógł ładować pliki binarne podczas środowiska uruchomieniowego. Jeśli zestaw Speech SDK może znajdować się `libgstreamer-1.0-0.dll` w czasie wykonywania, oznacza to, że pliki binarne znajdują się w ścieżce systemowej.

