---
title: Wdróż usługę Azure serwer proxy aplikacji usługi Azure AD dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak zapewnić bezpieczny dostęp do wewnętrznych aplikacji dla pracowników zdalnych przez wdrażanie i Konfigurowanie serwer proxy aplikacji usługi Azure Active Directory w Azure Active Directory Domain Servicesej domenie zarządzanej
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 7c30bca947788e93e57f462094d51f00e338c0b8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171657"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Wdrażanie serwer proxy aplikacji usługi Azure AD platformy Azure w celu bezpiecznego dostępu do wewnętrznych aplikacji w domenie zarządzanej Azure Active Directory Domain Services

Za pomocą Azure AD Domain Services (AD DS platformy Azure) można przekształcać starsze aplikacje działające lokalnie na platformę Azure. Serwer proxy aplikacji Azure Active Directory (AD) ułatwia obsługę zdalnych procesów roboczych przez bezpieczne publikowanie tych wewnętrznych aplikacji w domenie zarządzanej AD DS platformy Azure, dzięki czemu można uzyskać do nich dostęp za pośrednictwem Internetu.

Jeśli dopiero zaczynasz korzystać z usługi Azure serwer proxy aplikacji usługi Azure AD i chcesz dowiedzieć się więcej, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji wewnętrznych](../active-directory/manage-apps/application-proxy.md).

W tym artykule opisano sposób tworzenia i konfigurowania łącznika usługi Azure serwer proxy aplikacji usługi Azure AD w celu zapewnienia bezpiecznego dostępu do aplikacji w domenie zarządzanej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
    * Do korzystania z usługi Azure serwer proxy aplikacji usługi Azure AD jest wymagana **licencja Azure AD — wersja Premium** .
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Tworzenie maszyny wirtualnej z systemem Windows przyłączoną do domeny

Aby skierować ruch do aplikacji uruchomionych w środowisku, należy zainstalować składnik łącznika usługi Azure serwer proxy aplikacji usługi Azure AD. Ten łącznik usługi Azure serwer proxy aplikacji usługi Azure AD musi być zainstalowany na maszynie wirtualnej z systemem Windows Server, która jest dołączona do domeny zarządzanej. W przypadku niektórych aplikacji można wdrożyć wiele serwerów z zainstalowanym łącznikiem. Ta opcja wdrażania zapewnia większą dostępność i pomaga w obsłudze cięższych obciążeń uwierzytelniania.

Maszyna wirtualna, na której działa łącznik usługi Azure serwer proxy aplikacji usługi Azure AD, musi należeć do tej samej lub równorzędnej sieci wirtualnej, co domena zarządzana. Maszyny wirtualne, które będą hostować aplikacje publikowane przy użyciu serwera proxy aplikacji, również muszą zostać wdrożone w tej samej sieci wirtualnej platformy Azure.

Aby utworzyć maszynę wirtualną dla łącznika usługi Azure serwer proxy aplikacji usługi Azure AD, wykonaj następujące czynności:

1. [Utwórz niestandardową jednostkę organizacyjną](create-ou.md). Można delegować uprawnienia do zarządzania tą niestandardową jednostką organizacyjną dla użytkowników w domenie zarządzanej. Maszyny wirtualne dla usługi Azure serwer proxy aplikacji usługi Azure AD i uruchamiania aplikacji muszą być częścią niestandardowej jednostki organizacyjnej, a nie z domyślną jednostką organizacyjną *domeny usługi AAD* .
1. [Przyłączanie do domeny do maszyn wirtualnych][create-join-windows-vm], na których jest uruchomiony łącznik usługi Azure serwer proxy aplikacji usługi Azure AD, oraz tych, które uruchamiają aplikacje, do domeny zarządzanej. Utwórz te konta komputerów w niestandardowej jednostce organizacyjnej w poprzednim kroku.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Pobierz łącznik usługi Azure serwer proxy aplikacji usługi Azure AD

