---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128238"
---
## <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu usługi LUIS

Nowy użytkownik do LUIS musi wykonać następującą procedurę:

1. Zaloguj się do [portalu Luis](https://www.luis.ai), wybierz swój kraj/region i zaakceptuj warunki użytkowania. Jeśli zamiast tego zobaczysz **Moje aplikacje** , zasób Luis już istnieje i należy przejść do sekcji Tworzenie aplikacji. Dostępne są dwie opcje rejestracji:

    * Za pomocą zasobu platformy Azure (zalecane) — umożliwia połączenie konta LUIS z nowym lub istniejącym zasobem tworzenia platformy Azure. Jest to równoznaczne z zarejestrowaniem już zmigrowane. Nie trzeba już przechodzić przez [proces migracji](../luis-migration-authoring.md#what-is-migration) później.

    * Korzystanie z klucza w wersji próbnej. Dzięki temu możesz zalogować się do usługi LUIS przy użyciu zasobu próbnego, którego nie trzeba skonfigurować. W przypadku wybrania tej opcji po pewnym czasie konieczne będzie przeprowadzenie [migracji konta](../luis-migration-authoring.md#migration-steps) i połączenie aplikacji z zasobem tworzenia.

1. W wyświetlonym oknie **Wybierz Tworzenie** , Znajdź swoją subskrypcję platformy Azure i zasób Luis Authoring. Jeśli nie masz zasobu, możesz utworzyć nowy.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Wybierz typ zasobu Language Understanding tworzenia.":::
    
    Podczas tworzenia nowego zasobu tworzenia należy podać następujące informacje:
    * **Nazwa dzierżawy** — dzierżawy, z którą skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji platformy Azure** — subskrypcja, za którą zostanie naliczona stawka dla zasobu.
    * **Nazwa grupy zasobów platformy Azure** — wybrana lub utworzona nazwa niestandardowej grupy zasobów. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Nazwa zasobu platformy Azure** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.


