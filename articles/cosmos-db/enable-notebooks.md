---
title: Włącz Notesy na koncie Azure Cosmos DB (wersja zapoznawcza)
description: Wbudowane notesy Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z poziomu portalu. W tym artykule opisano sposób włączania tej funkcji dla kont usługi Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/09/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: b8ce745a0a0db96e7f6b9e3a0140d46364246e4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379611"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Włącz notesy dla kont Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Wbudowane notesy dla Azure Cosmos DB są obecnie dostępne w [29 regionach](#supported-regions). Aby korzystać z notesów, [Utwórz nowe konto Cosmos](#create-a-new-cosmos-account) lub [Włącz Notesy na istniejącym koncie](#enable-notebooks-in-an-existing-cosmos-account) w jednym z tych regionów. 

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano sposób włączania tej funkcji dla konta usługi Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Utwórz nowe konto Cosmos
Od 10 lutego 2021 nowe konta usługi Azure Cosmos utworzone w jednym z [obsługiwanych regionów](#supported-regions) będą automatycznie obsługiwały notesy. Nie jest wymagana dodatkowa konfiguracja umożliwiająca korzystanie z notesów. Aby utworzyć nowe konto, wykonaj następujące instrukcje:
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Wybierz pozycję **Utwórz zasoby**  >  **bazy danych**  >  **Azure Cosmos DB**.
1. Wprowadź ustawienia podstawowe dla konta. 
 
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Strona nowego konta usługi Azure Cosmos DB":::

1. Wybierz pozycję **Przejrzyj i utwórz**. Możesz pominąć opcję **Sieć** i **Tagi** . 
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

## <a name="supported-regions"></a>Obsługiwane regiony
Wbudowane notesy dla Azure Cosmos DB są obecnie dostępne w 29 regionach świadczenia usługi Azure. Nowe konta usługi Azure Cosmos utworzone w tych regionach będą miały automatycznie włączone notesy. Notesy są bezpłatne dla Twojego konta. 

- Australia Środkowa
- Australia Środkowa 2
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Kanada Wschodnia
- Indie Środkowe
- Central US
- East US
- Wschodnie stany USA 2
- Francja Środkowa
- Francja Południowa
- Niemcy Północne
- Niemcy Środkowo-Zachodnie
- Japonia Zachodnia
- Korea Południowa
- Północno-środkowe stany USA
- Europa Północna
- Południowo-środkowe stany USA
- Southeast Asia
- Szwajcaria Północna
- Środkowy Zjednoczone Emiraty Arabskie
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Zachodnio-środkowe stany USA
- West Europe
- Indie Zachodnie
- Zachodnie stany USA 2

## <a name="next-steps"></a>Następne kroki

* Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
* [Eksploruj galerię przykładów notesu](https://cosmos.azure.com/gallery.html)
* [Korzystanie z funkcji i poleceń notesu języka Python](use-python-notebook-features-and-commands.md)
* [Korzystanie z funkcji i poleceń notesu języka C#](use-csharp-notebook-features-and-commands.md)
* [Importowanie notesów z repozytorium GitHub](import-github-notebooks.md)


