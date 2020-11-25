---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026080"
---
Ten przykład obejmuje użycie usługi [Twilio](https://www.twilio.com/) do wysyłania komunikatów SMS na telefon komórkowy. Azure Functions ma już obsługę Twilio za pośrednictwem [powiązania Twilio](../articles/azure-functions/functions-bindings-twilio.md), a przykład korzysta z tej funkcji.

Pierwszym z nich jest konto Twilio. Możesz utworzyć jeden bezpłatnie o https://www.twilio.com/try-twilio . Po utworzeniu konta Dodaj następujące trzy **Ustawienia aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **TwilioAccountSid**  | Identyfikator SID konta usługi Twilio |
| **TwilioAuthToken**   | Token uwierzytelniania dla konta usługi Twilio |
| **TwilioPhoneNumber** | Numer telefonu skojarzony z kontem Twilio. Służy do wysyłania wiadomości SMS. |