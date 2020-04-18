---
title: Zmienianie transformacji wierszy w przepływie danych mapowania
description: Jak zaktualizować obiekt docelowy bazy danych przy użyciu transformacji wiersza alter w przepływie danych mapowania
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 77c58bb8dfa7d21b108d2aa63e90142f66877fb7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606523"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Zmienianie transformacji wierszy w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja alter wiersza służy do ustawiania zasad wstawiania, usuwania, aktualizowania i wprowadzania aktualizacji w wierszach. Jako wyrażenia można dodawać warunki typu jeden do wielu. Te warunki powinny być określone w kolejności priorytetu, jak każdy wiersz zostanie oznaczony zasadą odpowiadającą pierwszemu wyrażeniu dopasowania. Każdy z tych warunków może spowodować wstawienie, zaktualizowanie, usunięcie lub wertę wiersza (lub wierszy). Alter Row może tworzyć zarówno DDL, jak & akcje DML w bazie danych.

![Zmienianie ustawień wiersza](media/data-flow/alter-row1.png "Zmienianie ustawień wiersza")

Przekształcenia alter rowu będzie działać tylko w bazie danych lub ujścia usługi CosmosDB w przepływie danych. Akcje przypisywane do wierszy (wstawianie, aktualizowanie, usuwanie, upsert) nie będą występowane podczas sesji debugowania. Uruchom działanie Wykonaj przepływ danych w potoku, aby uchwalić zasady alter wiersz w tabelach bazy danych.

## <a name="specify-a-default-row-policy"></a>Określanie domyślnej zasady wiersza

Utwórz transformację alter wiersza i określ zasadę wiersza z `true()`warunkiem . Każdy wiersz, który nie pasuje do żadnego z wcześniej zdefiniowanych wyrażeń, zostanie oznaczony dla określonych zasad wiersza. Domyślnie każdy wiersz, który nie pasuje do żadnego `Insert`wyrażenia warunkowego, zostanie oznaczony jako .

![Zmienianie zasad wiersza](media/data-flow/alter-row4.png "Zmienianie zasad wiersza")

> [!NOTE]
> Aby oznaczyć wszystkie wiersze jedną zasadą, można utworzyć warunek `true()`dla tej zasady i określić warunek jako .

## <a name="view-policies-in-data-preview"></a>Wyświetlanie zasad w podglądzie danych

Tryb [debugowania](concepts-data-flow-debug-mode.md) służy do wyświetlania wyników zasad alter rowu w okienku podglądu danych. Podgląd danych transformacji alter wiersz nie będzie produkować ddl lub DML akcji względem obiektu docelowego.

![Zmienianie zasad wierszy](media/data-flow/alter-row3.png "Zmień zasady wiersza")

Każda zasada alter rowu jest reprezentowana przez ikonę, która wskazuje, czy wystąpi akcja wstawiania, aktualizacji, upsert lub usuniętej. Górny nagłówek pokazuje, ile wierszy ma wpływ na każdą zasadę w wersji zapoznawczej.

## <a name="allow-alter-row-policies-in-sink"></a>Zezwalaj na zmienianie zasad wierszy w zlewie

Aby zasady alter wiersz do pracy, strumień danych musi zapisywać do bazy danych lub usługi Cosmos sink. Na karcie **Ustawienia** w zlewie włącz, które zasady alter wierszy są dozwolone dla tego ujścia.

![Zmień zlew wiersza](media/data-flow/alter-row2.png "Zlew z wierszem Alter")

 Domyślnym zachowaniem jest zezwalanie tylko na wstawia. Aby zezwolić na aktualizacje, upserts lub usuwa, zaznacz pole w zlewie odpowiadające tego warunku. Jeśli aktualizacje, upserts lub deletes są włączone, należy określić, które kolumny kluczy w ujściu do dopasowania.

> [!NOTE]
> Jeśli wstawia, aktualizuje lub upserts zmodyfikować schemat tabeli docelowej w zlewie, przepływ danych zakończy się niepowodzeniem. Aby zmodyfikować schemat docelowy w bazie danych, wybierz pozycję **Odtwórz tabelę** jako akcję tabeli. Spowoduje to upuszczenie i ponownetworzenie tabeli z nową definicją schematu.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład jest alter `CleanData` transformacji wiersza `SpecifyUpsertConditions` o nazwie, która ma przychodzące strumienia i tworzy trzy warunki wiersza alter. W poprzedniej transformacji kolumna `alterRowCondition` o nazwie jest obliczana, która określa, czy wiersz jest wstawiany, aktualizowany lub usuwany w bazie danych. Jeśli wartość kolumny ma wartość ciągu, która pasuje do reguły alter wiersz, jest przypisana ta zasada.

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Zmienianie przykładu wiersza](media/data-flow/alter-row4.png "Zmienianie przykładu wiersza")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Następne kroki

Po przekształceniu alter rowu można [zatopić dane w docelowym magazynie danych](data-flow-sink.md).
