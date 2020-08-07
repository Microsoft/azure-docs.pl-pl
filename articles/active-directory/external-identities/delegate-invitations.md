---
title: Włączanie zewnętrznych ustawień współpracy B2B — Azure AD
description: Dowiedz się, jak włączyć Active Directory zewnętrznej współpracy B2B i zarządzać osobami, które mogą zapraszać użytkowników-Gości. Aby delegować zaproszenia, użyj roli zapraszania gościa.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6a2c1a9b908503ee5afc2687ebef473ffed626a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909544"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Włączanie zewnętrznej współpracy B2B i zarządzanie osobami, które mogą zapraszać gości

W tym artykule opisano sposób włączania współpracy B2B Azure Active Directory (Azure AD) i określania osób, które mogą zapraszać Gości. Domyślnie wszyscy użytkownicy i Goście w katalogu mogą zapraszać Gości, nawet jeśli nie są przypisani do roli administratora. Ustawienia współpracy zewnętrznej pozwalają włączać lub wyłączać zaproszenia gościa dla różnych typów użytkowników w organizacji. Możesz również delegować zaproszenia do poszczególnych użytkowników, przypisując role zezwalające im na zapraszanie Gości.

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
6. Na stronie **Ustawienia współpracy zewnętrznej** wybierz zasady, które chcesz włączyć.

   ![Ustawienia zewnętrznej współpracy](./media/delegate-invitations/control-who-to-invite.png)

  - **Uprawnienia użytkowników-Gości są ograniczone**: te zasady określają uprawnienia dla Gości w katalogu. Wybierz opcję **tak** , aby blokować Gościom pewne zadania katalogu, takie jak wyliczanie użytkowników, grup lub innych zasobów katalogu. Wybierz pozycję **nie** , aby udzielić Gościom tego samego dostępu do danych katalogu jako zwykłych użytkowników w katalogu.
   - **Administratorzy i użytkownicy w roli zapraszania gościa mogą zapraszać**: aby umożliwić administratorom i użytkownikom w roli "zapraszanie gościa" zapraszać Gości, ustaw tę zasadę na **wartość tak**.
   - **Członkowie mogą zapraszać**: aby zezwolić innym członkom katalogu na zapraszanie Gości, ustaw dla tych zasad wartość **tak**.
   - **Goście mogą zapraszać**: aby umożliwić Gościom Zapraszanie innych Gości, ustaw dla tych zasad wartość **tak**.
   - **Włącz jednorazowy kod dostępu wiadomości e-mail dla Gości (wersja zapoznawcza)**: Aby uzyskać więcej informacji na temat funkcji jednorazowego kodu dostępu, zobacz [poczta e-mail jednorazowe uwierzytelnianie kodu dostępu (wersja zapoznawcza)](one-time-passcode.md).
   - **Ograniczenia dotyczące współpracy**: Aby uzyskać więcej informacji na temat zezwalania lub blokowania zaproszeń do określonych domen, zobacz [Zezwalanie lub blokowanie zaproszeń dla użytkowników B2B z określonych organizacji](allow-deny-list.md).
   
   > [!NOTE]
   > Jeśli **Członkowie mogą zaprosić** jest ustawiona na wartość **nie** , a **Administratorzy i użytkownicy w roli osoby zapraszające gościa** mają ustawioną wartość **tak**, użytkownicy w roli **zapraszania gościa** nadal będą mogli zapraszać Gości.

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

