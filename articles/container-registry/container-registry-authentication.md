---
title: Opcje uwierzytelniania rejestru
description: Opcje uwierzytelniania dla prywatnego rejestru kontenerów platformy Azure, w tym logowanie przy użyciu tożsamości Azure Active Directory, korzystanie z jednostki usługi i korzystanie z opcjonalnych poświadczeń administratora.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 7ff55d569e2659262ce9f323e4db2ea7ed671d20
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784285"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Uwierzytelnianie za pomocą rejestru kontenerów platformy Azure

Istnieje kilka sposobów uwierzytelniania za pomocą rejestru kontenerów platformy Azure, z których każdy ma zastosowanie do co najmniej jednego scenariusza użycia rejestru.

Zalecane sposoby obejmują uwierzytelnianie w [](#individual-login-with-azure-ad)rejestrze bezpośrednio za pomocą indywidualnego logowania lub aplikacje i orkiestratorzy kontenerów mogą wykonywać nienadzorowane lub "bezgłowe" uwierzytelnianie przy użyciu jednostki usługi Azure Active Directory (Azure [AD).](#service-principal)

## <a name="authentication-options"></a>Opcje uwierzytelniania

W poniższej tabeli wymieniono dostępne metody uwierzytelniania i typowe scenariusze. Aby uzyskać szczegółowe informacje, zobacz powiązaną zawartość.

| Metoda                               | Jak się uwierzytelnić                                           | Scenariusze                                                            | Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)                             | Ograniczenia                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Indywidualna tożsamość usługi AD](#individual-login-with-azure-ad)                | `az acr login` w interfejsie wiersza polecenia platformy Azure                             | Interakcyjne wypychanie/ściąganie przez deweloperów, testerów                                    | Tak                              | Token usługi AD musi być odnawiany co 3 godziny     |
| [Jednostkę usługi AD](#service-principal)                  | `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure<br/><br/> Ustawienia logowania do rejestru w interfejsach API lub narzędziach<br/><br/> [Klucz tajny ściągania kubernetes](container-registry-auth-kubernetes.md)                                           | Nienadzorowane wypychanie z potoku ci/cd<br/><br/> Nienadzorowane ściąganie do platformy Azure lub usług zewnętrznych  | Tak                              | Domyślna wygaśnięcie hasła dodatku SP to 1 rok       |                                                           
| [Integracja z platformą AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Dołączanie rejestru podczas tworzenia lub aktualizowania klastra usługi AKS  | Nienadzorowane ściąganie do klastra usługi AKS                                                  | Nie, dostęp tylko do ściągania             | Dostępne tylko z klastrem usługi AKS            |
| [Tożsamość zarządzana dla zasobów platformy Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` w interfejsie wiersza polecenia platformy Azure                                       | Nienadzorowane wypychanie z potoku azure CI/CD<br/><br/> Nienadzorowane ściąganie do usług platformy Azure<br/><br/>   | Tak                              | Używaj tylko z wybranych usług platformy Azure, które [obsługują tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Administrator](#admin-account)                            | `docker login`                                          | Interakcyjne wypychanie/ściąganie przez indywidualnego dewelopera lub testera<br/><br/>Wdrażanie obrazu w portalu z rejestru do Azure App Service lub Azure Container Instances                      | Nie, zawsze ściągaj i wypychaj dostęp  | Jedno konto na rejestr, nie jest zalecane dla wielu użytkowników         |
| [Token dostępu o zakresie repozytorium](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` w interfejsie wiersza polecenia platformy Azure   | Interakcyjne wypychanie/ściąganie do repozytorium przez poszczególnych deweloperów lub testerów<br/><br/> Nienadzorowane wypychanie/ściąganie do repozytorium przez pojedynczy system lub urządzenie zewnętrzne                  | Tak                              | Obecnie nie są zintegrowane z tożsamością usługi AD  |

## <a name="individual-login-with-azure-ad"></a>Indywidualne logowanie za pomocą usługi Azure AD

Podczas bezpośredniej pracy z rejestrem, takiej jak ściąganie obrazów do i wypychanie obrazów ze stacji roboczej dewelopera do utworzonego rejestru, uwierzytelnianie przy użyciu indywidualnej tożsamości platformy Azure. Zaloguj się do interfejsu wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) za pomocą polecenia az [login,](/cli/azure/reference-index#az_login)a następnie uruchom [polecenie az acr login:](/cli/azure/acr#az_acr_login)

```azurecli
az login
az acr login --name <acrName>
```

Po zalogowaniu się za pomocą polecenia interfejs wiersza polecenia używa tokenu utworzonego podczas wykonywania w celu bezproblemowego uwierzytelnienia sesji `az acr login` `az login` w rejestrze. Aby ukończyć przepływ uwierzytelniania, interfejs wiersza polecenia platformy Docker i demon platformy Docker muszą być zainstalowane i uruchomione w środowisku. `az acr login` używa klienta platformy Docker do Azure Active Directory tokenu w `docker.config` pliku. Po zalogowaniu się w ten sposób poświadczenia są buforowane, a kolejne polecenia w sesji nie wymagają nazwy `docker` użytkownika ani hasła.

> [!TIP]
> Użyj także funkcji do uwierzytelniania poszczególnych tożsamości, gdy chcesz wypchnąć lub ściągnąć artefakty inne niż obrazy platformy Docker do rejestru, takie jak artefakty `az acr login` [OCI.](container-registry-oci-artifacts.md)  

W przypadku dostępu do rejestru token używany przez program jest ważny przez 3 godziny, dlatego zalecamy, aby zawsze logować się do rejestru przed `az acr login` uruchomieniem  `docker` polecenia. Jeśli token wygaśnie, możesz odświeżyć go ponownie przy użyciu `az acr login` polecenia w celu ponownego uwierzytelnienia. 

Korzystanie z tożsamości platformy Azure zapewnia kontrolę dostępu opartą na rolach `az acr login` [(RBAC) platformy Azure.](../role-based-access-control/role-assignments-portal.md) W niektórych scenariuszach możesz zalogować się do rejestru przy użyciu własnej indywidualnej tożsamości w usłudze Azure AD lub skonfigurować innych użytkowników platformy Azure z określonymi rolami i [uprawnieniami platformy Azure.](container-registry-roles.md) W przypadku scenariuszy między usługami lub w celu obsługi potrzeb grupy roboczej lub przepływu pracy tworzenia aplikacji, w przypadku których nie chcesz zarządzać indywidualnym dostępem, możesz również zalogować się przy użyciu tożsamości zarządzanej dla zasobów [platformy Azure.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

W niektórych przypadkach może być konieczne uwierzytelnienie za pomocą usługi , gdy demon platformy Docker nie `az acr login` jest uruchomiony w twoim środowisku. Na przykład może być konieczne uruchomienie skryptu w programie Azure Cloud Shell, który udostępnia interfejs wiersza polecenia platformy Docker, ale nie uruchamia `az acr login` demona platformy Docker.

W tym scenariuszu uruchom `az acr login` najpierw z `--expose-token` parametrem . Ta opcja uwidacznia token dostępu zamiast logowania się za pomocą interfejsu wiersza polecenia platformy Docker.

```azurecli
az acr login --name <acrName> --expose-token
```

W danych wyjściowych jest wyświetlany token dostępu w skrócie w tym miejscu:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
W przypadku uwierzytelniania rejestru zalecamy przechowywanie poświadczeń tokenu w bezpiecznej lokalizacji i stosowanie zalecanych rozwiązań w zakresie zarządzania poświadczeniami logowania platformy [Docker](https://docs.docker.com/engine/reference/commandline/login/). Na przykład przechowuj wartość tokenu w zmiennej środowiskowej:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Następnie uruchom program , przekazując jako nazwę użytkownika i `docker login` `00000000-0000-0000-0000-000000000000` używając tokenu dostępu jako hasła:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Jednostka usługi

Jeśli przypiszesz [jednostkę usługi](../active-directory/develop/app-objects-and-service-principals.md) do rejestru, aplikacja lub usługa może używać jej do uwierzytelniania bezgłowego. Jednostki usługi umożliwiają kontrolę dostępu opartą na rolach [(RBAC)](../role-based-access-control/role-assignments-portal.md) platformy Azure do rejestru i można przypisać wiele podmiotów zabezpieczeń usługi do rejestru. Wiele podmiotów zabezpieczeń usługi umożliwia definiowanie różnych dostępu dla różnych aplikacji.

Dostępne role dla rejestru kontenerów obejmują:

* **AcrPull:** ściąganie

* **AcrPush:** ściąganie i wypychanie

* **Właściciel:** ściąganie, wypychanie i przypisywanie ról do innych użytkowników

Aby uzyskać pełną listę ról, zobacz [Azure Container Registry ról i uprawnień.](container-registry-roles.md)

Aby uzyskać więcej wskazówek dotyczących skryptów interfejsu wiersza polecenia do tworzenia jednostki usługi do uwierzytelniania za pomocą rejestru kontenerów platformy Azure, zobacz Azure Container Registry uwierzytelnianie przy użyciu [jednostki usługi.](container-registry-auth-service-principal.md)

## <a name="admin-account"></a>Konto administratora

Każdy rejestr kontenerów zawiera konto użytkownika administratora, które jest domyślnie wyłączone. Możesz włączyć administratora i zarządzać jego poświadczeniami w witrynie Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure. Konto administratora ma pełne uprawnienia do rejestru.

Konto administratora jest obecnie wymagane w przypadku niektórych scenariuszy wdrażania obrazu z rejestru kontenerów do niektórych usług platformy Azure. Na przykład konto administratora jest potrzebne w przypadku użycia usługi Azure Portal do wdrożenia obrazu kontenera z rejestru bezpośrednio w usłudze [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) lub [usłudze Azure Web Apps for Containers.](container-registry-tutorial-deploy-app.md)

> [!IMPORTANT]
> Konto administratora jest przeznaczone dla jednego użytkownika w celu uzyskania dostępu do rejestru, głównie do celów testowych. Nie zalecamy udostępniania poświadczeń konta administratora wielu użytkownikom. Wszyscy użytkownicy uwierzytelniający się przy użyciu konta administratora są wyświetlani jako pojedynczy użytkownik z dostępem wypychania i ściągania do rejestru. Zmiana lub wyłączenie tego konta powoduje wyłączenie dostępu do rejestru dla wszystkich użytkowników, którzy używają jego poświadczeń. W przypadku scenariuszy bezgłowych zaleca się używanie indywidualnej tożsamości dla użytkowników i jednostki usługi.
>

Konto administratora ma dwa hasła, które można ponownie wygenerować. Dwa hasła umożliwiają zachowanie połączenia z rejestrem przy użyciu jednego hasła podczas ponownego generowania drugiego. Jeśli konto administratora jest włączone, możesz przekazać nazwę użytkownika i hasło do polecenia po wyświetleniu monitu o uwierzytelnienie `docker login` podstawowe w rejestrze. Na przykład:

```
docker login myregistry.azurecr.io 
```

Aby uzyskać zalecane rozwiązania dotyczące zarządzania poświadczeniami logowania, zobacz docker login command reference (Informacje o poleceniach logowania platformy [Docker).](https://docs.docker.com/engine/reference/commandline/login/)

Aby włączyć administratora dla istniejącego rejestru, możesz użyć parametru `--admin-enabled` [polecenia az acr update](/cli/azure/acr#az_acr_update) w interfejsie wiersza polecenia platformy Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Możesz włączyć administratora na stronie usługi Azure Portal, wybierając pozycję  Klucze dostępu w  obszarze **USTAWIENIA,** a następnie pozycję Włącz w obszarze **Administrator.**

![Włączanie interfejsu użytkownika administratora w Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Następne kroki

* [Wypychanie pierwszego obrazu przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
