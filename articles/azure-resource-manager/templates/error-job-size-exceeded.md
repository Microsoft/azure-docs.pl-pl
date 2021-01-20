---
title: Błąd przekroczenia rozmiaru zadania
description: Opisuje sposób rozwiązywania problemów, gdy rozmiar zadania lub szablon jest zbyt duży.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610407"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Rozwiązano błędy w przypadku przekroczenia rozmiaru zadania

W tym artykule opisano sposób rozwiązywania błędów **JobSizeExceededException** i **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu pojawia się błąd informujący o przekroczeniu limitów wdrożenia.

## <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wdrożenie przekroczy jeden z dozwolonych limitów. Zazwyczaj ten błąd jest wyświetlany, gdy szablon lub zadanie uruchamiające wdrożenie jest zbyt duże.

Zadanie wdrażania nie może przekroczyć 1 MB. Zadanie zawiera metadane dotyczące żądania. W przypadku dużych szablonów metadane połączone z szablonem mogą przekraczać dozwolony rozmiar zadania.


Ten szablon nie może przekroczyć 4 MB. Limit 4 MB ma zastosowanie do końcowego stanu szablonu po jego rozszerzeniu na definicje zasobów używające [kopiowania](copy-resources.md) do tworzenia wielu wystąpień. Ostatni stan zawiera również rozpoznane wartości zmiennych i parametrów.

Inne limity dla szablonu są następujące:

* parametry 256
* 256 zmienne
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

## <a name="solution-1---simplify-template"></a>Rozwiązanie 1 — uproszczenie szablonu

Pierwszą opcją jest uproszczenie szablonu. Ta opcja działa, gdy szablon wdraża wiele różnych typów zasobów. Rozważ podzielenie szablonu na [połączone szablony](linked-templates.md). Podziel typy zasobów na grupy logiczne i Dodaj połączony szablon dla każdej grupy. Na przykład, jeśli konieczne jest wdrożenie wielu zasobów sieciowych, można przenieść te zasoby do połączonego szablonu.

Możesz ustawić inne zasoby jako zależne od szablonu połączonego i [uzyskać wartości z danych wyjściowych połączonego szablonu](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Rozwiązanie 2 — zmniejszanie rozmiaru nazwy

Spróbuj skrócić długość nazw używanych dla [parametrów](template-parameters.md), [zmiennych](template-variables.md)i danych [wyjściowych](template-outputs.md). Gdy te wartości są powtarzane przez pętle kopiowania, duża nazwa jest przemnożona wiele razy.

## <a name="solution-3---use-serial-copy"></a>Rozwiązanie 3 — Korzystanie z kopii seryjnej

Rozważ zmianę pętli kopiowania z [równoległe na przetwarzanie szeregowe](copy-resources.md#serial-or-parallel). Tej opcji należy używać tylko wtedy, gdy podejrzewasz, że błąd pochodzi z wdrażania dużej liczby zasobów za pośrednictwem kopiowania. Ta zmiana może znacząco zwiększyć czas wdrożenia, ponieważ zasoby nie są wdrażane równolegle.
