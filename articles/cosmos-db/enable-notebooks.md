---
title: Włącz Notesy na koncie Azure Cosmos DB (wersja zapoznawcza)
description: Wbudowane notesy Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z poziomu portalu. W tym artykule opisano sposób włączania tej funkcji dla kont usługi Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76768018"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Włącz notesy dla kont Azure Cosmos DB (wersja zapoznawcza)

> [!IMPORTANT]
> Wbudowane notesy dla Azure Cosmos DB są obecnie dostępne w następujących regionach platformy Azure: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia Południowe Zjednoczone Królestwo, Europa Zachodnia i zachodnie stany USA 2. Aby korzystać z notesów, [Utwórz nowe konto z notesami](#enable-notebooks-in-a-new-cosmos-account) lub [Włącz Notesy na istniejącym koncie](#enable-notebooks-in-an-existing-cosmos-account) w jednym z tych regionów.

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano sposób włączania tej funkcji dla konta usługi Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Włącz Notesy na nowym koncie Cosmos
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Utwórz zasoby** > **bazy danych** > **Azure Cosmos DB**.
1. Na stronie **Tworzenie konta Azure Cosmos DB** wybierz pozycję **notesy**. 
 
    ![Opcja Wybierz notesy w Azure Cosmos DB Utwórz blok](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Wybierz pozycję **Przegląd + utwórz**. Możesz pominąć opcję **Sieć** i **Tagi** . 
1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    ![Okienko Powiadomienia w witrynie Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Strona konta usługi Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Przejdź do okienka **Eksplorator danych** . Zobaczysz teraz obszar roboczy notesy.

    ![Nowy obszar roboczy notesy Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Włącz Notesy na istniejącym koncie Cosmos
Możesz również włączyć Notesy na istniejących kontach. Ten krok należy wykonać tylko raz dla każdego konta.

1. Przejdź do okienka **Eksplorator danych** na koncie Cosmos.
1. Wybierz pozycję **Włącz notesy**.

    ![Utwórz nowy obszar roboczy notesy w Eksplorator danych](media/enable-notebooks/enable-notebooks-workspace.png)
1. Spowoduje to wyświetlenie monitu o utworzenie nowego obszaru roboczego notesów. Wybierz pozycję **Ukończ instalację.**
1. Twoje konto ma teraz możliwość korzystania z notesów!

## <a name="create-and-run-your-first-notebook"></a>Tworzenie i uruchamianie pierwszego notesu

Aby sprawdzić, czy możesz używać notesów, wybierz jeden z notesów w obszarze przykładowe notesy. Spowoduje to zapisanie kopii notesu w obszarze roboczym i otwarcie go.

W tym przykładzie użyjemy **GettingStarted. ipynb**. 

![Wyświetlanie notesu GettingStarted. ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Aby uruchomić Notes:
1. Wybierz pierwszą komórkę kodu zawierającą kod języka Python. 
1. Wybierz pozycję **Uruchom** , aby uruchomić komórkę. Możesz również użyć **klawiszy SHIFT + ENTER** , aby uruchomić komórkę.
1. Odśwież okienko zasobów, aby wyświetlić bazę danych i kontener, które zostały utworzone.

    ![Uruchamianie notesu wprowadzającego](media/enable-notebooks/run-first-notebook-cell.png)

Możesz również wybrać opcję **Nowy Notes** , aby utworzyć nowy Notes lub przekazać istniejący plik notesu (. ipynb), wybierając opcję **Przekaż plik** z menu **Moje notesy** . 

![Utwórz lub Przekaż nowy Notes](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Następne kroki

- Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
