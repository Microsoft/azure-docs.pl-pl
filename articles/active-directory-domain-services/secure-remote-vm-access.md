---
title: Zabezpieczanie zdalnego dostępu do maszyny wirtualnej w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak zabezpieczyć zdalny dostęp do maszyn wirtualnych przy użyciu serwera zasad sieciowych (NPS) i usługi Azure AD Multi-Factor Authentication z wdrożeniem Usługi pulpitu zdalnego w domenie zarządzanej Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618930"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Bezpieczny dostęp zdalny do maszyn wirtualnych w Azure Active Directory Domain Services

Aby zabezpieczyć zdalny dostęp do maszyn wirtualnych, które działają w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS), można użyć Usługi pulpitu zdalnego (RDS) i serwera zasad sieciowych (NPS). Usługa Azure AD DS uwierzytelnia użytkowników podczas żądania dostępu za pomocą środowiska RDS. W celu zwiększenia bezpieczeństwa można zintegrować usługę Azure AD Multi-Factor Authentication, aby zapewnić dodatkowy monit uwierzytelniania podczas logowania. Usługa Azure AD Multi-Factor Authentication używa rozszerzenia serwera NPS w celu zapewnienia tej funkcji.

> [!IMPORTANT]
> Zalecanym sposobem bezpiecznego łączenia się z maszynami wirtualnymi w domenie zarządzanej AD DS platformy Azure jest korzystanie z usługi Azure bastionu, a w pełni zarządzanej platformą usługę PaaS, która jest dostarczana w sieci wirtualnej. Host bastionu zapewnia bezpieczną i bezproblemową łączność Remote Desktop Protocol (RDP) z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu SSL. Po nawiązaniu połączenia za pośrednictwem hosta bastionu maszyny wirtualne nie potrzebują publicznego adresu IP i nie jest konieczne używanie sieciowych grup zabezpieczeń w celu udostępnienia dostępu do protokołu RDP na porcie TCP 3389.
>
> Zdecydowanie zalecamy korzystanie z usługi Azure bastionu we wszystkich regionach, w których są obsługiwane. W regionach bez dostępności usługi Azure bastionu wykonaj kroki opisane w tym artykule, aż do momentu udostępnienia usługi Azure bastionu. Weź pod uwagę przypisanie publicznych adresów IP do maszyn wirtualnych przyłączonych do platformy Azure AD DS, w których dozwolony jest cały ruch przychodzący RDP.
>
> Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure bastionu?][bastion-overview].

W tym artykule opisano sposób konfigurowania usług pulpitu zdalnego na platformie Azure AD DS i opcjonalnie używania rozszerzenia serwera NPS usługi Azure AD Multi-Factor Authentication.

![Usługi pulpitu zdalnego (RDS) — Omówienie](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance].
* Podsieć *obciążeń* utworzona w sieci wirtualnej Azure Active Directory Domain Services.
    * W razie potrzeby [Skonfiguruj sieci wirtualne dla Azure Active Directory Domain Services domeny zarządzanej][configure-azureadds-vnet].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Wdróż i skonfiguruj środowisko Pulpit zdalny

Aby rozpocząć, należy utworzyć co najmniej dwie maszyny wirtualne platformy Azure z systemem Windows Server 2016 lub Windows Server 2019. W celu zapewnienia nadmiarowości i wysokiej dostępności środowiska Pulpit zdalny (RD) można później dodać i zrównoważyć obciążenie dodatkowymi hostami.

Sugerowane wdrożenie usług pulpitu zdalnego obejmuje dwie następujące maszyny wirtualne:

* *RDGVM01* — uruchamia serwer brokera połączeń usług pulpitu zdalnego, serwer usług pulpitu zdalnego dostęp w sieci Web i serwer bramy usług pulpitu zdalnego.
* *RDSHVM01* — uruchamia serwer hosta sesji usług pulpitu zdalnego.

Upewnij się, że maszyny wirtualne są wdrażane w podsieci *obciążeń* sieci wirtualnej platformy Azure AD DS, a następnie Dołącz maszyny wirtualne do domeny zarządzanej. Aby uzyskać więcej informacji, zobacz jak [utworzyć i dołączyć maszynę wirtualną z systemem Windows Server do domeny zarządzanej][tutorial-create-join-vm].

Wdrożenie środowiska pulpitu zdalnego zawiera kilka kroków. Istniejący Przewodnik wdrażania usług pulpitu zdalnego może być używany bez żadnych konkretnych zmian do użycia w domenie zarządzanej:

