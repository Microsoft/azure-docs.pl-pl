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
ms.date: 04/04/2021
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307898"
---
# <a name="explore-the-synapse-knowledge-center"></a>Poznaj centrum wiedzy Synapse

W tym samouczku dowiesz się, jak korzystać z centrum wiedzy Synapse Studio.

## <a name="introduction-to-the-knowledge-center"></a>Wprowadzenie do centrum wiedzy

Istnieją dwa sposoby znajdowania centrum wiedzy w programie Synapse Studio:

  1. W centrum Narzędzia główne w prawym górnym rogu strony kliknij pozycję **nauka**.
  2. Na pasku menu u góry kliknij pozycję **?** a następnie **centrum merytoryczne**.

Wybierz jedną z metod i Otwórz **centrum merytoryczne**.

Gdy będzie widoczne, zobaczysz, że **centrum wiedzy** umożliwia wykonywanie trzech czynności:
* **Użyj przykładów natychmiastowych**. Jeśli potrzebujesz szybkiego przykładu działania Synapse, wybierz tę opcję.
* **Przeglądaj Galerię**. Ta opcja umożliwia łączenie przykładowych zestawów danych i Dodawanie przykładowego kodu w postaci skryptów SQL, notesów i potoków.
* **Samouczek Synapse Studio**. Ta opcja umożliwia zapoznanie się z krótkim przewodnikiem dotyczącym podstawowych części programu Synapse Studio. Jest to przydatne, jeśli wcześniej nie korzystano z programu Synapse Studio.

## <a name="exploring-use-samples-immediately"></a>Eksplorowanie: natychmiastowe korzystanie z przykładów

W tej sekcji znajdują się trzy elementy:
* Eksplorowanie przykładowych danych za pomocą platformy Spark
* Wykonywanie zapytań dotyczących danych przy użyciu języka SQL
* Tworzenie tabeli zewnętrznej przy użyciu języka SQL

1. W **centrum wiedzy** kliknij pozycję **Użyj przykładów natychmiast**.
1. Wybierz pozycję **kwerenda danych przy użyciu języka SQL**.
1. Kliknij pozycję **Użyj przykładu**.
1. Zostanie otwarty nowy przykładowy skrypt SQL.
1. Przewiń do pierwszego zapytania (wiersze 28 do 32) i wybierz tekst zapytania.
1. Kliknij przycisk Uruchom. Zostanie uruchomiony tylko kod, który został wybrany.

## <a name="loading-more-nyc-taxi-data"></a>Ładowanie większej ilości danych NYC taksówki

1. Przejdź do **centrum wiedzy**, a następnie kliknij pozycję **Przeglądaj Galerię**.
1. Wybierz kartę **skrypty SQL** w górnej części strony.
1. Wybierz pozycję Załaduj próbkę pozyskiwania danych **w Nowym Jorku Taxicab** , kliknij przycisk **Kontynuuj**.
1. W obszarze **Pula SQL** wybierz **pozycję Wybierz istniejącą pulę** i wybierz pozycję **SQLPOOL1**, a następnie wybierz utworzoną wcześniej bazę danych **SQLPOOL1** .
1. Kliknij pozycję **Otwórz skrypt**.
1. Zostanie otwarty nowy przykładowy skrypt SQL.
1. Kliknij przycisk **Uruchom**
1. Spowoduje to utworzenie kilku tabel dla wszystkich danych z NYC taksówki i załadowanie ich przy użyciu polecenia COPY T-SQL. Jeśli te tabele zostały utworzone w poprzednich krokach szybkiego startu, wybierz i wykonaj tylko kod w celu utworzenia i SKOPIOWAnia tabel, które nie istnieją.

    > [!NOTE] 
    > W przypadku korzystania z galerii przykładów dla skryptu SQL z dedykowaną pulą SQL (dawniej SQL DW) będzie można używać tylko istniejącej dedykowanej puli SQL (dawniej SQL DW).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie administratora](get-started-add-admin.md)

