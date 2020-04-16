---
title: Konfigurowanie zabezpieczeń w celu przyznania dostępu do danych — usługa Azure Time Series Insights Preview | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady zabezpieczeń, uprawnień i zarządzać zasadami dostępu do danych w środowisku usługi Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 1735daf66483df496141a642ac6633973aa0abf0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407542"
---
# <a name="grant-data-access-to-an-environment"></a>Udzielanie dostępu do danych do środowiska

W tym artykule omówiono dwa typy zasad dostępu usługi Azure Time Series Insights Preview.

> [!TIP]
> Odczytanie [uwierzytelniania i autoryzacji](time-series-insights-authentication-and-authorization.md) dla kroków rejestracji aplikacji usługi Azure Active Directory.

## <a name="sign-in-to-time-series-insights"></a>Zaloguj się do usługi Time Series Insights

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Znajdź środowisko usługi Time Series Insights. Wprowadź `Time Series` w polu **Wyszukiwania.** Wybierz **środowiska szeregów czasowych** w wynikach wyszukiwania.
1. Wybierz środowisko usługi Time Series Insights z listy.

## <a name="grant-data-access"></a>Przyznawanie dostępu do danych

Wykonaj następujące kroki, aby udzielić dostępu do danych dla podmiotu.

1. Wybierz pozycję **Zasady dostępu do danych**, a następnie wybierz pozycję + **Dodaj**.

    [![Wybieranie i dodawanie zasad dostępu do danych](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Wybierz **pozycję Wybierz użytkownika**. Wyszukaj nazwę użytkownika lub adres e-mail, aby zlokalizować użytkownika, którego chcesz dodać. Wybierz **wybierz wybierz,** aby potwierdzić wybór.

    [![Wybierz użytkownika, który ma dodać](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika:

    * Wybierz **współautora,** jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **program Reader,** aby umożliwić użytkownikowi wykonywanie zapytań o dane w środowisku i zapisywanie osobistych, nieuwzwolonych zapytań w środowisku.

   Wybierz **przycisk OK,** aby potwierdzić wybór roli.

    [![Potwierdź wybraną rolę](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Wybierz **przycisk OK** na stronie Wybierz rolę **użytkownika.**

    [![Wybierz przycisk OK na stronie Wybierz rolę użytkownika](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Upewnij się, że strona **Zasady dostępu do danych** zawiera listę użytkowników i role dla każdego użytkownika.

    [![Weryfikowanie poprawnych użytkowników i ról](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Zapewnianie dostępu gościa z innej dzierżawy usługi Azure AD

Rola `Guest` nie jest rolą zarządzania. Jest to termin używany dla konta, które jest zaproszone z jednej dzierżawy do drugiej. Po zaproszona do katalogu dzierżawy konto gościa może mieć taką samą kontrolę dostępu, jak każde inne konto. Dostęp do środowiska usługi Time Series Insights można udzielić za pomocą bloku Kontrola dostępu (IAM). Lub można udzielić dostępu do danych w środowisku za pośrednictwem zasad dostępu do danych bloku. Aby uzyskać więcej informacji na temat dostępu gościa do dzierżawy usługi Azure Active Directory (Azure AD), zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Wykonaj następujące kroki, aby udzielić gościowi dostępu do środowiska usługi Time Series Insights użytkownikowi usługi Azure AD z innej dzierżawy.

1. Wybierz pozycję **Zasady dostępu do danych**, a następnie wybierz pozycję + **Zaproś**.

    [![Wybierz pozycję Raporty dostępu do danych, a następnie + Zaproś](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Wprowadź adres e-mail użytkownika, którego chcesz zaprosić. Ten adres e-mail musi być skojarzony z usługą Azure AD. Opcjonalnie można dołączyć wiadomość osobistą do zaproszenia.

    [![Wprowadź adres e-mail, aby znaleźć wybranego użytkownika](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Poszukaj dymka potwierdzenia, który pojawi się na ekranie.

    [![Poszukaj bańki potwierdzenia, która ma się pojawić](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Wybierz **pozycję Wybierz użytkownika**. Wyszukaj adres e-mail zaproszonego użytkownika-gościa, aby zlokalizować użytkownika, którego chcesz dodać. Następnie **wybierz,** aby potwierdzić wybór.

    [![Wybierz użytkownika i potwierdź wybór](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Wybierz **pozycję Wybierz rolę**. Wybierz odpowiednią rolę dostępu dla użytkownika-gościa:

    * Wybierz **współautora,** jeśli chcesz zezwolić użytkownikowi na zmianę danych referencyjnych i udostępnianie zapisanych zapytań i perspektyw innym użytkownikom środowiska.

    * W przeciwnym razie wybierz **program Reader,** aby umożliwić użytkownikowi wykonywanie zapytań o dane w środowisku i zapisywanie osobistych, nieuwzwolonych zapytań w środowisku.

   Wybierz **przycisk OK,** aby potwierdzić wybór roli.

    [![Potwierdź wybór roli](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Wybierz **przycisk OK** na stronie Wybierz rolę **użytkownika.**

1. Upewnij się, że na stronie **Zasady dostępu do danych** są wyszczególniane użytkownika-gościa i role dla każdego użytkownika-gościa.

    [![Sprawdzanie, czy użytkownicy i role są poprawnie przypisane](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Teraz użytkownik-gość otrzyma wiadomość e-mail z zaproszeniem na adres e-mail określony powyżej. Użytkownik-gość **wybierze wprowadzenie,** aby potwierdzić ich akceptację i połączyć się z usługą Azure Cloud.

    [![Gość wybiera Wprowadzenie do zaakceptowania](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Po wybraniu **opcji Wprowadzenie**użytkownik-gość zostanie wyświetlony z polem uprawnień skojarzonym z organizacją administratora. Po udzieleniu zgody, wybierając **opcję Zaakceptuj,** zostaną zalogowane.

    [![Gość przegląda uprawnienia i akceptuje](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Administrator [udostępnia adres URL środowiska](time-series-insights-parameterized-urls.md) z gościem.

1. Po zalogowaniu się użytkownika gościa na adres e-mail użyty do zaproszenia, a oni zaakceptują zaproszenie, zostaną one przekierowane do witryny Azure portal. 

1. Gość może teraz uzyskać dostęp do środowiska udostępnionego przy użyciu adresu URL środowiska dostarczonego przez administratora. Mogą wprowadzić ten adres URL w przeglądarce internetowej w celu uzyskania natychmiastowego dostępu.

1. Dzierżawy administratora będą wyświetlane do użytkownika gościa po wybraniu jego ikony profilu w prawym górnym rogu Eksploratora szeregów czasowych.

    [![Wybór awatara na insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    Po użytkownik-gość wybiera dzierżawy administratora, będą mieli możliwość wyboru udostępnionego środowiska usługi Time Series Insights. 
    
    Mają teraz wszystkie możliwości związane z rolą, którą im podałeś w **kroku 5**.

    [![Użytkownik-gość wybiera dzierżawę platformy Azure z listy rozwijanej](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak dodać źródło zdarzeń usługi Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska usługi Time Series Insights.

* Wyślij [zdarzenia do źródła zdarzenia](./time-series-insights-send-events.md).

* Wyświetlanie [środowiska w Eksploratorze wglądu w szczegółowe informacje o szeregach czasowych](./time-series-insights-update-explorer.md).
