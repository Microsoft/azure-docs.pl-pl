---
title: Publikowanie notesów w galerii notesów Azure Cosmos DB
description: Dowiedz się, jak pobierać notesy z publicznej galerii, edytować je i publikować własne notesy w galerii.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: c7c910c7162bde2209b07eb4f1d533328bae08c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693213"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publikowanie notesów w galerii notesów Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB wbudowane notesy Jupyter są bezpośrednio zintegrowane z kontami Azure Cosmos DB w Azure Portal. Korzystając z tych notesów, można analizować i wizualizować dane z Azure Portal. Wbudowane notesy dla Azure Cosmos DB są obecnie dostępne w [29 regionach](#supported-regions). Aby korzystać z notesów, [Utwórz nowe konto Cosmos](#create-a-new-cosmos-account) lub [Włącz Notesy na istniejącym koncie](#enable-notebooks-in-an-existing-cosmos-account) w jednym z tych regionów.

Środowisko notesu w Azure Portal zawiera kilka przykładów opublikowanych przez zespół Azure Cosmos DB. Zawiera również publiczną galerię, w której można publikować i udostępniać własne notesy. Po opublikowaniu notesu w galerii jest on dostępny dla wszystkich Azure Cosmos DB użytkowników do wyświetlania i używania. W tym artykule dowiesz się, jak używać notesów z publicznej galerii i publikować Notes w galerii.

## <a name="download-a-notebook-from-the-gallery"></a>Pobierz Notes z galerii

Wykonaj następujące kroki, aby wyświetlić i pobrać notesy z galerii do obszaru roboczego notesy:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do konta Azure Cosmos DB z włączonym środowiskiem notesu.

1. Przejdź do   >  karty galerii **notesów** Eksplorator danych  >  **Galeria**  >  **publiczna** .

1. Zaakceptuj [kod postępowania](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  przed wyświetleniem lub opublikowaniem notesów w galerii. Kod postępowania jest rejestrowany dla poszczególnych użytkowników na poziomie subskrypcji. Po przełączeniu do innej subskrypcji musisz ją zaakceptować ponownie przed uzyskaniem dostępu do galerii. Aby zaakceptować kod postępowania, zaznacz pole wyboru i **Kontynuuj**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Przejdź do publicznej galerii notesów i zaakceptuj kod postępowania.":::

1. Następnie możesz dodać określony Notes do karty Ulubione lub pobrać go. Podczas pobierania notesu jest on kopiowany do obszaru roboczego notesy, w którym można go uruchomić lub edytować.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Pobierz Notes z galerii do obszaru roboczego.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publikowanie notesu w galerii

Podczas tworzenia własnych notesów lub edytowania istniejących notesów w celu dopasowania do innego scenariusza warto opublikować je w galerii. Po opublikowaniu notesu w galerii inni użytkownicy będą mogli uzyskać do niego dostęp. Możesz zapoznać się z [galerią przykładów notesu](https://cosmos.azure.com/gallery.html) , aby wyświetlić aktualnie dostępne notesy.

Aby opublikować Notes, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do konta Azure Cosmos DB z włączonym środowiskiem notesu.

1. Przejdź do karty **Eksplorator danych**  >  **notesy**  >  **Moje notesy** .

1. Przejdź do notesu, który chcesz opublikować. Wybierz przycisk **Zapisz** na pasku poleceń, a następnie wybierz pozycję **Publikuj do galerii**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Wybierz Notes do opublikowania w galerii.":::

   Możesz również znaleźć opcję **Publikuj w galerii** , wybierając pozycję.. **.** obok nazwy notesu:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Wybierz Notes do opublikowania w galerii.":::

1. Wypełnij formularz **Publikuj w galerii** , podając następujące informacje:

   * **Nazwa:** Przyjazna nazwa identyfikująca Notes.
   * **Opis:**  Krótki opis działania Twojego notesu.
   * **Tagi:** Tagi są opcjonalne i służą do filtrowania wyników podczas wyszukiwania według słowa kluczowego.
   * **Obraz okładki:** Obraz używany na stronie okładki po opublikowaniu notesu. Można wybrać jedną z następujących opcji:

     * **Obraz niestandardowy** — możesz przekazać obraz z komputera. Wybierz plik obrazu o współczynniku proporcji 256x144.
     * **URL** — Podaj publicznie dostępny adres URL, pod którym znajduje się obraz.
     * **Zrób zrzut ekranu** — zrzut ekranu przedstawiający otwarty Notes jest automatycznie pobierany i przekazywany do wersji zapoznawczej.
     * **Użyj pierwszego wyświetlenia danych wyjściowych** — dane wyjściowe pierwszej komórki, która ma wyświetlane dane wyjściowe. Komórki, w przypadku których wyświetlane są tylko dane dotyczące promocji/tekstu, nie są traktowane jako wyświetlanie danych wyjściowych.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Wypełnij formularz Publikuj w galerii.":::

   > [!NOTE]
   > Jeśli używasz opcji **Publikuj do galerii** z **...** obok nazwy notesu nie zobaczysz wszystkich opcji **obrazu okładki** . Dzieje się tak, ponieważ Notes może nie być otwarty i Azure Cosmos DB nie będzie miał dostępu do wykonania zrzutu ekranu lub uzyskania dostępu do pierwszego wyświetlanego danych wyjściowych.

1. Sprawdź, czy wersja zapoznawcza jest dobra, a następnie wybierz pozycję **Publikuj**. Po opublikowaniu ten Notes zostanie wyświetlony w publicznej galerii notesów Azure Cosmos DB. Przed opublikowaniem upewnij się, że zostały usunięte poufne dane lub dane wyjściowe. Zawartość notesu jest skanowana pod kątem naruszenia zasad firmy Microsoft przed opublikowaniem. Ten proces zazwyczaj trwa kilka sekund. Jeśli zostaną znalezione jakiekolwiek naruszenia, na karcie powiadomienia zostanie wyświetlony komunikat. Notes nie zostanie opublikowany, jeśli wykryje jakiekolwiek naruszenie, Usuń naruszony tekst i opublikuj go ponownie.

   > [!NOTE]
   > Po opublikowaniu notesów w publicznej galerii wszyscy użytkownicy Azure Cosmos DB mogą je wyświetlić. Dostęp nie jest ograniczony do na subskrypcję lub na poziom organizacji.

1. Po opublikowaniu notesu w galerii zobaczysz go na karcie **Moje opublikowane prace** . Możesz również usuwać i usuwać notesy opublikowane z publicznej galerii.

1. Możesz również zgłosić Notes, który narusza Kodeks postępowania. Jeśli zostanie wykryte naruszenie, Cosmos DB automatycznie usunie Notes z galerii. Jeśli Notes zostanie usunięty, użytkownicy będą mogli go zobaczyć na karcie **Moje opublikowane działania** z usuniętą notatką. Aby zgłosić Notes, przejdź do   >  karty galerii **notesów**  >    >  **publicznych** Galeria Eksplorator danych. Otwórz Notes, który chcesz zgłosić, umieść kursor na przycisku **Pomoc** w prawym górnym rogu i wybierz pozycję **Zgłoś nadużycie**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Zgłoś Notes, który narusza Kodeks postępowania.":::

## <a name="next-steps"></a>Następne kroki

* Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
* [Korzystanie z funkcji i poleceń notesu języka Python](use-python-notebook-features-and-commands.md)
* [Korzystanie z funkcji i poleceń notesu języka C#](use-csharp-notebook-features-and-commands.md)
* [Importowanie notesów z repozytorium GitHub](import-github-notebooks.md)
