---
title: Rozwiązywanie problemów z logowaniem w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe problemy z logowaniem użytkownika i błędy w Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618573"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Rozwiązywanie problemów z logowaniem do konta przy użyciu domeny zarządzanej Azure Active Directory Domain Services

Najczęstsze przyczyny konta użytkownika, które nie mogą zalogować się do domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS), obejmują następujące scenariusze:

* [Konto nie jest jeszcze zsynchronizowane z platformą Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Usługa Azure AD DS nie ma skrótów haseł, aby umożliwić logowanie się do konta.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Konto jest zablokowane.](#the-account-is-locked-out)

> [!TIP]
> Usługa Azure AD DS nie może synchronizować poświadczeń dla kont spoza dzierżawy usługi Azure AD. Użytkownicy zewnętrzni nie mogą zalogować się do domeny zarządzanej AD DS platformy Azure.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Konto nie jest jeszcze zsynchronizowane z platformą Azure AD DS

W zależności od rozmiaru katalogu może upłynąć trochę czasu dla kont użytkowników i skrótów poświadczeń, które będą dostępne w domenie zarządzanej. W przypadku dużych katalogów ta początkowa Jednokierunkowa synchronizacja z usługi Azure AD może trwać kilka godzin, a nawet jeden dzień lub dwa. Przed ponowną próbą uwierzytelnienia upewnij się, że zaczekasz wystarczająco długo.

W środowiskach hybrydowych, które użytkownik Azure AD Connect synchronizować lokalne dane katalogu w usłudze Azure AD, upewnij się, że uruchamiasz najnowszą wersję Azure AD Connect i [skonfigurowano Azure AD Connect do przeprowadzenia pełnej synchronizacji po włączeniu usługi Azure AD DS][azure-ad-connect-phs]. Jeśli wyłączysz usługę Azure AD DS i włączysz ją ponownie, należy ponownie wykonać te kroki.

Jeśli nadal występują problemy z kontami, które nie synchronizują się za pomocą Azure AD Connect, należy ponownie uruchomić usługę Azure AD Sync. Na komputerze z zainstalowanym Azure AD Connect Otwórz okno wiersza polecenia, a następnie uruchom następujące polecenia:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>AD DS platformy Azure nie ma skrótów haseł

Usługa Azure AD nie generuje ani nie przechowuje skrótów haseł w formacie wymaganym do uwierzytelniania NTLM lub Kerberos, dopóki nie zostanie włączona usługa Azure AD DS dla dzierżawy. Ze względów bezpieczeństwa usługa Azure AD nie przechowuje również żadnych poświadczeń hasła w postaci zwykłego tekstu. W związku z tym usługa Azure AD nie może automatycznie generować tych skrótów uwierzytelniania NTLM lub Kerberos w oparciu o istniejące poświadczenia użytkownika.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Środowiska hybrydowe z synchronizacją lokalną

W przypadku środowisk hybrydowych, które używają Azure AD Connect do synchronizacji ze środowiskiem lokalnym AD DS, można lokalnie generować i synchronizować wymagane skróty haseł NTLM lub Kerberos w usłudze Azure AD. Po utworzeniu domeny zarządzanej [Włącz synchronizację skrótów haseł, aby Azure Active Directory Domain Services][azure-ad-connect-phs]. Bez wykonywania tego kroku synchronizacji skrótów haseł nie można zalogować się do konta przy użyciu domeny zarządzanej. Jeśli wyłączysz usługę Azure AD DS i włączysz ją ponownie, należy ponownie wykonać te kroki.

Aby uzyskać więcej informacji, zobacz [jak działa synchronizacja skrótów haseł dla AD DS platformy Azure][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Środowiska tylko w chmurze bez synchronizacji lokalnej

W przypadku domen zarządzanych bez synchronizacji lokalnej tylko konta w usłudze Azure AD muszą generować również wymagane skróty uwierzytelniania NTLM lub Kerberos. Jeśli konto tylko w chmurze nie może się zalogować, program pomyślnie zakończył proces zmiany hasła dla konta po włączeniu usługi Azure AD DS?

* **Nie, hasło nie zostało zmienione.**
    * [Zmień hasło dla konta][enable-user-accounts] , aby generować wymagane skróty haseł, a następnie poczekaj 15 minut przed podjęciem próby ponownego zalogowania.
    * Jeśli wyłączysz usługę Azure AD DS i włączysz ją ponownie, każde konto musi ponownie wykonać te czynności, aby zmienić hasło i wygenerować wymagane skróty haseł.
* **Tak, hasło zostało zmienione.**
    * Spróbuj zalogować się przy użyciu formatu *UPN* , takiego jak `driley@aaddscontoso.com` , zamiast formatu *sAMAccountName* , np `AADDSCONTOSO\deeriley` ..
    * *SAMAccountName* może być automatycznie generowany dla użytkowników, których prefiks nazwy UPN jest zbyt długi lub jest taki sam jak inny użytkownik w domenie zarządzanej. Format *nazwy UPN* ma być unikatowy w ramach dzierżawy usługi Azure AD.

## <a name="the-account-is-locked-out"></a>Konto jest zablokowane

Konto użytkownika w domenie zarządzanej jest blokowane, gdy osiągnięto określony próg nieudanych prób logowania. To zachowanie blokady konta zostało zaprojektowane z myślą o ochronie przed powtarzanymi próbami logowania z wykorzystaniem pełnego wymuszania, które mogą wskazywać na zautomatyzowany atak cyfrowy.

Domyślnie jeśli w ciągu 2 minut występuje 5 nieudanych prób wprowadzenia hasła, konto jest zablokowane przez 30 minut.

Aby uzyskać więcej informacji i jak rozwiązać problemy z blokadą konta, zobacz [Rozwiązywanie problemów z blokadą konta w usłudze Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy z przyłączaniem maszyny wirtualnej do domeny zarządzanej, [Znajdź pomoc i Otwórz bilet pomocy technicznej dla Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md