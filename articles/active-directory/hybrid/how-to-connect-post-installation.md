---
title: 'Azure AD Connect: Następne kroki i sposób zarządzania Azure AD Connect | Microsoft Docs'
description: Dowiedz się, jak zwiększyć domyślną konfigurację i zadania operacyjne dla Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657124"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Następne kroki i sposób zarządzania Azure AD Connect
Korzystając z procedur operacyjnych w tym artykule, można dostosować program Azure Active Directory (Azure AD) Connect, aby spełniał potrzeby i wymagania organizacji.  

## <a name="add-additional-sync-admins"></a>Dodawanie dodatkowych administratorów synchronizacji
Domyślnie tylko użytkownik, który wykonał instalację i Administratorzy lokalni, może zarządzać zainstalowanym aparatem synchronizacji. Aby dodatkowe osoby mogły uzyskać dostęp do aparatu synchronizacji i zarządzać nim, zlokalizuj grupę o nazwie ADSyncAdmins na serwerze lokalnym i Dodaj ją do tej grupy.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Przypisywanie licencji do Azure AD — wersja Premium i użytkowników pakietu Enterprise Mobility Suite
Teraz, gdy użytkownicy zostali zsynchronizowani z chmurą, należy przypisać im licencję, aby umożliwić im przechodzenie do aplikacji w chmurze, takich jak Microsoft 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Aby przypisać licencję na Azure AD — wersja Premium lub pakiet Enterprise Mobility Suite

1. Zaloguj się do Azure Portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Na stronie **Active Directory** kliknij dwukrotnie katalog zawierający użytkowników, których chcesz skonfigurować.
4. W górnej części strony katalogu wybierz pozycję **Licencje**.
5. Na stronie **licencje** wybierz pozycję **Active Directory Premium** lub **Enterprise Mobility Suite**, a następnie kliknij przycisk **Przypisz**.
6. W oknie dialogowym wybierz użytkowników, do których chcesz przypisać licencje, a następnie kliknij ikonę znacznika wyboru, aby zapisać zmiany.

## <a name="verify-the-scheduled-synchronization-task"></a>Weryfikowanie zaplanowanego zadania synchronizacji
Użyj Azure Portal, aby sprawdzić stan synchronizacji.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Aby zweryfikować zadanie zaplanowanej synchronizacji
1. Zaloguj się do Azure Portal jako administrator.
2. W obszarze po lewej stronie wybierz pozycję **Active Directory**.
3. Po lewej stronie wybierz pozycję **Azure AD Connect**
4. W górnej części strony Zwróć uwagę na ostatnią synchronizację.

![Czas synchronizacji katalogu](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Uruchamianie zaplanowanego zadania synchronizacji
Jeśli konieczne jest uruchomienie zadania synchronizacji, można to zrobić, wykonując następujące czynności:

1. Kliknij dwukrotnie skrót Azure AD Connect pulpicie, aby uruchomić kreatora.
2. Kliknij przycisk **Konfiguruj**.
3. Na ekranie zadania wybierz **Opcje Dostosuj synchronizację** i kliknij przycisk **dalej** .
4. Wprowadzanie poświadczeń usługi Azure AD
5. Kliknij przycisk **Dalej**. Kliknij przycisk **Dalej**.  Kliknij przycisk **Dalej**.
5.  Na ekranie **gotowy do konfiguracji** upewnij się, że jest zaznaczone pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji** .
6.  Kliknij przycisk **Konfiguruj**.

Aby uzyskać więcej informacji na temat harmonogramu synchronizacji Azure AD Connect, zobacz [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Dodatkowe zadania dostępne w Azure AD Connect
Po wstępnej instalacji Azure AD Connect można zawsze uruchomić ponownie Kreatora ze strony startowej Azure AD Connect lub skrótu pulpitu.  Zobaczysz, że przechodzenie przez kreatora ponownie zawiera nowe opcje w postaci dodatkowych zadań.  

Poniższa tabela zawiera podsumowanie tych zadań i krótkie opisy poszczególnych zadań.

![Lista dodatkowych zadań](./media/how-to-connect-post-installation/addtasks2.png)

| Dodatkowe zadanie | Opis |
| --- | --- |
|**Ustawienia prywatności**|Zobacz, jakie dane telemetryczne są udostępniane firmie Microsoft.|
|**Wyświetl bieżącą konfigurację**|Wyświetl bieżące rozwiązanie Azure AD Connect.  Dotyczy to ustawień ogólnych, synchronizowanych katalogów i ustawień synchronizacji. |
| **Dostosuj opcje synchronizacji** |Zmień bieżącą konfigurację, taką jak dodanie do konfiguracji dodatkowych lasów Active Directory lub włączenie opcji synchronizacji, takich jak użytkownik, Grupa, urządzenie lub zapisywanie zwrotne haseł. |
|**Konfiguruj opcje urządzenia**|Opcje urządzenia dostępne do synchronizacji|
|**Odśwież schemat katalogu**|Umożliwia dodawanie nowych obiektów katalogów lokalnych na potrzeby synchronizacji|
|**Konfigurowanie trybu przejściowego** |Informacje o etapie, które nie są natychmiast synchronizowane i nie są eksportowane do usługi Azure AD lub Active Directory lokalnych.  Korzystając z tej funkcji, można wyświetlić podgląd synchronizacji przed ich wystąpieniem. |
|**Zmień Logowanie użytkownika**|Zmień metodę uwierzytelniania używaną przez użytkowników do logowania|
|**Zarządzaj Federacją**|Zarządzanie infrastrukturą AD FS, Odnawianie certyfikatów i Dodawanie serwerów AD FS|
|**Rozwiązywanie problemów**|Pomoc dotycząca rozwiązywania problemów Azure AD Connect|

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej [na temat integrowania tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
