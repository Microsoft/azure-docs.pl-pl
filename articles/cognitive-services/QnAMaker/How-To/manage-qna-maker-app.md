---
title: Zarządzanie aplikacją QnA Maker — QnA Maker
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. QnA Maker oferuje możliwość poprawy jakości bazy wiedzy z aktywną nauką. Jeden z nich może przeglądać, akceptować lub odrzucać i dodawać bez usuwania lub zmieniania istniejących pytań.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217333"
---
# <a name="manage-qna-maker-app"></a>Zarządzanie aplikacją QnA Maker

QnA Maker pozwala na współpracę z różnymi autorami i edytorami zawartości, oferując możliwość ograniczenia dostępu współpracującego w oparciu o rolę współpracownika.
Dowiedz się więcej o [zagadnieniach związanych z uwierzytelnianiem QNA Maker współpracownikami](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Dodawanie kontroli dostępu opartej na rolach (Azure RBAC)

QnA Maker umożliwia wielu osobom współpracę nad wszystkimi bazami wiedzy w tym samym QnA Maker zasobach. Ta funkcja jest świadczona z [kontrolą dostępu opartą na rolach (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Dostęp na poziomie zasobów QnA Maker

Nie można udostępnić konkretnej bazy wiedzy w usłudze QnA Maker. Jeśli potrzebujesz bardziej szczegółowej kontroli dostępu, rozważ rozmieszczenie baz wiedzy w różnych QnA Maker zasobach, a następnie Dodaj role do każdego zasobu.

## <a name="add-a-role-to-a-resource"></a>Dodawanie roli do zasobu

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Dodawanie konta użytkownika do zasobu QnA Maker

Poniższe kroki wykorzystują rolę współpracownika, ale dowolne role można dodać przy użyciu tych kroków

1. Zaloguj się do witryny [Azure](https://portal.azure.com/) Portal i przejdź do zasobu QNA Maker.

    ![Lista zasobów QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Przejdź do karty **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Wybierz rolę z następującej listy:

    |Rola|
    |--|
    |Właściciel|
    |Współautor|
    |Cognitive Services QnA Maker Reader|
    |Edytor QnA Maker Cognitive Services|
    |Cognitive Services użytkownika|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Dodaj rolę w usłudze IAM.":::

1. Wprowadź adres e-mail użytkownika i naciśnij pozycję **Zapisz**.

    ![QnA Maker Dodaj adres e-mail w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Wyświetl QnA Maker bazy wiedzy

Gdy osoba, do której udostępniono usługę QnA Maker, loguje się do [portalu QNA Maker](https://qnamaker.ai), zobaczy wszystkie bazy wiedzy w tej usłudze na podstawie ich roli.

Po wybraniu bazy wiedzy jej bieżąca rola dla tego zasobu QnA Maker będzie widoczna obok nazwy bazy wiedzy.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Zrzut ekranu bazy wiedzy w trybie edycji z nazwą roli w nawiasach obok nazwy bazy wiedzy w lewym górnym rogu strony sieci Web.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./manage-knowledge-bases.md)
