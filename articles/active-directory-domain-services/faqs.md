---
title: Często zadawane pytania dotyczące Azure AD Domain Services | Microsoft Docs
description: Przeczytaj i zapoznaj się z często zadawanymi pytaniami dotyczącymi konfiguracji, administracji i dostępności Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: iainfou
ms.openlocfilehash: 6e2daa60e99eb7aab34b11f240a2e2fb03c98582
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612408"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Często zadawane pytania dotyczące usług domenowych Azure Active Directory (AD)

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfiguracja

* [Czy mogę utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Czy mogę włączyć Azure AD Domain Services w klasycznej sieci wirtualnej?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Czy mogę włączyć Azure AD Domain Services w sieci wirtualnej Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Czy mogę migrować istniejącą domenę zarządzaną z klasycznej sieci wirtualnej do sieci wirtualnej Menedżer zasobów?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Czy mogę włączyć Azure AD Domain Services w ramach subskrypcji dostawcy rozwiązań w chmurze (CSP) platformy Azure?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Czy mogę włączyć Azure AD Domain Services w federacyjnym katalogu usługi Azure AD? Nie synchronizuję skrótów haseł z usługą Azure AD. Czy mogę włączyć Azure AD Domain Services dla tego katalogu?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Czy mogę udostępniać Azure AD Domain Services w wielu sieciach wirtualnych w ramach mojej subskrypcji?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Czy mogę włączyć Azure AD Domain Services przy użyciu programu PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Czy mogę włączyć Azure AD Domain Services przy użyciu szablonu Menedżer zasobów?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Czy można dodać kontrolery domeny do Azure AD Domain Services domeny zarządzanej?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Czy użytkownicy-Goście mogą zaproszeni do mojego katalogu używać Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Czy można przenieść istniejącą Azure AD Domain Servicesą domenę zarządzaną do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Czy Azure AD Domain Services zawierają opcje wysokiej dostępności?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Czy mogę utworzyć wiele domen zarządzanych dla jednego katalogu usługi Azure AD?
Nie. Można utworzyć tylko jedną domenę zarządzaną, Azure AD Domain Services dla jednego katalogu usługi Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w klasycznej sieci wirtualnej?
Klasyczne sieci wirtualne nie są obsługiwane w przypadku nowych wdrożeń. Istniejące domeny zarządzane wdrożone w klasycznych sieciach wirtualnych są nadal obsługiwane, dopóki nie zostaną wycofane z 1 marca 2023. W przypadku istniejących wdrożeń można [migrować Azure AD Domain Services z modelu klasycznej sieci wirtualnej do Menedżer zasobów](migrate-from-classic-vnet.md).

Aby uzyskać więcej informacji, zobacz [oficjalne powiadomienie o zaniechaniu](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Czy mogę włączyć Azure AD Domain Services w sieci wirtualnej Azure Resource Manager?
Tak. Azure AD Domain Services można włączyć w Azure Resource Manager sieci wirtualnej. Klasyczne sieci wirtualne platformy Azure nie są już dostępne podczas tworzenia domeny zarządzanej.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Czy mogę migrować istniejącą domenę zarządzaną z klasycznej sieci wirtualnej do sieci wirtualnej Menedżer zasobów?
Tak. Aby uzyskać więcej informacji, zobacz [migrowanie Azure AD Domain Services z klasycznego modelu sieci wirtualnej do Menedżer zasobów](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Czy mogę włączyć Azure AD Domain Services w ramach subskrypcji dostawcy rozwiązań w chmurze (CSP) platformy Azure?
Tak. Aby uzyskać więcej informacji, zobacz [How to enable Azure AD Domain Services in Azure CSP subscriptions](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Czy mogę włączyć Azure AD Domain Services w federacyjnym katalogu usługi Azure AD? Nie synchronizuję skrótów haseł z usługą Azure AD. Czy mogę włączyć Azure AD Domain Services dla tego katalogu?
Nie. Aby uwierzytelniać użytkowników za pośrednictwem protokołu NTLM lub Kerberos, Azure AD Domain Services musi mieć dostęp do skrótów haseł kont użytkowników. W katalogu federacyjnym skróty haseł nie są przechowywane w katalogu usługi Azure AD. W związku z tym Azure AD Domain Services nie działa z takimi katalogami usługi Azure AD.

Jeśli jednak używasz Azure AD Connect do synchronizacji skrótów haseł, możesz użyć Azure AD Domain Services, ponieważ wartości skrótu hasła są przechowywane w usłudze Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Czy mogę udostępniać Azure AD Domain Services w wielu sieciach wirtualnych w ramach mojej subskrypcji?
Sama usługa nie obsługuje bezpośrednio tego scenariusza. Twoja domena zarządzana jest dostępna tylko w jednej sieci wirtualnej naraz. Można jednak skonfigurować łączność między wieloma sieciami wirtualnymi, aby uwidocznić Azure AD Domain Services do innych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [jak połączyć sieci wirtualne na platformie Azure przy użyciu bram sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) lub [komunikacji równorzędnej sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Czy mogę włączyć Azure AD Domain Services przy użyciu programu PowerShell?
Tak. Aby uzyskać więcej informacji, zobacz [jak włączyć Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Czy mogę włączyć Azure AD Domain Services przy użyciu szablonu Menedżer zasobów?
Tak, możesz utworzyć Azure AD Domain Services domenę zarządzaną przy użyciu szablonu Menedżer zasobów. Przed wdrożeniem szablonu należy utworzyć nazwę główną usługi i grupę usługi Azure AD dla administracji przy użyciu Azure Portal lub Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Tworzenie domeny zarządzanej AD DS platformy Azure przy użyciu szablonu Azure Resource Manager](template-create-instance.md). W przypadku tworzenia Azure AD Domain Services domeny zarządzanej w Azure Portal istnieje również opcja eksportowania szablonu do użycia z dodatkowymi wdrożeniami.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Czy można dodać kontrolery domeny do Azure AD Domain Services domeny zarządzanej?
Nie. Domena udostępniona przez Azure AD Domain Services jest domeną zarządzaną. Nie ma potrzeby udostępniania, konfigurowania ani zarządzania kontrolerami domeny dla tej domeny. Te działania związane z zarządzaniem są udostępniane jako usługa przez firmę Microsoft. W związku z tym nie można dodać dodatkowych kontrolerów domeny (do odczytu i zapisu lub tylko do odczytu) dla domeny zarządzanej.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Czy użytkownicy-Goście mogą zaproszeni do mojego katalogu używać Azure AD Domain Services?
Nie. Użytkownicy-Goście zaproszeni do katalogu usługi Azure AD przy użyciu procesu zapraszania [B2B usługi Azure AD](../active-directory/external-identities/what-is-b2b.md) są synchronizowane w Azure AD Domain Services domenie zarządzanej. Jednak hasła dla tych użytkowników nie są przechowywane w katalogu usługi Azure AD. W związku z tym Azure AD Domain Services nie ma możliwości synchronizacji skrótów NTLM i Kerberos dla tych użytkowników w domenie zarządzanej. Tacy użytkownicy nie mogą zalogować się ani przyłączyć komputerów do domeny zarządzanej.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Czy można przenieść istniejącą Azure AD Domain Servicesą domenę zarządzaną do innej subskrypcji, grupy zasobów, regionu lub sieci wirtualnej?
Nie. Po utworzeniu domeny zarządzanej Azure AD Domain Services nie można przenieść domeny zarządzanej do innej grupy zasobów, sieci wirtualnej, subskrypcji itd. Zadbaj o wybranie najbardziej odpowiedniej subskrypcji, grupy zasobów, regionu i sieci wirtualnej podczas wdrażania domeny zarządzanej.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Czy Azure AD Domain Services zawierają opcje wysokiej dostępności?

Tak. Każda Azure AD Domain Services domeny zarządzanej zawiera dwa kontrolery domeny. Nie są zarządzane ani nie można łączyć się z tymi kontrolerami domeny, są one częścią usługi zarządzanej. W przypadku wdrażania Azure AD Domain Services w regionie, który obsługuje Strefy dostępności, kontrolery domeny są dystrybuowane między strefami. W regionach, które nie obsługują Strefy dostępności, kontrolery domeny są dystrybuowane w zestawach dostępności. Ta dystrybucja nie zawiera żadnych opcji konfiguracji ani kontroli zarządzania. Aby uzyskać więcej informacji, zobacz [Opcje dostępności dla maszyn wirtualnych na platformie Azure](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Administracja i operacje

* [Czy można połączyć się z kontrolerem domeny dla mojej domeny zarządzanej przy użyciu Pulpit zdalny?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Włączono Azure AD Domain Services. Jakie konto użytkownika jest używane do przyłączania do domeny maszyn do tej domeny?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Czy masz uprawnienia administratora domeny dla domeny zarządzanej dostarczonej przez Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy mogę modyfikować członkostwa w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Jak długo trwa wprowadzanie zmian wprowadzonych w katalogu usługi Azure AD, aby były widoczne w mojej domenie zarządzanej?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Czy można rozciągnąć schemat domeny zarządzanej udostępnionej przez Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Czy mogę modyfikować lub dodawać rekordy DNS w mojej domenie zarządzanej?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Jakie są zasady okresu istnienia hasła w domenie zarządzanej?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Czy Azure AD Domain Services zapewnić ochronę blokady konta usługi AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Czy można skonfigurować rozproszony system plików (DFS) i replikację w ramach Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Jak są stosowane aktualizacje systemu Windows w Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Czy można połączyć się z kontrolerem domeny dla mojej domeny zarządzanej przy użyciu Pulpit zdalny?
Nie. Nie masz uprawnień do łączenia się z kontrolerami domeny dla domeny zarządzanej przy użyciu Pulpit zdalny. Członkowie grupy *Administratorzy domeny usługi AAD* mogą administrować zarządzaną domeną przy użyciu narzędzi administracyjnych usług AD, takich jak Active Directory Administration Center (usługach ADAC) lub AD PowerShell. Te narzędzia są instalowane przy użyciu funkcji *Narzędzia administracji zdalnej serwera* na serwerze z systemem Windows przyłączonym do domeny zarządzanej. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej zarządzania w celu skonfigurowania i administrowania Azure AD Domain Services domeną zarządzaną](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Włączono Azure AD Domain Services. Jakie konto użytkownika jest używane do przyłączania do domeny maszyn do tej domeny?
Każde konto użytkownika, które jest częścią domeny zarządzanej, może dołączyć do maszyny wirtualnej. Członkowie grupy *Administratorzy domeny usługi AAD* otrzymują dostęp do pulpitu zdalnego dla komputerów, które zostały przyłączone do domeny zarządzanej.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy masz uprawnienia administratora domeny dla domeny zarządzanej dostarczonej przez Azure AD Domain Services?
Nie. Nie przyznano uprawnień administracyjnych w domenie zarządzanej. Uprawnienia *administratora domeny* i *administratora przedsiębiorstwa* nie są dostępne do użycia w ramach domeny. Członkowie grupy Administratorzy domeny lub Administratorzy przedsiębiorstwa w Active Directory lokalnym nie mają również uprawnień administratora domeny/przedsiębiorstwa w domenie zarządzanej.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Czy mogę modyfikować członkostwa w grupach przy użyciu protokołu LDAP lub innych narzędzi administracyjnych usługi AD w domenach zarządzanych?
Nie można zmodyfikować użytkowników i grup, które są synchronizowane z Azure Active Directory do Azure AD Domain Services, ponieważ ich źródło pochodzenia jest Azure Active Directory. Dowolny użytkownik lub Grupa, która pochodzi z domeny zarządzanej, może zostać zmodyfikowana.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak długo trwa wprowadzanie zmian wprowadzonych w katalogu usługi Azure AD, aby były widoczne w mojej domenie zarządzanej?
Zmiany wprowadzone w katalogu usługi Azure AD za pomocą interfejsu użytkownika usługi Azure AD lub programu PowerShell są automatycznie synchronizowane z domeną zarządzaną. Ten proces synchronizacji jest uruchamiany w tle. Nie zdefiniowano okresu dla tej synchronizacji, aby zakończyć wszystkie zmiany obiektu.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Czy można rozciągnąć schemat domeny zarządzanej udostępnionej przez Azure AD Domain Services?
Nie. Schemat jest zarządzany przez firmę Microsoft dla domeny zarządzanej. Rozszerzenia schematu nie są obsługiwane przez Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Czy mogę modyfikować lub dodawać rekordy DNS w mojej domenie zarządzanej?
Tak. Członkowie grupy *Administratorzy domeny usługi AAD* mają uprawnienia *administratora DNS* , aby modyfikować rekordy DNS w domenie zarządzanej. Ci użytkownicy mogą korzystać z konsoli Menedżera DNS na komputerze z systemem Windows Server przyłączonym do domeny zarządzanej w celu zarządzania systemem DNS. Aby skorzystać z konsoli Menedżera DNS, zainstaluj *Narzędzia serwera DNS*, które są częścią *Narzędzia administracji zdalnej serwera* funkcji opcjonalnej na serwerze. Aby uzyskać więcej informacji, zobacz [administrowanie systemem DNS w domenie zarządzanej Azure AD Domain Services](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jakie są zasady okresu istnienia hasła w domenie zarządzanej?
Domyślny okres istnienia hasła w domenie zarządzanej Azure AD Domain Services to 90 dni. Okres istnienia hasła nie jest zsynchronizowany z okresem istnienia hasła skonfigurowanym w usłudze Azure AD. W związku z tym może wystąpić sytuacja, w której hasła użytkowników wygasną w domenie zarządzanej, ale są nadal ważne w usłudze Azure AD. W takich scenariuszach użytkownicy muszą zmienić swoje hasło w usłudze Azure AD, a nowe hasło zostanie zsynchronizowane z domeną zarządzaną. Jeśli chcesz zmienić domyślny okres istnienia hasła w domenie zarządzanej, możesz [utworzyć i skonfigurować niestandardowe zasady haseł.](password-policy.md)

Ponadto zasady haseł usługi Azure AD dla *DisablePasswordExpiration* są synchronizowane z domeną zarządzaną. Gdy *DisablePasswordExpiration* jest stosowana do użytkownika w usłudze Azure AD *, zostanie zastosowana wartość konta* usług w *DONT_EXPIRE_PASSWORD* domenie zarządzanej dla synchronizowanego użytkownika.

Po zresetowaniu hasła przez użytkownika w usłudze Azure AD jest stosowany atrybut *forceChangePasswordNextSignIn = true* . Domena zarządzana synchronizuje ten atrybut z usługi Azure AD. Po wykryciu przez domenę zarządzaną *forceChangePasswordNextSignIn* jest ustawiona dla synchronizowanego użytkownika z usługi Azure AD, atrybut *pwdLastSet* w domenie zarządzanej jest ustawiony na *0*, co unieważnia obecnie ustawione hasło.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Czy Azure AD Domain Services zapewnić ochronę blokady konta usługi AD?
Tak. Pięć nieudanych prób wprowadzenia hasła w ciągu 2 minut w domenie zarządzanej powoduje, że konto użytkownika zostanie zablokowane przez 30 minut. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane. Nieprawidłowe próby hasła w domenie zarządzanej nie Zablokuj konta użytkownika w usłudze Azure AD. Konto użytkownika jest blokowane tylko w ramach domeny zarządzanej Azure AD Domain Services. Aby uzyskać więcej informacji, zobacz [zasady blokowania haseł i kont w domenach zarządzanych](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Czy można skonfigurować rozproszony system plików i replikację w ramach Azure AD Domain Services?
Nie. Rozproszony system plików (DFS) i replikacja nie są dostępne podczas korzystania z Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Jak są stosowane aktualizacje systemu Windows w Azure AD Domain Services?
Kontrolery domeny w domenie zarządzanej automatycznie stosują wymagane aktualizacje systemu Windows. Nie ma nic do konfigurowania lub administrowania tym miejscu. Upewnij się, że nie utworzysz reguł sieciowej grupy zabezpieczeń, która blokuje ruch wychodzący do aktualizacji systemu Windows. W przypadku własnych maszyn wirtualnych przyłączonych do domeny zarządzanej użytkownik jest odpowiedzialny za konfigurowanie i stosowanie wymaganych aktualizacji systemu operacyjnego i aplikacji.

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

* [Czy Azure AD Domain Services płatną usługę?](#is-azure-ad-domain-services-a-paid-service)
* [Czy istnieje bezpłatna wersja próbna usługi?](#is-there-a-free-trial-for-the-service)
* [Czy można wstrzymać Azure AD Domain Servicesą domenę zarządzaną?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy można przełączyć się w tryb failover Azure AD Domain Services do innego regionu dla zdarzenia DR?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Czy mogę uzyskać Azure AD Domain Services w ramach pakietu Enterprise Mobility Suite (EMS)? Czy muszę Azure AD — wersja Premium używać Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [W jakich regionach platformy Azure jest dostępna usługa?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Czy Azure AD Domain Services płatną usługę?
Tak. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Czy istnieje bezpłatna wersja próbna usługi?
Azure AD Domain Services jest uwzględniona w bezpłatnej wersji próbnej platformy Azure. Możesz zarejestrować się w celu uzyskania [bezpłatnej miesięcznej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Czy można wstrzymać Azure AD Domain Servicesą domenę zarządzaną?
Nie. Po włączeniu domeny zarządzanej Azure AD Domain Services usługa jest dostępna w wybranej sieci wirtualnej do momentu usunięcia domeny zarządzanej. Nie ma możliwości wstrzymania usługi. Rozliczanie jest kontynuowane co godzinę, dopóki nie usuniesz domeny zarządzanej.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Czy można przełączyć się w tryb failover Azure AD Domain Services do innego regionu dla zdarzenia DR?
Nie. Azure AD Domain Services obecnie nie udostępnia modelu wdrażania geograficznie nadmiarowego. Jest ona ograniczona do pojedynczej sieci wirtualnej w regionie świadczenia usługi Azure. Jeśli chcesz korzystać z wielu regionów platformy Azure, musisz uruchomić kontrolery domena usługi Active Directory na maszynach wirtualnych usługi Azure IaaS. Aby uzyskać wskazówki dotyczące architektury, zobacz sekcję [rozszerzając swoją lokalną domenę Active Directory na platformę Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Czy mogę uzyskać Azure AD Domain Services w ramach pakietu Enterprise Mobility Suite (EMS)? Czy muszę Azure AD — wersja Premium używać Azure AD Domain Services?
Nie. Azure AD Domain Services to usługa platformy Azure z płatność zgodnie z rzeczywistym użyciem, która nie jest częścią pakietu EMS. Azure AD Domain Services można używać ze wszystkimi wersjami usługi Azure AD (bezpłatna i Premium). Opłaty są naliczane godzinowo, w zależności od użycia.

### <a name="what-azure-regions-is-the-service-available-in"></a>W jakich regionach platformy Azure jest dostępna usługa?
Zapoznaj się ze stroną [usług platformy Azure według regionów](https://azure.microsoft.com/regions/#services/) , aby wyświetlić listę regionów świadczenia usługi Azure, w których Azure AD Domain Services jest dostępna.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot.md) , aby zapoznać się z typowymi problemami dotyczącymi konfigurowania i administrowania Azure AD Domain Services.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure AD Domain Services, zobacz [co to jest Azure Active Directory Domain Services?](overview.md).

Aby rozpocząć, zobacz [Tworzenie i Konfigurowanie domeny zarządzanej Azure Active Directory Domain Services](tutorial-create-instance.md).
