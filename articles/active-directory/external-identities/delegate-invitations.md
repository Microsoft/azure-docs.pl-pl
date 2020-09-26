---
title: Włączanie zewnętrznych ustawień współpracy B2B — Azure AD
description: Dowiedz się, jak włączyć Active Directory zewnętrznej współpracy B2B i zarządzać osobami, które mogą zapraszać użytkowników-Gości. Aby delegować zaproszenia, użyj roli zapraszania gościa.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb0147af559d9de4a8589344d61b06368086dd20
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278791"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Włączanie zewnętrznej współpracy B2B i zarządzanie osobami, które mogą zapraszać gości

W tym artykule opisano sposób włączania współpracy B2B Azure Active Directory (Azure AD), wyznaczania osób, które mogą zapraszać Gości i określić uprawnienia, które użytkownicy-Goście znajdują w usłudze Azure AD. 

Domyślnie wszyscy użytkownicy i Goście w katalogu mogą zapraszać Gości, nawet jeśli nie są przypisani do roli administratora. Ustawienia współpracy zewnętrznej pozwalają włączać lub wyłączać zaproszenia gościa dla różnych typów użytkowników w organizacji. Możesz również delegować zaproszenia do poszczególnych użytkowników, przypisując role zezwalające im na zapraszanie Gości.

Usługa Azure AD pozwala ograniczyć liczbę użytkowników, którzy zewnętrzni mogą zobaczyć w katalogu usługi Azure AD. Domyślnie użytkownicy-Goście są ustawiani z ograniczonym poziomem uprawnień, który blokuje ich wyliczanie użytkowników, grup lub innych zasobów katalogu, ale pozwala im na wyświetlanie członkostwa w grupach nieukrytych. Nowe ustawienie wersji zapoznawczej pozwala jeszcze bardziej ograniczyć dostęp gościa, aby Goście mogli tylko wyświetlać własne informacje o profilu. Aby uzyskać szczegółowe informacje, zobacz [ograniczanie uprawnień dostępu gościa (wersja zapoznawcza)](../users-groups-roles/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurowanie ustawień współpracy zewnętrznej B2B

Dzięki funkcji współpracy w usłudze Azure AD B2B Administrator dzierżawy może ustawić następujące zasady dotyczące zaproszenia:

- Wyłącz zaproszenia
- Tylko Administratorzy i użytkownicy w roli zapraszania gościa mogą zapraszać
- Administratorzy, rola zapraszania gościa i członkowie mogą zapraszać
- Wszyscy użytkownicy, w tym Goście, mogą zapraszać

Domyślnie wszyscy użytkownicy, w tym Goście, mogą zapraszać użytkowników-Gości.

### <a name="to-configure-external-collaboration-settings"></a>Aby skonfigurować ustawienia współpracy zewnętrznej:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator dzierżawy.
2. Wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **tożsamości zewnętrzne**  >  **Ustawienia współpracy zewnętrznej**.

4. W obszarze **ograniczenia dostępu użytkowników-Gości (wersja zapoznawcza)** wybierz poziom dostępu, który mają mieć użytkownicy-Goście:

   - **Użytkownicy-Goście mają taki sam dostęp jak członkowie (z największym**uwzględnieniem): Ta opcja zapewnia Gościom taki sam dostęp do zasobów usługi Azure AD i danych katalogu jako użytkowników-członków.

   - **Użytkownicy-Goście mają ograniczony dostęp do właściwości i członkostw obiektów katalogu**: (ustawienie domyślne) to ustawienie powoduje zablokowanie Gości z określonych zadań katalogu, takich jak wyliczanie użytkowników, grup lub innych zasobów katalogu. Goście mogą zobaczyć członkostwo wszystkich nieukrytych grup.

   - **Dostęp użytkownika-gościa jest ograniczony do właściwości i członkostw własnych obiektów katalogu (najbardziej restrykcyjne)**: w przypadku tego ustawienia Goście mogą uzyskiwać dostęp tylko do własnych profilów. Goście nie mogą wyświetlać profilów innych użytkowników, grup ani członkostwa w grupach.
  
    ![Ustawienia ograniczeń dostępu użytkowników-Gości](./media/delegate-invitations/guest-user-access.png)

5. W obszarze **Ustawienia zaproszenia gościa**wybierz odpowiednie ustawienia:

   - **Administratorzy i użytkownicy w roli zapraszania gościa mogą zapraszać**: aby umożliwić administratorom i użytkownikom w roli "zapraszanie gościa" zapraszać Gości, ustaw tę zasadę na **wartość tak**.

   - **Członkowie mogą zapraszać**: aby zezwolić innym członkom katalogu na zapraszanie Gości, ustaw dla tych zasad wartość **tak**.

   - **Goście mogą zapraszać**: aby umożliwić Gościom Zapraszanie innych Gości, ustaw dla tych zasad wartość **tak**.

   - **Włącz jednorazowy kod dostępu wiadomości e-mail dla Gości (wersja zapoznawcza)**: Aby uzyskać więcej informacji na temat funkcji jednorazowego kodu dostępu, zobacz [poczta e-mail jednorazowe uwierzytelnianie kodu dostępu (wersja zapoznawcza)](one-time-passcode.md).

   - Włącz samoobsługowe **Rejestrowanie się Gościa za pomocą przepływów użytkownika (wersja zapoznawcza)**: Aby uzyskać więcej informacji na temat tego ustawienia, zobacz [Dodawanie przepływu użytkownika samoobsługowego tworzenia konta do aplikacji (wersja zapoznawcza)](self-service-sign-up-user-flow.md).

   > [!NOTE]
   > Jeśli **Członkowie mogą zaprosić** jest ustawiona na wartość **nie** , a **Administratorzy i użytkownicy w roli osoby zapraszające gościa** mają ustawioną wartość **tak**, użytkownicy w roli **zapraszania gościa** nadal będą mogli zapraszać Gości.

    ![Ustawienia zapraszania gościa](./media/delegate-invitations/guest-invite-settings.png)

6. W obszarze **ograniczenia dotyczące współpracy**wybierz, czy zezwalać na zaproszenia do określonych domen, czy odrzucać je. Aby uzyskać więcej informacji, zobacz [Zezwalanie lub blokowanie zaproszeń użytkownikom B2B z określonych organizacji](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Przypisywanie roli osoby zapraszające gościa do użytkownika

Za pomocą roli osoby zapraszające gościa można udzielić indywidualnym użytkownikom możliwości zapraszania Gości bez przypisywania im administratora globalnego lub innej roli administratora. Przypisz rolę zapraszania gościa do poszczególnych użytkowników. Upewnij się, że ustawione **Administratorzy i użytkownicy w roli zapraszania gościa mogą zapraszać** na **wartość tak**.

Oto przykład, w którym pokazano, jak za pomocą programu PowerShell dodać użytkownika do roli zapraszania gościa:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi współpracy B2B w usłudze Azure AD:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników-Gości współpracy B2B bez zaproszenia](add-user-without-invite.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)

