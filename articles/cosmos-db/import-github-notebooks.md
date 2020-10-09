---
title: Importowanie i uruchamianie notesów z repozytorium GitHub do Azure Cosmos DB
description: Dowiedz się, jak nawiązać połączenie z usługą GitHub i zaimportować notesy z repozytorium GitHub do konta usługi Azure Cosmos. Po zaimportowaniu można je uruchomić, edytować i zapisać zmiany z powrotem w serwisie GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: d85f020152fa3cadb1d437c125d327f5e895e14e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262892"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importuj notesy z repozytorium GitHub do Azure Cosmos DB

Po [włączeniu obsługi notesu](enable-notebooks.md) dla kont usługi Azure Cosmos można utworzyć nowe notesy, przekazać nowe notesy z komputera lokalnego lub zaimportować istniejące notesy z kont w usłudze GitHub. W tym artykule przedstawiono sposób łączenia obszaru roboczego notesów z usługą GitHub i importowania notesów z repozytorium GitHub do konta usługi Azure Cosmos. Po zaimportowaniu można je uruchomić, wprowadzić zmiany i zapisać zmiany z powrotem w usłudze GitHub.

## <a name="get-notebooks-from-github"></a>Pobieranie notesów z usługi GitHub

Możesz połączyć się ze swoimi repozytoriami usługi GitHub lub innymi publicznymi repozytoriami usługi GitHub, aby odczytywać, tworzyć i udostępniać notesy w Azure Cosmos DB. Wykonaj następujące kroki, aby nawiązać połączenie z kontem usługi GitHub:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz kartę **Eksplorator danych** . Na tej karcie zostaną wyświetlone wszystkie istniejące bazy danych, kontenery i notesy.

1. Wybierz element menu **Połącz z usługą GitHub** .

1. Zostanie otwarta karta, w której można wybrać opcję połączenia tylko z **repozytoriami publicznymi** lub **repozytoriami publicznymi i prywatnymi**.  Po wybraniu wymaganej opcji wybierz opcję **Autoryzuj dostęp**. Aby Azure Cosmos DB uzyskać dostęp do repozytoriów na koncie usługi GitHub, wymagana jest autoryzacja.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autoryzuj Azure Cosmos DB, aby uzyskać dostęp do repozytoriów usługi GitHub":::

1. Nastąpi przekierowanie do strony sieci Web "github.com", na której można potwierdzić autoryzację. Wybierz przycisk **Autoryzuj AzureCosmosDBNotebooks** i wprowadź hasło do konta usługi GitHub w wierszu polecenia.

1. Po pomyślnym zaakceptowaniu autoryzacji następuje powrót do konta usługi Azure Cosmos. Następnie można zobaczyć wszystkie repozytoria publiczne/prywatne z konta usługi GitHub. Można wybrać repozytorium z listy dostępne lub dodać repozytorium bezpośrednio przy użyciu adresu URL.

1. Po wybraniu wymaganego repozytorium wpis repozytorium jest przenoszony z sekcji **przypięte repozytoria** do **przypiętych repozytoriów** . W razie potrzeby można również wybrać konkretną gałąź tego repozytorium, z której mają zostać zaimportowane notesy.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Autoryzuj Azure Cosmos DB, aby uzyskać dostęp do repozytoriów usługi GitHub":::

1. Wybierz **przycisk OK** , aby ukończyć operację importowania. Wszystkie notesy dostępne w wybranej gałęzi repozytorium są importowane do konta usługi Azure Cosmos.

Po zintegrowaniu usługi z kontem w usłudze GitHub zobaczysz listę repozytoriów i notesów na koncie usługi Azure Cosmos. Ta instrukcja ma wartość true, nawet jeśli wielu użytkowników loguje się do konta Azure Cosmos DB i doda własne konta. Innymi słowy, wielu użytkowników może korzystać z tego samego konta usługi Azure Cosmos, aby połączyć obszar roboczy Notes z usługą GitHub. Jednak każdy użytkownik widzi tylko listę repozytoriów i notesów, które zostały zaimportowane. Notesy importowane przez inne osoby nie są widoczne dla użytkownika.

Aby rozłączyć konto usługi GitHub z obszaru roboczego notesy, Otwórz kartę **Eksplorator danych** , wybierz pozycję `…` dalej w przypadku **repozytoriów GitHub** i wybierz pozycję **Rozłącz z serwisu GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Edytowanie notesu i wypychanie zmian do usługi GitHub

Można edytować istniejący Notes lub dodać nowy Notes do repozytorium i zapisać zmiany z powrotem w serwisie GitHub.

Po edycji istniejącego notesu wybierz pozycję **Zapisz**. Zostanie otwarte okno dialogowe, w którym można wprowadzić komunikat zatwierdzenia dla wprowadzonych zmian. Wybierz pozycję **Zatwierdź** , a Notes w serwisie GitHub został zaktualizowany. Możesz sprawdzić poprawność aktualizacji, logując się do konta usługi GitHub i sprawdzając historię zatwierdzeń.

W regularnym przepływie usługi GitHub po zatwierdzeniu zmian zwykle wypychane zostaną zmiany w zdalnym. Jednak w tym przypadku opcja zatwierdzania służy do przeznaczenie "przemieszczanie, zatwierdzanie i wypychanie" aktualizacji do usługi GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Autoryzuj Azure Cosmos DB, aby uzyskać dostęp do repozytoriów usługi GitHub":::

## <a name="next-steps"></a>Następne kroki

* Poznaj zalety [Azure Cosmos DB notesów Jupyter.](cosmosdb-jupyter-notebooks.md)

