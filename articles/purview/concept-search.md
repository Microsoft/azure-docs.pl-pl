---
title: Poznaj funkcje wyszukiwania w usłudze Azure kontrolą (wersja zapoznawcza)
description: W tym artykule opisano sposób, w jaki usługa Azure kontrolą umożliwia odnajdywanie danych za pomocą funkcji wyszukiwania.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553966"
---
# <a name="understand-search-features-in-azure-purview"></a>Poznaj funkcje wyszukiwania w usłudze Azure kontrolą

Ten artykuł zawiera omówienie funkcji wyszukiwania w usłudze Azure kontrolą. Wyszukiwanie jest podstawową możliwością platformy kontrolą, która umożliwia odnajdywanie i wykorzystywanie danych w organizacji.

## <a name="search"></a>Wyszukaj

Środowisko wyszukiwania kontrolą jest obsługiwane przez zarządzany indeks wyszukiwania. Gdy źródło danych zostanie zarejestrowane w usłudze kontrolą, jego metadane są indeksowane przez usługę wyszukiwania, aby umożliwić łatwe odnajdywanie. Indeks zapewnia możliwości związane z wyszukiwaniem i uzupełnia żądania wyszukiwania, badając miliony zasobów metadanych. Funkcja wyszukiwania ułatwia odnajdywanie, poznawanie i używanie danych w celu uzyskania największej wartości.

Środowisko wyszukiwania w programie kontrolą to proces trzech etapów:

1. Pole wyszukiwania pokazuje historię zawierającą ostatnio używane słowa kluczowe i zasoby.
1. Po rozpoczęciu wpisywania naciśnięć klawiszy, wyszukiwanie sugeruje pasujące słowa kluczowe i zasoby. 
1. Zostanie wyświetlona strona wynik wyszukiwania z zasobami pasującymi do wprowadzonego słowa kluczowego.

## <a name="reduce-the-time-to-discover-data"></a>Skrócenie czasu odnajdywania danych

Odnajdywanie danych to pierwszy krok dla obciążeń związanych z analizą danych lub zarządzaniem danymi dla odbiorców danych. Odnajdywanie danych może być czasochłonne, ponieważ użytkownik może nie wiedzieć, gdzie znaleźć potrzebne dane. Nawet po znalezieniu danych mogą wystąpić wątpliwości dotyczące tego, czy można zaufać danym i podjąć zależności. 

Celem wyszukiwania w usłudze Azure kontrolą jest przyspieszenie procesu odnajdywania danych dzięki zapewnieniu gestów, aby szybko znaleźć odpowiednie dane.

## <a name="recent-search-and-suggestions"></a>Ostatnie wyszukiwanie i sugestie

Wiele razy może pracować nad wieloma projektami w tym samym czasie. Aby ułatwić wznowienie poprzednich projektów, funkcja wyszukiwania kontrolą umożliwia wyświetlanie ostatnich słów kluczowych wyszukiwania i sugestii. Ponadto możesz zarządzać ostatnią historią wyszukiwania, wybierając pozycję **Wyświetl wszystko** w liście rozwijanej pole wyszukiwania.

## <a name="filters"></a>Filtry

Filtry (znane również jako zestawy *reguł*) są przeznaczone do uzupełniania wyszukiwania. Filtry są wyświetlane na stronie wyników wyszukiwania. Filtry na stronie z wynikami wyszukiwania obejmują klasyfikację, etykietę czułości, źródło danych i właścicieli. Użytkownicy mogą wybrać określone wartości w filtrze, aby wyświetlić tylko pasujące zasoby danych i ograniczyć wyniki wyszukiwania do pasujących elementów zawartości.

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Pasujące słowa kluczowe na stronie wyników wyszukiwania są wyróżniane, aby ułatwić zidentyfikowanie przyczyny zwrócenia zasobów danych przez wyszukiwanie. Dopasowanie słowa kluczowego może wystąpić w wielu polach, takich jak nazwa zasobu danych, opis i właściciel.

Może nie być oczywiste, dlaczego zasób danych jest uwzględniany w wyszukiwaniu, nawet z włączonym zaznaczaniem trafień. Wszystkie właściwości są domyślnie przeszukiwane. W związku z tym można zwrócić zasób danych z powodu dopasowania właściwości na poziomie kolumny. Ponieważ wielu użytkowników może dodawać adnotacje do zasobów danych z ich własnymi klasyfikacjami i opisami, nie wszystkie metadane są wyświetlane na liście wyników wyszukiwania.

## <a name="sort"></a>Sortowanie

Użytkownicy mają dwie opcje sortowania wyników wyszukiwania. Mogą sortować według nazwy zasobu lub według domyślnych zasad wyszukiwania.

## <a name="search-relevance"></a>Istotność wyszukiwania

Istotność to domyślny porządek sortowania na stronie wyników wyszukiwania. Istotność wyszukiwania umożliwia znalezienie dokumentów zawierających słowo kluczowe wyszukiwania (niektóre lub wszystkie). Zasoby zawierające wiele wystąpień słowa kluczowego Search są wyższe. Ponadto niestandardowe mechanizmy oceniania są stale dostrojone w celu poprawy znaczenia wyszukiwania.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie konta usługi Azure kontrolą w Azure Portal](create-catalog-portal.md)
* [Szybki Start: Tworzenie konta usługi Azure kontrolą za pomocą Azure PowerShell/interfejsu wiersza polecenia platformy Azure](create-catalog-powershell.md)
* [Szybki Start: korzystanie z programu kontrolą Studio](use-purview-studio.md)
