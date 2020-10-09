---
title: Użyj poprawek, aby bezpiecznie wprowadzać zmiany niepowodujące Nieprzerwania w API Management
titleSuffix: Azure API Management
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak przy użyciu poprawek wprowadzać zmiany niepowodujące niezgodności w usłudze API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a4655b20fabcc72e02037de05dd0ef7c4671e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254915"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Bezpieczne wprowadzanie zmian niepowodujących niezgodności przy użyciu poprawek
Gdy interfejs API jest gotowy i zaczyna być używany przez deweloperów, zazwyczaj musisz wprowadzać zmiany w tym interfejsie API i jednocześnie nie zakłócać pracy wywołującym interfejs API. Ponadto warto poinformować deweloperów o wprowadzanych zmianach. Możemy to zrobić w usłudze Azure API Management przy użyciu **poprawek**. Aby uzyskać więcej informacji, zobacz [Wersje i poprawki](https://azure.microsoft.com/blog/versions-revisions/) oraz [Przechowywanie wersji interfejsu API w usłudze Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej poprawki
> * Wprowadzanie w poprawce zmian niepowodujących niezgodności
> * Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

![Dziennik zmian w portalu dla deweloperów](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Dodawanie nowej poprawki

![Dodawanie poprawki interfejsu API](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Wybierz stronę **Interfejsy API**.
2. Z listy interfejsów API wybierz pozycję **Wersja demonstracyjna interfejsu API Conference** (lub inny interfejs API, do którego chcesz dodać poprawki).
3. Kliknij kartę **Poprawki** w menu w górnej części strony.
4. Wybierz opcję **+ Dodaj poprawkę**

    > [!TIP]
    > Możesz też wybrać opcję **Dodaj poprawkę** w menu kontekstowym (**...**) interfejsu API.

5. Podaj opis nowej poprawki, aby łatwiej zapamiętać, do czego służy.
6. Wybierz pozycję **Utwórz**
7. Nowa poprawka zostanie utworzona.

    > [!NOTE]
    > Oryginalny interfejs API pozostaje w poprawce **Poprawka 1**. Jest to poprawka, którą nadal będą wywoływać użytkownicy, dopóki nie wybierzesz innej poprawki jako bieżącej.

## <a name="make-non-breaking-changes-to-your-revision"></a>Wprowadzanie w poprawce zmian niepowodujących niezgodności

![Modyfikowanie poprawki](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. Z listy interfejsów API wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
2. Wybierz kartę **Projektowanie** w górnej części ekranu.
3. Zwróć uwagę, że **selektor poprawek** (bezpośrednio nad kartą projektowania) wskazuje poprawkę **Poprawka 2** jako aktualnie wybraną.

    > [!TIP]
    > Za pomocą selektora poprawek możesz przełączać się pomiędzy poprawkami, nad którymi chcesz pracować.

4. Wybierz opcję **+ Dodaj operację**.
5. Ustaw nową operację na **POST**, a nazwę, nazwę wyświetlaną i adres URL operacji na **test**.
6. **Zapisz** nową operację.
7. Wprowadziliśmy zmianę w poprawce **Poprawka 2**. Za pomocą **selektora poprawek** w górnej części strony przełącz się z powrotem na poprawkę **Poprawka 1**.
8. Zauważ, że nowa operacja nie pojawia się w ramach poprawki **Poprawka 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian

1. Wybierz kartę **Poprawki** w menu w górnej części strony.

    ![Menu poprawki na ekranie poprawek.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Otwórz menu kontekstowe (**...**) dla poprawki **Poprawka 2**.
3. Wybierz opcję **Ustaw jako bieżącą**.
4. Zaznacz pole **Publikuj w publicznym dzienniku zmian tego interfejsu API**, jeśli chcesz publikować uwagi dotyczące tej zmiany. Podaj opis zmiany, którą widzą deweloperzy, na przykład: **testowanie poprawek. Dodano nową operację "test".**
5. **Poprawka 2** jest teraz bieżącą poprawką.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

1. W Azure Portal wybierz pozycję **interfejsy API**.
2. Wybierz opcję **Portal dla deweloperów** z górnego menu.
3. Wybierz pozycję **Interfejsy API**, a następnie wybierz pozycję **Wersja demonstracyjna interfejsu API Conference**.
4. Zauważ, że nowa operacja **test** jest teraz dostępna.
5. Kliknij pozycję **Dziennik zmian** obok nazwy interfejsu API.
6. Zauważ, że na liście pojawia się wpis dziennika zmian.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowej poprawki
> * Wprowadzanie w poprawce zmian niepowodujących niezgodności
> * Ustawianie poprawki jako bieżącej oraz dodawanie wpisu dziennika zmian
> * Przeglądanie portalu dla deweloperów w celu wyświetlenia zmian i dziennika zmian

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md)
