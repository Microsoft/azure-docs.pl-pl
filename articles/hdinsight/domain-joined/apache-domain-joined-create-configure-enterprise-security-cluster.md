---
title: Tworzenie, konfigurowanie klastrów pakiet Enterprise Security — platforma Azure
description: Informacje na temat tworzenia i konfigurowania klastrów pakiet Enterprise Security w usłudze Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 07bd92e17b827dfeede5958587cecbdc97694329
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003926"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight

Pakiet Enterprise Security (ESP) dla usługi Azure HDInsight zapewnia dostęp do uwierzytelniania opartego na Active Directoryach, pomocy technicznej dla wieloużytkownika i kontroli dostępu opartej na rolach dla klastrów Apache Hadoop na platformie Azure. Klastry usługi HDInsight ESP umożliwiają organizacjom, które stosują ścisłe zasady zabezpieczeń firmowych w celu bezpiecznego przetwarzania poufnych danych.

W tym przewodniku pokazano, jak utworzyć klaster usługi Azure HDInsight z włączoną obsługą ESP. Przedstawiono w nim również, jak utworzyć maszynę wirtualną z systemem Windows IaaS, na Active Directory której jest włączony system nazw domen (DNS). Ten przewodnik umożliwia skonfigurowanie zasobów niezbędnych do zalogowania się lokalnych użytkowników do klastra usługi HDInsight z włączoną funkcją ESP.

Tworzony serwer działa jako zamiennik dla *rzeczywistego* środowiska lokalnego. Zostanie ona użyta na potrzeby instalacji i konfiguracji. W dalszej części tego procesu będziesz powtarzać kroki w Twoim środowisku.

Ten przewodnik pomoże również utworzyć hybrydowe środowisko tożsamości przy użyciu funkcji synchronizacji skrótów haseł z Azure Active Directory (Azure AD). Przewodnik uzupełnia [użycie ESP w usłudze HDInsight](apache-domain-joined-architecture.md).

Przed rozpoczęciem korzystania z tego procesu w Twoim środowisku:

* Skonfiguruj Active Directory i system DNS.
* Włącz usługę Azure AD.
* Synchronizowanie lokalnych kont użytkowników z usługą Azure AD.

