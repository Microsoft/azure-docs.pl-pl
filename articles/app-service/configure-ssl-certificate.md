---
title: Dodawanie certyfikatów TLS/SSL i zarządzanie nimi
description: Utwórz bezpłatny certyfikat, zaimportuj App Service, zaimportuj certyfikat Key Vault lub kup certyfikat App Service w Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1e05435f364cc30b351275439a04caff47c35512
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871799"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Dodawanie certyfikatu TSL/SSL w usłudze Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną i samonachowalną usługę hostingu w Internecie. W tym artykule przedstawiono sposób tworzenia, przekazywania lub importowania certyfikatu prywatnego lub certyfikatu publicznego do App Service. 

Po dodaniu certyfikatu do aplikacji App Service [](../azure-functions/index.yml)funkcji możesz zabezpieczyć niestandardową nazwę [DNS](configure-ssl-bindings.md) za jej pomocą lub użyć jej w [kodzie aplikacji](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Certyfikat przekazany do aplikacji jest przechowywany w jednostce wdrożenia powiązanej z kombinacją grupy zasobów i regionu aplikacji (wewnętrznie nazywaną przestrzenią *internetową).* Dzięki temu certyfikat będzie dostępny dla innych aplikacji w tej samej kombinacji grup zasobów i regionu. 

W poniższej tabeli wymieniono opcje dodawania certyfikatów w App Service:

|Opcja|Opis|
|-|-|
| Tworzenie bezpłatnego certyfikatu zarządzanego App Service zarządzanego (wersja zapoznawcza) | Prywatny certyfikat, który jest bezpłatny i łatwy w użyciu, jeśli wystarczy zabezpieczyć domenę niestandardową w [App Service.](app-service-web-tutorial-custom-domain.md) |
| Zakup certyfikatu App Service klienta | Prywatny certyfikat zarządzany przez platformę Azure. Łączy ona prostotę zautomatyzowanego zarządzania certyfikatami oraz elastyczność opcji odnawiania i eksportowania. |
| Importowanie certyfikatu z Key Vault | Przydatne w przypadku [używania Azure Key Vault](../key-vault/index.yml) do zarządzania [certyfikatami PKCS12.](https://wikipedia.org/wiki/PKCS_12) Zobacz [Wymagania dotyczące certyfikatu prywatnego.](#private-certificate-requirements) |
| Przekazywanie certyfikatu prywatnego | Jeśli masz już certyfikat prywatny od dostawcy zewnętrznego, możesz go przekazać. Zobacz [Wymagania dotyczące certyfikatu prywatnego.](#private-certificate-requirements) |
| Przekazywanie certyfikatu publicznego | Certyfikaty publiczne nie są używane do zabezpieczania domen niestandardowych, ale można załadować je do kodu, jeśli są potrzebne do uzyskania dostępu do zasobów zdalnych. |

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz App Service aplikacji.](./index.yml)
- W przypadku certyfikatu prywatnego upewnij się, że spełnia on wszystkie [wymagania App Service](#private-certificate-requirements).
- **Tylko bezpłatny certyfikat:**
    - Zamapuj domenę, dla której chcesz uzyskać certyfikat, App Service. Aby uzyskać więcej informacji, [zobacz Samouczek: mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).
    - W przypadku domeny głównej (na przykład contoso.com) upewnij się, że aplikacja nie ma skonfigurowanych żadnych ograniczeń adresów [IP.](app-service-ip-restrictions.md) Zarówno tworzenie certyfikatu, jak i okresowe odnawianie domeny głównej zależy od tego, czy aplikacja jest osiągalna z Internetu.

## <a name="private-certificate-requirements"></a>Wymagania dotyczące certyfikatu prywatnego

Bezpłatny [App Service zarządzany i](#create-a-free-managed-certificate-preview) [App Service](#import-an-app-service-certificate) już spełniają wymagania App Service. Jeśli zdecydujesz się przekazać lub zaimportować certyfikat prywatny do usługi App Service, certyfikat musi spełniać następujące wymagania:

* Wyeksportowany jako chroniony [hasłem plik PFX](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)zaszyfrowany przy użyciu trzykrotnego algorytmu DES.
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

Aby zabezpieczyć domenę niestandardową w powiązaniu TLS, certyfikat ma dodatkowe wymagania:

* Zawiera rozszerzone [użycie klucza do](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) uwierzytelniania serwera (OID = 1.3.6.1.5.5.7.3.1)
* Podpisany przez zaufany urząd certyfikacji

> [!NOTE]
> **Certyfikaty kryptografii opartej na krzywej eliptycznej (ECC, Elliptic Curve Cryptography)** mogą współpracować z usługą App Service, ale nie są uwzględnione w tym artykule. Skontaktuj się ze swoim urzędem certyfikacji, aby uzyskać informacje o dokładnych krokach, które należy wykonać w celu utworzenia certyfikatów ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Tworzenie bezpłatnego certyfikatu zarządzanego (wersja zapoznawcza)

> [!NOTE]
> Przed utworzeniem bezpłatnego certyfikatu zarządzanego upewnij się, że spełnino [wymagania wstępne](#prerequisites) dotyczące aplikacji.

Bezpłatna App Service Managed Certificate to podstawowe rozwiązanie do zabezpieczania niestandardowej nazwy DNS w App Service. Jest to w pełni funkcjonalny certyfikat protokołu TLS/SSL, który jest zarządzany przez usługę App Service odnawiany automatycznie. Bezpłatny certyfikat ma następujące ograniczenia:

- Nie obsługuje certyfikatów wieloznacznych.
- Nie można eksportować.
- Nie jest obsługiwane w App Service Environment (ASE).
- Nie jest obsługiwane w przypadku domen głównych zintegrowanych z usługą Traffic Manager.

> [!NOTE]
> Bezpłatny certyfikat jest wystawiany przez firmę DigiCert. W przypadku niektórych domen najwyższego poziomu należy jawnie zezwolić firmie DigiCert na wystawcę certyfikatu, tworząc rekord domeny [CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) o wartości `0 issue digicert.com` : .
> 

W menu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym panelu nawigacyjnym aplikacji wybierz pozycję Ustawienia protokołu **TLS/SSL** Certyfikaty klucza prywatnego  >  **(pfx)** Utwórz certyfikat  >  **App Service zarządzanego.**

![Tworzenie bezpłatnego certyfikatu w App Service](./media/configure-ssl-certificate/create-free-cert.png)

Wybierz domenę niestandardową, aby utworzyć bezpłatny certyfikat dla usługi , a następnie wybierz pozycję **Utwórz**. Dla każdej obsługiwanej domeny niestandardowej można utworzyć tylko jeden certyfikat.

Po zakończeniu operacji certyfikat zostanie wyświetlony na liście **Certyfikaty klucza prywatnego.**

![Tworzenie bezpłatnego certyfikatu zakończone](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową przy użyciu tego certyfikatu, należy utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w te [tematu Create binding (Tworzenie powiązania).](configure-ssl-bindings.md#create-binding)
>

## <a name="import-an-app-service-certificate"></a>Importowanie Certyfikat usługi App Service

W przypadku zakupu Certyfikat usługi App Service platformy Azure platforma Azure zarządza następującymi zadaniami:

- Zajmuje się procesem zakupu od firmy GoDaddy.
- Przeprowadza weryfikację domeny certyfikatu.
- Utrzymuje certyfikat w [programie Azure Key Vault](../key-vault/general/overview.md).
- Zarządza odnawianiem certyfikatów (zobacz [Odnawianie certyfikatu).](#renew-certificate)
- Automatycznie zsynchronizuj certyfikat z zaimportowaną kopią w App Service aplikacji.

Aby kupić certyfikat App Service, przejdź do [startowego zamówienia certyfikatu.](#start-certificate-order)

Jeśli masz już roboczy certyfikat App Service, możesz:

- [Zaimportuj certyfikat do App Service](#import-certificate-into-app-service).
- [Zarządzanie certyfikatem](#manage-app-service-certificates), na przykład odnawianie, ponowne kluczy i eksportowanie go.
> [!NOTE]
> App Service certyfikaty nie są obecnie obsługiwane w chmurach krajowych platformy Azure.

### <a name="start-certificate-order"></a>Rozpoczynanie zamówienia certyfikatu

Rozpocznij kolejność App Service certyfikatów na stronie <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">Certyfikat usługi App Service tworzenia aplikacji.</a>

![Rozpoczynanie App Service zakupu certyfikatu](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Skorzystaj z poniższej tabeli, aby skonfigurować certyfikat. Po zakończeniu kliknij pozycję **Gotowe**.

| Ustawienie | Opis |
|-|-|
| Nazwa | Przyjazna nazwa certyfikatu App Service użytkownika. |
| Naked Nazwa hosta domeny | W tym miejscu określ domenę główną. Wystawiony certyfikat zabezpiecza zarówno *domenę* główną, jak i `www` poddomenę. W wystawionym certyfikacie pole Nazwa pospolita zawiera domenę główną, a pole Alternatywna nazwa podmiotu zawiera `www` domenę. Aby zabezpieczyć tylko dowolną poddomenę, określ tutaj w pełni kwalifikowaną nazwę domeny (na przykład `mysubdomain.contoso.com` ).|
| Subskrypcja | Subskrypcja, która będzie zawierać certyfikat. |
| Grupa zasobów | Grupa zasobów, która będzie zawierać certyfikat. Możesz użyć nowej grupy zasobów lub na przykład wybrać tę samą grupę zasobów, App Service aplikację. |
| Certyfikat SKU | Określa typ certyfikatu do utworzenia, certyfikat standardowy lub certyfikat [wieloznaczny.](https://wikipedia.org/wiki/Wildcard_certificate) |
| Postanowienia prawne | Kliknij, aby potwierdzić, że akceptujesz postanowienia prawne. Certyfikaty są uzyskiwane od firmy GoDaddy. |

> [!NOTE]
> App Service certyfikaty zakupione na platformie Azure są wystawiane przez firmę GoDaddy. W przypadku niektórych domen najwyższego poziomu należy jawnie zezwolić na firmę GoDaddy jako wystawcę certyfikatu, tworząc rekord domeny [CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) o wartości : `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Przechowywanie w Azure Key Vault

Po zakończeniu procesu zakupu certyfikatu należy wykonać jeszcze kilka kroków, zanim będzie można rozpocząć korzystanie z tego certyfikatu. 

Wybierz certyfikat na stronie [App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) certyfikatów, a następnie kliknij pozycję **Konfiguracja certyfikatu**  >  **Krok 1: Przechowuj**.

![Konfigurowanie Key Vault magazynu App Service magazynu](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) to usługa platformy Azure, która pomaga chronić klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze. Jest to magazyn z możliwością wyboru dla App Service certyfikatów.

Na stronie **Key Vault Stan** kliknij pozycję **Repozytorium Key Vault,** aby utworzyć nowy magazyn, lub wybierz istniejący magazyn. Jeśli zdecydujesz się utworzyć nowy magazyn, skorzystaj z poniższej tabeli, aby skonfigurować magazyn, a następnie kliknij przycisk Utwórz. Utwórz nową Key Vault w tej samej subskrypcji i grupie zasobów co App Service aplikacji.

| Ustawienie | Opis |
|-|-|
| Nazwa | Unikatowa nazwa, która składa się ze znaków alfanumerycznych i łączników. |
| Grupa zasobów | Jako rekomendację wybierz tę samą grupę zasobów, która jest App Service certyfikatu. |
| Lokalizacja | Wybierz tę samą lokalizację, w App Service aplikacji. |
| Warstwa cenowa | Aby uzyskać więcej informacji, [zobacz Azure Key Vault szczegóły cennika.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Zasady dostępu| Definiuje aplikacje i dozwolony dostęp do zasobów magazynu. Możesz skonfigurować go później, zgodnie z instrukcjami w te [tematu Assign a Key Vault access policy (Przypisywanie zasad Key Vault dostępu).](../key-vault/general/assign-access-policy-portal.md) |
| Virtual Network dostępu | Ograniczanie dostępu magazynu do niektórych sieci wirtualnych platformy Azure. Możesz skonfigurować go później, zgodnie z instrukcjami w te [tematu Configure Azure Key Vault Firewalls and Virtual Networks (Konfigurowanie](../key-vault/general/network-security.md) zapór i sieci wirtualnych) |

Po wybraniu magazynu zamknij stronę **repozytorium Key Vault Repozytorium.** Opcja **Krok 1. Przechowywanie** powinna wyświetlić zielony znacznik wyboru dla powodzenia. Nie otwieraj strony w następnym kroku.

### <a name="verify-domain-ownership"></a>Weryfikowanie własności domeny

Na tej **samej stronie Konfiguracja certyfikatu,** która jest używana w ostatnim kroku, kliknij pozycję Krok **2. Weryfikacja .**

![Weryfikowanie domeny pod App Service certyfikatu](./media/configure-ssl-certificate/verify-domain.png)

Wybierz **pozycję App Service Weryfikację.** Ponieważ domena została już zamapowana na aplikację internetową (zobacz [Wymagania wstępne),](#prerequisites)jest już zweryfikowana. Po prostu kliknij **przycisk Weryfikuj,** aby zakończyć ten krok. Klikaj **przycisk Odśwież,** aż pojawi **się komunikat Certyfikat jest zweryfikowany** w domenie.

> [!NOTE]
> Obsługiwane są cztery typy metod weryfikacji domeny: 
> 
> - **App Service** — najbardziej wygodna opcja, gdy domena jest już zamapowana na aplikację App Service w tej samej subskrypcji. Wykorzystuje to fakt, że aplikacja App Service już zweryfikowała własność domeny.
> - **Domena** — sprawdź [domenę App Service zakupioną na platformie Azure.](manage-custom-dns-buy-domain.md) Platforma Azure automatycznie dodaje rekord TXT weryfikacji i kończy proces.
> - **Poczta** — zweryfikuj domenę, wysyłając wiadomość e-mail do administratora domeny. Instrukcje są udostępniane po wybraniu opcji.
> - **Ręczne** — weryfikowanie domeny przy użyciu strony HTML **(tylko certyfikat standardowy)** lub rekordu TXT systemu DNS. Po wybraniu opcji są udostępniane instrukcje.

### <a name="import-certificate-into-app-service"></a>Importowanie certyfikatu do App Service

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym okienku nawigacji aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL** Certyfikaty klucza prywatnego  >  **(pfx)**  >  **Importuj certyfikaty Certyfikat usługi App Service.**

![Importowanie App Service certyfikatu w App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Wybierz właśnie zakupiony certyfikat, a następnie wybierz przycisk **OK.**

Po zakończeniu operacji certyfikat zostanie wyświetlony na liście **Certyfikaty kluczy prywatnych.**

![Importowanie App Service certyfikatu zakończonego](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową przy użyciu tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w te [tematu Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importowanie certyfikatu z Key Vault

Jeśli używasz usługi Azure Key Vault do zarządzania certyfikatami, możesz zaimportować certyfikat PKCS12 z usługi Key Vault do usługi App Service, o ile spełnia [on wymagania](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autoryzowanie App Service odczytu z magazynu
Domyślnie dostawca App Service zasobów nie ma dostępu do Key Vault. Aby użyć klucza Key Vault wdrożenia certyfikatu, należy autoryzować dostęp do odczytu dostawcy zasobów [do usługi KeyVault.](../key-vault/general/assign-access-policy-cli.md) 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  jest główną nazwą dostawcy zasobów dla App Service i jest taka sama dla wszystkich subskrypcji platformy Azure. W Azure Government chmury użyj zamiast tego nazwy głównej usługi dostawcy `6a02c803-dafd-4136-b4c3-5a6f318b4714` zasobów.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importowanie certyfikatu z magazynu do aplikacji

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

Na lewym pasku nawigacyjnym aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL** Certyfikaty klucza prywatnego  >  **(pfx)**  >  **Importuj certyfikat Key Vault SSL.**

![Importowanie Key Vault certyfikatów w App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Skorzystaj z poniższej tabeli, aby wybrać certyfikat.

| Ustawienie | Opis |
|-|-|
| Subskrypcja | Subskrypcja, do Key Vault należy. |
| Key Vault | Magazyn z certyfikatem, który chcesz zaimportować. |
| Certyfikat | Wybierz certyfikat z listy certyfikatów PKCS12 w magazynie. Wszystkie certyfikaty PKCS12 w magazynie są wyświetlane z odciskami palca, ale nie wszystkie są obsługiwane w App Service. |

Po zakończeniu operacji certyfikat zostanie wyświetlony na liście **Certyfikaty kluczy prywatnych.** Jeśli importowanie nie powiedzie się z błędem, certyfikat nie spełnia wymagań dotyczących [App Service](#private-certificate-requirements).

![Importowanie Key Vault certyfikatu zakończone](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> W przypadku zaktualizowania certyfikatu w Key Vault przy użyciu nowego certyfikatu program App Service automatycznie zsynchronizuj certyfikat w ciągu 24 godzin.

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową przy użyciu tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w te [tematu Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Przekazywanie certyfikatu prywatnego

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

Wyeksportuj scalony certyfikat TLS/SSL z kluczem prywatnym, za pomocą których zostało wygenerowane żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _&lt; private-key-file>_ i _&lt; merged-certificate-file>_ ścieżkami do klucza prywatnego i scalonego pliku certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po wyświetleniu monitu określ hasło eksportu. Tego hasła użyjesz podczas przekazywania certyfikatu protokołu TLS/SSL do usługi App Service później.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Przekazywanie certyfikatu do App Service

Teraz możesz przekazać certyfikat do usługi App Service.

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

W lewym okienku nawigacji aplikacji wybierz pozycję **Ustawienia protokołu TLS/SSL** Certyfikaty klucza prywatnego  >  **(pfx)**  >  **Przekaż certyfikat.**

![Przekazywanie certyfikatu prywatnego w App Service](./media/configure-ssl-certificate/upload-private-cert.png)

W pozycji **Plik PFX certyfikatu** wybierz swój plik PFX. W polu **Hasło certyfikatu** wpisz hasło, które zostało utworzone podczas eksportowania pliku PFX. Po zakończeniu kliknij pozycję **Przekaż**. 

Po zakończeniu operacji certyfikat zostanie wyświetlony na liście **Certyfikaty kluczy prywatnych.**

![Przekazywanie certyfikatu zakończone](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Aby zabezpieczyć domenę niestandardową przy użyciu tego certyfikatu, nadal musisz utworzyć powiązanie certyfikatu. Wykonaj kroki opisane w te [tematu Tworzenie powiązania](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Przekazywanie certyfikatu publicznego

Certyfikaty publiczne są obsługiwane w formacie *cer.* 

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>menu po lewej stronie wybierz pozycję **App Services**  >  **\<app-name>** .

Na lewym pasku nawigacyjnym aplikacji kliknij pozycję **Ustawienia protokołu TLS/SSL**  >  **Certyfikaty publiczne (cer)**  >  **Przekaż certyfikat klucza publicznego.**

W **nazwa**, wpisz nazwę certyfikatu. W **pliku certyfikatu CER** wybierz plik CER.

Kliknij pozycję **Przekaż**.

![Przekazywanie certyfikatu publicznego w App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Po przesłaniu certyfikatu skopiuj odcisk palca certyfikatu i zobacz [Temat Udostępnij certyfikat](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Zarządzanie App Service certyfikatów

W tej sekcji pokazano, jak zarządzać certyfikatem App Service zakupionym w sekcji [Importowanie App Service certyfikatu.](#import-an-app-service-certificate)

- [Ponowne klucz certyfikatu](#rekey-certificate)
- [Odnawianie certyfikatu](#renew-certificate)
- [Eksportowanie certyfikatu](#export-certificate)
- [Usuwanie certyfikatu](#delete-certificate)

### <a name="rekey-certificate"></a>Ponowne klucz certyfikatu

Jeśli uważasz, że klucz prywatny certyfikatu został naruszony, możesz zmienić klucz certyfikatu. Wybierz certyfikat na stronie [App Service certyfikatów, a](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) następnie wybierz pozycję Wyklucz i **synchronizuj** w lewym panelu nawigacyjnym.

Kliknij **pozycję Wyklucz** ponownie, aby rozpocząć proces. Ten proces może potrwać od 1 do 10 minut.

![Ponowne wyklucz App Service certyfikatu](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Ponowne wykluczenie certyfikatu wycofuje certyfikat z nowym certyfikatem wystawionym przez urząd certyfikacji.

Po zakończeniu operacji ponownego kluczy kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania żadnych przestojów w działaniu aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj,** program App Service automatycznie zsynchronizuj certyfikat w ciągu 24 godzin.

### <a name="renew-certificate"></a>Odnawianie certyfikatu

Aby w dowolnym momencie włączyć automatyczne odnawianie certyfikatu, wybierz certyfikat na stronie certyfikaty App Service, [a](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) następnie kliknij pozycję **Ustawienia** automatycznego odnawiania w lewym panelu nawigacyjnym. Domyślnie certyfikaty App Service mają jednoroczny okres ważności.

Wybierz **pozycję Wł.,** a następnie **kliknij pozycję Zapisz.** Jeśli włączona jest funkcja automatycznego odnawiania, można rozpocząć automatyczne odnawianie 30 dni przed wygaśnięciem.

![Automatyczne odnawianie App Service certyfikatu](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Aby zamiast tego ręcznie odnowić certyfikat, kliknij pozycję **Odnów ręcznie.** Możesz zażądać ręcznego odnowienia certyfikatu na 60 dni przed wygaśnięciem.

Po zakończeniu operacji odnawiania kliknij pozycję **Synchronizuj**. Operacja synchronizacji automatycznie aktualizuje powiązania nazwy hosta dla certyfikatu w App Service bez powodowania żadnych przestojów w działaniu aplikacji.

> [!NOTE]
> Jeśli nie klikniesz przycisku **Synchronizuj,** program App Service automatycznie zsynchronizuj certyfikat w ciągu 24 godzin.

### <a name="export-certificate"></a>Eksportowanie certyfikatu

Ponieważ klucz Certyfikat usługi App Service to Key Vault [tajny,](../key-vault/general/about-keys-secrets-certificates.md)można wyeksportować jego kopię PFX i użyć jej dla innych usług platformy Azure lub spoza platformy Azure.

Aby wyeksportować Certyfikat usługi App Service jako plik PFX, uruchom następujące polecenia w Cloud Shell [.](https://shell.azure.com) Możesz również uruchomić go lokalnie, jeśli zainstalowano interfejs [wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Zastąp symbole zastępcze nazwami użytymi podczas tworzenia certyfikatu [App Service .](#start-certificate-order)

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

Pobrany plik *appservicecertificate.pfx* to pierwotny plik PKCS12 zawierający certyfikaty publiczne i prywatne. W każdym wierszu użyj pustego ciągu dla hasła importu i frazy PEM pass.

### <a name="delete-certificate"></a>Usuwanie certyfikatu 

Usunięcie certyfikatu App Service jest ostateczne i nieodwracalne. Usunięcie zasobu Certyfikat usługi App Service powoduje odwołanie certyfikatu. Każde powiązanie w App Service tym certyfikatem staje się nieprawidłowe. Aby zapobiec przypadkowemu usunięciu, platforma Azure umieszcza blokadę certyfikatu. Aby usunąć certyfikat App Service, należy najpierw usunąć blokadę usuwania certyfikatu.

Wybierz certyfikat na stronie [App Service certyfikaty,](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) a następnie wybierz pozycję **Blokady** w lewym panelu nawigacyjnym.

Znajdź blokadę certyfikatu z typem blokady **Usuń**. Po prawej stronie wybierz pozycję **Usuń**.

![Usuwanie blokady dla App Service certyfikatu](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Teraz możesz usunąć App Service certyfikatu. W lewym panelu nawigacyjnym wybierz pozycję **Przegląd**  >  **Usuń.** W oknie dialogowym potwierdzenia wpisz nazwę certyfikatu i wybierz przycisk **OK.**

## <a name="automate-with-scripts&quot;></a>Automatyzowanie przy użyciu skryptów

### <a name=&quot;azure-cli&quot;></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 &quot;Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania protokołu TLS/SSL w Azure App Service](configure-ssl-bindings.md)
* [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https)
* [Wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Używanie certyfikatu TLS/SSL w kodzie w usłudze Azure App Service](configure-ssl-certificate-in-code.md)
* [Często zadawane pytania: App Service certyfikatów](./faq-configuration-and-management.md)
