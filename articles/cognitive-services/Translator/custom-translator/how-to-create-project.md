---
title: Jak utworzyć projekt — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak utworzyć projekt i zarządzać nim w usłudze Azure Cognitive Services translatorem niestandardowym.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895802"
---
# <a name="create-a-project"></a>Tworzenie projektu

Projekt jest kontenerem dla modeli, dokumentów i testów. Każdy projekt automatycznie zawiera wszystkie dokumenty, które są przekazywane do tego obszaru roboczego, który ma poprawną parę językową.

Tworzenie projektu to pierwszy krok w kierunku tworzenia modelu.

## <a name="create-a-project"></a>Utwórz projekt:

1.  W portalu [translatora niestandardowego](https://portal.customtranslator.azure.ai) kliknij pozycję Utwórz projekt.

    ![Tworzenie projektu](media/how-to/how-to-create-project.png)

2.  W oknie dialogowym wprowadź następujące szczegółowe informacje o projekcie:

    a.  Nazwa projektu (wymagana): Nadaj projektowi unikatową, zrozumiałą nazwę. Nie trzeba wymieniać języków w tytule.

    b.  Opis: krótkie podsumowanie dotyczące projektu. Ten opis nie ma wpływu na zachowanie translatora niestandardowego ani systemowego systemu, ale może ułatwić rozróżnienie między różnymi projektami.

    c.  Para języków (wymagana): Wybierz język, z którego jest przeprowadzana translacja.

    d.  Kategoria (wymagana): Wybierz kategorię, która jest najbardziej odpowiednia dla Twojego projektu. Kategoria zawiera opis terminologii i stylu dokumentów, które zamierzasz przetłumaczyć.

    e.  Opis kategorii: to pole służy do lepszego opisywania określonego pola lub branży, w której pracujesz. Na przykład jeśli Twoja kategoria to medycyna, możesz dodać określony dokument, taki chirurg lub Pediatrics. Opis nie ma wpływu na zachowanie niestandardowego translatora ani systemu niestandardowego.

    f.  Etykieta projektu: [etykieta projektu](workspace-and-project.md#project-labels) rozróżnia projekty z tą samą parą językową i kategorią. Najlepszym rozwiązaniem jest użycie etykiety *tylko* wtedy, gdy planujesz kompilację wielu projektów dla tej samej pary językowej i tej samej kategorii i chcesz uzyskać dostęp do tych projektów z innym IDKategorii. Nie używaj tego pola, jeśli tworzysz systemy tylko dla jednej kategorii. Etykieta projektu nie jest wymagana i nie ułatwia rozróżniania par językowych. Możesz użyć tej samej etykiety dla wielu projektów.

    ![Okno dialogowe Tworzenie projektu](media/how-to/how-to-create-project-dialog.png)

3.  Kliknięcie pozycji Utwórz

## <a name="view-project-details"></a>Wyświetl szczegóły projektu

Strona docelowa usługi tłumaczenia niestandardowego pokazuje pierwsze 10 projektów w obszarze roboczym. Wyświetla nazwę projektu, parę języków, kategorię, stan i ocenę BLEU.

Po wybraniu projektu na stronie projektu zostaną wyświetlone następujące elementy:

- IDKategorii: element IDKategorii jest tworzony przez połączenie identyfikator obszaru roboczego, etykiety projektu i kodu kategorii. Używasz IDKategorii z interfejsem API usługi Text translator, aby uzyskać niestandardowe tłumaczenia. Aby skopiować, kliknij ikonę kopiowania.

- Przycisk uczenia: Użyj tego przycisku, aby rozpocząć [uczenie modelu](how-to-train-model.md).

- Przycisk Dodaj dokumenty: ten przycisk służy do [przekazywania dokumentów](how-to-upload-document.md).

- Przycisk filtrowania dokumentów: ten przycisk służy do filtrowania i wyszukiwania określonych dokumentów.

    ![Wyświetl szczegóły projektu](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wyszukiwać, edytować i usuwać projekt](how-to-search-edit-delete-projects.md).
- Dowiedz się, [jak przekazywać dokumenty](how-to-upload-document.md) do modeli tłumaczeń.
