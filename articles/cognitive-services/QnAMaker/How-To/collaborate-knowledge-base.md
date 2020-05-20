---
title: Współpraca z bazą wiedzy — QnA Maker
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostępna z Access Control opartych na rolach platformy Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650787"
---
# <a name="collaboration-with-authors-and-editors"></a>Współpraca z autorami i edytorami

Na poziomie zasobów QnA Maker jest dostępna współpraca umożliwiająca ograniczenie dostępu współpracowników na podstawie roli współpracownika. Dowiedz się więcej o [zagadnieniach związanych](../Concepts/role-based-access-control.md)z uwierzytelnianiem QNA Maker współpracownikami.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Dodawanie dostępu opartego na rolach (RBAC) do zasobu QnA Maker

QnA Maker umożliwia wielu osobom współpracę nad wszystkimi bazami wiedzy w tym samym QnA Maker zasobach. Ta funkcja jest dostępna z [Access Control opartych na rolach](../../../active-directory/role-based-access-control-configure.md)platformy Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Dostęp na poziomie zasobów QnA Maker

Nie można udostępnić konkretnej bazy wiedzy w usłudze QnA Maker. Jeśli potrzebujesz bardziej szczegółowej kontroli dostępu, rozważ rozmieszczenie baz wiedzy w różnych QnA Maker zasobach, a następnie Dodaj role do każdego zasobu.

## <a name="add-role-to-resource"></a>Dodawanie roli do zasobu

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Dodawanie konta użytkownika do zasobu QnA Maker

Poniższe kroki wykorzystują rolę współpracownika, ale dowolne [role](../reference-role-based-access-control.md) można dodać przy użyciu tych kroków

1. Zaloguj się do witryny [Azure](https://portal.azure.com/) Portal i przejdź do zasobu QNA Maker.

    ![Lista zasobów QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Przejdź do karty **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Wybierz pozycję **Dodaj**.

    ![Dodawanie QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Wybierz rolę z następującej listy:

    |Rola|
    |--|
    |Właściciel|
    |Współautor|
    |QnA Maker czytelnik|
    |Edytor QnA Maker|
    |Cognitive Services użytkownika|

    ![QnA Maker Dodaj rolę w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Wprowadź adres e-mail użytkownika i naciśnij pozycję **Zapisz**.

    ![QnA Maker Dodaj adres e-mail w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Gdy osoba, do której udostępniono usługę QnA Maker, loguje się do [portalu QNA Maker](https://qnamaker.ai), zobaczy wszystkie bazy wiedzy w tej usłudze na podstawie ich roli.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)

Dowiedz się więcej o współpracy:
* Kontrola dostępu oparta na rolach na [platformie Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [koncepcji](../Concepts/role-based-access-control.md) kontroli dostępu opartej na rolach
