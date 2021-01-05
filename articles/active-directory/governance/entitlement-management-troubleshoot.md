---
title: Rozwiązywanie problemów z zarządzaniem uprawnieniami — Azure AD
description: Dowiedz się więcej na temat niektórych elementów, które należy sprawdzić, aby ułatwić rozwiązywanie problemów Azure Active Directory Zarządzanie prawami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bca78195d4fb7bb265c4a7fd8d78ea49a35335b6
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746715"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Rozwiązywanie problemów z zarządzaniem prawami usługi Azure AD

W tym artykule opisano niektóre elementy, które należy zaznaczyć, aby pomóc w rozwiązywaniu problemów z zarządzaniem prawami Azure Active Directory (Azure AD).

## <a name="administration"></a>Administracja

* Jeśli zostanie wyświetlony komunikat o odmowie dostępu podczas konfigurowania zarządzania uprawnieniami, a jesteś administratorem globalnym, upewnij się, że katalog ma [licencję na Azure AD — wersja Premium P2 (lub EMS E5)](entitlement-management-overview.md#license-requirements).

* Jeśli podczas tworzenia lub wyświetlania pakietów dostępu zostanie wyświetlony komunikat Odmowa dostępu, a użytkownik jest członkiem grupy twórców katalogu, należy [utworzyć wykaz](entitlement-management-catalog-create.md) przed utworzeniem pierwszego pakietu dostępu.

## <a name="resources"></a>Zasoby

* Role aplikacji są definiowane przez samą aplikację i są zarządzane w usłudze Azure AD. Jeśli aplikacja nie ma żadnych ról zasobów, zarządzanie uprawnieniami przypisuje użytkowników do domyślnej roli **dostępu** .

    Należy zauważyć, że Azure Portal mogą również wyświetlać jednostki usługi dla usług, których nie można wybrać jako aplikacji.  W szczególności usługi **Exchange Online** i **SharePoint Online** są usługami, a nie aplikacjami, które mają role zasobów w katalogu, więc nie mogą być uwzględnione w pakiecie dostępu.  Zamiast tego użyj licencjonowania opartego na grupach w celu uzyskania odpowiedniej licencji dla użytkownika, który potrzebuje dostępu do tych usług.

* Aby grupa mogła być zasobem w pakiecie dostępu, musi być możliwa do zmodyfikowania w usłudze Azure AD.  Grupy, które pochodzą z lokalnej usługi Active Directory, nie mogą być przypisane jako zasoby, ponieważ nie można zmienić ich atrybutów właściciela ani członków w usłudze Azure AD.   Grupy, które pochodzą z usługi Exchange Online jako grupy dystrybucji, również nie mogą być modyfikowane w usłudze Azure AD. 

* Biblioteki dokumentów usługi SharePoint Online i pojedyncze dokumenty nie mogą być dodawane jako zasoby.  Zamiast tego należy utworzyć [grupę zabezpieczeń usługi Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), dołączyć tę grupę i rolę lokacji w pakiecie dostępu, a w usłudze SharePoint Online używać tej grupy do kontrolowania dostępu do biblioteki dokumentów lub dokumentu.

* Jeśli istnieją użytkownicy, którzy zostali już przypisani do zasobu, którym chcesz zarządzać za pomocą pakietu dostępu, upewnij się, że użytkownicy są przypisani do pakietu dostępu przy użyciu odpowiednich zasad. Na przykład możesz uwzględnić grupę w pakiecie dostępu, który ma już użytkowników w grupie. Jeśli Ci użytkownicy w grupie wymagają ciągłego dostępu, muszą mieć odpowiednie zasady dla pakietów dostępu, aby nie utracić dostępu do grupy. Pakiet dostępu można przypisać, prosząc użytkowników o zażądanie pakietu dostępu zawierającego ten zasób lub przez bezpośrednie przypisanie ich do pakietu dostępu. Aby uzyskać więcej informacji, zobacz temat [Zmiana ustawień żądania i zatwierdzania dla pakietu dostępu](entitlement-management-access-package-request-policy.md).

* Po usunięciu członka zespołu są one również usuwane z grupy Microsoft 365. Usunięcie z funkcji czatu zespołu może być opóźnione. Aby uzyskać więcej informacji, zobacz [członkostwo w grupie](/microsoftteams/office-365-groups#group-membership).

* Upewnij się, że katalog nie jest skonfigurowany do obsługi wielu regionów geograficznych. Zarządzanie upoważnieniami nie obsługuje obecnie wielu lokalizacji geograficznych na potrzeby usługi SharePoint Online. Lokacje usługi SharePoint Online muszą znajdować się w domyślnej lokalizacji geograficznej, aby podlegać zarządzaniu upoważnieniami. Aby uzyskać więcej informacji, zobacz [wiele geograficznie funkcji w usłudze OneDrive i SharePoint Online](/Microsoft 365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Pakiety dostępu

* W przypadku próby usunięcia pakietu lub zasad dostępu oraz wyświetlenia komunikatu o błędzie informującego, że istnieją aktywne przypisania, jeśli nie widzisz żadnych użytkowników z przypisaniami, sprawdź, czy wszyscy ostatnio usunięci użytkownicy nadal mają przydziały. W oknie 30-dniowym po usunięciu użytkownika można przywrócić konto użytkownika.   

## <a name="external-users"></a>Użytkownicy zewnętrzni

* Gdy użytkownik zewnętrzny chce zażądać dostępu do pakietu dostępu, upewnij się, że korzysta on z **linku my Access Portal** dla pakietu dostępu. Aby uzyskać więcej informacji, zobacz [udostępnianie linku do żądania pakietu dostępu](entitlement-management-access-package-settings.md). Jeśli użytkownik zewnętrzny odwiedza tylko **myaccess.Microsoft.com** i nie korzysta z pełnego linku Portal dostępu do portalu, zobaczy dostępne dla nich pakiety dostępu w swojej organizacji, a nie w organizacji.

* Jeśli użytkownik zewnętrzny nie może zażądać dostępu do pakietu dostępu lub nie może uzyskać dostępu do zasobów, należy sprawdzić [Ustawienia dla użytkowników zewnętrznych](entitlement-management-external-users.md#settings-for-external-users).

* Jeśli nowy użytkownik zewnętrzny, który nie został wcześniej podpisany w Twoim katalogu, odbierze pakiet dostępu zawierający lokację usługi SharePoint Online, jego pakiet dostępu będzie wyświetlany jako nie w pełni dostarczony do momentu aprowizacji konta w usłudze SharePoint Online. Aby uzyskać więcej informacji na temat ustawień udostępniania, zobacz [Przegląd ustawień udostępniania zewnętrznego usługi SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Żądania

* Gdy użytkownik chce zażądać dostępu do pakietu dostępu, należy się upewnić, że korzysta on z **linku my Access Portal** dla pakietu dostępu. Aby uzyskać więcej informacji, zobacz [udostępnianie linku do żądania pakietu dostępu](entitlement-management-access-package-settings.md).

* Jeśli otworzysz portal Mój dostęp przy użyciu przeglądarki w trybie prywatnym lub incognito, może to spowodować konflikt z zachowaniem logowania. Zalecamy, aby nie używać trybu prywatnego ani incognito w przeglądarce podczas odwiedzania portalu Mój dostęp.

* Gdy użytkownik, którego nie ma jeszcze w katalogu, loguje się do portalu Mój dostęp w celu zażądania pakietu dostępu, upewnij się, że uwierzytelnia się przy użyciu konta organizacyjnego. Konto organizacyjne może być kontem w katalogu zasobów lub w katalogu, który znajduje się w jednej z zasad pakietu dostępu. Jeśli konto użytkownika nie jest kontem organizacyjnym lub katalog, w którym użytkownik się uwierzytelnia, nie jest uwzględniony w zasadach, wówczas użytkownik nie będzie widział pakietu dostępu. Aby uzyskać więcej informacji, zobacz [żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md).

* Jeśli użytkownik nie może się zalogować do katalogu zasobów, nie będzie w stanie zażądać dostępu w portalu Mój dostęp. Aby użytkownik mógł zażądać dostępu, musisz usunąć blokadę logowania z profilu użytkownika. Aby usunąć blok logowania, w Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Użytkownicy**, kliknij użytkownika, a następnie kliknij pozycję **profil**. Edytuj sekcję **Ustawienia** i Zmień **blok Zaloguj** się na **nie**. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Możesz również sprawdzić, czy użytkownik został zablokowany ze względu na [Zasady ochrony tożsamości](../identity-protection/howto-identity-protection-remediate-unblock.md).

* Jeśli użytkownik jest obiektem żądającym i osobą zatwierdzającą, w portalu My Access nie zobaczy żądania dotyczącego pakietu dostępu na stronie **zatwierdzenia** . Takie zachowanie jest celowe — użytkownik nie może zatwierdzić własnego żądania. Upewnij się, że żądany pakiet dostępu ma dodatkowe osoby zatwierdzające skonfigurowane dla zasad. Aby uzyskać więcej informacji, zobacz temat [Zmiana ustawień żądania i zatwierdzania dla pakietu dostępu](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Wyświetl błędy dostarczania żądania

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Wybierz żądanie, które chcesz wyświetlić.

    Jeśli żądanie zawiera błędy dostarczania, stan żądania zostanie **wystawiony** lub **częściowo dostarczony**.

    Jeśli występują błędy dostarczania, w okienku szczegółów żądania zostanie wyświetlona liczba błędów dostarczania.

1. Kliknij liczbę, aby wyświetlić wszystkie błędy dostarczania żądania.

### <a name="reprocess-a-request"></a>Przetwórz ponownie żądanie

Jeśli po wyzwoleniu żądania przetworzenia pakietu dostępu zostanie spełniony błąd, należy poczekać, aż system ponownie przetworzy żądanie. System próbuje wiele razy przetworzyć kilka godzin, więc nie można wymusić ponownego przetwarzania w tym czasie. 

Można ponownie przetwarzać żądanie, które ma stan **dostawy zakończony niepowodzeniem** lub **częściowo dostarczone** i datę ukończenia mniejszą niż tydzień. Przycisk **Reprocess** zostanie wyszarzony w inny sposób.

![Przycisk Przetwórz ponownie wyszarzony](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Jeśli błąd zostanie rozwiązany w oknie prób, stan żądania zmieni się na **dostarczenie**. Żądanie zostanie przetworzone ponownie bez dodatkowych akcji od użytkownika.

- Jeśli błąd nie został usunięty podczas przedziału prób, stan żądania może być **Niepowodzenie dostawy** lub **częściowo dostarczone**. Następnie możesz użyć przycisku **Reprocess** . Przetworzenie żądania będzie miało siedem dni.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij żądanie, które chcesz ponownie przetworzyć.

1. W okienku Szczegóły żądania kliknij pozycję **Przetwórz ponownie żądanie**.

    ![Przetwórz ponownie żądanie zakończone niepowodzeniem](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Można anulować tylko oczekujące żądanie, które nie zostało jeszcze dostarczone lub którego dostarczenie nie powiodło się. Przycisk **Anuluj** zostałby wyszarzony w przeciwnym razie.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu, Menedżer pakietów dostępu lub Menedżer przypisań pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **żądania**.

1. Kliknij żądanie, które chcesz anulować.

1. W okienku Szczegóły żądania kliknij przycisk **Anuluj żądanie**.

## <a name="multiple-policies"></a>Wiele zasad

* Zarządzanie prawami odbywa się zgodnie z najniższymi najlepszymi rozwiązaniami. Gdy użytkownik zażąda dostępu do pakietu dostępu zawierającego wiele zasad, które mają zastosowanie, zarządzanie uprawnieniami obejmuje logikę ułatwiającą zapewnienie bardziej restrykcyjnych lub bardziej szczegółowych zasad w ramach zasad ogólnych. Jeśli zasady są ogólne, zarządzanie uprawnieniami może nie wyświetlać zasad do osoby żądającej lub automatycznie wybierać bardziej rygorystyczne zasady.

* Rozważmy na przykład pakiet dostępu zawierający dwie zasady dla pracowników wewnętrznych, w których obie zasady mają zastosowanie do osoby żądającej. Pierwsze zasady są przeznaczone dla konkretnych użytkowników, którzy zawierają zleceniodawcę. Druga zasada dotyczy wszystkich użytkowników w katalogu, do których należy użytkownik żądający. W tym scenariuszu pierwsze zasady są automatycznie wybierane dla obiektu żądającego, ponieważ jest bardziej rygorystyczne. Obiekt żądający nie ma opcji wyboru drugiej zasady.

* Gdy mają zastosowanie wiele zasad, automatycznie wybierane są zasady, które są wyświetlane na podstawie następującej logiki priorytetu:

    | Priorytet zasad | Zakres |
    | --- | --- |
    | P1 | Określeni użytkownicy i grupy w katalogu lub określonych połączonych organizacjach |
    | P2 | Wszyscy członkowie w katalogu (z wyłączeniem Gości) |
    | P3 | Wszyscy użytkownicy w katalogu (w tym Goście) lub określone połączone organizacje |
    | P4 | Wszystkie skonfigurowane połączone organizacje lub wszyscy użytkownicy (wszystkie połączone organizacje + Wszyscy nowi użytkownicy zewnętrzni) |
    
    Jeśli jakiekolwiek zasady znajdują się w kategorii o wyższym priorytecie, kategorie o niższym priorytecie są ignorowane. Aby zapoznać się z przykładem, jak wiele zasad o takim samym priorytecie jest wyświetlanych dla żądającego, zobacz [Wybieranie zasad](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem dla użytkowników zewnętrznych](entitlement-management-external-users.md)
- [Wyświetlanie raportów dotyczących sposobu, w jaki użytkownicy uzyskują dostęp w usłudze zarządzania uprawnieniami](entitlement-management-reports.md)