---
title: Azure Red Hat OpenShift z systemem OpenShift 4 — Konfigurowanie uwierzytelniania Azure Active Directory przy użyciu wiersza polecenia
description: Dowiedz się, jak skonfigurować uwierzytelnianie Azure Active Directory dla klastra Red Hat OpenShift systemu Azure z systemem OpenShift 4 przy użyciu wiersza polecenia
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0e9e7c5fccf56077e138ffe24226344257bb3a13
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636292"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Konfigurowanie uwierzytelniania Azure Active Directory na potrzeby klastra usługi Azure Red Hat OpenShift 4 (CLI)

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pobierz adresy URL specyficzne dla klastra, które będą używane do konfigurowania aplikacji Azure Active Directory.

Ustaw zmienne dla grupy zasobów i nazwy klastra.

Zamień **\<resource_group>** na nazwę grupy zasobów i **\<aro_cluster>** nazwę klastra.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Utwórz adres URL wywołania zwrotnego protokołu OAuth klastra i Zapisz go w zmiennej **oauthCallbackURL**. 

> [!NOTE]
> `AAD`Sekcja w adresie URL wywołania zwrotnego OAuth powinna być zgodna z nazwą dostawcy tożsamości OAuth, który zostanie później skonfigurowany.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

Format oauthCallbackURL jest nieco inny w przypadku domen niestandardowych:

* Uruchom następujące polecenie, jeśli używasz domeny niestandardowej, np. `contoso.com` . 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Jeśli nie korzystasz z domeny niestandardowej, będzie to `$domain` ciąg alnum ciągu i zostanie rozszerzony przez `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> `AAD`Sekcja w adresie URL wywołania zwrotnego OAuth powinna być zgodna z nazwą dostawcy tożsamości OAuth, który zostanie później skonfigurowany.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Tworzenie aplikacji Azure Active Directory na potrzeby uwierzytelniania

Zamień na **\<client_secret>** bezpieczne hasło aplikacji.

```azurecli-interactive
client_secret=<client_secret>
```

Utwórz aplikację Azure Active Directory i Pobierz identyfikator utworzonego aplikacji.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Pobierz identyfikator dzierżawy subskrypcji, do której należy aplikacja.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Utwórz plik manifestu, aby zdefiniować opcjonalne oświadczenia do uwzględnienia w tokenie identyfikatora

Deweloperzy aplikacji mogą używać [opcjonalnych oświadczeń](../active-directory/develop/active-directory-optional-claims.md) w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia mają być wysyłane do aplikacji.

Opcjonalne oświadczenia można używać do:

- Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.

Skonfigurujemy OpenShift do korzystania z tego `email` żądania i wrócisz do `upn` Ustawienia preferowanej nazwy użytkownika, dodając `upn` jako część tokenu identyfikatora zwróconego przez Azure Active Directory.

Utwórz **manifest.jsna** pliku, aby skonfigurować aplikację Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Aktualizowanie optionalClaims aplikacji Azure Active Directory przy użyciu manifestu

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aktualizowanie uprawnień do zakresu aplikacji Azure Active Directory

Aby można było odczytać informacje o użytkowniku z Azure Active Directory, musimy zdefiniować odpowiednie zakresy.

Dodaj uprawnienie do **Azure Active Directory Graph. User. Read** zakresu, aby włączyć logowanie i odczytywać profil użytkownika.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Możesz bezpiecznie zignorować komunikat, aby udzielić zgody, chyba że użytkownik zostanie uwierzytelniony jako Administrator globalny dla tego Azure Active Directory. Użytkownicy domeny standardowej będą proszeni o udzielenie zgody podczas pierwszego logowania do klastra przy użyciu poświadczeń usługi AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Przypisywanie użytkowników i grup do klastra (opcjonalnie)

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy pomyślnie uwierzytelniają się. Usługa Azure AD umożliwia administratorom dzierżawy i deweloperom ograniczenie aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

Postępuj zgodnie z instrukcjami w dokumentacji Azure Active Directory, aby [przypisać użytkowników i grupy do aplikacji](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurowanie uwierzytelniania OpenShift OpenID Connect

Pobierz `kubeadmin` poświadczenia. Uruchom następujące polecenie, aby znaleźć hasło `kubeadmin` użytkownika.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Zaloguj się do serwera interfejsu API klastra OpenShift przy użyciu następującego polecenia. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Utwórz klucz tajny OpenShift, aby przechowywać klucz tajny aplikacji Azure Active Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Utwórz plik **OIDC. YAML** , aby skonfigurować uwierzytelnianie OpenShift openid connect względem Azure Active Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Zastosuj konfigurację do klastra.

```azurecli-interactive
oc apply -f oidc.yaml
```

Otrzymasz odpowiedź podobną do następującej.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Weryfikuj logowanie za Azure Active Directory

Jeśli teraz wylogujesz konsolę sieci Web OpenShift i spróbujesz się zalogować ponownie, zostanie wyświetlona nowa opcja logowania za pomocą usługi **AAD**. Może być konieczne poczekanie przez kilka minut.

![Ekran logowania przy użyciu opcji Azure Active Directory](media/aro4-login-2.png)
