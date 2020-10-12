---
title: Konfigurowanie powiadomień i szablonów wiadomości e-mail
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować powiadomienia i szablony wiadomości e-mail w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84690306"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>How to configure notifications and email templates in Azure API Management

API Management zapewnia możliwość konfigurowania powiadomień dla określonych zdarzeń oraz konfigurowania szablonów wiadomości e-mail używanych do komunikowania się z administratorami i deweloperami wystąpienia API Management. W tym artykule pokazano, jak skonfigurować powiadomienia dla dostępnych zdarzeń i zawiera omówienie konfigurowania szablonów wiadomości e-mail używanych dla tych zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz wystąpienia usługi API Management, wykonaj następujące czynności: [Utwórz wystąpienie API Management platformy Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Konfigurowanie powiadomień

1.  Wybierz wystąpienie usługi **API Management** .
2.  Kliknij pozycję **powiadomienia** , aby wyświetlić dostępne powiadomienia.

    ![Powiadomienia wydawcy][api-management-publisher-notifications]

    Na potrzeby powiadomień można skonfigurować następującą listę zdarzeń.

    -   **Żądania subskrypcji (wymagające zatwierdzenia)** — określeni Adresaci poczty e-mail i użytkownicy otrzymają powiadomienia e-mail o żądaniach subskrypcji dla produktów interfejsu API wymagających zatwierdzenia.
    -   **Nowe subskrypcje** — określeni Adresaci poczty e-mail i użytkownicy otrzymają powiadomienia e-mail o nowych subskrypcjach produktu interfejsu API.
    -   **Żądania galerii aplikacji** — określeni Adresaci poczty e-mail i użytkownicy otrzymają powiadomienia e-mail, gdy nowe aplikacje zostaną przesłane do galerii aplikacji.
    -   **UDW** — określeni Adresaci poczty e-mail i użytkownicy otrzymają wiadomość e-mail z ukryciem wiadomości email dla wszystkich wiadomości e-mail wysyłanych do deweloperów.
    -   **Nowy problem lub komentarz** — określeni Adresaci poczty e-mail i użytkownicy otrzymają powiadomienia e-mail po przesłaniu nowego problemu lub komentarza w portalu dla deweloperów.
    -   **Zamknij komunikat konta** — określeni adresaci wiadomości e-mail i użytkownicy otrzymają powiadomienia e-mail, gdy konto zostanie zamknięte.
    -   **Zbliża się limit przydziału subskrypcji** — następujący adresaci wiadomości e-mail i użytkownicy otrzymają powiadomienia e-mail, gdy użycie subskrypcji zostanie zbliżone do limitu przydziału użycia.

        > [!NOTE]
        > Powiadomienia są wyzwalane tylko przez zasady [subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) . Zasady [przydziału według klucza](api-management-access-restriction-policies.md#SetUsageQuotaByKey) nie generują powiadomień.

    Dla każdego zdarzenia można określić adresatów wiadomości e-mail przy użyciu pola tekstowego adres e-mail lub wybrać użytkowników z listy.

3.  Aby określić adresy e-mail do powiadomienia, wprowadź je w polu tekstowym adres e-mail. Jeśli masz wiele adresów e-mail, rozdziel je przecinkami.

    ![Adresaci powiadomień][api-management-email-addresses]

4.  Kliknij przycisk **Dodaj**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Konfigurowanie szablonów powiadomień

API Management udostępnia szablony powiadomień dla wiadomości e-mail, które są wysyłane w trakcie administrowania usługą i korzystania z niej. Dostępne są następujące szablony wiadomości e-mail.

-   Zatwierdzenie przesłania galerii aplikacji
-   Farewell dewelopera
-   Powiadomienie o limicie przydziału dla deweloperów
-   Zaproś użytkownika
-   Dodano nowy komentarz do problemu
-   Odebrano nowy problem
-   Aktywowano nową subskrypcję
-   Potwierdzenie odnowienia subskrypcji
-   Odrzucenie żądania subskrypcji
-   Odebrano żądanie subskrypcji

Te szablony można zmodyfikować zgodnie z potrzebami.

Aby wyświetlić i skonfigurować szablony wiadomości e-mail dla wystąpienia API Management, kliknij przycisk **Szablony powiadomień**.

![Szablony wiadomości e-mail][api-management-email-templates]

Każdy szablon wiadomości e-mail ma temat w postaci zwykłego tekstu i definicję treści w formacie HTML. Każdy element można dostosować zgodnie z potrzebami.

![Edytor szablonów wiadomości e-mail][api-management-email-template]

Lista **parametrów** zawiera listę parametrów, które po wstawieniu do tematu lub treści zostaną zastąpione określoną wartością podczas wysyłania wiadomości e-mail. Aby wstawić parametr, umieść kursor w miejscu, w którym ma się znaleźć parametr, a następnie kliknij strzałkę po lewej stronie nazwy parametru.

> [!NOTE]
> Parametry nie są zamieniane na wartości rzeczywiste podczas wyświetlania lub wysyłania testu.

Aby zapisać zmiany w szablonie wiadomości e-mail, kliknij przycisk **Zapisz**lub aby anulować zmiany, kliknij przycisk **Odrzuć**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
