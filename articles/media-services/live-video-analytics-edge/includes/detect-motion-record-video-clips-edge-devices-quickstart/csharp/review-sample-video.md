---
ms.openlocfilehash: e0c313ea5e5062f725d0a65fefeac444214cf863
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682214"
---
Po skonfigurowaniu zasobów platformy Azure na potrzeby tego przewodnika Szybki Start do maszyny wirtualnej z systemem Linux na platformie Azure, która jest używana jako urządzenie IoT Edge, zostanie skopiowane krótkie wideo o dużej ilości danych. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Otwórz aplikację, taką jak [VLC Media Player](https://www.videolan.org/vlc/), wybierz kombinację klawiszy CTRL + N i wklej [ten link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) do wideo parkingowej partii, aby rozpocząć odtwarzanie. W przypadku 5-sekundowego znaku biały samochód porusza się w ramach parkingów.

Wykonaj następujące kroki, aby użyć usługi Analiza wideo na żywo na IoT Edge, aby wykryć ruch samochodu i nagrać klip wideo, zaczynając od 5-sekundowego znacznika.