![Diagram architektury usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Tworzenie środowiska lokalnego

W tej sekcji zostanie użyty szablon wdrożenia szybkiego startu platformy Azure w celu utworzenia nowych maszyn wirtualnych, skonfigurowania systemu DNS i dodania nowego lasu Active Directory.

1. Przejdź do szablonu wdrażania szybkiego startu, aby [utworzyć maszynę wirtualną platformy Azure z nowym lasem Active Directory](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Wybierz pozycję **Wdróż na platformie Azure**.
1. Zaloguj się do subskrypcji platformy Azure.
1. Na stronie **Tworzenie maszyny wirtualnej platformy Azure z nowym lasem usługi AD** podaj następujące informacje:

    |Właściwość | Wartość |
    |---|---|
    |Subskrypcja|Wybierz subskrypcję, w której chcesz wdrożyć zasoby.|
    |Grupa zasobów|Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę `OnPremADVRG`|
    |Lokalizacja|Wybierz lokalizację.|
    |Nazwa użytkownika administratora|`HDIFabrikamAdmin`|
    |Hasło administratora|Wprowadź hasło.|
    |Nazwa domeny|`HDIFabrikam.com`|
    |Prefiks DNS|`hdifabrikam`|

    Pozostaw pozostałe wartości domyślne.

    ![Szablon dotyczący tworzenia maszyny wirtualnej platformy Azure z nowym lasem usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Zapoznaj się z **warunkami i postanowieniami, a** następnie wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia.
1. Wybierz pozycję **Kup** i monitoruj wdrożenie i poczekaj na jego zakończenie. Wdrożenie zajmie około 30 minut.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurowanie użytkowników i grup na potrzeby dostępu do klastra

W tej sekcji utworzysz użytkowników, którzy będą mieli dostęp do klastra usługi HDInsight na końcu tego przewodnika.

1. Połącz się z kontrolerem domeny za pomocą Pulpit zdalny.
    1. W Azure Portal przejdź do **grup zasobów**  >  **OnPremADVRG**  >  **adVM**  >  **Connect**.
    1. Z listy rozwijanej **adres IP** wybierz publiczny adres IP.
    1. Wybierz pozycję **Pobierz plik RDP**, a następnie otwórz plik.
    1. Użyj `HDIFabrikam\HDIFabrikamAdmin` jako nazwy użytkownika.
    1. Wprowadź hasło wybrane dla konta administratora.
    1. Wybierz pozycję **OK**.

1. Na pulpicie nawigacyjnym **Menedżer serwera** kontroler domeny Przejdź do **menu Narzędzia**  >  **Active Directory Użytkownicy i komputery**.

    ![Na pulpicie nawigacyjnym Menedżer serwera Otwórz Active Directory Zarządzanie](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Utwórz dwóch nowych użytkowników: **HDIAdmin** i **HDIUser**. Ci dwaj użytkownicy będą logować się do klastrów usługi HDInsight.

    1. Na stronie **Active Directory Użytkownicy i komputery** kliknij prawym przyciskiem myszy `HDIFabrikam.com` , a następnie przejdź do **nowego**  >  **użytkownika**.

        ![Utwórz nowego użytkownika Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Na stronie **nowy obiekt — użytkownik** wprowadź `HDIUser` **imię** i **nazwisko oraz nazwę logowania użytkownika**. Pozostałe pola zostaną automatycznie wypełnione. Następnie wybierz przycisk **Dalej**.

        ![Tworzenie pierwszego obiektu użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. W wyświetlonym oknie podręcznym wprowadź hasło dla nowego konta. Wybierz pozycję **hasło nigdy nie wygasa**, a następnie kliknij przycisk **OK** w wyświetlonym komunikacie.
    1. Wybierz pozycję **dalej**, a następnie kliknij przycisk **Zakończ** , aby utworzyć nowe konto.
    1. Powtórz powyższe kroki, aby utworzyć użytkownika `HDIAdmin` .

        ![Tworzenie drugiego obiektu użytkownika administracyjnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Utwórz globalną grupę zabezpieczeń.

    1. W **Active Directory Użytkownicy i komputery**, kliknij prawym przyciskiem myszy `HDIFabrikam.com` , a następnie przejdź do **nowej**  >  **grupy**.

    1. Wprowadź `HDIUserGroup` wartość w polu tekstowym **Nazwa grupy** .

    1. Wybierz pozycję **OK**.

    ![Utwórz nową grupę Active Directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Utwórz nowy obiekt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Dodaj członków do **HDIUserGroup**.

    1. Kliknij prawym przyciskiem myszy pozycję **HDIUser** i wybierz pozycję **Dodaj do grupy..**..
    1. W polu tekstowym **Wprowadź nazwy obiektów do wybrania** wpisz `HDIUserGroup` . Następnie wybierz przycisk **OK**, a następnie ponownie **przycisk OK** w oknie podręcznym.
    1. Powtórz poprzednie kroki dla konta **HDIAdmin** .

        ![Dodaj element członkowski HDIUser do grupy HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Środowisko Active Directory zostało utworzone. Dodano dwóch użytkowników i grupę użytkowników, którzy mogą uzyskiwać dostęp do klastra usługi HDInsight.

Użytkownicy będą synchronizowani z usługą Azure AD.

### <a name="create-an-azure-ad-directory"></a>Tworzenie katalogu usługi Azure AD

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz pozycję **Utwórz zasób** i wpisz `directory` . Wybierz pozycję **Azure Active Directory**  >  **Utwórz**.
1. W obszarze **Nazwa organizacji** wpisz `HDIFabrikam` .
1. W polu **początkowa nazwa domeny** wprowadź `HDIFabrikamoutlook` .
1. Wybierz pozycję **Utwórz**.

    ![Tworzenie katalogu usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Utwórz domenę niestandardową

1. Z nowego **Azure Active Directory** w obszarze **Zarządzaj** wybierz pozycję **niestandardowe nazwy domen**.
1. Wybierz pozycję **+ Dodaj domenę niestandardową**.
1. W obszarze **nazwa domeny niestandardowej** wpisz `HDIFabrikam.com` , a następnie wybierz pozycję **Dodaj domenę**.
1. Następnie Zakończ [Dodawanie informacji DNS do rejestratora domen](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Utwórz domenę niestandardową](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Tworzenie grupy

1. Z nowego **Azure Active Directory** w obszarze **Zarządzaj** wybierz pozycję **grupy**.
1. Wybierz pozycję **+ Nowa grupa**.
1. W polu tekstowym **Nazwa grupy** wprowadź `AAD DC Administrators` .
1. Wybierz pozycję **Utwórz**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurowanie dzierżawy usługi Azure AD

Teraz skonfigurujesz dzierżawę usługi Azure AD, aby można było synchronizować użytkowników i grupy z wystąpienia Active Directory lokalnego z chmurą.

Utwórz Active Directory administratora dzierżawy.

1. Zaloguj się do Azure Portal i wybierz dzierżawę usługi Azure AD, **HDIFabrikam**.

1. Przejdź do obszarze **Zarządzanie**  >  **użytkownikami**  >  **nowy użytkownik**.

1. Wprowadź następujące szczegóły dotyczące nowego użytkownika:

    **Tożsamość**

    |Właściwość |Opis |
    |---|---|
    |Nazwa użytkownika|Wprowadź `fabrikamazureadmin` wartość w polu tekstowym. Z listy rozwijanej Nazwa domeny wybierz pozycję `hdifabrikam.com`|
    |Nazwa| Wprowadź `fabrikamazureadmin`.|

    **Hasło**
    1. Wybierz pozycję **pozwól mi utworzyć hasło**.
    1. Wprowadź bezpieczne hasło.

    **Grupy i role**
    1. Wybierz opcję **0 wybranych grup**.
    1. Wybierz pozycję **administratorzy kontrolera domeny usługi AAD**, a następnie **Wybierz pozycję**.

    ![Okno dialogowe grupy usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Wybierz pozycję **użytkownik**.
    1. Wybierz pozycję **administrator globalny**, a następnie **Wybierz pozycję**.

    ![Okno dialogowe Rola usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Wybierz pozycję **Utwórz**.

1. Następnie nowy użytkownik może zalogować się do Azure Portal, w którym zostanie wyświetlony monit o zmianę hasła. Należy to zrobić przed skonfigurowaniem Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronizuj użytkowników lokalnych z usługą Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurowanie Microsoft Azure Active Directory Connect

1. Na kontrolerze domeny Pobierz [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Otwórz pobrany plik wykonywalny i zaakceptuj postanowienia licencyjne. Wybierz opcję **Kontynuuj**.

1. Wybierz pozycję **Użyj ustawień ekspresowych**.

1. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Użyj nazwy użytkownika `fabrikamazureadmin@hdifabrikam.com` utworzonej podczas konfigurowania dzierżawy Active Directory. Następnie wybierz przycisk **Dalej**.

    ![Strona "łączenie z platformą Azure A D".](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Na stronie **Połącz z Active Directory Domain Services** wprowadź nazwę użytkownika i hasło dla konta administratora przedsiębiorstwa. Użyj nazwy użytkownika `HDIFabrikam\HDIFabrikamAdmin` i utworzonego wcześniej hasła. Następnie wybierz przycisk **Dalej**.

   ![Strona "łączenie z D S".](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Na stronie **Konfiguracja logowania usługi Azure AD** wybierz pozycję **dalej**.
   ![Strona "Konfiguracja logowania za pomocą usługi Azure AD"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Na stronie **gotowy do skonfigurowania** wybierz pozycję **Zainstaluj**.

   ![Strona "gotowe do konfiguracji"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Na stronie **Konfiguracja ukończona** wybierz pozycję **Zakończ**.
   ![Strona "Konfiguracja ukończona"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Po zakończeniu synchronizacji upewnij się, że użytkownicy utworzeni w katalogu IaaS są zsynchronizowani z usługą Azure AD.
   1. Zaloguj się w witrynie Azure Portal.
   1. Wybierz **Azure Active Directory**  >  **HDIFabrikam**  >  **użytkowników** HDIFabrikam.

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Tworzenie tożsamości zarządzanej przypisanej przez użytkownika, której można użyć do skonfigurowania Azure AD Domain Services (AD DS platformy Azure). Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz pozycję **Utwórz zasób** i wpisz `managed identity` . Wybierz pozycję **Zarządzanie tożsamościami zarządzanymi przez użytkownika**  >  **Create**.
1. Wprowadź wartość w polu **nazwa zasobu** `HDIFabrikamManagedIdentity` .
1. Wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź `HDIFabrikam-CentralUS` .
1. W obszarze **Lokalizacja** wybierz pozycję **środkowe stany USA**.
1. Wybierz pozycję **Utwórz**.

![Tworzenie nowej tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Włączanie usług Azure AD DS

Wykonaj następujące kroki, aby włączyć usługę Azure AD DS. Aby uzyskać więcej informacji, zobacz [Włączanie usługi Azure AD DS przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

1. Utwórz sieć wirtualną, aby hostować AD DS platformy Azure. Uruchom następujący kod programu PowerShell.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz pozycję **Utwórz zasób**, wprowadź `Domain services` i wybierz pozycję **Azure AD Domain Services**  >  **Utwórz**.
1. Na stronie **podstawowe** :
    1. W obszarze **Nazwa katalogu** wybierz utworzony katalog usługi Azure AD: **HDIFabrikam**.
    1. W obszarze **nazwa domeny DNS** wprowadź *HDIFabrikam.com*.
    1. Wybierz subskrypcję.
    1. Określ grupę zasobów **HDIFabrikam-środkowe**. W obszarze **Lokalizacja** wybierz pozycję **środkowe stany USA**.

        ![Podstawowe informacje dotyczące usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Na stronie **Sieć** wybierz sieć (**HDIFabrikam-VNET**) i podsieć (**AADDS-Subnet**), która została utworzona przy użyciu skryptu programu PowerShell. Lub wybierz pozycję **Utwórz nową** , aby teraz utworzyć sieć wirtualną.

    ![Krok "Utwórz sieć wirtualną"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Na stronie **Grupa administratorów** powinna zostać wyświetlona powiadomienie z informacją o tym, że grupa o nazwie **Administratorzy domeny usługi AAD** została już utworzona w celu administrowania tą grupą. Możesz zmodyfikować członkostwo w tej grupie, jeśli chcesz, ale w tym przypadku nie musisz go zmieniać. Wybierz pozycję **OK**.

    ![Wyświetlanie grupy administratorów usługi Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Na stronie **Synchronizacja** Włącz pełną synchronizację, wybierając pozycję **wszystkie**  >  **OK**.

    ![Włączanie synchronizacji usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Na stronie **Podsumowanie** sprawdź szczegóły AD DS platformy Azure i wybierz **przycisk OK**.

    ![Podsumowanie "Włącz Azure AD Domain Services"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Po włączeniu usługi Azure AD DS lokalny serwer DNS jest uruchamiany na maszynach wirtualnych usługi Azure AD.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Skonfiguruj sieć wirtualną AD DS platformy Azure

Wykonaj następujące kroki, aby skonfigurować sieć wirtualną usługi Azure AD DS (**HDIFabrikam-AADDSVNET**) do korzystania z niestandardowych serwerów DNS.

1. Zlokalizuj adresy IP niestandardowych serwerów DNS.
    1. Wybierz `HDIFabrikam.com` zasób usługi Azure AD DS.
    1. W obszarze **Zarządzaj** wybierz pozycję **Właściwości**.
    1. Znajdź adresy IP w obszarze **adres IP w sieci wirtualnej**.

    ![Lokalizowanie niestandardowych adresów IP DNS dla usługi Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Skonfiguruj **HDIFabrikam-AADDSVNET** , aby używać niestandardowych adresów IP 10.0.0.4 i 10.0.0.5.

    1. W obszarze **Ustawienia** wybierz pozycję **serwery DNS**.
    1. wybierz pozycję **Niestandardowe**.
    1. W polu tekstowym wprowadź pierwszy adres IP (*10.0.0.4*).
    1. Wybierz pozycję **Zapisz**.
    1. Powtórz kroki, aby dodać inny adres IP (*10.0.0.5*).

W naszym scenariuszu skonfigurujemy AD DS platformy Azure do używania adresów IP 10.0.0.4 i 10.0.0.5, ustawiając ten sam adres IP w sieci wirtualnej Azure AD DS:

![Strona niestandardowe serwery DNS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Zabezpieczanie ruchu LDAP

Protokół LDAP (Lightweight Directory Access Protocol) służy do odczytu i zapisu w Azure Active Directory. Istnieje możliwość poufności i bezpiecznego ruchu LDAP przy użyciu technologii SSL (SSL) lub Transport Layer Security (TLS). Protokół LDAP można włączyć za pośrednictwem protokołu SSL (LDAPs) przez zainstalowanie prawidłowo sformatowanego certyfikatu.

Aby uzyskać więcej informacji na temat bezpiecznego protokołu LDAP, zobacz [Konfigurowanie LDAPS dla domeny zarządzanej AD DS platformy Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md).

W tej sekcji utworzysz certyfikat z podpisem własnym, pobierzesz certyfikat i skonfigurujesz LDAPs dla domeny zarządzanej usługi **HDIFabrikam** Azure AD DS.

Poniższy skrypt tworzy certyfikat dla **HDIFabrikam**. Certyfikat jest zapisywany w ścieżce *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> \#Do utworzenia żądania certyfikatu TLS/SSL można użyć dowolnego narzędzia lub aplikacji, która tworzy prawidłowe żądanie standardu kryptografii klucza publicznego (PKCS) 10.

Sprawdź, czy certyfikat został zainstalowany w **osobistym** magazynie komputera:

1. Uruchom program Microsoft Management Console (MMC).
1. Dodaj przystawkę **Certyfikaty** , która zarządza certyfikatami na komputerze lokalnym.
1. Rozwiń węzeł **Certyfikaty (Komputer lokalny)**  > **Osobiste** > **Certyfikaty**. W magazynie **osobistym** powinien istnieć nowy certyfikat. Ten certyfikat jest wystawiony dla w pełni kwalifikowanej nazwy hosta.

    ![Weryfikowanie tworzenia certyfikatu lokalnego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. W okienku po prawej stronie kliknij prawym przyciskiem myszy utworzony certyfikat. Wskaż **wszystkie zadania**, a następnie wybierz pozycję **Eksportuj**.

1. Na stronie **Eksportowanie klucza prywatnego** wybierz opcję **tak, eksportuj klucz prywatny**. Komputer, na którym zostanie zaimportowany klucz, musi mieć klucz prywatny odczytywanie zaszyfrowanych wiadomości.

    ![Strona eksportowanie klucza prywatnego Kreatora eksportu certyfikatów](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Na stronie **Format pliku eksportu** Pozostaw ustawienia domyślne, a następnie wybierz przycisk **dalej**.
1. Na stronie **hasło** wpisz hasło klucza prywatnego. W obszarze **szyfrowanie** wybierz pozycję **TripleDES-SHA1**. Następnie wybierz przycisk **Dalej**.
1. Na stronie **Eksportuj plik** wpisz ścieżkę i nazwę wyeksportowanego pliku certyfikatu, a następnie wybierz przycisk **dalej**. Nazwa pliku musi mieć rozszerzenie PFX. Ten plik jest skonfigurowany w Azure Portal w celu nawiązania bezpiecznego połączenia.
1. Włącz LDAPs dla domeny zarządzanej AD DS platformy Azure.
    1. Na Azure Portal wybierz domenę `HDIFabrikam.com` .
    1. W obszarze **Zarządzaj** wybierz pozycję **Secure LDAP**.
    1. Na stronie **Secure LDAP** w obszarze **Secure LDAP** wybierz pozycję **Włącz**.
    1. Wyszukaj plik certyfikatu PFX wyeksportowany na komputerze.
    1. Wprowadź hasło certyfikatu.

    ![Włącz bezpieczny protokół LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Po włączeniu protokołów LDAP Upewnij się, że jest dostępna przez włączenie portu 636.
    1. W grupie zasobów **HDIFabrikam-środkowe** wybierz grupę zabezpieczeń sieci **AADDS-HDIFabrikam.com-sieciowej grupy zabezpieczeń**.
    1. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego**  >  **Dodaj**.
    1. Na stronie **Dodawanie reguły zabezpieczeń ruchu przychodzącego** wprowadź następujące właściwości, a następnie wybierz pozycję **Dodaj**:

        | Właściwość | Wartość |
        |---|---|
        | Źródło | Dowolne |
        | Zakresy portów źródłowych | * |
        | Miejsce docelowe | Dowolne |
        | Zakres portów docelowych | 636 |
        | Protokół | Dowolny |
        | Akcja | Zezwalaj |
        | Priorytet | \<Desired number> |
        | Nazwa | Port_LDAP_636 |

    ![Okno dialogowe Dodawanie reguły zabezpieczeń ruchu przychodzącego](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** to zarządzana tożsamość przypisana przez użytkownika. Rola współautor usług domenowych w usłudze HDInsight jest włączona dla tożsamości zarządzanej, która będzie zezwalać tej tożsamości na odczytywanie, tworzenie, modyfikowanie i usuwanie usług domenowych.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight z obsługą ESP

Ten krok wymaga następujących wymagań wstępnych:

1. Utwórz nową grupę zasobów *HDIFabrikam-zachodni* w lokalizacji **zachodnie stany USA**.
1. Utwórz sieć wirtualną, która będzie hostować klaster usługi HDInsight z obsługą ESP.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Utwórz relację równorzędną między siecią wirtualną, która hostuje platformę Azure AD DS ( `HDIFabrikam-AADDSVNET` ) i sieć wirtualną, która będzie hostować klaster usługi HDInsight z włączoną funkcją ESP ( `HDIFabrikam-HDIVNet` ). Użyj poniższego kodu programu PowerShell do komunikacji równorzędnej z dwiema sieciami wirtualnymi.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Utwórz nowe konto Azure Data Lake Storage Gen2 o nazwie **Hdigen2store**. Skonfiguruj konto przy użyciu tożsamości zarządzanej przez użytkownika **HDIFabrikamManagedIdentity**. Aby uzyskać więcej informacji, zobacz [używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Skonfiguruj niestandardowy serwer DNS w sieci wirtualnej **HDIFabrikam-AADDSVNET** .
    1. Przejdź do Azure Portal > **grup zasobów**  >  **OnPremADVRG**  >  **HDIFabrikam AADDSVNET**  >  **serwerów DNS**.
    1. Wybierz pozycję **niestandardowe** i wprowadź *10.0.0.4* i *10.0.0.5*.
    1. Wybierz pozycję **Zapisz**.

        ![Zapisz niestandardowe ustawienia DNS dla sieci wirtualnej](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Utwórz nowy klaster usługi HDInsight Spark z włączoną obsługą ESP.
    1. Wybierz pozycję **niestandardowy (rozmiar, ustawienia, aplikacje)**.
    1. Wprowadź szczegóły dotyczące **podstaw** (sekcja 1). Upewnij się, że **Typ klastra** to **Spark 2,3 (HDI 3,6)**. Upewnij się, że **Grupa zasobów** to **HDIFabrikam-środkowe**.

    1. W przypadku **zabezpieczeń i sieci** (sekcja 2) wprowadź następujące informacje:
        * W obszarze **pakiet Enterprise Security** wybierz pozycję **włączone**.
        * Wybierz pozycję **administrator klastra** i wybierz konto **HDIAdmin** utworzone jako administrator lokalny. Kliknij pozycję **Wybierz**.
        * Wybierz pozycję **Grupa dostępu do klastra**  >  **HDIUserGroup**. Każdy użytkownik dodany do tej grupy w przyszłości będzie mógł uzyskać dostęp do klastrów usługi HDInsight.

            ![Wybierz grupę dostępu do klastra HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Wykonaj pozostałe kroki konfiguracji klastra i sprawdź szczegóły dotyczące **podsumowania klastra**. Wybierz pozycję **Utwórz**.

1. Zaloguj się do interfejsu użytkownika Ambari dla nowo utworzonego klastra pod adresem `https://CLUSTERNAME.azurehdinsight.net` . Użyj nazwy użytkownika `hdiadmin@hdifabrikam.com` i hasła administratora.

    ![Okno logowania interfejsu użytkownika Apache Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Na pulpicie nawigacyjnym klastra wybierz pozycję **role**.
1. Na stronie **role** , w obszarze **Przypisz role do tych**, obok roli **administrator klastra** wprowadź grupę *hdiusergroup*. 

    ![Przypisywanie roli administratora klastra do hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Otwórz klienta Secure Shell (SSH) i zaloguj się w klastrze. Użyj **hdiuser** , który został utworzony w lokalnym wystąpieniu Active Directory.

    ![Logowanie do klastra przy użyciu klienta SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Jeśli możesz zalogować się przy użyciu tego konta, klaster ESP został prawidłowo skonfigurowany do synchronizacji z lokalnym wystąpieniem Active Directory.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [wprowadzeniem do Apache Hadoop zabezpieczenia przy użyciu protokołu ESP](hdinsight-security-overview.md).