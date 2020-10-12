---
title: 'Często zadawane pytania: SRE i DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Często zadawane pytania: zrozumienie relacji między SRE i DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 3d0698f2780a4ccc41eedbde70fddea1766c5f21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090333"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Często zadawane pytania: Jaka jest relacja między SRE i DevOps?

Istnieje zestaw często zadawanych pytań, które obejmują relacje między inżynierami i DevOpsami niezawodności lokacji, w tym "jak są one takie same? Czym się od siebie różnią? Czy jesteśmy w naszej organizacji? ". W tym artykule podjęto próbę udostępnienia niektórych odpowiedzi oferowanych przez społeczności SRE i DevOps, które pomogą bliżej zrozumieć tę relację.

## <a name="how-are-they-the-same"></a>Jak są one takie same?

SRE i DevOps są zarówno nowoczesnymi praktykami operacji, które zostały utworzone i opracowane w odpowiedzi na wyzwania, które obejmują:

- Rosnąca złożoność naszych środowisk produkcyjnych i procesów programistycznych
- zwiększenie zależności biznesowej od ciągłego działania tych środowisk,
- niezdolność do liniowego skalowania liczby pracowników proporcjonalnie do rozmiaru tych środowisk.
- konieczność szybszego przechodzenia z zachowaniem stabilności operacyjnej

Obie operacje mają na celu zwrócenie uwagi na zagadnienia, które są kluczowe dla zajmowania się tymi wyzwaniami, takimi jak monitorowanie/obserwowanie, Automatyzacja, dokumentacja i narzędzia programistycznego tworzenia oprogramowania.

Istnieje duże nakładanie się narzędzi i obszarów pracy między SRE i DevOps. Ze względu na to, że jest to _skoroszyt o niezawodności witryny_ , "SRE uważa się w tych samych elementach jak DevOps, ale z nieznacznie różnymi przyczynami".

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Trzy różne sposoby porównywania dwóch praktyk związanych z operacjami

Podobieństwa między SRE i DevOps są jasne. W rzeczywistości jest to, w jaki sposób są one różne lub rozbieżne. W tym miejscu oferujemy trzy sposoby, aby myśleć o ich relacji w taki sposób, aby Nuance się z tym pytaniem. Użytkownik nie może wyrazić zgody na te odpowiedzi, ale każdy z nich zapewnia dobre miejsce do dyskusji.

### <a name="class-sre-implements-interface-devops"></a>"Klasa SRE implementuje interfejs DevOps"

_Skoroszyt niezawodności witryny_ (wymieniony na [liście książki zasobów](../resources/books.md)) zawiera omówienie SRE i DevOps w pierwszym rozdziale. Ten rozdział używa frazy "Klasa SRE implementuje interfejs DevOps" jako podtytuł. Jest to zalecane, aby zasugerować (przy użyciu frazy zakierowanej do deweloperów), które SRE może być uważane za konkretną implementację DevOps. Zgodnie z rozdziałem "DevOps jest stosunkowo cicha od sposobu uruchamiania operacji na szczegółowym poziomie", a SRE jest znacznie bardziej opisowa w praktyce. Odpowiedź na pytanie, w jaki sposób są SRE, może być uważana za jedną z wielu możliwych implementacji DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE to niezawodność, w której DevOps jest dostarczanie

To porównanie jest nieco muddied ponieważ istnieje wiele definicji zarówno dla SRE, jak i DevOps, ale nadal jest potencjalnie przydatne. Rozpoczyna się ono od pytania "Jeśli trzeba będzie wykonać każdą operację na jednym lub dwóch słowach, które odzwierciedlają jej podstawowe zagadnienia, co to jest?".

Jeśli korzystamy z tej definicji SRE z [centrum niezawodności lokacji](../index.yml):

> Inżynieria niezawodności lokacji to dyscyplina poświęcona pomaganiu organizacji w trwałym osiągnięciu odpowiedniego poziomu niezawodności w zakresie systemów, usług i produktów.

w takim przypadku można łatwo wypowiedzieć, że słowo for SRE ma wartość "niezawodność". Posiadanie odpowiednich środków w środku nazwy również zapewnia pewne doskonałe dowody dla tego żądania.

Jeśli korzystamy z tej definicji DevOps z [Centrum zasobów Azure DevOps](https://docs.microsoft.com/azure/devops/learn/):

> DevOps to związek ludzi, procesów i produktów umożliwiający ciągłe dostarczanie wartości użytkownikom końcowym.

następnie podobną DevOpsą dla elementu "Delivery" może być "dostarczanie".

W związku z tym "SRE jest w sposób wiarygodny, jak DevOps.

### <a name="direction-of-attention"></a>Kierunek uwagi

Ta odpowiedź jest cytowana lub nieco paraphrased od wkładu przez Thomas Limoncelli do książki _wyszukiwania SRE_ wymienionej na [liście książki zasobów](../resources/books.md). Uważamy, że inżynierowie DevOps są w znacznym stopniu skoncentrowane na cyklu życia rozwoju oprogramowania z okazjonalnymi zadaniami produkcyjnymi, a SREs na działania produkcyjne z sporadycznymi zobowiązaniami wydawaniu potoku.

Jednak co ważniejsze, tworzy również diagram, który rozpoczyna się od procesu tworzenia oprogramowania po jednej stronie, a operacje produkcyjne pracują na drugim. Te dwa są połączone przez zwykły potok, który jest zbudowany w celu przejęcia kodu od programisty, Shepherd go przez żądaną liczbę testów i etap, a następnie przenieść ten kod do środowiska produkcyjnego.

Limoncelli uwagi, że inżynierowie DevOps uruchamiają się w środowisku deweloperskim i automatyzują kroki do produkcji. Po zakończeniu nastąpi powrót do optymalizacji wąskich gardeł.

SREs, z drugiej strony, koncentrują się na operacjach produkcyjnych i docierają do potoku jako środek zwiększania wyniku końcowego (na przykład w kierunku odwrotnym).

Jest to różnica w kierunku fokusu SRE i DevOps, który może ułatwić ich rozróżnienie.

## <a name="coexistence-in-the-same-organization"></a>Współistnienie w tej samej organizacji

Ostatecznym pytaniem "czy" może być SRE i DevOps w tej samej organizacji? ".

Odpowiedź na to pytanie to Emphatic "yes!".

Mamy nadzieję, że poprzednie odpowiedzi zapewnią, że te dwie działania są nakładane na siebie i, gdy nie nakładają się na siebie, jak mogą być uzupełniane. Organizacje z ustanowioną praktyką DevOps mogą eksperymentować z praktykami SRE w niewielkiej skali (na przykład próbując SLIs i SLO) bez konieczności zatwierdzania tworzenia SREych stanowisk lub zespołów. Jest to dość typowy wzorzec wdrażania SRE.

## <a name="next-steps"></a>Następne kroki

Chcesz dowiedzieć się więcej o niezawodności witryny lub DevOps? Zapoznaj się z naszą [witryną centrum inżynierów](../index.yml) ds. niezawodności i [Centrum zasobów Azure DevOps](https://docs.microsoft.com/azure/devops/learn/).
