---
title: Skonfigurować "pozostania w Twojej rejestracji?" Monituj o konta Azure Active Directory
description: Dowiedz się więcej o tym, jak nadal zalogowano (KMSI), co spowoduje wyświetlenie niezalogowanego konta? Monituj, sposób konfigurowania go w portalu Azure Active Directory i rozwiązywanie problemów z logowaniem.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89320260"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Skonfigurować "pozostania w Twojej rejestracji?" Monituj o konta usługi Azure AD

Nie wylogowuj mnie (KMSI) powoduje, że po pomyślnym zalogowaniu się użytkownika **zostanie wyświetlony monit** . Jeśli użytkownik **odbierze odpowiedź na ten** monit, usługa nie wylogowuj mnie podaje im token trwałego [odświeżenia](../develop/developer-glossary.md#refresh-token). W przypadku dzierżawców federacyjnych monit zostanie wyświetlony po pomyślnym uwierzytelnieniu użytkownika w usłudze tożsamości federacyjnej.

Na poniższym diagramie przedstawiono przepływ logowania użytkownika dla zarządzanej dzierżawy i dzierżawcy federacyjnego oraz nowy monit "nie wylogowuj mnie". Ten przepływ zawiera inteligentną logikę, dzięki czemu nie będzie można wyświetlić opcji **pozostania w** systemie, jeśli system uczenia maszynowego wykryje logowanie wysokiego ryzyka lub zalogowanie z urządzenia udostępnionego.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagram przedstawiający przepływ logowania użytkownika dla dzierżawy zarządzanej a federacyjnej":::

> [!NOTE]
> Skonfigurowanie opcji nie wylogowuj mnie wymaga użycia usługi Azure Active Directory (Azure AD) Premium 1, Premium 2 lub Basic albo posiadania licencji Microsoft 365. Aby uzyskać więcej informacji na temat licencjonowania i wydań, zobacz [Rejestrowanie się w usłudze Azure AD — wersja Premium](active-directory-get-started-premium.md).<br><br>Wersje Azure AD — wersja Premium i Basic są dostępne dla klientów z Chin przy użyciu wystąpienia usługi Azure AD na całym świecie. Wersje Premium i Podstawowa usługi Azure AD nie są obecnie obsługiwane w usłudze platformy Azure świadczonej przez firmę 21Vianet w Chinach. Aby uzyskać więcej informacji, porozmawiaj z nami przy użyciu [Forum usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="configure-kmsi"></a>Konfigurowanie KMSI

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.
1. Wybierz **Azure Active Directory**, wybierz **znakowanie firmowe**, a następnie wybierz pozycję **Konfiguruj**.
1. W sekcji **Ustawienia zaawansowane** Znajdź **opcję Pokaż, która ma pozostać w** ustawieniu.

   To ustawienie umożliwia określenie, czy użytkownicy pozostają zalogowani do usługi Azure AD do momentu jawnego wylogowania.
   * Jeśli wybierzesz opcję **nie**, pozostanie **zalogowany** , gdy użytkownik się zaloguje, a użytkownik musi się zalogować za każdym razem, gdy przeglądarka zostanie zamknięta i ponownie otwarta.
   * Jeśli wybierzesz opcję **tak**, użytkownik zostanie **zalogowany** .

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Zrzut ekranu przedstawia opcję Pokaż, która pozostanie niezalogowana":::

## <a name="troubleshoot-sign-in-issues"></a>Rozwiązywanie problemów z logowaniem

Jeśli użytkownik nie działa w wierszu **pozostawania z logowaniem?** , jak pokazano na poniższym diagramie, ale porzuca próbę logowania, zobaczysz wpis dziennika logowania wskazujący przerwanie.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Pokazuje, że zarejestrowano nadal? pytać":::

Szczegóły dotyczące błędu logowania są następujące i wyróżnione w przykładzie.

* **Kod błędu logowania**: 50140
* **Przyczyna niepowodzenia**: ten błąd wystąpił z powodu przerwania "nie wylogowuj mnie" podczas logowania użytkownika.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Przykład wpisu dziennika logowania z przerwaniem &quot;nie wylogowuj mnie&quot;":::

Aby uniemożliwić użytkownikom wyświetlanie przerwania, należy ustawić **opcję Pokaż na** wartość **nie** w ustawieniach zaawansowanych znakowania. Spowoduje to wyłączenie monitu KMSI dla wszystkich użytkowników w katalogu usługi Azure AD.

Można również użyć formantów sesji trwałej przeglądarki w dostępie warunkowym, aby uniemożliwić użytkownikom wyświetlenie monitu KMSI. Ta opcja umożliwia wyłączenie monitu KMSI dla wybranej grupy użytkowników (na przykład administratorów globalnych) bez wpływu na zachowanie logowania dla pozostałych użytkowników w katalogu. Aby uzyskać więcej informacji, zobacz [częstotliwość logowania użytkownika](../conditional-access/howto-conditional-access-session-lifetime.md). 

Aby upewnić się, że monit KMSI jest wyświetlany tylko wtedy, gdy może korzystać z użytkownika, monit KMSI nie jest zamierzony w następujących scenariuszach:

* Użytkownik jest zalogowany za pomocą bezproblemowego logowania jednokrotnego i zintegrowanego uwierzytelniania systemu Windows (IWA)
* Użytkownik jest zalogowany za pośrednictwem Active Directory Federation Services i IWA
* Użytkownik jest gościem w dzierżawie
* Ocena ryzyka użytkownika jest wysoka
* Logowanie odbywa się podczas przepływu zgody użytkownika lub administratora
* W zasadach dostępu warunkowego jest konfigurowana trwała kontrolka sesji przeglądarki

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych ustawień, które wpływają na limit czasu sesji logowania:

* Microsoft 365 — [limit czasu bezczynności sesji](/sharepoint/sign-out-inactive-users)
* Dostęp warunkowy usługi Azure AD — [częstotliwość logowania użytkownika](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure Portal — [limit czasu nieaktywności poziomu katalogu](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)