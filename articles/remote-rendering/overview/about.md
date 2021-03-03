---
title: Informacje
description: Wprowadzenie do zdalnego renderowania na platformie Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 6001fff54b86b7c18684092aad6c7fdf64f81990
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729950"
---
# <a name="about-azure-remote-rendering"></a>Azure Remote Rendering — informacje

*Zdalne renderowanie na platformie Azure* (ARR) to usługa, która pozwala na renderowanie wysokiej jakości interaktywnej zawartości 3D w chmurze i przesyłanie strumieniowe w czasie rzeczywistym do urządzeń, takich jak HoloLens 2.

![Diagram przedstawiający przykład renderowania wysokiej jakości interaktywnego aparatu 3W.](../media/arr-engine.png)

Urządzenia nietetheringowe mają ograniczoną moc obliczeniową do renderowania złożonych modeli. W przypadku wielu aplikacji byłoby to nieakceptowalne, ale w celu zmniejszenia wyglądu wizualizacji w dowolny sposób. Poniższy zrzut ekranu porównuje model z pełną szczegółowością z modelem, który został Decimated przy użyciu wspólnego narzędzia do tworzenia zawartości:

![Przykładowy model](./media/engine-model-decimated.png)

Zredukowany model składa się z około 200 000 trójkątów (łącznie ze szczegółowymi częściami wewnętrznymi), a nie więcej niż 18 000 000 trójkątów w oryginalnym modelu.

*Zdalne renderowanie* rozwiązuje ten problem przez przeniesienie obciążenia renderowania do wysokiej klasy procesorów GPU w chmurze. Aparat grafiki hostowany w chmurze renderuje obraz, koduje go jako strumień wideo i strumienie dla urządzenia docelowego.

## <a name="hybrid-rendering"></a>Renderowanie hybrydowe

W większości aplikacji nie jest wystarczające, aby renderować skomplikowany model. Wymagany jest również niestandardowy interfejs użytkownika zapewniający funkcjonalność dla użytkownika. Zdalne renderowanie na platformie Azure nie wymusza użycia dedykowanej struktury interfejsu użytkownika, zamiast obsługuje *renderowanie hybrydowe*. Oznacza to, że można renderować elementy na urządzeniu przy użyciu preferowanej metody, takiej jak [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Na końcu ramki funkcja renderowania zdalnego platformy Azure automatycznie łączy lokalnie wyrenderowaną zawartość ze zdalnym obrazem. Jest to nawet możliwe z poprawnym zamknięcia.

## <a name="multi-gpu-rendering"></a>Renderowanie przez wiele procesorów GPU

Niektóre modele są zbyt złożone, aby można było renderować przy użyciu interaktywnej szybkości klatek nawet dla procesora GPU o wysokiej klasy. Szczególnie w przypadku wizualizacji przemysłowej jest to typowy problem. Aby dodatkowo wypchnąć limity, zdalne renderowanie na platformie Azure umożliwia rozproszenie obciążenia do wielu procesorów GPU. Wyniki są scalane w jeden obraz, dzięki czemu proces jest całkowicie niewidoczny dla użytkownika.

## <a name="high-level-architecture"></a>Architektura wysokiego poziomu

Ten diagram ilustruje architekturę renderowania zdalnego:

![Architektura](./media/arr-high-level-architecture.png)

Pełny cykl generowania obrazu obejmuje następujące kroki:

1. Po stronie klienta: Konfiguracja ramki
    1. Twój kod: dane wejściowe użytkownika są przetwarzane, wykres sceny zostanie zaktualizowany
    1. Kod ARR: aktualizacje grafu sceny i przewidywane ułożenie elementu podrzędnego są wysyłane do serwera
1. Po stronie serwera: renderowanie zdalne
    1. Aparat renderowania dystrybuuje renderowanie między dostępnymi procesorami GPU
    1. Dane wyjściowe z wielu procesorów GPU składają się na pojedynczy obraz
    1. Obraz został zakodowany jako strumień wideo, wysłany z powrotem do klienta
1. Po stronie klienta: finalizowanie
    1. Twój kod: renderowana jest opcjonalna zawartość lokalna (interfejs użytkownika, znaczniki,...)
    1. Kod ARR: w elemencie "obecny" lokalna zawartość jest automatycznie scalana ze strumieniem wideo

Opóźnienie sieci to główny problem. Czas między wysłaniem żądania i odebraniem wyniku jest zwykle zbyt długi dla interaktywnych częstotliwości klatek. W każdej chwili może być w locie więcej niż jedna ramka.

## <a name="next-steps"></a>Następne kroki

* [Wymagania systemowe](system-requirements.md)
* [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](../quickstarts/render-model.md)
