---
title: Tworzenie lasu zasobów Azure AD Domain Services przy użyciu Azure PowerShell | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć i skonfigurować Las zasobów Azure Active Directory Domain Services i Las wychodzący w środowisku lokalnym Active Directory Domain Services przy użyciu Azure PowerShell.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: joflore
ms.openlocfilehash: 32ec3eface215330aba9e40b46e45b97b5c07091
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041107"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Utwórz Las zasobów Azure Active Directory Domain Services i zaufanie lasu wychodzącego do domeny lokalnej przy użyciu Azure PowerShell

W środowiskach, w których nie można synchronizować skrótów haseł lub masz użytkowników, którzy logują się wyłącznie przy użyciu kart inteligentnych, aby nie wiedzieli hasła, możesz użyć lasu zasobów w Azure Active Directory Domain Services (Azure AD DS). Las zasobów używa jednokierunkowego zaufania wychodzącego z platformy Azure AD DS do co najmniej jednego środowiska AD DS lokalnego. Ta relacja zaufania umożliwia użytkownikom, aplikacjom i komputerom uwierzytelnianie w domenie lokalnej z domeny zarządzanej AD DS platformy Azure. W lesie zasobów lokalne skróty haseł nigdy nie są synchronizowane.

![Diagram zaufania lasów z usługi Azure AD DS do lokalnego AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie lasu zasobów usługi Azure AD DS przy użyciu Azure PowerShell
> * Utwórz jednokierunkowe zaufanie lasu wychodzącego w domenie zarządzanej przy użyciu Azure PowerShell
> * Skonfiguruj system DNS w środowisku lokalnym AD DS, aby obsługiwał łączność z domeną zarządzaną
> * Utwórz jednokierunkowe zaufanie lasu przychodzącego w środowisku lokalnym AD DS
> * Testowanie i weryfikowanie relacji zaufania na potrzeby uwierzytelniania i dostępu do zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Lasy zasobów domeny zarządzanej nie obsługują obecnie usługi Azure HDInsight ani Azure Files. Domyślne lasy użytkownika domeny zarządzanej obsługują obie te dodatkowe usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].

* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](/powershell/azure/install-az-ps).
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Zainstaluj i skonfiguruj program Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł Azure AD PowerShell i nawiązać połączenie z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] .
* Aby włączyć usługę Azure AD DS, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usługi Azure AD DS, musisz mieć uprawnienia *współautora* w ramach subskrypcji platformy Azure.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym artykule opisano tworzenie i Konfigurowanie zaufania lasu wychodzącego z domeny zarządzanej przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="deployment-process"></a>Proces wdrażania

Jest to proces wieloczęściowy służący do tworzenia lasu zasobów domeny zarządzanej i relacji zaufania z AD DSami lokalnymi. Następujące kroki wysokiego poziomu kompilują zaufane środowisko hybrydowe:

1. Utwórz nazwę główną usługi domeny zarządzanej.
1. Utwórz Las zasobów domeny zarządzanej.
1. Utwórz hybrydową łączność sieciową przy użyciu sieci VPN typu lokacja-lokacja lub ekspresowej trasy.
1. Utwórz stronę zarządzaną domeny relacji zaufania.
1. Utwórz lokalną AD DS stronie relacji zaufania.

