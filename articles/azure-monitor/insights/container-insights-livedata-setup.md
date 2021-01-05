---
title: Skonfiguruj Azure Monitor dla kontenerów dane dynamiczne (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania widoku w czasie rzeczywistym dzienników kontenerów (stdout/stderr) i zdarzeń bez używania polecenia kubectl z Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: references_regions
ms.openlocfilehash: 62bc7613995296504dfba551cdb631ac3386aa75
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830789"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Jak skonfigurować funkcję Live Data (wersja zapoznawcza)

Aby wyświetlić dane na żywo (wersja zapoznawcza) przy użyciu Azure Monitor kontenerów z klastrów usługi Azure Kubernetes Service (AKS), należy skonfigurować uwierzytelnianie, aby udzielić uprawnień dostępu do danych Kubernetes. Ta konfiguracja zabezpieczeń umożliwia dostęp do danych w czasie rzeczywistym za pomocą interfejsu API Kubernetes bezpośrednio w Azure Portal.

Ta funkcja obsługuje następujące metody kontroli dostępu do dzienników, zdarzeń i metryk:

- AKS bez włączonej autoryzacji RBAC Kubernetes
- AKS włączone z autoryzacją RBAC Kubernetes
    - AKS skonfigurowany przy użyciu powiązania roli klastra **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0&preserve-view=true)**
- AKS włączone z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD)

Te instrukcje wymagają zarówno dostępu administracyjnego do klastra Kubernetes, jak i w przypadku konfigurowania do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników, dostępu administracyjnego do usługi Azure AD.

W tym artykule wyjaśniono, jak skonfigurować uwierzytelnianie, aby kontrolować dostęp do funkcji danych na żywo (wersja zapoznawcza) z klastra:

- Kubernetes z obsługą kontroli dostępu opartej na rolach (Kubernetes RBAC)
- Azure Active Directory zintegrowany klaster AKS.

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) są nieobsługiwane w przypadku tej funkcji. Ta funkcja wykorzystuje bezpośrednio dostęp do interfejsu API Kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieci w celu blokowania interfejsu API Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu.

## <a name="authentication-model"></a>Model uwierzytelniania

Funkcja dane dynamiczne (wersja zapoznawcza) korzysta z interfejsu API Kubernetes, identycznego z `kubectl` narzędziem wiersza polecenia. Punkty końcowe interfejsu API Kubernetes używają certyfikatu z podpisem własnym, którego przeglądarka nie będzie mogła zweryfikować. Ta funkcja wykorzystuje wewnętrzny serwer proxy do weryfikowania certyfikatu za pomocą usługi AKS, co zapewnia, że ruch jest zaufany.

Azure Portal poprosi o zweryfikowanie poświadczeń logowania do klastra Azure Active Directory i przekieruje użytkownika do konfiguracji rejestracji klienta podczas tworzenia klastra (i ponownie skonfigurowany w tym artykule). To zachowanie jest podobne do procesu uwierzytelniania wymaganego przez program `kubectl` .

>[!NOTE]
>Autoryzacja klastra jest zarządzana przez program Kubernetes oraz model zabezpieczeń, z którym jest on skonfigurowany. Użytkownicy, którzy uzyskują dostęp do tej funkcji, wymagają uprawnień do pobrania konfiguracji Kubernetes (*kubeconfig*), podobnie jak w przypadku uruchamiania programu `az aks get-credentials -n {your cluster name} -g {your resource group}` . Ten plik konfiguracji zawiera token autoryzacji i uwierzytelniania dla **roli użytkownika klastra usługi Azure Kubernetes Service**, w przypadku klastrów z obsługą kontroli RBAC platformy Azure i AKS bez włączenia autoryzacji Kubernetes RBAC. Zawiera informacje o rejestracji w usłudze Azure AD i klientach, gdy AKS jest włączona z logowaniem jednokrotnym opartym na protokole SAML Azure Active Directory (AD).

>[!IMPORTANT]
>Użytkownicy tych funkcji muszą mieć [rolę użytkownika klastra usługi Azure Kubernetes](../../role-based-access-control/built-in-roles.md) w celu pobrania `kubeconfig` i użycia tej funkcji. Użytkownicy **nie** wymagają dostępu współautora do klastra w celu korzystania z tej funkcji.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Korzystanie z clusterMonitoringUser z klastrami z obsługą RBAC Kubernetes

Aby wyeliminować konieczność stosowania dodatkowych zmian w konfiguracji, aby umożliwić roli użytkownika Kubernetes wiązanie **clusterUser** dostępu do funkcji dane dynamiczne (wersja zapoznawcza) po [włączeniu autoryzacji RBAC Kubernetes](#configure-kubernetes-rbac-authorization) , AKS dodał nowe powiązanie roli klastra Kubernetes o nazwie **clusterMonitoringUser**. To powiązanie roli klastra ma wszystkie niezbędne uprawnienia dostępne w celu uzyskania dostępu do interfejsu API Kubernetes oraz punktów końcowych służących do używania funkcji Live Data (wersja zapoznawcza).

Aby można było korzystać z funkcji danych na żywo (wersja zapoznawcza) z nowym użytkownikiem, musisz być członkiem roli [współautora](../../role-based-access-control/built-in-roles.md#contributor) [usługi Azure Kubernetes](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) lub współpracownika w zasobie klastra AKS. Azure Monitor dla kontenerów, gdy jest włączone, jest skonfigurowany do uwierzytelniania przy użyciu domyślnie clusterMonitoringUser. Jeśli w klastrze nie ma powiązania roli clusterMonitoringUser, w zamian jest używana wartość **clusterUser** w ramach uwierzytelniania. Współautor daje dostęp do clusterMonitoringUser (jeśli istnieje), a użytkownik klastra usługi Azure Kuberenetes Service zapewnia dostęp do clusterUser. Każda z tych dwóch ról zapewnia wystarczający dostęp do korzystania z tej funkcji.

AKS to nowe powiązanie roli w styczniu 2020, dlatego klastry utworzone przed 2020 stycznia nie są dostępne. Jeśli masz klaster, który został utworzony przed stycznia 2020, nowy **clusterMonitoringUser** można dodać do istniejącego klastra, WYKONUJĄC operację Put w klastrze lub wykonując inną operację w klastrze, która wykonuje operację Put w klastrze, na przykład aktualizując wersję klastra.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Klaster Kubernetes bez włączonej Kubernetes RBAC

Jeśli masz klaster Kubernetes, który nie jest skonfigurowany przy użyciu autoryzacji RBAC Kubernetes lub jest zintegrowany z logowaniem jednokrotnym usługi Azure AD, nie musisz wykonywać tych czynności. Wynika to z faktu, że uprawnienia administracyjne są domyślnie dostępne w konfiguracji bez kontroli RBAC.

## <a name="configure-kubernetes-rbac-authorization"></a>Konfigurowanie autoryzacji RBAC Kubernetes

Po włączeniu autoryzacji RBAC Kubernetes są wykorzystywane dwa użytkownicy: **clusterUser** i **clusterAdmin** , aby uzyskać dostęp do interfejsu API Kubernetes. Jest to podobne do uruchamiania `az aks get-credentials -n {cluster_name} -g {rg_name}` bez opcji administracyjnych. Oznacza to, że **clusterUser** musi mieć przyznany dostęp do punktów końcowych w interfejsie API Kubernetes.

W poniższych przykładowych krokach pokazano, jak skonfigurować powiązanie roli klastra z tego szablonu konfiguracji YAML.

1. Skopiuj i wklej plik YAML i Zapisz go jako LogReaderRBAC. YAML.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
       name: containerHealth-log-reader
    rules:
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"]
          resources:
             - "pods/log"
             - "events"
             - "nodes"
             - "pods"
             - "deployments"
             - "replicasets"
          verbs: ["get", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
       name: containerHealth-read-logs-global
    roleRef:
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io
    subjects:
    - kind: User
      name: clusterUser
      apiGroup: rbac.authorization.k8s.io
    ```

2. Aby zaktualizować konfigurację, uruchom następujące polecenie: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Jeśli w klastrze zastosowano poprzednią wersję `LogReaderRBAC.yaml` pliku, zaktualizuj ją, kopiując i wklejając nowy kod przedstawiony w kroku 1 powyżej, a następnie uruchom polecenie przedstawione w kroku 2, aby zastosować je do klastra.

## <a name="configure-ad-integrated-authentication"></a>Konfigurowanie uwierzytelniania zintegrowanego z usługą AD

Klaster AKS skonfigurowany do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników korzysta z poświadczeń logowania osoby, która uzyskuje dostęp do tej funkcji. W tej konfiguracji możesz zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD.

Należy zmienić rejestrację klienta usługi Azure AD, aby umożliwić Azure Portal przekierowywanie stron autoryzacji jako zaufanego adresu URL przekierowania. Użytkownicy z usługi Azure AD uzyskują dostęp bezpośrednio do tych samych punktów końcowych interfejsu API Kubernetes za pośrednictwem **ClusterRoles** i **ClusterRoleBindings**.

Aby uzyskać więcej informacji na temat zaawansowanej konfiguracji zabezpieczeń w programie Kubernetes, zapoznaj się z [dokumentacją Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

>[!NOTE]
>Jeśli tworzysz nowy klaster z obsługą RBAC Kubernetes, zobacz [integrowanie Azure Active Directory z usługą Azure Kubernetes](../../aks/azure-ad-integration-cli.md) i wykonaj kroki konfigurowania uwierzytelniania usługi Azure AD. Podczas wykonywania kroków w celu utworzenia aplikacji klienckiej w tej sekcji wyróżniane są dwa adresy URL przekierowania, które należy utworzyć dla Azure Monitor kontenery pasujące do tych określonych w kroku 3 poniżej.

### <a name="client-registration-reconfiguration"></a>Ponowna konfiguracja rejestracji klienta

1. Zlokalizuj rejestrację klienta dla klastra Kubernetes w usłudze Azure AD w obszarze **Azure Active Directory > rejestracje aplikacji** w Azure Portal.

2. Wybierz pozycję **uwierzytelnianie** w okienku po lewej stronie.

3. Dodaj dwa adresy URL przekierowania do tej listy jako typy aplikacji **sieci Web** . Pierwszą podstawową wartością adresu URL powinna być `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` , a druga podstawowa wartość adresu URL `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Jeśli używasz tej funkcji w Chinach platformy Azure, pierwszą podstawową wartością adresu URL powinna być, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a druga podstawowa wartość adresu URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Po zarejestrowaniu adresów URL przekierowania, w obszarze **niejawne przyznanie**, wybierz opcje tokeny **dostępu** i **tokeny identyfikatora** , a następnie Zapisz zmiany.

>[!NOTE]
>Konfigurowanie uwierzytelniania przy użyciu Azure Active Directory logowania jednokrotnego można wykonać tylko podczas początkowego wdrażania nowego klastra AKS. Nie można skonfigurować logowania jednokrotnego dla klastra AKS, który już został wdrożony.

>[!IMPORTANT]
>W przypadku zmiany konfiguracji usługi Azure AD na potrzeby uwierzytelniania użytkowników przy użyciu zaktualizowanego identyfikatora URI Wyczyść pamięć podręczną przeglądarki, aby upewnić się, że zaktualizowany token uwierzytelniania zostanie pobrany i zastosowany.

## <a name="grant-permission"></a>Udziel uprawnienia

Każde konto usługi Azure AD musi mieć uprawnienia do odpowiednich interfejsów API w programie Kubernetes, aby można było uzyskać dostęp do funkcji dane dynamiczne (wersja zapoznawcza). Kroki służące do przyznania konta Azure Active Directory są podobne do kroków opisanych w sekcji [KUBERNETES RBAC Authentication](#configure-kubernetes-rbac-authorization) . Przed zastosowaniem szablonu konfiguracji YAML do klastra Zastąp wartość **clusterUser** w obszarze **ClusterRoleBinding** wybranym użytkownikiem.

>[!IMPORTANT]
>Jeśli użytkownik, któremu przyznano Kubernetes RBAC, jest w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie elementu userPrincipalName. Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, zapytaj o i Użyj właściwości objectId.

Aby uzyskać dodatkową pomoc dotyczącą konfigurowania klastra AKS **ClusterRoleBinding**, zobacz [Create Kubernetes RBAC Binding](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu uwierzytelniania można wyświetlić [metryki](container-insights-livedata-metrics.md), [wdrożenia](container-insights-livedata-deployments.md)oraz [zdarzenia i dzienniki](container-insights-livedata-overview.md) w czasie rzeczywistym z klastra.
