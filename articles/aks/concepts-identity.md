---
title: Pojęcia — dostęp i tożsamość w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o dostępie i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym o integracji Azure Active Directory, Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) oraz rolach i powiązaniach.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 98044f6ff6311241717cb66a6e26a72702d749e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181452"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Istnieją różne sposoby uwierzytelniania, kontroli dostępu/autoryzacji i bezpiecznych klastrów Kubernetes. Przy użyciu kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC) można udzielić użytkownikom, grupom i kontom usług dostępu tylko do zasobów, których potrzebują. Dzięki usłudze Azure Kubernetes Service (AKS) można dodatkowo ulepszyć strukturę zabezpieczeń i uprawnień przy użyciu Azure Active Directory i kontroli RBAC platformy Azure. Te podejście ułatwiają Zabezpieczanie dostępu do klastra i zapewnianie minimalnych wymaganych uprawnień dla deweloperów i operatorów.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają uwierzytelnianie i przypisywanie uprawnień w programie AKS.

## <a name="aks-service-permissions"></a>Uprawnienia usługi AKS

Podczas tworzenia klastra program AKS tworzy lub modyfikuje zasoby potrzebne do utworzenia i uruchomienia klastra, takiego jak maszyny wirtualne i karty sieciowe, w imieniu użytkownika tworzącego klaster. Ta tożsamość różni się od uprawnienia tożsamości klastra, który jest tworzony podczas tworzenia klastra.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Tożsamość tworzenia i obsługi uprawnień klastra

Tożsamości tworzące i obsługujące klaster potrzebują następujących uprawnień.

| Uprawnienie | Przyczyna |
|---|---|
| Microsoft. COMPUTE/diskEncryptionSets/Read | Wymagane w celu odczytania identyfikatora zestawu szyfrowania dysków. |
| Microsoft. COMPUTE/proximityPlacementGroups/Write | Wymagane do aktualizowania grup umieszczania sąsiedztwa. |
| Microsoft. Network/applicationGateways/odczyt <br/> Microsoft. Network/applicationGateways/Write <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Wymagane do skonfigurowania bram aplikacji i przyłączenia do podsieci. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Wymagane do skonfigurowania sieciowej grupy zabezpieczeń dla podsieci przy użyciu niestandardowej sieci wirtualnej.|
| Microsoft.Network/publicIPAddresses/join/action <br/> Microsoft. Network/publicIPPrefixes/Join/Action | Wymagane do skonfigurowania wychodzących publicznych adresów IP na usługa Load Balancer w warstwie Standardowa. |
| Microsoft. OperationalInsights/Workspaces/sharedkeys/Read <br/> Microsoft. OperationalInsights/obszary robocze/odczyt <br/> Microsoft. OperationsManagement/Solutions/Write <br/> Microsoft. OperationsManagement/Solutions/Read <br/> Microsoft. ManagedIdentity/Resourceidentity/Assign/Action | Wymagane do tworzenia i aktualizowania Log Analytics obszarów roboczych oraz monitorowania platformy Azure dla kontenerów. |

### <a name="aks-cluster-identity-permissions"></a>Uprawnienia do tożsamości klastra AKS

Następujące uprawnienia są używane przez tożsamość klastra AKS, która jest tworzona i skojarzona z klastrem AKS podczas tworzenia klastra. Wszystkie uprawnienia są używane z poniższych przyczyn:

