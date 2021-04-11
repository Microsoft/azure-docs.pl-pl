---
title: Pojęcia — dostęp i tożsamość w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o dostępie i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym o integracji Azure Active Directory, Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) oraz rolach i powiązaniach.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105922"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Można uwierzytelniać, autoryzować, zabezpieczać i kontrolować dostęp do klastrów Kubernetes na wiele sposobów. 
* Przy użyciu kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC) można udzielić użytkownikom, grupom i kontom usług dostępu tylko do zasobów, których potrzebują. 
* Dzięki usłudze Azure Kubernetes Service (AKS) można dodatkowo ulepszyć strukturę zabezpieczeń i uprawnień za pośrednictwem Azure Active Directory i kontroli RBAC platformy Azure. 

Kubernetes RBAC i AKS pomagają w zabezpieczaniu dostępu do klastra i zapewniania minimalnych wymaganych uprawnień dla deweloperów i operatorów.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają uwierzytelnianie i przypisywanie uprawnień w programie AKS.

## <a name="aks-service-permissions"></a>Uprawnienia usługi AKS

Podczas tworzenia klastra program AKS generuje lub modyfikuje potrzebne zasoby (takie jak maszyny wirtualne i karty sieciowe), aby utworzyć i uruchomić klaster w imieniu użytkownika. Ta tożsamość różni się od uprawnienia tożsamości klastra, który jest tworzony podczas tworzenia klastra.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Tożsamość tworzenia i obsługi uprawnień klastra

Tożsamości tworzące i obsługujące klaster potrzebują następujących uprawnień.

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Wymagane w celu odczytania identyfikatora zestawu szyfrowania dysków. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Wymagane do aktualizowania grup umieszczania sąsiedztwa. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane do skonfigurowania bram aplikacji i przyłączenia do podsieci. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane do skonfigurowania sieciowej grupy zabezpieczeń dla podsieci przy użyciu niestandardowej sieci wirtualnej.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Wymagane do skonfigurowania wychodzących publicznych adresów IP na usługa Load Balancer w warstwie Standardowa. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Wymagane do tworzenia i aktualizowania Log Analytics obszarów roboczych oraz monitorowania platformy Azure dla kontenerów. |

### <a name="aks-cluster-identity-permissions"></a>Uprawnienia do tożsamości klastra AKS

Następujące uprawnienia są używane przez tożsamość klastra AKS, która jest tworzona i skojarzona z klastrem AKS. Wszystkie uprawnienia są używane z poniższych przyczyn:

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Wymagane do tworzenia użytkowników i obsługi klastra
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Wymagane w celu skonfigurowania modułu równoważenia obciążenia dla usługi równoważenia. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Wymagane do znajdowania i konfigurowania publicznych adresów IP dla usługi równoważenia obciążenia. |
| `Microsoft.Network/publicIPAddresses/join/action` | Wymagane do skonfigurowania publicznych adresów IP dla usługi równoważenia obciążenia. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Wymagane do tworzenia lub usuwania reguł zabezpieczeń dla usługi równoważenia obciążenia. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Wymagane do skonfigurowania AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Wymagane do skonfigurowania kont magazynu dla AzureFile lub AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Wymagane do skonfigurowania tabel tras i tras dla węzłów. |
| `Microsoft.Compute/virtualMachines/read` | Wymagane do znalezienia informacji o maszynach wirtualnych w VMAS, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| `Microsoft.Compute/virtualMachines/write` | Wymagane w celu dołączenia AzureDisks do maszyny wirtualnej w VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Wymagane do znalezienia informacji o maszynach wirtualnych w zestawie skalowania maszyn wirtualnych, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| `Microsoft.Network/networkInterfaces/write` | Wymagane do dodania maszyny wirtualnej w VMAS do puli adresów zaplecza modułu równoważenia obciążenia. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Wymagane do dodania zestawu skalowania maszyn wirtualnych do pul adresów zaplecza modułu równoważenia obciążenia i skalowania w poziomie węzłów w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Wymagane, aby dołączyć AzureDisks i dodać maszynę wirtualną z zestawu skalowania maszyn wirtualnych do modułu równoważenia obciążenia. |
| `Microsoft.Network/networkInterfaces/read` | Wymagane do wyszukiwania wewnętrznych adresów IP i pul adresów zaplecza modułu równoważenia obciążenia dla maszyn wirtualnych w VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Wymagane do przeszukiwania wewnętrznych adresów IP i pul adresów zaplecza modułu równoważenia obciążenia dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Wymagane, aby znaleźć publiczne adresy IP dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Wymagane do sprawdzenia, czy istnieje podsieć dla wewnętrznego modułu równoważenia obciążenia w innej grupie zasobów. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Wymagane do skonfigurowania migawek dla AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Wymagane, aby znaleźć rozmiary maszyn wirtualnych na potrzeby znajdowania limitów woluminów AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Dodatkowe uprawnienia do tożsamości klastra

