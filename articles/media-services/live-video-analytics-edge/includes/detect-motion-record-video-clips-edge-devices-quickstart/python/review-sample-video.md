---
ms.openlocfilehash: df6137e4e00ebd089ef7fb5efa163a513fb30728
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98061084"
---
Po skonfigurowaniu zasobów platformy Azure na potrzeby tego przewodnika Szybki Start do maszyny wirtualnej z systemem Linux na platformie Azure, która jest używana jako urządzenie IoT Edge, zostanie skopiowane krótkie wideo o dużej ilości danych. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Otwórz aplikację, taką jak [VLC Media Player](https://www.videolan.org/vlc/), wybierz kombinację klawiszy CTRL + N i wklej [ten link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) do wideo parkingowej partii, aby rozpocząć odtwarzanie. W przypadku 5-sekundowego znaku biały samochód porusza się w ramach parkingów.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Wykonaj następujące kroki, aby użyć usługi Analiza wideo na żywo na IoT Edge, aby wykryć ruch samochodu i nagrać klip wideo, zaczynając od 5-sekundowego znacznika.
