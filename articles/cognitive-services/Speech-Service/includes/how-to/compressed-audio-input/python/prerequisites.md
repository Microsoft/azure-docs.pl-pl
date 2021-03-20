---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821523"
---
Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencjonowania GStreamer pliki binarne nie są kompilowane i połączone z zestawem Speech SDK. Deweloperzy muszą zainstalować kilka zależności i wtyczek, zobacz [Instalowanie w systemie Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) lub [Instalowanie w systemie Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Pliki binarne GStreamer muszą znajdować się w ścieżce systemowej, aby zestaw mowy SDK mógł ładować pliki binarne podczas środowiska uruchomieniowego. Na przykład w systemie Windows, jeśli zestaw Speech SDK może znajdować się `libgstreamer-1.0-0.dll` w czasie wykonywania, oznacza to, że pliki binarne GStreamer znajdują się w ścieżce systemowej.

