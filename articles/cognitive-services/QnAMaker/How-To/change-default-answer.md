---
title: Pobierz domyślną odpowiedź — QnA Maker
description: Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979978"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Zmień domyślną odpowiedź dla zasobu QnA Maker

Domyślna odpowiedź dla bazy wiedzy jest przeznaczona do zwrócenia, jeśli nie zostanie znaleziona odpowiedź. Jeśli używasz aplikacji klienckiej, takiej jak [usługa Azure bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), może ona również mieć oddzielną odpowiedź domyślną, co oznacza, że żadna odpowiedź nie spełniła progu wyniku.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Ustawianie odpowiedzi domyślnej podczas tworzenia bazy wiedzy

Podczas tworzenia nowej bazy wiedzy domyślny tekst odpowiedzi jest jednym z ustawień. Jeśli nie ustawisz go podczas procesu tworzenia, możesz go później zmienić przy użyciu poniższej procedury.

## <a name="change-default-answer-in-qna-maker-portal"></a>Zmień domyślną odpowiedź w portalu QnA Maker

Domyślna odpowiedź bazy wiedzy jest zwracana, gdy nie jest zwracana odpowiedź z usługi QnA Maker.

1. Zaloguj się do [portalu QNA Maker](https://www.qnamaker.ai/) i wybierz bazę wiedzy z listy.
1. Wybierz pozycję **Ustawienia** na pasku nawigacyjnym.
1. Zmień wartość **domyślnego tekstu odpowiedzi** w sekcji **Zarządzaj bazą wiedzy** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Zrzut ekranu przedstawiający Portal QnA Maker, stronę ustawień z wyróżnioną domyślną polem tekstowym odpowiedzi.":::

1. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bazy wiedzy](../How-to/manage-knowledge-bases.md)