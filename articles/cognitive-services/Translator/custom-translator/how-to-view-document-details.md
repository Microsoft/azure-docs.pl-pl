---
title: Szczegóły dokumentu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Na stronie Lista dokumentów zostanie wyświetlony pierwszy 10 dokumentów w obszarze roboczym. Dla każdego dokumentu wyświetlana jest nazwa, parowanie, typ, język, sygnatura czasowa przekazywania i adres e-mail użytkownika, który przesłał dokument.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 87b999069ef9088a731a4e972c5d548cac0b917c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509578"
---
# <a name="view-document-details"></a>Wyświetlanie szczegółów dokumentu

Na stronie Lista dokumentów zostanie wyświetlony pierwszy 10 dokumentów w obszarze roboczym. Dla każdego dokumentu wyświetlana jest nazwa, parowanie, typ, język, sygnatura czasowa przekazywania i adres e-mail użytkownika, który przesłał dokument.

Kliknij pojedynczy dokument, aby wyświetlić stronę szczegóły dokumentu. Na stronie szczegóły dokumentu zostanie wyświetlona lista wyodrębnionych zdań z dokumentu.

- Domyślnie w polu listy rozwijanej jest zaznaczona wartość "widoczne", źródłowa i docelowa język "obok siebie", ale można przełączać się do wyświetlania zdań w języku źródłowym lub docelowym.
- 20 zdań jest domyślnie wyświetlanych na stronie. Można użyć formantu stronicowania, aby przeglądać między stronami.

![szczegóły dokumentu](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Usuwanie dokumentu

Użytkownik musi być właścicielem obszaru roboczego, aby usunąć dokument w celu usunięcia dokumentu. Ponadto, jeśli dokument jest używany przez model, który jest częścią procesu szkolenia lub dowolnej części procesu wdrażania, nie można usunąć dokumentu.

1. Przejdź do strony dokumentu
2. Umieść kursor na dowolnym rekordzie dokumentu i kliknij ikonę kosza.

    ![Usuwanie dokumentu](media/how-to/how-to-delete-document-1.png)

3. Potwierdź usunięcie.

    ![Potwierdzenie usunięcia](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak szkolić model](how-to-train-model.md).
