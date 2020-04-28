---
title: Rozwiązywanie problemów z zabezpieczaniem protokołu LDAP w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z zabezpieczaniem protokołu LDAP (LDAPs) dla domeny zarządzanej Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77132163"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Rozwiązywanie problemów z łącznością za pośrednictwem protokołu LDAP w Azure Active Directory Domain Services domeny zarządzanej

Aplikacje i usługi korzystające z protokołu LDAP (Lightweight Directory Access Protocol) do komunikowania się z Azure Active Directory Domain Services (Azure AD DS) można [skonfigurować tak, aby używały bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md). Aby zabezpieczyć protokół LDAP działał poprawnie, musi być otwarty odpowiedni certyfikat i wymagane porty sieciowe.

Ten artykuł pomaga w rozwiązywaniu problemów z bezpiecznym dostępem do protokołu LDAP na platformie Azure AD DS.

## <a name="common-connection-issues"></a>Typowe problemy z połączeniem

Jeśli masz problemy z nawiązywaniem połączenia z domeną zarządzaną platformy Azure AD DS przy użyciu protokołu Secure LDAP, zapoznaj się z następującymi krokami rozwiązywania problemów. Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z domeną zarządzaną AD DS platformy Azure:

* Łańcuch wystawcy certyfikatu bezpiecznego protokołu LDAP musi być zaufany na kliencie. Główny urząd certyfikacji (CA) można dodać do magazynu zaufanych certyfikatów głównych na kliencie, aby ustanowić relację zaufania.
    * Należy się upewnić, że [Certyfikaty są eksportowane i stosowane na komputerach klienckich][client-cert].
* Sprawdź, czy certyfikat bezpiecznego protokołu LDAP dla domeny zarządzanej ma nazwę DNS w *temacie* lub *Alternatywna nazwa podmiotu* .
    * Przejrzyj [wymagania dotyczące bezpiecznego certyfikatu LDAP][certs-prereqs] i w razie potrzeby Utwórz certyfikat zastępczy.
* Sprawdź, czy klient LDAP, taki jak *Ldp. exe* , łączy się z bezpiecznym punktem końcowym protokołu LDAP przy użyciu nazwy DNS, a nie adresu IP.
    * Certyfikat zastosowany do domeny zarządzanej usługi Azure AD DS nie obejmuje adresów IP usługi, tylko nazw DNS.
* Sprawdź nazwę DNS, z którą łączy się klient LDAP. Musi on zostać rozpoznany jako publiczny adres IP dla bezpiecznego protokołu LDAP w domenie zarządzanej AD DS platformy Azure.
    * Jeśli nazwa DNS jest rozpoznawana jako wewnętrzny adres IP, Zaktualizuj rekord DNS, aby mógł zostać rozpoznany jako zewnętrzny adres IP.
* W przypadku łączności zewnętrznej Grupa zabezpieczeń sieci musi zawierać regułę umożliwiającą ruch do portu TCP 636 z Internetu.
    * Jeśli można nawiązać połączenie z domeną zarządzaną platformy Azure AD DS przy użyciu bezpiecznego protokołu LDAP z zasobów podłączonych bezpośrednio do sieci wirtualnej, ale nie połączeń zewnętrznych, należy się upewnić, że [utworzono regułę sieciowej grupy zabezpieczeń, która zezwala na bezpieczny ruch protokołu LDAP][ldaps-nsg].

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
