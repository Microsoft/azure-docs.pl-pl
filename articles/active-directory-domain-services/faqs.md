---
title: Często zadawane pytania dotyczące Azure AD Domain Services | Microsoft Docs
description: Przeczytaj i poznaj niektóre często zadawane pytania dotyczące konfiguracji, administracji i dostępności dla Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749334"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Często zadawane pytania dotyczące usług domenowych Azure Active Directory (AD)

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfigurowanie

* [Czy mogę utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Czy mogę włączyć Azure AD Domain Services w klasycznej sieci wirtualnej?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Czy mogę włączyć Azure AD Domain Services w Azure Resource Manager wirtualnej?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Czy można migrować istniejącą domenę zarządzaną z klasycznej sieci wirtualnej do Resource Manager wirtualnej?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Czy mogę włączyć Azure AD Domain Services subskrypcji Azure CSP (Dostawca rozwiązań w chmurze)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Czy mogę włączyć Azure AD Domain Services w federacyjnie katalogu usługi Azure AD? Nie synchronizuj skrótów haseł z usługą Azure AD. Czy mogę włączyć Azure AD Domain Services dla tego katalogu?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Czy mogę udostępnić Azure AD Domain Services w wielu sieciach wirtualnych w ramach subskrypcji?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Czy mogę włączyć Azure AD Domain Services przy użyciu programu PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Czy mogę włączyć Azure AD Domain Services szablonu Resource Manager szablonu?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Czy można dodać kontrolery domeny do Azure AD Domain Services zarządzanej?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Czy użytkownicy-goście mogą być zapraszani do mojego katalogu przy użyciu Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Czy mogę przenieść istniejącą Azure AD Domain Services zarządzaną do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Czy mogę zmienić nazwę istniejącej Azure AD Domain Services domeny?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Czy Azure AD Domain Services opcje wysokiej dostępności?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Czy mogę utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?
Nie. Można utworzyć tylko jedną domenę zarządzaną, która jest Azure AD Domain Services dla jednego katalogu usługi Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w klasycznej sieci wirtualnej?
Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejące domeny zarządzane wdrożone w klasycznych sieciach wirtualnych będą nadal obsługiwane do 1 marca 2023 r. W przypadku istniejących wdrożeń można przeprowadzić [migrację Azure AD Domain Services klasycznego](migrate-from-classic-vnet.md)modelu sieci wirtualnej do Resource Manager .

Aby uzyskać więcej informacji, zobacz [oficjalne powiadomienie o cofaniu.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w Azure Resource Manager wirtualnej?
Tak. Azure AD Domain Services można włączyć w Azure Resource Manager wirtualnej. Klasyczne sieci wirtualne platformy Azure nie są już dostępne podczas tworzenia domeny zarządzanej.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Czy można migrować istniejącą domenę zarządzaną z klasycznej sieci wirtualnej do Resource Manager wirtualnej?
Tak. Aby uzyskać więcej informacji, zobacz [Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Czy mogę włączyć Azure AD Domain Services subskrypcji Azure CSP (Dostawca rozwiązań w chmurze)?
Tak. Aby uzyskać więcej informacji, [zobacz jak włączyć Azure AD Domain Services w Azure CSP subskrypcji.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Czy mogę włączyć Azure AD Domain Services w federacyjną usłudze Azure AD? Nie synchronizuj skrótów haseł z usługą Azure AD. Czy mogę włączyć Azure AD Domain Services dla tego katalogu?
Nie. Aby uwierzytelnić użytkowników za pośrednictwem protokołu NTLM lub Kerberos, Azure AD Domain Services musi mieć dostęp do skrótów haseł kont użytkowników. W katalogu federacyjną skróty haseł nie są przechowywane w katalogu usługi Azure AD. Dlatego Azure AD Domain Services nie działa z takimi katalogami usługi Azure AD.

Jeśli jednak używasz funkcji synchronizacji skrótów Azure AD Connect, możesz użyć Azure AD Domain Services ponieważ wartości skrótów haseł są przechowywane w usłudze Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy mogę udostępnić Azure AD Domain Services w wielu sieciach wirtualnych w ramach mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio tego scenariusza. Domena zarządzana jest dostępna tylko w jednej sieci wirtualnej jednocześnie. Można jednak skonfigurować łączność między wieloma sieciami wirtualnymi, aby udostępnić Azure AD Domain Services innym sieciom wirtualnym. Aby uzyskać więcej informacji, zobacz jak połączyć sieci wirtualne na [platformie Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) przy użyciu bram sieci VPN lub [komunikacji równorzędnej sieci wirtualnych.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Czy mogę włączyć Azure AD Domain Services przy użyciu programu PowerShell?
Tak. Aby uzyskać więcej informacji, zobacz [jak włączyć obsługę Azure AD Domain Services programu PowerShell.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Czy mogę włączyć Azure AD Domain Services szablonu Resource Manager szablonu?
Tak, możesz utworzyć domenę Azure AD Domain Services zarządzaną przy użyciu Resource Manager szablonu. Przed wdrożeniem szablonu należy utworzyć jednostkę usługi i grupę usługi Azure AD na Azure Portal lub Azure PowerShell usługi Azure AD. Aby uzyskać więcej informacji, zobacz Create an Azure AD DS managed domain using an Azure Resource Manager template (Tworzenie domeny [zarządzanej przy użyciu Azure Resource Manager szablonu).](template-create-instance.md) W przypadku tworzenia Azure AD Domain Services zarządzanej w Azure Portal istnieje również opcja eksportowania szablonu do użycia z dodatkowymi wdrożeniami.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Czy można dodać kontrolery domeny do Azure AD Domain Services zarządzanej?
Nie. Domena dostarczana przez Azure AD Domain Services jest domeną zarządzaną. Nie trzeba aprowizować, konfigurować ani w inny sposób zarządzać kontrolerami domeny dla tej domeny. Te działania związane z zarządzaniem są udostępniane jako usługa przez firmę Microsoft. W związku z tym nie można dodawać dodatkowych kontrolerów domeny (do odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Czy użytkownicy-goście mogą być zapraszani do mojego katalogu przy użyciu Azure AD Domain Services?
Nie. Użytkownicy-goście zaproszeni do katalogu usługi Azure AD przy użyciu procesu zapraszania [usługi Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) są synchronizowane z twoją Azure AD Domain Services zarządzaną. Jednak hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku Azure AD Domain Services nie ma możliwości synchronizowania skrótów NTLM i Kerberos dla tych użytkowników w domenie zarządzanej. Tacy użytkownicy nie mogą logować się ani dołączać komputerów do domeny zarządzanej.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Czy mogę przenieść istniejącą Azure AD Domain Services zarządzaną do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?
Nie. Po utworzeniu Azure AD Domain Services zarządzanej nie można przenieść domeny zarządzanej do innej grupy zasobów, sieci wirtualnej, subskrypcji itp. Podczas wdrażania domeny zarządzanej należy wybrać najbardziej odpowiednią subskrypcję, grupę zasobów, region i sieć wirtualną.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>Czy mogę zmienić nazwę istniejącej Azure AD Domain Services domeny?
Nie. Po utworzeniu Azure AD Domain Services zarządzanej nie można zmienić nazwy domeny DNS. Podczas tworzenia domeny zarządzanej dokładnie wybierz nazwę domeny DNS. Aby uzyskać informacje na temat wyboru nazwy domeny DNS, zobacz samouczek dotyczący tworzenia i konfigurowania Azure AD Domain Services [domeny zarządzanej.](tutorial-create-instance.md#create-a-managed-domain)

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Czy Azure AD Domain Services opcje wysokiej dostępności?

Tak. Każda Azure AD Domain Services zarządzana zawiera dwa kontrolery domeny. Nie zarządzasz tymi kontrolerami domeny ani nie łączysz się z nimi, ponieważ są one częścią usługi zarządzanej. W przypadku wdrożenia Azure AD Domain Services w regionie, który obsługuje Strefy dostępności, kontrolery domeny są rozproszone w różnych strefach. W regionach, które nie obsługują Strefy dostępności, kontrolery domeny są rozproszone w zestawach dostępności. Nie masz żadnych opcji konfiguracji ani kontroli zarządzania nad tą dystrybucją. Aby uzyskać więcej informacji, zobacz [Opcje dostępności dla maszyn wirtualnych na platformie Azure.](../virtual-machines/availability.md)

## <a name="administration-and-operations"></a>Administracja i operacje

* [Czy mogę nawiązać połączenie z kontrolerem domeny dla mojej domeny zarządzanej przy użyciu Pulpit zdalny?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Mam włączone Azure AD Domain Services. Jakiego konta użytkownika używam do przyłączania maszyn do domeny?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Czy mam uprawnienia administratora domeny dla domeny zarządzanej udostępniane przez Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy można modyfikować członkostwa w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Jak długo trwa, aby zmiany wprowadzone w katalogu usługi Azure AD były widoczne w domenie zarządzanej?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Czy mogę rozszerzyć schemat domeny zarządzanej dostarczony przez Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy mogę modyfikować lub dodawać rekordy DNS w domenie zarządzanej?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Jakie są zasady okresu istnienia hasła w domenie zarządzanej?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Czy Azure AD Domain Services ochrony konta usługi AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Czy można skonfigurować rozproszony system plików (DFS) i replikację w ramach Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Jak są stosowane aktualizacje systemu Windows w Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Czy mogę nawiązać połączenie z kontrolerem domeny dla mojej domeny zarządzanej przy użyciu Pulpit zdalny?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej przy użyciu Pulpit zdalny. Członkowie grupy Administratorzy kontrolera domeny usługi *AAD* mogą administrować domeną zarządzaną przy użyciu narzędzi administracyjnych usługi AD, takich jak Centrum administracyjne usługi Active Directory (ADAC) lub ad PowerShell. Te narzędzia są instalowane przy użyciu *Narzędzia administracji zdalnej serwera* na serwerze z systemem Windows przyłączony do domeny zarządzanej. Aby uzyskać więcej informacji, zobacz Create a management VM to configure and administer an Azure AD Domain Services managed domain (Tworzenie maszyny wirtualnej zarządzania w celu skonfigurowania domeny [zarządzanej Azure AD Domain Services administrowania nimi).](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Włączono Azure AD Domain Services. Jakiego konta użytkownika używam do przyłączania maszyn do domeny do tej domeny?
Każde konto użytkownika, które jest częścią domeny zarządzanej, może dołączyć do maszyny wirtualnej. Członkowie grupy Administratorzy kontrolera domeny usługi *AAD* mają dostęp pulpitu zdalnego do maszyn, które zostały przyłączone do domeny zarządzanej.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy mam uprawnienia administratora domeny dla domeny zarządzanej udostępniane przez Azure AD Domain Services?
Nie. W domenie zarządzanej nie są przyznawane uprawnienia administracyjne. *Uprawnienia administratora domeny* *i* administratora przedsiębiorstwa nie są dostępne do użycia w domenie. Członkowie grupy administratorów domeny lub administratorów przedsiębiorstwa w Twojej lokalna usługa Active Directory również nie mają przyznanych uprawnień administratora domeny/przedsiębiorstwa w domenie zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Czy można modyfikować członkostwa w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?
Użytkowników i grup synchronizowanych z Azure Active Directory do Azure AD Domain Services nie można modyfikować, ponieważ ich źródło pochodzenia jest Azure Active Directory. Obejmuje to przeniesienie użytkowników lub grup z jednostki organizacyjnej zarządzanej przez użytkowników usługi AADDC do niestandardowej jednostki organizacyjnej. Każdy użytkownik lub grupa pochodząca z domeny zarządzanej może zostać zmodyfikowany.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa, aby zmiany wprowadzone w katalogu usługi Azure AD były widoczne w mojej domenie zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD przy użyciu interfejsu użytkownika usługi Azure AD lub programu PowerShell są automatycznie synchronizowane z domeną zarządzaną. Ten proces synchronizacji działa w tle. Nie ma zdefiniowanego okresu dla tej synchronizacji, aby ukończyć wszystkie zmiany obiektu.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy mogę rozszerzyć schemat domeny zarządzanej dostarczany przez Azure AD Domain Services?
Nie. Schemat jest administrowany przez firmę Microsoft dla domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Czy mogę modyfikować lub dodawać rekordy DNS w domenie zarządzanej?
Tak. Członkowie grupy Administratorzy kontrolera domeny usługi *AAD* mają przyznane *uprawnienia administratora DNS* do modyfikowania rekordów DNS w domenie zarządzanej. Ci użytkownicy mogą używać konsoli Menedżera DNS na maszynie z systemem Windows Server przyłączony do domeny zarządzanej w celu zarządzania systemem DNS. Aby użyć konsoli Menedżera DNS, zainstaluj narzędzia serwera *DNS,* które są częścią *Narzędzia administracji zdalnej serwera* opcjonalną funkcją na serwerze. Aby uzyskać więcej informacji, zobacz [Administrowanie systemem DNS w Azure AD Domain Services domenie zarządzanej.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jakie są zasady okresu istnienia hasła w domenie zarządzanej?
Domyślny okres istnienia hasła w domenie zarządzanej Azure AD Domain Services to 90 dni. Ten okres istnienia hasła nie jest synchronizowany z okresem istnienia hasła skonfigurowanym w usłudze Azure AD. W związku z tym może wystąpić sytuacja, w której hasła użytkowników wygasają w domenie zarządzanej, ale są nadal ważne w usłudze Azure AD. W takich scenariuszach użytkownicy muszą zmienić swoje hasło w usłudze Azure AD, a nowe hasło zostanie zsynchronizowane z twoją domeną zarządzaną. Jeśli chcesz zmienić domyślny okres istnienia hasła w domenie zarządzanej, możesz utworzyć i [skonfigurować niestandardowe zasady haseł.](password-policy.md).

Ponadto zasady haseł usługi Azure AD dla *funkcji DisablePasswordExpiration* są synchronizowane z domeną zarządzaną. Po *zastosowaniu wartości DisablePasswordExpiration* do użytkownika w usłudze Azure AD wartość *UserAccountControl* zsynchronizowanego użytkownika w domenie zarządzanej *DONT_EXPIRE_PASSWORD* zastosowana.

Gdy użytkownicy resetują swoje hasło w usłudze Azure AD, stosowany jest atrybut *forceChangePasswordNextSignIn=True.* Domena zarządzana synchronizuje ten atrybut z usługi Azure AD. Gdy domena zarządzana wykryje, że wartość *forceChangePasswordNextSignIn* jest ustawiona dla zsynchronizowanego użytkownika z usługi Azure AD, atrybut *pwdLastSet* w domenie zarządzanej jest ustawiony na *wartość 0*, co unieważnia aktualnie ustawione hasło.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Czy Azure AD Domain Services ochrony konta usługi AD?
Tak. Pięć prób nieprawidłowego hasła w ciągu 2 minut w domenie zarządzanej powoduje zablokowanie konta użytkownika na 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane. Próby nieprawidłowego hasła w domenie zarządzanej nie blokają konta użytkownika w usłudze Azure AD. Konto użytkownika jest zablokowane tylko w twojej Azure AD Domain Services zarządzanej. Aby uzyskać więcej informacji, zobacz Password and account lockout policies on managed domains (Zasady [blokowania haseł i kont w domenach zarządzanych).](password-policy.md)

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Czy można skonfigurować rozproszony system plików replikacji w ramach Azure AD Domain Services?
Nie. rozproszony system plików (DFS) i replikacja nie są dostępne podczas korzystania z Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Jak są stosowane aktualizacje systemu Windows w Azure AD Domain Services?
Kontrolery domeny w domenie zarządzanej automatycznie stosują wymagane aktualizacje systemu Windows. Nie musisz tutaj nic konfigurować ani administrować. Upewnij się, że nie tworzysz reguł sieciowej grupy zabezpieczeń, które blokują ruch wychodzący do usługi Windows Updates. W przypadku własnych maszyn wirtualnych przyłączonych do domeny zarządzanej użytkownik jest odpowiedzialny za konfigurowanie i stosowanie wszelkich wymaganych aktualizacji systemu operacyjnego i aplikacji.

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

* [Czy Azure AD Domain Services usługa płatna?](#is-azure-ad-domain-services-a-paid-service)
* [Czy istnieje bezpłatna wersja próbna usługi?](#is-there-a-free-trial-for-the-service)
* [Czy mogę wstrzymać domenę Azure AD Domain Services zarządzaną?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy mogę przesieć w Azure AD Domain Services do innego regionu na wypadek zdarzenia awarii?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy mogę uzyskać Azure AD Domain Services w ramach pakietu Enterprise Mobility Suite (EMS)? Czy muszę Azure AD — wersja Premium używać Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [W jakich regionach platformy Azure jest dostępna usługa?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Czy Azure AD Domain Services usługa płatna?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna usługi?
Azure AD Domain Services jest uwzględniony w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się w celu bezpłatnej miesięcznej wersji [próbnej platformy Azure.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy mogę wstrzymać domenę Azure AD Domain Services zarządzaną?
Nie. Po włączeniu zarządzanej domeny Azure AD Domain Services usługa jest dostępna w wybranej sieci wirtualnej do momentu usunięcia domeny zarządzanej. Nie ma możliwości wstrzymania usługi. Rozliczanie jest kontynuowane co godzinę do momentu usunięcia domeny zarządzanej.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Czy mogę przesieć w Azure AD Domain Services do innego regionu na wypadek zdarzenia awarii?
Tak, aby zapewnić odporność geograficzną domeny zarządzanej, można utworzyć dodatkową replikę ustawioną na równorzędną sieć wirtualną w dowolnym regionie świadczenia usługi Azure, który obsługuje Azure AD DS. [](tutorial-create-replica-set.md) Zestawy replik współdzielą tę samą przestrzeń nazw i konfigurację z domeną zarządzaną.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Czy mogę uzyskać Azure AD Domain Services w ramach pakietu Enterprise Mobility Suite (EMS)? Czy muszę Azure AD — wersja Premium używać Azure AD Domain Services?
Nie. Azure AD Domain Services jest usługą platformy Azure z płatnością zgodnie z ty i nie jest częścią usługi EMS. Azure AD Domain Services można używać ze wszystkimi wersjami usługi Azure AD (bezpłatna i Premium). Opłaty są naliczane godzinowo, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>W jakich regionach platformy Azure jest dostępna usługa?
Zapoznaj się ze stroną [Usługi platformy Azure](https://azure.microsoft.com/regions/#services/) według regionów, aby wyświetlić listę regionów świadczenia usługi Azure, w Azure AD Domain Services są dostępne.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zapoznaj się z [przewodnikiem rozwiązywania problemów,](troubleshoot.md) aby uzyskać rozwiązania typowych problemów z konfigurowaniem lub administrowaniem Azure AD Domain Services.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Azure AD Domain Services, zobacz [Co to jest Azure Active Directory Domain Services?](overview.md).

Aby rozpocząć pracę, zobacz Create and configure an Azure Active Directory Domain Services managed domain (Tworzenie [i konfigurowanie Azure Active Directory Domain Services zarządzanej).](tutorial-create-instance.md)