Wykonaj następujące kroki, aby pobrać łącznik usługi Azure serwer proxy aplikacji usługi Azure AD. Pobrany plik instalacyjny jest kopiowany do maszyny wirtualnej serwera proxy aplikacji w następnej sekcji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta użytkownika, które ma uprawnienia *administratora przedsiębiorstwa* w usłudze Azure AD.
1. Wyszukaj i wybierz **Azure Active Directory** w górnej części portalu, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. Z menu po lewej stronie wybierz pozycję **serwer proxy aplikacji** . Aby utworzyć pierwszy łącznik i włączyć serwer proxy aplikacji, wybierz link, aby **pobrać łącznik**.
1. Na stronie pobierania zaakceptuj postanowienia licencyjne i umowę o ochronie prywatności, a następnie wybierz pozycję **zaakceptuj postanowienia & Pobierz**.

    ![Pobierz łącznik serwera proxy aplikacja usługi Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalowanie i rejestrowanie łącznika usługi Azure serwer proxy aplikacji usługi Azure AD

Za pomocą maszyny wirtualnej gotowej do użycia jako łącznik usługi Azure serwer proxy aplikacji usługi Azure AD teraz skopiuj i uruchom plik instalacyjny pobrany z Azure Portal.

1. Skopiuj plik instalacyjny programu Azure serwer proxy aplikacji usługi Azure AD Connector na maszynę wirtualną.
1. Uruchom plik instalacyjny, taki jak *AADApplicationProxyConnectorInstaller.exe*. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania.
1. Podczas instalacji zostanie wyświetlony monit o zarejestrowanie łącznika przy użyciu serwera proxy aplikacji w katalogu usługi Azure AD.
   * Podaj poświadczenia dla administratora globalnego w katalogu usługi Azure AD. Poświadczenia administratora globalnego usługi Azure AD mogą różnić się od poświadczeń platformy Azure w portalu

        > [!NOTE]
        > Konto administratora globalnego użyte do zarejestrowania łącznika musi należeć do tego samego katalogu, w którym jest włączona usługa serwera proxy aplikacji.
        >
        > Jeśli na przykład domena usługi Azure AD to *contoso.com*, Administrator globalny powinien mieć lub mieć `admin@contoso.com` inny prawidłowy alias w tej domenie.

   * Jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona dla maszyny wirtualnej, na której jest instalowany łącznik, ekran rejestracji może zostać zablokowany. Aby zezwolić na dostęp, postępuj zgodnie z instrukcjami w komunikacie o błędzie lub wyłącz zaawansowane zabezpieczenia programu Internet Explorer podczas procesu instalacji.
   * Jeśli rejestracja łącznika nie powiedzie się, zobacz [Rozwiązywanie problemów z serwerem proxy aplikacji](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Po zakończeniu instalacji należy wyświetlić uwagę dla środowisk z serwerem proxy wychodzącego. Aby skonfigurować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD do pracy przez wychodzący serwer proxy, uruchom dostarczony skrypt, taki jak `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na stronie serwer proxy aplikacji w Azure Portal nowy łącznik zostanie wyświetlony ze stanem *aktywne*, jak pokazano w następującym przykładzie:

    ![Nowy łącznik usługi Azure serwer proxy aplikacji usługi Azure AD wyświetlany jako aktywny w Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Aby zapewnić wysoką dostępność aplikacji uwierzytelnianych za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD, można zainstalować łączniki na wielu maszynach wirtualnych. Powtórz te same kroki opisane w poprzedniej sekcji, aby zainstalować łącznik na innych serwerach przyłączonych do domeny zarządzanej.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Włączanie ograniczonego delegowania protokołu Kerberos opartego na zasobach

Jeśli chcesz używać funkcji logowania jednokrotnego w aplikacjach przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA), przyznaj łączniki platformy Azure serwer proxy aplikacji usługi Azure AD uprawnienia do personifikacji użytkowników oraz wysyłania i odbierania tokenów w ich imieniu. Aby przyznać te uprawnienia, należy skonfigurować ograniczone delegowanie protokołu Kerberos (KCD) dla łącznika w celu uzyskania dostępu do zasobów w domenie zarządzanej. Ponieważ nie masz uprawnień administratora domeny w domenie zarządzanej, tradycyjne KCD na poziomie konta nie można skonfigurować w domenie zarządzanej. Zamiast tego należy użyć KCD opartego na zasobach.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD) w Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Musisz się zalogować na konto użytkownika, które jest członkiem grupy *administratorzy kontrolera domeny usługi Azure AD* w dzierżawie usługi Azure AD, aby uruchomić następujące polecenia cmdlet programu PowerShell.
>
> Konta komputerów dla maszyny wirtualnej i maszyn wirtualnych łącznika serwera proxy aplikacji muszą znajdować się w niestandardowej jednostce organizacyjnej, w której masz uprawnienia do konfigurowania KCD opartego na zasobach. Nie można skonfigurować KCD opartego na zasobach dla konta komputera w wbudowanym kontenerze *komputery DC w usłudze AAD* .

Użyj elementu [Get-ADComputer][Get-ADComputer] , aby pobrać ustawienia dla komputera, na którym zainstalowano łącznik usługi Azure serwer proxy aplikacji usługi Azure AD. Z poziomu maszyny wirtualnej zarządzania przyłączonym do domeny i zalogowanego jako konto użytkownika, które jest członkiem grupy *administratorzy kontrolera domeny usługi Azure AD* , uruchom następujące polecenia cmdlet.

Poniższy przykład pobiera informacje o koncie komputera o nazwie *appproxy.aaddscontoso.com*. Podaj własną nazwę komputera dla maszyny wirtualnej serwer proxy aplikacji usługi Azure AD platformy Azure, która została skonfigurowana w poprzednich krokach.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Dla każdego serwera aplikacji, na którym są uruchomione aplikacje związane z platformą Azure serwer proxy aplikacji usługi Azure AD Użyj polecenia cmdlet [Set-ADComputer][Set-ADComputer] programu PowerShell, aby skonfigurować KCD oparte na zasobach. W poniższym przykładzie łącznik usługi Azure serwer proxy aplikacji usługi Azure AD ma przyznane uprawnienia do korzystania z komputera *AppServer.aaddscontoso.com* :

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

W przypadku wdrażania wielu łączników serwer proxy aplikacji usługi Azure AD platformy Azure należy skonfigurować KCD oparte na zasobach dla każdego wystąpienia łącznika.

## <a name="next-steps"></a>Następne kroki

Korzystając z usługi Azure serwer proxy aplikacji usługi Azure AD zintegrowanej z usługą Azure AD DS, Opublikuj aplikacje dla użytkowników w celu uzyskania dostępu. Aby uzyskać więcej informacji, zobacz [publikowanie aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer