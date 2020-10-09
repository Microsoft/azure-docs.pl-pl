---
title: Dodawanie certyfikatów TLS/SSL i zarządzanie nimi
description: Utwórz bezpłatny certyfikat, zaimportuj certyfikat App Service, zaimportuj certyfikat Key Vault lub Kup certyfikat App Service w Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: c8ede3c4a186b4b24d56651deb8172fdcde8e5ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420884"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Dodawanie certyfikatu TSL/SSL w usłudze Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym artykule pokazano, jak utworzyć, przekazać lub zaimportować certyfikat prywatny lub certyfikat publiczny do App Service. 

Po dodaniu certyfikatu do aplikacji App Service lub [aplikacji funkcji](../azure-functions/index.yml)można [zabezpieczyć niestandardową nazwę DNS](configure-ssl-bindings.md) lub [użyć jej w kodzie aplikacji](configure-ssl-certificate-in-code.md).

Poniższa tabela zawiera listę opcji związanych z dodawaniem certyfikatów w App Service:

|Opcja|Opis|
|-|-|
| Utwórz bezpłatny certyfikat zarządzany App Service (wersja zapoznawcza) | Prywatny certyfikat, którego można łatwo użyć, jeśli musisz tylko zabezpieczyć `www` [domenę niestandardową](app-service-web-tutorial-custom-domain.md) lub dowolną domenę wykorzystaną w App Service. |
| Zakup certyfikatu App Service | Prywatny certyfikat zarządzany przez platformę Azure. Łączy ona prostotę zautomatyzowanego zarządzania certyfikatami i elastyczność opcji odnawiania i eksportowania. |
| Importuj certyfikat z Key Vault | Przydatne w przypadku używania [Azure Key Vault](../key-vault/index.yml) do zarządzania [certyfikatami PKCS12](https://wikipedia.org/wiki/PKCS_12). Zobacz [wymagania dotyczące certyfikatu prywatnego](#private-certificate-requirements). |
| Przekaż certyfikat prywatny | Jeśli masz już certyfikat prywatny od innego dostawcy, możesz go przekazać. Zobacz [wymagania dotyczące certyfikatu prywatnego](#private-certificate-requirements). |
| Przekaż certyfikat publiczny | Certyfikaty publiczne nie są używane do zabezpieczania domen niestandardowych, ale można je ładować do kodu, jeśli chcesz uzyskać dostęp do zdalnych zasobów. |

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać następujące czynności:

- [Utwórz aplikację App Service](./index.yml).
- Tylko bezpłatny certyfikat: Mapuj poddomenę (na przykład `www.contoso.com` ) na App Service przy użyciu [rekordu CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Wymagania dotyczące certyfikatu prywatnego

> [!NOTE]
> Usługa Azure Web Apps **nie obsługuje AES256** , a wszystkie pliki PFX powinny być szyfrowane za pomocą usługi TripleDES.

[Bezpłatny App Service certyfikat zarządzany](#create-a-free-certificate-preview) lub [certyfikat App Service](#import-an-app-service-certificate) już spełnia wymagania App Service. Jeśli zdecydujesz się przekazać lub zaimportować certyfikat prywatny do App Service, certyfikat musi spełniać następujące wymagania:

* Eksportowany jako [chroniony hasłem plik PFX](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

Aby zabezpieczyć domenę niestandardową w ramach powiązania TLS, certyfikat ma dodatkowe wymagania:

* Zawiera [rozszerzone użycie klucza](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) uwierzytelniania serwera (OID = 1.3.6.1.5.5.7.3.1)
* Podpisany przez zaufany urząd certyfikacji

> [!NOTE]
> **Certyfikaty kryptografii opartej na krzywej eliptycznej (ECC, Elliptic Curve Cryptography)** mogą współpracować z usługą App Service, ale nie są uwzględnione w tym artykule. Skontaktuj się ze swoim urzędem certyfikacji, aby uzyskać informacje o dokładnych krokach, które należy wykonać w celu utworzenia certyfikatów ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Utwórz bezpłatny certyfikat (wersja zapoznawcza)

Bezpłatny App Service certyfikat zarządzany to rozwiązanie, które umożliwia Zabezpieczanie niestandardowej nazwy DNS w programie App Service. Jest to w pełni funkcjonalny certyfikat TLS/SSL, który jest zarządzany przez App Service i odnawiany automatycznie. Bezpłatny certyfikat obejmuje następujące ograniczenia:

- Nie obsługuje certyfikatów symboli wieloznacznych.
- Nie obsługuje domen niedozwolonych.
- Nie jest możliwy do eksportu.
- Nie jest obsługiwane w App Service Environment (ASE)
- Nie obsługuje rekordów. Na przykład automatyczne odnawianie nie działa z rekordami.

> [!NOTE]
> Bezpłatny certyfikat jest wystawiany przez DigiCert. W przypadku niektórych domen najwyższego poziomu należy jawnie zezwolić DigiCert jako wystawcy certyfikatu przez utworzenie [rekordu domeny CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) o wartości: `0 issue digicert.com` .
> 

Aby utworzyć bezpłatny App Service certyfikat zarządzany:

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**  >  **certyfikaty kluczy prywatnych (pfx)**  >  **Utwórz App Service certyfikat zarządzany**.

![Utwórz bezpłatny certyfikat w App Service](./media/configure-ssl-certificate/create-free-cert.png)

W oknie dialogowym zostanie wyświetlona dowolna wykorzystana domena, która jest prawidłowo mapowana do aplikacji przy użyciu rekordu CNAME. Wybierz domenę niestandardową, aby utworzyć bezpłatny certyfikat, a następnie wybierz pozycję **Utwórz**. Dla każdej obsługiwanej domeny niestandardowej można utworzyć tylko jeden certyfikat.

Po zakończeniu operacji zobaczysz certyfikat na liście **Certyfikaty klucza prywatnego** .

![Ukończono tworzenie bezpłatnego certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal trzeba utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w temacie [Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importuj Certyfikat usługi App Service

Jeśli kupisz Certyfikat usługi App Service z platformy Azure, platforma Azure zarządza następującymi zadaniami:

- Należy zwrócić uwagę na proces zakupu od GoDaddy.
- Wykonuje weryfikację domeny dla certyfikatu.
- Utrzymuje certyfikat w [Azure Key Vault](../key-vault/general/overview.md).
- Zarządza odnowieniem certyfikatu (zobacz [odnów certyfikat](#renew-certificate)).
- Automatycznie Synchronizuj certyfikat z zaimportowanymi kopiami w aplikacjach App Service.

Aby kupić certyfikat App Service, przejdź do pozycji [Rozpocznij kolejność certyfikatów](#start-certificate-order).

Jeśli masz już działającą App Service certyfikat, możesz:

- [Zaimportuj certyfikat do App Service](#import-certificate-into-app-service).
- [Zarządzaj certyfikatami](#manage-app-service-certificates), takimi jak odnowienie, ponowne generowanie kluczy i eksportowanie.

### <a name="start-certificate-order"></a>Uruchom kolejność certyfikatów

Uruchom App Service kolejność certyfikatów na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stronie tworzenie certyfikat usługi App Service</a>.

![Rozpocznij App Service zakupu certyfikatu](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Skorzystaj z poniższej tabeli, aby skonfigurować certyfikat. Po zakończeniu kliknij pozycję **Gotowe**.

| Ustawienie | Opis |
|-|-|
| Nazwa | Przyjazna nazwa certyfikatu App Service. |
| Nazwa hosta z wydaną domeną | W tym miejscu określ domenę główną. Wystawiony certyfikat zabezpiecza *zarówno* domenę główną, jak i `www` poddomenę. W wystawionym certyfikacie pole Common Name (nazwa pospolita) zawiera domenę główną, a pole Alternatywna nazwa podmiotu zawiera `www` domenę. Aby zabezpieczyć tylko każdą poddomenę, określ w pełni kwalifikowaną nazwę domeny podrzędnej domeny (na przykład `mysubdomain.contoso.com` ).|
| Subskrypcja | Subskrypcja, która będzie zawierać certyfikat. |
| Grupa zasobów | Grupa zasobów, która będzie zawierać certyfikat. Możesz na przykład użyć nowej grupy zasobów lub wybrać tę samą grupę zasobów co App Service aplikacji. |
| Jednostka SKU certyfikatu | Określa typ certyfikatu do utworzenia, czy certyfikat standardowy czy [certyfikat wieloznaczny](https://wikipedia.org/wiki/Wildcard_certificate). |
| Postanowienia prawne | Kliknij, aby potwierdzić, że zgadzasz się z postanowieniami prawnymi. Certyfikaty są uzyskiwane z GoDaddy. |

> [!NOTE]
> App Service certyfikaty zakupione na platformie Azure są wystawiane przez GoDaddy. W przypadku niektórych domen najwyższego poziomu należy jawnie zezwolić GoDaddy jako wystawcy certyfikatu przez utworzenie [rekordu domeny CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) z wartością: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Przechowywanie w Azure Key Vault

Po zakończeniu procesu zakupu certyfikatu należy wykonać kilka dodatkowych kroków, aby można było rozpocząć korzystanie z tego certyfikatu. 

Na stronie [Certyfikaty App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) wybierz certyfikat, a następnie kliknij pozycję **Konfiguracja certyfikatu**  >  **krok 1: Magazyn**.

![Konfigurowanie Key Vault magazynu certyfikatu App Service](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) to usługa platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Jest to magazyn wybrany dla App Service certyfikatów.

Na stronie **stan Key Vault** kliknij pozycję **Key Vault repozytorium** , aby utworzyć nowy magazyn, lub wybierz istniejący magazyn. Jeśli zdecydujesz się utworzyć nowy magazyn, Skorzystaj z poniższej tabeli, aby skonfigurować magazyn, a następnie kliknij przycisk Utwórz. Utwórz nowy Key Vault w ramach tej samej subskrypcji i grupy zasobów co aplikacja App Service.

| Ustawienie | Opis |
|-|-|
| Nazwa | Unikatowa nazwa, która składa się z znaków alfanumerycznych i kresek. |
| Grupa zasobów | Zgodnie z zaleceniem wybierz tę samą grupę zasobów co certyfikat App Service. |
| Lokalizacja | Wybierz tę samą lokalizację, w której znajduje się aplikacja App Service. |
| Warstwa cenowa | Aby uzyskać więcej informacji, zobacz [Azure Key Vault szczegóły cennika](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zasady dostępu| Definiuje aplikacje i dozwolony dostęp do zasobów magazynu. Można ją później skonfigurować, wykonując czynności opisane w sekcji [przypisywanie zasad dostępu Key Vault](/azure/key-vault/general/assign-access-policy-portal). |
| Dostęp Virtual Network | Ogranicz dostęp do magazynu do określonych sieci wirtualnych platformy Azure. Można skonfigurować ją później, wykonując czynności opisane w sekcji [konfigurowanie Azure Key Vault zapór i sieci wirtualnych](/azure/key-vault/general/network-security) |

Po wybraniu magazynu zamknij stronę **repozytorium Key Vault** . Opcja " **krok 1: Magazyn** " powinna zawierać zielony znacznik wyboru dla sukcesu. Pozostaw otwartą stronę w następnym kroku.

### <a name="verify-domain-ownership"></a>Weryfikuj własność domeny

Na tej samej stronie **konfiguracji certyfikatu** , która została użyta w ostatnim kroku, kliknij pozycję **krok 2. Weryfikacja**.

![Weryfikuj domenę dla App Service certyfikat](./media/configure-ssl-certificate/verify-domain.png)

Wybierz **App Service weryfikację**. Ponieważ domena została już zmapowana do aplikacji sieci Web (zobacz [wymagania wstępne](#prerequisites)), została już sprawdzona. Po prostu kliknij przycisk **Weryfikuj** , aby zakończyć ten krok. Kliknij przycisk **Odśwież** , dopóki nie zostanie wyświetlona **zweryfikowana domena certyfikatu** .

> [!NOTE]
> Obsługiwane są cztery typy metod weryfikacji domeny: 
> 
> - **App Service** — najbardziej wygodna opcja, gdy domena została już zmapowana do aplikacji App Service w ramach tej samej subskrypcji. Wykorzystuje fakt, że aplikacja App Servicea już zweryfikowała własność domeny.
> - **Domena** — sprawdź [domenę App Service zakupionej na platformie Azure](manage-custom-dns-buy-domain.md). Platforma Azure automatycznie dodaje rekord TXT weryfikacji dla Ciebie i kończy proces.
> - **Poczta** — Sprawdź domenę, wysyłając wiadomość e-mail do administratora domeny. Instrukcje są dostępne po wybraniu opcji.
> - **Ręcznie** — Sprawdź domenę, używając strony HTML (tylko certyfikat**Standardowy** ) lub rekordu TXT DNS. Instrukcje są dostępne po wybraniu opcji.

### <a name="import-certificate-into-app-service"></a>Importuj certyfikat do App Service

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**  >  **Certyfikaty klucza prywatnego (pfx)**  >  **certyfikat usługi App Service**.

![Importuj certyfikat App Service w programie App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Wybierz certyfikat, który został właśnie zakupiony, i wybierz pozycję **OK**.

Po zakończeniu operacji zobaczysz certyfikat na liście **Certyfikaty klucza prywatnego** .

![Zakończono Importowanie certyfikatu App Service](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal trzeba utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w temacie [Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importuj certyfikat z Key Vault

Jeśli używasz Azure Key Vault do zarządzania certyfikatami, możesz zaimportować certyfikat PKCS12 z Key Vault do App Service o ile [spełnia wymagania](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autoryzuj App Service do odczytu z magazynu
Domyślnie dostawca zasobów App Service nie ma dostępu do Key Vault. Aby można było użyć Key Vault do wdrożenia certyfikatu, należy [autoryzować dostawcę zasobów dostępu do odczytu do magazynu](../key-vault/general/group-permissions-for-apps.md#grant-access-to-your-key-vault)kluczy. 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  jest główną nazwą usługi dostawcy zasobów dla App Service i jest taka sama dla wszystkich subskrypcji platformy Azure. W przypadku Azure Government środowiska chmury Użyj `6a02c803-dafd-4136-b4c3-5a6f318b4714` zamiast tego jako nazwy głównej usługi dostawcy zasobów.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importowanie certyfikatu z magazynu do aplikacji

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**  >  **certyfikaty kluczy prywatnych (pfx)**  >  **Importuj Key Vault certyfikat**.

![Importuj certyfikat Key Vault w programie App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Skorzystaj z poniższej tabeli, aby wybrać certyfikat.

| Ustawienie | Opis |
|-|-|
| Subskrypcja | Subskrypcja, do której należy Key Vault. |
| Usługa Key Vault | Magazyn z certyfikatem, który ma zostać zaimportowany. |
| Certyfikat | Wybierz z listy PKCS12 certyfikatów w magazynie. Wszystkie certyfikaty PKCS12 w magazynie są wyświetlane z odciskiem palca, ale nie wszystkie są obsługiwane w App Service. |

Po zakończeniu operacji zobaczysz certyfikat na liście **Certyfikaty klucza prywatnego** . Jeśli importowanie nie powiedzie się z powodu błędu, certyfikat nie spełnia [wymagań dotyczących App Service](#private-certificate-requirements).

![Zakończono Importowanie certyfikatu Key Vault](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Jeśli certyfikat zostanie zaktualizowany w Key Vault przy użyciu nowego certyfikatu, App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal trzeba utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w temacie [Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Po uzyskaniu certyfikatu od dostawcy certyfikatów wykonaj kroki opisane w tej sekcji, aby przygotować go do App Service.

### <a name="merge-intermediate-certificates"></a>Scalanie certyfikatów pośrednich

Jeśli Twój urząd certyfikacji dał Ci wiele certyfikatów w łańcuchu certyfikatów, musisz kolejno scalić certyfikaty.

Aby to zrobić, otwórz każdy otrzymany certyfikat w edytorze tekstów.

Utwórz plik scalonego certyfikatu o nazwie _mergedcertificate.crt_. W edytorze tekstów skopiuj zawartość każdego certyfikatu do tego pliku. Kolejność certyfikatów powinna być zgodna z kolejnością w łańcuchu certyfikatów, poczynając od Twojego certyfikatu i kończąc na certyfikacie głównym. Wygląda to następująco:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Eksportowanie certyfikatu do pliku PFX

Wyeksportuj scalony certyfikat TLS/SSL z kluczem prywatnym, za pomocą którego zostało wygenerowane żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _ &lt; pliku prywatnego-Key>_ i _ &lt; scalonego pliku certyfikatu>_ ze ścieżkami do klucza prywatnego i scalonym plikiem certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po wyświetleniu monitu określ hasło eksportu. To hasło będzie używane podczas przekazywania certyfikatu TLS/SSL do App Service później.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Przekaż certyfikat do App Service

Teraz możesz przekazać certyfikat do App Service.

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL**—  >  certyfikat przekazywania**certyfikatów klucza prywatnego (pfx)**  >  **Upload Certificate**.

![Przekaż certyfikat prywatny w App Service](./media/configure-ssl-certificate/upload-private-cert.png)

W pozycji **Plik PFX certyfikatu** wybierz swój plik PFX. W polu **Hasło certyfikatu** wpisz hasło, które zostało utworzone podczas eksportowania pliku PFX. Po zakończeniu kliknij przycisk **Przekaż**. 

Po zakończeniu operacji zobaczysz certyfikat na liście **Certyfikaty klucza prywatnego** .

![Zakończono przekazywanie certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal trzeba utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w temacie [Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Przekaż certyfikat publiczny

Certyfikaty publiczne są obsługiwane w formacie *CER* . 

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>z menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji kliknij pozycję **Ustawienia TLS/SSL**  >  **Certyfikaty publiczne (CER)**  >  **Przekaż certyfikat klucza publicznego**.

W polu **Nazwa**wpisz nazwę certyfikatu. W polu **plik certyfikatu CER**wybierz plik CER.

Kliknij pozycję **Przekaż**.

![Przekaż certyfikat publiczny w App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Po przekazaniu certyfikatu Skopiuj odcisk palca certyfikatu i sprawdź, czy [certyfikat jest dostępny](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Zarządzanie certyfikatami App Service

W tej sekcji przedstawiono sposób zarządzania certyfikatem App Service zakupionego w [ramach importowania certyfikatu App Service](#import-an-app-service-certificate).

- [Certyfikat ponownego tworzenia kluczy](#rekey-certificate)
- [Odnów certyfikat](#renew-certificate)
- [Eksportowanie certyfikatu](#export-certificate)
- [Usuń certyfikat](#delete-certificate)

### <a name="rekey-certificate"></a>Certyfikat ponownego tworzenia kluczy

Jeśli uważasz, że zabezpieczenia klucza prywatnego certyfikatu zostały naruszone, możesz naruszyć certyfikat. Na stronie [certyfikaty App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) wybierz certyfikat, a następnie wybierz pozycję Wymień **i zsynchronizuj** po lewej stronie nawigacyjnej.

Kliknij **pozycję** wykluczaj, aby uruchomić proces. Ten proces może potrwać 1-10 minut.

![Ponowne generowanie klucza certyfikatu App Service](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Ponowne utworzenie klucza certyfikatu zestawia certyfikat z nowym certyfikatem wystawionym przez urząd certyfikacji.

Po ukończeniu operacji ponownego tworzenia kluczy kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj**, App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

### <a name="renew-certificate"></a>Odnów certyfikat

Aby włączyć automatyczne odnawianie certyfikatu w dowolnym momencie, wybierz certyfikat na stronie [App Service certyfikaty](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) , a następnie kliknij pozycję **Ustawienia automatycznego odnawiania** w lewym okienku nawigacji. Domyślnie certyfikaty App Service mają okres ważności jeden rok.

Wybierz pozycję **włączone** , a następnie kliknij pozycję **Zapisz**. Certyfikaty mogą rozpoczynać automatyczne odnawianie 60 dni przed wygaśnięciem, jeśli włączono automatyczne odnawianie.

![Odnów certyfikat App Service automatycznie](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij pozycję **odnowienie ręczne**. Możesz poprosić o ręczne odnowienie certyfikatu o 60 dni przed wygaśnięciem.

Po zakończeniu operacji odnawiania kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania przestojów aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj**, App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

### <a name="export-certificate"></a>Eksportowanie certyfikatu

Ponieważ Certyfikat usługi App Service jest [Key Vault tajny](../key-vault/general/about-keys-secrets-certificates.md), można wyeksportować kopię pliku PFX i użyć jej do innych usług platformy Azure lub poza platformą Azure.

Aby wyeksportować Certyfikat usługi App Service jako plik PFX, uruchom następujące polecenia w [Cloud Shell](https://shell.azure.com). Można go również uruchomić lokalnie, jeśli [zainstalowano interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Zastąp symbole zastępcze nazwami użytymi podczas [tworzenia certyfikatu App Service](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Pobrany plik *appservicecertificate. pfx* to pierwotny plik PKCS12, który zawiera certyfikaty publiczne i prywatne. W każdym monicie użyj pustego ciągu dla hasła importu i frazy przekazywania PEM.

### <a name="delete-certificate"></a>Usuń certyfikat 

Usuwanie certyfikatu App Service jest ostateczne i nieodwracalne. Usuwanie Certyfikat usługi App Service zasobów powoduje odwołanie do odwołanego certyfikatu. Wszystkie powiązania w App Service z tym certyfikatem staną się nieprawidłowe. Aby zapobiec przypadkowemu usunięciu, platforma Azure umieszcza blokadę certyfikatu. Aby usunąć certyfikat App Service, należy najpierw usunąć blokadę usuwania dla certyfikatu.

Na stronie [certyfikaty App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) wybierz certyfikat, a następnie wybierz pozycję **blokady** w lewym okienku nawigacji.

Znajdź blokadę certyfikatu z typem blokady **delete**. Z prawej strony wybierz pozycję **Usuń**.

![Usuń blokadę dla certyfikatu App Service](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Teraz możesz usunąć certyfikat App Service. W lewym okienku nawigacji wybierz pozycję **Przegląd**  >  **Usuń**. W oknie dialogowym potwierdzenia wpisz nazwę certyfikatu i wybierz **przycisk OK**.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Dodatkowe zasoby

* [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Użyj certyfikatu TLS/SSL w kodzie w Azure App Service](configure-ssl-certificate-in-code.md)
* [Często zadawane pytania: certyfikaty App Service](./faq-configuration-and-management.md)
