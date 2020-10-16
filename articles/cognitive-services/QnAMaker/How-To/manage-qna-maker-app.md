---
title: Zarządzanie aplikacją QnA Maker — QnA Maker
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. QnA Maker oferuje możliwość poprawy jakości bazy wiedzy z aktywną nauką. Jeden z nich może przeglądać, akceptować lub odrzucać i dodawać bez usuwania lub zmieniania istniejących pytań.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128480"
---
# <a name="manage-qna-maker-app"></a>Zarządzanie aplikacją QnA Maker

QnA Maker pozwala na współpracę z różnymi autorami i edytorami zawartości, oferując możliwość ograniczenia dostępu współpracującego w oparciu o rolę współpracownika.
Dowiedz się więcej o [zagadnieniach związanych z uwierzytelnianiem QNA Maker współpracownikami](../Concepts/role-based-access-control.md).

Możesz również poprawić jakość bazy wiedzy, proponując alternatywne pytania poprzez [aktywną naukę](../Concepts/active-learning-suggestions.md). Zgłoszenia użytkowników są brane pod uwagę i pojawiają się w postaci sugestii na liście pytań alternatywnych. Aby dodać te sugestie jako pytania alternatywne lub je odrzucić, możesz to zrobić.

Baza wiedzy nie zmienia się automatycznie. Aby zmiany zaczęły obowiązywać, należy zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Dodawanie kontroli dostępu opartej na rolach (Azure RBAC)

QnA Maker umożliwia wielu osobom współpracę nad wszystkimi bazami wiedzy w tym samym QnA Maker zasobach. Ta funkcja jest świadczona z [kontrolą dostępu opartą na rolach (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Dostęp na poziomie zasobów QnA Maker

Nie można udostępnić konkretnej bazy wiedzy w usłudze QnA Maker. Jeśli potrzebujesz bardziej szczegółowej kontroli dostępu, rozważ rozmieszczenie baz wiedzy w różnych QnA Maker zasobach, a następnie Dodaj role do każdego zasobu.

## <a name="add-a-role-to-a-resource"></a>Dodawanie roli do zasobu

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Dodawanie konta użytkownika do zasobu QnA Maker

Poniższe kroki wykorzystują rolę współpracownika, ale dowolne [role](../reference-role-based-access-control.md) można dodać przy użyciu tych kroków

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

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker Dodaj rolę w usłudze IAM.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uaktualnij wersję środowiska uruchomieniowego, aby użyć usługi Active Learning

Usługa Active Learning jest obsługiwana w środowisku uruchomieniowym w wersji 4.4.0 i nowszych. Jeśli baza wiedzy została utworzona w starszej wersji, [Uaktualnij środowisko uruchomieniowe](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby użyć tej funkcji.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Włącz aktywną naukę w przypadku pytań alternatywnych

Aktywna nauka jest domyślnie wyłączona. Włącz tę opcję, aby zobaczyć sugerowane pytania. Po włączeniu aktywnego uczenia należy wysłać informacje z aplikacji klienckiej do QnA Maker. Aby uzyskać więcej informacji, zobacz temat [Architektura przepływu na potrzeby używania GenerateAnswer i uczenia interfejsów API z bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wybierz pozycję **Publikuj** , aby opublikować bazę wiedzy. Aktywne zapytania szkoleniowe są zbierane tylko z punktu końcowego przewidywania interfejsu API GenerateAnswer. Zapytania do okienka test w portalu QnA Maker nie wpływają na aktywną naukę.

1. Aby włączyć aktywną naukę w portalu QnA Maker, przejdź do prawego górnego rogu, wybierz swoją **nazwę**i przejdź do pozycji [**Ustawienia usługi**](https://www.qnamaker.ai/UserSettings).

    ![Na stronie ustawień usługi Włącz zaproponowane wątpliwości dotyczące aktywnego uczenia. Wybierz swoją nazwę użytkownika w prawym górnym menu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker a następnie Przełącz **aktywną naukę**.

    > [!div class="mx-imgBorder"]
    > [![Na stronie Ustawienia usługi Przełącz funkcję Active Learning. Jeśli nie możesz przełączać tej funkcji, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > Dokładna wersja na powyższym obrazie jest pokazywana tylko jako przykład. Twoja wersja może być inna.
    Po włączeniu **aktywnej uczenia** baza wiedzy sugeruje nowe pytania w regularnych odstępach czasu na podstawie pytań przesłanych przez użytkownika. **Aktywną naukę** można wyłączyć, przełączając ponownie ustawienie.

## <a name="review-suggested-alternate-questions"></a>Przejrzyj sugerowane pytania alternatywne

Zapoznaj się z [alternatywnymi sugerowanymi pytaniami](improve-knowledge-base.md) na stronie **edytowania** każdej bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./manage-knowledge-bases.md)