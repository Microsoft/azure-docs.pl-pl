---
title: Najlepsze rozwiązania
description: Zalecane najlepsze rozwiązania w celu uzyskania lepszych wyników
author: ariye
ms.author: crtreasu
ms.date: 02/17/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: da3be6e3d97e50b27ded29ba017164fdbd9a0a5b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503049"
---
# <a name="best-practices"></a>Najlepsze rozwiązania

Zalecamy wypróbowanie niektórych z tych kroków w celu uzyskania najlepszych wyników.

## <a name="ingestion"></a>Pozyskiwanie

- Sprawdź wymiary obiektów fizycznych. Kotwice obiektów najlepiej sprawdzają się w przypadku obiektów, których najmniejszy wymiar znajduje się w zakresie zalecanej wartości 1M-10 mln.
- Zbadaj model 3D w oprogramowaniu takim jak [**MeshLab**](https://www.meshlab.net/) , aby poznać następujące szczegóły.
  - Upewnij się, że model 3W ma trójkątną siatkę i że trójkąty na zewnątrz powierzchni zewnętrznej. Oznacza to, że wierzchołki powinny być zorientowane, aby normalne wypełniać reguły po prawej stronie w ich orientacji.
  - Upewnij się, że model 3D został określony z poprawnymi jednostkami skalowania w odniesieniu do obiektów fizycznych. Jednostki powinny być jednym z: ***centymetry, decimeters, stopy, cale, kilometry, metry, metry***.
  - Potwierdź nominalny kierunek ciężkości odnoszący się do rzeczywistej pionowej orientacji obiektu. Jeśli obiekt jest pionowy w dół/grawitacja to-Y, użyj wartości ***(0,-1, 0)** _ lub _*_ (0, 0,-1) _ * * dla-Z, a podobnie do dowolnego innego kierunku.
  - Upewnij się, że model 3D jest zakodowany w jednym z obsługiwanych formatów: `.glb` , `.gltf` , `.ply` , `.fbx` , `.obj` .
- Nasza usługa konwersji modeli może zająć dużo czasu, aby przetwarzać duży, wysoki model LOD (poziom szczegółowości). W celu uzyskania skuteczności można wstępnie przetworzyć model 3D w celu usunięcia wnętrza twarzy.

## <a name="detection"></a>Wykrywanie

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Udostępniony zestaw SDK środowiska uruchomieniowego wymaga regionu wyszukiwania dostarczonego przez użytkownika w celu wyszukania i wykrycia obiektów fizycznych. Region wyszukiwania może być polem ograniczenia, sferą, frustum widoku lub dowolną kombinacją tych elementów. Aby uniknąć fałszywego wykrywania, preferowane jest ustawienie regionu wyszukiwania wystarczająco duże, aby pokryć obiekt. Gdy korzystasz z dostarczonych przykładowych aplikacji, możesz przystąpić po jednej stronie obiektu około 2 metrów od najbliższej powierzchni i uruchomić aplikację.
- Przed uruchomieniem aplikacji obiekt zakotwiczony na urządzeniu HoloLens 2 Usuń hologramy w sąsiedztwie miejsca pracy przy użyciu ustawień głównych urządzeń za pomocą ***ustawień->hologramów systemu >***

  Ten krok zapewnia, że jeśli nowy obiekt, taki jak samochód, znajduje się w tym samym miejscu, co zajęte wcześniej, lub obiekt został przeniesiony z przestrzeni docelowej, wszelkie stare i nieistotne hologramy nie będą utrwalane i utworzysz wizualizację mylącą dla obiektu aktualnie w widoku.
- Po usunięciu hologramów i przed rozpoczęciem korzystania z aplikacji Zeskanuj obiekt, taki jak samochód, patrząc na obiekt przy założeniu, że urządzenie należy do ponad 1-2 metrów i powoli przechodzą cały obiekt o jeden lub dwa razy.

  Ten krok zapewnia, że wszystkie pozostałe oszacowania powierzchni, które zostały utworzone w miejscu przez wcześniejsze obiekty i skany, są odświeżane przy użyciu powierzchni bieżącego obiektu docelowego, z którym zamierzasz współpracować. W przeciwnym razie aplikacja może wyświetlać podwójne widmo, co prowadzi do niedokładnego wyrównania modelu 3D i skojarzonych z nim hologramów. Przed skanowaniem przed przeskanowaniem obiekt znacznie zmniejsza opóźnienie wykrywania AOA, powiedzmy od 30 sekund do 5 sekund.
- W przypadku ciemnych i wysoce odbijających obiektów, może być konieczne przeskanowanie obiektu z bliższego zakresu, a także przemieszczenie w górę i w dół i w lewo i w prawo, aby umożliwić urządzeniu wyświetlanie powierzchni z wielu kątów i wielu odległości.
- Gdy widzisz nieprawidłowe wykrywanie obiektów, takie jak Orientacja przerzucana lub jeśli jest to nieprawidłowe, takie jak model przechylony, należy wizualizować mapowanie przestrzenne. Często nieprawidłowe wyniki są spowodowane niską lub niekompletnym odbudową powierzchni. Można usunąć Hologramy, skanować obiekt i ponownie uruchamiać wykrywanie obiektów w aplikacji.
- Udostępniony zestaw SDK środowiska uruchomieniowego zawiera kilka parametrów umożliwiających użytkownikom precyzyjne dostosowanie wykrywania, jak pokazano w naszych przykładowych aplikacjach. Parametry domyślne działają dobrze w przypadku większości obiektów. Jeśli okaże się, że musisz dostosować je do określonych obiektów, poniżej przedstawiono niektóre zalecenia:
  - Użyj niższej wartości progowej pokrycia powierzchni, jeśli obiekt fizyczny jest duży, ciemny lub Shiny.
  - Zezwalaj na małą zmianę skalowania (na przykład 0,1) dla dużego obiektu, takiego jak samochód.
  - Zezwalaj na pewne odchylenia w stopniach między lokalnym wymiarem pionowym obiektu a grawitacją, gdy obiekt jest na pochyłości.
