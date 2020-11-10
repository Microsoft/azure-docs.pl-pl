---
title: Konfigurowanie wbudowanego rejestru kontenerów dla usługi Azure Red Hat OpenShift 4
description: Konfigurowanie wbudowanego rejestru kontenerów dla usługi Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414803"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurowanie wbudowanego rejestru kontenerów dla usługi Azure Red Hat OpenShift 4

W tym artykule opisano sposób konfigurowania wbudowanego rejestru obrazów kontenerów w klastrze Red Hat OpenShift (ARO) 4. Omawiane tematy:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure AD
> * Konfigurowanie połączenia OpenID Connect
> * Dostęp do rejestru wbudowanego obrazu kontenera

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz klaster, wykonując kroki opisane w temacie [Tworzenie klastra usługi Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Należy utworzyć klaster z `--pull-secret` argumentem `az aro create` .  Jest to wymagane, jeśli chcesz skonfigurować usługę Azure AD do logowania, zgodnie z wymaganiami tego artykułu.
* Połącz się z klastrem, wykonując kroki opisane w temacie [łączenie z klastrem usługi Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Upewnij się, że wykonano kroki opisane w temacie "Instalowanie interfejsu wiersza polecenia OpenShift", ponieważ użyjemy `oc` polecenia w dalszej części tego artykułu.
   * Zanotuj adres URL konsoli klastra, który wygląda podobnie do programu `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Wartości dla `<random>` i `<region>` zostaną użyte w dalszej części tego artykułu.
   * Zanotuj `kubeadmin` poświadczenia. Zostaną one użyte w dalszej części tego artykułu.

## <a name="set-up-azure-active-directory"></a>Skonfiguruj Azure Active Directory

Azure Active Directory (Azure AD) implementuje OpenID Connect Connect (OIDC). OIDC umożliwia korzystanie z usługi Azure AD do logowania się do klastra ARO. Wykonaj poniższe kroki, aby skonfigurować usługę Azure AD.

1. Skonfiguruj dzierżawę usługi Azure AD, wykonując czynności opisane w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant). Twoje konto platformy Azure może już mieć dzierżawę. Jeśli tak, możesz pominąć tworzenie, jeśli masz wystarczające uprawnienia w dzierżawie, aby postępować zgodnie z instrukcjami. Zanotuj swój **Identyfikator dzierżawy**. Ta wartość zostanie użyta później.
2. Utwórz co najmniej jednego użytkownika usługi Azure AD, wykonując czynności opisane w temacie [Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Możesz użyć tych kont lub własnych do testowania aplikacji. Zanotuj adresy e-mail i hasła tych kont, aby się zalogować.
3. Utwórz nową rejestrację aplikacji w dzierżawie usługi Azure AD, wykonując czynności opisane w [przewodniku szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app). 
   1. Odwołaj uwagę w wymaganiach wstępnych dotyczących wartości dla `<random>` i `<region>` . Użyj tych wartości, aby utworzyć identyfikator URI zgodnie z następującą formułą:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Po osiągnięciu kroku dotyczącego **identyfikatora URI przekierowania** wprowadź identyfikator URI z poprzedniego kroku.
   1. Wybierz pozycję **Zarejestruj**.
   1. Na stronie **Przegląd** aplikacji Zanotuj wartość wyświetlaną dla **identyfikatora aplikacji (klienta)** , jak pokazano poniżej.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Strona przeglądu aplikacji usługi Azure AD.":::

4. Utwórz nowy klucz tajny klienta. 
   1. W nowo utworzonej rejestracji aplikacji wybierz pozycję **certyfikaty & wpisy tajne** w okienku nawigacji po lewej stronie.
   1. Wybierz pozycję **Nowy wpis tajny klienta**.
   1. Podaj **Opis** i wybierz pozycję **Dodaj**.
   1. Zapisz wywygenerowaną wartość **klucza tajnego klienta** . Ta wartość zostanie użyta w dalszej części artykułu.

### <a name="add-openid-connect-identity-provider"></a>Dodaj dostawcę tożsamości OpenID Connect Connect

Ta wartość zawiera wbudowany rejestr kontenerów.  Aby uzyskać do niego dostęp, Skonfiguruj dostawcę tożsamości (dostawcy tożsamości) przy użyciu usługi Azure AD OIDC, wykonując następujące kroki.

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki jako `kubeadmin` .  Jest to ta sama procedura, która jest używana podczas wykonywania kroków opisanych w [samouczku: łączenie z klastrem usługi Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. W okienku nawigacji po lewej stronie wybierz pozycję **Administracja**  >  **Ustawienia klastra**.
1. W środku strony wybierz pozycję **Konfiguracja globalna**.
1. Znajdź **uwierzytelnianie OAuth** w kolumnie **zasób konfiguracji** tabeli i wybierz ją.
1. W obszarze **dostawcy tożsamości** wybierz pozycję **Dodaj** i wybierz pozycję **OpenID Connect Połącz**.
1. Ustaw **nazwę** jako **OpenID Connect**.  Ta wartość może już być wypełniona.
1. Ustaw **Identyfikator klienta** i **klucz tajny klienta** jako wartości z poprzedniego kroku.
1. Wykonaj ten krok, aby znaleźć wartość **adresu URL wystawcy**.
   1. Zamień na **\<tenant-id>** zapisany podczas sekcji **konfigurację Azure Active Directory** w adresie URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Otwórz adres URL w tej samej przeglądarce, która była używana do współpracy z Azure Portal.
   1. Skopiuj wartość **wystawcy** właściwości w zwróconej treści JSON i wklej ją w polu tekstowym ZATYTUŁOWANYM **adres URL wystawcy**.  Wartość **wystawcy** będzie wyglądać podobnie `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Przejdź do dolnej części strony i wybierz pozycję **Dodaj**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect Połącz w OpenShift.":::
1. Wyloguj się z konsoli sieci Web OpenShift, wybierając przycisk **polecenia: admin** w prawym górnym rogu okna przeglądarki i wybierając pozycję **Wyloguj**.

### <a name="access-the-built-in-container-image-registry"></a>Dostęp do rejestru wbudowanego obrazu kontenera

Poniższe instrukcje umożliwiają dostęp do wbudowanego rejestru.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definiowanie użytkownika usługi Azure AD jako administrator

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.

   1. Aby zalogować się do konsoli programu, należy użyć funkcji InPrivate, incognito lub innych odpowiedników okna przeglądarki.
   1. Okno będzie wyglądać inaczej po włączeniu OIDC.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Okno logowania z włączonym połączeniem OpenID Connect.":::
   1. Wybierz **OpenID Connect**

   > [!NOTE]
   > Zanotuj nazwę użytkownika i hasło, których używasz do logowania się tutaj. Ta nazwa użytkownika i hasło będą działać jako administrator dla innych akcji w tym i innych artykułach.
1. Zaloguj się przy użyciu interfejsu wiersza polecenia OpenShift, wykonując następujące kroki.  W przypadku dyskusji ten proces jest znany jako `oc login` .
   1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowanego użytkownika, a następnie wybierz **polecenie Kopiuj logowanie**.
   1. Jeśli to konieczne, zaloguj się do nowego okna karty z tym samym użytkownikiem.
   1. Wybierz pozycję **Wyświetl token**.
   1. Skopiuj poniższą wartość w polu **Zaloguj się przy użyciu tego tokenu** do schowka i uruchom ją w powłoce, jak pokazano poniżej.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Uruchom `oc whoami` w konsoli programu i zanotuj dane wyjściowe jako **\<aad-user>** .  Ta wartość zostanie użyta w dalszej części artykułu.
1. Wyloguj się z konsoli sieci Web OpenShift. Wybierz przycisk w prawym górnym rogu okna przeglądarki oznaczonego jako **\<aad-user>** i wybierz **Wyloguj** się.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Przyznaj użytkownikowi usługi Azure AD niezbędne role do interakcji z rejestrem

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu `kubeadmin` poświadczeń.
1. Zaloguj się do interfejsu wiersza polecenia OpenShift za pomocą tokenu dla programu `kubeadmin` , wykonując kroki opisane `oc login` powyżej, ale wykonaj je po zalogowaniu się do konsoli sieci Web za pomocą programu `kubeadmin` .
1. Wykonaj następujące polecenia, aby włączyć dostęp do wbudowanego rejestru dla **użytkownika usługi AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   Dane wyjściowe powinny wyglądać podobnie do poniższego.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   Dane wyjściowe powinny wyglądać podobnie do poniższego.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   Dane wyjściowe powinny wyglądać podobnie do poniższego.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   Dane wyjściowe powinny wyglądać podobnie do poniższego.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Uzyskaj adres URL rejestru kontenerów

Użyj `oc get route` polecenia, jak pokazano poniżej, aby uzyskać adres URL rejestru kontenerów.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Zwróć uwagę na dane wyjściowe konsoli dla **adresu URL Container Registry**. Zostanie ona użyta jako w pełni kwalifikowana nazwa rejestru dla tego przewodnika i kolejnych.

## <a name="next-steps"></a>Następne kroki

Użyj wbudowanego rejestru obrazów kontenerów, wdrażając aplikację w usłudze OpenShift.  W przypadku aplikacji Java postępuj zgodnie ze wskazówkami dotyczącymi [wdrażania aplikacji Java z dodatkiem Open wolności/WebSphere w klastrze Red Hat OpenShift 4 na platformie Azure](howto-deploy-java-liberty-app.md).
