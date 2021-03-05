---
title: Azure Red Hat OpenShift z systemem OpenShift 4 — Konfigurowanie uwierzytelniania Azure Active Directory przy użyciu Azure Portal i konsoli sieci Web OpenShift
description: Dowiedz się, jak skonfigurować uwierzytelnianie Azure Active Directory dla klastra Red Hat OpenShift systemu Azure z systemem OpenShift 4 przy użyciu Azure Portal i konsoli sieci Web OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: eda45b1a1a011a646915cf45e45218ae168a2af6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213083"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Konfigurowanie uwierzytelniania Azure Active Directory na potrzeby klastra usługi Azure Red Hat OpenShift 4 (Portal)

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Utwórz **adres URL wywołania zwrotnego protokołu OAuth** klastra i zanotuj go. Pamiętaj, aby zastąpić **ciąg ARO-RG** nazwą grupy zasobów i **wystawić klaster** z nazwą klastra.

> [!NOTE]
> `AAD`Sekcja w adresie URL wywołania zwrotnego OAuth powinna być zgodna z nazwą dostawcy tożsamości OAuth, który zostanie później skonfigurowany.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Tworzenie aplikacji Azure Active Directory na potrzeby uwierzytelniania

Zaloguj się do Azure Portal i przejdź do [bloku rejestracje aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), a następnie kliknij pozycję **Nowa rejestracja** , aby utworzyć nową aplikację.

Podaj nazwę aplikacji, na przykład **ARO-azuread-auth**, i wypełnij **Identyfikator URI przekierowania** , używając wartości pobranego wcześniej adresu URL wywołania zwrotnego protokołu OAuth.

![Rejestrowanie nowej aplikacji](media/aro4-ad-registerapp.png)

Przejdź do pola **certyfikaty & wpisy tajne** i kliknij pozycję **nowy klucz tajny klienta** i wprowadź szczegóły. Zanotuj wartość klucza, ponieważ będzie ona używana w późniejszym etapie. Nie będzie można go odzyskać ponownie.

![Utwórz klucz tajny](media/aro4-ad-clientsecret.png)

Przejdź do **omówienia** i zanotuj identyfikator **aplikacji (klienta) identyfikator** i **katalog (dzierżawa)**. Będziesz ich potrzebować na późniejszym etapie.

![Pobieranie identyfikatorów aplikacji (klienta) i katalogów (dzierżawców)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń

Deweloperzy aplikacji mogą używać [opcjonalnych oświadczeń](../active-directory/develop/active-directory-optional-claims.md) w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia mają być wysyłane do aplikacji.

Opcjonalne oświadczenia można używać do:

* Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
* Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
* Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.

Skonfigurujemy OpenShift do korzystania z tego `email` żądania i wrócisz do `upn` Ustawienia preferowanej nazwy użytkownika, dodając `upn` jako część tokenu identyfikatora zwróconego przez Azure Active Directory.

Przejdź do obszaru **Konfiguracja tokenu (wersja zapoznawcza)** i kliknij pozycję **Dodaj opcjonalne** pole. Wybierz pozycję **Identyfikator** , a następnie sprawdź oświadczenia **adresu e-mail** i **nazwy UPN** .

![Zrzut ekranu pokazujący dodane oświadczenia e-mail i nazwy UPN.](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Przypisywanie użytkowników i grup do klastra (opcjonalnie)

Aplikacje zarejestrowane w dzierżawie usługi Azure Active Directory (Azure AD) są domyślnie dostępne dla wszystkich użytkowników dzierżawy, którzy pomyślnie uwierzytelniają się. Usługa Azure AD umożliwia administratorom dzierżawy i deweloperom ograniczenie aplikacji do określonego zestawu użytkowników lub grup zabezpieczeń w dzierżawie.

Postępuj zgodnie z instrukcjami w dokumentacji Azure Active Directory, aby [przypisać użytkowników i grupy do aplikacji](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Konfigurowanie uwierzytelniania OpenShift OpenID Connect

Pobierz `kubeadmin` poświadczenia. Uruchom następujące polecenie, aby znaleźć hasło `kubeadmin` użytkownika.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Następujące przykładowe dane wyjściowe pokazują, że hasło będzie w `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Adres URL konsoli klastra można znaleźć, uruchamiając następujące polecenie, które będzie wyglądać następująco: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Uruchom w przeglądarce adres URL konsoli i zaloguj się przy użyciu `kubeadmin` poświadczeń.

Przejdź do opcji **Administracja**, kliknij pozycję **Ustawienia klastra**, a następnie wybierz kartę **Konfiguracja globalna** . Przewiń, aby wybrać protokół **OAuth**.

Przewiń w dół do pozycji **Dodaj** w obszarze **dostawcy tożsamości** i wybierz pozycję **OpenID Connect Connect (Połącz**).
![Wybierz pozycję OpenID Connect Połącz z listy rozwijanej dostawcy tożsamości](media/aro4-oauth-idpdrop.png)

Podaj nazwę usługi **AAD**, **Identyfikator klienta** jako **Identyfikator aplikacji** i **klucz tajny klienta**. **Adres URL wystawcy** jest sformatowany w taki sposób: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Zastąp symbol zastępczy wcześniej pobranym IDENTYFIKATORem dzierżawy.

![Wypełnij szczegóły protokołu OAuth](media/aro4-oauth-idp-1.png)

Przewiń w dół do sekcji **oświadczenia** i zaktualizuj **preferowaną nazwę użytkownika** , aby używała wartości z oświadczenia **nazwy UPN** .

![Wypełnij szczegóły oświadczeń](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Weryfikuj logowanie za Azure Active Directory

Jeśli teraz wylogujesz konsolę sieci Web OpenShift i spróbujesz ponownie zalogować się, zostanie wyświetlona nowa opcja logowania za pomocą usługi **AAD**. Może być konieczne poczekanie przez kilka minut.

![Ekran logowania z opcją Azure Active Directory](media/aro4-login-2.png)
