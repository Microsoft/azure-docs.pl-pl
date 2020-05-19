---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5e2a74f69f575f4b9b332b98d7e19089418b29ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585247"
---
## <a name="sign-in-to-luis-portal"></a>Zaloguj się do portalu LUIS

Nowy użytkownik do LUIS musi wykonać następującą procedurę:

1. Zaloguj się do [portalu Luis](https://www.luis.ai), wybierz swój kraj i zaakceptuj warunki użytkowania. Jeśli zamiast tego zobaczysz **Moje aplikacje** , zasób Luis już istnieje i należy przejść do sekcji Tworzenie aplikacji.

1. Wybierz pozycję **Utwórz zasób platformy Azure** , a następnie wybierz pozycję **Utwórz zasób autorstwa, aby przeprowadzić migrację aplikacji do programu.**

    ![Wybierz typ zasobu Language Understanding tworzenia](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wprowadź szczegółowe informacje o zasobie.

    ![Utwórz zasób tworzenia](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu tworzenia**należy podać następujące informacje:

    * **Nazwa zasobu** — wybrana przez Ciebie Nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawca** — dzierżawa, z którą skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, za którą zostanie naliczona stawka dla zasobu.
    * **Grupa zasobów** — wybrana lub utworzona nazwa niestandardowej grupy zasobów. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji zależy od wybranej **grupy zasobów** .
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.

1. Zostanie wyświetlone podsumowanie zasobu, który ma zostać utworzony. Wybierz pozycję **Dalej**.

    ![Utwórz zasób tworzenia](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potwierdź, wybierając pozycję **Kontynuuj**.

    ![Utwórz zasób tworzenia](../media/sign-in/sign-in-confirm-continue.png)