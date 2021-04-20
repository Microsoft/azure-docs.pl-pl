---
title: Samodzielne hostienie portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak samodzielnie hostować API Management portalu dla deweloperów.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741888"
---
# <a name="self-host-the-api-management-developer-portal"></a>Samodzielne hoste API Management portalu dla deweloperów

W tym samouczku opisano, jak samodzielnie [hostować API Management dla deweloperów.](api-management-howto-developer-portal.md) Samodzielna hosting zapewnia elastyczność rozszerzania portalu dla deweloperów za pomocą niestandardowej logiki i widżetów, które dynamicznie dostosowywają strony w środowisku uruchomieniowym. Możesz samodzielnie hostować wiele portali dla swojego API Management, z różnymi funkcjami. Gdy samodzielnie hostujesz portal, stajesz się jego konserwacją i ponosisz odpowiedzialność za jego uaktualnienia. 

Poniższe kroki pokazują, jak skonfigurować lokalne środowisko projektowe, wprowadzić zmiany w portalu dla deweloperów oraz opublikować i wdrożyć je na koncie usługi Azure Storage.

Jeśli pliki multimedialne zostały już przekazane lub zmodyfikowane w portalu zarządzanym, zobacz [Przenoszenie](#move-from-managed-to-self-hosted-developer-portal)z zarządzanych do hostowanych samodzielnie w dalszej części tego artykułu.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować lokalne środowisko projektowe, musisz mieć:

- Wystąpienie API Management usługi. Jeśli go nie masz, zobacz [Szybki start — tworzenie wystąpienia usługi Azure API Management .](get-started-create-service-instance.md)
- Konto usługi Azure Storage z [włączoną funkcją statycznych witryn](../storage/blobs/storage-blob-static-website.md) internetowych. Zobacz [Tworzenie konta magazynu.](../storage/common/storage-account-create.md)
- Usługa Git na twojej maszynie. Zainstaluj go, korzystając z [tego samouczka usługi Git.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Node.js (wersja LTS `v10.15.0` lub nowsza) i npm na maszynie. Zobacz [Pobieranie i instalowanie programu Node.js i npm.](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Interfejs wiersza polecenia platformy Azure. Wykonaj [kroki instalacji interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli-windows)

## <a name="step-1-set-up-local-environment"></a>Krok 1. Konfigurowanie środowiska lokalnego

Aby skonfigurować środowisko lokalne, musisz sklonować repozytorium, przełączyć się do najnowszej wersji portalu dla deweloperów i zainstalować pakiety npm.

1. [Sklonuj repozytorium api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) z witryny GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Przejdź do lokalnej kopii tego repo:

    ```console
    cd api-management-developer-portal
    ```

1. Zapoznaj się z najnowszą wersję portalu.

    Przed uruchomieniem poniższego kodu sprawdź bieżący tag wydania w sekcji [Wydania](https://github.com/Azure/api-management-developer-portal/releases) repozytorium i zastąp wartość tagiem `<current-release-tag>` latest release.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Zainstaluj wszystkie dostępne pakiety npm:

    ```console
    npm install
    ```

> [!TIP]
> Zawsze używaj [najnowszej wersji portalu](https://github.com/Azure/api-management-developer-portal/releases) i dbaj o to, aby twoje poszycie portalu było aktualne. Inżynierowie oprogramowania używają gałęzi `master` tego repozytorium do codziennego tworzenia oprogramowania. Ma niestabilne wersje oprogramowania.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Krok 2. Konfigurowanie plików JSON, statycznej witryny internetowej i ustawień CORS

Portal dla deweloperów wymaga API Management API REST do zarządzania zawartością.

### <a name="configdesignjson-file"></a>config.design.jsw pliku

Przejdź do `src` folderu i otwórz plik `config.design.json` .

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Skonfiguruj plik:

1. W wartości `managementApiUrl` zastąp wartość nazwą swojego API Management `<service-name>` wystąpienia. Jeśli skonfigurowano domenę [niestandardową](configure-custom-domain.md), użyj jej zamiast tego (na przykład `https://management.contoso.com` ).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Ręcznie utwórz token SAS,](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) aby umożliwić bezpośredni dostęp interfejsu API REST do API Management wystąpienia.

1. Skopiuj wygenerowany token i wklej go jako `managementApiAccessToken` wartość.

1. W wartości `backendUrl` zastąp wartość nazwą swojego API Management `<service-name>` wystąpienia. Jeśli skonfigurowano domenę [niestandardową](configure-custom-domain.md), użyj jej zamiast tego (na przykład `https://portal.contoso.com` ).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Jeśli chcesz włączyć usługę CAPTCHA w portalu dla deweloperów, zobacz [Włączanie CAPTCHA.](#enable-captcha)

### <a name="configpublishjson-file"></a>config.publish.jsw pliku

Przejdź do `src` folderu i otwórz `config.publish.json` plik.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Skonfiguruj plik:

1. Skopiuj i wklej `managementApiUrl` wartości i z poprzedniego pliku `managementApiAccessToken` konfiguracji.

1. Jeśli chcesz włączyć usługę CAPTCHA w portalu dla deweloperów, zobacz [Włączanie CAPTCHA.](#enable-captcha)

### <a name="configruntimejson-file"></a>config.runtime.jsw pliku

Przejdź do `src` folderu i otwórz `config.runtime.json` plik.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Skonfiguruj plik:

1. Skopiuj i wklej `managementApiUrl` wartość z poprzedniego pliku konfiguracji.

1. W wartości `backendUrl` zastąp wartość nazwą swojego API Management `<service-name>` wystąpienia. Jeśli skonfigurowano domenę [niestandardową,](configure-custom-domain.md)użyj jej zamiast tego (na przykład. `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Konfigurowanie statycznej witryny internetowej

Skonfiguruj funkcję **statycznej witryny** internetowej na koncie magazynu, udostępniając trasy do indeksu i stron błędów:

1. Przejdź do konta magazynu w witrynie Azure Portal wybierz pozycję Statyczna witryna **internetowa** z menu po lewej stronie.

1. Na stronie **Statyczna witryna** internetowa wybierz pozycję **Włączone.**

1. W **polu Nazwa dokumentu indeksu** wprowadźindex.htm *l*.

1. W polu **Ścieżka dokumentu błędu** wprowadź *wartość 404/index.html*.

1. Wybierz pozycję **Zapisz**.

### <a name="configure-the-cors-settings"></a>Konfigurowanie ustawień cors

Skonfiguruj ustawienia udostępniania zasobów między źródłami (CORS):

1. Przejdź do konta magazynu w chmurze Azure Portal wybierz pozycję **CORS** z menu po lewej stronie.

1. Na **karcie Blob service** skonfiguruj następujące reguły:

    | Reguła | Wartość |
    | ---- | ----- |
    | Dozwolone źródła | * |
    | Dozwolone metody | Wybierz wszystkie zlecenia HTTP. |
    | Dozwolone nagłówki | * |
    | Uwidocznione nagłówki | * |
    | Maksymalny wiek | 0 |

1. Wybierz pozycję **Zapisz**.

## <a name="step-3-run-the-portal"></a>Krok 3. Uruchamianie portalu

Teraz możesz skompilować i uruchomić wystąpienie portalu lokalnego w trybie projektowania. W trybie projektowania wszystkie optymalizacje są wyłączone i mapy źródłowe są włączone.

Uruchom następujące polecenie:

```console
npm start
```

Po krótkiej chwili zostanie automatycznie otwarta domyślna przeglądarka z lokalnym wystąpieniem portalu dla deweloperów. Adres domyślny to `http://localhost:8080` , ale port może ulec zmianie, jeśli jest już `8080` zajęty. Wszelkie zmiany w bazie kodu projektu wyzwolą ponowne skompilowanie i odświeżenie okna przeglądarki.

## <a name="step-4-edit-through-the-visual-editor"></a>Krok 4. Edytowanie za pomocą edytora wizualizacji

Do wykonania tych zadań użyj edytora wizualizacji:

- Dostosowywanie portalu
- Tworzenie zawartości
- Organizowanie struktury witryny internetowej
- Stylizowanie wyglądu

Zobacz [Samouczek: uzyskiwanie dostępu do portalu dla deweloperów i dostosowywanie go.](api-management-howto-developer-portal-customize.md) Obejmuje on podstawowe informacje dotyczące administracyjnego interfejsu użytkownika i zawiera listę zalecanych zmian zawartości domyślnej. Zapisz wszystkie zmiany w środowisku lokalnym, a następnie naciśnij **klawisze Ctrl+C,** aby je zamknąć.

## <a name="step-5-publish-locally"></a>Krok 5. Publikowanie lokalne

Dane portalu pochodzą w postaci obiektów o silnych typach. Następujące polecenie tłumaczy je na pliki statyczne i umieszcza dane wyjściowe w `./dist/website` katalogu :

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Krok 6. Przekazywanie plików statycznych do obiektu blob

Użyj interfejsu wiersza polecenia platformy Azure, aby przekazać lokalnie wygenerowane pliki statyczne do obiektu blob i upewnić się, że odwiedzający mogą do nich uzyskać dostęp:

1. Otwórz wiersz polecenia systemu Windows, program PowerShell lub inną powłokę poleceń.

1. Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure.
   
    Zastąp `<account-connection-string>` ciąg ciągami połączenia konta magazynu. Można go pobrać z sekcji **Klucze dostępu** konta magazynu.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Krok 7. Przejdź do witryny internetowej

Witryna internetowa znajduje się teraz pod nazwą hosta określoną we właściwościach usługi Azure Storage **(podstawowy punkt końcowy w** **statycznych witrynach internetowych).**

## <a name="step-8-change-api-management-notification-templates"></a>Krok 8. Zmienianie szablonów API Management powiadomień

Zastąp adres URL portalu deweloperów w szablonach powiadomień API Management, aby wskazać własny portal. Zobacz [Konfigurowanie powiadomień i szablonów wiadomości e-mail w usłudze Azure API Management.](api-management-howto-configure-notifications.md)

W szczególności należy wprowadzić następujące zmiany w szablonach domyślnych:

> [!NOTE]
> Wartości w następujących **zaktualizowanych sekcjach** zakładają, że hostujesz portal pod adresem **https: \/ /portal.contoso.com/**. 

### <a name="email-change-confirmation"></a>Potwierdzenie zmiany w wiadomości e-mail

Zaktualizuj adres URL portalu dla deweloperów w szablonie powiadomienia z **potwierdzeniem zmiany wiadomości** e-mail:

**Oryginalna zawartość**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualizacja**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Potwierdzenie nowego konta dewelopera

Zaktualizuj adres URL portalu dla deweloperów w **szablonie powiadomienia o potwierdzeniu nowego konta** dewelopera:

**Oryginalna zawartość**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualizacja**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Zapraszanie użytkownika

Zaktualizuj adres URL portalu deweloperów w **szablonie powiadomienia zaproś** użytkownika:

**Oryginalna zawartość**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Aktualizacja**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Aktywowano nową subskrypcję

Zaktualizuj adres URL portalu deweloperów w **szablonie powiadomień Nowa subskrypcja aktywowana:**

**Oryginalna zawartość**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Aktualizacja**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Oryginalna zawartość**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Aktualizacja**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Oryginalna zawartość**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Aktualizacja**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Oryginalna zawartość**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Aktualizacja**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Potwierdzenie zmiany hasła

Zaktualizuj adres URL portalu dla deweloperów w **szablonie powiadomienia z potwierdzeniem zmiany** hasła:

**Oryginalna zawartość**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualizacja**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Wszystkie szablony

Zaktualizuj adres URL portalu dla deweloperów w dowolnym szablonie, który ma link w stopce:

**Oryginalna zawartość**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualizacja**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Przechodzenie z portalu zarządzanego do własnego portalu dla deweloperów

Z czasem wymagania biznesowe mogą ulec zmianie. Może się to zakończyć sytuacją, w której zarządzana wersja portalu dla deweloperów API Management nie spełnia już Twoich potrzeb. Na przykład nowe wymaganie może wymusić tworzenie niestandardowego widżetu, który integruje się z dostawcą danych innej firmy. W przeciwieństwie do wersji manged, samodzielnie hostowana wersja portalu zapewnia pełną elastyczność i rozszerzalność.

### <a name="transition-process"></a>Proces przejścia

Możesz przejść z wersji zarządzanej do wersji hostowanej samodzielnie w ramach tego samego API Management usługi. Proces zachowuje modyfikacje wprowadzone w zarządzanej wersji portalu. Pamiętaj, aby wcześniej wykonać kopię zapasową zawartości portalu. Skrypt kopii zapasowej można znaleźć w `scripts` folderze repozytorium GitHub API Management portal [deweloperów.](https://github.com/Azure/api-management-developer-portal)

Proces konwersji jest niemal identyczny jak w przypadku konfigurowania ogólnego portalu samodzielnie, jak pokazano w poprzednich krokach tego artykułu. W kroku konfiguracji występuje jeden wyjątek. Konto magazynu w pliku musi być takie samo jak konto `config.design.json` magazynu zarządzanej wersji portalu. Aby [uzyskać instrukcje](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls) dotyczące pobierania adresu URL sygnatury dostępu współdzielonego, zobacz Samouczek: używanie przypisanej przez system tożsamości maszyny wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Azure Storage za pośrednictwem poświadczeń SAS.

> [!TIP]
> Zalecamy użycie oddzielnego konta magazynu w `config.publish.json` pliku . Takie podejście zapewnia większą kontrolę i upraszcza zarządzanie usługą hostingu w portalu.

## <a name="enable-captcha"></a>Włączanie captcha

Podczas konfigurowania własnego portalu można wyłączyć captcha za pomocą `useHipCaptcha` tego ustawienia. Komunikacja z captcha odbywa się za pośrednictwem punktu końcowego, który umożliwia współużytkować zasoby między źródłami (CORS, Cross-Origin Resource Sharing) tylko w przypadku nazwy hosta zarządzanego portalu deweloperów. Jeśli portal dla deweloperów jest hostowany samodzielnie, używa innej nazwy hosta, a captcha nie zezwoli na komunikację.

### <a name="update-the-json-config-files"></a>Aktualizowanie plików konfiguracji JSON

Aby włączyć usługę CAPTCHA we własnym portalu:

1. Przypisz domenę niestandardową (na przykład ) do punktu końcowego portalu deweloperów `api.contoso.com` usługi API Management  wirtualnej.

    Ta domena dotyczy zarządzanej wersji portalu i punktu końcowego CAPTCHA. Aby uzyskać instrukcje, [zobacz Konfigurowanie niestandardowej nazwy domeny dla wystąpienia usługi Azure API Management](configure-custom-domain.md).

1. Przejdź do `src` folderu w [środowisku lokalnym dla](#step-1-set-up-local-environment) własnego portalu.

1. Zaktualizuj pliki `json` konfiguracji:

    | Plik | Nowa wartość | Uwaga |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Zastąp `<custom-domain>` wartość domeną niestandardową, która zostanie ustawiona w pierwszym kroku. |
    |  | `"useHipCaptcha": true` | Zmień wartość na `true` |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Zastąp `<custom-domain>` wartość domeną niestandardową, która zostanie ustawiona w pierwszym kroku. |
    |  | `"useHipCaptcha": true` | Zmień wartość na `true` |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Zastąp `<custom-domain>` wartość domeną niestandardową, która zostanie ustawiona w pierwszym kroku. |

1. [Opublikuj](#step-5-publish-locally) portal.

1. [Przekaż](#step-6-upload-static-files-to-a-blob) i hostuj nowo opublikowany portal.

1. Uwidocznij własny portal za pośrednictwem domeny niestandardowej.

Pierwszy i drugi poziom domeny portalu muszą być zgodne z domeną ustawioną w pierwszym kroku. Na przykład `portal.contoso.com`. Dokładne kroki zależą od wybranej platformy hostingu. Jeśli używasz konta usługi Azure Storage, możesz zapoznać się z instrukcjami mapowanie domeny niestandardowej [na Azure Blob Storage punktu końcowego.](../storage/blobs/storage-custom-domain-name.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alternatywnych podejściach do samodzielnego hostingu](developer-portal-alternative-processes-self-host.md)
