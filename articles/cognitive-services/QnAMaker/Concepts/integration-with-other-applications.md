---
title: Integracja z innymi aplikacjami — QnA Maker
description: QnA Maker integruje się z aplikacjami klienckimi, takimi jak Chat botów, a także z innymi usługami przetwarzania języka naturalnego, takimi jak Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776531"
---
# <a name="design-knowledge-base-for-client-applications"></a>Projektowanie bazy wiedzy dla aplikacji klienckich

QnA Maker integruje się z aplikacjami klienckimi, takimi jak Chat botów, a także z innymi usługami przetwarzania języka naturalnego, takimi jak Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integracja z klientem konwersacji

QnA Maker integruje się z konwersacjami aplikacji klienckich, takimi jak [Microsoft bot Framework](https://dev.botframework.com/). Tekst wysłany do QnA Maker nie musi być czyszczony ani przekształcony. QnA Maker akceptuje Języki naturalne i zwraca najlepszą odpowiedź.

## <a name="create-a-bot-without-writing-any-code"></a>Utwórz bot bez pisania kodu

Po opublikowaniu bazy wiedzy Utwórz Bot na stronie **publikowania** , wybierając przycisk **Utwórz bot** . Skorzystaj z [samouczka bot](../Quickstarts/create-publish-knowledge-base.md) , aby dowiedzieć się, co się stanie po wybraniu przycisku.

## <a name="providing-multi-turn-conversations"></a>Udostępnianie wieloetapowych konwersacji

Klient bot zapewnia najlepszą wybraną odpowiedź z bazy wiedzy i może podać monity, jeśli odpowiedź jest częścią wieloskładnikowej pary QnA. Dowiedz się, [jak](../how-to/multiturn-conversation.md) dodać do bazy wiedzy dane pytań i odpowiedzi dla wieloskładnikowej konwersacji.

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Chociaż QnA Maker przetwarza pytania wykorzystujące przetwarzanie języka naturalnego, można również użyć części większego systemu, która odpowiada na pytania z wielu baz wiedzy. Możesz połączyć QnA Maker z inną usługą poznawczej, Language Understanding (LUIS), aby zapewnić przetwarzanie języka naturalnego przed przystąpieniem do określonej bazy wiedzy. Dowiedz się więcej o tym, kiedy i jak używać [Luis i QNA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) wspólnie.

## <a name="next-steps"></a>Następne kroki

Poznaj [koncepcje](development-lifecycle-knowledge-base.md) cyklu programowania dla QNA Maker.