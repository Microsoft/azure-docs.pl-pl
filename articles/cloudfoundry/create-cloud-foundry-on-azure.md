---
title: Tworzenie klastra rozwiązania Pivotal Cloud Foundry na platformie Azure
description: Dowiedz się, jak skonfigurować parametry wymagane do aprowizowania klastra rozwiązania Pivotal Cloud Foundry na platformie Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 735c0955a25a3995c94c73bd6471643ce2783df3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682617"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Tworzenie klastra rozwiązania Pivotal Cloud Foundry na platformie Azure

W tym samouczku opisano szybkie kroki dotyczące tworzenia i generowania parametrów potrzebnych do aprowizowania klastra rozwiązania Pivotal Cloud Foundry na platformie Azure. Aby znaleźć rozwiązanie Pivotal Cloud Foundry, wyszukaj je w witrynie Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Wyszukiwanie rozwiązania Pivotal Cloud Foundry na platformie Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Generowanie klucza publicznego SSH

Istnieje kilka sposobów generowania klucza publicznego SSH przy użyciu systemu Windows, Linux lub komputera Mac.

```Bash
ssh-keygen -t rsa -b 2048
```

Aby uzyskać więcej informacji, zobacz [Use SSH keys with Windows on Azure (Korzystanie z kluczy SSH w systemie Windows na platformie Azure)](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

> [!NOTE]
>
> Aby utworzyć jednostkę usługi, potrzebne są uprawnienia właściciela konta. Możesz też napisać skrypt w celu zautomatyzowania tworzenia jednostki usługi. Możesz na przykład użyć polecenia interfejsu wiersza polecenia platformy Azure [az ad sp create-for-rbac](/cli/azure/ad/sp).

1. Zaloguj się do swojego konta platformy Azure.

    ```azurecli
    az login
    ```

    ![Logowanie do interfejsu wiersza polecenia platformy Azure](media/deploy/az-login-output.png )
 
    Skopiuj wartość „id” jako swój **identyfikator subskrypcji** oraz wartość „tenantId”, której użyjesz później.

2. Ustaw subskrypcję domyślną dla tej konfiguracji.

    ```azurecli
    az account set -s {id}
    ```

3. Utworzenie aplikacji usługi Azure Active Directory dla rozwiązania Pivotal Cloud Foundry. Określ unikatowe hasło alfanumeryczne. Zapisz hasło jako element **clientSecret** do późniejszego użycia.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Skopiuj wartość „appId” w danych wyjściowych jako identyfikator **clientID** do późniejszego użycia.

    > [!NOTE]
    >
    > Wybierz własną stronę główną aplikacji i identyfikator URI identyfikatora, na przykład http \: //www \. contoso.com.

4. Utwórz jednostkę usługi przy użyciu nowego identyfikatora aplikacji.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Ustaw rolę uprawnień jednostki usługi jako Współautor.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    Możesz też użyć

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![przypisania roli Jednostka usługi.](media/deploy/svc-princ.png )

6. Upewnij się, że możesz pomyślnie zalogować się do jednostki usługi przy użyciu identyfikatora aplikacji, hasła oraz identyfikatora dzierżawy.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Utwórz plik JSON w następującym formacie. Użyj wcześniej skopiowanych wartości **subscriptionID**, **tenantID**, **clientID** i **clientSecret**. Zapisz plik.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pobieranie tokenu usługi Pivotal Network

1. Zarejestruj się lub zaloguj się na swoim koncie usługi [Pivotal Network](https://network.pivotal.io).
2. Wybierz nazwę profilu w prawym górnym rogu strony. Wybierz pozycję **Edit Profile** (Edytuj profil).
3. Przewiń do dołu strony i skopiuj wartość **LEGACY API TOKEN**. Jest to wartość **tokenu usługi Pivotal Network** do późniejszego użycia.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Aprowizowanie klastra rozwiązania Cloud Foundry na platformie Azure

Masz teraz wszystkie parametry potrzebne do aprowizowania [klastra rozwiązania Pivotal Clound Foundry na platformie Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Wprowadź parametry, a następnie utwórz klaster rozwiązania PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Weryfikowanie wdrożenia i logowanie się w usłudze Pivotal Ops Manager

1. W klastrze usługi PCF wyświetlany jest stan wdrożenia.

    ![Stan wdrożenia platformy Azure](media/deploy/deployment.png )

2. Wybierz link **Wdrożenia** w obszarze nawigacji po lewej stronie, aby uzyskać poświadczenia usługi PCF Ops Manager. Wybierz pozycję **Deployment Name** (Nazwa wdrożenia) na następnej stronie.
3. W obszarze nawigacji po lewej stronie kliknij link **Outputs** (Dane wyjściowe), aby wyświetlić adres URL, nazwę użytkownika i hasło dla usługi PCF Ops Manager. Wartość „OPSMAN FQDN” jest adresem URL.
 
    ![Dane wyjściowe wdrożenia usługi Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Przejdź do adresu URL w przeglądarce internetowej. Wprowadź poświadczenia z poprzedniego kroku, aby się zalogować.

    ![Strona logowania usługi Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Jeśli w przeglądarce Internet Explorer zostanie wyświetlony błąd mówiący, że witryna nie jest bezpieczna, wybierz pozycję **Więcej informacji** i przejdź do strony internetowej. W przeglądarce Firefox wybierz pozycję **Zaawansowane** i dodaj certyfikację, aby kontynuować.

5. W usłudze PCF Ops Manager zostaną wyświetlone wdrożone wystąpienia platformy Azure. Teraz w tym miejscu możesz wdrażać aplikacje i nimi zarządzać.
               
    ![Wdrożone wystąpienie platformy Azure w usłudze Pivotal](media/deploy/ops-mgr.png )
