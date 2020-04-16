---
title: Elementy wiadomości e-mail z zaproszeniem B2B — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Szablon wiadomości e-mail z zaproszeniem do współpracy usługi Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407212"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementy wiadomości e-mail z zaproszeniem do współpracy B2B — usługa Azure Active Directory

Wiadomości e-mail z zaproszeniem są kluczowym składnikiem, który ma na celu wprowadzenie partnerów jako użytkowników współpracy B2B w usłudze Azure AD. Chociaż nie jest [wymagane, aby wysłać wiadomość e-mail, aby zaprosić kogoś za pomocą współpracy B2B,](add-user-without-invite.md)w ten sposób daje użytkownikowi wszystkie informacje potrzebne do podjęcia decyzji o tym, czy zaakceptować zaproszenie. Daje im również link, do którego zawsze mogą się odwołać w przyszłości, gdy muszą wrócić do swoich zasobów.

![Zrzut ekranu przedstawiający wiadomość e-mail z zaproszeniem B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Ten nowy szablon wiadomości e-mail jest nadal wdrażany dla wszystkich dzierżawców, więc niektórzy dzierżawcy nadal używają starszego projektu. Do końca maja 2020 r. zaproszenia od wszystkich najemców będą korzystać z tego szablonu.

## <a name="explaining-the-email"></a>Objaśnienie wiadomości e-mail

Przyjrzyjmy się kilku elementom wiadomości e-mail, aby wiedzieć, jak najlepiej korzystać z ich możliwości.

### <a name="subject"></a>Podmiot

Temat wiadomości e-mail jest zgodny z tym wzorcem:

&lt;nazwa&gt; użytkownika poprosiła Cię o dostęp do aplikacji w ich organizacji.

### <a name="from-address"></a>Z adresu

Używamy wzorca podobnego do LinkedIn dla adresu Od. Ten wzorzec powinien jasno powiedzieć, invites@microsoft.comże chociaż wiadomość e-mail pochodzi z , zaproszenie pochodzi z innej organizacji. Format jest: Zaproszenia <invites@microsoft.com> firmy Microsoft lub &lt;zaproszenia&gt; <invites@microsoft.com>firmy Microsoft w imieniu tenantname . 

### <a name="reply-to"></a>Odpowiedz na

Wiadomość e-mail z odpowiedzią jest ustawiona na adres e-mail osoby zapraszającej, gdy jest dostępna, dzięki czemu odpowiadanie na wiadomość e-mail odsyła wiadomość e-mail z powrotem do osoby zapraszającej.

### <a name="phishing-warning"></a>Ostrzeżenie o wyłudzaniu informacji

Wiadomość e-mail zaczyna się od krótkiego ostrzeżenia dla użytkownika o wyłudzaniu informacji, ostrzegając go, że powinien akceptować tylko oczekujące zaproszenia. Dobrą praktyką jest upewnienie się, że zaproszeni partnerzy nie będą zaskoczeni twoim zaproszeniem, wspominając je z wyprzedzeniem.

![Obraz ostrzeżenia o wyłudzaniu informacji w wiadomości e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informacje o zaproszeniu

Wiadomość e-mail zawiera informacje o zaproszeniu i organizacji, od której wysyłają zaproszenie. Obejmuje to nazwę nadawcy i adres e-mail, a także nazwę i domenę podstawową skojarzoną z organizacją. Wszystkie te informacje powinny pomóc zaproszonemu w podejmowaniu świadomej decyzji o przyjęciu zaproszenia.

![Obraz informacji o zaproszeniu w wiadomości e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Wiadomość z zaproszeniem

Jeśli osoba zapraszana zawiera wiadomość jako część zaproszenia, gdy [zaprosi użytkownika-gościa do katalogu, grupy lub aplikacji](add-users-administrator.md) lub gdy korzysta z interfejsu API [zaproszenia,](customize-invitation-api.md)wiadomość zostanie wyróżniona w głównej sekcji wiadomości e-mail. Również są nazwy zaproszonego i zdjęcie profilu, jeśli już ustawić jeden. Sama wiadomość jest obszarem tekstowym, więc ze względów bezpieczeństwa nie przetwarza tagów HTML.

![Obraz wiadomości z zaproszeniem w wiadomości e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Przycisk Zaakceptuj i adres URL przekierowania

Następna sekcja wiadomości e-mail zawiera informacje o tym, gdzie osoba zaproszona zostanie podjęta po zaakceptowaniu zaproszenia, a także przycisk, aby to zrobić.  W przyszłości zaproszony zawsze może użyć tego linku, aby bezpośrednio powrócić do zasobów.

![Obraz przycisku akceptuj i przekierowania adresu URL w wiadomości e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Przekrój stopki

Stopka zawiera więcej informacji na temat wysyłanego zaproszenia. Zawsze istnieje możliwość zablokowania przyszłych zaproszeń dla zaproszonych osób. Jeśli organizacja [ustawiła zasady zachowania poufności informacji,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)łącze do oświadczenia jest wyświetlane w tym miejscu.  W przeciwnym razie notatka wskazuje, że organizacja nie ustawiła zasad zachowania poufności informacji.

![Obraz sekcji stopki w wiadomości e-mail](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>Jak określa się język

Język przedstawiony użytkownikowi-gościowi w wiadomości e-mail z zaproszeniem zależy od następujących ustawień. Te ustawienia są wyświetlane w kolejności pierwszeństwa. Jeśli ustawienie nie jest skonfigurowane, następne ustawienie na liście określa język.

- **MessageLanguage** właściwość [zaproszonegouserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) obiektu, jeśli create invitation API jest używany
-   Właściwość **preferredLanguage** określona w [obiekcie użytkownika](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) gościa
-   **Język powiadomień** ustawiony we właściwościach dzierżawy domowej użytkownika-gościa (tylko dla dzierżaw usługi Azure AD)
-   **Język powiadomień** ustawiony we właściwościach dzierżawy zasobów

Jeśli żadne z tych ustawień nie jest skonfigurowane, domyślnie język jest domyślny na angielski (US).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Co to jest współpraca usługi Azure AD B2B](what-is-b2b.md)
- [Jak administratorzy usługi Azure Active Directory dodawać użytkowników współpracy B2B?](add-users-administrator.md)
- [W jaki sposób pracownicy informacji dodają użytkowników współpracy B2B?](add-users-information-worker.md)
- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)
