---
title: Zarządzanie bazami wiedzy — QnA Maker
description: QnA Maker pozwala zarządzać bazami wiedzy, zapewniając dostęp do ustawień i zawartości bazy wiedzy.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 890b555703f922168a820a0535296b9f55218752
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777735"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Tworzenie bazy wiedzy i zarządzanie ustawieniami

QnA Maker pozwala zarządzać bazami wiedzy, zapewniając dostęp do ustawień bazy wiedzy i źródeł danych.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).
> * Utworzono [zasób QNA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w Azure Portal. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranym podczas tworzenia zasobu.

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

1. Zaloguj się do portalu [QnAMaker.AI](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

1. Na stronie **Tworzenie** przejdź do **kroku 1** , jeśli masz już zasób QNA Maker.

    Jeśli jeszcze nie utworzono zasobu, wybierz pozycję **Utwórz usługę QNA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranym podczas tworzenia zasobu.

    Po zakończeniu tworzenia zasobu w Azure Portal Wróć do portalu QnA Maker, Odśwież stronę przeglądarki i przejdź do **kroku 2**.

1. W **kroku 3**wybierz pozycję Active Directory, subskrypcję, usługę (zasób) i język dla wszystkich baz wiedzy utworzonych w usłudze.

   ![Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. W **kroku 3**Nazwij bazę wiedzy `My Sample QnA KB` .

1. W **kroku 4**Skonfiguruj ustawienia w następującej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz wyodrębnianie wieloskładnikowe z adresów URL, plików PDF lub DOCX.**|Zaznaczono|
    |**Domyślny tekst odpowiedzi**| `Quickstart - default answer not found.`|
    |**+ Dodaj adres URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Pogawędka**|Wybierz **profesjonalne**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją KB**.

    Proces wyodrębniania zajmuje kilka chwil, aby odczytać dokument i zidentyfikować pytania i odpowiedzi.

    Po pomyślnym utworzeniu bazy wiedzy przez QnA Maker zostanie otwarta strona **bazy wiedzy** . Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="edit-knowledge-base"></a>Edytowanie bazy wiedzy

1.  Na górnym pasku nawigacyjnym wybierz pozycję **Moje bazy wiedzy** .

       Wszystkie utworzone lub udostępnione przez Ciebie usługi można zobaczyć w kolejności malejącej według daty **ostatniej modyfikacji** .

       ![Moje bazy wiedzy](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Wybierz konkretną bazę wiedzy, aby wprowadzić w niej zmiany.

1.  Wybierz pozycję **Ustawienia**. Poniższa lista zawiera pola, które można zmienić.

       |Cel|Akcja|
       |--|--|
       |Dodaj adres URL|Możesz dodać nowe adresy URL, aby dodać nową zawartość często zadawanych pytań do bazy wiedzy, klikając pozycję **Zarządzaj bazą wiedzy — > "+ Dodaj adres URL"** .|
       |Usuwanie adresu URL|Istniejące adresy URL można usunąć, wybierając ikonę Usuń.|
       |Odśwież zawartość|Jeśli chcesz, aby baza wiedzy przeszukał najnowszą zawartość istniejących adresów URL, zaznacz pole wyboru **Odśwież** . Spowoduje to zaktualizowanie bazy wiedzy przy użyciu najnowszej zawartości adresu URL. Nie jest to ustawienie regularnego harmonogramu aktualizacji.|
       |Dodaj plik|Możesz dodać obsługiwany dokument pliku, który będzie częścią bazy wiedzy, wybierając pozycję **Zarządzaj bazą wiedzy**, a następnie wybierając pozycję **+ Dodaj plik**|
    |Import|Możesz również zaimportować każdą istniejącą bazę wiedzy, wybierając przycisk **Importuj bazę wiedzy** . |
    |Aktualizacja|Aktualizacja bazy wiedzy zależy od **warstwy cenowej zarządzania** używanej podczas tworzenia usługi QNA Maker skojarzonej z bazą wiedzy. W razie potrzeby można również zaktualizować warstwę zarządzania z Azure Portal.

  1. Po zakończeniu wprowadzania zmian w bazie wiedzy wybierz pozycję **Zapisz i Wyszkol** w prawym górnym rogu strony, aby zachować zmiany.

       ![Zapisz i pouczenie](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Jeśli opuścisz stronę przed wybraniem opcji **Zapisz i pouczenie**, wszystkie zmiany zostaną utracone.



## <a name="manage-large-knowledge-bases"></a>Zarządzanie dużymi bazami wiedzy

* **Grupy źródeł danych**: bazami są pogrupowane według źródła danych, z którego zostały wyodrębnione. Można rozwinąć lub zwinąć źródło danych.

    ![Użyj paska źródła danych QnA Maker, aby zwinąć i rozwinąć pytania i odpowiedzi ze źródła danych](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Baza wiedzy wyszukiwania**: bazę wiedzy można wyszukać, wpisując w polu tekstowym w górnej części tabeli bazy wiedzy. Kliknij przycisk ENTER, aby wyszukać zawartość pytania, odpowiedzi lub metadanych. Kliknij ikonę X, aby usunąć filtr wyszukiwania.

    ![Użyj pola wyszukiwania QnA Maker powyżej pytań i odpowiedzi, aby zmniejszyć widok tylko do elementów zgodnych z filtrem](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stronicowanie**: szybkie przechodzenie między źródłami danych w celu zarządzania dużymi bazami wiedzy

    ![Użyj QnA Maker funkcji stronicowania nad pytaniami i odpowiedziami, aby poruszać się po stronach pytań i odpowiedzi](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Usuń bazy wiedzy

Usunięcie bazy wiedzy (KB) jest operacją trwałą. Nie można jej cofnąć. Przed usunięciem bazy wiedzy należy wyeksportować bazę wiedzy ze strony **Ustawienia** w portalu QNA Maker.

Jeśli udostępnisz swoją bazę wiedzy współpracownikom,] (współpraca-wiedza-base.md), a następnie usuniesz ją, wszyscy utraci dostęp do bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzaniu językiem](language-knowledge-base.md) wszystkich baz wiedzy w zasobie.

* Edytuj pary QnA
* Zarządzanie zasobami platformy Azure używanymi przez QnA Maker