| Uprawnienie | Przyczyna |
|---|---|
| Microsoft. ContainerService/managedClusters/*  <br/> | Wymagane do tworzenia użytkowników i obsługi klastra
| Microsoft. Network/loadBalancers/Delete <br/> Microsoft. Network/loadBalancers/odczyt <br/> Microsoft. Network/loadBalancers/Write | Wymagane w celu skonfigurowania modułu równoważenia obciążenia dla usługi równoważenia. |
| Microsoft. Network/adresów publicipaddress/Delete <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | Wymagane do znajdowania i konfigurowania publicznych adresów IP dla usługi równoważenia obciążenia. |
| Microsoft.Network/publicIPAddresses/join/action | Wymagane do skonfigurowania publicznych adresów IP dla usługi równoważenia obciążenia. |
| Microsoft. Network/networkSecurityGroups/odczyt <br/> Microsoft. Network/networkSecurityGroups/Write | Wymagane do tworzenia lub usuwania reguł zabezpieczeń dla usługi równoważenia obciążenia. |
| Microsoft. COMPUTE/disks/Delete <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Microsoft. COMPUTE/Locations/DiskOperations/Read | Wymagane do skonfigurowania AzureDisks. |
| Microsoft. Storage/storageAccounts/Delete <br/> Microsoft. Storage/storageAccounts/listKeys/akcja <br/> Microsoft. Storage/storageAccounts/Read <br/> Microsoft. Storage/storageAccounts/Write <br/> Microsoft. Storage/Operations/Read | Wymagane do skonfigurowania kont magazynu dla AzureFile lub AzureDisk. |
| Microsoft. Network/routeTables/odczyt <br/> Microsoft. Network/routeTables/Routes/Delete <br/> Microsoft. Network/routeTables/Routes/Read <br/> Microsoft. Network/routeTables/Routes/write <br/> Microsoft. Network/routeTables/Write | Wymagane do skonfigurowania tabel tras i tras dla węzłów. |
| Microsoft. COMPUTE/virtualMachines/Read | Wymagane do znalezienia informacji o maszynach wirtualnych w VMAS, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| Microsoft. COMPUTE/virtualMachines/Write | Wymagane w celu dołączenia AzureDisks do maszyny wirtualnej w VMAS. |
| Microsoft. COMPUTE/virtualMachineScaleSets/Read <br/> Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/odczyt <br/> Microsoft. COMPUTE/virtualMachineScaleSets/virtualmachines/instanceView/Read | Wymagane do znalezienia informacji o maszynach wirtualnych w zestawie skalowania maszyn wirtualnych, takich jak strefy, domena błędów, rozmiar i dyski danych. |
| Microsoft. Network/networkInterfaces/Write | Wymagane do dodania maszyny wirtualnej w VMAS do puli adresów zaplecza modułu równoważenia obciążenia. |
| Microsoft. COMPUTE/virtualMachineScaleSets/Write | Wymagane do dodania zestawu skalowania maszyn wirtualnych do pul adresów zaplecza modułu równoważenia obciążenia i skalowania w poziomie węzłów w zestawie skalowania maszyn wirtualnych. |
| Microsoft. COMPUTE/virtualMachineScaleSets/virtualmachines/Write | Wymagane, aby dołączyć AzureDisks i dodać maszynę wirtualną z zestawu skalowania maszyn wirtualnych do modułu równoważenia obciążenia. |
| Microsoft. Network/networkInterfaces/odczyt | Wymagane do wyszukiwania wewnętrznych adresów IP i pul adresów zaplecza modułu równoważenia obciążenia dla maszyn wirtualnych w VMAS. |
| Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/Read | Wymagane do przeszukiwania wewnętrznych adresów IP i pul adresów zaplecza modułu równoważenia obciążenia dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfiguration/adresów publicipaddress/Read | Wymagane, aby znaleźć publiczne adresy IP dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych. |
| Microsoft. Network/virtualNetworks/odczyt <br/> Microsoft.Network/virtualNetworks/subnets/read | Wymagane do sprawdzenia, czy istnieje podsieć dla wewnętrznego modułu równoważenia obciążenia w innej grupie zasobów. |
| Microsoft. COMPUTE/snapshots/Delete <br/> Microsoft. COMPUTE/snapshots/Read <br/> Microsoft. COMPUTE/snapshots/Write | Wymagane do skonfigurowania migawek dla AzureDisk. |
| Microsoft. COMPUTE/Locations/vmSizes/Read <br/> Microsoft. COMPUTE/Locations/Operations/Read | Wymagane, aby znaleźć rozmiary maszyn wirtualnych na potrzeby znajdowania limitów woluminów AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Dodatkowe uprawnienia do tożsamości klastra

Podczas tworzenia klastra z określonymi atrybutami są niezbędne następujące dodatkowe uprawnienia. Te uprawnienia nie są automatycznie przypisywane, dlatego należy dodać te uprawnienia do tożsamości klastra po utworzeniu.

| Uprawnienie | Przyczyna |
|---|---|
| Microsoft. Network/networkSecurityGroups/Write <br/> Microsoft. Network/networkSecurityGroups/odczyt | Wymagane w przypadku używania sieciowej grupy zabezpieczeń w innej grupie zasobów. Wymagane do skonfigurowania reguł zabezpieczeń dla usługi równoważenia obciążenia. |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Wymagane w przypadku używania podsieci w innej grupie zasobów, takiej jak niestandardowa Sieć wirtualna. |
| Microsoft. Network/routeTables/Routes/Read <br/> Microsoft. Network/routeTables/Routes/write | Wymagany, jeśli używana jest podsieć skojarzona z tabelą tras w innej grupie zasobów, takiej jak niestandardowa Sieć wirtualna z niestandardową tabelą tras. Wymagane, aby sprawdzić, czy podsieć już istnieje dla podsieci w innej grupie zasobów. |
| Microsoft.Network/virtualNetworks/subnets/read | Wymagane w przypadku korzystania z wewnętrznego modułu równoważenia obciążenia w innej grupie zasobów. Wymagane, aby sprawdzić, czy podsieć już istnieje dla wewnętrznego modułu równoważenia obciążenia w grupie zasobów. |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC)

Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, Kubernetes używa kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC). Ten mechanizm kontroli pozwala przypisywać użytkowników lub grupy użytkowników, uprawnienia do wykonywania takich czynności jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchamiania obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub przyznawane przez cały klaster AKS. Za pomocą Kubernetes RBAC można utworzyć *role* do definiowania uprawnień, a następnie przypisać te role do użytkowników z *powiązaniami ról*.

Aby uzyskać więcej informacji, zobacz [using KUBERNETES RBAC Authorization][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Role i ClusterRoles

Przed przypisaniem uprawnień do użytkowników przy użyciu Kubernetes RBAC należy najpierw zdefiniować te uprawnienia jako *rolę*. Kubernetes role *udzielą* uprawnień. Nie ma koncepcji uprawnienia *Odmów* .

Role są używane do udzielania uprawnień w przestrzeni nazw. Jeśli musisz udzielić uprawnień w całym klastrze lub do zasobów klastra poza daną przestrzenią nazw, zamiast tego możesz użyć *ClusterRoles*.

ClusterRole działa w taki sam sposób, aby przyznać uprawnienia do zasobów, ale mogą być stosowane do zasobów w całym klastrze, a nie w określonym obszarze nazw.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu ról w celu udzielenia uprawnień do zasobów należy przypisać te uprawnienia Kubernetes RBAC z *roląbinding*. Jeśli klaster AKS [integruje się z usługą Azure Active Directory (Azure AD)](#azure-active-directory-integration), powiązania są przyznane użytkownikom usługi Azure AD uprawnienia do wykonywania działań w ramach klastra, zobacz jak [kontrolować dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach Kubernetes i tożsamości Azure Active Directory](azure-ad-rbac.md).

Powiązania ról służą do przypisywania ról dla danego obszaru nazw. Takie podejście umożliwia logicznie segregowanie pojedynczego klastra AKS, dzięki czemu użytkownicy mogą uzyskiwać dostęp do zasobów aplikacji w ich przypisanej przestrzeni nazw. Jeśli musisz powiązać role w całym klastrze lub z zasobami klastra spoza danego obszaru nazw, zamiast tego możesz użyć *ClusterRoleBindings*.

ClusterRoleBinding działa w taki sam sposób, aby powiązać role z użytkownikami, ale można je stosować do zasobów w całym klastrze, a nie w określonym obszarze nazw. Takie podejście umożliwia przyznanie administratorom lub inżynierom pomocy technicznej dostępu do wszystkich zasobów w klastrze AKS.


> [!NOTE]
> Wszystkie akcje klastra podejmowane przez firmę Microsoft/AKS są realizowane przy użyciu zgody użytkownika w ramach wbudowanej roli Kubernetes `aks-service` i wbudowanego powiązania roli `aks-service-rolebinding` . Ta rola umożliwia AKS Rozwiązywanie problemów z klastrem i diagnozowanie problemów, ale nie może modyfikować uprawnień ani tworzyć ról ani powiązań ról ani innych akcji o wysokim poziomie uprawnień. Dostęp do roli jest włączony tylko w ramach aktywnych biletów pomocy technicznej z dostępem just-in-Time (JIT). Przeczytaj więcej na temat [zasad pomocy technicznej AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

Jednym z podstawowych typów użytkownika w Kubernetes jest *konto usługi*. Konto usługi istnieje w usłudze i jest zarządzane przez program, interfejs API Kubernetes. Poświadczenia dla kont usług są przechowywane jako wpisy tajne Kubernetes, dzięki czemu mogą być używane przez autoryzowane zasobniki do komunikowania się z serwerem interfejsu API. Większość żądań interfejsu API zapewnia token uwierzytelniania dla konta usługi lub zwykłego konta użytkownika.

Normalne konta użytkowników zapewniają bardziej tradycyjny dostęp dla administratorów lub deweloperów, nie tylko usługi i procesy. Kubernetes sama nie udostępnia rozwiązania do zarządzania tożsamościami, w którym są przechowywane regularne konta użytkowników i hasła. Zamiast tego zewnętrzne rozwiązania tożsamości można zintegrować z usługą Kubernetes. W przypadku klastrów AKS to zintegrowane rozwiązanie do tworzenia tożsamości jest Azure Active Directory.

Aby uzyskać więcej informacji na temat opcji tożsamości w programie Kubernetes, zobacz [Kubernetes Authentication (uwierzytelnianie][kubernetes-authentication]).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Bezpieczeństwo klastrów AKS można rozszerzyć przy użyciu integracji Azure Active Directory (AD). Usługa Azure AD oparta na dekadach zarządzania tożsamościami w przedsiębiorstwie obejmuje wiele dzierżawców, opartych na chmurze katalogów i usługi zarządzania tożsamościami, które łączą podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Usługa Azure AD umożliwia integrowanie tożsamości lokalnych z klastrami AKS w celu zapewnienia jednego źródła do zarządzania kontami i zabezpieczeń.

![Integracja Azure Active Directory z klastrami AKS](media/concepts-identity/aad-integration.png)

W przypadku klastrów AKS zintegrowanych z usługą Azure AD można udzielić użytkownikom lub grupom dostępu do zasobów Kubernetes w przestrzeni nazw lub w klastrze. Aby uzyskać `kubectl` kontekst konfiguracji, użytkownik może uruchomić polecenie [AZ AKS Get-Credentials][az-aks-get-credentials] . Gdy użytkownik będzie współdziałać z klastrem AKS przy użyciu programu `kubectl` , zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi Azure AD. Takie podejście zapewnia pojedyncze Źródło poświadczeń zarządzania kontami użytkowników i hasła. Użytkownik może uzyskać dostęp tylko do zasobów zdefiniowanych przez administratora klastra.

Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][openid-connect]. W ramach klastra Kubernetes [uwierzytelnianie tokenu elementu webhook][webhook-token-docs] służy do weryfikowania tokenów uwierzytelniania. Uwierzytelnianie za pomocą tokenu elementu webhook jest konfigurowane i zarządzane w ramach klastra AKS.

### <a name="webhook-and-api-server"></a>Element webhook i serwer interfejsu API

![Przepływ uwierzytelniania elementu webhook i serwera interfejsu API](media/concepts-identity/auth-flow.png)

Jak pokazano na powyższej ilustracji, serwer interfejsu API wywołuje serwer webhook AKS i wykonuje następujące czynności:

1. Aplikacja kliencka usługi Azure AD jest używana przez polecenia kubectl do logowania użytkowników przy użyciu przepływu przypisywania autoryzacji urządzeń z uwierzytelnianiem [OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Usługa Azure AD udostępnia access_token, id_token i refresh_token.
3. Użytkownik wysyła żądanie polecenia kubectl z access_token z kubeconfig.
4. Polecenia kubectl wysyła access_token do serwera interfejsu API.
5. Serwer interfejsu API jest skonfigurowany z serwerem elementu webhook uwierzytelniania w celu przeprowadzenia walidacji.
6. Serwer elementu webhook uwierzytelniania potwierdza, że podpis tokenu sieci Web JSON jest prawidłowy, sprawdzając publiczny klucz podpisywania usługi Azure AD.
7. Aplikacja serwera używa poświadczeń dostarczonych przez użytkownika do wykonywania zapytań dotyczących członkostwa w grupach zalogowanego użytkownika z usługi MS interfejs API programu Graph.
8. Odpowiedź jest wysyłana do serwera interfejsu API z informacjami o użytkowniku, takimi jak oświadczenie głównej nazwy użytkownika (UPN) tokenu dostępu i członkostwem w grupie użytkownika na podstawie identyfikatora obiektu.
9. Interfejs API wykonuje decyzję autoryzacji na podstawie roli Kubernetes/Rolebinding.
10. Po autoryzowaniu serwer interfejsu API zwróci odpowiedź do polecenia kubectl.
11. Polecenia kubectl dostarcza użytkownikowi informacje zwrotne.
 
**Dowiedz się, jak zintegrować AKS z usługą AAD [tutaj](managed-aad.md).**

## <a name="azure-role-based-access-control-azure-rbac"></a>Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)

Azure RBAC to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure.

 Usługa Azure RBAC jest przeznaczona do pracy nad zasobami w ramach subskrypcji platformy Azure, a Kubernetes RBAC jest przeznaczony do pracy nad zasobami Kubernetes w ramach klastra AKS. 

Za pomocą usługi Azure RBAC utworzysz *definicję roli* , która zawiera opis uprawnień do zastosowania. Użytkownik lub grupa jest następnie przypisywana do tej definicji roli za pośrednictwem *przypisania roli* dla określonego *zakresu*, który może być pojedynczym zasobem, grupą zasobów lub subskrypcją.

Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?][azure-rbac]

Istnieją dwa poziomy dostępu, które są konieczne, aby w pełni obsługiwać klaster AKS: 
1. [Uzyskaj dostęp do zasobu AKS w ramach subskrypcji platformy Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). Ten proces pozwala kontrolować skalowanie lub Uaktualnianie klastra przy użyciu interfejsów API AKS oraz ściąganie kubeconfig.
2. Dostęp do interfejsu API Kubernetes. Ten dostęp jest kontrolowany przez [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (tradycyjnie) lub [integrację z usługą Azure RBAC z AKS dla autoryzacji Kubernetes](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Uwierzytelnianie RBAC platformy Azure w celu autoryzacji dostępu do zasobu AKS

Korzystając z usługi Azure RBAC, możesz udostępnić użytkownikom (lub tożsamościom) szczegółowy dostęp do zasobów AKS w ramach jednej lub kilku subskrypcji. Można na przykład mieć [rolę współautora usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) , która umożliwia wykonywanie akcji takich jak skalowanie i Uaktualnianie klastra. Inny użytkownik może mieć [rolę administratora klastra usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) , która zapewnia tylko uprawnienie do ściągania kubeconfig administratora.

Alternatywnie można dać użytkownikowi rolę rola [współautora](../role-based-access-control/built-in-roles.md#contributor) , która może obejmować powyższe uprawnienia i wszystkie akcje możliwe dla zasobu AKS z wyjątkiem zarządzania uprawnieniami.

Zobacz więcej, jak używać funkcji RBAC platformy Azure, aby zabezpieczyć dostęp do pliku kubeconfig, który zapewnia dostęp do interfejsu API Kubernetes w [tym miejscu](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC dla autoryzacji Kubernetes (wersja zapoznawcza)

Dzięki integracji z usługą Azure RBAC AKS będzie używać serwera elementu webhook autoryzacji Kubernetes, aby umożliwić zarządzanie uprawnieniami i przypisaniami zasobów klastra K8s zintegrowanych z usługą Azure AD przy użyciu definicji ról platformy Azure i przypisań ról.

![Przepływ autoryzacji usługi Azure RBAC dla Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak pokazano na powyższym diagramie, podczas korzystania z integracji usługi Azure RBAC wszystkie żądania do interfejsu API Kubernetes będą zgodne z tym samym przepływem uwierzytelniania, co wyjaśniono w [sekcji integracja Azure Active Directory](#azure-active-directory-integration). 

Jednak w takim przypadku zamiast polegać wyłącznie na Kubernetes RBAC na potrzeby autoryzacji, żądanie jest faktycznie autoryzowane przez platformę Azure, o ile tożsamość, która dokonała żądania, istnieje w usłudze AAD. Jeśli tożsamość nie istnieje w usłudze AAD, na przykład w ramach konta usługi Kubernetes, kontrola RBAC platformy Azure nie zostanie rozpoczęta i będzie standardowa kontrola RBAC Kubernetes.

W tym scenariuszu można nadać użytkownikom jedną z czterech wbudowanych ról lub tworzyć role niestandardowe w taki sam sposób, jak w przypadku ról Kubernetes, ale w tym przypadku przy użyciu mechanizmów i interfejsów API platformy Azure. 

Ta funkcja pozwoli na przykład nie tylko udzielić użytkownikom uprawnień do zasobu AKS w różnych subskrypcjach, ale skonfigurować i nadać im rolę i uprawnienia, które będą znajdować się w każdym z tych klastrów, które kontrolują dostęp do interfejsu API Kubernetes. Na przykład można udzielić `Azure Kubernetes Service RBAC Viewer` roli w zakresie subskrypcji, a jej odbiorca będzie mogła wyświetlać i pobierać wszystkie obiekty Kubernetes ze wszystkich klastrów, ale nie mogą ich modyfikować.

> [!IMPORTANT]
> Należy pamiętać, że przed użyciem tej funkcji należy włączyć uwierzytelnianie RBAC systemu Azure na potrzeby autoryzacji Kubernetes. Aby uzyskać szczegółowe informacje i wskazówki krok po kroku, [Zobacz tutaj](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Wbudowane role

AKS udostępnia następujące cztery wbudowane role. Są one podobne do [wbudowanych ról Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , ale z kilkoma różnicami, takimi jak obsługa CRDs. Aby zapoznać się z pełną listą akcji dozwolonych przez każdą wbudowaną rolę, zobacz [tutaj](../role-based-access-control/built-in-roles.md).

| Rola                                | Opis  |
|-------------------------------------|--------------|
| Przeglądarka RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie `Secrets` , ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do `ServiceAccount` poświadczeń w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolne `ServiceAccount` w przestrzeni nazw (w formie eskalacji uprawnień).  |
| Składnik zapisywania RBAC usługi Azure Kubernetes Service | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola pozwala uzyskać dostęp do `Secrets` i uruchamiać zasobniki jako dowolne ServiceAccount w przestrzeni nazw, dzięki czemu można je wykorzystać do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. |
| Administrator RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora, który ma być przyznany w przestrzeni nazw. Zezwala na dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym możliwość tworzenia ról i powiązań ról w przestrzeni nazw. Ta rola nie zezwala na dostęp do zapisu do przydziału zasobów ani do samego obszaru nazw. |
| Administrator klastra RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora do wykonywania dowolnych akcji względem dowolnego zasobu. Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |


## <a name="summary"></a>Podsumowanie

Ta tabela zawiera podsumowanie sposobów, w których użytkownicy mogą uwierzytelniać się w Kubernetes, gdy jest włączona integracja z usługą Azure AD.  We wszystkich przypadkach sekwencja poleceń użytkownika jest:
1. Uruchom `az login` w celu uwierzytelnienia na platformie Azure.
1. Uruchom `az aks get-credentials` , aby pobrać poświadczenia dla klastra do programu `.kube/config` .
1. `kubectl`Polecenia Run (pierwszy z nich mogą wyzwalać uwierzytelnianie na podstawie przeglądarki w celu uwierzytelnienia w klastrze, zgodnie z opisem w poniższej tabeli).

Przyznanym rolą w drugiej kolumnie jest przyznanie roli RBAC platformy Azure, które jest wyświetlane na karcie **Access Control** w Azure Portal. Grupa Administratorzy klastra usługi Azure AD jest wyświetlana na karcie **Konfiguracja** w portalu (lub z nazwą parametru `--aad-admin-group-object-ids` w interfejsie wiersza polecenia platformy Azure).

| Opis        | Wymagane przyznanie roli| Administratorzy klastra: grupy usługi Azure AD | Kiedy stosować |
| -------------------|------------|----------------------------|-------------|
| Starsze logowanie administratora przy użyciu certyfikatu klienta| **Rola administratora usługi Azure Kubernetes**. Ta rola umożliwia `az aks get-credentials` użycie z `--admin` flagą, która pobiera [starszy certyfikat administratora klastra (poza usługą Azure AD)](control-kubeconfig-access.md) do użytkownika `.kube/config` . Jest to jedyne przeznaczenie roli administratora usługi Azure Kubernetes.|n/d|Jeśli użytkownik jest trwale zablokowany przez nie ma dostępu do prawidłowej grupy usługi Azure AD z dostępem do klastra.| 
| Usługa Azure AD z ręcznym (klastrowym) RoleBindings| **Rola użytkownika Azure Kubernetes**. Rola "użytkownik" pozwala `az aks get-credentials` używać bez `--admin` flagi. (Jest to jedyne przeznaczenie roli użytkownika Azure Kubernetes). W przypadku klastra z obsługą usługi Azure AD jest pobierane [puste wpisy](control-kubeconfig-access.md) w usłudze `.kube/config` , które wyzwalają uwierzytelnianie oparte na przeglądarce, gdy są one najpierw używane przez program `kubectl` .| Użytkownik nie należy do żadnej z tych grup. Ze względu na to, że użytkownik nie należy do żadnych grup administratorów klastra, ich prawa będą kontrolowane w całości przez wszystkie RoleBindings lub ClusterRoleBindings, które zostały skonfigurowane przez administratorów klastrów. (Klaster) RoleBindings wyznaczanie [użytkowników usługi Azure AD lub grup usługi Azure AD](azure-ad-rbac.md) jako ich `subjects` . Jeśli nie skonfigurowano takich powiązań, użytkownik nie będzie mógł excute żadnych `kubectl` poleceń.|Jeśli chcesz uzyskać precyzyjną kontrolę dostępu i nie używasz usługi Azure RBAC do autoryzacji Kubernetes. Należy pamiętać, że użytkownik, który konfiguruje powiązania, musi zalogować się przy użyciu jednej z innych metod wymienionych w tej tabeli.|
| Usługa Azure AD według członków grupy administratorów| Jak wyżej|Użytkownik jest członkiem jednej z wymienionych tutaj grup. AKS automatycznie generuje ClusterRoleBinding, który wiąże wszystkie wymienione grupy z `cluster-admin` rolą Kubernetes. Aby użytkownicy w tych grupach mogli uruchamiać wszystkie `kubectl` polecenia jako `cluster-admin` .|Jeśli chcesz wygodnie udzielić użytkownikom pełnych praw administratora i _nie_ korzystali z usługi Azure RBAC na potrzeby autoryzacji Kubernetes.|
| Usługa Azure AD z uwierzytelnianiem RBAC platformy Azure dla usługi Kubernetes|Dwie role: najpierw, **rola użytkownika Azure Kubernetes** (jak powyżej). Druga — jedną z " **RBAC** usługi Azure Kubernetes Service..." role wymienione powyżej lub własne alternatywy niestandardowe.|Pole role administratora na karcie Konfiguracja jest nieistotne, gdy jest włączone uwierzytelnianie RBAC platformy Azure dla usługi Kubernetes.|Używasz usługi Azure RBAC do autoryzacji Kubernetes. Takie podejście zapewnia szczegółową kontrolę, bez konieczności konfigurowania RoleBindings lub ClusterRoleBindings.|

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą Azure AD i Kubernetes RBAC, zobacz [integrowanie Azure Active Directory z AKS][aks-aad].
- W przypadku skojarzonych najlepszych rozwiązań zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].
- Aby rozpocząć korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes, zobacz [Używanie usługi Azure RBAC do autoryzacji dostępu w klastrze usługi Azure Kubernetes Service (AKS)](manage-azure-rbac.md).
- Aby rozpocząć Zabezpieczanie pliku kubeconfig, zobacz [ograniczanie dostępu do pliku konfiguracji klastra](control-kubeconfig-access.md)

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
