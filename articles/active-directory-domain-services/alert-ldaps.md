---
title: Rozwiązywanie alertów bezpiecznego protokołu LDAP w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy i rozwiązywać typowe alerty z bezpiecznym protokołem LDAP dla Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: d987bbbe2a35dd24341b75d5663bab33d8c3bdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220256"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: Secure LDAP alertów w Azure Active Directory Domain Services

Aplikacje i usługi korzystające z protokołu LDAP (Lightweight Directory Access Protocol) do komunikowania się z Azure Active Directory Domain Services (Azure AD DS) można [skonfigurować tak, aby używały bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md). Aby zabezpieczyć protokół LDAP działał poprawnie, musi być otwarty odpowiedni certyfikat i wymagane porty sieciowe.

Ten artykuł ułatwia zrozumienie i rozwiązywanie typowych alertów z bezpiecznym dostępem do protokołu LDAP na platformie Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Konfiguracja sieci Secure LDAP

### <a name="alert-message"></a>Komunikat alertu

*Secure LDAP przez Internet jest włączony dla domeny zarządzanej. Jednak dostęp do portu 636 nie jest zablokowany przy użyciu sieciowej grupy zabezpieczeń. Może to spowodować ujawnienie kont użytkowników w domenie zarządzanej w celu wymuszenia hasła.*

### <a name="resolution"></a>Rozwiązanie

Po włączeniu bezpiecznego protokołu LDAP zaleca się utworzenie dodatkowych reguł, które ograniczają dostęp do określonych adresów IP dla ruchu przychodzącego. Te reguły chronią domenę zarządzaną przed atakami polegającymi na wymuszeniu. Aby zaktualizować grupę zabezpieczeń sieci w celu ograniczenia dostępu do portu 636 protokołu TCP dla bezpiecznego protokołu LDAP, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **sieciowe grupy zabezpieczeń**.
1. Wybierz grupę zabezpieczeń sieci skojarzoną z domeną zarządzaną, taką jak *AADDS-contoso.com-sieciowej grupy zabezpieczeń*, a następnie wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**
1. Wybierz pozycję **+ Dodaj** , aby utworzyć regułę dla portu TCP 636. W razie potrzeby wybierz pozycję **Zaawansowane** w oknie, aby utworzyć regułę.
1. W polu **Źródło**wybierz opcję *adresy IP* z menu rozwijanego. Wprowadź źródłowe adresy IP, do których chcesz udzielić dostępu dla bezpiecznego ruchu LDAP.
1. Wybierz *dowolną* opcję jako **lokalizację docelową**, a następnie wprowadź *636* dla **docelowych zakresów portów**.
1. Ustaw **Protokół** jako *TCP* i **akcję** na *Zezwalaj*.
1. Określ priorytet reguły, a następnie wprowadź nazwę, taką jak *RestrictLDAPS*.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj** , aby utworzyć regułę.

Kondycja domeny zarządzanej automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

> [!TIP]
> Port TCP 636 nie jest jedyną regułą wymaganą do bezproblemowego działania usługi Azure AD DS. Aby dowiedzieć się więcej, zobacz [sieciowe grupy zabezpieczeń i wymagane porty platformy Azure AD DS](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: wygaśnie certyfikat Secure LDAP

### <a name="alert-message"></a>Komunikat alertu

*Certyfikat bezpiecznego protokołu LDAP dla domeny zarządzanej wygaśnie w dniu [Date]].*

### <a name="resolution"></a>Rozwiązanie

Utwórz zastępczy certyfikat bezpiecznego protokołu LDAP, wykonując kroki umożliwiające [utworzenie certyfikatu dla bezpiecznego protokołu LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Zastosuj certyfikat zastępczy do usługi Azure AD DS i Roześlij certyfikat do wszystkich klientów, którzy łączą się przy użyciu protokołu Secure LDAP.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
