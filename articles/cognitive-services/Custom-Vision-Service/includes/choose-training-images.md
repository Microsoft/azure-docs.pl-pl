---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256304"
---
Jako minimum zalecamy używanie co najmniej 30 obrazów na tag w początkowym zestawie szkoleniowym. Warto również zebrać kilka dodatkowych obrazów w celu przetestowania modelu po jego przeszkoleniu.

Aby efektywnie szkolić model, użyj obrazów z odmianą wizualną. Wybierz obrazy, które różnią się w zależności od:
* kąt kamery
* oświetlenia
* tło
* styl wizualizacji
* osoby/zgrupowane elementy
* size
* typ

Ponadto upewnij się, że wszystkie obrazy szkoleniowe spełniają następujące kryteria:
* Format jpg, PNG, BMP lub gif
* nie więcej niż 6MB w rozmiarze (4 MB dla obrazów predykcyjnych)
* nie mniej niż 256 pikseli na najkrótszej krawędzi; Każdy obraz krótszy niż ten zostanie automatycznie przeskalowany w górę przez Custom Vision Service

> [!NOTE]
> Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.