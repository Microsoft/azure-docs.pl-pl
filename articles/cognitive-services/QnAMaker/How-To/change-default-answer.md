---
title: Pobierz domyślną odpowiedź — QnA Maker
description: Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 14954f89fcdcbbc1ef4b8654582a3274f4bb0923
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776820"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Zmień domyślną odpowiedź dla zasobu QnA Maker

Domyślna odpowiedź dla bazy wiedzy jest przeznaczona do zwrócenia, jeśli nie zostanie znaleziona odpowiedź. Jeśli używasz aplikacji klienckiej, takiej jak [usługa Azure bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), może ona również mieć oddzielną odpowiedź domyślną, co oznacza, że żadna odpowiedź nie spełniła progu wyniku.

## <a name="types-of-default-answer"></a>Typy odpowiedzi domyślnych

W bazie wiedzy istnieją dwa typy odpowiedzi domyślnej. Ważne jest, aby zrozumieć, jak i kiedy każda z nich jest zwracana z kwerendy predykcyjnej:


|Typ pytania|Opis odpowiedzi|
|--|--|
|Odpowiedź KB, gdy nie jest określona odpowiedź|`No good match found in KB.` — Gdy [interfejs API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nie znajdzie odpowiedzi na pytanie, `DefaultAnswer` zostanie zwrócona wartość ustawienia usługi App Service. Wszystkie bazy wiedzy w tym samym QnA Maker zasobów współdzielą ten sam domyślny tekst odpowiedzi.<br>Ustawienie to można zarządzać w Azure Portal za pośrednictwem usługi App Service lub za pomocą interfejsów API REST na potrzeby [pobierania](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) lub [aktualizowania](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) ustawienia.|
|Tekst instrukcji monitowania|W przypadku korzystania z monitu monitujące w przepływie konwersacji może nie być potrzebna odpowiedź w parze QnA, ponieważ użytkownik chce wybrać jedną z monitów. W takim przypadku należy ustawić określony tekst przez ustawienie domyślnego tekstu odpowiedzi, który jest zwracany z każdym prognozą dla monitów kolejnych. Tekst jest przeznaczony do wyświetlania jako tekst instruktażowy do wyboru monitów kolejnych. Przykładem dla tego domyślnego tekstu odpowiedzi jest `Please select from the following choices` . Ta konfiguracja została omówiona w kilku następnych sekcjach tego dokumentu. Można również ustawić jako część definicji bazy wiedzy `defaultAnswerUsedForExtraction` przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

### <a name="client-application-integration"></a>Integracja aplikacji klienckiej

W przypadku aplikacji klienckiej, takiej jak bot z **usługą Azure bot**, można wybrać jedną z następujących scenariuszy:

* Użyj ustawienia bazy wiedzy
* Użyj innego tekstu w aplikacji klienckiej, aby rozróżnić, kiedy odpowiedź jest zwracana, ale nie jest zgodna z progiem wyniku. Ten tekst może być tekstem statycznym przechowywanym w kodzie lub może być przechowywany na liście ustawień aplikacji klienta.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Ustaw odpowiedź domyślną monitu podczas tworzenia bazy wiedzy

Podczas tworzenia nowej bazy wiedzy domyślny tekst odpowiedzi jest jednym z ustawień. Jeśli nie ustawisz go podczas procesu tworzenia, możesz go później zmienić przy użyciu poniższej procedury.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Zmień domyślną odpowiedź monitu w programie QnA Maker Portal

Domyślna odpowiedź bazy wiedzy jest zwracana, gdy nie jest zwracana odpowiedź z usługi QnA Maker.

1. Zaloguj się do [portalu QNA Maker](https://www.qnamaker.ai/) i wybierz bazę wiedzy z listy.
1. Wybierz pozycję **Ustawienia** na pasku nawigacyjnym.
1. Zmień wartość **domyślnego tekstu odpowiedzi** w sekcji **Zarządzaj bazą wiedzy** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Zrzut ekranu przedstawiający Portal QnA Maker, stronę ustawień z wyróżnioną domyślną polem tekstowym odpowiedzi.":::

1. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bazy wiedzy](../How-to/manage-knowledge-bases.md)
