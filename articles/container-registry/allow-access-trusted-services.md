---
title: Uzyskiwanie dostępu do rejestru z ograniczeniami sieci przy użyciu zaufanej usługi platformy Azure
description: Umożliwianie zaufanego wystąpienia usługi platformy Azure bezpiecznego dostępu do rejestru kontenerów ograniczonego przez sieć w celu ściągania lub wypychania obrazów
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785874"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Zezwalaj zaufanym usługom na bezpieczny dostęp do rejestru kontenerów z ograniczeniami sieci (wersja zapoznawcza)

Azure Container Registry umożliwia wybieranie zaufanych usług platformy Azure na dostęp do rejestru skonfigurowanego przy użyciu reguł dostępu do sieci. Gdy zaufane usługi są dozwolone, wystąpienie zaufanej usługi może bezpiecznie pominąć reguły sieci rejestru i wykonywać operacje, takie jak ściąganie lub wypychanie obrazów. Tożsamość zarządzana wystąpienia usługi jest używana na potrzeby dostępu i musi mieć przypisaną rolę platformy Azure oraz uwierzytelnić się w rejestrze.

Użyj polecenia Azure Cloud Shell lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady poleceń w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.18 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zezwalanie na dostęp do rejestru przez zaufane usługi platformy Azure jest funkcją w **wersji zapoznawczej.**

## <a name="limitations"></a>Ograniczenia

