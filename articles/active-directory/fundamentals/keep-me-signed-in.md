---
title: Skonfigurować "pozostania w Twojej rejestracji?" Monituj o konta Azure Active Directory
description: Dowiedz się więcej o tym, jak nadal zalogowano (KMSI), co spowoduje wyświetlenie niezalogowanego konta? Monituj, sposób konfigurowania go w portalu Azure Active Directory i rozwiązywanie problemów z logowaniem.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: a82f81888828cb5edd42c37a6e8b2c2ee51fe603
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85339555"
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

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Przykład wpisu dziennika logowania z przerwaniem "nie wylogowuj mnie"":::

Aby uniemożliwić użytkownikom wyświetlanie przerwania, należy ustawić **opcję Pokaż na** wartość **nie** w ustawieniach zaawansowanych znakowania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych ustawień, które wpływają na limit czasu sesji logowania:

* Microsoft 365 — [limit czasu bezczynności sesji](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Dostęp warunkowy usługi Azure AD — [częstotliwość logowania użytkownika](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Azure Portal — [limit czasu nieaktywności poziomu katalogu](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
