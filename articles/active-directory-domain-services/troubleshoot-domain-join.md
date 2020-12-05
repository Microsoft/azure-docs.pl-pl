---
title: Rozwiązywanie problemów z przyłączaniem do domeny za pomocą Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać typowe problemy podczas próby dołączenia do domeny lub łączenia aplikacji z usługą Azure Active Directory Domain Services i nie można nawiązać połączenia z domeną zarządzaną ani uwierzytelniać się w niej.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618607"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Rozwiązywanie problemów z przyłączaniem do domeny za pomocą Azure Active Directory Domain Services domeny zarządzanej

Podczas próby dołączenia maszyny wirtualnej lub połączenia aplikacji z domeną zarządzaną Azure Active Directory Domain Services (Azure AD DS) może zostać wyświetlony błąd, którego nie można zrobić. Aby rozwiązać problemy z przyłączaniem do domeny, należy zapoznać się z następującymi kwestiami:

* Jeśli nie otrzymasz monitu o uwierzytelnienie, maszyna wirtualna lub aplikacja nie może nawiązać połączenia z domeną zarządzaną platformy Azure AD DS.
    * Rozpocznij Rozwiązywanie [problemów z łącznością w celu przyłączenia do domeny](#connectivity-issues-for-domain-join).
* Jeśli podczas uwierzytelniania wystąpi błąd, połączenie z domeną zarządzaną powiedzie się.
    * Rozpocznij Rozwiązywanie [problemów związanych z poświadczeniami podczas przyłączania do domeny](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemy z łącznością w przypadku przyłączania do domeny

Jeśli maszyna wirtualna nie może znaleźć domeny zarządzanej, zazwyczaj występuje problem z połączeniem sieciowym lub konfiguracją. Zapoznaj się z poniższymi krokami rozwiązywania problemów, aby zlokalizować i rozwiązać problem:

1. Upewnij się, że maszyna wirtualna jest połączona z tą samą lub równorzędną siecią wirtualną, co domena zarządzana. Jeśli nie, maszyna wirtualna nie może znaleźć domeny i połączyć się z nią w celu dołączenia.
    * Jeśli maszyna wirtualna nie jest połączona z tą samą siecią wirtualną, upewnij się, że wirtualne sieci równorzędne lub połączenie sieci VPN jest *aktywne* lub *połączone* , aby umożliwić poprawne przepływ ruchu.
1. Spróbuj wysłać polecenie ping do domeny przy użyciu nazwy domeny zarządzanej, na przykład `ping aaddscontoso.com` .
    * Jeśli odpowiedź ping nie powiedzie się, spróbuj wysłać polecenie ping do adresów IP dla domeny wyświetlanej na stronie Przegląd w portalu dla domeny zarządzanej, na przykład `ping 10.0.0.4` .
    * Jeśli można pomyślnie wysłać polecenie ping do adresu IP, ale nie do domeny, może to spowodować niepoprawne skonfigurowanie usługi DNS. Upewnij się, że [skonfigurowano serwery DNS domeny zarządzanej dla sieci wirtualnej][configure-dns].
1. Spróbuj użyć opróżniania pamięci podręcznej programu rozpoznawania nazw DNS na maszynie wirtualnej, na przykład `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Konfiguracja sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)

Podczas tworzenia domeny zarządzanej jest również tworzona Grupa zabezpieczeń sieci z odpowiednimi regułami dla pomyślnego działania domeny. Jeśli edytujesz lub utworzysz dodatkowe reguły sieciowej grupy zabezpieczeń, możesz przypadkowo zablokować porty wymagane przez usługę Azure AD DS, aby zapewnić połączenia i usługi uwierzytelniania. Te reguły sieciowej grupy zabezpieczeń mogą powodować problemy, takie jak synchronizacja haseł nie została ukończona, użytkownicy nie mogą się zalogować lub problemy z przyłączaniem do domeny.

Jeśli nadal występują problemy z połączeniem, zapoznaj się z poniższymi krokami rozwiązywania problemów:

1. Sprawdź stan kondycji domeny zarządzanej w Azure Portal. Jeśli masz alert dla usługi *AADDS001*, reguła sieciowej grupy zabezpieczeń blokuje dostęp.
1. Przejrzyj [wymagane porty i reguły sieciowej grupy zabezpieczeń][network-ports]. Upewnij się, że żadne reguły sieciowej grupy zabezpieczeń nie są stosowane do maszyny wirtualnej lub sieci wirtualnej, z której nawiązujesz połączenie, blokując te porty sieciowe.
1. Po rozwiązaniu problemów z konfiguracją grupy zabezpieczeń sieci alert *AADDS001* znika ze strony kondycji w ciągu około 2 godzin. Mając teraz dostęp do połączenia sieciowego, spróbuj ponownie dołączyć do domeny.

## <a name="credentials-related-issues-during-domain-join"></a>Problemy związane z poświadczeniami podczas przyłączania do domeny

Jeśli zostanie wyświetlone okno dialogowe z prośbą o podanie poświadczeń w celu przyłączenia do domeny zarządzanej, maszyna wirtualna może połączyć się z domeną przy użyciu sieci wirtualnej platformy Azure. Proces przyłączania do domeny kończy się niepowodzeniem podczas uwierzytelniania w domenie lub autoryzacji do ukończenia procesu przyłączania do domeny przy użyciu poświadczeń.

Aby rozwiązać problemy związane z poświadczeniami, zapoznaj się z następującymi krokami rozwiązywania problemów:

1. Spróbuj użyć formatu UPN w celu określenia poświadczeń, takich jak `dee@contoso.onmicrosoft.com` . Upewnij się, że ta nazwa UPN jest prawidłowo skonfigurowana w usłudze Azure AD.
    * Nazwa *sAMAccountName* dla Twojego konta może zostać wygenerowana automatycznie, jeśli istnieje wielu użytkowników z tym samym PREFIKSEM nazwy UPN w dzierżawie lub jeśli prefiks nazwy UPN jest zbyt długi. W związku z tym format *sAMAccountName* dla konta może być inny niż oczekiwany lub używany w domenie lokalnej.
1. Spróbuj użyć poświadczeń dla konta użytkownika, które jest częścią domeny zarządzanej, aby dołączyć maszyny wirtualne do domeny zarządzanej.
1. Upewnij się, że [włączono synchronizację haseł][enable-password-sync] i upłynął wystarczająco długo, aby można było ukończyć synchronizację wstępnego hasła.

## <a name="next-steps"></a>Następne kroki

Aby lepiej zrozumieć Active Directory procesy w ramach operacji przyłączania do domeny, zobacz [problemy z przyłączaniem i uwierzytelnianiem][join-authentication-issues].

Jeśli nadal masz problemy z przyłączaniem maszyny wirtualnej do domeny zarządzanej, [Znajdź pomoc i Otwórz bilet pomocy technicznej dla Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