Podczas tworzenia klastra o określonych atrybutach będą potrzebne następujące dodatkowe uprawnienia do tożsamości klastra. Ponieważ te uprawnienia nie są automatycznie przypisywane, należy je dodać do tożsamości klastra po jej utworzeniu.

| Uprawnienie | Przyczyna |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Wymagane w przypadku używania sieciowej grupy zabezpieczeń w innej grupie zasobów. Wymagane do skonfigurowania reguł zabezpieczeń dla usługi równoważenia obciążenia. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Wymagane w przypadku używania podsieci w innej grupie zasobów, takiej jak niestandardowa Sieć wirtualna. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Wymagany, jeśli używana jest podsieć skojarzona z tabelą tras w innej grupie zasobów, takiej jak niestandardowa Sieć wirtualna z niestandardową tabelą tras. Wymagane, aby sprawdzić, czy podsieć już istnieje dla podsieci w innej grupie zasobów. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Wymagane w przypadku korzystania z wewnętrznego modułu równoważenia obciążenia w innej grupie zasobów. Wymagane, aby sprawdzić, czy podsieć już istnieje dla wewnętrznego modułu równoważenia obciążenia w grupie zasobów. |
| `Microsoft.Network/privatednszones/*` | Wymagane w przypadku używania prywatnej strefy DNS w innej grupie zasobów, takiej jak niestandardowy privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kontrola dostępu na podstawie ról na platformie Kubernetes

Kubernetes RBAC zapewnia szczegółowe filtrowanie akcji użytkownika. Mechanizm kontroli:
* Przypisujesz użytkownikom lub grupom użytkowników uprawnienia do tworzenia i modyfikowania zasobów lub wyświetlania dzienników z uruchamiania obciążeń aplikacji. 
* Można zakres uprawnień do pojedynczej przestrzeni nazw lub całego klastra AKS. 
* Tworzysz *role* , aby definiować uprawnienia, a następnie przypisuj te role do użytkowników z *powiązaniami ról*.

Aby uzyskać więcej informacji, zobacz [using KUBERNETES RBAC Authorization][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Role i ClusterRoles

#### <a name="roles"></a>Role
Przed przypisaniem uprawnień do użytkowników za pomocą Kubernetes RBAC należy zdefiniować uprawnienia użytkownika jako *rolę*. Udzielanie uprawnień w przestrzeni nazw za pomocą ról. 

> [!NOTE]
> Role Kubernetes *przyznają* uprawnienia; nie *odmówią* uprawnień.

Aby udzielić uprawnień w całym klastrze lub do zasobów klastra poza daną przestrzenią nazw, zamiast tego można użyć *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

ClusterRole przyznaje i stosuje uprawnienia do zasobów w całym klastrze, a nie w określonej przestrzeni nazw.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu ról w celu udzielenia uprawnień do zasobów należy przypisać te uprawnienia Kubernetes RBAC z *roląbinding*. Jeśli klaster AKS [integruje się z usługą Azure Active Directory (Azure AD)](#azure-ad-integration), RoleBindings przyznać uprawnienia użytkownikom usługi Azure AD w celu wykonywania akcji w ramach klastra. Zobacz jak [kontrolować dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach Kubernetes i tożsamości Azure Active Directory](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Przypisz role do użytkowników w danej przestrzeni nazw za pomocą RoleBindings. Za pomocą RoleBindings można logicznie oddzielić pojedynczy klaster AKS, umożliwiając użytkownikom dostęp do zasobów aplikacji w ich przypisanej przestrzeni nazw. 

Aby powiązać role w całym klastrze lub z zasobami klastra spoza danego obszaru nazw, zamiast tego należy użyć *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Za pomocą ClusterRoleBinding można powiązać role z użytkownikami i stosować je w całym klastrze, a nie w określonej przestrzeni nazw. Takie podejście umożliwia przyznanie administratorom lub inżynierom pomocy technicznej dostępu do wszystkich zasobów w klastrze AKS.


> [!NOTE]
> Firma Microsoft/AKS wykonuje wszelkie akcje klastra z zgodą użytkownika w ramach wbudowanej roli Kubernetes `aks-service` i wbudowanego powiązania roli `aks-service-rolebinding` . 
> 
> Ta rola umożliwia AKS Rozwiązywanie problemów z klastrem i diagnozowanie problemów, ale nie może modyfikować uprawnień ani tworzyć ról ani powiązań ról ani innych akcji o wysokim poziomie uprawnień. Dostęp do roli jest włączony tylko w ramach aktywnych biletów pomocy technicznej z dostępem just-in-Time (JIT). Przeczytaj więcej na temat [zasad pomocy technicznej AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

*Konta usług* są jednym z podstawowych typów użytkownika w Kubernetes. Interfejs API Kubernetes przechowuje konta usług i zarządza nimi. Poświadczenia konta usługi są przechowywane jako wpisy tajne Kubernetes, dzięki czemu mogą być używane przez autoryzowane zasobniki do komunikowania się z serwerem interfejsu API. Większość żądań interfejsu API zapewnia token uwierzytelniania dla konta usługi lub zwykłego konta użytkownika.

Normalne konta użytkowników zapewniają bardziej tradycyjny dostęp dla administratorów lub deweloperów, a nie tylko usługi i procesy. Mimo że usługa Kubernetes nie udostępnia rozwiązania do zarządzania tożsamościami do przechowywania zwykłych kont użytkowników i haseł, można zintegrować zewnętrzne rozwiązania do obsługi tożsamości w Kubernetes. W przypadku klastrów AKS to zintegrowane rozwiązanie do tworzenia tożsamości to usługa Azure AD.

Aby uzyskać więcej informacji na temat opcji tożsamości w programie Kubernetes, zobacz [Kubernetes Authentication (uwierzytelnianie][kubernetes-authentication]).

## <a name="azure-ad-integration"></a>Integracja z usługą Azure AD

Zwiększ bezpieczeństwo klastra AKS dzięki integracji z usługą Azure AD. Usługa Azure AD utworzona na podstawie dekady zarządzania tożsamościami w przedsiębiorstwie obejmuje wiele dzierżawców, opartych na chmurze usługi zarządzania tożsamościami, które łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Usługa Azure AD umożliwia integrowanie tożsamości lokalnych z klastrami AKS w celu zapewnienia jednego źródła do zarządzania kontami i zabezpieczeń.

![Integracja Azure Active Directory z klastrami AKS](media/concepts-identity/aad-integration.png)

W przypadku klastrów AKS zintegrowanych z usługą Azure AD można udzielić użytkownikom lub grupom dostępu do zasobów Kubernetes w przestrzeni nazw lub w klastrze. 

1. Aby uzyskać `kubectl` kontekst konfiguracji, użytkownik uruchamia polecenie [AZ AKS Get-Credentials][az-aks-get-credentials] . 
1. Gdy użytkownik współdziała z klastrem AKS przy użyciu programu `kubectl` , zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi Azure AD. 

Takie podejście zapewnia pojedyncze Źródło poświadczeń zarządzania kontami użytkowników i hasła. Użytkownik może uzyskać dostęp tylko do zasobów zdefiniowanych przez administratora klastra.

Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][openid-connect]. W ramach klastra Kubernetes [uwierzytelnianie tokenu elementu webhook][webhook-token-docs] służy do weryfikowania tokenów uwierzytelniania. Uwierzytelnianie za pomocą tokenu elementu webhook jest konfigurowane i zarządzane w ramach klastra AKS.

### <a name="webhook-and-api-server"></a>Element webhook i serwer interfejsu API

![Przepływ uwierzytelniania elementu webhook i serwera interfejsu API](media/concepts-identity/auth-flow.png)

Jak pokazano na powyższej ilustracji, serwer interfejsu API wywołuje serwer webhook AKS i wykonuje następujące czynności:

1. `kubectl` używa aplikacji klienckiej usługi Azure AD do logowania użytkowników przy użyciu przepływu przypisywania autoryzacji urządzeń z uwierzytelnianiem [OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Usługa Azure AD udostępnia access_token, id_token i refresh_token.
3. Użytkownik wysyła żądanie do `kubectl` access_token od `kubeconfig` .
4. `kubectl` wysyła access_token do serwera interfejsu API.
5. Serwer interfejsu API jest skonfigurowany z serwerem elementu webhook uwierzytelniania w celu przeprowadzenia walidacji.
6. Serwer elementu webhook uwierzytelniania potwierdza, że podpis tokenu sieci Web JSON jest prawidłowy, sprawdzając publiczny klucz podpisywania usługi Azure AD.
7. Aplikacja serwera używa poświadczeń dostarczonych przez użytkownika do wykonywania zapytań dotyczących członkostwa w grupach zalogowanego użytkownika z usługi MS interfejs API programu Graph.
8. Odpowiedź jest wysyłana do serwera interfejsu API z informacjami o użytkowniku, takimi jak oświadczenie głównej nazwy użytkownika (UPN) tokenu dostępu i członkostwem w grupie użytkownika na podstawie identyfikatora obiektu.
9. Interfejs API wykonuje decyzję autoryzacji na podstawie roli Kubernetes/Rolebinding.
10. Po autoryzowaniu serwer interfejsu API zwróci odpowiedź na `kubectl` .
11. `kubectl` przekazuje Użytkownikowi informacje zwrotne.
 
Dowiedz się, jak zintegrować usługę AKS z usługą Azure AD za pomocą naszego [przewodnika z integracją usługi Azure AD zarządzanego](managed-aad.md)przez program AKS.

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Kontrola dostępu oparta na rolach (RBAC) na platformie Azure to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure.

| System RBAC | Opis |
|---|---|
| Kontrola dostępu na podstawie ról na platformie Kubernetes | Zaprojektowana do pracy nad zasobami Kubernetes w klastrze AKS. |
| Kontrola dostępu na podstawie ról platformy Azure | Zaprojektowana do pracy nad zasobami w ramach subskrypcji platformy Azure. |

Za pomocą usługi Azure RBAC utworzysz *definicję roli* , która zawiera opis uprawnień do zastosowania. Następnie można przypisać użytkownika lub zgrupować tę definicję roli za pośrednictwem *przypisania roli* dla określonego *zakresu*. Zakres może być pojedynczym zasobem, grupą zasobów lub między subskrypcją.

Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?][azure-rbac]

Istnieją dwa poziomy dostępu, które są konieczne, aby w pełni obsługiwać klaster AKS: 
* [Uzyskaj dostęp do zasobu AKS w ramach subskrypcji platformy Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Kontrolowanie skalowania lub Uaktualnianie klastra przy użyciu interfejsów API AKS.
  * Ściągnij `kubeconfig` .
* Dostęp do interfejsu API Kubernetes. Ten dostęp jest kontrolowany przez:
  * [KUBERNETES RBAC](#kubernetes-rbac) (tradycyjnie).
  * [Integrowanie usługi Azure RBAC z usługą AKS na potrzeby autoryzacji Kubernetes](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Uwierzytelnianie RBAC platformy Azure w celu autoryzacji dostępu do zasobu AKS

Korzystając z usługi Azure RBAC, możesz udostępnić użytkownikom (lub tożsamościom) szczegółowy dostęp do zasobów AKS w ramach jednej lub kilku subskrypcji. Można na przykład użyć [roli współautor usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) do skalowania i uaktualniania klastra. Tymczasem inny użytkownik z [rolą administratora klastra usługi Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) ma uprawnienia do ściągania administratora `kubeconfig` .

Alternatywnie można nadać użytkownikowi rolę rola [współautor](../role-based-access-control/built-in-roles.md#contributor) ogólna. Za pomocą ogólnej roli współautor użytkownicy mogą wykonywać powyższe uprawnienia i wszystkie akcje możliwe dla zasobu AKS, z wyjątkiem zarządzania uprawnieniami.

Za [pomocą usługi Azure RBAC Zdefiniuj dostęp do pliku konfiguracji Kubernetes w AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC dla autoryzacji Kubernetes (wersja zapoznawcza)

Dzięki integracji z usługą Azure RBAC AKS będzie używać serwera elementu webhook autoryzacji Kubernetes, dzięki czemu można zarządzać uprawnieniami i przypisaniami zasobów klastra zintegrowanym z usługą Azure AD, korzystając z definicji ról i przypisań ról platformy Azure.

![Przepływ autoryzacji usługi Azure RBAC dla Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak pokazano na powyższym diagramie, podczas korzystania z integracji z usługą Azure RBAC wszystkie żądania kierowane do interfejsu API Kubernetes będą zgodne z tym samym przepływem uwierzytelniania, co zostało opisane w [sekcji integracja Azure Active Directory](#azure-ad-integration). 

Jeśli tożsamość wysyłająca żądanie istnieje w usłudze Azure AD, platforma Azure będzie mogła autoryzować żądanie przy użyciu Kubernetes RBAC. Jeśli tożsamość istnieje poza usługą Azure AD (tj. kontem usługi Kubernetes), autoryzacja pozostanie w normalnym Kubernetes RBAC.

W tym scenariuszu używane są mechanizmy i interfejsy API platformy Azure do przypisywania ról wbudowanych użytkowników lub tworzenia ról niestandardowych w taki sam sposób jak w przypadku ról Kubernetes. 

Korzystając z tej funkcji, nie tylko przyznasz użytkownikom uprawnienia do zasobu AKS w różnych subskrypcjach, ale konfigurujesz także rolę i uprawnienia dla każdego z tych klastrów kontrolujących dostęp do interfejsu API Kubernetes. Na przykład można udzielić `Azure Kubernetes Service RBAC Viewer` roli w zakresie subskrypcji. Odbiorca roli będzie mógł wyświetlać i pobierać wszystkie obiekty Kubernetes ze wszystkich klastrów bez konieczności ich modyfikowania.

> [!IMPORTANT]
> Przed rozpoczęciem korzystania z tej funkcji należy włączyć uwierzytelnianie RBAC systemu Azure na potrzeby autoryzacji Kubernetes. Aby uzyskać szczegółowe informacje i wskazówki krok po kroku, Skorzystaj z naszego przewodnika dotyczącego [autoryzacji usługi Azure Kubernetes](manage-azure-rbac.md) .

#### <a name="built-in-roles"></a>Wbudowane role

AKS udostępnia następujące cztery wbudowane role. Są one podobne do [wbudowanych ról Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) z kilkoma różnicami, takimi jak obsługa CRDs. Zapoznaj się z pełną listą akcji dozwolonych przez każdą [wbudowaną rolę platformy Azure](../role-based-access-control/built-in-roles.md).

| Rola                                | Opis  |
|-------------------------------------|--------------|
| Przeglądarka RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. <br> Nie zezwala na wyświetlanie ról ani powiązań ról.<br> Nie zezwala na wyświetlanie `Secrets` . Odczytywanie `Secrets` zawartości umożliwia dostęp do `ServiceAccount` poświadczeń w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolne `ServiceAccount` w przestrzeni nazw (w formie eskalacji uprawnień).  |
| Składnik zapisywania RBAC usługi Azure Kubernetes Service | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. <br> Nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. <br> Zezwala na dostęp do `Secrets` i uruchamianie zasobników jako dowolne konto ServiceAccount w przestrzeni nazw, dlatego może służyć do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. |
| Administrator RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora, który ma być przyznany w przestrzeni nazw. <br> Zezwala na dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym możliwość tworzenia ról i powiązań ról w przestrzeni nazw. <br> Nie zezwala na dostęp do zapisu do przydziału zasobów lub do samego obszaru nazw. |
| Administrator klastra RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora do wykonywania dowolnych akcji względem dowolnego zasobu. <br> Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |


## <a name="summary"></a>Podsumowanie

Wyświetl tabelę, aby zapoznać się z krótkim podsumowaniem, jak użytkownicy mogą uwierzytelniać się w usłudze Kubernetes, gdy jest włączona integracja z usługą Azure AD. We wszystkich przypadkach sekwencja poleceń użytkownika jest:
1. Uruchom `az login` w celu uwierzytelnienia na platformie Azure.
1. Uruchom `az aks get-credentials` , aby pobrać poświadczenia dla klastra do programu `.kube/config` .
1. Uruchom `kubectl` polecenia. 
   * Pierwsze polecenie może wyzwolić uwierzytelnianie na podstawie przeglądarki w celu uwierzytelnienia w klastrze, zgodnie z opisem w poniższej tabeli.

W Azure Portal można znaleźć następujące informacje:
* *Przyznanie roli* (przyznanie roli RBAC platformy Azure), do którego odwołuje się w drugiej kolumnie, jest wyświetlane na karcie **Access Control** . 
* Grupa Administratorzy klastra usługi Azure AD jest wyświetlana na karcie **Konfiguracja** .
  * Znaleziono również nazwę parametru `--aad-admin-group-object-ids` w interfejsie wiersza polecenia platformy Azure.


| Opis        | Wymagane przyznanie roli| Administratorzy klastra: grupy usługi Azure AD | Kiedy stosować |
| -------------------|------------|----------------------------|-------------|
| Starsze logowanie administratora przy użyciu certyfikatu klienta| **Rola administratora usługi Azure Kubernetes**. Ta rola umożliwia `az aks get-credentials` użycie z `--admin` flagą, która pobiera [starszy certyfikat administratora klastra (poza usługą Azure AD)](control-kubeconfig-access.md) do użytkownika `.kube/config` . Jest to jedyne przeznaczenie roli administratora usługi Azure Kubernetes.|n/d|Jeśli użytkownik jest trwale zablokowany przez nie ma dostępu do prawidłowej grupy usługi Azure AD z dostępem do klastra.| 
| Usługa Azure AD z ręcznym (klastrowym) RoleBindings| **Rola użytkownika Azure Kubernetes**. Rola "użytkownik" pozwala `az aks get-credentials` używać bez `--admin` flagi. (Jest to jedyne przeznaczenie roli użytkownika Azure Kubernetes). W przypadku klastra z obsługą usługi Azure AD jest pobierane [puste wpisy](control-kubeconfig-access.md) w usłudze `.kube/config` , które wyzwalają uwierzytelnianie oparte na przeglądarce, gdy są one najpierw używane przez program `kubectl` .| Użytkownik nie należy do żadnej z tych grup. Ze względu na to, że użytkownik nie należy do żadnych grup administratorów klastra, ich prawa będą kontrolowane w całości przez wszystkie RoleBindings lub ClusterRoleBindings, które zostały skonfigurowane przez administratorów klastrów. (Klaster) RoleBindings wyznaczanie [użytkowników usługi Azure AD lub grup usługi Azure AD](azure-ad-rbac.md) jako ich `subjects` . Jeśli nie skonfigurowano takich powiązań, użytkownik nie będzie mógł excute żadnych `kubectl` poleceń.|Jeśli chcesz uzyskać precyzyjną kontrolę dostępu i nie używasz usługi Azure RBAC do autoryzacji Kubernetes. Należy pamiętać, że użytkownik, który konfiguruje powiązania, musi zalogować się przy użyciu jednej z innych metod wymienionych w tej tabeli.|
| Usługa Azure AD według członków grupy administratorów| Jak wyżej|Użytkownik jest członkiem jednej z wymienionych tutaj grup. AKS automatycznie generuje ClusterRoleBinding, który wiąże wszystkie wymienione grupy z `cluster-admin` rolą Kubernetes. Aby użytkownicy w tych grupach mogli uruchamiać wszystkie `kubectl` polecenia jako `cluster-admin` .|Jeśli chcesz wygodnie udzielić użytkownikom pełnych praw administratora i _nie_ korzystali z usługi Azure RBAC na potrzeby autoryzacji Kubernetes.|
| Usługa Azure AD z uwierzytelnianiem RBAC platformy Azure dla usługi Kubernetes|Dwie role: <br> Najpierw **rola użytkownika Azure Kubernetes** (jak powyżej). <br> Druga — jedną z " **RBAC** usługi Azure Kubernetes Service..." role wymienione powyżej lub własne alternatywy niestandardowe.|Pole role administratora na karcie Konfiguracja jest nieistotne, gdy jest włączone uwierzytelnianie RBAC platformy Azure dla usługi Kubernetes.|Używasz usługi Azure RBAC do autoryzacji Kubernetes. Takie podejście zapewnia szczegółową kontrolę, bez konieczności konfigurowania RoleBindings lub ClusterRoleBindings.|

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą Azure AD i Kubernetes RBAC, zobacz [integrowanie Azure Active Directory z AKS][aks-aad].
- W przypadku skojarzonych najlepszych rozwiązań zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].
- Aby rozpocząć korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes, zobacz [Używanie usługi Azure RBAC do autoryzacji dostępu w klastrze usługi Azure Kubernetes Service (AKS)](manage-azure-rbac.md).
- Aby rozpocząć Zabezpieczanie `kubeconfig` pliku, zobacz [ograniczanie dostępu do pliku konfiguracji klastra](control-kubeconfig-access.md)

Aby uzyskać więcej informacji na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

- [Kubernetes/AKS klastrów i obciążeń][aks-concepts-clusters-workloads]
- [Zabezpieczenia Kubernetes/AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes/AKS][aks-concepts-network]
- [Magazyn Kubernetes/AKS][aks-concepts-storage]
- [Skala Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
