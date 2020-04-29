---
title: Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service
description: Jak utworzyć klastry usługi Azure Kubernetes Service (AKS) z włączoną obsługą Azure Active Directory
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617593"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrowanie usługi Azure Active Directory z usługą Azure Kubernetes Service

Usługę Azure Kubernetes Service (AKS) można skonfigurować do korzystania z Azure Active Directory (Azure AD) do uwierzytelniania użytkowników. W tej konfiguracji możesz zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD.

Administratorzy klastra mogą konfigurować kontrolę dostępu opartą na rolach (RBAC) Kubernetes na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów.

W tym artykule wyjaśniono, jak:

- Wdróż wymagania wstępne dla usług AKS i Azure AD.
- Wdróż klaster z obsługą usługi Azure AD.
- Utwórz podstawową rolę RBAC w klastrze AKS przy użyciu Azure Portal.

Te kroki można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure][azure-ad-cli].

> [!NOTE]
> Usługę Azure AD można włączyć tylko podczas tworzenia nowego klastra z obsługą kontroli RBAC. Nie można włączyć usługi Azure AD w istniejącym klastrze AKS.

## <a name="authentication-details"></a>Szczegóły uwierzytelniania

Uwierzytelnianie usługi Azure AD jest dostarczane do klastrów AKS, które mają OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0.

Aby uzyskać więcej informacji na temat OpenID Connect Connect, zobacz temat [Autoryzuj dostęp do aplikacji sieci Web za pomocą OpenID Connect Connect i Azure AD][open-id-connect].

W klastrze Kubernetes uwierzytelnianie tokenu elementu webhook służy do uwierzytelniania tokenów. Uwierzytelnianie za pomocą tokenu elementu webhook jest konfigurowane i zarządzane w ramach klastra AKS.

Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu webhook, zobacz sekcję [uwierzytelnianie tokenu elementu webhook][kubernetes-webhook] w dokumentacji Kubernetes.

Aby zapewnić uwierzytelnianie usługi Azure AD dla klastra AKS, tworzone są dwie aplikacje usługi Azure AD. Pierwsza aplikacja to składnik serwera, który zapewnia uwierzytelnianie użytkownika. Druga aplikacja jest składnikiem klienta, który jest używany po wyświetleniu monitu interfejsu wiersza polecenia w celu uwierzytelnienia. Ta aplikacja kliencka używa aplikacji serwera do rzeczywistego uwierzytelniania poświadczeń dostarczonych przez klienta.

> [!NOTE]
> Podczas konfigurowania uwierzytelniania usługi Azure AD for AKS są konfigurowane dwie aplikacje usługi Azure AD. Kroki delegowania uprawnień dla każdej aplikacji muszą zostać wykonane przez administratora dzierżawy platformy Azure.

## <a name="create-the-server-application"></a>Tworzenie aplikacji serwera

Pierwsza aplikacja usługi Azure AD jest stosowana w celu uzyskania członkostwa w grupie usługi Azure AD użytkownika. Aby utworzyć tę aplikację w Azure Portal:

1. Wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **nową rejestrację**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADServer*.

    b. W przypadku **obsługiwanych typów kont**wybierz opcję **konta tylko w tym katalogu organizacji**.
    
    c. Wybierz pozycję **Sieć Web** dla opcji Typ URI przekierowania, a następnie wprowadź dowolną wartość w formacie URI *https://aksazureadserver*, na przykład.

    d. Po zakończeniu wybierz pozycję **zarejestruj** .

2. Wybierz pozycję **manifest**, a następnie Edytuj wartość **groupMembershipClaims:** Value jako **All**. Po zakończeniu aktualizacji wybierz pozycję **Zapisz**.

    ![Aktualizuj członkostwo grupy do wszystkich](media/aad-integration/edit-manifest.png)

3. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **certyfikaty & wpisy tajne**.

    a. Wybierz pozycję **+ nowy klucz tajny klienta**.

    b. Dodaj Opis klucza, taki jak *AKS serwera Azure AD*. Wybierz czas wygaśnięcia, a następnie wybierz pozycję **Dodaj**.

    c. Zanotuj wartość klucza, która jest wyświetlana tylko w tym momencie. W przypadku wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest określana jako klucz tajny aplikacji serwera.

4. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **uprawnienia interfejsu API**, a następnie wybierz pozycję **+ Dodaj uprawnienie**.

    a. W obszarze **interfejsy API firmy Microsoft**wybierz pozycję **Microsoft Graph**.

    b. Wybierz pozycję **uprawnienia delegowane**, a następnie zaznacz pole wyboru obok pozycji **katalog > katalog. odczyt. wszystkie (odczyt danych katalogu)**.

    c. Jeśli domyślne delegowane uprawnienie dla **użytkownika > użytkownika. Odczytaj (Zaloguj się i odczytaj profil użytkownika)** nie istnieje, zaznacz pole wyboru obok niego.

    d. Wybierz pozycję **uprawnienia aplikacji**, a następnie zaznacz pole wyboru obok pozycji **katalog > katalog. odczyt. wszystkie (odczyt danych katalogu)**.

    ![Ustaw uprawnienia wykresu](media/aad-integration/graph-permissions.png)

    e. Wybierz pozycję **Dodaj uprawnienia** , aby zapisać aktualizacje.

    f. W obszarze **wyrażanie zgody**wybierz pozycję **Udziel zgody administratora**. Ten przycisk nie jest dostępny. bieżące używane konto nie jest wyświetlane jako Administrator dzierżawy.

    Po pomyślnym udzieleniu uprawnień w portalu zostanie wyświetlone następujące powiadomienie:

   ![Powiadomienie o pomyślnym przyznanych uprawnieniach](media/aad-integration/permissions-granted.png)

5. W lewym okienku aplikacji usługi Azure AD wybierz opcję **Uwidocznij interfejs API**, a następnie wybierz pozycję **+ Dodaj zakres**.
    
    a. Wprowadź **nazwę zakresu**, **nazwę wyświetlaną zgody administratora**, a następnie **Opis zgody administratora** , taki jak *AKSAzureADServer*.

    b. Upewnij się, że **stan** jest ustawiony na **włączone**.

    ![Uwidacznianie aplikacji serwerowej jako interfejsu API do użycia z innymi usługami](media/aad-integration/expose-api.png)

    c. Wybierz pozycję **Dodaj zakres**.

6. Wróć do strony **przeglądu** aplikacji i zanotuj **Identyfikator aplikacji (klienta)**. W przypadku wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest określana jako identyfikator aplikacji serwera.

    ![Pobierz identyfikator aplikacji](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Tworzenie aplikacji klienckiej

Druga aplikacja usługi Azure AD jest używana po zalogowaniu się przy użyciu interfejsu wiersza polecenia Kubernetes (polecenia kubectl).

1. Wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **nową rejestrację**.

    a. Nadaj aplikacji nazwę, taką jak *AKSAzureADClient*.

    b. W przypadku **obsługiwanych typów kont**wybierz opcję **konta tylko w tym katalogu organizacji**.

    c. W polu Typ URI przekierowania wybierz pozycję **Sieć Web** , a następnie wprowadź dowolną wartość w formacie *https://aksazureadclient*URI, taką jak.

    >[!NOTE]
    >Jeśli tworzysz nowy klaster z obsługą kontroli dostępu RBAC do obsługi Azure Monitor kontenerów, Dodaj następujące dwa dodatkowe adresy URL przekierowania do tej listy jako typy aplikacji **sieci Web** . Pierwszą podstawową wartością adresu URL powinna być `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` , a druga podstawowa wartość adresu URL powinna być`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Jeśli używasz tej funkcji w Chinach platformy Azure, pierwszą podstawową wartością adresu URL powinna być `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` , a druga podstawowa wartość adresu URL powinna być`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Aby uzyskać więcej informacji, zobacz [temat jak skonfigurować funkcję Live Data (wersja zapoznawcza)](../azure-monitor/insights/container-insights-livedata-setup.md) dla Azure monitor kontenerów i instrukcje dotyczące konfigurowania uwierzytelniania w sekcji [Konfigurowanie uwierzytelniania zintegrowanego usługi AD](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) .

    d. Po zakończeniu wybierz pozycję **zarejestruj** .

2. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **uprawnienia interfejsu API**, a następnie wybierz pozycję **+ Dodaj uprawnienie**.

    a. Wybierz pozycję **Moje interfejsy API**, a następnie wybierz aplikację serwera usługi Azure AD utworzoną w poprzednim kroku, taką jak *AKSAzureADServer*.

    b. Wybierz pozycję **uprawnienia delegowane**, a następnie zaznacz pole wyboru obok aplikacji serwera usługi Azure AD.

    ![Konfigurowanie uprawnień aplikacji](media/aad-integration/select-api.png)

    c. Wybierz pozycję **Dodaj uprawnienia**.

    d. W obszarze **wyrażanie zgody**wybierz pozycję **Udziel zgody administratora**. Ten przycisk nie jest dostępny, jeśli bieżące konto nie jest administratorem dzierżawy. Po udzieleniu uprawnień w portalu zostanie wyświetlone następujące powiadomienie:

    ![Powiadomienie o pomyślnym przyznanych uprawnieniach](media/aad-integration/permissions-granted.png)

3. W lewym okienku aplikacji usługi Azure AD wybierz pozycję **uwierzytelnianie**.

    - W obszarze **domyślny typ klienta**wybierz pozycję **tak** , aby **traktować klienta jako klienta publicznego**.

5. W lewym okienku aplikacji usługi Azure AD Zanotuj identyfikator aplikacji. W przypadku wdrażania klastra AKS z obsługą usługi Azure AD ta wartość jest określana jako identyfikator aplikacji klienckiej.

   ![Pobieranie identyfikatora aplikacji](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Następnie Uzyskaj identyfikator dzierżawy platformy Azure. Ta wartość jest używana podczas tworzenia klastra AKS.

Na Azure Portal wybierz pozycję właściwości **Azure Active Directory** > **Properties** i zanotuj **Identyfikator katalogu**. Podczas tworzenia klastra AKS z obsługą usługi Azure AD ta wartość jest określana jako identyfikator dzierżawy.

![Uzyskaj identyfikator dzierżawy platformy Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Wdrażanie klastra AKS

Użyj polecenia [AZ Group Create][az-group-create] , aby utworzyć grupę zasobów dla klastra AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [AZ AKS Create][az-aks-create] , aby WDROŻYĆ klaster AKS. Następnie zastąp wartości w następującym przykładowym poleceniu. Użyj wartości zebranych podczas tworzenia aplikacji usługi Azure AD dla identyfikatora aplikacji serwera, klucza tajnego aplikacji, identyfikatora aplikacji klienta i identyfikatora dzierżawy.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Tworzenie klastra AKS trwa kilka minut.

## <a name="create-an-rbac-binding"></a>Tworzenie powiązania RBAC

> [!NOTE]
> W nazwie powiązania roli klastra jest rozróżniana wielkość liter.

Przed użyciem konta Azure Active Directory z klastrem AKS należy utworzyć powiązanie role lub role klastra. Role definiują uprawnienia do udzielenia, a powiązania stosują je do wybranych użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub całego klastra. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

Najpierw użyj polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] z `--admin` argumentem, aby zalogować się do klastra z dostępem administratora.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie utwórz ClusterRoleBinding dla konta usługi Azure AD, które chcesz udzielić dostępu do klastra AKS. Poniższy przykład daje kontu pełen dostęp do wszystkich przestrzeni nazw w klastrze:

- Jeśli użytkownik, któremu przyznano powiązanie RBAC, znajduje się w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie głównej nazwy użytkownika (UPN). Przejdź do kroku, aby utworzyć manifest YAML dla ClusterRoleBinding.

- Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, zapytaj i Użyj zamiast niego właściwości **objectid** . W razie potrzeby Pobierz identyfikator objectId wymaganego konta użytkownika za pomocą polecenia [AZ AD User show][az-ad-user-show] . Podaj główną nazwę użytkownika (UPN) wymaganego konta:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Utwórz plik, taki jak *RBAC-AAD-User. YAML*, a następnie wklej poniższą zawartość. W ostatnim wierszu Zastąp **userPrincipalName_or_objectId** nazwą UPN lub identyfikatorem obiektu. Wybór zależy od tego, czy użytkownik jest tą samą dzierżawą usługi Azure AD.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Zastosuj powiązanie przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] , jak pokazano w następującym przykładzie:

```console
kubectl apply -f rbac-aad-user.yaml
```

Powiązanie roli można również utworzyć dla wszystkich członków grupy usługi Azure AD. Grupy usługi Azure AD są określane przy użyciu identyfikatora obiektu grupy, jak pokazano w poniższym przykładzie.

Utwórz plik, taki jak *RBAC-AAD-Group. YAML*, a następnie wklej poniższą zawartość. Zaktualizuj identyfikator obiektu grupy za pomocą jednego z dzierżawy usługi Azure AD:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Zastosuj powiązanie przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] , jak pokazano w następującym przykładzie:

```console
kubectl apply -f rbac-aad-group.yaml
```

Aby uzyskać więcej informacji na temat zabezpieczania klastra Kubernetes z funkcją RBAC, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Uzyskiwanie dostępu do klastra za pomocą usługi Azure AD

Pobierz kontekst dla użytkownika niebędącego administratorem przy użyciu polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po uruchomieniu `kubectl` polecenia zostanie wyświetlony monit o uwierzytelnienie przy użyciu platformy Azure. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zakończyć proces, jak pokazano w następującym przykładzie:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po zakończeniu procesu token uwierzytelniania jest buforowany. Zostanie wyświetlony monit o ponowne zalogowanie się po wygaśnięciu tokenu lub ponownym utworzeniu pliku konfiguracji Kubernetes.

Jeśli po pomyślnym zalogowaniu zostanie wyświetlony komunikat o błędzie autoryzacji, sprawdź następujące kryteria:

```console
error: You must be logged in to the server (Unauthorized)
```


- W zależności od tego, czy konto użytkownika znajduje się w tej samej dzierżawie usługi Azure AD, jest zdefiniowany odpowiedni identyfikator obiektu, czy nazwa UPN.
- Użytkownik nie jest członkiem więcej niż 200 grup.
- Wpis tajny zdefiniowany w rejestracji aplikacji dla serwera jest zgodny z wartością skonfigurowaną `--aad-server-app-secret`za pomocą.

## <a name="next-steps"></a>Następne kroki

Aby używać użytkowników i grup usługi Azure AD do kontrolowania dostępu do zasobów klastra, zobacz [Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości usługi Azure AD w AKS][azure-ad-rbac].

Aby uzyskać więcej informacji na temat zabezpieczania klastrów Kubernetes, zobacz [Opcje dostępu i tożsamości dla AKS][rbac-authorization].

Aby dowiedzieć się więcej na temat tożsamości i kontroli zasobów, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
