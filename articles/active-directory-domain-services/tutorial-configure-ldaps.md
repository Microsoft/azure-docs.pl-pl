---
title: Samouczek — Konfigurowanie LDAPs dla Azure Active Directory Domain Services | Microsoft Docs
description: W ramach tego samouczka nauczysz się konfigurować protokół Secure Lightweight Directory Access Protocol (LDAPs) dla domeny zarządzanej Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d5dbb7b71e2d67ed5b3f624c93c3c143d6c98e5d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618539"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Samouczek: Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej Azure Active Directory Domain Services

Aby komunikować się z domeną zarządzaną Azure Active Directory Domain Services (Azure AD DS), używany jest protokół LDAP (Lightweight Directory Access Protocol). Domyślnie ruch związany z protokołem LDAP nie jest szyfrowany, co stanowi problem z zabezpieczeniami w wielu środowiskach.

Za pomocą usługi Azure AD DS można skonfigurować domenę zarządzaną w taki sposób, aby korzystała z protokołu LDAPs (Secure Lightweight Directory Access Protocol). W przypadku korzystania z bezpiecznego protokołu LDAP ruch jest szyfrowany. Secure LDAP jest również znana jako LDAP over SSL (SSL) lub Transport Layer Security (TLS).

W tym samouczku pokazano, jak skonfigurować LDAPs dla domeny zarządzanej AD DS platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu cyfrowego do użycia z platformą Azure AD DS
> * Włącz bezpieczny protokół LDAP dla AD DS platformy Azure
> * Konfigurowanie bezpiecznego protokołu LDAP do użytku w publicznej sieci Internet
> * Wiązanie i testowanie bezpiecznego protokołu LDAP dla domeny zarządzanej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][create-azure-ad-ds-instance].
* Narzędzie *LDP.exe* zainstalowane na komputerze.
    * W razie potrzeby [zainstaluj narzędzia administracji zdalnej serwera (RSAT)][rsat] dla *Active Directory Domain Services i LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Ten samouczek umożliwia skonfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Utwórz certyfikat dla bezpiecznego protokołu LDAP

Aby można było użyć bezpiecznego protokołu LDAP, certyfikat cyfrowy jest używany do szyfrowania komunikacji. Ten certyfikat cyfrowy jest stosowany do domeny zarządzanej i umożliwia narzędziom, takim jak *LDP.exe* , korzystanie z bezpiecznej szyfrowanej komunikacji podczas wykonywania zapytań dotyczących danych. Istnieją dwa sposoby utworzenia certyfikatu bezpiecznego dostępu do domeny zarządzanej za pomocą protokołu LDAP:

* Certyfikat z publicznego urzędu certyfikacji lub urzędu certyfikacji przedsiębiorstwa.
    * Jeśli organizacja pobiera certyfikaty z publicznego urzędu certyfikacji, uzyskaj bezpieczny certyfikat LDAP z tego publicznego urzędu certyfikacji. Jeśli używasz urzędu certyfikacji przedsiębiorstwa w organizacji, uzyskaj bezpieczny certyfikat LDAP od urzędu certyfikacji przedsiębiorstwa.
    * Publiczny urząd certyfikacji działa tylko w przypadku używania niestandardowej nazwy DNS z domeną zarządzaną. Jeśli nazwa domeny usługi DNS została zakończona w *. onmicrosoft.com*, nie można utworzyć certyfikatu cyfrowego w celu zabezpieczenia połączenia z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *. onmicrosoft.com* , więc publiczny urząd certyfikacji nie wystawia certyfikatu. W tym scenariuszu należy utworzyć certyfikat z podpisem własnym i użyć go do skonfigurowania bezpiecznego protokołu LDAP.
* Utworzony samodzielnie certyfikat z podpisem własnym.
    * Ta metoda jest przydatna do celów testowych i zawiera informacje o tym samouczku.

Certyfikat, którego żądanie lub utworzenie, musi spełniać poniższe wymagania. W przypadku włączenia bezpiecznego protokołu LDAP z nieprawidłowym certyfikatem w domenie zarządzanej występują problemy:

