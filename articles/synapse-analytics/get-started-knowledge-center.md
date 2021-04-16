---
title: 'Samouczek: rozpoczynanie pracy z Centrum wiedzy synapse'
description: Z tego samouczka dowiesz się, jak korzystać z Centrum wiedzy synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517355"
---
# <a name="explore-the-synapse-knowledge-center"></a>Eksplorowanie rozwiązania Synapse Centrum wiedzy

Z tego samouczka dowiesz się, jak używać centrum Synapse Studio Wiedzy.

## <a name="finding-to-the-knowledge-center"></a>Znajdowanie Centrum wiedzy

Istnieją dwa sposoby znajdowania Centrum wiedzy w Synapse Studio:

  1. W centrum Strona główna w prawym górnym rogu strony kliknij pozycję **Dowiedz się.**
  2. Na pasku menu u góry kliknij pozycję **?** a następnie **Centrum wiedzy**.

Wybierz jedną z metod i otwórz **Centrum wiedzy**.

## <a name="exploring-the-knowledge-center"></a>Eksplorowanie Centrum wiedzy

Gdy będzie ona widoczna, zobaczysz, że **Centrum wiedzy** można wykonać trzy czynności:
* **Natychmiast użyj przykładów.** Jeśli potrzebujesz szybkiego przykładu działania synapse, wybierz tę opcję.
* **Przeglądaj galerię**. Ta opcja umożliwia łączenie przykładowych zestawów danych i dodawanie przykładowego kodu w postaci skryptów, notesów i potoków SQL.
* **Przewodnik Synapse Studio**. Ta opcja umożliwia krótki przewodnik po podstawowych częściach Synapse Studio. Jest to przydatne, jeśli nigdy wcześniej nie Synapse Studio wcześniej.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Natychmiastowe używanie przykładów: trzy przykłady, które ułatwiają szybkie rozpoczynanie pracy

W tej sekcji znajdują się trzy elementy:
* Eksplorowanie przykładowych danych za pomocą platformy Spark
* Wykonywanie zapytań o dane przy użyciu języka SQL
* Tworzenie tabeli zewnętrznej przy użyciu języka SQL

1. Na stronie **Centrum wiedzy** kliknij pozycję **Użyj przykładów natychmiast.**
1. Wybierz pozycję **Query data with SQL (Wykonywanie zapytań o dane przy użyciu języka SQL).**
1. Kliknij **pozycję Użyj przykładu**.
1. Zostanie otwarty nowy przykładowy skrypt SQL.
1. Przewiń do pierwszego zapytania (wiersze od 28 do 32) i wybierz tekst zapytania.
1. Kliknij przycisk Uruchom. Zostanie uruchomiony tylko wybrany kod.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Galeria: zbieranie przykładowych zestawów danych i przykładowego kodu

1. Przejdź do Centrum **wiedzy i** kliknij pozycję **Przeglądaj galerię.**
1. Wybierz **kartę Skrypty SQL** u góry.
1. Wybierz **pozycję Załaduj zestaw danych New York Taxicab** Przykład pozyskiwania danych, a następnie kliknij przycisk **Kontynuuj.**
1. W **obszarze Pula SQL** wybierz pozycję Wybierz **istniejącą** pulę, wybierz pozycję **SQLPOOL1,** a następnie wybierz utworzoną wcześniej bazę danych **SQLPOOL1.**
1. Kliknij **pozycję Open Script (Otwórz skrypt).**
1. Zostanie otwarty nowy przykładowy skrypt SQL.
1. Kliknij pozycję **Uruchom.**
1. Spowoduje to utworzenie kilku tabel dla wszystkich danych dotyczących taksówek w Stanie Nowy Jork i załadowanie ich przy użyciu polecenia T-SQL COPY. Jeśli te tabele zostały utworzone w poprzednich krokach Szybkiego startu, wybierz i wykonaj tylko kod create i copy dla tabel, które nie istnieją.

    > [!NOTE] 
    > W przypadku używania przykładowej galerii dla skryptu SQL z dedykowaną pulą SQL (dawniej SQL DW) będzie można używać tylko istniejącej dedykowanej puli SQL (dawniej SQL DW).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie administratora](get-started-add-admin.md)

