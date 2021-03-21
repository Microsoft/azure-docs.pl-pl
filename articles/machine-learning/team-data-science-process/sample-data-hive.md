---
title: Przykładowe dane w tabelach Hive usługi Azure HDInsight — proces nauki danych zespołu
description: W dół — przykładowe dane przechowywane w tabelach Hive usługi Azure HDInsight przy użyciu zapytań programu Hive w celu zmniejszenia ilości danych na potrzeby analizy.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321887"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Przykładowe dane w tabelach usługi Azure HDInsight Hive
W tym artykule opisano sposób podłączania przykładowych danych przechowywanych w tabelach Hive usługi Azure HDInsight przy użyciu zapytań programu Hive w celu zmniejszenia ich do rozmiaru w celu łatwiejszego zarządzania analizą. Obejmuje trzy popularne metody próbkowania:

* Jednolite Próbkowanie losowe
* Losowe próbkowanie według grup
* Próbkowanie Stratified

**Dlaczego warto Przykładowo dane?**
Jeśli zestaw danych, który planujesz analizować jest duży, zazwyczaj dobrym pomysłem jest Przepróbkowanie danych w celu zmniejszenia ich do mniejszej, ale reprezentatywnej i większej możliwej do zarządzania wielkości. Dalsze próbkowanie ułatwia zrozumienie, eksplorację i inżynierowanie danych. Jej rolą w procesie nauki danych zespołu jest umożliwienie szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](./index.yml).

## <a name="how-to-submit-hive-queries"></a>Jak przesłać zapytania Hive
Zapytania Hive mogą być przesyłane z konsoli Command-Line Hadoop w węźle głównym klastra usługi Hadoop.  Zaloguj się do węzła głównego klastra Hadoop, Otwórz konsolę usługi Hadoop Command-Line i prześlij do niej zapytania programu Hive. Aby uzyskać instrukcje dotyczące przesyłania zapytań Hive w konsoli Command-Line Hadoop, zobacz [Jak przesłać zapytania Hive](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Jednolite Próbkowanie losowe
Jednorodne Próbkowanie losowe oznacza, że każdy wiersz w zestawie danych ma równe szanse na próbkowanie. Można ją zaimplementować przez dodanie dodatkowego pola Rand () do zestawu danych w wewnętrznej kwerendzie "Select", a w zewnętrznym elemencie "Select" zapytania, który ma ten warunek dla tego losowego pola.

Oto przykładowe zapytanie:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

W tym miejscu `<sample rate, 0-1>` określa proporcje rekordów, które użytkownicy chcą próbkować.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Losowe próbkowanie według grup
Podczas próbkowania kategorii dane można uwzględnić lub wykluczyć wszystkie wystąpienia dla pewnej wartości zmiennej kategorii. Ten rodzaj próbkowania jest nazywany "próbkowanie przez grupę". Na przykład jeśli masz zmienną kategorii "*State*", która ma wartości, takich jak NY, ma, CA, NJ i PA, chcesz, aby rekordy z każdego stanu były razem, niezależnie od tego, czy są one próbkowane.

Oto przykładowe zapytanie zawierające próbki według grupy:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Próbkowanie Stratified
Próbkowanie losowe jest stratified w odniesieniu do zmiennej kategorii, gdy uzyskane próbki zawierają wartości kategorii, które są obecne w tym samym współczynniku, co w populacji nadrzędnej. Korzystając z tego samego przykładu, Załóżmy, że dane mają następujące obserwacje według stanów: NJ ma 100 obserwacji, NY ma 60 uwagi, a WA ma 300 obserwacje. Jeśli określisz częstotliwość próbkowania stratified do 0,5, uzyskana próbka powinna mieć około 50, 30 i 150 obserwacje odpowiednio wartości NJ, NY i WA.

Oto przykładowe zapytanie:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

Aby uzyskać informacje na temat bardziej zaawansowanych metod próbkowania, które są dostępne w usłudze Hive, zobacz [LanguageManual próbkowanie](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).