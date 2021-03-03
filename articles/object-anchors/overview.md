---
title: Zakotwiczenia obiektów platformy Azure — omówienie
description: Dowiedz się, jak kotwice obiektów platformy Azure pomagają wykrywać obiekty w świecie fizycznym.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 02/18/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 099307ba1085ff6d24bc6bb4000a592aabc8f8f6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749059"
---
# <a name="azure-object-anchors-overview"></a>Zakotwiczenia obiektów platformy Azure — omówienie

Kotwice obiektów platformy Azure umożliwiają aplikacji wykrywanie obiektu w świecie fizycznym przy użyciu modelu 3D i oszacowanie jego ułożenia 6DoF. Ułożenie 6DoF (6 stopni swobody) jest zdefiniowane jako rotacja i translacja między modelem 3D a jego fizycznym odpowiednikiem. 

Kotwice obiektów platformy Azure składa się z zarządzanej usługi na potrzeby konwersji modeli oraz zestawu SDK klienta środowiska uruchomieniowego dla urządzeń HoloLens. Usługa akceptuje model obiektów 3D i wyprowadza model kotwic obiektów platformy Azure. Model kotwic obiektów platformy Azure jest używany wraz z zestawem SDK środowiska uruchomieniowego w celu umożliwienia aplikacji HoloLens załadowania modelu obiektów, wykrywania i śledzenia wystąpień tego modelu w świecie fizycznym.

:::image type="content" source="./media/aoa-overview.jpg" alt-text="Kotwice obiektu platformy Azure w działaniu":::

## <a name="examples"></a>Przykłady

Przykładowe przypadki użycia włączone przez kotwice obiektów platformy Azure obejmują:

- **Trenowanie**. Twórz środowiska szkoleniowe o rzeczywistości mieszanej dla pracowników, bez konieczności umieszczania znaczników lub Poświęcaj czas na ręczne Dostosowywanie wyrównania hologramu. Jeśli chcesz rozszerzyć środowisko szkoleniowe o rzeczywistości mieszanej z automatycznym wykrywaniem i śledzeniem, Pozyskaj swój model do usługi kotwic obiektów i będziesz mieć jeden krok bliżej środowiska bez znacznika.

- **Wskazówki dotyczące zadań**. Przechodzenie między pracownikami za pośrednictwem zestawu zadań może być bardzo uproszczone w przypadku korzystania z rzeczywistości mieszanej. Nałóż instrukcje cyfrowe i najlepsze rozwiązania na podstawie obiektu fizycznego — są one maszyną w fabryce fabryki lub w ramach kuchni w zespole — mogą znacznie zmniejszyć liczbę problemów z ukończeniem zestawu zadań. Wyzwolenie tych środowisk zwykle wymaga jakiejś postaci znacznika lub ręcznego wyrównania, ale z kotwicą obiektów można utworzyć środowisko, które automatycznie wykrywa obiekt związany z zadaniem. Następnie bezproblemowo przepływaj przez wskazówki dotyczące rzeczywistości mieszanej bez znaczników ani ręcznego wyrównania.

- **Znajdowanie zasobów**. Jeśli masz już model 3W niektórych obiektów w obszarze fizycznym, kotwice obiektów mogą umożliwić lokalizowanie i śledzenie wystąpień tego obiektu w środowisku fizycznym.

## <a name="next-steps"></a>Następne kroki

Poniższe sekcje zawierają informacje o rozpoczynaniu pracy z użyciem i kompilowaniu aplikacji przy użyciu kotwic obiektów platformy Azure.

> [!div class="nextstepaction"]
> [Pozyskiwanie modelu](quickstarts/get-started-model-ingestion.md)

> [!div class="nextstepaction"]
> [Unity — HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens z MRTK](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Urządzenie HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
