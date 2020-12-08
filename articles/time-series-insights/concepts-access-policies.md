---
title: Konfigurowanie zabezpieczeń w celu udzielenia dostępu do danych — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skonfigurować zabezpieczenia, uprawnienia i zarządzać zasadami dostępu do danych w środowisku Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780862"
---
# <a name="grant-data-access-to-an-environment"></a>Przyznawanie dostępu do danych w środowisku

W tym artykule omówiono dwa typy zasad dostępu Azure Time Series Insights.

## <a name="sign-in-to-azure-time-series-insights"></a>Zaloguj się do Azure Time Series Insights

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Znajdź środowisko Azure Time Series Insights, wprowadzając `Time Series Insights environments` w polu **wyszukiwania** . Wybierz `Time Series Insights environments` w wynikach wyszukiwania.
1. Wybierz z listy środowisko Azure Time Series Insights.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby udzielić dostępu do danych dla podmiotu zabezpieczeń.

1. Wybierz pozycję **zasady dostępu do danych**, a następnie wybierz pozycję **+ Dodaj**.

    [![Wybieranie i Dodawanie zasad dostępu do danych](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Wybierz **pozycję Wybierz użytkownika**. Wyszukaj nazwę użytkownika lub adres e-mail, aby zlokalizować użytkownika, który chcesz dodać. Wybierz pozycję **Wybierz** , aby potwierdzić wybór.

    [![Wybierz użytkownika do dodania](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika:

    * Wybierz opcję **współautor** , jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz opcję **czytelnik** , aby zezwolić użytkownikowi na wykonywanie zapytań dotyczących danych w środowisku i zapisywanie osobistych, nieudostępnianych zapytań w środowisku.

   Wybierz **przycisk OK** , aby potwierdzić wybór roli.

    [![Potwierdź wybraną rolę](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Na stronie **Wybieranie roli użytkownika** wybierz **przycisk OK** .

    [![Na stronie Wybieranie roli użytkownika wybierz przycisk OK](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Upewnij się, że na stronie **zasady dostępu do danych** są wyświetlane Użytkownicy i role dla każdego użytkownika.

    [![Weryfikowanie prawidłowych użytkowników i ról](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Zapewnianie dostępu gościa z innej dzierżawy usługi Azure AD

`Guest`Rola nie jest rolą zarządzania. Jest to termin używany w przypadku konta zaproszonego z jednej dzierżawy do innej. Po zaproszeniu konta gościa do katalogu dzierżawy może on mieć taką samą kontrolę dostępu, jak dowolne inne konto. Można udzielić dostępu do środowiska Azure Time Series Insights za pomocą bloku Access Control (IAM). Można też udzielić dostępu do danych w środowisku za pomocą bloku zasady dostępu do danych. Aby uzyskać więcej informacji na temat dostępu gościa dzierżawy Azure Active Directory (Azure AD), przeczytaj temat [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../active-directory/external-identities/add-users-administrator.md).

Wykonaj następujące kroki, aby przyznać dostęp gościa do środowiska Azure Time Series Insights do użytkownika usługi Azure AD z innej dzierżawy.

1. Przejdź do Azure Portal, kliknij przycisk  **Azure Active Directory**, przewiń w dół na karcie **Przegląd** , a następnie wybierz pozycję **użytkownik-Gość**.

    [![Wybierz zasady dostępu do danych, a następnie + Zaproś](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Wprowadź adres e-mail użytkownika, który chcesz zaprosić. Ten adres e-mail musi być skojarzony z usługą Azure AD. Opcjonalnie możesz dołączyć osobistą wiadomość z zaproszeniem.

    [![Wprowadź adres e-mail, aby znaleźć wybranego użytkownika](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Wyszukaj dymek potwierdzenia, który pojawia się na ekranie. Możesz również kliknąć pozycję **powiadomienia** , aby potwierdzić, że użytkownik-Gość został dodany.

    [![Wyszukaj dymek potwierdzenia do wyświetlenia](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Wróć do środowiska Time Series Insights, aby dodać nowo utworzonego użytkownika-gościa. Kliknij pozycję **zasady dostępu do danych** zgodnie z opisem w sekcji **przyznawanie dostępu do danych**. **Wybierz pozycję użytkownik**. Wyszukaj adres e-mail zaproszonego użytkownika-gościa, aby zlokalizować użytkownika, który chcesz dodać. Następnie **Wybierz pozycję** , aby potwierdzić wybór.

    [![Wybierz użytkownika i potwierdź wybór](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika-gościa:

    * Wybierz opcję **współautor** , jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz opcję **czytelnik** , aby zezwolić użytkownikowi na wykonywanie zapytań dotyczących danych w środowisku i zapisywanie osobistych, nieudostępnianych zapytań w środowisku.

   Wybierz **przycisk OK** , aby potwierdzić wybór roli.

    [![Potwierdź wybór roli](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Na stronie **Wybieranie roli użytkownika** wybierz **przycisk OK** .

1. Upewnij się, że na stronie **zasady dostępu do danych** jest wyświetlana lista użytkowników-Gości i role poszczególnych użytkowników-Gości.

    [![Sprawdź, czy użytkownicy i role są prawidłowo przypisani](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Teraz użytkownik-gość otrzyma wiadomość e-mail z zaproszeniem na podany powyżej adres e-mail. Użytkownik-Gość wybierze opcję **Rozpocznij** , aby potwierdzić akceptację i nawiązać połączenie z chmurą platformy Azure.

    [![Gość wybiera pozycję Rozpocznij, aby zaakceptować](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Po wybraniu pozycji **Rozpocznij** użytkownik zostanie wyświetlony z polem uprawnień skojarzonym z organizacją administratora. Po udzieleniu uprawnień przez wybranie pozycji **Akceptuj**, zostaną one zalogowane.

    [![Gość przegląda uprawnienia i akceptuje](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Administrator [współużytkuje adres URL środowiska](time-series-insights-parameterized-urls.md) z gościem.

1. Gdy użytkownik-Gość zostanie zalogowany na adres e-mail, który został użyty do jego zaproszenia i zaakceptuje zaproszenie, zostanie skierowany do Azure Portal.

1. Gość może teraz uzyskiwać dostęp do udostępnionego środowiska przy użyciu adresu URL środowiska udostępnionego przez administratora. Mogą oni wprowadzić ten adres URL w przeglądarce sieci Web, aby uzyskać natychmiastowy dostęp.

1. Dzierżawa administratora zostanie wyświetlona dla użytkownika-gościa po wybraniu ikony profilu w prawym górnym rogu Eksploratora szeregów czasowych.

    [![Wybór awatara w insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Po wybraniu dzierżawy przez użytkownika-gościa będzie można wybrać środowisko udostępnione Azure Time Series Insights.

    Mają teraz wszystkie możliwości skojarzone z rolą podaną w **kroku 5**.

    [![Użytkownik-Gość wybiera dzierżawę platformy Azure z listy rozwijanej](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Odczytaj [uwierzytelnianie i autoryzację](time-series-insights-authentication-and-authorization.md) dla Azure Active Directory kroków rejestracji aplikacji.

* Wyświetl [swoje środowisko w eksploratorze Azure Time Series Insights](./concepts-ux-panels.md).
