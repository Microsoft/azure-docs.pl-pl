---
title: 'Samouczek: Konfigurowanie PHS jako kopii zapasowej AD FS w Azure AD Connect | Microsoft Docs'
description: Opisuje sposób włączania synchronizacji skrótów haseł jako zapasu dla usług AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9f59906c566d80344891c0796a85b0a4972e68
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313097"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Samouczek: Konfigurowanie PHS jako kopii zapasowej AD FS w Azure AD Connect

Następujący samouczek przeprowadzi Cię przez konfigurowanie synchronizacji skrótów haseł jako zapasu i trybu failover dla usług AD FS.  W tym dokumencie przedstawimy także sposób włączenia synchronizacji skrótów haseł jako podstawowej metody uwierzytelniania w przypadku awarii lub niedostępności usług AD FS.

>[!NOTE] 
>Chociaż te kroki są zwykle wykonywane w sytuacjach awaryjnych lub awarii, zaleca się przetestowanie tych kroków i zweryfikowanie procedur przed wystąpieniem awarii.

>[!NOTE]
>Jeśli nie masz dostępu do usługi Azure AD Connect Server lub serwer nie ma dostępu do Internetu, możesz skontaktować się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/en-us/contactus/) , aby pomóc w wprowadzeniu zmian w stronie usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne
Ten samouczek jest oparty na [samouczku: sfederować pojedyncze środowisko lasu usługi AD z chmurą](tutorial-federation.md) i jest to wymagane przed podjęciem próby wykonania tego samouczka.  Jeśli tamten samouczek nie został wykonany, zrób to przed podjęciem próby wykonania kroków zawartych w tym dokumencie.

