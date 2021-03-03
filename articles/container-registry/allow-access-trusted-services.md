---
title: Dostęp do rejestru z ograniczeniami do sieci przy użyciu zaufanej usługi platformy Azure
description: Włącz zaufane wystąpienie usługi platformy Azure, aby bezpiecznie uzyskać dostęp do rejestru kontenerów z ograniczeniami do sieci w celu ściągania lub wypychania obrazów
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 3cc32630ea689891e7ba75163c33bc499a38becd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716486"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Zezwalaj zaufanym usługom na bezpieczny dostęp do rejestru kontenerów z ograniczeniami w sieci (wersja zapoznawcza)

Azure Container Registry umożliwia wybieranie zaufanych usług platformy Azure w celu uzyskania dostępu do rejestru, który został skonfigurowany za pomocą reguł dostępu do sieci. Gdy usługi zaufane są dozwolone, zaufane wystąpienie usługi może bezpiecznie ominąć reguły sieci w rejestrze i wykonać operacje, takie jak obrazy ściągające lub wypychane. Zarządzana tożsamość wystąpienia usługi jest używana w celu uzyskania dostępu i musi mieć przypisaną rolę platformy Azure i uwierzytelniać się w rejestrze.

Użyj Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady poleceń w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2,18 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Zezwalanie na dostęp do rejestru za pomocą zaufanych usług platformy Azure jest funkcją w **wersji zapoznawczej** .

## <a name="limitations"></a>Ograniczenia

