---
title: Korzystanie z funkcji ułatwień dostępu w projektancie
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat skrótów klawiaturowych i funkcji ułatwień dostępu czytnika ekranu dostępnych w projektancie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893419"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Używanie narzędzia Azure Machine Learning Designer przy użyciu klawiatury

Dowiedz się, jak używać programu Azure Machine Learning Designer przy użyciu klawiatury i czytnika ekranu. Aby zapoznać się z listą skrótów klawiaturowych, które działają wszędzie na Azure Portal, zobacz [skróty klawiaturowe w Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Ten przepływ pracy został przetestowany przy użyciu programu [narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) i [szczęk](https://www.freedomscientific.com/products/software/jaws/), ale powinien współpracować z innymi czytnikami ekranu standardowego.

## <a name="navigate-the-pipeline-graph"></a>Nawigowanie po grafie potoku

Wykres potoku jest zorganizowany jako lista zagnieżdżona. Lista zewnętrzna to lista modułów, która opisuje wszystkie moduły na wykresie potoku. Lista wewnętrzna to lista połączeń, która opisuje wszystkie połączenia określonego modułu.  

1. Na liście modułów Użyj klawisza Strzałka, aby przełączyć moduły.
1. Użyj klawisza Tab, aby otworzyć listę połączeń dla modułu docelowego.
1. Użyj klawisza Strzałka, aby przełączać się między portami połączenia dla modułu.
1. Użyj "G", aby przejść do modułu docelowego.

## <a name="edit-the-pipeline-graph"></a>Edytowanie wykresu potoku

### <a name="add-a-module-to-the-graph"></a>Dodawanie modułu do wykresu

1. Użyj kombinacji klawiszy CTRL + F6, aby przełączyć fokus z kanwy do drzewa modułów.
1. Znajdź żądany moduł w drzewie modułów przy użyciu standardowej kontrolki TreeView.

### <a name="edit-a-module"></a>Edytowanie modułu

Aby połączyć moduł z innym modułem:

1. Naciśnij klawisze Ctrl + Shift + H w przypadku kierowania do modułu na liście modułów, aby otworzyć pomocnika połączenia.
1. Edytuj porty połączenia dla modułu.

Aby dostosować właściwości modułu:

1. Naciśnij klawisze Ctrl + Shift + E, gdy obiektem docelowym jest moduł, aby otworzyć właściwości modułu.
1. Edytuj właściwości modułu.

## <a name="navigation-shortcuts"></a>Skróty nawigacji

| Klawiszy | Opis |
|-|-|
| Ctrl + F6 | Przełącz fokus między kanwą a drzewem modułu |
| Ctrl + F1   | Otwórz kartę informacji, gdy koncentruje się na węźle w drzewie modułów |
| Ctrl + Shift + H | Otwórz pomocnika połączenia, gdy fokus znajduje się w węźle |
| Ctrl + Shift + E | Otwórz właściwości modułu, gdy fokus znajduje się w węźle |
| Ctrl + G | Przenieś fokus na pierwszy węzeł zakończony niepowodzeniem, Jeśli uruchomienie potoku nie powiodło się |

## <a name="action-shortcuts"></a>Skróty akcji

Użyj następujących skrótów z kluczem dostępu. Aby uzyskać więcej informacji o kluczach dostępu, zobacz https://en.wikipedia.org/wiki/Access_key .

| Klawiszy | Akcja |
|-|-|
| Klucz dostępu + R | Uruchom |
| Klucz dostępu + P | Opublikuj |
| Klucz dostępu + C | Klonowanie |
| Klucz dostępu + D | Wdrażanie |
| Klucz dostępu + I | Utwórz/zaktualizuj potok wnioskowania |
| Klucz dostępu + B | Utwórz/zaktualizuj potok wnioskowania dotyczącego partii |
| Klucz dostępu + K | Otwórz listę rozwijaną "Utwórz potok wnioskowania" |
| Klawisz dostępu + U | Otwórz listę rozwijaną "potoku aktualizacji wnioskowania" |
| Klucz dostępu + M | Otwórz listę rozwijaną (...) |

## <a name="next-steps"></a>Następne kroki

- [Włączanie dużego kontrastu lub zmienianie motywu](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Narzędzia dotyczące ułatwień dostępu w firmie Microsoft](https://www.microsoft.com/accessibility)
