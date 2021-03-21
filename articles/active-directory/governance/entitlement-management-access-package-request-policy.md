---
title: Ustawienia żądania zmiany dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania — Azure Active Directory
description: Dowiedz się, jak zmienić ustawienia żądania dla pakietu dostępu w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6bc00af8d54c8748dd82b934974282e0e8da0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426964"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ustawienia żądania zmiany dla pakietu dostępu w usłudze Azure AD uprawnienia zarządzania

Jako Menedżer pakietów dostępu można zmienić użytkowników, którzy mogą zażądać pakietu dostępu w dowolnym momencie, edytując zasady lub dodając nowe zasady. W tym artykule opisano sposób zmiany ustawień żądania dla istniejącego pakietu dostępu.

## <a name="choose-between-one-or-multiple-policies"></a>Wybierz jedną lub wiele zasad

Sposób określania, kto może zażądać pakietu dostępu, jest zasadą. Przed utworzeniem nowych zasad lub edytowaniem istniejących zasad w pakiecie dostępu należy określić, ile zasad potrzebuje pakiet dostępu. 

Podczas tworzenia pakietu dostępu należy określić ustawienie żądania, które tworzy zasady. Większość pakietów dostępu będzie mieć pojedyncze zasady, ale jeden pakiet dostępu może mieć wiele zasad. Należy utworzyć wiele zasad dla pakietu dostępu, jeśli chcesz zezwolić różnym zestawom użytkowników na przyznawanie przypisań z różnymi ustawieniami żądania i zatwierdzania. 

Na przykład pojedyncze zasady nie mogą być używane do przypisywania użytkowników wewnętrznych i zewnętrznych do tego samego pakietu dostępu. Można jednak utworzyć dwie zasady w tym samym pakiecie dostępu — jeden dla użytkowników wewnętrznych i jeden dla użytkowników zewnętrznych. Jeśli istnieje wiele zasad, które mają zastosowanie do użytkownika, zostanie wyświetlony monit o określenie zasad, do których mają być przypisane. Na poniższym diagramie przedstawiono pakiet dostępu z dwoma zasadami.

