---
title: Pojęcia — dostęp i tożsamość w usługach Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o dostępie i tożsamościach w usługach Azure Kubernetes Service (AKS), w tym o integracji z usługą Azure Active Directory, kontroli dostępu opartej na rolach (RBAC) kubernetes oraz rolach i powiązaniach.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789559"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Dostęp do klastrów Kubernetes można uwierzytelniać, autoryzować, zabezpieczać i kontrolować na różne sposoby. 
* Przy użyciu kontroli dostępu opartej na rolach (RBAC) kubernetes można udzielić użytkownikom, grupom i kontom usługi dostępu tylko do potrzebnych im zasobów. 
* Dzięki Azure Kubernetes Service (AKS) można dodatkowo zwiększyć strukturę zabezpieczeń i uprawnień za pośrednictwem Azure Active Directory RBAC platformy Azure. 

Kontroli dostępu na poziomie ról i usługi AKS na platformie Kubernetes ułatwiają zabezpieczanie dostępu do klastra i zapewnianie deweloperom i operatorom tylko minimalnych wymaganych uprawnień.

W tym artykule oprowadzono podstawowe pojęcia, które ułatwiają uwierzytelnianie i przypisywanie uprawnień w u usługi AKS.

## <a name="aks-service-permissions"></a>Uprawnienia usługi AKS

Podczas tworzenia klastra usługa AKS generuje lub modyfikuje zasoby, których potrzebuje (takich jak maszyny wirtualne i karty sieciowe), aby utworzyć i uruchomić klaster w imieniu użytkownika. Ta tożsamość różni się od uprawnień tożsamości klastra, które jest tworzone podczas tworzenia klastra.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Tożsamość tworząca i działająca uprawnienia klastra

Następujące uprawnienia są wymagane przez tożsamość tworzenia i obsługi klastra.

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Wymagany do odczytu identyfikatora zestawu szyfrowania dysków. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Wymagane do aktualizowania grup umieszczania w pobliżu. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane do skonfigurowania bram aplikacji i dołączenia do podsieci. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane do skonfigurowania sieciowej grupy zabezpieczeń dla podsieci w przypadku korzystania z niestandardowej sieci wirtualnej.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Wymagane do skonfigurowania publicznych ip ruchu wychodzącego na usługa Load Balancer w warstwie Standardowa. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Wymagane do tworzenia i aktualizowania obszarów roboczych usługi Log Analytics oraz monitorowania platformy Azure dla kontenerów. |

### <a name="aks-cluster-identity-permissions"></a>Uprawnienia tożsamości klastra usługi AKS

Następujące uprawnienia są używane przez tożsamość klastra usługi AKS, która jest tworzona i skojarzona z klastrem usługi AKS. Każde uprawnienie jest używane z poniższych powodów:

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Wymagane do tworzenia użytkowników i obsługi klastra
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Wymagane do skonfigurowania usługi równoważenia obciążenia dla usługi LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Wymagane do znalezienia i skonfigurowania publicznych ip dla usługi LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/join/action` | Wymagane do konfigurowania publicznych ip dla usługi LoadBalancer. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Wymagane do tworzenia lub usuwania reguł zabezpieczeń dla usługi LoadBalancer. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Wymagane do skonfigurowania azureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Wymagane do skonfigurowania kont magazynu dla usługi AzureFile lub AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Wymagane do skonfigurowania tabel tras i tras dla węzłów. |
| `Microsoft.Compute/virtualMachines/read` | Wymagane do znalezienia informacji o maszynach wirtualnych w programie VMAS, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| `Microsoft.Compute/virtualMachines/write` | Wymagane do dołączania dysków AzureDisks do maszyny wirtualnej w programie VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Wymagane do znalezienia informacji dotyczących maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| `Microsoft.Network/networkInterfaces/write` | Wymagane do dodania maszyny wirtualnej w programie VMAS do puli adresów zaplecza usługi równoważenia obciążenia. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Wymagane do dodawania zestawu skalowania maszyn wirtualnych do pul adresów zaplecza usługi równoważenia obciążenia i skalowania węzłów w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Wymagane do dołączenia dysku AzureDisks i dodania maszyny wirtualnej z zestawu skalowania maszyn wirtualnych do usługi równoważenia obciążenia. |
| `Microsoft.Network/networkInterfaces/read` | Wymagane do przeszukiwania wewnętrznych adresów IP i pul adresów zaplecza usługi równoważenia obciążenia dla maszyn wirtualnych w maszynie wirtualnej. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Wymagany do wyszukiwania wewnętrznych adresów IP i pul adresów zaplecza usługi równoważenia obciążenia dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Wymagane do znalezienia publicznych ip dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Wymagane do sprawdzenia, czy podsieć istnieje dla wewnętrznego równoważenia obciążenia w innej grupie zasobów. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Wymagane do skonfigurowania migawek dla dysku AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Wymagane do znajdowania rozmiarów maszyn wirtualnych w celu znalezienia limitów woluminów AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Dodatkowe uprawnienia tożsamości klastra

Podczas tworzenia klastra z określonymi atrybutami potrzebne będą następujące dodatkowe uprawnienia dla tożsamości klastra. Ponieważ te uprawnienia nie są przypisywane automatycznie, należy dodać je do tożsamości klastra po jej utworzeniu.

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Wymagane w przypadku używania sieciowej grupy zabezpieczeń w innej grupie zasobów. Wymagane do skonfigurowania reguł zabezpieczeń dla usługi LoadBalancer. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane w przypadku korzystania z podsieci w innej grupie zasobów, takiej jak niestandardowa sieć wirtualna. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Wymagane w przypadku korzystania z podsieci skojarzonej z tabelą tras w innej grupie zasobów, takiej jak niestandardowa sieć wirtualna z niestandardową tabelą tras. Wymagane do sprawdzenia, czy podsieć istnieje już dla podsieci w innej grupie zasobów. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Wymagane w przypadku korzystania z wewnętrznego równoważenia obciążenia w innej grupie zasobów. Wymagane do sprawdzenia, czy podsieć istnieje już dla wewnętrznego równoważenia obciążenia w grupie zasobów. |
| `Microsoft.Network/privatednszones/*` | Wymagane w przypadku korzystania z prywatnej strefy DNS w innej grupie zasobów, takiej jak niestandardowa strefa privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kontrola dostępu na podstawie ról na platformie Kubernetes

Kontroli RBAC na platformie Kubernetes zapewnia szczegółowe filtrowanie akcji użytkownika. Za pomocą tego mechanizmu sterowania:
* Przypisujesz użytkownikom lub grupom użytkowników uprawnienie do tworzenia i modyfikowania zasobów lub wyświetlania dzienników z uruchomionych obciążeń aplikacji. 
* Zakres uprawnień można określić dla pojedynczej przestrzeni nazw lub całego klastra usługi AKS. 
* Tworzysz *role w* celu zdefiniowania uprawnień, a następnie przypisujesz te role do użytkowników z *powiązaniami ról.*

Aby uzyskać więcej informacji, zobacz [Using Kubernetes RBAC authorization (Używanie autoryzacji RBAC na platformie Kubernetes).][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Role i clusterRole

#### <a name="roles"></a>Role
Przed przypisaniem uprawnień do użytkowników przy użyciu kontroli RBAC na platformie Kubernetes należy zdefiniować uprawnienia użytkownika jako *rolę*. Udzielanie uprawnień w przestrzeni nazw przy użyciu ról. 

> [!NOTE]
> Role kubernetes *przyznają* uprawnienia; Nie odmawiają *uprawnień.*

Aby udzielić uprawnień w całym klastrze lub do zasobów klastra spoza danej przestrzeni nazw, można zamiast tego użyć *funkcji ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

ClusterRole przyznaje i stosuje uprawnienia do zasobów w całym klastrze, a nie w określonej przestrzeni nazw.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowanych rolach w celu udzielenia uprawnień do zasobów przypisz te uprawnienia kontroli RBAC na platformie Kubernetes za pomocą *powiązyania ról.* Jeśli klaster usługi AKS integruje się z usługą [Azure Active Directory (Azure AD),](#azure-ad-integration)roleBindings przyznają użytkownikom usługi Azure AD uprawnienia do wykonywania akcji w klastrze. Zobacz, jak kontrolować dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory [Kubernetes.](azure-ad-rbac.md)

#### <a name="rolebindings"></a>RoleBindings

Przypisz role do użytkowników dla danej przestrzeni nazw przy użyciu po stronie rolebindings. Za pomocą elementu RoleBindings można logicznie oddzielić pojedynczy klaster usługi AKS, umożliwiając użytkownikom tylko dostęp do zasobów aplikacji w przypisanej im przestrzeni nazw. 

Aby powiązać role w całym klastrze lub z zasobami klastra poza podaną przestrzenią nazw, należy zamiast tego użyć *clusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

KlasterRoleBinding umożliwia powiązanie ról z użytkownikami i stosowanie ich do zasobów w całym klastrze, a nie w określonej przestrzeni nazw. Takie podejście umożliwia przyznanie administratorom lub inżynierom pomocy technicznej dostępu do wszystkich zasobów w klastrze usługi AKS.


> [!NOTE]
> Firma Microsoft/AKS wykonuje wszystkie akcje klastra za zgodą użytkownika w ramach wbudowanej roli Kubernetes i `aks-service` wbudowanego powiązania roli `aks-service-rolebinding` . 
> 
> Ta rola umożliwia UKS rozwiązywanie i diagnozowanie problemów z klastrem, ale nie może modyfikować uprawnień, tworzyć ról, powiązań ról ani innych akcji o wysokim poziomie uprawnień. Dostęp do ról jest włączany tylko w ramach aktywnych biletów pomocy technicznej z dostępem just in time (JIT). Przeczytaj więcej na temat [zasad obsługi AKS.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

*Konta usług* są jednym z podstawowych typów użytkowników w usłudze Kubernetes. Interfejs API platformy Kubernetes przechowuje konta usług i zarządza nimi. Poświadczenia konta usługi są przechowywane jako wpisy tajne platformy Kubernetes, dzięki czemu mogą być używane przez autoryzowane zasobniki do komunikowania się z serwerem interfejsu API. Większość żądań interfejsu API zapewnia token uwierzytelniania dla konta usługi lub normalnego konta użytkownika.

Konta zwykłych użytkowników umożliwiają bardziej tradycyjny dostęp administratorom i deweloperom, nie tylko usługom i procesom. Chociaż rozwiązanie Kubernetes nie zapewnia rozwiązania do zarządzania tożsamościami do przechowywania zwykłych kont użytkowników i haseł, rozwiązania do obsługi tożsamości zewnętrznych można zintegrować z rozwiązaniem Kubernetes. W przypadku klastrów usługi AKS to zintegrowane rozwiązanie do zarządzania tożsamościami to usługa Azure AD.

Aby uzyskać więcej informacji na temat opcji tożsamości na platformie Kubernetes, zobacz [Uwierzytelnianie kubernetes][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

Zwiększ bezpieczeństwo klastra usługi AKS dzięki integracji z usługą Azure AD. Oparta na dekadach zarządzania tożsamościami w przedsiębiorstwie usługa Azure AD to wielodostępna, oparta na chmurze usługa do zarządzania katalogami i tożsamościami, która łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Za pomocą usługi Azure AD można zintegrować tożsamości lokalne z klastrami usługi AKS, aby zapewnić jedno źródło zarządzania kontami i ich zabezpieczeń.

![Azure Active Directory integracji z klastrami AKS](media/concepts-identity/aad-integration.png)

Dzięki klastrom usługi AKS zintegrowanym z usługą Azure AD można udzielić użytkownikom lub grupom dostępu do zasobów platformy Kubernetes w przestrzeni nazw lub w klastrze. 

1. Aby uzyskać kontekst `kubectl` konfiguracji, użytkownik uruchamia [polecenie az aks get-credentials.][az-aks-get-credentials] 
1. Gdy użytkownik wchodzi w interakcję z klastrem AKS za pomocą usługi , jest wyświetlany monit o zalogowanie się przy `kubectl` użyciu poświadczeń usługi Azure AD. 

Takie podejście zapewnia jedno źródło do zarządzania kontami użytkowników i poświadczeń haseł. Użytkownik może uzyskać dostęp tylko do zasobów zgodnie z definicją administratora klastra.

Uwierzytelnianie usługi Azure AD jest udostępniane klastrom usługi AKS za pomocą OpenID Connect. OpenID Connect to warstwa tożsamości zbudowana na podstawie protokołu OAuth 2.0. Aby uzyskać więcej informacji na OpenID Connect, zobacz [dokumentację programu Open ID Connect.][openid-connect] W klastrze Kubernetes tokeny uwierzytelniania są weryfikowane za pomocą uwierzytelniania [tokenów.][webhook-token-docs] Uwierzytelnianie tokenu element webhook jest konfigurowane i zarządzane w ramach klastra usługi AKS.

### <a name="webhook-and-api-server"></a>Webhook i serwer interfejsu API

![Przepływ uwierzytelniania serwera webhook i serwera interfejsu API](media/concepts-identity/auth-flow.png)

Jak pokazano na powyższej ilustracji, serwer interfejsu API wywołuje serwer elementów webhook usługi AKS i wykonuje następujące czynności:

1. `kubectl`używa aplikacji klienckiej usługi Azure AD do logowania użytkowników przy użyciu przepływu autoryzacji urządzenia [OAuth 2.0.](../active-directory/develop/v2-oauth2-device-code.md)
2. Usługa Azure AD udostępnia access_token, id_token i refresh_token.
3. Użytkownik wykonuje żądanie do użytkownika `kubectl` za pomocą access_token z `kubeconfig` .
4. `kubectl` wysyła access_token do serwera interfejsów API.
5. Serwer interfejsu API jest skonfigurowany przy użyciu serwera uwierzytelniania webhook w celu przeprowadzenia walidacji.
6. Serwer uwierzytelniania element webhook potwierdza, JSON Web Token podpis jest prawidłowy, sprawdzając publiczny klucz podpisywania usługi Azure AD.
7. Aplikacja serwera używa poświadczeń dostarczonych przez użytkownika do wykonywania zapytań o członkostwo w grupach zalogowanego użytkownika z konta interfejs Graph API.
8. Odpowiedź jest wysyłana do serwera interfejsu API z informacjami o użytkowniku, takimi jak główna nazwa użytkownika (UPN) oświadczenia tokenu dostępu i członkostwo użytkownika w grupie na podstawie identyfikatora obiektu.
9. Interfejs API wykonuje decyzję o autoryzacji w oparciu o rolę/element RoleBinding platformy Kubernetes.
10. Po autoryzacji serwer interfejsu API zwraca odpowiedź na `kubectl` .
11. `kubectl` udostępnia użytkownikowi opinię.
 
Dowiedz się, jak zintegrować usługę AKS z usługą Azure AD z naszym przewodnikiem po integracji usługi Azure AD zarządzanej [przez usługę AKS.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Kontrola dostępu oparta na rolach (RBAC) platformy Azure [to](../azure-resource-manager/management/overview.md) system autoryzacji oparty na Azure Resource Manager, który umożliwia szczegółowe zarządzanie dostępem do zasobów platformy Azure.

| System RBAC | Opis |
|---|---|
| Kontrola dostępu na podstawie ról na platformie Kubernetes | Zaprojektowana do pracy z zasobami kubernetes w klastrze usługi AKS. |
| Kontrola dostępu na podstawie ról platformy Azure | Zaprojektowana do pracy z zasobami w ramach subskrypcji platformy Azure. |

Za pomocą kontroli RBAC platformy Azure tworzysz *definicję roli,* która określa uprawnienia do zastosowania. Następnie przypiszesz użytkownikowi lub grupie tę definicję roli za pomocą *przypisania roli* dla określonego *zakresu.* Zakresem może być pojedynczy zasób, grupa zasobów lub subskrypcja.

Aby uzyskać więcej informacji, zobacz Co to jest kontrola dostępu na podstawie ról [(RBAC) platformy Azure?][azure-rbac]

Istnieją dwa poziomy dostępu potrzebne do pełnego działania klastra usługi AKS: 
* [Uzyskaj dostęp do zasobu usługi AKS w ramach subskrypcji platformy Azure.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Kontroluj skalowanie lub uaktualnianie klastra przy użyciu interfejsów API usługi AKS.
  * Ściągnij swój `kubeconfig` .
* Dostęp do interfejsu API platformy Kubernetes. Ten dostęp jest kontrolowany przez:
  * [RBAC (tradycyjnie) kubernetes.](#kubernetes-rbac)
  * [Integrowanie kontroli RBAC platformy Azure z usługą AKS w celu autoryzacji kubernetes.](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC do autoryzacji dostępu do zasobu usługi AKS

Dzięki kontroli dostępu na podstawie ról platformy Azure możesz zapewnić użytkownikom (lub tożsamościom) szczegółowy dostęp do zasobów usługi AKS w co najmniej jednej subskrypcji. Możesz na przykład użyć roli [współautora Azure Kubernetes Service,](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) aby skalować i uaktualnić klaster. W międzyczasie inny użytkownik z rolą [Azure Kubernetes Service klastra](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) ma uprawnienia tylko do ściągania administratora `kubeconfig` .

Alternatywnie możesz nadać użytkownikowi ogólną rolę [Współautor.](../role-based-access-control/built-in-roles.md#contributor) Dzięki ogólnej roli Współautor użytkownicy mogą wykonywać powyższe uprawnienia i wykonywać każdą możliwą akcję w zasobie usługi AKS, z wyjątkiem zarządzania uprawnieniami.

[Użyj funkcji RBAC platformy Azure, aby zdefiniować dostęp do pliku konfiguracji platformy Kubernetes w usłudze AKS.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC for Kubernetes Authorization (wersja zapoznawcza)

Dzięki integracji RBAC platformy Azure usługa AKS będzie używać serwera webhook autoryzacji Kubernetes, aby zarządzać uprawnieniami i przypisaniami zasobów klastra Kubernetes zintegrowanymi z usługą Azure AD przy użyciu definicji ról i przypisań ról platformy Azure.

![Azure RBAC for Kubernetes authorization flow (Azure RBAC dla przepływu autoryzacji platformy Kubernetes)](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak pokazano na powyższym diagramie, podczas korzystania z integracji RBAC platformy Azure wszystkie żądania do interfejsu API Kubernetes będą postępować zgodnie z tym samym przepływem uwierzytelniania, co wyjaśniono w sekcji integracji Azure Active Directory [azure.](#azure-ad-integration) 

Jeśli tożsamość, która żąda, istnieje w usłudze Azure AD, platforma Azure będzie skojarzeń z RBAC platformy Kubernetes w celu autoryzowania żądania. Jeśli tożsamość istnieje poza usługą Azure AD (tj. kontem usługi Kubernetes), autoryzacja będzie odraczać normalne użycie kontroli RBAC platformy Kubernetes.

W tym scenariuszu użyjemy mechanizmów kontroli RBAC platformy Azure i interfejsów API do przypisywania wbudowanych ról użytkowników lub tworzenia ról niestandardowych, podobnie jak w przypadku ról platformy Kubernetes. 

Dzięki tej funkcji nie tylko udzielasz użytkownikom uprawnień do zasobu usługi AKS w różnych subskrypcjach, ale także konfigurujesz rolę i uprawnienia dla każdego z tych klastrów kontrolujących dostęp do interfejsu API kubernetes. Możesz na przykład udzielić `Azure Kubernetes Service RBAC Viewer` roli w zakresie subskrypcji. Odbiorca roli będzie mógł wyświetlić listę i pobrać wszystkie obiekty Kubernetes ze wszystkich klastrów bez ich modyfikowania.

> [!IMPORTANT]
> Przed użyciem tej funkcji należy włączyć funkcję RBAC platformy Azure dla autoryzacji platformy Kubernetes. Aby uzyskać więcej szczegółów i wskazówki krok po kroku, postępuj zgodnie z instrukcje w przewodniku [Use Azure RBAC for Kubernetes Authorization](manage-azure-rbac.md) (Używanie kontroli RBAC platformy Azure dla autoryzacji kubernetes).

#### <a name="built-in-roles"></a>Wbudowane role

AKS udostępnia następujące cztery wbudowane role. Są one podobne do wbudowanych [ról kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) z kilkoma różnicami, takich jak obsługa CLD. Zobacz pełną listę akcji dozwolonych przez każdą wbudowaną rolę [platformy Azure.](../role-based-access-control/built-in-roles.md)

| Rola                                | Opis  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Viewer  | Zezwala na dostęp tylko do odczytu, aby wyświetlić większość obiektów w przestrzeni nazw. <br> Nie zezwala na wyświetlanie ról ani powiązań ról.<br> Nie zezwala na `Secrets` wyświetlanie . Odczytywanie zawartości umożliwia dostęp do poświadczeń w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolny w przestrzeni nazw `Secrets` `ServiceAccount` `ServiceAccount` (w formie podwyżsenia poziomu uprawnień).  |
| Azure Kubernetes Service RBAC Writer | Umożliwia dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. <br> Nie zezwala na wyświetlanie lub modyfikowanie ról ani powiązań ról. <br> Umożliwia uzyskiwanie dostępu do zasobników i uruchamianie ich jako dowolnego konta usługi w przestrzeni nazw, dzięki czemu może służyć do uzyskiwania poziomów dostępu do interfejsu API dowolnego konta usługi w `Secrets` przestrzeni nazw. |
| Azure Kubernetes Service RBAC Admin  | Zezwala na dostęp administratora, który ma zostać przyznany w przestrzeni nazw. <br> Umożliwia dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym na możliwość tworzenia ról i powiązań ról w przestrzeni nazw. <br> Nie zezwala na dostęp do zapisu do limitu przydziału zasobów ani do samej przestrzeni nazw. |
| Azure Kubernetes Service RBAC Cluster Admin  | Umożliwia superuzyskonom dostęp do wykonywania dowolnych akcji na dowolnym zasobie. <br> Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |


## <a name="summary"></a>Podsumowanie

Wyświetl tabelę, aby uzyskać krótkie podsumowanie sposobu uwierzytelniania użytkowników na platformie Kubernetes po włączeniu integracji z usługą Azure AD. We wszystkich przypadkach sekwencja poleceń użytkownika to:
1. Uruchom , `az login` aby uwierzytelnić się na platformie Azure.
1. Uruchom `az aks get-credentials` , aby pobrać poświadczenia dla klastra do systemu `.kube/config` .
1. Uruchamianie `kubectl` poleceń. 
   * Pierwsze polecenie może wyzwolić uwierzytelnianie oparte na przeglądarce w celu uwierzytelnienia w klastrze, zgodnie z opisem w poniższej tabeli.

W Azure Portal znajdują się:
* Na *karcie Usługi* zarządzania rolami jest wyświetlana nazwa grant roli (przyznawanie roli RBAC platformy Azure), o której Access Control w **drugiej kolumnie.** 
* Grupa administratorów klastra usługi Azure AD jest wyświetlana na **karcie** Konfiguracja.
  * Można go również znaleźć z nazwą parametru `--aad-admin-group-object-ids` w interfejsie wiersza polecenia platformy Azure.


| Opis        | Wymagane przyznanie roli| Grupy usługi Azure AD administratora klastra | Kiedy stosować |
| -------------------|------------|----------------------------|-------------|
| Logowanie starszego administratora przy użyciu certyfikatu klienta| **Rola administratora usługi Azure Kubernetes.** Ta rola umożliwia korzystanie z flagi , która pobiera starszy certyfikat administratora klastra (spoza usługi `az aks get-credentials` `--admin` Azure [AD)](control-kubeconfig-access.md) do witryny `.kube/config` użytkownika . Jest to jedyny cel "roli administratora usługi Azure Kubernetes".|n/d|Jeśli dostęp do prawidłowej grupy usługi Azure AD z dostępem do klastra zostanie trwale zablokowany.| 
| Usługa Azure AD z ręcznymi (klastrami)wiązaniami ról| **Rola użytkownika usługi Azure Kubernetes.** Rola "Użytkownik" umożliwia `az aks get-credentials` korzystanie bez `--admin` flagi . (Jest to jedyny cel "roli użytkownika usługi Azure Kubernetes"). Wynikiem w klastrze z włączoną usługą [](control-kubeconfig-access.md) Azure AD jest pobranie pustego wpisu do usługi , który wyzwala uwierzytelnianie oparte na przeglądarce, gdy zostanie po raz `.kube/config` pierwszy użyte przez usługę `kubectl` .| Użytkownik nie należy do żadnej z tych grup. Ponieważ użytkownik nie należy do żadnych grup administratorów klastra, jego prawa będą całkowicie kontrolowane przez dowolne ustawienia RoleBindings lub ClusterRoleBindings, które zostały ustawione przez administratorów klastra. (Klaster)RoleBindings (Klaster)RoleBindings (Klastry) noszą użytkowników usługi [Azure AD lub grupy usługi Azure AD](azure-ad-rbac.md) jako swoje grupy `subjects` . Jeśli żadne takie powiązania nie zostały ustawione, użytkownik nie będzie mógł wytłaniać żadnych `kubectl` poleceń.|Jeśli potrzebujesz precyzyjnej kontroli dostępu, a nie używasz kontroli dostępu na RBAC platformy Azure do autoryzacji kubernetes. Należy pamiętać, że użytkownik, który konfiguruje powiązania, musi zalogować się za pomocą jednej z innych metod wymienionych w tej tabeli.|
| Usługa Azure AD według członka grupy administratorów| Jak wyżej|Użytkownik jest członkiem jednej z grup wymienionych tutaj. Usługa AKS automatycznie generuje klaster ClusterRoleBinding, który wiąże wszystkie wymienione grupy z rolą `cluster-admin` Kubernetes. Dzięki czemu użytkownicy w tych grupach mogą uruchamiać `kubectl` wszystkie polecenia jako `cluster-admin` .|Jeśli chcesz wygodnie przyznać użytkownikom pełne  prawa administratora i nie używasz funkcji RBAC platformy Azure do autoryzacji kubernetes.|
| Usługa Azure AD z RBAC platformy Azure na celu autoryzację kubernetes|Dwie role: <br> Najpierw rola **użytkownika usługi Azure Kubernetes** (jak powyżej). <br> Po drugie, jeden z "Azure Kubernetes Service **RBAC..."** wymienione powyżej lub własne niestandardowe alternatywy.|Pole role administratora na karcie Konfiguracja nie ma znaczenia, gdy jest włączona funkcja RBAC platformy Azure dla autoryzacji kubernetes.|Używasz kontroli RBAC platformy Azure do autoryzacji platformy Kubernetes. Takie podejście zapewnia precyzyjne sterowanie bez konieczności konfiguracji po stronie rolebindings ani clusterRoleBindings.|

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą Azure AD i kontroli RBAC na platformie Kubernetes, zobacz [Integrate Azure Active Directory with AKS (Integracja aplikacji Azure Active Directory usługą AKS).][aks-aad]
- Aby uzyskać skojarzone najlepsze rozwiązania, zobacz Best practices for authentication and authorization in AKS (Najlepsze rozwiązania dotyczące uwierzytelniania [i autoryzacji w u usługi AKS).][operator-best-practices-identity]
- Aby rozpocząć pracę z usługą Azure RBAC na użytek autoryzacji Kubernetes, zobacz Używanie kontroli dostępu na platformie Azure do autoryzowania dostępu [w ramach klastra usługi Azure Kubernetes Service (AKS).](manage-azure-rbac.md)
- Aby rozpocząć zabezpieczanie `kubeconfig` pliku, zobacz [Ograniczanie dostępu do pliku konfiguracji klastra](control-kubeconfig-access.md)

Aby uzyskać więcej informacji na temat podstawowych pojęć dotyczących rozwiązania Kubernetes i AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Zabezpieczenia kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
