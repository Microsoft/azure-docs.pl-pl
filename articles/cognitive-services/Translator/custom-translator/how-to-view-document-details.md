---
title: Szczegóły dokumentu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Na stronie Lista dokumentów zostanie wyświetlony pierwszy 10 dokumentów w obszarze roboczym. Dla każdego dokumentu wyświetlana jest nazwa, parowanie, typ, język, sygnatura czasowa przekazywania i adres e-mail użytkownika, który przesłał dokument.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 22c982babbed1322e97127379be75e7b070ed81f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994246"
---
# <a name="view-document-details"></a>Wyświetlanie szczegółów dokumentu

Na stronie Lista dokumentów zostanie wyświetlony pierwszy 10 dokumentów w obszarze roboczym. Dla każdego dokumentu wyświetlana jest nazwa, parowanie, typ, język, sygnatura czasowa przekazywania i adres e-mail użytkownika, który przesłał dokument.

Kliknij pojedynczy dokument, aby wyświetlić stronę szczegóły dokumentu. Na stronie szczegóły dokumentu zostanie wyświetlona lista wyodrębnionych zdań z dokumentu.

- Domyślnie język "Źródło" jest wybierany w polu listy rozwijanej, ale można przełączać się do wyświetlania zdań w języku docelowym.
- 20 zdań jest domyślnie wyświetlanych na stronie. Można użyć formantu stronicowania, aby przeglądać między stronami.

![szczegóły dokumentu](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Usuwanie dokumentu

Użytkownik musi być właścicielem obszaru roboczego, aby usunąć dokument w celu usunięcia dokumentu. Ponadto, jeśli dokument jest używany przez model, który jest częścią procesu szkolenia lub dowolnej części procesu wdrażania, nie można usunąć dokumentu.

1. Przejdź do strony dokumentu
2.  Umieść kursor na dowolnym rekordzie dokumentu i kliknij ikonę kosza.

    ![Usuwanie dokumentu](media/how-to/how-to-delete-document-1.png)

3.  Potwierdź usunięcie.

    ![Potwierdzenie usunięcia](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak szkolić model](how-to-train-model.md).
