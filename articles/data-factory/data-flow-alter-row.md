---
title: Zmień transformację wierszy w mapowaniu przepływu danych
description: Jak zaktualizować obiekt docelowy bazy danych przy użyciu przekształceń ALTER Row w mapowaniu przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729135"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Zmień transformację wierszy w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia ALTER Row, aby ustawić zasady wstawiania, usuwania, aktualizacji i upsert w wierszach. Możesz dodać warunki "jeden do wielu" jako wyrażenia. Warunki te należy określić w kolejności priorytetu, ponieważ każdy wiersz zostanie oznaczony przy użyciu zasad odpowiadających pierwszemu wyrażeniu. Każdy z tych warunków może spowodować Wstawianie, aktualizowanie, usuwanie lub upserted wierszy (lub wierszy). Polecenie ALTER Row może generować do bazy danych zarówno akcje DDL & DML.

![Zmień ustawienia wiersza](media/data-flow/alter-row1.png "Zmień ustawienia wiersza")

Przekształcenia ALTER Row będą działać tylko dla bazy danych lub ujścia CosmosDB w przepływie danych. Akcje przypisane do wierszy (INSERT, Update, DELETE, upsert) nie będą występować podczas sesji debugowania. Uruchamianie działania wykonywania przepływu danych w potoku w celu przeprowadzenia zasad ALTER Row w tabelach bazy danych.

## <a name="specify-a-default-row-policy"></a>Określanie domyślnych zasad wiersza

Tworzenie przekształcenia ALTER Row i Określanie zasad wiersza z warunkiem `true()`. Każdy wiersz, który nie jest zgodny z żadnym z wcześniej zdefiniowanych wyrażeń, zostanie oznaczony dla określonych zasad wiersza. Domyślnie każdy wiersz, który nie jest zgodny z żadnym wyrażeniem warunkowym, `Insert`będzie oznaczony jako.

![Zmień zasady wiersza](media/data-flow/alter-row4.png "Zmień zasady wiersza")

> [!NOTE]
> Aby oznaczyć wszystkie wiersze z jedną zasadą, można utworzyć warunek dla tych zasad i określić warunek jako `true()`.

## <a name="view-policies-in-data-preview"></a>Wyświetl zasady w podglądzie danych

Użyj [trybu debugowania](concepts-data-flow-debug-mode.md) , aby wyświetlić wyniki zasad ALTER Row w okienku Podgląd danych. Podgląd danych przekształcenia ALTER Row nie będzie generował działań DDL ani DML względem obiektu docelowego.

![Zasady ALTER Row](media/data-flow/alter-row3.png "Zasady ALTER Row")

Każda zasada ALTER Row jest reprezentowana przez ikonę, która wskazuje, czy nastąpi akcja INSERT, Update, upsert lub Deleted. Górny nagłówek pokazuje, ile wierszy ma wpływ poszczególne zasady w wersji zapoznawczej.

## <a name="allow-alter-row-policies-in-sink"></a>Zezwalaj na zasady ALTER Row w usłudze sink

Aby zasady ALTER Row działały, strumień danych musi zapisywać do bazy danych lub ujścia Cosmos. Na karcie **Ustawienia** w ujścia, Włącz, które zasady ALTER Row są dozwolone dla tego ujścia.

![Zmień ujścia wierszy](media/data-flow/alter-row2.png "Zmień ujścia wierszy")

Domyślne zachowanie zezwala tylko na operacje wstawiania. Aby zezwolić na aktualizacje, upserts lub usunąć, zaznacz pole w ujścia odpowiadające temu warunku. W przypadku, gdy są dostępne aktualizacje, upserts lub,, należy określić kolumny klucza w zlewie, według których ma być zgodny.

> [!NOTE]
> Jeśli Wstaw, aktualizacje lub upserts modyfikują schemat tabeli docelowej w ujścia, przepływ danych zakończy się niepowodzeniem. Aby zmodyfikować schemat docelowy w bazie danych, wybierz polecenie **Utwórz ponownie tabelę** jako akcję tabeli. Spowoduje to usunięcie i ponowne utworzenie tabeli przy użyciu nowej definicji schematu.

Transformacja ujścia wymaga jednego klucza lub szeregu kluczy dla unikatowej identyfikacji wiersza w docelowej bazie danych. W przypadku ujścia SQL Ustaw klucze na karcie Ustawienia ujścia. W przypadku CosmosDB należy ustawić klucz partycji w ustawieniach, a także ustawić pole systemowe CosmosDB w mapowaniu ujścia. W przypadku CosmosDB wymagane jest uwzględnienie kolumny systemowej "ID" dla aktualizacji, upserts i usunięć.

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

Poniższy przykład jest przekształceniem ALTER Row o `CleanData` nazwie, który pobiera strumień `SpecifyUpsertConditions` przychodzący i tworzy trzy warunki ALTER Row. W poprzedniej transformacji kolumna o nazwie `alterRowCondition` jest obliczana, która określa, czy wiersz został wstawiony, zaktualizowany lub usunięty z bazy danych. Jeśli wartość w kolumnie ma wartość ciągu zgodną z regułą ALTER Row, przypisywana jest ta zasada.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Przykład zmiany wiersza](media/data-flow/alter-row4.png "Przykład zmiany wiersza")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Następne kroki

Po przekształceniu zmiany wiersza możesz chcieć [zaujścia danych do docelowego magazynu danych](data-flow-sink.md).
