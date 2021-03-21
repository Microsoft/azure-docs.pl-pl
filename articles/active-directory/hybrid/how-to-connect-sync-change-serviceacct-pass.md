---
title: 'Azure AD Connect Sync: zmiana konta usługi ADSync | Microsoft Docs'
description: Ten dokument tematu opisuje klucz szyfrowania i sposób porzucenia po zmianie hasła.
services: active-directory
keywords: Konto usługi Azure AD Sync, hasło
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4dcc7ed6076c3bac723d709f50f1b3ab2ce8f58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996563"
---
# <a name="changing-the-adsync-service-account-password"></a>Zmienianie hasła konta usługi ADSync
Jeśli zmienisz hasło konta usługi ADSync, usługa synchronizacji nie będzie mogła zostać uruchomiona prawidłowo, dopóki nie powrócisz klucza szyfrowania i ponownie zainicjowano hasło konta usługi ADSync. 

Azure AD Connect w ramach usług synchronizacji program używa klucza szyfrowania do przechowywania haseł konta łącznika AD DS i konta usługi ADSync.  Te konta są szyfrowane, zanim zostaną zapisane w bazie danych programu. 

Używany klucz szyfrowania jest zabezpieczony przy użyciu funkcji [ochrony danych systemu Windows (DPAPI)](/previous-versions/ms995355(v=msdn.10)). DPAPI chroni klucz szyfrowania za pomocą **konta usługi ADSync**. 