Przed rozpoczęciem upewnij się, że rozumiesz [zagadnienia dotyczące sieci, nazewnictwo lasów i wymagania DNS](tutorial-create-forest-trust.md#networking-considerations). Nie można zmienić nazwy lasu domeny zarządzanej po jej wdrożeniu.

## <a name="create-the-azure-ad-service-principal"></a>Tworzenie jednostki usługi Azure AD

Usługa Azure AD DS wymaga nazwy głównej usługi, która synchronizuje dane z usługi Azure AD. Ten podmiot zabezpieczeń należy utworzyć w dzierżawie usługi Azure AD przed utworzeniem lasu zasobów domeny zarządzanej.

Utwórz nazwę główną usługi Azure AD dla usługi Azure AD DS, aby komunikować się i uwierzytelniać siebie. Określony identyfikator aplikacji jest używany o nazwie *usługi kontrolera domeny* o identyfikatorze *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* . Nie zmieniaj tego identyfikatora aplikacji.

Utwórz nazwę główną usługi Azure AD przy użyciu polecenia cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Tworzenie lasu zasobów domeny zarządzanej

Aby utworzyć Las zasobów domeny zarządzanej, należy użyć `New-AzureAaddsForest` skryptu. Ten skrypt jest częścią szerszego zestawu poleceń, które obsługują tworzenie lasów zasobów domeny zarządzanej i zarządzanie nimi, w tym tworzenie jednokierunkowego lasu powiązanego w następnej sekcji. Te skrypty są dostępne w [Galeria programu PowerShell](https://www.powershellgallery.com/) i są podpisane cyfrowo przez Zespół inżynieryjny usługi Azure AD.

1. Najpierw utwórz grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup][New-AzResourceGroup] . W poniższym przykładzie grupa zasobów ma nazwę moja *zasobów* i jest tworzona w regionie *zachodnim* . Użyj własnej nazwy i żądanego regionu:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Zainstaluj `New-AaddsResourceForestTrust` skrypt z [Galeria programu PowerShell][powershell-gallery] przy użyciu polecenia cmdlet [Install-Script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Przejrzyj następujące parametry, które są odpowiednie dla `New-AzureAaddsForest` skryptu. Upewnij się, że masz również wymagania wstępne **Azure PowerShell** i modułów **programu POWERSHELL usługi Azure AD** . Upewnij się, że zaplanowano wymagania dotyczące sieci wirtualnej w celu zapewnienia łączności z aplikacjami i lokalnymi.

    | Nazwa                         | Parametr skryptu          | Opis |
    |:-----------------------------|---------------------------|:------------|
    | Subskrypcja                 | *-azureSubscriptionId*    | Identyfikator subskrypcji używany na potrzeby rozliczeń w usłudze Azure AD DS. Listę subskrypcji można uzyskać za pomocą polecenia cmdlet [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Grupa zasobów               | *-aaddsResourceGroupName* | Nazwa grupy zasobów dla domeny zarządzanej i skojarzonych zasobów. |
    | Lokalizacja                     | *-aaddsLocation*          | Region świadczenia usługi Azure, który będzie hostować domenę zarządzaną. Aby uzyskać dostęp do dostępnych regionów, zobacz sekcję [Obsługiwane regiony dla platformy Azure AD DS.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Administrator usługi Azure AD DS    | *-aaddsAdminUser*         | Główna nazwa użytkownika pierwszego administratora domeny zarządzanej. To konto musi być istniejącym kontem użytkownika w chmurze w Twojej Azure Active Directory. Użytkownik i użytkownik uruchamiający skrypt są dodawane do grupy *administratorów kontrolera domeny usługi AAD* . |
    | Nazwa domeny usługi Azure AD DS      | *-aaddsDomainName*        | Nazwa FQDN domeny zarządzanej, zgodnie z poprzednimi wskazówkami dotyczącymi wybierania nazwy lasu. |

    `New-AzureAaddsForest`Skrypt może utworzyć sieć wirtualną platformy Azure i podsieć usługi azure AD DS, jeśli te zasoby jeszcze nie istnieją. Skrypt może opcjonalnie utworzyć podsieci obciążenia, jeśli określono:

    | Nazwa                              | Parametr skryptu                  | Opis |
    |:----------------------------------|:----------------------------------|:------------|
    | Nazwa sieci wirtualnej              | *-aaddsVnetName*                  | Nazwa sieci wirtualnej dla domeny zarządzanej.|
    | Przestrzeń adresowa                     | *-aaddsVnetCIDRAddressSpace*      | Zakres adresów sieci wirtualnej w notacji CIDR (w przypadku tworzenia sieci wirtualnej).|
    | Nazwa podsieci AD DS platformy Azure           | *-aaddsSubnetName*                | Nazwa podsieci sieci wirtualnej *aaddsVnetName* obsługującej domenę zarządzaną. Nie Wdrażaj własnych maszyn wirtualnych i obciążeń w tej podsieci. |
    | Zakres adresów AD DS platformy Azure         | *-aaddsSubnetCIDRAddressRange*    | Zakres adresów podsieci w notacji CIDR dla wystąpienia usług AAD DS, na przykład *192.168.1.0/24* . Zakres adresów musi być zawarty w zakresie adresów sieci wirtualnej i różnić się od innych podsieci. |
    | Nazwa podsieci obciążenia (opcjonalnie)   | *-workloadSubnetName*             | Opcjonalna nazwa podsieci w sieci wirtualnej *aaddsVnetName* do tworzenia dla własnych obciążeń aplikacji. Maszyny wirtualne i aplikacje, a także są połączone z równorzędną siecią wirtualną platformy Azure. |
    | Zakres adresów obciążenia (opcjonalnie) | *-workloadSubnetCIDRAddressRange* | Opcjonalny zakres adresów podsieci w notacji CIDR dla obciążenia aplikacji, na przykład *192.168.2.0/24* . Zakres adresów musi być zawarty w zakresie adresów sieci wirtualnej i różnić się od innych podsieci.|

1. Teraz Utwórz Las zasobów domeny zarządzanej przy użyciu `New-AzureAaaddsForest` skryptu. Poniższy przykład tworzy Las o nazwie *addscontoso.com* i tworzy podsieć obciążenia. Podaj własne nazwy parametrów i zakresy adresów IP lub istniejące sieci wirtualne.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Tworzenie lasu zasobów domeny zarządzanej i obsługę zasobów zajmuje trochę czasu. Zezwalaj na ukończenie skryptu. Przejdź do następnej sekcji, aby skonfigurować połączenie z siecią lokalną, podczas gdy Las zasobów usługi Azure AD w tle.

## <a name="configure-and-validate-network-settings"></a>Konfigurowanie i weryfikowanie ustawień sieci

W przypadku kontynuowania wdrażania domeny zarządzanej, skonfiguruj i sprawdź poprawność łączności sieciowej hybrydowej z lokalnym centrum danych. Potrzebna jest również maszyna wirtualna zarządzania do użycia z domeną zarządzaną do regularnej konserwacji. Niektóre połączenia hybrydowe mogą już istnieć w Twoim środowisku lub może być konieczne współpracują z innymi osobami w zespole, aby skonfigurować połączenia.

Przed rozpoczęciem upewnij się, że rozumiesz [zagadnienia i zalecenia dotyczące sieci](tutorial-create-forest-trust.md#networking-considerations).

1. Utwórz połączenie hybrydowe z siecią lokalną na platformie Azure przy użyciu połączenia sieci VPN platformy Azure lub usługi Azure ExpressRoute. Konfiguracja sieci hybrydowej znajduje się poza zakresem tej dokumentacji i może już istnieć w Twoim środowisku. Aby uzyskać szczegółowe informacje dotyczące konkretnych scenariuszy, zobacz następujące artykuły:

    * [Sieć VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Omówienie usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Jeśli połączenie zostanie utworzone bezpośrednio do sieci wirtualnej domeny zarządzanej, należy użyć oddzielnej podsieci bramy. Nie należy tworzyć bramy w podsieci domeny zarządzanej.

1. Aby administrować domeną zarządzaną, należy utworzyć maszynę wirtualną zarządzania, przyłączyć ją do domeny zarządzanej, a następnie zainstalować wymagane narzędzia do zarządzania AD DS.

    Podczas wdrażania lasu zasobów domeny zarządzanej należy [utworzyć maszynę wirtualną z systemem Windows Server](./join-windows-vm.md) , a następnie [zainstalować podstawowe narzędzia do zarządzania AD DS](./tutorial-create-management-vm.md) w celu zainstalowania wymaganych narzędzi do zarządzania. Zaczekaj na przyłączenie maszyny wirtualnej zarządzania do domeny zarządzanej do momentu pomyślnego wdrożenia jednego z poniższych kroków.

1. Sprawdź poprawność łączności sieciowej między siecią lokalną a siecią wirtualną platformy Azure.

    * Upewnij się, że lokalny kontroler domeny może nawiązać połączenie z zarządzaną maszyną wirtualną przy użyciu `ping` pulpitu zdalnego, na przykład.
    * Sprawdź, czy maszyna wirtualna zarządzania może połączyć się z lokalnymi kontrolerami domeny za pomocą narzędzia, takiego jak `ping` .

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services** . Wybierz domenę zarządzaną, taką jak *aaddscontoso.com* , i poczekaj na zgłoszenie stanu jako **uruchomiony** .

    Po uruchomieniu programu [zaktualizuj ustawienia DNS dla sieci wirtualnej platformy Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) , a następnie [Włącz konta użytkowników dla platformy Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby zakończyć konfiguracje dla lasu zasobów domeny zarządzanej.

1. Zanotuj adresy DNS widoczne na stronie Przegląd. Te adresy są potrzebne podczas konfigurowania lokalnego Active Directory relacji zaufania w poniższej sekcji.
1. Uruchom ponownie maszynę wirtualną zarządzania, aby otrzymać nowe ustawienia DNS, a następnie [Dołącz maszynę wirtualną do domeny zarządzanej](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Po przyłączeniu maszyny wirtualnej zarządzania do domeny zarządzanej Połącz się ponownie przy użyciu pulpitu zdalnego.

    W wierszu polecenia Użyj `nslookup` nazwy lasu zasobów domeny zarządzanej, aby sprawdzić poprawność rozpoznawania nazw dla lasu zasobów.

    ```console
    nslookup aaddscontoso.com
    ```

    Polecenie powinno zwrócić dwa adresy IP dla lasu zasobów.

## <a name="create-the-forest-trust"></a>Tworzenie zaufania lasu

Zaufanie lasu ma dwie części — zaufanie lasu ruchu wychodzącego w lesie zasobów domeny zarządzanej i jednokierunkowe zaufanie lasu przychodzącego w lesie lokalnym AD DS. Po obu stronach tej relacji zaufania można utworzyć ręcznie. Po utworzeniu obu stron Użytkownicy i zasoby mogą pomyślnie uwierzytelniać się przy użyciu zaufania lasu. Las zasobów domeny zarządzanej obsługuje do 5 1-kierunkowych relacji zaufania między lasami w lasach lokalnych.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Tworzenie strony zarządzanej domeny relacji zaufania

Użyj `Add-AaddsResourceForestTrust` skryptu, aby utworzyć stronę zarządzaną domeny relacji zaufania. Najpierw zainstaluj `Add-AaddsResourceForestTrust` skrypt z [Galeria programu PowerShell][powershell-gallery] przy użyciu polecenia cmdlet [Install-Script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Teraz podaj następujące informacje dotyczące skryptu:

| Nazwa                               | Parametr skryptu     | Opis |
|:-----------------------------------|:---------------------|:------------|
| Nazwa domeny usługi Azure AD DS            | *-ManagedDomainFqdn* | Nazwa FQDN domeny zarządzanej, na przykład *aaddscontoso.com* |
| Lokalna nazwa domeny AD DS      | *-TrustFqdn*         | Nazwa FQDN lasu zaufanego, na przykład *OnPrem.contoso.com* |
| Przyjazna nazwa zaufania                | *-TrustFriendlyName* | Przyjazna nazwa relacji zaufania. |
| Adresy IP lokalnego AD DS DNS | *-TrustDnsIPs*       | Rozdzielana przecinkami lista adresów IPv4 serwera DNS dla domeny zaufanej. |
| Hasło zaufania                     | *-TrustPassword*     | Złożone hasło dla relacji zaufania. To hasło jest również wprowadzane podczas tworzenia jednokierunkowego zaufania przychodzącego w AD DS lokalnej. |
| Poświadczenia                        | *-Poświadczenia*       | Poświadczenia używane do uwierzytelniania na platformie Azure. Użytkownik musi należeć do *grupy Administratorzy kontrolera domeny usługi AAD* . Jeśli nie zostanie podany, skrypt będzie monitował o uwierzytelnienie. |

Poniższy przykład tworzy relację zaufania o nazwie *myAzureADDSTrust* do *OnPrem.contoso.com* . Użyj własnych nazw parametrów i haseł:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Zapamiętaj hasło zaufania. Musisz użyć tego samego hasła podczas tworzenia lokalnej strony zaufania.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurowanie systemu DNS w domenie lokalnej

Aby prawidłowo rozpoznać domenę zarządzaną w środowisku lokalnym, może być konieczne dodanie usług przesyłania dalej do istniejących serwerów DNS. Jeśli nie skonfigurowano środowiska lokalnego do komunikowania się z domeną zarządzaną, wykonaj następujące czynności z poziomu stacji roboczej zarządzania dla lokalnej domeny AD DS:

1. Wybierz pozycję **Uruchom | Narzędzia administracyjne | System DNS**
1. Kliknij prawym przyciskiem myszy opcję serwer DNS, na przykład *myAD01* , wybierz polecenie **Właściwości**
1. Wybierz opcję **usługi przesyłania dalej** , a następnie pozycję **Edytuj** , aby dodać dodatkowe usługi przesyłania dalej.
1. Dodaj adresy IP domeny zarządzanej, takie jak *10.0.1.4* i *10.0.1.5* .
1. W wierszu polecenia lokalnego Sprawdź poprawność rozpoznawania nazw przy użyciu **polecenia nslookup** nazwy domeny lasu zasobów domeny zarządzanej. Na przykład `Nslookup aaddscontoso.com` należy zwrócić dwa adresy IP dla lasu zasobów domeny zarządzanej.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Utwórz przychodzące zaufanie lasu w domenie lokalnej

Lokalna domena AD DS musi mieć przychodzące zaufanie lasu dla domeny zarządzanej. Relację zaufania należy utworzyć ręcznie w domenie AD DS lokalnej, nie można jej utworzyć przy użyciu Azure Portal.

Aby skonfigurować zaufanie przychodzące w domenie AD DS lokalnego, wykonaj następujące kroki na stacji roboczej zarządzania dla lokalnej domeny AD DS:

1. Wybierz pozycję **Uruchom | Narzędzia administracyjne | Active Directory domen i relacji zaufania**
1. Wybierz pozycję domena, na przykład *OnPrem.contoso.com* , a następnie wybierz pozycję **Właściwości** .
1. Wybierz kartę **relacje zaufania** , a następnie pozycję **nowe zaufanie**
1. Wprowadź nazwę domeny zarządzanej, na przykład *aaddscontoso.com* , a następnie wybierz przycisk **dalej** .
1. Wybierz opcję utworzenia **zaufania lasu** , aby utworzyć **jeden ze sposobów: zaufanie przychodzące** .
1. Wybierz, aby utworzyć relację zaufania **tylko dla tej domeny** . W następnym kroku utworzysz relację zaufania w Azure Portal dla domeny zarządzanej.
1. Wybierz opcję użycia **uwierzytelniania w całym lesie** , a następnie wprowadź i Potwierdź hasło zaufania. To samo hasło jest również wprowadzane w Azure Portal w następnej sekcji.
1. Przejdź do kolejnych kilku okien z opcjami domyślnymi, a następnie wybierz opcję **nie, nie potwierdzaj zaufania wychodzącego** . Nie można zweryfikować relacji zaufania, ponieważ konto administratora delegowanego do lasu zasobów domeny zarządzanej nie ma wymaganych uprawnień. To zachowanie jest celowe.
1. Wybierz **zakończenie**

## <a name="validate-resource-authentication"></a>Weryfikowanie uwierzytelniania zasobów

Następujące typowe scenariusze pozwalają sprawdzić, czy zaufanie lasu prawidłowo uwierzytelnia użytkowników i dostęp do zasobów:

* [Uwierzytelnianie użytkownika lokalnego z lasu zasobów usługi Azure AD DS](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Dostęp do zasobów w lesie zasobów AD DS platformy Azure przy użyciu lokalnego użytkownika](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Włącz udostępnianie plików i drukarek](#enable-file-and-printer-sharing)
    * [Tworzenie grupy zabezpieczeń i Dodawanie członków](#create-a-security-group-and-add-members)
    * [Tworzenie udziału plików na potrzeby dostępu między lasami](#create-a-file-share-for-cross-forest-access)
    * [Weryfikowanie uwierzytelniania między lasami w ramach zasobu](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Uwierzytelnianie użytkownika lokalnego z lasu zasobów usługi Azure AD DS

Należy mieć przyłączoną maszynę wirtualną z systemem Windows Server do domeny zasobów domeny zarządzanej. Ta maszyna wirtualna służy do testowania lokalnego użytkownika na maszynie wirtualnej.

1. Nawiąż połączenie z maszyną wirtualną z systemem Windows Server przyłączoną do lasu zasobów domeny zarządzanej przy użyciu Pulpit zdalny i poświadczeń administratora domeny zarządzanej. Jeśli wystąpi błąd Uwierzytelnianie na poziomie sieci (NLA), sprawdź, czy używane konto użytkownika nie jest kontem użytkownika domeny.

    > [!TIP]
    > Aby bezpiecznie połączyć się z maszynami wirtualnymi przyłączonymi do Azure AD Domain Services, możesz użyć [usługi Azure bastionu Host](../bastion/bastion-overview.md) w obszarze Obsługiwane regiony platformy Azure.

1. Otwórz wiersz polecenia i użyj polecenia, `whoami` Aby wyświetlić nazwę wyróżniającą aktualnie uwierzytelnionego użytkownika:

    ```console
    whoami /fqdn
    ```

1. Użyj `runas` polecenia, aby uwierzytelnić się jako użytkownik z domeny lokalnej. W poniższym poleceniu Zastąp `userUpn@trusteddomain.com` wartość nazwą UPN użytkownika z zaufanej domeny lokalnej. Polecenie poprosi o hasło użytkownika:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Jeśli uwierzytelnianie zakończyło się pomyślnie, zostanie otwarty nowy wiersz polecenia. Tytuł nowego wiersza polecenia zawiera `running as userUpn@trusteddomain.com` .
1. Użyj `whoami /fqdn` w nowym wierszu polecenia, aby wyświetlić nazwę wyróżniającą uwierzytelnionego użytkownika z lokalnej Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Dostęp do zasobów w lesie zasobów AD DS platformy Azure przy użyciu lokalnego użytkownika

Korzystając z maszyny wirtualnej systemu Windows Server dołączonej do lasu zasobów domeny zarządzanej, można przetestować scenariusz, w którym użytkownicy mogą uzyskiwać dostęp do zasobów hostowanych w lesie zasobów podczas uwierzytelniania z komputerów w domenie lokalnej z użytkownikami z domeny lokalnej. W poniższych przykładach pokazano, jak tworzyć i testować różne typowe scenariusze.

#### <a name="enable-file-and-printer-sharing"></a>Włącz udostępnianie plików i drukarek

1. Nawiąż połączenie z maszyną wirtualną z systemem Windows Server przyłączoną do lasu zasobów domeny zarządzanej przy użyciu Pulpit zdalny i poświadczeń administratora domeny zarządzanej. Jeśli wystąpi błąd Uwierzytelnianie na poziomie sieci (NLA), sprawdź, czy używane konto użytkownika nie jest kontem użytkownika domeny.

    > [!TIP]
    > Aby bezpiecznie połączyć się z maszynami wirtualnymi przyłączonymi do Azure AD Domain Services, możesz użyć [usługi Azure bastionu Host](../bastion/bastion-overview.md) w obszarze Obsługiwane regiony platformy Azure.

1. Otwórz okno **Ustawienia systemu Windows** , a następnie wyszukaj i wybierz pozycję **Centrum sieci i udostępniania** .
1. Wybierz opcję **Zmień zaawansowane ustawienia udostępniania** .
1. W obszarze **Profil domeny** wybierz pozycję **Włącz udostępnianie plików i drukarek** , a następnie **Zapisz zmiany** .
1. Zamknij **Centrum sieci i udostępniania** .

#### <a name="create-a-security-group-and-add-members"></a>Tworzenie grupy zabezpieczeń i Dodawanie członków

1. Otwórz narzędzie **Użytkownicy i komputery usługi Active Directory** .
1. Kliknij prawym przyciskiem myszy nazwę domeny, wybierz pozycję **Nowy** , a następnie wybierz pozycję **jednostka organizacyjna** .
1. W polu Nazwa wpisz *LocalObjects* , a następnie wybierz przycisk **OK** .
1. Wybierz i kliknij prawym przyciskiem myszy pozycję **LocalObjects** w okienku nawigacji. Wybierz opcję **Nowy** , a następnie pozycję **Grupuj** .
1. W polu **Nazwa grupy** wpisz *FileServerAccess* . W obszarze **zakres grupy** wybierz pozycję **domena lokalna** , a następnie wybierz przycisk **OK** .
1. W okienku zawartości kliknij dwukrotnie pozycję **FileServerAccess** . Wybierz pozycję **Członkowie** , wybierz opcję **Dodaj** , a następnie wybierz pozycję **lokalizacje** .
1. Wybierz Active Directory lokalnego z widoku **Lokalizacja** , a następnie wybierz przycisk **OK** .
1. Wpisz *Użytkownicy domeny* w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz pozycję **Sprawdź nazwy** , podaj poświadczenia dla Active Directory lokalnego, a następnie wybierz **przycisk OK** .

    > [!NOTE]
    > Musisz podać poświadczenia, ponieważ relacja zaufania ma tylko jeden sposób. Oznacza to, że użytkownicy z domeny zarządzanej nie mogą uzyskać dostępu do zasobów ani wyszukiwać użytkowników lub grup w domenie zaufanej (lokalnej).

1. Grupa **Użytkownicy domeny** z lokalnego Active Directory powinna być członkiem grupy **FileServerAccess** . Wybierz **przycisk OK** , aby zapisać grupę i zamknąć okno.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Tworzenie udziału plików na potrzeby dostępu między lasami

1. Na maszynie wirtualnej z systemem Windows Server przyłączonym do lasu zasobów domeny zarządzanej Utwórz folder i podaj nazwę, taką jak *CrossForestShare* .
1. Kliknij prawym przyciskiem myszy folder i wybierz polecenie **Właściwości** .
1. Wybierz kartę **zabezpieczenia** , a następnie wybierz pozycję **Edytuj** .
1. W oknie dialogowym *uprawnienia dla CrossForestShare* wybierz pozycję **Dodaj** .
1. Wpisz *FileServerAccess* w polu **Wprowadź nazwy obiektów do wybrania** , a następnie wybierz przycisk **OK** .
1. Wybierz pozycję *FileServerAccess* z listy **nazwy grup lub użytkowników** . Na liście **uprawnienia dla FileServerAccess** wybierz opcję *Zezwalaj* na uprawnienia **Modyfikacja** i **zapis** , a następnie wybierz przycisk **OK** .
1. Wybierz kartę **udostępnianie** , a następnie wybierz pozycję **Udostępnianie zaawansowane...**
1. Wybierz opcję **Udostępnij ten folder** , a następnie wprowadź nazwę zapamiętania udziału plików w polu **Nazwa udziału** , np. *CrossForestShare* .
1. Wybierz pozycję **uprawnienia** . Na liście **uprawnienia dla wszystkich użytkowników** wybierz opcję **Zezwalaj** na uprawnienie **zmiana** .
1. Wybierz przycisk **OK** dwa razy, a następnie **Zamknij** .

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Weryfikowanie uwierzytelniania między lasami w ramach zasobu

1. Zaloguj się na komputerze z systemem Windows przyłączonym do lokalnego Active Directory przy użyciu konta użytkownika z Active Directory lokalnego.
1. Korzystając z **Eksploratora Windows** , Połącz się z udziałem utworzonym przy użyciu w pełni kwalifikowanej nazwy hosta i udziału, takiego jak `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Aby sprawdzić poprawność uprawnień do zapisu, zaznacz w folderze prawym przyciskiem myszy, wybierz polecenie **Nowy** , a następnie wybierz pozycję **dokument tekstowy** . Użyj domyślnej nazwy **nowego dokumentu tekstowego** .

    Jeśli uprawnienia do zapisu są ustawione prawidłowo, zostanie utworzony nowy dokument tekstowy. Poniższe kroki będą otwierać, edytować i usuwać plik stosownie do potrzeb.
1. Aby sprawdzić uprawnienia do odczytu, Otwórz **Nowy dokument tekstowy** .
1. Aby zweryfikować uprawnienia modyfikacja, Dodaj tekst do pliku i Zamknij **Notatnik** . Po wyświetleniu monitu o zapisanie zmian wybierz pozycję **Zapisz** .
1. Aby sprawdzić poprawność uprawnienia do usuwania, kliknij prawym przyciskiem myszy pozycję **Nowy dokument tekstowy** i wybierz polecenie **Usuń** . Wybierz opcję **tak** , aby potwierdzić usunięcie pliku.

## <a name="update-or-remove-outbound-forest-trust"></a>Aktualizowanie lub usuwanie zaufania lasu wychodzącego

Jeśli trzeba zaktualizować istniejący jednokierunkowy Las wychodzący z domeny zarządzanej, można użyć `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` skryptów i. Skrypty te pomagają w scenariuszach, w których chcesz zaktualizować przyjazną nazwę zaufania lasu lub hasło zaufania. Aby usunąć jednokierunkowe zaufanie wychodzące z domeny zarządzanej, można użyć `Remove-AaddsResourceForestTrust` skryptu. Należy ręcznie usunąć jednokierunkowe zaufanie lasu przychodzącego w skojarzonym lesie lokalnym AD DS.

### <a name="update-a-forest-trust"></a>Aktualizowanie zaufania lasu

W przypadku normalnego działania lesie domeny zarządzanej i lasu lokalnego negocjują zwykły proces aktualizacji hasła między sobą. Jest to część standardowego procesu zabezpieczeń relacji zaufania AD DS. Nie musisz ręcznie obrócić hasła zaufania, chyba że wystąpi problem z relacją zaufania i chcesz ręcznie zresetować do znanego hasła. Aby uzyskać więcej informacji, zobacz [zmiany hasła zaufanego obiektu domeny](concepts-forest-trust.md#tdo-password-changes).

W poniższych przykładowych krokach przedstawiono sposób aktualizowania istniejącej relacji zaufania, jeśli konieczne jest ręczne zresetowanie hasła zaufania wychodzącego:

1. Zainstaluj `Get-AaddsResourceForestTrusts` skrypty i `Set-AaddsResourceForestTrust` z [Galeria programu PowerShell][powershell-gallery] przy użyciu polecenia cmdlet [Install-Script][Install-Script] :

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Aby można było zaktualizować istniejące zaufanie, należy najpierw pobrać zasób zaufania przy użyciu `Get-AaddsResourceForestTrusts` skryptu. W poniższym przykładzie istniejące zaufanie jest przypisane do obiektu o nazwie *existingTrust* . Określ nazwę własnego lasu domeny zarządzanej i nazwę lasu lokalnego do zaktualizowania:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Aby zaktualizować istniejące hasło zaufania, użyj `Set-AaddsResourceForestTrust` skryptu. Określ istniejący obiekt zaufania z poprzedniego kroku, a następnie nowe hasło relacji zaufania. W programie PowerShell nie jest wymuszana złożoność hasła, dlatego należy się upewnić, że można generować i używać bezpiecznego hasła dla danego środowiska.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Usuwanie zaufania lasu

Jeśli nie potrzebujesz już jednokierunkowego zaufania lasu wychodzącego z domeny zarządzanej do lokalnego lasu AD DS, możesz go usunąć. Przed usunięciem zaufania upewnij się, że żadne aplikacje ani usługi nie muszą uwierzytelniać się w lokalnym lesie AD DS. Należy ręcznie usunąć jednokierunkowe zaufanie przychodzące w lesie lokalnym AD DS.

1. Zainstaluj `Remove-AaddsResourceForestTrust` skrypt z [Galeria programu PowerShell][powershell-gallery] przy użyciu polecenia cmdlet [Install-Script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Teraz Usuń zaufanie lasu przy użyciu `Remove-AaddsResourceForestTrust` skryptu. W poniższym przykładzie relacja zaufania o nazwie *myAzureADDSTrust* między lasem domeny zarządzanej o nazwie *aaddscontoso.com* i lokalnym lasem *OnPrem.contoso.com* jest usuwana. Określ nazwę własnego lasu domeny zarządzanej i nazwę lasu lokalnego do usunięcia:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Aby usunąć jednokierunkowe zaufanie przychodzące z lasu lokalnego AD DS, Połącz się z komputerem zarządzania z dostępem do lokalnego lasu AD DS i wykonaj następujące czynności:

1. Wybierz pozycję **Uruchom | Narzędzia administracyjne | Active Directory domen i relacji zaufania**
1. Wybierz pozycję domena, na przykład *OnPrem.contoso.com* , a następnie wybierz pozycję **Właściwości** .
1. Wybierz kartę **relacje zaufania** , a następnie wybierz istniejące zaufanie przychodzące z lasu domeny zarządzanej.
1. Wybierz pozycję **Usuń** , a następnie potwierdź, że chcesz usunąć zaufanie przychodzące.

## <a name="next-steps"></a>Następne kroki

W tym artykule zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie lasu zasobów domeny zarządzanej przy użyciu Azure PowerShell
> * Utwórz jednokierunkowe zaufanie lasu wychodzącego w domenie zarządzanej przy użyciu Azure PowerShell
> * Skonfiguruj system DNS w lokalnym środowisku AD DS, aby umożliwić obsługę łączności z domeną zarządzaną
> * Utwórz jednokierunkowe zaufanie lasu przychodzącego w środowisku lokalnym AD DS
> * Testowanie i weryfikowanie relacji zaufania na potrzeby uwierzytelniania i dostępu do zasobów

Aby uzyskać więcej informacji koncepcyjnych o typach lasów w usłudze Azure AD DS, zobacz [co to są lasy zasobów?][concepts-forest] czy [relacje zaufania lasów działają w usłudze Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/