---
title: 'Samouczek: wprowadzenie Eksplorowanie Synapse Knowledge Center'
description: W tym samouczku dowiesz się, jak korzystać z centrum wiedzy Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 47b8c45e22569cc758d00fb8534f409ecebf58ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299935"
---
# <a name="explore-the-synapse-knowledge-center"></a>Poznaj centrum wiedzy Synapse

W tym samouczku dowiesz się, jak korzystać z centrum wiedzy Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Przechodzenie do centrum wiedzy

Istnieją dwa sposoby znajdowania centrum wiedzy w programie Synapse Studio:

  1. W centrum Narzędzia główne w obszarze przydatne linki kliknij pierwszy link o nazwie **centrum wiedzy**.
  2. Na pasku menu u góry kliknij pozycję **?** a następnie  **centrum merytoryczne**.

Wybierz jedną z metod i Otwórz **centrum merytoryczne**.

## <a name="overview"></a>Omówienie

**Centrum wiedzy** umożliwia wykonywanie następujących czynności:
* **Użyj przykładów natychmiastowych**. Ta opcja jest zoptymalizowana pod kątem wyświetlania analiz w działaniu tak szybko, jak to możliwe. Jeśli potrzebujesz szybkiego przykładu działania Synapse, wybierz tę opcję.
* **Dostępna Przykładowa przeglądarka**. Ta opcja umożliwia łączenie przykładowych zestawów danych i Dodawanie przykładowego kodu w postaci skryptów SQL, notesów i potoków.
* **Samouczek Synapse Studio**. Ta opcja umożliwia zapoznanie się z krótkim przewodnikiem dotyczącym podstawowych części programu Synapse Studio. Jest to przydatne, jeśli wcześniej nie korzystano z programu Synapse Studio.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>Eksplorowanie magazynu obiektów BLOB przy użyciu programu SQL na żądanie

1. **Centrum merytoryczne**, kliknij pozycję **Użyj przykładów natychmiast**
1. Wybieranie **danych zapytania przy użyciu języka SQL** 
1. Kliknij pozycję **Użyj przykładów natychmiast**
1. Spowoduje to utworzenie nowego skryptu SQL.
1. Przewiń do pierwszego zapytania (wiersze 28 do 32) i wybierz tekst zapytania
1. Kliknij przycisk Uruchom. Zostanie uruchomiony wybrany tekst.

## <a name="loading-more-nyc-taxi-data"></a>Ładowanie większej ilości danych NYC taksówki
1. **Centrum wiedzy**, kliknij przycisk **Przeglądaj dostępne przykłady** 
1. Wybierz kartę **skrypty SQL** u góry
1. Wybierz pozycję **Załaduj zestaw danych Taxicab New York**
1. W obszarze **dane wejściowe**wybierz **pozycję Wybierz istniejącą pulę** i wybierz pozycję **SQLDB1**
1. Kliknij pozycję **Otwórz skrypt**
1. Zostanie wyświetlony nowy skrypt SQL.
1. Kliknij przycisk **Uruchom**
1. Spowoduje to utworzenie kilku tabel dla wszystkich danych z NYC taksówki i załadowanie ich przy użyciu polecenia COPY T-SQL.

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
