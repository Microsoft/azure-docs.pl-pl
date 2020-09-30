---
title: Zarządzanie wersjami — LUIS
titleSuffix: Azure Cognitive Services
description: Wersje umożliwiają kompilowanie i publikowanie różnych modeli. Dobrym sposobem jest klonowanie bieżącego aktywnego modelu do innej wersji aplikacji przed wprowadzeniem zmian w modelu.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 3cf4ce5dc7061065ea501ae1c00cd5b9e5404770
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540867"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Używanie wersji do edytowania i testowania bez wpływu na aplikacje tymczasowe i produkcyjne

Wersje umożliwiają kompilowanie i publikowanie różnych modeli. Dobrym sposobem jest klonowanie bieżącego aktywnego modelu do innej [wersji](luis-concept-version.md) aplikacji przed wprowadzeniem zmian w modelu.

Aktywna wersja to wersja edytowana w sekcji **kompilacja** portalu Luis z intencjami, obiektami, funkcjami i wzorcami. W przypadku korzystania z interfejsów API tworzenia nie trzeba ustawiać aktywnej wersji, ponieważ wywołania interfejsu API REST specyficzne dla wersji obejmują wersję w marszrucie.

Aby współpracować z wersjami, Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** , a następnie wybierz pozycję **Zarządzaj** na górnym pasku, a następnie wybierz pozycję **wersje** w lewym okienku nawigacji.

Lista wersji pokazuje, które wersje zostały opublikowane, gdzie zostały opublikowane i która wersja jest obecnie aktywna.

## <a name="clone-a-version"></a>Klonowanie wersji

1. Wybierz wersję, którą chcesz sklonować, a następnie wybierz pozycję **Klonuj** z paska narzędzi.

2. W oknie dialogowym **klonowanie wersji** wpisz nazwę nowej wersji, na przykład "0,2".

   ![Okno dialogowe klonowania wersji](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > Identyfikator wersji może składać się tylko z znaków, cyfr lub znaku "." i nie może zawierać więcej niż 10 znaków.

   Nowa wersja o określonej nazwie zostanie utworzona i ustawiona jako aktywna wersja.

## <a name="set-active-version"></a>Ustaw aktywną wersję

Wybierz wersję z listy, a następnie wybierz pozycję **Aktywuj** na pasku narzędzi.

## <a name="import-version"></a>Wersja importowana

Możesz zaimportować `.json` lub `.lu` wersję aplikacji.

1. Wybierz pozycję **Importuj** z paska narzędzi, a następnie wybierz format.

2. W oknie podręcznym **Importuj nową wersję** wprowadź nazwę nowej wersji 10 znaków. Należy ustawić identyfikator wersji tylko wtedy, gdy wersja w pliku już istnieje w aplikacji.

    ![Sekcja zarządzania, Strona wersji, importowanie nowej wersji](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Po zaimportowaniu wersji Nowa wersja zostanie uaktywniona.

### <a name="import-errors"></a>Błędy importowania

* Błędy tokenizatora: Jeśli **wystąpi błąd tokenizatora** podczas importowania, próbujesz zaimportować wersję, która używa innej [tokenizatora](luis-language-support.md#custom-tokenizer-versions) niż aktualnie używanej przez aplikację. Aby rozwiązać ten problem, zobacz [Migrowanie między wersjami tokenizatora](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Inne akcje

* Aby **usunąć** wersję, wybierz wersję z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi. Wybierz przycisk **OK**.
* Aby **zmienić nazwę** wersji, wybierz wersję z listy, a następnie wybierz pozycję **Zmień nazwę** z paska narzędzi. Wprowadź nową nazwę i wybierz pozycję **gotowe**.
* Aby **wyeksportować** wersję, wybierz wersję z listy, a następnie wybierz pozycję **Eksportuj aplikację** z paska narzędzi. Wybierz pozycję JSON lub LU, aby wyeksportować dane do kopii zapasowej lub zapisać w kontroli źródła, wybierz pozycję **Eksportuj dla kontenera** , aby [użyć tej aplikacji w kontenerze Luis](luis-container-howto.md).

