---
title: Ograniczone delegowanie protokołu Kerberos dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak włączyć ograniczone delegowanie protokołu Kerberos oparte na zasobach (KCD) w domenie zarządzanej Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619780"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD) w Azure Active Directory Domain Services

Podczas uruchamiania aplikacji może być konieczne, aby te aplikacje miały dostęp do zasobów w kontekście innego użytkownika. Active Directory Domain Services (AD DS) obsługuje mechanizm o nazwie *delegowanie protokołu Kerberos* , który umożliwia użycie tego przypadku. *Ograniczone* delegowanie protokołu Kerberos (KCD) następnie kompiluje ten mechanizm w celu zdefiniowania określonych zasobów, do których można uzyskać dostęp w kontekście użytkownika.

Domeny zarządzane Azure Active Directory Domain Services (Azure AD DS) są bardziej bezpiecznie zablokowane niż tradycyjne środowiska AD DS lokalnego, dlatego należy użyć bezpieczniejszego KCD *opartego na zasobach* .

W tym artykule opisano sposób konfigurowania ograniczonego delegowania protokołu Kerberos opartego na zasobach w domenie zarządzanej AD DS platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance].
* Maszyna wirtualna zarządzania systemem Windows Server, która jest dołączona do domeny zarządzanej AD DS platformy Azure.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć maszynę wirtualną z systemem Windows Server i przyłączyć ją do domeny zarządzanej][create-join-windows-vm] , a następnie [zainstalować narzędzia do zarządzania AD DS][tutorial-create-management-vm].
* Konto użytkownika, które jest członkiem grupy *administratorów DC usługi Azure AD* w dzierżawie usługi Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Omówienie ograniczonego delegowania protokołu Kerberos

Delegowanie protokołu Kerberos pozwala jednemu kontu personifikować inne konto, aby uzyskać dostęp do zasobów. Na przykład aplikacja sieci Web, która uzyskuje dostęp do składnika sieci Web zaplecza, może personifikować się jako inne konto użytkownika podczas tworzenia połączenia zaplecza. Delegowanie protokołu Kerberos jest niezabezpieczone, ponieważ nie ogranicza to zasobów, do których konto personifikuje może uzyskać dostęp.

*Ograniczone* delegowanie protokołu Kerberos (KCD) ogranicza usługi lub zasoby, które mogą łączyć się z określonym serwerem lub aplikacją podczas personifikowania innej tożsamości. Tradycyjna KCD wymaga uprawnień administratora domeny do skonfigurowania konta domeny dla usługi i ogranicza konto do działania w jednej domenie.

Tradycyjna KCD również zawiera kilka problemów. Na przykład we wcześniejszych systemach operacyjnych administrator usługi nie miał użytecznych metod, aby wiedzieć, które usługi frontonu są delegowane do usługi zasobów, do których należą. Dowolna usługa frontonu, która może delegować do usługi zasobów, jest potencjalnym punktem ataku. W przypadku naruszenia bezpieczeństwa serwera, na którym jest hostowana usługa frontonu skonfigurowana do delegowania usług zasobów, można także złamać zabezpieczenia usług zasobów.

W domenie zarządzanej nie masz uprawnień administratora domeny. W związku z tym tradycyjne KCD oparte na koncie nie można skonfigurować w domenie zarządzanej. Zamiast tego można korzystać z KCD opartego na zasobach, co jest również bezpieczniejsze.

### <a name="resource-based-kcd"></a>KCD oparte na zasobach

System Windows Server 2012 i nowsze zapewnia administratorom usługi możliwość konfigurowania ograniczonego delegowania dla swojej usługi. Ten model jest znany jako KCD oparte na zasobach. Dzięki temu administrator usługi zaplecza może zezwalać na korzystanie z usługi KCD lub odmówić określonych usług frontonu.

KCD oparte na zasobach są konfigurowane przy użyciu programu PowerShell. Używasz poleceń cmdlet [Set-ADComputer][Set-ADComputer] i [Set-ADUser][Set-ADUser] , w zależności od tego, czy konto personifikacji jest kontem komputera, czy kontem użytkownika/kontem usługi.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurowanie KCD opartego na zasobach dla konta komputera