1. Zaloguj się do maszyn wirtualnych utworzonych dla środowiska usług pulpitu zdalnego przy użyciu konta należącego do grupy *administratorzy kontrolera domeny usługi Azure AD* , np. *contosoadmin*.
1. Aby utworzyć i skonfigurować RDS, użyj istniejącego [przewodnika wdrażania środowiska pulpit zdalny][deploy-remote-desktop]. Dystrybuuj składniki serwera usług pulpitu zdalnego na maszynach wirtualnych platformy Azure zgodnie z potrzebami.
    * Specyficzne dla platformy Azure AD DS — podczas konfigurowania licencjonowania usług pulpitu zdalnego Ustaw dla niego tryb **na urządzenie** , a nie **na użytkownika** zgodnie z opisem w przewodniku wdrażania.
1. Jeśli chcesz zapewnić dostęp przy użyciu przeglądarki sieci Web, [skonfiguruj pulpit zdalny klienta sieci Web dla użytkowników][rd-web-client].

W przypadku wdrożenia usług pulpitu zdalnego w domenie zarządzanej można zarządzać tą usługą i korzystać z niej w taki sam sposób, jak w przypadku lokalnej domeny AD DS.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Wdrażanie i Konfigurowanie serwera zasad sieciowych i rozszerzenia serwera NPS usługi Azure AD MFA

Jeśli chcesz zwiększyć bezpieczeństwo środowiska logowania użytkowników, możesz opcjonalnie zintegrować środowisko usług pulpitu zdalnego z usługą Azure AD Multi-Factor Authentication. W przypadku tej konfiguracji użytkownicy otrzymują dodatkowy monit podczas logowania w celu potwierdzenia ich tożsamości.

Aby zapewnić tę możliwość, w środowisku jest instalowany dodatkowy serwer zasad sieciowych (NPS) wraz z rozszerzeniem zasad sieciowych usługi Azure AD Multi-Factor Authentication. To rozszerzenie integruje się z usługą Azure AD, aby zażądać i zwrócić stan zaskakujących pytań dotyczących uwierzytelniania wieloskładnikowego.

Użytkownicy muszą być [zarejestrowani do korzystania z usługi Azure ad Multi-Factor Authentication][user-mfa-registration], co może wymagać dodatkowych licencji usługi Azure AD.

Aby zintegrować usługę Azure AD Multi-Factor Authentication w środowisku usługi Azure AD DS Pulpit zdalny, Utwórz serwer zasad sieciowych i zainstaluj rozszerzenie:

1. Utwórz dodatkową maszynę wirtualną z systemem Windows Server 2016 lub 2019, taką jak *NPSVM01*, która jest połączona z podsiecią *obciążeń* w sieci wirtualnej platformy Azure AD DS. Dołącz maszynę wirtualną do domeny zarządzanej.
1. Zaloguj się na serwerze zasad sieciowych NPS jako konto, które jest częścią grupy *administratorów DC usługi Azure AD* , np. *contosoadmin*.
1. W obszarze **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**, a następnie Zainstaluj rolę *usług zasad sieciowych i dostępu sieciowego* .
1. Aby [zainstalować i skonfigurować rozszerzenie NPS usługi Azure AD MFA][nps-extension], należy użyć istniejącego artykułu z poradnikiem.

Po zainstalowaniu serwera NPS i usługi Azure AD Multi-Factor Authentication rozszerzenia serwera NPS wykonaj następną sekcję, aby skonfigurować ją do użycia ze środowiskiem usług pulpitu zdalnego.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integracja Pulpit zdalny bramy i usługi Azure AD Multi-Factor Authentication

Aby zintegrować rozszerzenie zasad sieciowych Multi-Factor Authentication usługi Azure AD, Skorzystaj z istniejącego artykułu z poradnikiem, aby [zintegrować infrastrukturę pulpit zdalny Gateway przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i usługi Azure AD][azure-mfa-nps-integration].

Do integracji z domeną zarządzaną są konieczne następujące dodatkowe opcje konfiguracji:

1. Nie [Rejestruj serwera NPS w Active Directory][register-nps-ad]. Ten krok zakończy się niepowodzeniem w domenie zarządzanej.
1. W [kroku 4 Aby skonfigurować zasady sieciowe][create-nps-policy], zaznacz również pole wyboru **Ignoruj właściwości telefonowania konta użytkownika**.
1. Jeśli używasz systemu Windows Server 2019 dla rozszerzenia serwera NPS i usługi Azure AD Multi-Factor Authentication, uruchom następujące polecenie, aby zaktualizować bezpieczny kanał, aby umożliwić serwerowi NPS poprawne komunikowanie się:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Po zalogowaniu się użytkownicy będą monitowani o dodatkowy czynnik uwierzytelniania, na przykład wiadomość tekstowa lub monit w aplikacji Microsoft Authenticator.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o zwiększaniu odporności wdrożenia, zobacz [usługi pulpitu zdalnego-wysoka dostępność][rds-high-availability].

Aby uzyskać więcej informacji na temat zabezpieczania logowania użytkowników, zobacz [jak to działa: usługa Azure AD Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability