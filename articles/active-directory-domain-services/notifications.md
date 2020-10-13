---
title: Powiadomienia e-mail dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować powiadomienia e-mail o problemach w Azure Active Directory Domain Services domenie zarządzanej
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: c273b1a6c497ec8dcb8c5bc5f50ccfccc9fce155
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968021"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Skonfiguruj powiadomienia e-mail pod kątem problemów w Azure Active Directory Domain Services

Kondycja domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS) jest monitorowana przez platformę Azure. Na stronie stan kondycji w Azure Portal są wyświetlane alerty dla domeny zarządzanej. Aby upewnić się, że w odpowiednim czasie odpowiedziały problemy, powiadomienia e-mail można skonfigurować do raportowania alertów dotyczących kondycji natychmiast po ich wykryciu w domenie zarządzanej AD DS platformy Azure.

W tym artykule opisano sposób konfigurowania adresatów powiadomień e-mail dla domeny zarządzanej.

## <a name="email-notification-overview"></a>Omówienie powiadomień e-mail

Aby poinformować użytkownika o problemach z domeną zarządzaną, możesz skonfigurować powiadomienia e-mail. Te powiadomienia e-mail określają domenę zarządzaną, w której znajduje się alert, oraz przedstawiają czas wykrywania i łącze do strony kondycji w Azure Portal. Następnie można wykonać podane porady dotyczące rozwiązywania problemów, aby rozwiązać te problemy.

Następujące przykładowe powiadomienie e-mail wskazuje krytyczne ostrzeżenie lub alert został wygenerowany w domenie zarządzanej:

![Przykładowe powiadomienie e-mail](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Zawsze upewnij się, że wiadomość e-mail pochodzi od zweryfikowanego nadawcy firmy Microsoft przed kliknięciem linków w komunikacie. Powiadomienia e-mail zawsze pochodzą z `azure-noreply@microsoft.com` adresu.

### <a name="why-would-i-receive-email-notifications"></a>Dlaczego otrzymuję powiadomienia e-mail?

Usługa Azure AD DS wysyła powiadomienia e-mail o ważnych aktualizacjach dotyczących domeny zarządzanej. Te powiadomienia są przeznaczone tylko dla pilnych problemów, które mają wpływ na usługę i powinny być natychmiast rozwiązywane. Każde powiadomienie e-mail jest wyzwalane przez alert w domenie zarządzanej. Alerty są również wyświetlane w Azure Portal i można je wyświetlić na [stronie usługi Azure AD DS Health][check-health].

Usługa Azure AD DS nie wysyła wiadomości e-mail w celu reklamy, aktualizacji lub sprzedaży.

### <a name="when-will-i-receive-email-notifications"></a>Kiedy otrzymam powiadomienia e-mail?

Powiadomienie jest wysyłane natychmiast po znalezieniu [nowego alertu][troubleshoot-alerts] w domenie zarządzanej. Jeśli alert nie zostanie rozwiązany, dodatkowe powiadomienia e-mail będą wysyłane jako przypomnienia co cztery dni.

### <a name="who-should-receive-the-email-notifications"></a>Kto powinien otrzymywać powiadomienia e-mail?

Lista adresatów wiadomości e-mail dla usługi Azure AD DS powinna składać się z osób, które mogą administrować i wprowadzać zmiany w domenie zarządzanej. Ta lista wiadomości e-mail powinna być uważana za "pierwszych odpowiadających" na wszelkie alerty i problemy.

Do powiadomień e-mail można dodać maksymalnie pięciu dodatkowych adresatów. Jeśli potrzebujesz więcej niż pięciu adresatów powiadomień e-mail, Utwórz listę dystrybucyjną i Dodaj ją do listy powiadomień.

Możesz również wybrać opcję posiadania wszystkich *administratorów globalnych* w katalogu usługi Azure AD, a każdy członek grupy *Administratorzy domeny w usłudze AAD* odbiera powiadomienia e-mail. Usługa Azure AD DS wysyła tylko powiadomienia do 100 adresów e-mail, w tym listę administratorów globalnych i administratorów usługi AAD DC.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby przejrzeć istniejących adresatów powiadomień e-mail lub dodać dodatkowych adresatów, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Po lewej stronie okna zasobów AD DS platformy Azure wybierz pozycję **Ustawienia powiadomień**. Są wyświetlane wszyscy adresaci powiadomień e-mail.
1. Aby dodać adresata poczty e-mail, wprowadź adres e-mail w tabeli dodatkowych adresatów.
1. Gdy skończysz, wybierz pozycję **Zapisz** na pasku nawigacyjnym najwyższego poziomu.

> [!WARNING]
> Po zmianie ustawień powiadomień ustawienia powiadomień dla całej domeny zarządzanej są aktualizowane, a nie tylko dla siebie.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Otrzymuję powiadomienie e-mail dotyczące alertu, ale po zalogowaniu się do Azure Portal nie było alertu. Co się stało?

Jeśli alert zostanie rozwiązany, alert zostanie wyczyszczony z Azure Portal. Najbardziej prawdopodobną przyczyną jest to, że ktoś inny otrzymuje powiadomienia e-mail o rozwiązaniu alertu w domenie zarządzanej lub został automatycznie rozwiązany przez platformę Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Dlaczego nie mogę edytować ustawień powiadomień?

Jeśli nie możesz uzyskać dostępu do strony ustawień powiadomień w Azure Portal, nie masz uprawnień do edytowania domeny zarządzanej. Skontaktuj się z administratorem globalnym, aby uzyskać uprawnienia do edytowania zasobu usługi Azure AD DS lub usunąć go z listy adresatów.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Nie otrzymuję powiadomień e-mail, mimo że podałem mój adres e-mail. Dlaczego?

Sprawdź spam lub folder wiadomości-śmieci w wiadomości e-mail dotyczącej powiadomienia i upewnij się, że nadawca programu ma zezwalać `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów, które mogą być zgłaszane, zobacz temat [Rozwiązywanie alertów w domenie zarządzanej][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