W tym scenariuszu Załóżmy, że masz aplikację sieci Web, która jest uruchamiana na komputerze o nazwie *contoso-WEBAPP.aaddscontoso.com*.

Aplikacja sieci Web musi uzyskać dostęp do internetowego interfejsu API, który jest uruchamiany na komputerze o nazwie *contoso-API.aaddscontoso.com* w kontekście użytkowników domeny.

Wykonaj następujące kroki, aby skonfigurować ten scenariusz:

1. [Utwórz niestandardową jednostkę organizacyjną](create-ou.md). Można delegować uprawnienia do zarządzania tą niestandardową jednostką organizacyjną dla użytkowników w domenie zarządzanej.
1. [Przyłączanie do domeny maszyn wirtualnych][create-join-windows-vm], zarówno tych, na których jest uruchomiona aplikacja sieci Web, jak i tych, na których jest uruchomiony internetowy interfejs API, do domeny zarządzanej. Utwórz te konta komputerów w niestandardowej jednostce organizacyjnej w poprzednim kroku.

    > [!NOTE]
    > Konta komputerów dla aplikacji sieci Web i internetowego interfejsu API muszą znajdować się w niestandardowej jednostce organizacyjnej, w której masz uprawnienia do konfigurowania KCD opartego na zasobach. Nie można skonfigurować KCD opartego na zasobach dla konta komputera w wbudowanym kontenerze *komputery DC w usłudze AAD* .

1. Na koniec Skonfiguruj KCD oparte na zasobach przy użyciu polecenia cmdlet [Set-ADComputer][Set-ADComputer] programu PowerShell.

    Z poziomu maszyny wirtualnej zarządzania przyłączonym do domeny i zalogowanego jako konto użytkownika, które jest członkiem grupy *administratorzy kontrolera domeny usługi Azure AD* , uruchom następujące polecenia cmdlet. Podaj własne nazwy komputerów w razie konieczności:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurowanie KCD opartego na zasobach dla konta użytkownika

W tym scenariuszu Załóżmy, że masz aplikację sieci Web, która działa jako konto usługi o nazwie *appsvc*. Aplikacja sieci Web musi uzyskać dostęp do internetowego interfejsu API, który jest uruchomiony jako konto usługi o nazwie *backendsvc* w kontekście użytkowników domeny. Wykonaj następujące kroki, aby skonfigurować ten scenariusz:

1. [Utwórz niestandardową jednostkę organizacyjną](create-ou.md). Można delegować uprawnienia do zarządzania tą niestandardową jednostką organizacyjną dla użytkowników w domenie zarządzanej.
1. [Przyłącz do domeny zarządzanej maszyny wirtualne][create-join-windows-vm] , na których działa interfejs API/zasób sieci Web zaplecza. Utwórz konto komputera w ramach niestandardowej jednostki organizacyjnej.
1. Utwórz konto usługi (na przykład *appsvc*) używane do uruchamiania aplikacji sieci Web w ramach niestandardowej jednostki organizacyjnej.

    > [!NOTE]
    > Ponownie konto komputera dla maszyny wirtualnej interfejsu API sieci Web oraz konto usługi dla aplikacji sieci Web musi znajdować się w niestandardowej jednostce organizacyjnej, w której masz uprawnienia do konfigurowania KCD opartego na zasobach. Nie można skonfigurować KCD opartych na zasobach na kontach wbudowanych w kontenerze usługi *AAD DC* lub *użytkownicy usługi AAD* . Oznacza to również, że nie można użyć kont użytkowników synchronizowanych z usługą Azure AD w celu skonfigurowania KCD opartego na zasobach. Należy utworzyć i używać kont usług utworzonych w ramach usługi Azure AD DS.

1. Na koniec Skonfiguruj KCD oparte na zasobach przy użyciu polecenia cmdlet [Set-ADUser][Set-ADUser] programu PowerShell.

    Z poziomu maszyny wirtualnej zarządzania przyłączonym do domeny i zalogowanego jako konto użytkownika, które jest członkiem grupy *administratorzy kontrolera domeny usługi Azure AD* , uruchom następujące polecenia cmdlet. Podaj własne nazwy usług zgodnie z potrzebami:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak delegowanie działa w Active Directory Domain Services, zobacz [Omówienie delegowania ograniczonego protokołu Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)