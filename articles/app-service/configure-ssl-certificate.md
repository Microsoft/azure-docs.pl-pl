---
title: Dodawanie certyfikatów TLS/SSL i zarządzanie nimi
description: Utwórz bezpłatny certyfikat, zaimportuj certyfikat usługi App Service, zaimportuj certyfikat usługi Key Vault lub kup certyfikat usługi App Service w usłudze Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 9f9fcc0b3d8dfe19961668e77da91bc9f14ff2d1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453901"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Dodawanie certyfikatu TLS/SSL w usłudze Azure App Service

[Usługa Azure App Service](overview.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu. W tym artykule pokazano, jak utworzyć, przekazać lub zaimportować certyfikat prywatny lub certyfikat publiczny do usługi App Service. 

Po dodaniu certyfikatu do aplikacji lub [aplikacji usługi](https://docs.microsoft.com/azure/azure-functions/)App Service można [zabezpieczyć niestandardową nazwę DNS](configure-ssl-bindings.md) lub użyć jej w [kodzie aplikacji.](configure-ssl-certificate-in-code.md)

W poniższej tabeli wymieniono opcje dodawania certyfikatów w usłudze App Service:

|Opcja|Opis|
|-|-|
| Tworzenie bezpłatnego certyfikatu zarządzanego usługi App Service (wersja zapoznawcza) | Prywatny certyfikat, który jest łatwy w użyciu, `www` jeśli wystarczy zabezpieczyć [domenę niestandardową](app-service-web-tutorial-custom-domain.md) lub dowolną domenę nienagącą w usłudze App Service. |
| Zakup certyfikatu usługi app service | Certyfikat prywatny zarządzany przez platformę Azure. Łączy w sobie prostotę zautomatyzowanego zarządzania certyfikatami oraz elastyczność opcji odnawiania i eksportowania. |
| Importowanie certyfikatu z magazynu kluczy | Przydatne w przypadku korzystania z [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) do zarządzania [certyfikatami PKCS12](https://wikipedia.org/wiki/PKCS_12). Zobacz [Wymagania dotyczące certyfikatów prywatnych](#private-certificate-requirements). |
| Przekazywanie certyfikatu prywatnego | Jeśli masz już certyfikat prywatny od zewnętrznego dostawcy, możesz go przekazać. Zobacz [Wymagania dotyczące certyfikatów prywatnych](#private-certificate-requirements). |
| Przekazywanie certyfikatu publicznego | Certyfikaty publiczne nie są używane do zabezpieczania domen niestandardowych, ale można załadować je do kodu, jeśli potrzebujesz ich, aby uzyskać dostęp do zasobów zdalnych. |

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem:

- [Tworzenie aplikacji usługi App Service](/azure/app-service/).
- Tylko wolny certyfikat: mapuj poddomenę (na `www.contoso.com`przykład) do usługi App Service z [rekordem CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Wymagania dotyczące certyfikatów prywatnych

> [!NOTE]
> Usługa Azure Web Apps **nie** obsługuje usługi AES256, a wszystkie pliki pfx powinny być szyfrowane za pomocą tripledes.

[Bezpłatny certyfikat zarządzany usługi App Service](#create-a-free-certificate-preview) lub certyfikat usługi app [service](#import-an-app-service-certificate) spełniają już wymagania usługi App Service. Jeśli zdecydujesz się przekazać lub zaimportować certyfikat prywatny do usługi App Service, certyfikat musi spełniać następujące wymagania:

* Eksportowany jako [chroniony hasłem plik PFX](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

Aby zabezpieczyć domenę niestandardową w powiązaniu TLS, certyfikat ma dodatkowe wymagania:

* Zawiera [rozszerzone użycie klucza](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) do uwierzytelniania serwera (OID = 1.3.6.1.5.5.7.3.1)
* Podpisany przez zaufany urząd certyfikacji

> [!NOTE]
> **Certyfikaty kryptografii opartej na krzywej eliptycznej (ECC, Elliptic Curve Cryptography)** mogą współpracować z usługą App Service, ale nie są uwzględnione w tym artykule. Skontaktuj się ze swoim urzędem certyfikacji, aby uzyskać informacje o dokładnych krokach, które należy wykonać w celu utworzenia certyfikatów ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Tworzenie bezpłatnego certyfikatu (wersja zapoznawcza)

Bezpłatny certyfikat zarządzany usługi App Service to pod klucz rozwiązanie do zabezpieczania niestandardowej nazwy DNS w usłudze App Service. Jest to w pełni funkcjonalny certyfikat TLS/SSL, który jest zarządzany przez usługę App Service i odnawiany automatycznie. Bezpłatny certyfikat ma następujące ograniczenia:

- Nie obsługuje certyfikatów wieloznacznych.
- Nie obsługuje domen nagich.
- Nie można eksportować.
- Nie obsługuje rekordów DNS A.

> [!NOTE]
> Bezpłatny certyfikat jest wydawany przez DigiCert. W przypadku niektórych domen najwyższego poziomu należy jawnie zezwolić firmie DigiCert jako wystawcy `0 issue digicert.com`certyfikatów na utworzenie rekordu domeny [CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) o wartości: .
> 

Aby utworzyć bezpłatny certyfikat zarządzany usługi App Service:

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji wybierz pozycję **Ustawienia TLS/SSL** > **Certyfikaty klucza prywatnego (pfx)** > **Utwórz certyfikat zarządzany usługi App Service**.

![Tworzenie bezpłatnego certyfikatu w usłudze app service](./media/configure-ssl-certificate/create-free-cert.png)

W oknie dialogowym zostanie wyświetlona każda nienaga domena, która jest prawidłowo mapowana do aplikacji z rekordem CNAME. Wybierz domenę niestandardową, dla aby utworzyć bezpłatny certyfikat, i wybierz pozycję **Utwórz**. Dla każdej obsługiwanej domeny niestandardowej można utworzyć tylko jeden certyfikat.

Po zakończeniu operacji zostanie wyświetlony certyfikat na liście **Certyfikaty klucza prywatnego.**

![Tworzenie bezpłatnego certyfikatu zakończonego](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w [10.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-an-app-service-certificate"></a>Importowanie certyfikatu usługi aplikacji

W przypadku zakupu certyfikatu usługi app service z platformy Azure platforma Azure zarządza następującymi zadaniami:

- Zajmuje się procesem zakupu od firmy GoDaddy.
- Przeprowadza weryfikację domeny certyfikatu.
- Zachowuje certyfikat w [usłudze Azure Key Vault](../key-vault/general/overview.md).
- Zarządza odnawianiem certyfikatów (patrz [Odnawianie certyfikatu).](#renew-certificate)
- Automatycznie synchronizuj certyfikat z zaimportowanymi kopiami w aplikacjach usługi App Service.

Aby kupić certyfikat usługi App Service, przejdź do [ekranu Uruchom zamówienie certyfikatu](#start-certificate-order).

Jeśli masz już działający certyfikat usługi App Service, możesz:

- [Zaimportuj certyfikat do usługi App Service](#import-certificate-into-app-service).
- [Zarządzanie certyfikatem](#manage-app-service-certificates), na przykład odnawianie, ponowne kluczowanie i eksportowanie go.

### <a name="start-certificate-order"></a>Rozpocznij zamówienie certyfikatu

Uruchom zamówienie certyfikatu usługi App Service na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">stronie Tworzenie certyfikatu usługi app service</a>.

![Uruchamianie zakupu certyfikatu usługi App Service](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Poniższa tabela ułatwia konfigurowanie certyfikatu. Po zakończeniu kliknij pozycję **Gotowe**.

| Ustawienie | Opis |
|-|-|
| Nazwa | Przyjazna nazwa certyfikatu usługi App Service. |
| Nazwa hosta naked domain | Określ tutaj domenę główną. Wystawiony certyfikat zabezpiecza zarówno domenę główną, *jak* i poddomenę. `www` W wystawionym certyfikacie pole Nazwa pospolita zawiera `www` domenę główną, a pole Nazwa alternatywna podmiotu zawiera domenę. Aby zabezpieczyć tylko dowolną poddomenę, określ tutaj w pełni kwalifikowaną nazwę domeny poddomeny (na przykład `mysubdomain.contoso.com`).|
| Subskrypcja | Subskrypcja, która będzie zawierać certyfikat. |
| Grupa zasobów | Grupa zasobów, która będzie zawierać certyfikat. Można użyć nowej grupy zasobów lub wybrać tę samą grupę zasobów co aplikacja usługi App Service, na przykład. |
| Jednostka SKU certyfikatu | Określa typ certyfikatu do utworzenia, niezależnie od tego, czy jest to certyfikat standardowy, czy [symbol wieloznaczny](https://wikipedia.org/wiki/Wildcard_certificate). |
| Warunki prawne | Kliknij, aby potwierdzić, że zgadzasz się z warunkami prawnymi. Certyfikaty są uzyskiwane z firmy GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Przechowywanie w usłudze Azure Key Vault

Po zakończeniu procesu zakupu certyfikatu, przed rozpoczęciem korzystania z tego certyfikatu, należy wykonać kilka czynności, które należy wykonać. 

Wybierz certyfikat na stronie [Certyfikaty usługi app service,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a następnie kliknij**krok 1**konfiguracji >  **certyfikatu:** Magazyn .

![Konfigurowanie przechowywania magazynu usługi App Service w magazynie magazynu aplikacji](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) to usługa platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Jest to magazyn z wyboru dla certyfikatów usługi App Service.

Na stronie **Stan przechowalni kluczy** kliknij pozycję **Repozytorium magazynu kluczy,** aby utworzyć nowy magazyn lub wybrać istniejący przechowalnia. Jeśli zdecydujesz się utworzyć nowy magazyn, użyj poniższej tabeli, aby skonfigurować przechowalnię i kliknij przycisk Utwórz. Utwórz nową przechowalnię kluczy w tej samej grupie subskrypcji i zasobów, co aplikacja usługi App Service.

| Ustawienie | Opis |
|-|-|
| Nazwa | Unikatowa nazwa, która składa się dla znaków alfanumeryczne i kresek. |
| Grupa zasobów | Jako zalecenie wybierz tę samą grupę zasobów co certyfikat usługi App Service. |
| Lokalizacja | Wybierz tę samą lokalizację co aplikacja usługi App Service. |
| Warstwa cenowa | Aby uzyskać więcej informacji, zobacz [Szczegóły cen usługi Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zasady dostępu| Definiuje aplikacje i dozwolony dostęp do zasobów magazynu. Można go skonfigurować później, wykonując czynności opisane w [udziel kilku aplikacjom dostępu do magazynu kluczy](../key-vault/general/group-permissions-for-apps.md). |
| Dostęp do sieci wirtualnej | Ogranicz dostęp do magazynu do niektórych sieci wirtualnych platformy Azure. Można go skonfigurować później, wykonując czynności opisane w [temacie Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych](../key-vault/general/network-security.md) |

Po wybraniu przechowalni zamknij stronę **Repozytorium magazynu kluczy.** Opcja **Krok 1: Sklep** powinien wyświetlać zielony znacznik wyboru sukcesu. Zachowaj otwartą stronę w następnym kroku.

### <a name="verify-domain-ownership"></a>Weryfikowanie własności domeny

Na tej samej stronie **konfiguracji certyfikatu,** która została użyta w ostatnim kroku, kliknij **krok 2: Sprawdź**.

![Weryfikowanie certyfikatu domeny usługi App Service](./media/configure-ssl-certificate/verify-domain.png)

Wybierz **opcję Weryfikacja usługi aplikacji**. Ponieważ domena została już zamapowana do aplikacji internetowej (zobacz [Wymagania wstępne),](#prerequisites)jest już zweryfikowana. Po prostu kliknij **przycisk Sprawdź,** aby zakończyć ten krok. Kliknij przycisk **Odśwież,** aż pojawi się komunikat **Certyfikat zweryfikowany w domenie.**

> [!NOTE]
> Obsługiwane są cztery typy metod weryfikacji domeny: 
> 
> - **Usługa aplikacji** — najwygodniejsza opcja, gdy domena jest już mapowana do aplikacji usługi App Service w tej samej subskrypcji. Korzysta z faktu, że aplikacja usługi App Service już zweryfikowała własność domeny.
> - **Domena** — weryfikuj [domenę usługi App Service zakupioną na platformie Azure](manage-custom-dns-buy-domain.md). Platforma Azure automatycznie dodaje rekord TXT weryfikacji dla Ciebie i kończy proces.
> - **Poczta** — weryfikuj domenę, wysyłając wiadomość e-mail do administratora domeny. Instrukcje są dostarczane po wybraniu opcji.
> - **Ręcznie** — sprawdź domenę przy użyciu strony HTML (tylko certyfikat**standardowy)** lub rekordu DNS TXT. Instrukcje są dostarczane po wybraniu opcji.

### <a name="import-certificate-into-app-service"></a>Importowanie certyfikatu do usługi App Service

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji wybierz pozycję **Ustawienia TLS/SSL** > **Certyfikaty klucza prywatnego (.pfx)** > Import App Service**Certificate**.

![Importowanie certyfikatu usługi App Service w usłudze aplikacji](./media/configure-ssl-certificate/import-app-service-cert.png)

Wybierz właśnie zakupiony certyfikat i wybierz **przycisk OK**.

Po zakończeniu operacji zostanie wyświetlony certyfikat na liście **Certyfikaty klucza prywatnego.**

![Certyfikat importu usługi App Service został ukończony](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w [10.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-a-certificate-from-key-vault"></a>Importowanie certyfikatu z magazynu kluczy

Jeśli używasz usługi Azure Key Vault do zarządzania certyfikatami, możesz zaimportować certyfikat PKCS12 z usługi Key Vault do usługi App Service, o ile [spełnia on wymagania.](#private-certificate-requirements)

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji wybierz pozycję **Ustawienia TLS/SSL** > **Certyfikaty klucza prywatnego (pfx)** > **Importuj certyfikat przechowalni kluczy**.

![Importowanie certyfikatu magazynu kluczy w usłudze aplikacji](./media/configure-ssl-certificate/import-key-vault-cert.png)

Poniższa tabela ułatwia wybór certyfikatu.

| Ustawienie | Opis |
|-|-|
| Subskrypcja | Subskrypcja, do której należy Usługa Key Vault. |
| Usługa Key Vault | Przechowalnia z certyfikatem, który chcesz zaimportować. |
| Certyfikat | Wybierz z listy certyfikatów PKCS12 w przechowalni. Wszystkie certyfikaty PKCS12 w przechowalni są wyświetlane z odciskami palców, ale nie wszystkie są obsługiwane w usłudze App Service. |

Po zakończeniu operacji zostanie wyświetlony certyfikat na liście **Certyfikaty klucza prywatnego.** Jeśli importowanie nie powiedzie się z powodu błędu, certyfikat nie spełnia [wymagań usługi App Service](#private-certificate-requirements).

![Certyfikat Importuj przechowalnię kluczy został ukończony](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w [10.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-private-certificate"></a>Przekazywanie certyfikatu prywatnego

Po uzyskaniu certyfikatu od dostawcy certyfikatów wykonaj kroki opisane w tej sekcji, aby przygotować go do usługi App Service.

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

Wyeksportuj scalony certyfikat TLS/SSL za pomocą klucza prywatnego, za pomocą których wygenerowano żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _ &lt;>pliku klucza prywatnego_ i _ &lt;>scalonym plikiem certyfikatu_ ścieżkami do klucza prywatnego i scalonego pliku certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po wyświetleniu monitu określ hasło eksportu. Użyjesz tego hasła podczas przekazywania certyfikatu TLS/SSL do usługi App Service później.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Przekazywanie certyfikatu do usługi app service

Teraz możesz przesłać certyfikat do usługi App Service.

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji wybierz pozycję **Ustawienia TLS/SSL** > **Certyfikaty klucza prywatnego (pfx)** > **Przekaż certyfikat**.

![Przekazywanie certyfikatu prywatnego w usłudze app service](./media/configure-ssl-certificate/upload-private-cert.png)

W pozycji **Plik PFX certyfikatu** wybierz swój plik PFX. W polu **Hasło certyfikatu** wpisz hasło, które zostało utworzone podczas eksportowania pliku PFX. Po zakończeniu kliknij przycisk **Przekaż**. 

Po zakończeniu operacji zostanie wyświetlony certyfikat na liście **Certyfikaty klucza prywatnego.**

![Zakończono przekazywanie certyfikatu](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową za pomocą tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w [10.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-public-certificate"></a>Przekazywanie certyfikatu publicznego

Certyfikaty publiczne są obsługiwane w formacie *.cer.* 

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Po lewej stronie nawigacji aplikacji kliknij pozycję >  **Ustawienia TLS/SSL****Certyfikaty publiczne (cer)** > **Przekaż certyfikat klucza publicznego**.

W **pliku Name**wpisz nazwę certyfikatu. W **pliku certyfikatu CER**wybierz plik CER.

Kliknij pozycję **Przekaż**.

![Przekazywanie certyfikatu publicznego w usłudze app service](./media/configure-ssl-certificate/upload-public-cert.png)

Po przekazaniu certyfikatu skopiuj odcisk palca certyfikatu i zobacz [Udostępnij certyfikat](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Zarządzanie certyfikatami usługi app service

W tej sekcji pokazano, jak zarządzać certyfikatem usługi App Service zakupionym w [zaimportuj certyfikat usługi App Service](#import-an-app-service-certificate).

- [Certyfikat ponownego klucza](#rekey-certificate)
- [Odnawianie certyfikatu](#renew-certificate)
- [Eksportowanie certyfikatu](#export-certificate)
- [Usuwanie certyfikatu](#delete-certificate)

### <a name="rekey-certificate"></a>Certyfikat ponownego klucza

Jeśli uważasz, że klucz prywatny certyfikatu został naruszony, możesz ponownie wywrzeć certyfikat. Wybierz certyfikat na stronie [Certyfikaty usługi app service,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a następnie wybierz pozycję **Ponownieklizowany i Zsynchronizowany** z lewej strony nawigacji.

Kliknij **przycisk Ponownie,** aby rozpocząć proces. Ten proces może potrwać 1-10 minut.

![Ponowne klucze certyfikatu usługi app service](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Ponowne tworzenie klucza certyfikatu jest rzutowe certyfikatem z nowym certyfikatem wystawionym przez urząd certyfikacji.

Po zakończeniu operacji ponownego klucza kliknij przycisk **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta certyfikatu w usłudze App Service bez powodowania przestojów w aplikacjach.

> [!NOTE]
> Jeśli nie klikniesz **przycisku Synchronizuj,** usługa App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

### <a name="renew-certificate"></a>Odnawianie certyfikatu

Aby włączyć automatyczne odnawianie certyfikatu w dowolnym momencie, wybierz certyfikat na stronie [Certyfikaty usługi app service,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a następnie kliknij pozycję **Automatyczne odnawianie ustawień** w lewej części nawigacji. Domyślnie certyfikaty usługi app service mają roczny okres ważności.

Wybierz **pozycję Włączone** i kliknij pozycję **Zapisz**. Certyfikaty mogą rozpocząć automatyczne odnawianie 60 dni przed wygaśnięciem, jeśli masz włączone automatyczne odnawianie.

![Automatyczne odnawianie certyfikatu usługi App Service](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij przycisk **Ręczne odnawianie**. Możesz poprosić o ręczne odnowienie certyfikatu na 60 dni przed wygaśnięciem.

Po zakończeniu operacji odnawiania kliknij przycisk **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta certyfikatu w usłudze App Service bez powodowania przestojów w aplikacjach.

> [!NOTE]
> Jeśli nie klikniesz **przycisku Synchronizuj,** usługa App Service automatycznie zsynchronizuje certyfikat w ciągu 48 godzin.

### <a name="export-certificate"></a>Eksportowanie certyfikatu

Ponieważ certyfikat usługi app service jest [kluczem tajnym magazynu kluczy,](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)można wyeksportować jego kopię PFX i używać jej do innych usług platformy Azure lub poza platformą Azure.

Aby wyeksportować certyfikat usługi App Service jako plik PFX, uruchom następujące polecenia w [aplikacji Cloud Shell](https://shell.azure.com). Można również uruchomić go lokalnie, jeśli [zainstalowano platformę Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Zastąp symbole zastępcze nazwami używanymi podczas [tworzenia certyfikatu usługi App Service](#start-certificate-order).

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

Pobrany plik *appservicecertificate.pfx* jest nieprzetworzonym plikiem PKCS12, który zawiera certyfikaty publiczne i prywatne. W każdym wierszu polecenia użyj pustego ciągu dla hasła importu i frazy przebiegu PEM.

### <a name="delete-certificate"></a>Usuwanie certyfikatu 

Usunięcie certyfikatu usługi App Service jest ostateczne i nieodwracalne. Usunięcie zasobu certyfikatu usługi app service powoduje odwołanie certyfikatu. Wszelkie powiązania w usłudze App Service z tym certyfikatem stają się nieważne. Aby zapobiec przypadkowemu usunięciu, platforma Azure blokuje certyfikat. Aby usunąć certyfikat usługi App Service, należy najpierw usunąć blokadę usuwania certyfikatu.

Wybierz certyfikat na stronie [Certyfikaty usługi app service,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a następnie wybierz pozycję **Blokady** w lewej nawigacji.

Znajdź blokadę na certyfikacie z typem blokady **Usuń**. Po prawej stronie wybierz pozycję **Usuń**.

![Usuń blokadę certyfikatu usługi App Service](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Teraz możesz usunąć certyfikat usługi App Service. W lewej nawigacji wybierz pozycję **Przegląd** > **Usuń**. W oknie dialogowym potwierdzenia wpisz nazwę certyfikatu i wybierz **przycisk OK**.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Zabezpiecz niestandardową nazwę DNS za pomocą powiązania TLS/SSL w usłudze Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service](configure-ssl-certificate-in-code.md)
* [Często zadawane pytania: Certyfikaty usługi aplikacji](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