![Wiele zasad w pakiecie dostępu](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Ile zasad będzie potrzebnych?

| Scenariusz | Liczba zasad |
| --- | --- |
| Chcę, aby wszyscy użytkownicy w katalogu mieli te same ustawienia żądania i zatwierdzania dla pakietu dostępu | Jeden |
| Chcę, aby wszyscy użytkownicy w niektórych połączonych organizacjach mogli żądać pakietu dostępu | Jeden |
| Chcę zezwolić użytkownikom w moim katalogu, a także użytkownikom spoza mojego katalogu na żądanie pakietu dostępu | Wiele |
| Chcę określić różne ustawienia zatwierdzania dla niektórych użytkowników | Wiele |
| Chcę, aby niektórzy użytkownicy mieli dostęp do przypisań pakietów, a inni użytkownicy mogą ją rozciągnąć | Wiele |

Aby uzyskać informacje o logice priorytetu, która jest używana w przypadku stosowania wielu zasad, zobacz [wiele zasad](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Otwórz istniejący pakiet dostępu i Dodaj nowe zasady dla ustawień żądania

Jeśli masz zestaw użytkowników, którzy powinni mieć różne ustawienia żądań i zatwierdzeń, prawdopodobnie trzeba będzie utworzyć nowe zasady. Wykonaj następujące kroki, aby rozpocząć dodawanie nowych zasad do istniejącego pakietu dostępu:

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie **Dodaj zasady**.

1. Na karcie **podstawy** zostanie uruchomiony program. Wpisz nazwę i opis zasad.

    ![Tworzenie zasad o nazwie i opisie](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknij przycisk **dalej** , aby otworzyć kartę **żądania** .

1. Zmień **użytkowników, którzy mogą żądać ustawienia dostępu** . Wykonaj kroki opisane w poniższych sekcjach, aby zmienić ustawienie na jedną z następujących opcji: 
    - [Dla użytkowników w katalogu](#for-users-in-your-directory) 
    - [Dla użytkowników nieznajdujących się w katalogu](#for-users-not-in-your-directory)
    - [Brak (tylko przypisania bezpośrednie do administratorów)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Dla użytkowników w katalogu

Wykonaj te kroki, jeśli chcesz zezwolić użytkownikom w katalogu na żądanie tego pakietu dostępu. Podczas definiowania zasad żądań można określić poszczególnych użytkowników lub bardziej często grup użytkowników. Na przykład organizacja może mieć już grupę, taką jak **Wszyscy pracownicy**.  Jeśli ta grupa zostanie dodana w zasadach dla użytkowników, którzy mogą żądać dostępu, wówczas każdy członek tej grupy może następnie zażądać dostępu.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników w katalogu**.

    Po wybraniu tej opcji pojawią się nowe opcje, które umożliwią dokładniejsze określenie, kto w katalogu może zażądać tego pakietu dostępu.

    ![Dostęp do pakietów — dla użytkowników w katalogu](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Konkretni użytkownicy i grupy** | Wybierz tę opcję, jeśli chcesz, aby tylko użytkownicy i grupy w Twoim katalogu mogli żądać tego pakietu dostępu. |
    | **Wszyscy członkowie (z wyłączeniem Gości)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy należący do katalogu mogli żądać tego pakietu dostępu. Ta opcja nie obejmuje żadnych użytkowników-Gości, którzy zostali zaproszeni do katalogu. |
    | **Wszyscy użytkownicy (w tym Goście)** | Wybierz tę opcję, jeśli chcesz, aby wszyscy użytkownicy i Goście w katalogu mogli żądać tego pakietu dostępu. |

    Użytkownicy-Goście odwołują się do użytkowników zewnętrznych, którzy zostali zaproszeni do katalogu za pomocą [usługi Azure AD B2B](../external-identities/what-is-b2b.md). Aby uzyskać więcej informacji o różnicach między użytkownikami należącymi do członków i użytkownikami-Gośćmi, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. W przypadku wybrania **określonych użytkowników i grup** kliknij pozycję **Dodaj użytkowników i grupy**.

1. W okienku wybierz użytkowników i grupy wybierz użytkowników i grupy, które chcesz dodać.

    ![Dostęp do pakietu-żądania — Wybieranie użytkowników i grup](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Kliknij pozycję **Wybierz** , aby dodać użytkowników i grupy.

1. Aby wymagać zatwierdzenia, należy wykonać czynności opisane w sekcji [Zmiana ustawień zatwierdzania pakietu dostępu w usłudze Azure AD uprawnień zarządzanie](entitlement-management-access-package-approval-policy.md) , aby skonfigurować ustawienia zatwierdzania.

1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>Dla użytkowników nieznajdujących się w katalogu

 Użytkownicy, którzy nie znajdują się **w katalogu** , odwołują użytkowników znajdujących się w innym katalogu lub domenie usługi Azure AD. Ci użytkownicy mogą nie zostać zaproszeni do katalogu. Katalogi usługi Azure AD muszą być skonfigurowane tak, aby zezwalać na zaproszenia w **ograniczeniach współpracy**. Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](../external-identities/delegate-invitations.md).

> [!NOTE]
> Konto użytkownika-gościa zostanie utworzone dla użytkownika, który jeszcze nie znajduje się w katalogu, którego żądanie jest zatwierdzone lub zaakceptowane. Gość zostanie zaproszony, ale nie otrzyma wiadomości e-mail z zaproszeniem. Zamiast tego otrzymają wiadomość e-mail, gdy zostanie dostarczone przypisanie pakietu dostępu. Domyślnie, gdy użytkownik tego gościa nie ma już żadnych przypisań pakietów dostępu, ponieważ ostatnie przypisanie wygasło lub zostało anulowane, konto użytkownika Gość zostanie zablokowane przed zalogowaniem się i usunięciem. Jeśli chcesz, aby użytkownicy-Goście pozostali w Twoim katalogu przez czas nieokreślony, nawet jeśli nie mają przypisanych pakietów dostępu, możesz zmienić ustawienia konfiguracji zarządzania uprawnieniami. Aby uzyskać więcej informacji na temat obiektu użytkownika-gościa, zobacz [właściwości Azure Active Directory użytkownika współpracy B2B](../external-identities/user-properties.md).

Wykonaj następujące kroki, aby zezwolić użytkownikom, którzy nie należą do katalogu, na żądanie tego pakietu dostępu:

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **dla użytkowników, którzy nie są w Twoim katalogu**.

    Po wybraniu tej opcji pojawiają się nowe opcje.

    ![Dostęp do pakietów — dla użytkowników spoza katalogu](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Wybierz jedną z następujących opcji:

    |  |  |
    | --- | --- |
    | **Określone organizacje połączone** | Wybierz tę opcję, jeśli chcesz wybrać z listy organizacji, które wcześniej dodaliśmy do administratora. Wszyscy użytkownicy wybranych organizacji mogą zażądać tego pakietu dostępu. |
    | **Wszystkie skonfigurowane połączone organizacje** | Wybierz tę opcję, jeśli wszyscy użytkownicy ze wszystkich skonfigurowanych połączonych organizacji mogą żądać tego pakietu dostępu. Tylko użytkownicy z skonfigurowanych połączonych organizacji mogą żądać pakietów dostępu, które są widoczne dla użytkowników ze wszystkich skonfigurowanych organizacji. |
    | **Wszyscy użytkownicy (wszystkie połączone organizacje + Wszyscy nowi użytkownicy zewnętrzni)** | Wybierz tę opcję, jeśli dowolny użytkownik w Internecie powinien mieć możliwość zażądania tego pakietu dostępu.  Jeśli nie należy do połączonej organizacji w katalogu, połączona organizacja zostanie automatycznie utworzona dla nich po zażądaniu pakietu. Automatycznie utworzona podłączona organizacja będzie w stanie **proponowanym** . Aby uzyskać więcej informacji na temat proponowanego stanu, zobacz [Właściwości stanu połączonych organizacji](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Połączona organizacja to zewnętrzny katalog usługi Azure AD lub domena, z którą istnieje relacja.

1. W przypadku wybrania opcji **określone połączone organizacje** kliknij pozycję **Dodaj katalogi** , aby wybrać z listy połączonych organizacji, które zostały wcześniej dodane przez administratora.

1. Wpisz nazwę lub nazwę domeny, aby wyszukać połączoną wcześniej organizację.

    ![Dostęp do pakietu-żądania — wybierz katalogi](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Jeśli organizacja, z którą chcesz współpracować, nie znajduje się na liście, możesz poprosił administratora, aby dodał ją jako połączoną organizację. Aby uzyskać więcej informacji, zobacz [Dodawanie połączonej organizacji](entitlement-management-organization.md).

1. Po wybraniu wszystkich połączonych organizacji kliknij pozycję **Wybierz**.

    > [!NOTE]
    > Wszyscy użytkownicy z wybranych połączonych organizacji będą mogli zażądać tego pakietu dostępu. Obejmuje to użytkowników w usłudze Azure AD ze wszystkich poddomen skojarzonych z organizacją, chyba że te domeny są blokowane przez listę dozwolonych lub zablokowanych warunków B2B platformy Azure. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](../external-identities/allow-deny-list.md).

1. Aby wymagać zatwierdzenia, należy wykonać czynności opisane w sekcji [Zmiana ustawień zatwierdzania pakietu dostępu w usłudze Azure AD uprawnień zarządzanie](entitlement-management-access-package-approval-policy.md) , aby skonfigurować ustawienia zatwierdzania.
 
1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Brak (tylko przypisania bezpośrednie do administratorów)

Wykonaj następujące kroki, aby obejść żądania dostępu i umożliwić administratorom bezpośrednie przypisanie określonych użytkowników do tego pakietu dostępu. Użytkownicy nie będą musieli żądać pakietu dostępu. Nadal można ustawiać ustawienia cyklu życia, ale nie ma żadnych ustawień żądania.

1. W sekcji **Użytkownicy, którzy mogą zażądać dostępu** , kliknij pozycję **Brak (tylko przypisania bezpośrednie administratora**).

    ![Dostęp do pakietów — brak przypisań tylko administrator](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Po utworzeniu pakietu dostępu można bezpośrednio przypisać określonych użytkowników wewnętrznych i zewnętrznych do pakietu dostępu. W przypadku określenia użytkownika zewnętrznego w katalogu zostanie utworzone konto użytkownika-gościa. Aby uzyskać informacje na temat bezpośredniego przypisywania użytkownika, zobacz [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](entitlement-management-access-package-assignments.md).

1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Otwieranie i edytowanie istniejących zasad ustawień żądania

Aby zmienić ustawienia żądań i zatwierdzeń dla pakietu dostępu, należy otworzyć odpowiednie zasady. Wykonaj następujące kroki, aby otworzyć i edytować ustawienia żądania dla pakietu dostępu:

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **zasady** , a następnie kliknij zasady, które chcesz edytować.

    Zostanie otwarte okienko Szczegóły zasad w dolnej części strony.

    ![Dostęp do programu Access — okienko Szczegóły zasad](./media/entitlement-management-shared/policy-details.png)

1. Kliknij przycisk **Edytuj** , aby edytować zasady.

    ![Dostęp do pakietu — edycja zasad](./media/entitlement-management-shared/policy-edit.png)

1. Kliknij kartę **żądania** , aby otworzyć ustawienia żądania.

1. Wykonaj kroki opisane w poprzednich sekcjach, aby zmienić ustawienia żądania zgodnie z potrzebami.

1. Przejdź do sekcji [Włączanie żądań](#enable-requests) .

## <a name="enable-requests"></a>Włącz żądania

1. Jeśli chcesz, aby pakiet dostępu był natychmiast dostępny dla użytkowników w zasadach żądania, przesuń przełącznik Enable na **wartość tak**.

    Zawsze możesz włączyć ją w przyszłości po zakończeniu tworzenia pakietu dostępu.

    W przypadku wybrania opcji **Brak (tylko przypisania bezpośrednie do administratorów)** i skonfigurowania opcji Włącz na **nie** Administratorzy nie mogą bezpośrednio przypisywać tego pakietu dostępu.

    ![Dostęp do pakietu — ustawienie zasad włączania zasad](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Kliknij przycisk **Dalej**.

1. Aby wymagać od osoby żądającej podania dodatkowych informacji podczas żądania dostępu do pakietu dostępu, należy wykonać czynności opisane w [ustawieniach informacje o zatwierdzeniu i zażądaniu (wersja zapoznawcza) dla pakietu dostępu w usłudze Azure AD uprawnienia](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) do konfigurowania informacji o żądającym (wersja zapoznawcza).

1. Skonfiguruj ustawienia cyklu życia.

1. Jeśli edytujesz zasady, kliknij przycisk **Aktualizuj**. Jeśli dodajesz nowe zasady, kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

- [Zmień ustawienia zatwierdzania dla pakietu dostępu](entitlement-management-access-package-approval-policy.md)
- [Zmień ustawienia cyklu życia pakietu dostępu](entitlement-management-access-package-lifecycle-policy.md)
- [Wyświetl żądania dla pakietu dostępu](entitlement-management-access-package-requests.md)