* Aby uzyskać dostęp do rejestru kontenerów z ograniczeniami do sieci, należy użyć zarządzanej tożsamości przypisanej do systemu w [usłudze zaufanej](#trusted-services) . Tożsamości zarządzane przypisane przez użytkownika nie są obecnie obsługiwane.
* Zezwalanie na zaufane usługi nie ma zastosowania do rejestru kontenerów skonfigurowanego za pomocą [punktu końcowego usługi](container-registry-vnet.md). Ta funkcja ma wpływ tylko na rejestry, które są ograniczone do [prywatnego punktu końcowego](container-registry-private-link.md) lub mają zastosowane [reguły dostępu do publicznych adresów IP](container-registry-access-selected-networks.md) . 

## <a name="about-trusted-services"></a>Informacje o zaufanych usługach

Azure Container Registry ma warstwowy model zabezpieczeń obsługujący wiele konfiguracji sieci, które ograniczają dostęp do rejestru, w tym:

* [Prywatny punkt końcowy z linkiem prywatnym platformy Azure](container-registry-private-link.md). Po skonfigurowaniu prywatny punkt końcowy rejestru jest dostępny tylko dla zasobów w sieci wirtualnej, przy użyciu prywatnych adresów IP.  
* [Reguły zapory rejestru](container-registry-access-selected-networks.md), które umożliwiają dostęp do publicznego punktu końcowego rejestru tylko z określonych publicznych adresów IP lub zakresów adresów. Możesz również skonfigurować zaporę tak, aby blokowała dostęp do publicznego punktu końcowego w przypadku korzystania z prywatnych punktów końcowych.

W przypadku wdrożenia w sieci wirtualnej lub skonfigurowaniu reguł zapory rejestr odmówi dostępu domyślnie użytkownikom lub usługom spoza tych źródeł. 

Kilka wielodostępnych usług platformy Azure działa z sieci, których nie można uwzględnić w tych ustawieniach sieci rejestru, uniemożliwiając im ściąganie lub wypychanie obrazów do rejestru. Oznaczając pewne wystąpienia usługi jako "zaufany", właściciel rejestru może zezwolić na wybieranie zasobów platformy Azure w celu bezpiecznego obejścia ustawień sieciowych w rejestrze w celu ściągania lub wypychania obrazów. 

### <a name="trusted-services"></a>Usługi zaufane

Wystąpienia następujących usług mogą uzyskać dostęp do rejestru kontenerów z ograniczeniami w sieci, jeśli ustawienie **Zezwalaj na zaufane usługi** jest włączone (wartość domyślna). Więcej usług zostanie dodanych z upływem czasu.

|Usługa zaufana  |Obsługiwane scenariusze użycia  |
|---------|---------|
|Zadania usługi ACR     | [Uzyskiwanie dostępu do innego rejestru z zadania ACR](container-registry-tasks-cross-registry-authentication.md)       |
|Azure Container Registry | [Importuj obrazy z innego rejestru kontenerów platformy Azure](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Curently, włączenie ustawienia Zezwalaj na zaufane usługi nie zezwala na wystąpienia innych zarządzanych usług platformy Azure, w tym App Service, Azure Container Instances i Azure Security Center w celu uzyskania dostępu do rejestru kontenerów z ograniczeniami sieci.

## <a name="allow-trusted-services---cli"></a>Zezwalaj na zaufane usługi — interfejs wiersza polecenia

Domyślnie ustawienie Zezwalaj na zaufane usługi jest włączone w nowym rejestrze kontenerów platformy Azure. Wyłączenie lub włączenie ustawienia przez uruchomienie polecenia [AZ ACR Update](/cli/azure/acr#az-acr-update) .

Aby wyłączyć:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Aby włączyć ustawienie w istniejącym rejestrze lub rejestrze, w którym jest już wyłączone:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Zezwalaj na zaufane usługi — Portal

Domyślnie ustawienie Zezwalaj na zaufane usługi jest włączone w nowym rejestrze kontenerów platformy Azure. 

Aby wyłączyć lub ponownie włączyć ustawienie w portalu:

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Ustawienia** wybierz pozycję **Sieć**. 
1. W obszarze **Zezwalaj na dostęp do sieci publicznej** wybierz opcję **wybrane sieci** lub **wyłączone**.
1. Wykonaj jedną z następujących czynności:
    * Aby wyłączyć dostęp przez usługi zaufane, w obszarze **wyjątek zapory** Usuń zaznaczenie pola wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego rejestru kontenerów**. 
    * Aby zezwolić na używanie zaufanych usług, w obszarze **wyjątek zapory** zaznacz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego rejestru kontenerów**.
1. Wybierz pozycję **Zapisz**.

## <a name="trusted-services-workflow"></a>Przepływ pracy zaufanych usług

Poniżej przedstawiono typowy przepływ pracy umożliwiający wystąpieniu zaufanej usługi dostęp do rejestru kontenerów z ograniczeniami w sieci.

1. Włącz [tożsamość zarządzaną przez system dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w wystąpieniu jednego z [zaufanych usług](#trusted-services) dla Azure Container Registry.
1. Przypisz tożsamość [rolę platformy Azure](container-registry-roles.md) do rejestru. Na przykład Przypisz rolę ACRPull do ściągania obrazów kontenerów.
1. W rejestrze z ograniczeniami w sieci Skonfiguruj ustawienie zezwalające na dostęp za pomocą zaufanych usług.
1. Uwierzytelnianie za pomocą rejestru z ograniczeniami sieci przy użyciu poświadczeń tożsamości. 
1. Ściąganie obrazów z rejestru lub wykonywanie innych operacji dozwolonych przez rolę.

### <a name="example-acr-tasks"></a>Przykład: ACR zadania

Poniższy przykład demonstruje użycie zadań ACR jako usługi zaufanej. Aby uzyskać szczegółowe informacje o zadaniu [, zobacz Uwierzytelnianie między rejestrami w ACR zadania przy użyciu tożsamości zarządzanej przez platformę Azure](container-registry-tasks-cross-registry-authentication.md) .

1. Utwórz lub zaktualizuj usługę Azure Container Registry i [wypchnij przykładowy obraz podstawowy](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) do rejestru. Ten rejestr jest *rejestrem podstawowym* dla scenariusza.
1. W drugim rejestrze kontenera platformy Azure [Zdefiniuj](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) i [Utwórz](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) zadanie ACR, aby ściągnąć obraz z rejestru podstawowego. Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zadania.
1. Przypisz zadanie tożsamość [roli platformy Azure, aby uzyskać dostęp do rejestru podstawowego](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Na przykład Przypisz rolę AcrPull, która ma uprawnienia do ściągania obrazów.
1. [Dodaj do zadania poświadczenia tożsamości zarządzanej](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) .
1. Aby upewnić się, że zadanie pomija ograniczenia sieci, [Wyłącz dostęp publiczny](container-registry-access-selected-networks.md#disable-public-network-access) w rejestrze podstawowym.
1. Uruchom zadanie. Jeśli rejestr podstawowy i zadanie są prawidłowo skonfigurowane, zadanie zostanie wykonane pomyślnie, ponieważ rejestr podstawowy zezwala na dostęp.

Aby przetestować wyłączanie dostępu przez zaufane usługi:

1. W rejestrze podstawowym wyłącz ustawienie, aby zezwolić na dostęp za pomocą zaufanych usług.
1. Uruchom ponownie zadanie. W takim przypadku uruchomienie zadania nie powiedzie się, ponieważ rejestr podstawowy nie zezwala już na dostęp przez zadanie.

## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Konfigurowanie prywatnego linku platformy Azure dla usługi Azure Container Registry](container-registry-private-link.md).
* Aby skonfigurować reguły zapory rejestru, zobacz [Konfigurowanie reguł sieci publicznego adresu IP](container-registry-access-selected-networks.md).
