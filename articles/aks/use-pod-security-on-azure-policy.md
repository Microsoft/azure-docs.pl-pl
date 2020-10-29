---
title: Zabezpieczanie zasobników z Azure Policy w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć Azure Policy program Kubernetes w usłudze Azure AKS
services: container-service
ms.topic: article
ms.date: 09/22/2020
author: jluk
ms.openlocfilehash: 5178aa30c3bfec014dd10e2c4f3de182aaef7e68
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900127"
---
# <a name="secure-pods-with-azure-policy"></a>Zabezpieczanie zasobników przy użyciu usługi Azure Policy

Aby zwiększyć bezpieczeństwo klastra AKS, można kontrolować, jakie funkcje są przyznawane i czy wszystkie są uruchomione względem zasad firmy. Ten dostęp jest definiowany za pomocą wbudowanych zasad udostępnianych przez [dodatek Azure Policy dla AKS][kubernetes-policy-reference]. Dzięki zapewnieniu dodatkowej kontroli nad aspektami zabezpieczeń w zakresie specyfikacji, takich jak uprawnienia główne, zapewnia ściślejszy poziom bezpieczeństwa i wgląd w to, co jest wdrażane w klastrze. Jeśli pod warunkiem nie spełniają warunków określonych w zasadach, Azure Policy może nie zezwalać na uruchamianie lub Flagowanie naruszenia. W tym artykule opisano sposób użycia Azure Policy w celu ograniczenia wdrożenia w programie AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Zainstaluj dodatek Azure Policy dla AKS

