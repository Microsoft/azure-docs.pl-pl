---
title: Konfigurowanie wbudowanego rejestru kontenerów dla systemu Azure Red Hat OpenShift 4
description: Konfigurowanie wbudowanego rejestru kontenerów dla systemu Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 79b74c9dccd68ac3abfd9ae6cb2d0c345e45d4bf
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070614"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Konfigurowanie wbudowanego rejestru kontenerów dla systemu Azure Red Hat OpenShift 4

W systemie Azure Red Hat OpenShift jest dostępny zintegrowany rejestr obrazów kontenerów o nazwie [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) , który umożliwia automatyczne Inicjowanie obsługi nowych repozytoriów obrazów na żądanie. Zapewnia to użytkownikom wbudowaną lokalizację, w której aplikacje kompilują w celu wypchnięcia powstających obrazów.

W tym artykule opisano sposób konfigurowania wbudowanego rejestru obrazów kontenerów w klastrze Red Hat OpenShift (ARO) 4. Omawiane tematy:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure AD
> * Konfigurowanie połączenia OpenID Connect
> * Dostęp do rejestru wbudowanego obrazu kontenera

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster ARO. Jeśli potrzebujesz klastra ARO, zobacz samouczek ARO, [Tworzenie klastra usługi Azure Red Hat OpenShift 4](./tutorial-create-cluster.md). Należy utworzyć klaster z `--pull-secret` argumentem `az aro create` .  Jest to konieczne, aby skonfigurować uwierzytelnianie Azure Active Directory i wbudowany rejestr kontenerów.

Po utworzeniu klastra Połącz się z klastrem, wykonując kroki opisane w temacie [łączenie z klastrem usługi Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Upewnij się, że wykonano kroki opisane w temacie "Instalowanie interfejsu wiersza polecenia OpenShift", ponieważ użyjemy `oc` polecenia w dalszej części tego artykułu.
   * Zanotuj adres URL konsoli klastra, który wygląda podobnie do programu `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Wartości dla `<random>` i `<region>` zostaną użyte w dalszej części tego artykułu.
   * Zanotuj `kubeadmin` poświadczenia. Zostaną one również użyte w dalszej części tego artykułu.

### <a name="configure-azure-active-directory-authentication"></a>Konfigurowanie uwierzytelniania usługi Azure Active Directory 

Azure Active Directory (Azure AD) implementuje OpenID Connect Connect (OIDC). OIDC umożliwia korzystanie z usługi Azure AD do logowania się do klastra ARO. Wykonaj kroki opisane w temacie [konfigurowanie Azure Active Directory uwierzytelniania](configure-azure-ad-cli.md) , aby skonfigurować klaster.

## <a name="access-the-built-in-container-image-registry"></a>Dostęp do rejestru wbudowanego obrazu kontenera

Teraz, po skonfigurowaniu metod uwierzytelniania w klastrze ARO, Włącz dostęp do wbudowanego rejestru.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definiowanie użytkownika usługi Azure AD jako administrator

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD. Użyjemy uwierzytelniania OpenShift OpenID Connect w odniesieniu do Azure Active Directory, aby użyć OpenID Connect do zdefiniowania administratora.

   1. Aby zalogować się do konsoli programu, należy użyć funkcji InPrivate, incognito lub innych odpowiedników okna przeglądarki. Okno będzie wyglądać inaczej po włączeniu OIDC.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Okno logowania z włączonym połączeniem OpenID Connect.":::
   1. Wybierz **OpenID Connect**

   > [!NOTE]
   > Zanotuj nazwę użytkownika i hasło, których używasz do logowania się tutaj. Ta nazwa użytkownika i hasło będą działać jako administrator dla innych akcji w tym i innych artykułach.
2. Zaloguj się przy użyciu interfejsu wiersza polecenia OpenShift, wykonując następujące kroki.  W przypadku dyskusji ten proces jest znany jako `oc login` .
   1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowanego użytkownika, a następnie wybierz **polecenie Kopiuj logowanie**.
   2. Jeśli to konieczne, zaloguj się do nowego okna karty z tym samym użytkownikiem.
   3. Wybierz pozycję **Wyświetl token**.
   4. Skopiuj poniższą wartość w polu **Zaloguj się przy użyciu tego tokenu** do schowka i uruchom ją w powłoce, jak pokazano poniżej.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Uruchom `oc whoami` w konsoli programu i zanotuj dane wyjściowe jako **\<aad-user>** .  Ta wartość zostanie użyta w dalszej części artykułu.
4. Wyloguj się z konsoli sieci Web OpenShift. Wybierz przycisk w prawym górnym rogu okna przeglądarki oznaczonego jako **\<aad-user>** i wybierz **Wyloguj** się.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Przyznaj użytkownikowi usługi Azure AD niezbędne role do interakcji z rejestrem

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu `kubeadmin` poświadczeń.
1. Zaloguj się do interfejsu wiersza polecenia OpenShift za pomocą tokenu dla programu `kubeadmin` , wykonując kroki opisane `oc login` powyżej, ale wykonaj je po zalogowaniu się do konsoli sieci Web za pomocą programu `kubeadmin` .
1. Wykonaj następujące polecenia, aby włączyć dostęp do wbudowanego rejestru dla **użytkownika usługi AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
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

Teraz, po skonfigurowaniu wbudowanego rejestru obrazów kontenerów, możesz zacząć od wdrożenia aplikacji na OpenShift. W przypadku aplikacji Java zapoznaj się [z tematem Wdrażanie aplikacji Java z otwartą wolnością/WebSphereą w klastrze Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).