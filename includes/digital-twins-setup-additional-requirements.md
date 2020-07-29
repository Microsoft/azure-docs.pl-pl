---
author: baanders
description: Dołączanie pliku w celu uzyskania dodatkowych wymagań w instalacji usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 57be62d0f74f19e35eedf8720e2a6f5cbd3b45d4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87335782"
---
Istnieje możliwość, aby organizacja wymagała dodatkowych akcji od właścicieli subskrypcji w celu pomyślnego skonfigurowania rejestracji aplikacji (i w związku z tym, aby zakończyć konfigurowanie użytecznego wystąpienia usługi Azure Digital bliźniaczych reprezentacji). Wymagane kroki mogą się różnić w zależności od ustawień określonych w organizacji.

Poniżej przedstawiono niektóre typowe potencjalne działania, które może potrzebować właściciel. Te i inne operacje można wykonać na stronie [*rejestracje aplikacja usługi Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) w Azure Portal.
* Przyznaj zgodę administratora na rejestrację aplikacji. Twoja organizacja może mieć *zgodę administratora* , która została zadana globalnie włączona w usłudze Azure AD dla wszystkich rejestracji aplikacji w ramach subskrypcji. Jeśli tak, właściciel będzie musiał wybrać ten przycisk dla swojej firmy na stronie *uprawnień interfejsu API* rejestracji aplikacji, aby Rejestracja aplikacji była prawidłowa:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/grant-admin-consent.png" alt-text="Widok portalu przycisku "Udziel zgody administratora" w obszarze uprawnienia interfejsu API":::
  - Jeśli zgoda została udzielona pomyślnie, wpis dla usługi Azure Digital bliźniaczych reprezentacji powinien zawierać wartość *stanu* _przyznany dla **(Twoja firma)** _
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/granted-admin-consent.png" alt-text="Widok portalu zgody administratora udzielonego dla firmy w ramach uprawnień interfejsu API":::
* Aktywuj dostęp klienta publicznego
* Ustawianie określonych adresów URL odpowiedzi dla dostępu do sieci Web i pulpitu
* Zezwalaj na niejawne przepływy uwierzytelniania OAuth2

Aby uzyskać więcej informacji o rejestracji aplikacji i jej różnych opcjach, zobacz [*Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft*](https://docs.microsoft.com/graph/auth-register-app-v2).

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji, masz przypisane uprawnienia do zarządzania nim i skonfigurowano uprawnienia do aplikacji klienckiej w celu uzyskania do niego dostępu.