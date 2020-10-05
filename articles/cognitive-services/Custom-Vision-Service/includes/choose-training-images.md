---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "70130104"
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
