---
title: Jak wyszukiwać, edytować i usuwać program Project — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Translator niestandardowy zapewnia różne sposoby zarządzania projektami w wydajny sposób. Możesz utworzyć wiele projektów, przeszukiwać je na podstawie kryteriów, edytując swoje projekty. Usuwanie projektu jest również możliwe w przypadku translatora niestandardowego.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896447"
---
# <a name="search-edit-and-delete-projects"></a>Wyszukiwanie, edytowanie i usuwanie projektów

Translator niestandardowy zapewnia wiele sposobów zarządzania projektami w wydajny sposób. Możesz tworzyć wiele projektów, przeszukiwać je na podstawie kryteriów i edytować swoje projekty. Usuwanie projektu jest również możliwe w przypadku translatora niestandardowego.  

## <a name="search-and-filter-projects"></a>Wyszukaj i Filtruj projekty

Narzędzie filtru umożliwia wyszukiwanie projektów według różnych warunków filtrowania. Filtruje takie jak nazwa projektu, stan, język źródłowy i Kategoria projektu.

1. Kliknij przycisk Filtr.

    ![Wyszukaj w projekcie](media/how-to/how-to-search-project.png)

2. Można filtrować według dowolnego (lub wszystkich) następujących pól: Nazwa projektu, język źródłowy, język docelowy, Kategoria i dostępność projektu.

3. Kliknij przycisk Zastosuj.

    ![Wyszukaj opcje filtru projektu](media/how-to/how-to-search-project-filters.png)

4. Wyczyść filtr, aby wyświetlić wszystkie projekty, naciskając pozycję "Wyczyść".

## <a name="edit-a-project"></a>Edytuj projekt

Translator niestandardowy umożliwia edytowanie nazwy i opisu projektu. Inne metadane projektu, takie jak kategoria, język źródłowy i język docelowy, nie są dostępne do edycji. W poniższych krokach opisano sposób edytowania projektu.

1. Kliknij ikonę ołówka, która pojawia się po umieszczeniu wskaźnika myszy na projekcie.

    ![Edytuj projekt](media/how-to/how-to-edit-project.png)

2. W oknie dialogowym można zmodyfikować nazwę projektu, opis projektu, opis kategorii i etykietę projektu, jeśli żaden model nie został wdrożony. Nie można zmodyfikować kategorii ani pary językowej po utworzeniu projektu.

    ![Edytuj projekt — okno dialogowe](media/how-to/how-to-edit-project-dialog.png)

3. Kliknij przycisk "Zapisz".

## <a name="delete-a-project"></a>Usuwanie projektu

Możesz usunąć projekt, gdy nie jest już potrzebny. Upewnij się, że projekt nie ma modeli w stanie aktywnym, takich jak wdrożone, przekazane szkolenia, przetwarzanie danych, wdrażanie itp., w przeciwnym razie operacja usuwania zakończy się niepowodzeniem. Poniżej opisano, jak usunąć projekt.

1. Umieść kursor na dowolnym rekordzie projektu i kliknij ikonę kosza.

   ![Usuń projekt](media/how-to/how-to-delete-project.png)

2. Potwierdź usunięcie. Usunięcie projektu spowoduje usunięcie wszystkich modeli, które zostały utworzone w ramach tego projektu. Usunięcie projektu nie będzie miało wpływu na dokumenty.

   ![Okno dialogowe potwierdzania usuwania](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Następne kroki

- [Przekaż dokumenty](how-to-upload-document.md) , aby rozpocząć tworzenie niestandardowego modelu tłumaczenia.
