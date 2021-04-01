---
title: Zasady uczenia — Personalizacja
description: Ustawienia uczenia określają *Parametry* szkolenia modelu. Dwa modele tych samych danych, które są przeszkolone przy użyciu różnych ustawień uczenia, będą kończyć się różnymi.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253636"
---
# <a name="learning-policy-and-settings"></a>Zasady i ustawienia uczenia

Ustawienia uczenia określają *Parametry* szkolenia modelu. Dwa modele tych samych danych, które są przeszkolone przy użyciu różnych ustawień uczenia, będą kończyć się różnymi.

[Zasady i ustawienia nauki](how-to-settings.md#configure-rewards-for-the-feedback-loop) są ustawiane w Twoim zasobie personalizacji w Azure Portal.

## <a name="import-and-export-learning-policies"></a>Importowanie i eksportowanie zasad uczenia

Można importować i eksportować pliki zasad uczenia z Azure Portal. Ta metoda służy do zapisywania istniejących zasad, testowania ich, zastępowania i archiwizowania ich w kontroli kodu źródłowego jako artefaktów do przyszłego odwołania i inspekcji.

Dowiedz się, [jak](how-to-manage-model.md#import-a-new-learning-policy) importować i eksportować zasady uczenia w Azure Portal dla zasobu personalizowania.

## <a name="understand-learning-policy-settings"></a>Informacje o ustawieniach zasad nauki

Ustawienia zasad nauki nie są przeznaczone do zmiany. Zmień ustawienia tylko wtedy, gdy zrozumiesz, jak mają wpływ na personalizowanie. Bez tej wiedzy mogą wystąpić problemy, w tym unieważnienie modeli personalizacji.

Personalizacja używa [vowpalwabbit](https://github.com/VowpalWabbit) do uczenia i oceny zdarzeń. Zapoznaj się z [dokumentacją vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) na temat edytowania ustawień uczenia przy użyciu vowpalwabbit. Po otrzymaniu prawidłowych argumentów wiersza polecenia Zapisz polecenie do pliku o następującym formacie (Zastąp wartość właściwości argumenty pożądanym poleceniem) i Przekaż plik, aby zaimportować ustawienia uczenia w okienku **Ustawienia modelu i uczenia** w Azure Portal dla zasobu personalizowania.

Poniżej przedstawiono `.json` przykładowe zasady uczenia się.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Porównanie zasad uczenia

Można porównać, w jaki sposób różne zasady uczenia są wykonywane względem wcześniejszych danych w dziennikach personalizacji przez wykonywanie [ocen w trybie offline](concepts-offline-evaluation.md).

[Przekaż własne zasady uczenia](how-to-manage-model.md) , aby porównać je z bieżącymi zasadami nauki.

## <a name="optimize-learning-policies"></a>Optymalizowanie zasad uczenia

Personalizowanie może utworzyć zoptymalizowane zasady uczenia w [ocenie w trybie offline](how-to-offline-evaluation.md). Zoptymalizowane zasady uczenia, które mają lepsze korzyści wynikające z oceny w trybie offline, będą zwracać lepsze wyniki, gdy są używane w trybie online w programie personalizacji.

Po zoptymalizowaniu zasad uczenia można zastosować je bezpośrednio do personalizacji, aby natychmiast zastąpić bieżące zasady. Można też zapisać zoptymalizowane zasady do dalszej oceny, a następnie zdecydować, czy należy je odrzucić, zapisać lub zastosować.

## <a name="next-steps"></a>Następne kroki

* Informacje o [zdarzeniach aktywnych i nieaktywnych](concept-active-inactive-events.md).
