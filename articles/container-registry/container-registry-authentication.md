---
title: Opcje uwierzytelniania rejestru
description: Opcje uwierzytelniania dla prywatnego rejestru kontenerów platformy Azure, w tym logowanie za pomocą tożsamości Azure Active Directory, korzystanie z jednostek usługi i używanie opcjonalnych poświadczeń administratora.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712041"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Uwierzytelnianie przy użyciu usługi Azure Container Registry

Istnieje kilka sposobów uwierzytelniania przy użyciu usługi Azure Container Registry, z których każdy ma zastosowanie do co najmniej jednego scenariusza użycia rejestru.

Zalecane sposoby: uwierzytelnianie do rejestru bezpośrednio za pośrednictwem [pojedynczej nazwy logowania](#individual-login-with-azure-ad)lub aplikacje oraz koordynatorów kontenerów mogą wykonywać nienadzorowane lub bezobsługowe uwierzytelnianie przy użyciu [nazwy głównej usługi](#service-principal)Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Opcje uwierzytelniania

W poniższej tabeli wymieniono dostępne metody uwierzytelniania i zalecane scenariusze. Aby uzyskać szczegółowe informacje, zobacz połączoną zawartość.

| Metoda                               | Jak uwierzytelniać                                           | Scenariusze                                                            | Kontrola dostępu oparta na rolach                             | Ograniczenia                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Indywidualna tożsamość usługi AD](#individual-login-with-azure-ad)                | `az acr login` w interfejsie wiersza polecenia platformy Azure                             | Interaktywny Wypychanie/Ściąganie przez deweloperów, testerów                                    | Tak                              | Token usługi AD musi być odnawiany co 3 godziny     |
| [Nazwa główna usługi AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`w interfejsie wiersza polecenia platformy Azure<br/><br/> Ustawienia logowania do rejestru w interfejsach API lub narzędziach<br/><br/> [Kubernetes klucza tajnego](container-registry-auth-kubernetes.md)                                           | Nienadzorowane wypychanie z potoku ciągłej integracji/ciągłego wdrażania<br/><br/> Nienadzorowane ściąganie do platformy Azure lub usług zewnętrznych  | Tak                              | Domyślne wygaśnięcie hasła SP to 1 rok       |                                                           
| [Integracja z usługą AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Dołącz rejestr podczas tworzenia lub aktualizowania klastra AKS  | Nienadzorowany ściąganie do klastra AKS                                                  | Nie, tylko dostęp do ściągania             | Dostępne tylko z klastrem AKS            |
| [Zarządzana tożsamość zasobów platformy Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` w interfejsie wiersza polecenia platformy Azure                                       | Nienadzorowane wypychanie z potoku ciągłej integracji/ciągłego wdrażania platformy Azure<br/><br/> Nienadzorowane ściąganie do usług platformy Azure<br/><br/>   | Tak                              | Używaj tylko z usług platformy Azure, które [obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Administrator](#admin-account)                            | `docker login`                                          | Interaktywny Wypychanie/Ściąganie przez indywidualnych deweloperów lub testerów                           | Nie, zawsze Uzyskuj dostęp do ściągania i wypychania  | Pojedyncze konto na rejestr, niezalecane dla wielu użytkowników         |
| [Token dostępu do zakresu repozytorium](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`w interfejsie wiersza polecenia platformy Azure   | Interaktywny Wypychanie/Ściąganie do repozytorium przez indywidualnego dewelopera lub testera<br/><br/> Nienadzorowane Wypychanie/Ściąganie do repozytorium przez poszczególne systemy lub urządzenia zewnętrzne                  | Tak                              | Obecnie nie zintegrowano z tożsamością usługi AD  |

## <a name="individual-login-with-azure-ad"></a>Indywidualne logowanie za pomocą usługi Azure AD

W przypadku bezpośredniej pracy z rejestrem, na przykład ściągania obrazów do i wypychania obrazów z stacji roboczej deweloperskiej, należy uwierzytelnić się za pomocą polecenia [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) w [interfejsie wiersza poleceń platformy Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Gdy logujesz się za pomocą `az acr login` programu, interfejs wiersza polecenia używa tokenu utworzonego, gdy wykonasz [AZ login](/cli/azure/reference-index#az-login) , aby bezproblemowo uwierzytelniać sesję z rejestrem. Aby ukończyć przepływ uwierzytelniania, platforma Docker musi być zainstalowana i uruchomiona w danym środowisku. `az acr login`używa klienta platformy Docker, aby ustawić token Azure Active Directory w `docker.config` pliku. Po zalogowaniu się w ten sposób poświadczenia są buforowane, a kolejne `docker` polecenia w sesji nie wymagają nazwy użytkownika ani hasła.

> [!TIP]
> Należy również użyć `az acr login` do uwierzytelniania pojedynczej tożsamości, gdy chcesz wypchnąć lub ściągnąć artefakty inne niż obrazy Docker do rejestru, takie jak [artefakty OCI](container-registry-oci-artifacts.md).  


W przypadku dostępu do rejestru token używany przez program `az acr login` jest ważny przez **3 godziny**, dlatego zalecamy, aby zawsze logować się do rejestru przed uruchomieniem `docker` polecenia. Jeśli token wygaśnie, możesz go odświeżyć za pomocą `az acr login` polecenia ponownie, aby przeprowadzić ponowną próbę uwierzytelnienia. 

Używanie `az acr login` z tożsamościami platformy Azure zapewnia [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md). W przypadku niektórych scenariuszy możesz zalogować się do rejestru przy użyciu indywidualnej tożsamości w usłudze Azure AD. W przypadku scenariuszy obejmujących wiele usług lub do obsługi potrzeb grupy roboczej lub przepływu pracy programistycznej, w której nie chcesz zarządzać indywidualnym dostępem, możesz także zalogować się przy użyciu [tożsamości zarządzanej dla zasobów platformy Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Jednostka usługi

Jeśli przypiszesz jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, aplikacja lub usługa może jej używać do bezobsługowego uwierzytelniania. Jednostki usług umożliwiają [dostęp oparty na rolach](../role-based-access-control/role-assignments-portal.md) do rejestru i można przypisać wiele jednostek usługi do rejestru. Wiele jednostek usługi pozwala definiować różne prawa dostępu dla różnych aplikacji.

Dostępne role dla rejestru kontenerów obejmują:

* **AcrPull**: ściąganie

* **AcrPush**: ściąganie i wypychanie

* **Właściciel**: ściąganie, wypychanie i przypisywanie ról innym użytkownikom

Aby uzyskać pełną listę ról, zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md).

Aby skrypty interfejsu wiersza polecenia utworzyły jednostkę usługi do uwierzytelniania przy użyciu usługi Azure Container Registry i uzyskać więcej wskazówek, zobacz [Azure Container Registry Authentication with Service Principals](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Konto administratora

Każdy rejestr kontenerów zawiera konto użytkownika administratora, które jest domyślnie wyłączone. Możesz włączyć administratora i zarządzać jego poświadczeniami w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure.

> [!IMPORTANT]
> Konto administratora jest przeznaczone dla jednego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. Nie zaleca się udostępniania poświadczeń konta administratora między wieloma użytkownikami. Wszyscy użytkownicy uwierzytelniani przy użyciu konta administratora są wyświetlani jako pojedynczy użytkownik z dostępem wypychanym i ściąganiem do rejestru. Zmiana lub wyłączenie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników korzystających z jego poświadczeń. Indywidualna tożsamość jest zalecana dla użytkowników i jednostek usługi dla scenariuszy bezobsługowych.
>

Konto administratora jest dostarczane z dwoma hasłami, które mogą być ponownie generowane. Dwa hasła umożliwiają zachowanie połączenia z rejestrem przy użyciu jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, można przekazać nazwę użytkownika i hasło do `docker login` polecenia po wyświetleniu monitu o uwierzytelnienie podstawowe w rejestrze. Przykład:

```
docker login myregistry.azurecr.io 
```

Najlepsze rozwiązania dotyczące zarządzania poświadczeniami logowania można znaleźć w dokumentacji polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Aby umożliwić administratorowi istniejący rejestr, można użyć `--admin-enabled` parametru [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) w interfejsie wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Możesz włączyć administratora w Azure Portal, przechodząc do rejestru, wybierając pozycję **klucze dostępu** w obszarze **Ustawienia**, a następnie pozycję **Włącz** w obszarze **administrator**.

![Włącz interfejs użytkownika administratora w Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Następne kroki

* [Wypchnij swój pierwszy obraz przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
