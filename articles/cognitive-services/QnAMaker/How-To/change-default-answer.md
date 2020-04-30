---
title: Pobierz domyślną odpowiedź — QnA Maker
description: Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097102"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Zmień domyślną odpowiedź dla zasobu QnA Maker

Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.

## <a name="change-default-answer-in-the-azure-portal"></a>Zmień domyślną odpowiedź w Azure Portal

1. Przejdź do [Azure Portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzoną usługę QNA Maker.

2. Kliknij, aby otworzyć **App Service**.

    ![W Azure Portal uzyskaj dostęp do usługi App Service dla QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij pozycję **Ustawienia aplikacji** i Zmień pole **DefaultAnswer** na żądaną domyślną odpowiedź. Kliknij przycisk **Zapisz**.

    ![Wybierz pozycję Ustawienia aplikacji, a następnie Edytuj DefaultAnswer QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługę App Service

    ![Po zmianie DefaultAnswer należy ponownie uruchomić QnA Maker appService](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bazy wiedzy](../How-to/manage-knowledge-bases.md)