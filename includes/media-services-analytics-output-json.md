---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183645"
---
Zadanie tworzy plik wyjściowy JSON zawierający metadane dotyczące wykrytych i śledzonych twarzy. Metadane obejmują współrzędne wskazujące lokalizację powierzchni, a także numer IDENTYFIKACYJNy twarzy wskazujący na śledzenie tej osoby. Numery IDENTYFIKACYJNe kroju są podatne na zresetowanie w warunkach, gdy czołowa powierzchnia zostanie utracona lub nakłada się w ramkę, co oznacza, że niektóre osoby mogą uzyskać przypisane wiele identyfikatorów.

Wyjściowy kod JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Główne elementy JSON

| Element | Opis |
| --- | --- |
| Wersja |Odnosi się to do wersji interfejsu API wideo. |
| Dział |"Ticks" na sekundę filmu wideo. |
| przesunięcie |Jest to przesunięcie czasu dla sygnatur czasowych. W wersji 1,0 interfejsów API wideo zawsze będzie równa 0. W przyszłych scenariuszach dział IT może zmienić tę wartość. |
| width, wysoki |Szerokość i wysoki wyjściowej ramki wideo (w pikselach).|
| szybkości |Liczba klatek na sekundę w wideo. |
| [elementy](#fragments-json-elements) |Metadane są podzielone na różne segmenty o nazwie fragmenty. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. |

### <a name="fragments-json-elements"></a>Fragmenty elementów JSON

|Element|Opis|
|---|---|
| start |Godzina rozpoczęcia pierwszego zdarzenia w "Takty". |
| czas trwania |Długość fragmentu w "Takty". |
| index | (Dotyczy tylko Azure Media Redactor) definiuje indeks ramki bieżącego zdarzenia. |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu w "Takty". |
| zdarzenia |Każde zdarzenie zawiera wykryte i śledzone powierzchnie w tym czasie. Jest to tablica zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie. Pusty nawias [] oznacza, że nie wykryto żadnych twarzy. |
| identyfikator |Identyfikator śledzonej wartości. Ta liczba może przypadkowo ulec zmianie, jeśli nie zostanie wykryta. Dana osoba powinna mieć ten sam identyfikator w całym wideo, ale nie może być gwarantowany ze względu na ograniczenia algorytmu wykrywania (zamknięcia itp.). |
| x, y |Lewe górne współrzędne X i Y pola granica czołowa w znormalizowanej skali od 0,0 do 1,0. <br/>-Współrzędne X i Y są względne dla krajobrazu zawsze, dlatego jeśli w przypadku systemu iOS masz pionowy film wideo (lub odwrotnie), konieczne będzie odpowiednie przełożenie współrzędnych. |
| Szerokość, Wysokość |Szerokość i wysokość pola granica czołowa w znormalizowanej skali od 0,0 do 1,0. |
| facesDetected |Jest to dostępne na końcu wyników JSON i zawiera podsumowanie liczby twarzy wykrytej przez algorytm podczas filmu wideo. Ponieważ identyfikatory mogą być przypadkowo resetowane, jeśli twarz przestanie być wykrywane (np. twarz wychodzi z ekranu, wyszukiwany), ta liczba nie zawsze jest równa prawdziwej liczbie twarzy w filmie wideo. |
