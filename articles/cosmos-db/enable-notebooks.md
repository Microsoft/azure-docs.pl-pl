---
title: Włącz Notesy na koncie Azure Cosmos DB (wersja zapoznawcza)
description: Wbudowane notesy Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z poziomu portalu. W tym artykule opisano sposób włączania tej funkcji dla kont usługi Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 7b52a066f80b686a0e424d8f63d520d46691a72a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187820"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Włącz notesy dla kont Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Wbudowane notesy dla Azure Cosmos DB są obecnie dostępne w następujących regionach platformy Azure: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia Południowe Zjednoczone Królestwo, Europa Zachodnia i zachodnie stany USA 2. Aby korzystać z notesów, [Utwórz nowe konto z notesami](#enable-notebooks-in-a-new-cosmos-account) lub [Włącz Notesy na istniejącym koncie](#enable-notebooks-in-an-existing-cosmos-account) w jednym z tych regionów.

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano sposób włączania tej funkcji dla konta usługi Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Włącz Notesy na nowym koncie Cosmos

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Utwórz zasoby**  >  **bazy danych**  >  **Azure Cosmos DB**.
1. Na stronie **Tworzenie konta Azure Cosmos DB** wybierz pozycję **notesy**. 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Opcja Wybierz notesy w Azure Cosmos DB Utwórz blok":::

1. Wybierz pozycję **Przeglądanie + tworzenie**. Możesz pominąć opcję **Sieć** i **Tagi** . 
1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Okienko Powiadomienia w witrynie Azure Portal":::

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Strona konta usługi Azure Cosmos DB":::

1. Przejdź do okienka **Eksplorator danych** . Zobaczysz teraz obszar roboczy notesy.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Nowy obszar roboczy notesy Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Włącz Notesy na istniejącym koncie Cosmos

Możesz również włączyć Notesy na istniejących kontach. Ten krok należy wykonać tylko raz dla każdego konta.

1. Przejdź do okienka **Eksplorator danych** na koncie Cosmos.
1. Wybierz pozycję **Włącz notesy**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Utwórz nowy obszar roboczy notesy w Eksplorator danych":::

1. Spowoduje to wyświetlenie monitu o utworzenie nowego obszaru roboczego notesów. Wybierz pozycję **Ukończ instalację.**
1. Twoje konto ma teraz możliwość korzystania z notesów!

## <a name="create-and-run-your-first-notebook"></a>Tworzenie i uruchamianie pierwszego notesu

Aby sprawdzić, czy możesz używać notesów, wybierz jeden z notesów w obszarze przykładowe notesy. Spowoduje to zapisanie kopii notesu w obszarze roboczym i otwarcie go.

W tym przykładzie użyjemy **GettingStarted. ipynb**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Wyświetlanie notesu GettingStarted. ipynb":::

Aby uruchomić Notes:
1. Wybierz pierwszą komórkę kodu zawierającą kod języka Python. 
1. Wybierz pozycję **Uruchom** , aby uruchomić komórkę. Możesz również użyć **klawiszy SHIFT + ENTER** , aby uruchomić komórkę.
1. Odśwież okienko zasobów, aby wyświetlić bazę danych i kontener, które zostały utworzone.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Uruchamianie notesu wprowadzającego":::

Możesz również wybrać opcję **Nowy Notes** , aby utworzyć nowy Notes lub przekazać istniejący plik notesu (. ipynb), wybierając opcję **Przekaż plik** z menu **Moje notesy** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Utwórz lub Przekaż nowy Notes":::

## <a name="next-steps"></a>Następne kroki

- Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
