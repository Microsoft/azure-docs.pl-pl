---
title: Konfigurowanie środowiska deweloperskiego usługi Azure Red Hat OpenShift
description: Poniżej przedstawiono wymagania wstępne dotyczące pracy z Microsoft Azure Red Hat OpenShift.
keywords: Konfiguracja instalatora Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: b468f967e68b72e3c9da276dc2077fc09256c895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470038"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Konfigurowanie środowiska deweloperskiego usługi Azure Red Hat OpenShift

Aby kompilować i uruchamiać Microsoft Azure aplikacje Red Hat OpenShift, należy wykonać następujące działania:

* Zainstaluj wersję 2.0.65 (lub nowszą) interfejsu wiersza polecenia platformy Azure (lub użyj Azure Cloud Shell).
* Zarejestruj się, aby uzyskać dostęp do `AROGA` funkcji i powiązanych dostawców zasobów.
* Utwórz dzierżawę usługi Azure Active Directory (Azure AD).
* Utwórz obiekt aplikacji usługi Azure AD.
* Utwórz użytkownika usługi Azure AD.

Poniższe instrukcje przeprowadzą Cię przez wszystkie te wymagania wstępne.

## <a name="install-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Na platformie Azure Red Hat OpenShift jest wymagany system 2.0.65 lub nowszy interfejsu wiersza polecenia platformy Azure. Jeśli zainstalowano już interfejs wiersza polecenia platformy Azure, możesz sprawdzić, która wersja została zainstalowana, uruchamiając polecenie:

```azurecli
az --version
```

Pierwszy wiersz danych wyjściowych będzie miał wersję interfejsu wiersza polecenia, na przykład `azure-cli (2.0.65)` .

Poniżej znajdują się instrukcje dotyczące [instalowania interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli?view=azure-cli-latest) Jeśli wymagana jest nowa instalacja lub uaktualnienie.

Alternatywnie można użyć [Azure Cloud Shell](../cloud-shell/overview.md). Gdy korzystasz z Azure Cloud Shell, pamiętaj, aby wybrać środowisko **bash** , jeśli planujesz postępować zgodnie z serią samouczków [Tworzenie klastra Red Hat OpenShift dla platformy Azure](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Zarejestruj dostawców i funkcje

`Microsoft.ContainerService AROGA` `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` Przed wdrożeniem pierwszego klastra usługi Azure Red Hat OpenShift należy ręcznie zarejestrować funkcję,,, i dostawców.

Aby ręcznie zarejestrować tych dostawców i funkcji, należy skorzystać z poniższych instrukcji z poziomu powłoki bash, jeśli zainstalowano interfejs wiersza polecenia lub z sesji Azure Cloud Shell (bash) w Azure Portal:

1. Jeśli masz wiele subskrypcji platformy Azure, określ odpowiedni identyfikator subskrypcji:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Zarejestruj funkcję Microsoft. ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Zarejestruj dostawcę Microsoft. Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Zarejestruj dostawcę Microsoft. COMPUTE:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Zarejestruj dostawcę Microsoft. Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Zarejestruj dostawcę Microsoft. Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Zarejestruj dostawcę magazynu Microsoft. kluczy:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Odświeżanie rejestracji dostawcy zasobów Microsoft. ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure Active Directory (Azure AD)

Usługa Azure Red Hat OpenShift wymaga skojarzonej dzierżawy usługi Azure Active Directory (Azure AD), która reprezentuje organizację i jej relacje z firmą Microsoft. Dzierżawa usługi Azure AD umożliwia rejestrowanie i kompilowanie aplikacji oraz zarządzanie nimi, a także korzystanie z innych usług platformy Azure.

Jeśli nie masz usługi Azure AD, która ma być używana jako dzierżawca dla klastra Red Hat OpenShift platformy Azure, lub chcesz utworzyć dzierżawcę do testowania, postępuj zgodnie z instrukcjami w temacie [Tworzenie dzierżawy usługi Azure AD dla klastra Red Hat OpenShift](howto-create-tenant.md) w celu kontynuowania tego przewodnika.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Tworzenie użytkownika usługi Azure AD, grupy zabezpieczeń i obiektu aplikacji

W systemie Azure Red Hat OpenShift są wymagane uprawnienia do wykonywania zadań w klastrze, na przykład konfigurowania magazynu. Te uprawnienia są reprezentowane za pomocą [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). Należy również utworzyć nowego użytkownika Active Directory na potrzeby testowania aplikacji uruchamianych w klastrze Red Hat OpenShift platformy Azure.

Postępuj zgodnie z instrukcjami w temacie [Tworzenie obiektu i użytkownika aplikacji usługi Azure AD](howto-aad-app-configuration.md) , aby utworzyć nazwę główną usługi, wygeneruj klucz tajny klienta i adres URL wywołania zwrotnego uwierzytelniania dla aplikacji, a następnie utwórz nową grupę zabezpieczeń usługi Azure AD i użytkownika w celu uzyskania dostępu do klastra.

## <a name="next-steps"></a>Następne kroki

Teraz możesz przystąpić do korzystania z usługi Azure Red Hat OpenShift!

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli