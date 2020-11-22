---
title: 'Samouczek: wprowadzenie Eksplorowanie Synapse Knowledge Center'
description: W tym samouczku dowiesz się, jak korzystać z centrum wiedzy Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 0314f5e55a476cd1cd1f8a31bd918e0ebb64c75f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254242"
---
# <a name="explore-the-synapse-knowledge-center"></a>Poznaj centrum wiedzy Synapse

W tym samouczku dowiesz się, jak korzystać z centrum wiedzy Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Przechodzenie do centrum wiedzy

Istnieją dwa sposoby znajdowania centrum wiedzy w programie Synapse Studio:

  1. W centrum Narzędzia główne w prawym górnym rogu strony kliknij pozycję **nauka**.
  2. Na pasku menu u góry kliknij pozycję **?** a następnie  **centrum merytoryczne**.

Wybierz jedną z metod i Otwórz **centrum merytoryczne**.

## <a name="overview"></a>Omówienie

**Centrum wiedzy** umożliwia wykonywanie następujących czynności:
* **Użyj przykładów natychmiastowych**. Jeśli potrzebujesz szybkiego przykładu działania Synapse, wybierz tę opcję.
* **Przeglądaj Galerię**. Ta opcja umożliwia łączenie przykładowych zestawów danych i Dodawanie przykładowego kodu w postaci skryptów SQL, notesów i potoków.
* **Samouczek Synapse Studio**. Ta opcja umożliwia zapoznanie się z krótkim przewodnikiem dotyczącym podstawowych części programu Synapse Studio. Jest to przydatne, jeśli wcześniej nie korzystano z programu Synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Eksplorowanie magazynu obiektów BLOB za pomocą puli SQL bezserwerowej

1. Przejdź do **centrum wiedzy**, a następnie kliknij pozycję **Użyj przykładów natychmiast**
1. Wybieranie **danych zapytania przy użyciu języka SQL** 
1. Kliknij pozycję **Użyj przykładów natychmiast**
1. Spowoduje to utworzenie nowego skryptu SQL.
1. Przewiń do pierwszego zapytania (wiersze 28 do 32) i wybierz tekst zapytania
1. Kliknij przycisk Uruchom. Zostanie uruchomiony wybrany tekst.

## <a name="loading-more-nyc-taxi-data"></a>Ładowanie większej ilości danych NYC taksówki
1. Przejdź do **centrum wiedzy**, a następnie kliknij pozycję **Przeglądaj Galerię** 
1. Wybierz kartę **skrypty SQL** u góry
1. Wybierz pozycję **Załaduj zestaw danych Taxicab New York**
1. W obszarze **dane wejściowe** wybierz **pozycję Wybierz istniejącą pulę** i wybierz pozycję **SQLDB1**
1. Kliknij pozycję **Otwórz skrypt**
1. Zostanie wyświetlony nowy skrypt SQL.
1. Kliknij przycisk **Uruchom**
1. Spowoduje to utworzenie kilku tabel dla wszystkich danych z NYC taksówki i załadowanie ich przy użyciu polecenia COPY T-SQL.

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Azure Synapse Analytics](get-started.md)
* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z bezserwerowej puli SQL](quickstart-sql-on-demand.md)