Jeśli musisz zmienić hasło do konta usługi, możesz użyć procedur [porzucania klucza szyfrowania konta usługi ADSync](#abandoning-the-adsync-service-account-encryption-key) .  Te procedury należy również zastosować, jeśli trzeba porzucić klucz szyfrowania z dowolnego powodu.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemy związane z zmianą hasła
W przypadku zmiany hasła do konta usługi należy wykonać dwie czynności.

Najpierw należy zmienić hasło w Menedżerze kontroli usług systemu Windows.  Dopóki ten problem nie zostanie rozwiązany, zobaczysz następujące błędy:


- Jeśli spróbujesz uruchomić usługę synchronizacji w Menedżerze sterowania usługami systemu Windows, zostanie wyświetlony komunikat o błędzie "**system Windows nie może uruchomić usługi synchronizacji Microsoft Azure AD na komputerze lokalnym**". **Błąd 1069: usługa nie została uruchomiona z powodu niepowodzenia logowania.**"
- W obszarze Windows Podgląd zdarzeń dziennik zdarzeń systemu zawiera błąd o **identyfikatorze 7038** i komunikat "**Usługa ADSync nie mogła zalogować się jako z aktualnie skonfigurowanym hasłem z powodu następującego błędu: Nazwa użytkownika lub hasło jest niepoprawne".**

W przypadku gdy hasło zostanie zaktualizowane, usługa synchronizacji nie może pobrać klucza szyfrowania za pomocą funkcji DPAPI. Bez klucza szyfrowania usługa synchronizacji nie może odszyfrować haseł wymaganych do synchronizacji z lokalnymi usługami AD i Azure AD.
Zostaną wyświetlone następujące błędy:

- W obszarze Menedżer sterowania usługami systemu Windows, jeśli próbujesz uruchomić usługę synchronizacji i nie można pobrać klucza szyfrowania, wystąpi błąd "<strong>system Windows nie może uruchomić synchronizacji Microsoft Azure AD na komputerze lokalnym. Aby uzyskać więcej informacji, przejrzyj dziennik zdarzeń systemu. Jeśli jest to usługa firmy innej niż Microsoft, skontaktuj się z dostawcą usługi i zapoznaj się z kodem błędu specyficznym dla usługi — 21451857952</strong>. "
- W obszarze Windows Podgląd zdarzeń dziennik zdarzeń aplikacji zawiera błąd z **identyfikatorem zdarzenia 6028** i komunikatem o błędzie *"nie można uzyskać dostępu do klucza szyfrowania serwera".*

Aby upewnić się, że te błędy nie są wyświetlane, wykonaj procedury [porzucania klucza szyfrowania konta usługi ADSync](#abandoning-the-adsync-service-account-encryption-key) podczas zmiany hasła.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Porzucanie klucza szyfrowania konta usługi ADSync
>[!IMPORTANT]
>Poniższe procedury dotyczą tylko Azure AD Connect kompilacji wersji 1.1.443.0 lub starszej. Tego elementu nie można używać w nowszych wersjach Azure AD Connect, ponieważ porzucanie klucza szyfrowania jest obsługiwane przez program Azure AD Connect w przypadku zmiany hasła konta usługi synchronizacji AD, w związku z czym następujące kroki nie są wymagane w nowszych wersjach.   

Aby porzucić klucz szyfrowania, wykonaj czynności opisane w poniższych procedurach.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Co zrobić, jeśli trzeba porzucić klucz szyfrowania

Jeśli musisz porzucić klucz szyfrowania, Użyj poniższych procedur, aby to zrobić.

1. [Zatrzymaj usługę synchronizacji](#stop-the-synchronization-service)

1. [Porzuć istniejący klucz szyfrowania](#abandon-the-existing-encryption-key)

2. [Podaj hasło do konta łącznika AD DS](#provide-the-password-of-the-ad-ds-connector-account)

3. [Zainicjuj ponownie hasło konta usługi ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Uruchom usługę synchronizacji](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Zatrzymaj usługę synchronizacji
Najpierw można zatrzymać usługę w Menedżerze kontroli usług systemu Windows.  Upewnij się, że usługa nie jest uruchomiona przy próbie jej zatrzymania.  Jeśli tak, zaczekaj na jej zakończenie, a następnie zatrzymaj ją.


1. Przejdź do menedżera kontroli usług systemu Windows (Uruchom usługi → Services).
2. Wybierz pozycję **Microsoft Azure AD Synchronize** i kliknij przycisk Zatrzymaj.

#### <a name="abandon-the-existing-encryption-key"></a>Porzuć istniejący klucz szyfrowania
Porzuć istniejący klucz szyfrowania, aby można było utworzyć nowy klucz szyfrowania:

1. Zaloguj się do serwera Azure AD Connect jako administrator.

2. Rozpocznij nową sesję programu PowerShell.

3. Przejdź do folderu: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Uruchom polecenie: `./miiskmu.exe /a`

![Zrzut ekranu przedstawiający program PowerShell po uruchomieniu polecenia.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Podaj hasło do konta łącznika AD DS
Ponieważ istniejące hasła przechowywane wewnątrz bazy danych nie mogą być już odszyfrowywane, należy podać usługę synchronizacji przy użyciu hasła konta łącznika AD DS. Usługa synchronizacji szyfruje hasła przy użyciu nowego klucza szyfrowania:

1. Uruchom Synchronization Service Manager (Uruchom usługę synchronizacji →).
</br>![Service Manager synchronizacji](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Przejdź do karty **Łączniki** .
3. Wybierz **Łącznik usługi AD** , który odnosi się do lokalnej usługi AD. Jeśli masz więcej niż jeden łącznik usługi AD, powtórz następujące kroki dla każdego z nich.
4. W obszarze **Akcje** wybierz pozycję **Właściwości**.
5. W podręcznym oknie dialogowym wybierz pozycję **Połącz z lasem Active Directory**:
6. Wprowadź hasło konta AD DS w polu tekstowym **hasło** . Jeśli hasło nie jest znane, przed wykonaniem tego kroku należy ustawić jego nazwę na znaną wartość.
7. Kliknij przycisk **OK** , aby zapisać nowe hasło i zamknąć okno dialogowe.
![Zrzut ekranu przedstawiający stronę "łączenie się z lasem Active Directory" w oknie "właściwości".](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Zainicjuj ponownie hasło konta usługi ADSync
Nie można bezpośrednio podać hasła konta usługi Azure AD do usługi synchronizacji. Zamiast tego należy użyć polecenia cmdlet **Add-ADSyncAADServiceAccount** , aby ponownie zainicjować konto usługi Azure AD. Polecenie cmdlet resetuje hasło konta i udostępnia je usłudze synchronizacji:

1. Rozpocznij nową sesję programu PowerShell na serwerze Azure AD Connect.
2. Uruchom polecenie cmdlet `Add-ADSyncAADServiceAccount` .
3. W podręcznym oknie dialogowym podaj poświadczenia administratora globalnego usługi Azure AD dla dzierżawy usługi Azure AD.
![Narzędzie klucza szyfrowania Azure AD Connect synchronizacji](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Jeśli się powiedzie, zobaczysz wiersz polecenia programu PowerShell.

#### <a name="start-the-synchronization-service"></a>Uruchom usługę synchronizacji
Teraz, gdy usługa synchronizacji ma dostęp do klucza szyfrowania i wszystkich wymaganych haseł, można uruchomić ponownie usługę w Menedżerze sterowania usługami systemu Windows:


1. Przejdź do menedżera kontroli usług systemu Windows (Uruchom usługi → Services).
2. Wybierz pozycję **Microsoft Azure AD Synchronize** i kliknij przycisk Uruchom ponownie.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
