---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183032"
---
Ten przykład obejmuje użycie usługi [Twilio](https://www.twilio.com/) do wysyłania komunikatów SMS na telefon komórkowy. Azure Functions ma już obsługę Twilio za pośrednictwem [powiązania Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), a przykład korzysta z tej funkcji.

Pierwszym z nich jest konto Twilio. Możesz utworzyć jeden bezpłatnie o https://www.twilio.com/try-twilio . Po utworzeniu konta Dodaj następujące trzy **Ustawienia aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **TwilioAccountSid**  | Identyfikator SID konta usługi Twilio |
| **TwilioAuthToken**   | Token uwierzytelniania dla konta usługi Twilio |
| **TwilioPhoneNumber** | Numer telefonu skojarzony z kontem Twilio. Służy do wysyłania wiadomości SMS. |