>[!IMPORTANT]
>Przed przełączeniem do PHS należy utworzyć kopię zapasową środowiska AD FS.  Można to zrobić za pomocą [narzędzia AD FS szybkie przywracanie](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>Włączanie synchronizacji skrótów haseł w programie Azure AD Connect
Pierwszym krokiem w środowisku programu Azure AD Connect korzystającym z federacji jest włączenie synchronizacji skrótów haseł i umożliwienie programowi Azure AD Connect synchronizowania skrótów.

Wykonaj następujące czynności:

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij przycisk **Konfiguruj**.
3.  Na stronie Zadania dodatkowe wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie kliknij pozycję **Dalej**.
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na ekranie **Łączenie katalogów** kliknij pozycję **Dalej**.
6.  Na ekranie **Filtrowanie domen i jednostek organizacyjnych** kliknij przycisk **Dalej**.
7.  Na ekranie **Funkcje opcjonalne** zaznacz opcję **Synchronizacja skrótów haseł** i kliknij pozycję **Dalej**.
![Wybierz](media/tutorial-phs-backup/backup1.png)</br>
8.  Na ekranie **gotowy do skonfigurowania** kliknij pozycję **Konfiguruj**.
9.  Po zakończeniu konfigurowania kliknij pozycję **Zakończ**.
10. Gotowe.  Wszystko jest zrobione.  Synchronizacja skrótów haseł zostanie teraz przeprowadzona i będzie można jej używać jako zapasu, jeśli usługi AD FS staną się niedostępne.

## <a name="switch-to-password-hash-synchronization"></a>Przejście na synchronizację skrótów haseł
Teraz pokażemy, jak przejść na synchronizację skrótów haseł. Przed rozpoczęciem zastanów się nad warunkami wykonania przejścia. Nie rób tego z przyczyn tymczasowych, takich jak awaria sieci, pomniejszy problem z usługami AD FS lub problem wpływający na część użytkowników. Jeśli podejmiesz decyzję o wykonaniu przejścia, ponieważ rozwiązanie problemu trwałoby zbyt długo, wykonaj następujące kroki:

> [!IMPORTANT]
> Należy pamiętać, że potrwa to trochę czasu, aby skróty haseł były synchronizowane z usługą Azure AD.  Oznacza to, że ukończenie synchronizacji może potrwać 3 godziny, a zanim będzie możliwe rozpoczęcie uwierzytelniania przy użyciu skrótów haseł.

1. Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij przycisk **Konfiguruj**.
3.  Wybierz pozycję **Zmień dane logowania użytkownika** i kliknij pozycję **Dalej**.
![Zmień](media/tutorial-phs-backup/backup2.png)</br>
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To konto zostało utworzone [tutaj](tutorial-federation.md#create-a-global-administrator-in-azure-ad) w poprzednim samouczku.
5.  Na ekranie **Logowanie użytkownika** wybierz pozycję **Synchronizacja skrótów haseł** i zaznacz pole **Nie konwertuj kont użytkowników**.  
6.  Pozostaw domyślne zaznaczenie pola **Włącz logowanie jednokrotne**, a następnie kliknij pozycję **Dalej**.
7.  Na ekranie **Włącz logowanie jednokrotne** kliknij pozycję **Dalej**.
8.  Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
9.  Po zakończeniu konfigurowania kliknij pozycję **Zakończ**.
10. Użytkownicy mogą teraz używać swoich haseł do logowania się do platformy Azure i usług platformy Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1. Przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.</br>
   ![Zrzut ekranu, który wyświetla komunikat o pomyślnym przetestowaniu logowania. ](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Przełącz z powrotem do Federacji
Teraz pokażemy, jak przełączyć się z powrotem do Federacji.  W tym celu wykonaj następujące czynności:

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij przycisk **Konfiguruj**.
3.  Wybierz pozycję **Zmień dane logowania użytkownika** i kliknij pozycję **Dalej**.
4.  Podaj nazwę użytkownika i hasło administratora globalnego.  To jest konto, które [zostało utworzone w](tutorial-federation.md#create-a-global-administrator-in-azure-ad) poprzednim samouczku.
5.  Na ekranie **logowania użytkownika** wybierz opcję **Federacja z AD FS** i kliknij przycisk **dalej**.  
6. Na stronie Poświadczenia administratora domeny wprowadź nazwę użytkownika oraz hasło konta contoso\Administrator, a następnie kliknij przycisk **Dalej**.
7. Na ekranie farmy AD FS kliknij przycisk **dalej**.
8. Na ekranie **domena usługi Azure AD** wybierz z listy rozwijanej domenę, a następnie kliknij przycisk **dalej**.
9. Na ekranie **Wszystko gotowe do skonfigurowania** kliknij pozycję **Konfiguruj**.
10. Po zakończeniu konfiguracji kliknij przycisk **dalej**.
![Konfigurowanie](media/tutorial-phs-backup/backup4.png)</br>
11. Na stronie **Weryfikowanie łączności federacyjnej** kliknij przycisk **Weryfikuj**.  W celu pomyślnego wykonania tej czynności może być konieczne skonfigurowanie rekordów DNS (Dodaj rekordy a i AAAA).
![Zrzut ekranu przedstawiający ekran Weryfikowanie łączności federacyjnej i przycisk Weryfikuj.](media/tutorial-phs-backup/backup5.png)</br>
12. Kliknij przycisk **Zakończ**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Zresetuj AD FS i zaufanie platformy Azure
Teraz musimy zresetować zaufanie między AD FS i platformą Azure.

1.  Kliknij dwukrotnie ikonę programu Azure AD Connect utworzoną na pulpicie.
2.  Kliknij przycisk **Konfiguruj**.
3.  Wybierz pozycję **Zarządzaj Federacją** i kliknij przycisk **dalej**.
4.  Wybierz pozycję **Zresetuj relację zaufania usługi Azure AD** , a następnie kliknij przycisk **dalej**.
![Reset](media/tutorial-phs-backup/backup6.png)</br>
5.  Na ekranie **łączenie z usługą Azure AD** wprowadź nazwę użytkownika i hasło administratora globalnego.
6.  Na ekranie **Połącz z AD FS** wprowadź nazwę użytkownika i hasło contoso\administrator, a następnie kliknij przycisk **Dalej.**
7.  Na ekranie **Certyfikaty** kliknij przycisk **dalej**.

## <a name="test-signing-in-with-a-user"></a>Testowanie logowania za pomocą użytkownika

1.  Przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.
![Weryfikacja](media/tutorial-password-hash-sync/verify1.png)

W ten sposób pomyślnie skonfigurowano środowisko tożsamości hybrydowej, które można wykorzystać do testowania lub do bliższego zapoznania się z możliwościami platformy Azure.

## <a name="next-steps"></a>Następne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)