* Aby uzyskać dostęp do rejestru kontenerów [](#trusted-services) z ograniczeniami sieci, należy użyć przypisanej przez system tożsamości zarządzanej włączonej w zaufanej usłudze. Tożsamości zarządzane przypisane przez użytkownika nie są obecnie obsługiwane.
* Zezwalanie na zaufane usługi nie ma zastosowania do rejestru kontenerów skonfigurowanego za pomocą punktu [końcowego usługi](container-registry-vnet.md). Ta funkcja ma wpływ tylko na rejestry z ograniczeniami dotyczącymi prywatnego punktu końcowego [lub](container-registry-private-link.md) z [zastosowanymi regułami dostępu do publicznych adresów IP.](container-registry-access-selected-networks.md) 

## <a name="about-trusted-services"></a>Informacje o zaufanych usługach

Azure Container Registry ma warstwowy model zabezpieczeń obsługujący wiele konfiguracji sieci, które ograniczają dostęp do rejestru, w tym:

* [Prywatny punkt końcowy z Azure Private Link](container-registry-private-link.md). Po skonfigurowaniu prywatny punkt końcowy rejestru jest dostępny tylko dla zasobów w sieci wirtualnej przy użyciu prywatnych adresów IP.  
* [Reguły zapory rejestru](container-registry-access-selected-networks.md), które zezwalają na dostęp do publicznego punktu końcowego rejestru tylko z określonych publicznych adresów IP lub zakresów adresów. Możesz również skonfigurować zaporę tak, aby blokowała cały dostęp do publicznego punktu końcowego podczas korzystania z prywatnych punktów końcowych.

W przypadku wdrożenia w sieci wirtualnej lub skonfigurowania przy użyciu reguł zapory rejestr domyślnie odmawia dostępu użytkownikom lub usługom spoza tych źródeł. 

Kilka wielodostępnych usług platformy Azure działa z sieci, których nie można dodać do tych ustawień sieci rejestru, uniemożliwiając im ściąganie lub wypychanie obrazów do rejestru. Przez wyznaczenie niektórych wystąpień usługi jako "zaufanych", właściciel rejestru może zezwolić na wybranie zasobów platformy Azure w celu bezpiecznego obejścia ustawień sieci rejestru w celu ściągania lub wypychania obrazów. 

### <a name="trusted-services"></a>Zaufane usługi

Wystąpienia następujących usług mogą uzyskać dostęp do rejestru kontenerów z  ograniczeniami sieci, jeśli ustawienie zezwalaj na zaufane usługi w rejestrze jest włączone (ustawienie domyślne). Z czasem będzie dodawanych więcej usług.

|Zaufana usługa  |Obsługiwane scenariusze użycia  |
|---------|---------|
|Zadania usługi ACR     | [Uzyskiwanie dostępu do innego rejestru z zadania ACR](container-registry-tasks-cross-registry-authentication.md)       |
|Usługa Machine Learning | [Wdrażanie](../machine-learning/how-to-deploy-custom-docker-image.md) lub [trenowanie](../machine-learning/how-to-train-with-custom-image.md) modelu w Machine Learning roboczym przy użyciu niestandardowego obrazu kontenera platformy Docker |
|Azure Container Registry | [Importowanie obrazów z innego rejestru kontenerów platformy Azure](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Włączenie ustawienia Zezwalaj na zaufane usługi nie zezwala wystąpieniom innych zarządzanych usług platformy Azure, w tym App Service, Azure Container Instances i Azure Security Center na dostęp do rejestru kontenerów ograniczonego przez sieć.

## <a name="allow-trusted-services---cli"></a>Zezwalaj na zaufane usługi — interfejs wiersza polecenia

Domyślnie ustawienie Zezwalaj na zaufane usługi jest włączone w nowym rejestrze kontenerów platformy Azure. Wyłącz lub włącz ustawienie, uruchamiając [polecenie az acr update.](/cli/azure/acr#az_acr_update)

Aby wyłączyć:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Aby włączyć ustawienie w istniejącym rejestrze lub rejestrze, w którym jest ono już wyłączone:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Zezwalaj na zaufane usługi — portal

Domyślnie ustawienie Zezwalaj na zaufane usługi jest włączone w nowym rejestrze kontenerów platformy Azure. 

Aby wyłączyć lub ponownie włączyć ustawienie w portalu:

1. W portalu przejdź do rejestru kontenerów.
1. W **obszarze Ustawienia** wybierz pozycję **Sieć.** 
1. W **elektorze Zezwalaj na dostęp do sieci publicznej** wybierz pozycję Wybrane **sieci** lub **Wyłączone.**
1. Wykonaj jedną z następujących czynności:
    * Aby wyłączyć dostęp przez zaufane usługi, w obszarze Wyjątek **zapory** usuń zaznaczenie pola wyboru Zezwalaj zaufanym **usługi firmy Microsoft na dostęp do tego rejestru kontenerów.** 
    * Aby zezwolić na zaufane usługi, w obszarze **Wyjątek zapory** zaznacz pole wyboru Zezwalaj usługi firmy Microsoft **dostęp do tego rejestru kontenerów.**
1. Wybierz pozycję **Zapisz**.

## <a name="trusted-services-workflow"></a>Przepływ pracy zaufanych usług

Oto typowy przepływ pracy umożliwiający wystąpieniu zaufanej usługi dostęp do rejestru kontenerów z ograniczeniami sieci.

1. Włącz tożsamość zarządzaną przypisaną przez system [dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w wystąpieniu jednej z [zaufanych usług dla](#trusted-services) Azure Container Registry.
1. Przypisz tożsamości [rolę platformy Azure](container-registry-roles.md) do rejestru. Na przykład przypisz rolę ACRPull, aby ściągać obrazy kontenerów.
1. W rejestrze z ograniczeniami sieci skonfiguruj ustawienie zezwalania na dostęp za pomocą zaufanych usług.
1. Użyj poświadczeń tożsamości do uwierzytelniania w rejestrze z ograniczeniami sieci. 
1. Ściągnij obrazy z rejestru lub wykonaj inne operacje dozwolone przez rolę.

### <a name="example-acr-tasks"></a>Przykład: zadania usługi ACR

W poniższym przykładzie pokazano używanie zadania usługi ACR jako zaufanej usługi. Aby uzyskać szczegółowe informacje o zadaniu, zobacz [Cross-registry authentication in an ACR task using an Azure-managed identity](container-registry-tasks-cross-registry-authentication.md) (Uwierzytelnianie między rejestrami w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure).

1. Utwórz lub zaktualizuj rejestr kontenerów platformy Azure i [wypchnie przykładowy obraz podstawowy](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) do rejestru. Ten rejestr jest *rejestrem bazowym* dla tego scenariusza.
1. W drugim rejestrze kontenerów platformy Azure [zdefiniuj](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) [i](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) utwórz zadanie usługi ACR, aby ściągnąć obraz z rejestru podstawowego. Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zadania.
1. Przypisz tożsamości zadania [do roli platformy Azure, aby uzyskać dostęp do rejestru podstawowego.](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources) Możesz na przykład przypisać rolę AcrPull, która ma uprawnienia do ściągania obrazów.
1. [Dodaj poświadczenia tożsamości zarządzanej](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) do zadania.
1. Aby upewnić się, że zadanie pomija ograniczenia sieci, [wyłącz dostęp publiczny](container-registry-access-selected-networks.md#disable-public-network-access) w rejestrze bazowym.
1. Uruchom zadanie. Jeśli podstawowy rejestr i zadanie są prawidłowo skonfigurowane, zadanie jest uruchamiane pomyślnie, ponieważ rejestr podstawowy zezwala na dostęp.

Aby przetestować wyłączanie dostępu przez zaufane usługi:

1. W rejestrze bazowym wyłącz ustawienie , aby zezwolić na dostęp przez zaufane usługi.
1. Uruchom ponownie zadanie. W takim przypadku uruchomienie zadania nie powiedzie się, ponieważ rejestr podstawowy nie zezwala już na dostęp przez zadanie.

## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Azure Private Link dla rejestru kontenerów platformy Azure.](container-registry-private-link.md)
* Aby skonfigurować reguły zapory rejestru, zobacz [Konfigurowanie reguł sieci publicznych adresów IP.](container-registry-access-selected-networks.md)