* **Zaufany wystawca** — certyfikat musi być wystawiony przez Urząd zaufany przez komputery łączące się z domeną zarządzaną przy użyciu protokołu Secure LDAP. Ten Urząd może być publicznym urzędem certyfikacji lub urzędem certyfikacji przedsiębiorstwa zaufanym przez te komputery.
* **Okres istnienia** — certyfikat musi być ważny przez co najmniej 3-6 miesięcy. Secure LDAP dostęp do domeny zarządzanej zostanie zakłócony po wygaśnięciu certyfikatu.
* **Nazwa podmiotu** — nazwa podmiotu w certyfikacie musi być domeną zarządzaną. Na przykład jeśli domena ma nazwę *aaddscontoso.com*, nazwa podmiotu certyfikatu musi być **. aaddscontoso.com*.
    * Nazwa DNS lub alternatywna nazwa podmiotu certyfikatu musi być certyfikatem z symbolem wieloznacznym, aby zapewnić prawidłowe działanie bezpiecznego protokołu LDAP z Azure AD Domain Services. Kontrolery domeny używają losowo nazw i można je usunąć lub dodać, aby zapewnić, że usługa pozostanie dostępna.
* **Użycie klucza** — certyfikat musi być skonfigurowany pod kątem *podpisów cyfrowych* i *szyfrowania kluczy*.
* **Cel certyfikatu** — certyfikat musi być prawidłowy na potrzeby uwierzytelniania serwera TLS.

Dostępnych jest kilka narzędzi do tworzenia certyfikatów z podpisem własnym, takich jak OpenSSL, narzędzie, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet itp.

W tym samouczku utworzymy certyfikat z podpisem własnym dla bezpiecznego protokołu LDAP przy użyciu polecenia cmdlet [New-SelfSignedCertificate][New-SelfSignedCertificate] .

Otwórz okno programu PowerShell jako **administrator** i uruchom następujące polecenia. Zastąp zmienną *$dnsname* nazwą DNS używaną przez własną domenę zarządzaną, taką jak *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Następujące przykładowe dane wyjściowe pokazują, że certyfikat został pomyślnie wygenerowany i jest przechowywany w lokalnym magazynie certyfikatów (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Zrozumienie i eksportowanie wymaganych certyfikatów

Aby użyć bezpiecznego protokołu LDAP, ruch sieciowy jest szyfrowany przy użyciu infrastruktury kluczy publicznych (PKI).

* Klucz **prywatny** jest stosowany do domeny zarządzanej.
    * Ten klucz prywatny jest używany do *odszyfrowywania* bezpiecznego ruchu LDAP. Klucz prywatny powinien być stosowany tylko do domeny zarządzanej i nie jest rozpowszechniany na komputerach klienckich.
    * Certyfikat zawierający klucz prywatny używa *.* Format pliku PFX.
    * Algorytm szyfrowania dla certyfikatu musi być *TripleDES-SHA1*.
* Na komputerach klienckich jest stosowany klucz **publiczny** .
    * Ten klucz publiczny jest używany do *szyfrowania* bezpiecznego ruchu LDAP. Klucz publiczny może być dystrybuowany do komputerów klienckich.
    * Certyfikaty bez klucza prywatnego korzystają z programu *.* Format pliku CER.

Te dwa klucze, *prywatne* i *publiczne* klucze, należy upewnić się, że tylko odpowiednie komputery mogą pomyślnie komunikować się ze sobą. Jeśli używasz publicznego urzędu certyfikacji lub urzędu certyfikacji przedsiębiorstwa, zostanie wystawiony certyfikat zawierający klucz prywatny i można go zastosować do domeny zarządzanej. Klucz publiczny powinien już być znany i zaufany przez komputery klienckie.

W tym samouczku utworzono certyfikat z podpisem własnym z kluczem prywatnym, dlatego należy wyeksportować odpowiednie składniki prywatne i publiczne.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Eksportowanie certyfikatu dla AD DS platformy Azure

Aby można było użyć certyfikatu cyfrowego utworzonego w poprzednim kroku z domeną zarządzaną, należy wyeksportować certyfikat do programu *.* Plik certyfikatu PFX, który zawiera klucz prywatny.

1. Aby otworzyć okno dialogowe *uruchamiania* , wybierz klucze **systemu Windows**  +  **R** .
1. Otwórz program Microsoft Management Console (MMC), wprowadzając **program MMC** w oknie dialogowym *uruchamiania* , a następnie wybierz przycisk **OK**.
1. W wierszu **Kontrola konta użytkownika** , a następnie wybierz pozycję **tak** , aby uruchomić program MMC jako administrator.
1. Z menu **plik** wybierz pozycję **Dodaj/Usuń przystawkę...**
1. W kreatorze **przystawek certyfikatów** wybierz pozycję **konto komputera**, a następnie wybierz przycisk **dalej**.
1. Na stronie **Wybieranie komputera** wybierz pozycję **komputer lokalny: (komputer, na którym uruchomiona jest ta konsola)**, a następnie wybierz pozycję **Zakończ**.
1. W oknie dialogowym **Dodawanie lub usuwanie przystawek** kliknij **przycisk OK** , aby dodać przystawkę Certyfikaty do programu MMC.
1. W oknie programu MMC rozwiń węzeł **katalog główny konsoli**. Wybierz pozycję **Certyfikaty (komputer lokalny)**, a następnie rozwiń węzeł **osobiste** , a następnie węzeł **Certyfikaty** .

    ![Otwórz magazyn certyfikatów osobistych w programie Microsoft Management Console.](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Zostanie wyświetlony certyfikat z podpisem własnym utworzony w poprzednim kroku, taki jak *aaddscontoso.com*. Wybierz pozycję Ten certyfikat prawym przyciskiem myszy, a następnie wybierz pozycję **wszystkie zadania > Eksportuj...**

    ![Eksportowanie certyfikatu w programie Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. W **Kreatorze eksportu certyfikatów** wybierz pozycję **dalej**.
1. Należy wyeksportować klucz prywatny dla certyfikatu. Jeśli klucz prywatny nie jest uwzględniony w wyeksportowanym certyfikacie, Akcja włączenia bezpiecznego protokołu LDAP dla domeny zarządzanej nie powiedzie się.

    Na stronie **Eksportowanie klucza prywatnego** wybierz opcję **tak, eksportuj klucz prywatny**, a następnie wybierz przycisk **dalej**.
1. Domeny zarządzane obsługują tylko *.* Format pliku certyfikatu PFX, który zawiera klucz prywatny. Nie Eksportuj certyfikatu jako *.* Format pliku certyfikatu CER bez klucza prywatnego.

    Na stronie **Format pliku eksportu** wybierz opcję **wymiana informacji osobistych — PKCS #12 (. PFX)** jako format pliku dla wyeksportowanego certyfikatu. Zaznacz pole wyboru *Dołącz wszystkie certyfikaty ze ścieżki certyfikacji, jeśli jest to możliwe*:

    ![Wybierz opcję Wyeksportuj certyfikat w formacie PKCS 12 (. PFX) — format pliku](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Ponieważ ten certyfikat jest używany do odszyfrowywania danych, należy dokładnie kontrolować dostęp. Hasło może służyć do ochrony korzystania z certyfikatu. Bez poprawnego hasła nie można zastosować certyfikatu do usługi.

    Na stronie **zabezpieczenia** wybierz opcję **hasła** , aby chronić *.* Plik certyfikatu PFX. Algorytm szyfrowania musi być *TripleDES-SHA1*. Wprowadź i Potwierdź hasło, a następnie wybierz pozycję **dalej**. To hasło jest używane w następnej sekcji w celu włączenia bezpiecznego protokołu LDAP dla domeny zarządzanej.
1. Na stronie **Eksport pliku** Określ nazwę pliku i lokalizację, w której chcesz wyeksportować certyfikat, taki jak *C:\Users\accountname\azure-AD-DS.pfx*. Zanotuj hasło i lokalizację *. Plik PFX* , ponieważ te informacje będą wymagane w następnych krokach.
1. Na stronie Przegląd wybierz pozycję **Zakończ** , aby wyeksportować certyfikat do programu *.* Plik certyfikatu PFX. W przypadku pomyślnego wyeksportowania certyfikatu zostanie wyświetlone okno dialogowe potwierdzenia.
1. Pozostaw otwarty program MMC do użycia w poniższej sekcji.

### <a name="export-a-certificate-for-client-computers"></a>Eksportowanie certyfikatu dla komputerów klienckich

Komputery klienckie muszą ufać wystawcy certyfikatu bezpiecznego protokołu LDAP, aby można było pomyślnie nawiązać połączenie z domeną zarządzaną przy użyciu protokołu LDAPs. Komputery klienckie muszą mieć certyfikat, aby pomyślnie szyfrować dane, które są odszyfrowywane przez usługę Azure AD DS. Jeśli używasz publicznego urzędu certyfikacji, komputer powinien automatycznie ufać tym wystawcom certyfikatów i mieć odpowiedni certyfikat.

W tym samouczku zostanie użyty certyfikat z podpisem własnym i Wygenerowano certyfikat zawierający klucz prywatny w poprzednim kroku. Teraz wyeksportuj i Zainstaluj certyfikat z podpisem własnym w zaufanym magazynie certyfikatów na komputerze klienckim:

1. Wróć do programu MMC dla *certyfikatów (komputer lokalny) > magazynu certyfikatów > osobistych* . Pokazany jest certyfikat z podpisem własnym utworzony w poprzednim kroku, taki jak *aaddscontoso.com*. Wybierz pozycję Ten certyfikat prawym przyciskiem myszy, a następnie wybierz pozycję **wszystkie zadania > Eksportuj...**
1. W **Kreatorze eksportu certyfikatów** wybierz pozycję **dalej**.
1. Ponieważ nie potrzebujesz klucza prywatnego dla klientów, na stronie **Eksportowanie klucza prywatnego** wybierz pozycję **nie Eksportuj klucza prywatnego**, a następnie wybierz przycisk **dalej**.
1. Na stronie **Format pliku eksportu** wybierz pozycję **Base-64 encoded X. 509 (. CER)** jako format pliku dla wyeksportowanego certyfikatu:

    ![Wybierz opcję Wyeksportuj certyfikat w formacie X. 509 z kodowaniem Base-64 (. CER) — format pliku](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Na stronie **Eksport pliku** Określ nazwę pliku i lokalizację, w której chcesz wyeksportować certyfikat, taki jak *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Na stronie Przegląd wybierz pozycję **Zakończ** , aby wyeksportować certyfikat do programu *.* Plik certyfikatu CER. W przypadku pomyślnego wyeksportowania certyfikatu zostanie wyświetlone okno dialogowe potwierdzenia.

*.* Plik certyfikatu CER może być teraz dystrybuowany do komputerów klienckich, które muszą ufać bezpiecznemu połączeniu LDAP z domeną zarządzaną. Zainstalujmy certyfikat na komputerze lokalnym.

1. Otwórz Eksploratora plików i przejdź do lokalizacji, w której zapisano plik *.* Plik certyfikatu cer, taki jak *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Kliknij prawym przyciskiem myszy *.* Plik certyfikatu cer, a następnie wybierz pozycję **Zainstaluj certyfikat**.
1. W **Kreatorze importu certyfikatów** wybierz opcję przechowywania certyfikatu na *komputerze lokalnym*, a następnie wybierz pozycję **dalej**:

    ![Wybierz opcję importowania certyfikatu do lokalnego magazynu maszynowego](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Po wyświetleniu monitu wybierz opcję **tak** , aby umożliwić komputerowi wprowadzanie zmian.
1. Wybierz opcję **automatycznego wybierania magazynu certyfikatów na podstawie typu certyfikatu**, a następnie wybierz przycisk **dalej**.
1. Na stronie Przegląd wybierz pozycję **Zakończ** , aby zaimportować *. Certyfikat CER* . okno dialogowe potwierdzenia pliku jest wyświetlane, gdy certyfikat został pomyślnie zaimportowany.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Włącz bezpieczny protokół LDAP dla AD DS platformy Azure

Przy użyciu certyfikatu cyfrowego utworzonego i wyeksportowanego, który zawiera klucz prywatny, a komputer kliencki ustawił zaufanie połączenia, należy teraz włączyć bezpieczny protokół LDAP w domenie zarządzanej. Aby włączyć bezpieczny protokół LDAP w domenie zarządzanej, wykonaj następujące czynności konfiguracyjne:

1. W [Azure Portal](https://portal.azure.com)wprowadź w polu **wyszukiwania zasobów** pozycję *usługi domenowe* . Wybierz **Azure AD Domain Services** z wyniku wyszukiwania.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Po lewej stronie okna AD DS platformy Azure wybierz pozycję **Secure LDAP**.
1. Domyślnie bezpieczny dostęp LDAP do domeny zarządzanej jest wyłączony. Przełącz **Secure LDAP** , aby **włączyć**.
1. Secure LDAP dostęp do domeny zarządzanej za pośrednictwem Internetu jest domyślnie wyłączony. Po włączeniu publicznego dostępu do bezpiecznego protokołu LDAP domena jest podatna na ataki w trybie bezprawnego hasła przez Internet. W następnym kroku Grupa zabezpieczeń sieci jest skonfigurowana w celu blokowania dostępu tylko do wymaganych zakresów źródłowych adresów IP.

    Przełącz **Zezwalanie na włączanie bezpiecznego dostępu do protokołu LDAP przez Internet** . **Enable**

1. Wybierz ikonę folderu obok **. Plik PFX z bezpiecznym certyfikatem LDAP**. Przejdź do ścieżki *. Plik PFX* , a następnie wybierz certyfikat utworzony w poprzednim kroku, który zawiera klucz prywatny.

    > [!IMPORTANT]
    > Zgodnie z poprzednią sekcją wymagań dotyczących certyfikatów nie można używać certyfikatu z publicznego urzędu certyfikacji z domyślną domeną *. onmicrosoft.com* . Firma Microsoft jest właścicielem domeny *. onmicrosoft.com* , więc publiczny urząd certyfikacji nie wystawia certyfikatu.
    >
    > Upewnij się, że certyfikat ma odpowiedni format. Jeśli tak nie jest, platforma Azure generuje błędy walidacji certyfikatu po włączeniu bezpiecznego protokołu LDAP.

1. Wprowadź **hasło do odszyfrowania. Plik PFX** został ustawiony w poprzednim kroku, gdy certyfikat został wyeksportowany do programu *. Plik PFX* .
1. Wybierz pozycję **Zapisz** , aby włączyć bezpieczny protokół LDAP.

    ![Włącz Secure LDAP dla domeny zarządzanej w Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Zostanie wyświetlone powiadomienie, że dla domeny zarządzanej jest konfigurowany bezpieczny protokół LDAP. Nie można modyfikować innych ustawień dla domeny zarządzanej, dopóki ta operacja nie zostanie ukończona.

Włączenie bezpiecznego protokołu LDAP dla domeny zarządzanej może potrwać kilka minut. Jeśli certyfikat bezpiecznego protokołu LDAP, który nie jest zgodny z wymaganymi kryteriami, Akcja włączenia bezpiecznego protokołu LDAP dla domeny zarządzanej nie powiedzie się.

Niektóre typowe przyczyny niepowodzenia to jeśli nazwa domeny jest nieprawidłowa, algorytm szyfrowania dla certyfikatu nie jest *TripleDES-SHA1* lub certyfikat wygaśnie wkrótce lub już wygasł. Możesz ponownie utworzyć certyfikat z prawidłowymi parametrami, a następnie włączyć bezpieczny protokół LDAP przy użyciu tego zaktualizowanego certyfikatu.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Zablokuj bezpieczny dostęp do protokołu LDAP za pośrednictwem Internetu

Po włączeniu bezpiecznego dostępu do protokołu LDAP za pośrednictwem Internetu do domeny zarządzanej powstaje zagrożenie bezpieczeństwa. Domena zarządzana jest dostępna z Internetu na porcie TCP 636. Zaleca się ograniczenie dostępu do domeny zarządzanej do określonych znanych adresów IP dla danego środowiska. Reguła grupy zabezpieczeń sieci platformy Azure może służyć do ograniczania dostępu do bezpiecznego protokołu LDAP.

Utwórz regułę zezwalającą na dostęp przychodzący do bezpiecznego protokołu LDAP za pośrednictwem portu TCP 636 z określonego zestawu adresów IP. Domyślna reguła *DenyAll* z niższym priorytetem ma zastosowanie do całego ruchu przychodzącego z Internetu, więc tylko podane adresy mogą dotrzeć do domeny zarządzanej przy użyciu protokołu Secure LDAP.

1. W Azure Portal wybierz pozycję *grupy zasobów* po lewej stronie nawigacyjnej.
1. Wybierz grupę zasobów, na przykład grupa *zasobów*, a następnie wybierz grupę zabezpieczeń sieci, na przykład *aaads-sieciowej grupy zabezpieczeń*.
1. Zostanie wyświetlona lista istniejących reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego. Po lewej stronie okien sieciowych grup zabezpieczeń wybierz pozycję **ustawienia > reguły zabezpieczeń dla ruchu przychodzącego**.
1. Wybierz pozycję **Dodaj**, a następnie utwórz regułę zezwalającą na port *TCP* *636*. Aby zwiększyć bezpieczeństwo, wybierz źródło jako *adresy IP* , a następnie określ własny prawidłowy adres IP lub zakres dla swojej organizacji.

    | Ustawienie                           | Wartość        |
    |-----------------------------------|--------------|
    | Źródło                            | Adresy IP |
    | Źródłowe adresy IP/zakresy CIDR | Prawidłowy adres IP lub zakres dla Twojego środowiska |
    | Zakresy portów źródłowych                | *            |
    | Miejsce docelowe                       | Dowolne          |
    | Zakresy portów docelowych           | 636          |
    | Protokół                          | TCP          |
    | Akcja                            | Zezwalaj        |
    | Priorytet                          | 401          |
    | Nazwa                              | AllowLDAPS   |

1. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj** , aby zapisać i zastosować regułę.

    ![Utwórz regułę sieciowej grupy zabezpieczeń, aby zabezpieczyć dostęp do protokołu LDAP za pośrednictwem Internetu](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurowanie strefy DNS na potrzeby dostępu zewnętrznego

Mając bezpieczny dostęp do protokołu LDAP włączony przez Internet, zaktualizuj strefę DNS, aby komputery klienckie mogły znaleźć tę domenę zarządzaną. *Zewnętrzny adres IP Secure LDAP* zostanie wyświetlony na karcie **Właściwości** dla domeny zarządzanej:

![Wyświetl zewnętrzny adres IP protokołu LDAP dla domeny zarządzanej w Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Skonfiguruj zewnętrzny dostawca DNS, aby utworzyć rekord hosta, taki jak *LDAPS*, aby rozwiązać ten zewnętrzny adres IP. Aby najpierw przetestować lokalnie na swojej maszynie, możesz utworzyć wpis w pliku hosts systemu Windows. Aby pomyślnie edytować plik Hosts na komputerze lokalnym, Otwórz program *Notepad* jako administrator, a następnie otwórz plik *C:\Windows\System32\drivers\etc\hosts*

Poniższy przykładowy wpis DNS, z zewnętrznym dostawcą DNS lub w lokalnym pliku hosts, rozwiązuje ruch *LDAPS.aaddscontoso.com* do zewnętrznego adresu IP *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testowanie zapytań do domeny zarządzanej

Aby nawiązać połączenie i utworzyć powiązanie z domeną zarządzaną oraz przeszukiwać przy użyciu protokołu LDAP, użyj narzędzia *LDP.exe* . To narzędzie jest zawarte w pakiecie Narzędzia administracji zdalnej serwera (RSAT). Aby uzyskać więcej informacji, zobacz [install narzędzia administracji zdalnej serwera][rsat].

1. Otwórz *LDP.exe* i Połącz się z domeną zarządzaną. Wybierz pozycję **połączenie**, a następnie wybierz pozycję **Połącz.**
1. Wprowadź nazwę domeny usługi DNS Secure LDAP, która została utworzona w poprzednim kroku, na przykład *LDAPS.aaddscontoso.com*. Aby użyć bezpiecznego protokołu LDAP, ustaw wartość **port** na *636*, a następnie zaznacz pole wyboru **protokołu SSL**.
1. Wybierz **przycisk OK** , aby połączyć się z domeną zarządzaną.

Następnie powiąż z domeną zarządzaną. Użytkownicy (i konta usług) nie mogą wykonać prostych powiązań LDAP w przypadku wyłączenia synchronizacji skrótów haseł NTLM w domenie zarządzanej. Aby uzyskać więcej informacji na temat wyłączania synchronizacji skrótów haseł NTLM, zobacz [Zabezpieczanie domeny zarządzanej][secure-domain].

1. Wybierz opcję menu **połączenie** , a następnie wybierz pozycję **bind...**.
1. Podaj poświadczenia konta użytkownika należącego do domeny zarządzanej. Wprowadź hasło konta użytkownika, a następnie wprowadź domenę, na przykład *aaddscontoso.com*.
1. W **polu Typ powiązania** wybierz opcję *powiązania z poświadczeniami*.
1. Wybierz **przycisk OK** , aby powiązać z domeną zarządzaną.

Aby wyświetlić obiekty przechowywane w domenie zarządzanej:

1. Wybierz opcję menu **Widok** , a następnie wybierz **drzewo**.
1. Pozostaw pole *BaseDN* puste, a następnie wybierz przycisk **OK**.
1. Wybierz kontener, taki jak *AADDC Users (Użytkownicy*), a następnie wybierz kontener prawym przyciskiem myszy i wybierz polecenie **Wyszukaj**.
1. Pozostaw ustawione wstępnie wypełnione pola, a następnie wybierz pozycję **Uruchom**. Wyniki zapytania są wyświetlane w oknie po prawej stronie, jak pokazano w następujących przykładowych danych wyjściowych:

    ![Wyszukaj obiekty w domenie zarządzanej przy użyciu LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Aby bezpośrednio wysyłać zapytania do określonego kontenera, w menu **drzewa widoku >** można określić **BaseDN** , taki jak *OU = AADDC users, DC = AADDSCONTOSO, DC = com* lub *OU = AADDC komputery, DC = AADDSCONTOSO, DC = com*. Aby uzyskać więcej informacji na temat formatowania i tworzenia zapytań, zobacz [podstawowe informacje dotyczące zapytań LDAP][ldap-query-basics].

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli dodano wpis DNS do lokalnego pliku hosts komputera w celu przetestowania łączności dla tego samouczka, Usuń ten wpis i Dodaj formalny rekord do strefy DNS. Aby usunąć wpis z lokalnego pliku hosts, wykonaj następujące czynności:

1. Na komputerze lokalnym Otwórz *Notatnik* jako administrator
1. Przeglądaj i Otwórz plik *C:\Windows\System32\drivers\etc\hosts*
1. Usuń wiersz dla dodanego rekordu, taki jak `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu cyfrowego do użycia z platformą Azure AD DS
> * Włącz bezpieczny protokół LDAP dla AD DS platformy Azure
> * Konfigurowanie bezpiecznego protokołu LDAP do użytku w publicznej sieci Internet
> * Wiązanie i testowanie bezpiecznego protokołu LDAP dla domeny zarządzanej

> [!div class="nextstepaction"]
> [Konfigurowanie synchronizacji skrótów haseł dla hybrydowego środowiska usługi Azure AD](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