Aby zabezpieczyć AKS z wykorzystaniem Azure Policy, należy zainstalować Azure Policy dodatek dla AKS w klastrze AKS. Wykonaj następujące [kroki, aby zainstalować dodatek Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

W tym dokumencie założono, że masz następujące elementy, które zostały wdrożone w powiązanym powyższym przewodniku.

* Zarejestrowano `Microsoft.ContainerService` `Microsoft.PolicyInsights` dostawców zasobów i przy użyciu polecenia `az provider register`
* Interfejs wiersza polecenia platformy Azure 2,12 lub nowszej
* Klaster AKS w wersji 1,15 lub nowszej z dodatkiem Azure Policy

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Omówienie zabezpieczania zasobników za pomocą Azure Policy dla AKS

>[!NOTE]
> Ten dokument zawiera szczegółowe informacje na temat używania Azure Policy do zabezpieczania zasobników, które są następnikiem [funkcji zasad zabezpieczeń Kubernetes pod w wersji zapoznawczej](use-pod-security-policies.md).
> **Nie można jednocześnie włączyć obu zasad zabezpieczeń (wersja zapoznawcza) i dodatku Azure Policy dla AKS.**
> 
> Jeśli zainstalowano dodatek Azure Policy w klastrze z włączonymi zasadami zabezpieczeń na poziomie, [wykonaj następujące kroki, aby wyłączyć zasady zabezpieczeń na](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster)poziomie.

W klastrze AKS kontroler przyjmowania jest używany do przechwytywania żądań do serwera interfejsu API, gdy zasób zostanie utworzony i zaktualizowany. Kontroler przyjmowania danych może następnie *sprawdzić poprawność* żądania zasobu względem zestawu reguł, czy należy go utworzyć.

Wcześniej [zasady zabezpieczeń funkcji (wersja zapoznawcza)](use-pod-security-policies.md) zostały włączone w projekcie Kubernetes, aby ograniczyć, jakie elementy można wdrożyć.

Za pomocą dodatku Azure Policy, klaster AKS może używać wbudowanych zasad platformy Azure, które zabezpieczają zasoby i inne Kubernetes podobne do zasad zabezpieczeń na poziomie. Dodatek Azure Policy dla AKS instaluje zarządzane wystąpienie [strażnika](https://github.com/open-policy-agent/gatekeeper), sprawdzając kontroler dopuszczenia. Azure Policy dla Kubernetes jest oparty na otwartym agencie zasad Open Source, który opiera się na [języku zasad rego](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Ten dokument zawiera szczegółowe informacje na temat sposobu używania Azure Policy do zabezpieczania zasobników w klastrze AKS i poinstruować o tym, jak przeprowadzić migrację z zasad zabezpieczeń (wersja zapoznawcza).

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia ogólne mają zastosowanie do Azure Policy dodatku dla klastrów Kubernetes:

- Dodatek Azure Policy dla Kubernetes jest obsługiwany w Kubernetes w wersji **1,14** lub nowszej.
- Dodatek Azure Policy dla Kubernetes można wdrożyć tylko w pulach węzłów systemu Linux
- Obsługiwane są tylko wbudowane definicje zasad
- Maksymalna liczba niezgodnych rekordów na zasady dla klastra: **500**
- Maksymalna liczba niezgodnych rekordów na subskrypcję: **1 000 000**
- Instalacje strażnika poza dodatkiem Azure Policy nie są obsługiwane. Przed włączeniem dodatku Azure Policy Odinstaluj wszystkie składniki zainstalowane przez poprzednią instalację strażnika.
- [Przyczyny braku zgodności](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) nie są dostępne dla tego [trybu dostawcy zasobów](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

Następujące ograniczenia mają zastosowanie tylko do Azure Policy dodatku dla AKS:

- Nie można jednocześnie włączyć [zasad zabezpieczeń AKS (wersja zapoznawcza)](use-pod-security-policies.md) i dodatku Azure Policy dla AKS. 
- Obszary nazw są automatycznie wykluczane przez Azure Policy dodatku do oceny: _polecenia-system_ , _strażnik-system_ i _AKS-Periscope_ .

### <a name="recommendations"></a>Zalecenia

Poniżej przedstawiono ogólne zalecenia dotyczące używania dodatku Azure Policy:

- Aby można było uruchomić dodatek Azure Policy, wymagane są 3 składniki strażnika: 1 podelementy inspekcji w ramach i 2 replik. Te składniki zużywają więcej zasobów, ponieważ liczba zasobów Kubernetes i przypisań zasad zwiększa się w klastrze, który wymaga operacji inspekcji i wymuszania.

  - W przypadku mniej niż 500 zasobników w jednym klastrze z maksymalnie 20 ograniczeniami: 2 procesorów wirtualnych vCPU i 350 MB pamięci na składnik.
  - Ponad 500 zasobników w jednym klastrze z maksymalnie 40 ograniczeniami: 3 procesorów wirtualnych vCPU i 600 MB pamięci na składnik.

Poniższe zalecenie dotyczy tylko AKS i dodatku Azure Policy:

- Użyj puli węzłów systemu z funkcją `CriticalAddonsOnly` zmiany harmonogramu, aby zaplanować zasobniki strażników. Aby uzyskać więcej informacji, zobacz [Korzystanie z pul węzłów systemowych](use-system-pools.md#system-and-user-node-pools).
- Zabezpiecz ruch wychodzący z klastrów AKS. Aby uzyskać więcej informacji, zobacz [Kontrola ruchu wychodzącego dla węzłów klastra](limit-egress-traffic.md).
- Jeśli klaster został `aad-pod-identity` włączony, w WĘŹLE NMI (tożsamość zarządzana) są modyfikowane węzły dołączenie iptables do przechwytywania wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania wysłane do punktu końcowego metadanych są przechwytywane przez NMI, nawet jeśli nie są używane `aad-pod-identity` . AzurePodIdentityException CRD można skonfigurować w taki sposób `aad-pod-identity` , aby informował, że wszelkie żądania kierowane do punktu końcowego metadanych pochodzące z elementu pod, które pasują do etykiet zdefiniowanych w CRD, powinny być przekazywane z serwerem proxy bez żadnego przetwarzania w NMI. Systemowy `kubernetes.azure.com/managedby: aks` wymiarname z etykietą w _polecenia —_ przestrzeń nazw systemu powinna zostać wykluczona w `aad-pod-identity` ramach konfigurowania AzurePodIdentityException CRD. Aby uzyskać więcej informacji, zobacz temat [wyłączanie usługi AAD-pod-Identity dla określonego elementu lub aplikacji](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Aby skonfigurować wyjątek, Zainstaluj polecenie [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

Dodatek Azure Policy wymaga zasobów procesora i pamięci do działania. Te wymagania zwiększają się wraz ze wzrostem rozmiaru klastra. Zapoznaj się z [zaleceniami Azure Policy][policy-recommendations] , aby uzyskać ogólne wskazówki dotyczące korzystania z Azure Policy dodatku.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Zasady platformy Azure do zabezpieczania Kubernetesch

Po zainstalowaniu dodatku Azure Policy domyślnie nie są stosowane żadne zasady.

Istnieją 11 wbudowanych zasad platformy Azure oraz dwie wbudowane inicjatywy, które w pełni zabezpieczają w klastrze AKS.
Poszczególne zasady można dostosować przy użyciu efektu. W [tym miejscu znajduje się pełna lista zasad AKS i ich obsługiwane efekty][policy-samples]. Przeczytaj więcej na temat [Azure Policy efektów](../governance/policy/concepts/effects.md).

Zasady platformy Azure można stosować na poziomie grupy zarządzania, subskrypcji lub grupy zasobów. Podczas przypisywania zasad na poziomie grupy zasobów upewnij się, że docelowa Grupa zasobów klastra AKS została wybrana w zakresie zasad. Każdy klaster w przypisanym zakresie z zainstalowanym dodatkiem Azure Policy jest w zakresie dla zasad.

Jeśli korzystasz z [zasad zabezpieczeń (wersja zapoznawcza) ](use-pod-security-policies.md), Dowiedz się, jak [przeprowadzić migrację do Azure Policy i inne różnice w zachowaniu](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Wbudowane inicjatywy zasad

Inicjatywa w Azure Policy to zbiór definicji zasad, które są dostosowane do osiągnięcia pojedynczego przełożonego celu. Korzystanie z inicjatyw może uprościć zarządzanie i przypisywanie zasad w klastrach AKS. Inicjatywa istnieje jako pojedynczy obiekt, Dowiedz się więcej o [inicjatywach Azure Policy](../governance/policy/overview.md#initiative-definition).

Azure Policy for Kubernetes oferuje dwie wbudowane inicjatywy, w których znajdują się bezpieczne, [podstawowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) i [ograniczone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Obie wbudowane inicjatywy są zbudowane z definicji używanych w ramach [zasad zabezpieczeń na poziomie z Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Kontrola zasad zabezpieczeń pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Link definicji Azure Policy| [Podstawowa inicjatywa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Inicjatywy z ograniczeniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Nie Zezwalaj na uruchamianie uprzywilejowanych kontenerów|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Tak | Tak
|Nie Zezwalaj na udostępnianie przestrzeni nazw hostów|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Tak | Tak
|Ogranicz użycie sieci i portów hosta|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Tak | Tak
|Ogranicz użycie systemu plików hosta|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Tak | Tak
|Ogranicz możliwości systemu Linux do [zestawu domyślnego](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Tak | Tak
|Ograniczanie użycia zdefiniowanych typów woluminów|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Tak — dozwolone typy woluminów to `configMap` , `emptyDir` ,,, `projected` `downwardAPI``persistentVolumeClaim`|
|Eskalacja uprawnień do elementu głównego|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Tak |
|Ograniczanie identyfikatorów użytkowników i grup kontenera|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Tak|
|Ograniczanie alokacji elementu FSGroup, który jest właścicielem woluminów na woluminie|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Tak — dozwolone są reguły,, `runAsUser: mustRunAsNonRoot` `supplementalGroup: mustRunAs 1:65536` `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Wymaga profilu seccomp|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Tak, allowedProfiles * `docker/default` lub `runtime/default` |

\* Platforma Docker/default jest przestarzała w Kubernetes od wersji 1.11

### <a name="additional-optional-policies"></a>Dodatkowe zasady opcjonalne

Istnieją dodatkowe zasady platformy Azure, które można zastosować pojedynczo poza zastosowaniem inicjatywy. Należy rozważyć dołączenie tych zasad Oprócz inicjatyw, jeśli wymagania nie są spełnione przez wbudowane inicjatywy.

|[Kontrola zasad zabezpieczeń pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Link definicji Azure Policy| Zastosuj oprócz podstawowej inicjatywy | Zastosuj oprócz inicjatywy z ograniczeniami |
|---|---|---|---|
|Zdefiniuj profil AppArmor używany przez kontenery|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Opcjonalne | Opcjonalne |
|Zezwalaj na instalacje, które nie są tylko do odczytu|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Opcjonalne | Opcjonalne |
|Ogranicz do określonych sterowników FlexVolume|[Chmura publiczna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Opcjonalne-use, jeśli chcesz ograniczyć sterowniki FlexVolume, ale nie inne ustawione przez "Ogranicz użycie zdefiniowanych typów woluminów" | Nie dotyczy — inicjatywy z ograniczeniami obejmują "ograniczanie użycia zdefiniowanych typów woluminów", co nie zezwala na wszystkie sterowniki FlexVolume |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Nieobsługiwane wbudowane zasady dla zarządzanych klastrów AKS

> [!NOTE]
> Następujące 3 zasady nie są **obsługiwane w programie AKS** z powodu dostosowywania aspektów zarządzanych i zabezpieczonych przez AKS jako usługi zarządzanej. Te zasady są zbudowane specjalnie dla połączonych klastrów usługi Azure ARC z niezarządzanymi płaszczyznami kontroli.

|[Kontrola zasad zabezpieczeń pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Definiowanie niestandardowego kontekstu SELinux kontenera|
|Ogranicz profil sysctl używany przez kontenery|
|Domyślne typy instalacji procesu są zdefiniowane w celu zmniejszenia obszaru ataków|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Wykluczanie przestrzeni nazw

> [!WARNING]
> W celu pozostawania w dobrej kondycji w przestrzeniach nazw administratorów, takich jak polecenia-system, należy uruchomić niezależną przestrzeń nazw, usuwając z listy Domyślnie wykluczone przestrzenie nazw, które mogą powodować naruszenia zasad z powodu wymaganego systemu.

Usługa AKS wymaga, aby w klastrze działały systemowe w celu zapewnienia krytycznych usług, takich jak rozpoznawanie nazw DNS. Zasady ograniczające funkcjonalność pod mogą mieć wpływ na stabilność systemu. W związku z tym następujące przestrzenie nazw są **wykluczone z oceny zasad podczas żądania przyjmowania podczas tworzenia, aktualizacji i inspekcji zasad** . Wymusza to wykluczenie nowych wdrożeń do tych przestrzeni nazw z zasad platformy Azure.

1. polecenia — system
1. Strażnik — system
1. Azure — łuk
1. AKS — Periscope

Dodatkowe niestandardowe przestrzenie nazw mogą być wykluczone z oceny podczas tworzenia, aktualizowania i inspekcji. Te wykluczenia należy stosować w przypadku wyspecjalizowanych zasobników, które są uruchamiane w oficjalnie zaakceptowanej przestrzeni nazw i chcą uniknąć wyzwalania naruszeń inspekcji.

## <a name="apply-the-baseline-initiative"></a>Zastosuj inicjatywę bazową

> [!TIP]
> Wszystkie zasady domyślnie mają wpływ na inspekcję. Efekty można aktualizować w dowolnym momencie za jego Azure Policy.

Aby zastosować inicjatywę bazową, można przydzielić przez Azure Portal.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Skorzystaj z [tego linku do Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , aby zapoznać się z inicjatywą linii bazowej zabezpieczeń
1. Ustaw **zakres** na poziom subskrypcji lub tylko grupę zasobów zawierającą klastry AKS z włączonym dodatkiem Azure Policy
1. Wybierz stronę **Parametry** i zaktualizuj **efekt** z `audit` programu do, `deny` Aby zablokować nowe wdrożenia naruszające inicjatywę bazową.
1. Dodaj dodatkowe przestrzenie nazw, które mają być wykluczone z oceny podczas tworzenia, aktualizacji i inspekcji, [niektóre przestrzenie nazw są wymuszane jako wykluczone z oceny zasad.](#namespace-exclusion) 
 ![ Zaktualizuj efekt](media/use-pod-security-on-azure-policy/update-effect.png)
1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać zasady

Aby sprawdzić, czy zasady są stosowane do klastra, należy uruchomić program `kubectl get constrainttemplates` .

> [!NOTE]
> Synchronizowanie zasad może potrwać [do 20 minut](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) .

Dane wyjściowe powinny wyglądać podobnie do:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Weryfikuj odrzucanie uprzywilejowanego elementu pod

Najpierw Przetestuj, co się dzieje, gdy planujesz go z kontekstem zabezpieczeń `privileged: true` . Ten kontekst zabezpieczeń przekazuje uprawnienia administratora. Inicjatywa podstawowa nie zezwala na dostęp do uprzywilejowanych zasobników, więc żądanie zostanie odrzucone, co spowoduje odrzucenie wdrożenia.

Utwórz plik o nazwie `nginx-privileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Utwórz polecenie pod with [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Zgodnie z oczekiwaniami nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przełączeniem.

## <a name="test-creation-of-an-unprivileged-pod"></a>Tworzenie testu dla nieuprzywilejowanego pod

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć elementu głównego do powiązania NGINX z portem 80. Ta prośba została odrzucona przez inicjatywę zasad linii bazowej, więc uruchomienie nie powiodło się. Spróbujmy teraz korzystać z tego samego NGINX pod bez uprzywilejowanego dostępu.

Utwórz plik o nazwie `nginx-unprivileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pomyślnie zaplanowano zadanie pod. Po sprawdzeniu stanu usługi pod za pomocą [polecenia kubectl Get-zasobnikowego][kubectl-get] , pod jest *uruchomiony* :

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

W tym przykładzie przedstawiono podstawową inicjatywę wpływającą wyłącznie na wdrożenia, które naruszają zasady w kolekcji. Dozwolone wdrożenia nadal działają.

Usuń NGINX nieuprzywilejowany pod za pomocą polecenia [Delete polecenia kubectl][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Wyłączanie zasad i dodatku Azure Policy

Aby usunąć inicjatywę bazową:

1. Przejdź do okienka zasady na Azure Portal
1. Wybierz pozycję **przypisania** w okienku po lewej stronie
1. Kliknij przycisk "..." obok profilu linii bazowej
1. Wybierz pozycję "Usuń przypisanie"

![Usuń przypisanie](media/use-pod-security-on-azure-policy/delete-assignment.png)

Aby wyłączyć dodatek Azure Policy, użyj polecenia [AZ AKS Disable-dodatkis][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Dowiedz się, jak usunąć [dodatek Azure Policy z Azure Portal](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrowanie z zasad zabezpieczeń Kubernetes pod do Azure Policy

Aby przeprowadzić migrację z zasad zabezpieczeń na komputerze, należy wykonać następujące działania w klastrze.

1. [Wyłącz zasady zabezpieczeń pod](use-pod-security-policies.md#clean-up-resources) względem klastra
1. Włączanie [dodatku Azure Policy][kubernetes-policy-reference]
1. Włącz odpowiednie zasady platformy Azure z [dostępnych zasad wbudowanych][policy-samples]

Poniżej znajduje się podsumowanie zachowania zmian między zasadami zabezpieczeń a Azure Policy.

|Scenariusz| Zasady zabezpieczeń pod | Azure Policy |
|---|---|---|
|Instalacja|Włącz funkcję zasad zabezpieczeń pod |Włącz dodatek Azure Policy
|Wdrażanie zasad| Wdróż zasób zasad zabezpieczeń| Przypisz zasady platformy Azure do zakresu subskrypcji lub grupy zasobów. Dodatek Azure Policy jest wymagany dla aplikacji zasobów Kubernetes.
| Zasady domyślne | Po włączeniu zasad zabezpieczeń na platformie AKS są stosowane domyślne zasady uprzywilejowane i nieograniczone. | Nie są stosowane żadne zasady domyślne, włączając dodatek Azure Policy. Należy jawnie włączyć zasady w Azure Policy.
| Kto może tworzyć i przypisywać zasady | Administrator klastra tworzy zasób pod kątem zasad zabezpieczeń | Użytkownicy muszą mieć minimalną rolę uprawnień "właściciel" lub "Współautor zasad zasobów" w grupie zasobów klastra AKS. -Za pomocą interfejsu API użytkownicy mogą przypisywać zasady w zakresie zasobów klastra AKS. Użytkownik powinien mieć co najmniej uprawnienia "właściciel" lub "Współautor zasad zasobów" w zasobie klastra AKS. -W Azure Portal zasady można przypisywać na poziomie grupy zarządzania/subskrypcji/grupy zasobów.
| Autoryzowanie zasad| Konta użytkowników i usług wymagają jawnych uprawnień do używania zasad zabezpieczeń usługi. | Do autoryzacji zasad nie jest wymagane żadne dodatkowe przypisanie. Po przypisaniu zasad na platformie Azure wszyscy użytkownicy klastrów mogą korzystać z tych zasad.
| Zastosowanie zasad | Użytkownik administracyjny pomija wymuszanie zasad zabezpieczeń pod. | Wszyscy użytkownicy (administrator & nie administrator) widzą te same zasady. Nie ma żadnej specjalnej wielkości liter na podstawie użytkowników. Aplikacje zasad mogą być wykluczone na poziomie przestrzeni nazw.
| Zakres zasad | Zasady zabezpieczeń pod nie są obszarami nazw | Szablony ograniczeń używane przez Azure Policy nie są obszarami nazw.
| Akcja odmowy/inspekcji/mutacji | Zasady zabezpieczeń pod obsługują tylko akcje Odmów. Mutację można wykonać przy użyciu wartości domyślnych podczas tworzenia żądań. Walidacja może odbywać się w trakcie żądania aktualizacji.| Azure Policy obsługuje obie akcje inspekcji & odmowy. Mutacja nie jest jeszcze obsługiwana, ale została zaplanowana.
| Zgodność z zasadami zabezpieczeń | Nie ma wglądu w zgodność z jednostkami, które istniały przed włączeniem zasad zabezpieczeń pod. Brak zgodnych zasobników utworzonych po włączeniu zasad zabezpieczeń na poziomie. | Niezgodne zasobniki, które istniały przed zastosowaniem zasad platformy Azure, byłyby widoczne w przypadku naruszeń zasad. Niezgodne zasobniki utworzone po włączeniu zasad platformy Azure są odrzucane, jeśli zasady są ustawione z efektem Odmów.
| Jak wyświetlić zasady w klastrze | `kubectl get psp` | `kubectl get constrainttemplate` -Wszystkie zasady są zwracane.
| Zasady zabezpieczeń na poziomie Standard — uprzywilejowane | Zasób zasad zabezpieczeń uprzywilejowanych jest tworzony domyślnie podczas włączania funkcji. | Tryb uprzywilejowany oznacza brak ograniczeń, w związku z czym nie ma żadnego przypisania Azure Policy.
| [Zasady zabezpieczeń w warstwie Standardowa — linia bazowa/domyślna](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Użytkownik instaluje zasób bazowy zasad zabezpieczeń. | Azure Policy zawiera [wbudowaną inicjatywę bazową](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , która jest mapowana na zasady zabezpieczeń według planu bazowego.
| [W warstwie Standardowa zasad zabezpieczeń — z ograniczeniami](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Użytkownik instaluje zasób z ograniczoną zasadą zabezpieczeń. | Azure Policy zawiera [wbudowaną ograniczoną inicjatywę](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) , która jest mapowana na zasady zabezpieczeń ograniczone pod.

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak zastosować zasady platformy Azure, które ograniczają możliwości wdrażania uprzywilejowanych, aby uniemożliwić korzystanie z uprzywilejowanego dostępu. Istnieje wiele zasad, które można zastosować, takich jak zasady ograniczające użycie woluminów. Aby uzyskać więcej informacji na temat dostępnych opcji, zapoznaj się z tematem [Azure Policy dokumentacja Kubernetes][kubernetes-policy-reference].

Aby uzyskać więcej informacji na temat ograniczania ruchu sieciowego, zobacz [bezpieczny ruch między jednostkami z użyciem zasad sieciowych w AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete