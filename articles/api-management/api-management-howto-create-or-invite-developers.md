---
title: Jak zarządzać kontami użytkowników na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak tworzyć i zapraszać użytkowników w usłudze Azure API Management. Wyświetl dodatkowe zasoby, które mają być używane po utworzeniu konta dewelopera.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 92e032eb104835788f515cc7800fe5dacfa8adaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88566135"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Zarządzanie kontami użytkowników w usłudze Azure API Management

W API Management deweloperzy są użytkownikami interfejsów API udostępnianych przy użyciu API Management. W tym przewodniku pokazano, jak tworzyć i zapraszać deweloperów do używania interfejsów API i produktów, które są dostępne dla nich przy użyciu wystąpienia API Management. Aby uzyskać informacje na temat programu programistycznego zarządzania kontami użytkowników, zapoznaj się z dokumentacją [jednostki użytkownika](/rest/api/apimanagement/2019-12-01/user) w temacie [API Management REST](/rest/api/apimanagement/) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj zadania w tym artykule: [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Tworzenie nowego dewelopera

Aby dodać nowego użytkownika, wykonaj kroki opisane w tej sekcji:

1. Wybierz kartę **Użytkownicy** z lewej strony ekranu.
2. Naciśnij klawisze **+ Dodaj**.
3. Wprowadź odpowiednie informacje dla użytkownika.
4. Kliknij przycisk **Dodaj**.

    ![Dodawanie nowego użytkownika](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Nowo utworzone konta dewelopera są domyślnie **aktywne** i skojarzone z grupą **deweloperzy** . Konta deweloperów, które znajdują się w stanie **aktywnym** , mogą być używane do uzyskiwania dostępu do wszystkich interfejsów API, dla których mają subskrypcje. Aby skojarzyć nowo utworzonego dewelopera z dodatkowymi grupami, zobacz [Jak skojarzyć grupy z deweloperami][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Zaproś dewelopera
Aby zaprosić dewelopera, wykonaj kroki opisane w tej sekcji:

1. Wybierz kartę **Użytkownicy** z lewej strony ekranu.
2. Naciśnij pozycję **+ Zaproś**.

Zostanie wyświetlony komunikat z potwierdzeniem, ale nowo zaproszony Deweloper nie pojawia się na liście dopiero po zaakceptowaniu zaproszenia. 

Po zaproszeniu dewelopera do dewelopera jest wysyłana wiadomość e-mail. Ta wiadomość e-mail jest generowana przy użyciu szablonu i można ją dostosowywać. Aby uzyskać więcej informacji, zobacz [Konfigurowanie szablonów wiadomości e-mail][Configure email templates].

Po zaakceptowaniu zaproszenia konto zostanie uaktywnione.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"></a> Dezaktywowanie lub ponowne uaktywnianie konta dewelopera

Domyślnie nowo utworzone lub zaproszone konta dewelopera są **aktywne**. Aby dezaktywować konto dewelopera, kliknij przycisk **Blokuj**. Aby ponownie uaktywnić zablokowane konto dewelopera, kliknij przycisk **Aktywuj**. Zablokowane konto dewelopera nie może uzyskać dostępu do portalu dla deweloperów lub wywołać żadnych interfejsów API. Aby usunąć konto użytkownika, kliknij przycisk **Usuń**.

Aby zablokować użytkownika, wykonaj następujące czynności.

1. Wybierz kartę **Użytkownicy** z lewej strony ekranu.
2. Kliknij użytkownika, który chcesz zablokować.
3. Naciśnij klawisz **bloku**.

## <a name="reset-a-user-password"></a>Resetowanie hasła użytkownika

Aby programowo współpracować z kontami użytkowników, zapoznaj się z dokumentacją jednostki użytkownika w temacie Informacje o [interfejsie API REST API Management](/rest/api/apimanagement/) . Aby zresetować hasło konta użytkownika do określonej wartości, można użyć operacji [Aktualizuj użytkownika](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) i określić wymagane hasło.

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki
Po utworzeniu konta dewelopera można je skojarzyć z rolami i subskrybować je do produktów i interfejsów API. Aby uzyskać więcej informacji, zobacz [How to Create and use Groups][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
