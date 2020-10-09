---
title: Grupy inteligentne
description: Grupy inteligentne to agregacja alertów, które pomagają zredukować hałas alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 05b05f8bc079bb3768ac2f1a03593bc9260b41aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77665497"
---
# <a name="smart-groups"></a>Grupy inteligentne

Typowym wyzwaniem związanym z alertami jest przechodzenie między zakłóceniami, aby dowiedzieć się, jakie są rzeczywiste kwestie — grupy inteligentne są przeznaczone do rozwiązania tego problemu.  

Grupy inteligentne są tworzone automatycznie przy użyciu algorytmów uczenia maszynowego w celu łączenia powiązanych alertów, które reprezentują pojedynczy problem.  Po utworzeniu alertu algorytm dodaje go do nowej lub istniejącej grupy inteligentnej na podstawie informacji, takich jak wzorce historyczne, podobne właściwości i podobna struktura. Na przykład jeśli procesor CPU na kilku maszynach wirtualnych w subskrypcji jednocześnie przekroczy wiele indywidualnych alertów, a jeśli takie alerty wystąpiły kiedykolwiek w przeszłości, te alerty prawdopodobnie zostaną zgrupowane w jednej grupie inteligentnej, co sugeruje potencjalną powszechną przyczynę główną. Oznacza to, że w celu rozwiązywania problemów z alertami, grupy inteligentne nie tylko umożliwiają zredukowanie szumu przez zarządzanie powiązanymi alertami jako pojedynczą zagregowaną jednostką, a także prowadzi do tych wspólnych głównych przyczyn dla ich alertów.

Obecnie algorytm uwzględnia tylko alerty z tej samej usługi monitorowania w ramach subskrypcji. Grupy inteligentne mogą zmniejszyć do 99% szumu alertów w ramach tej konsolidacji. Możesz zobaczyć przyczynę dołączenia alertów do grupy na stronie szczegółów grupy inteligentnej.

Możesz wyświetlić szczegóły grup inteligentnych i ustawić stan podobnie jak w przypadku alertów. Każdy alert jest członkiem jednej i tylko jednej grupy inteligentnej. 

## <a name="smart-group-state"></a>Stan grupy inteligentnej

Stan grupy inteligentnej jest podobnym pojęciem do stanu alertu, który umożliwia zarządzanie procesem rozwiązywania na poziomie grupy inteligentnej. Podobny do stanu alertu, gdy tworzona jest grupa inteligentna, ma **Nowy** stan, który można zmienić na **potwierdzony** lub **zamknięty**.

Obsługiwane są następujące inteligentne Stany grup.

| Stan | Opis |
|:---|:---|
| Nowy | Problem został właśnie wykryty i nie został jeszcze zweryfikowany. |
| Potwierdzony | Administrator przejrzał grupę inteligentną i uruchomił ją. |
| Zamknięte | Problem został rozwiązany. Po zamknięciu grupy inteligentnej można ją otworzyć ponownie, zmieniając ją na inny stan. |

[Dowiedz się, jak zmienić stan grupy inteligentnej.](https://aka.ms/managing-alert-smart-group-states)

> [!NOTE]
>  Zmiana stanu grupy inteligentnej nie powoduje zmiany stanu alertów poszczególnych członków.

## <a name="smart-group-details-page"></a>Strona szczegółów grupy inteligentnej

Po wybraniu grupy inteligentnej zostanie wyświetlona strona szczegółów grupy inteligentnej. Zawiera szczegółowe informacje o grupie inteligentnej, w tym przyczynę, która została użyta do utworzenia grupy, i umożliwia zmianę jej stanu.
 
![Szczegóły grupy inteligentnej](media/alerts-smartgroups-overview/smart-group-detail.png)


Strona szczegółów grupy inteligentnej zawiera następujące sekcje.

| Sekcja | Opis |
|:---|:---|
| Alerty | Wyświetla listę poszczególnych alertów, które znajdują się w grupie inteligentnej. Wybierz Alert, aby otworzyć stronę szczegółów alertu. |
| Historia | Wyświetla listę wszystkich akcji podejmowanych przez grupę inteligentną i wszelkie zmiany, które są w niej wprowadzane. Jest to obecnie ograniczone do zmian stanu i zmian członkostwa w alertach. |

## <a name="smart-group-taxonomy"></a>Taksonomia grupy inteligentnej

Nazwa grupy inteligentnej jest nazwą pierwszego alertu. Nie można utworzyć grupy inteligentnej ani zmienić jej nazwy.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie grupami inteligentnymi](https://aka.ms/managing-smart-groups)
- [Zmiana alertu i stanu grupy inteligentnej](https://aka.ms/managing-alert-smart-group-states)


