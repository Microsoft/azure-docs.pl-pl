---
title: Błąd przekroczenia rozmiaru zadania
description: Opisuje sposób rozwiązywania problemów, gdy rozmiar zadania lub szablon jest zbyt duży.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889369"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Rozwiązano błędy w przypadku przekroczenia rozmiaru zadania

W tym artykule opisano sposób rozwiązywania błędów **JobSizeExceededException** i **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu pojawia się błąd informujący o przekroczeniu limitów wdrożenia.

## <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wdrożenie przekroczy jeden z dozwolonych limitów. Zazwyczaj ten błąd jest wyświetlany, gdy szablon lub zadanie uruchamiające wdrożenie jest zbyt duże.

Zadanie wdrażania nie może przekroczyć 1 MB. Zadanie zawiera metadane dotyczące żądania. W przypadku dużych szablonów metadane w połączeniu z szablonem mogą przekraczać dozwolony rozmiar zadania.

Rozmiar szablonu nie może przekraczać 4 MB. Limit 4 MB ma zastosowanie do końcowego stanu szablonu po jego rozszerzeniu na definicje zasobów używające [kopiowania](copy-resources.md) do tworzenia wielu wystąpień. Stan końcowy zawiera również rozpoznane wartości dla zmiennych i parametrów.

Inne limity dla szablonu są następujące:

* parametry 256
* 256 zmienne
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

W przypadku używania pętli kopiowania do wdrożenia zasobu nie należy używać nazwy pętli jako zależności:

```json
dependsOn: [ "nicLoop" ]
```

Zamiast tego należy użyć wystąpienia zasobu z pętli, która musi być zależna. Na przykład:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Rozwiązanie 1 — uproszczenie szablonu

Pierwszą opcją jest uproszczenie szablonu. Ta opcja działa, gdy szablon wdraża wiele różnych typów zasobów. Rozważ podzielenie szablonu na [połączone szablony](linked-templates.md). Podziel typy zasobów na grupy logiczne i Dodaj połączony szablon dla każdej grupy. Na przykład, jeśli konieczne jest wdrożenie wielu zasobów sieciowych, można przenieść te zasoby do połączonego szablonu.

Możesz ustawić inne zasoby jako zależne od szablonu połączonego i [uzyskać wartości z danych wyjściowych połączonego szablonu](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Rozwiązanie 2 — zmniejszanie rozmiaru nazwy

Spróbuj skrócić długość nazw używanych dla [parametrów](template-parameters.md), [zmiennych](template-variables.md)i danych [wyjściowych](template-outputs.md). Gdy te wartości są powtarzane przez pętle kopiowania, duża nazwa jest przemnożona wiele razy.