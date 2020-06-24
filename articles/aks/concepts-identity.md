---
title: Pojęcia — dostęp i tożsamość w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o dostępie i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym o integracji Azure Active Directory, Kubernetes kontroli dostępu opartej na rolach (RBAC) oraz rolach i powiązaniach.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84690425"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Istnieją różne sposoby uwierzytelniania przy użyciu i bezpiecznych klastrów Kubernetes. Za pomocą kontroli dostępu opartej na rolach (RBAC) można udzielić użytkownikom lub grupom dostępu tylko do zasobów, których potrzebują. Dzięki usłudze Azure Kubernetes Service (AKS) można dodatkowo ulepszyć strukturę zabezpieczeń i uprawnień przy użyciu Azure Active Directory. Te podejścia ułatwiają Zabezpieczanie obciążeń aplikacji i danych klientów.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają uwierzytelnianie i przypisywanie uprawnień w programie AKS:

- [Konta usługi Kubernetes](#kubernetes-service-accounts)
- [Integracja z usługą Azure Active Directory](#azure-active-directory-integration)
- [Kontrola dostępu oparta na rolach (RBAC)](#role-based-access-controls-rbac)
- [Role i ClusterRoles](#roles-and-clusterroles)
- [RoleBindings i ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Konta usługi Kubernetes

Jednym z podstawowych typów użytkownika w Kubernetes jest *konto usługi*. Konto usługi istnieje w usłudze i jest zarządzane przez program, interfejs API Kubernetes. Poświadczenia dla kont usług są przechowywane jako wpisy tajne Kubernetes, dzięki czemu mogą być używane przez autoryzowane zasobniki do komunikowania się z serwerem interfejsu API. Większość żądań interfejsu API zapewnia token uwierzytelniania dla konta usługi lub zwykłego konta użytkownika.

Normalne konta użytkowników zapewniają bardziej tradycyjny dostęp dla administratorów lub deweloperów, a nie tylko usługi i procesy. Kubernetes sama nie udostępnia rozwiązania do zarządzania tożsamościami, w którym są przechowywane regularne konta użytkowników i hasła. Zamiast tego zewnętrzne rozwiązania tożsamości można zintegrować z usługą Kubernetes. W przypadku klastrów AKS to zintegrowane rozwiązanie do tworzenia tożsamości jest Azure Active Directory.

Aby uzyskać więcej informacji na temat opcji tożsamości w programie Kubernetes, zobacz [Kubernetes Authentication (uwierzytelnianie][kubernetes-authentication]).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Bezpieczeństwo klastrów AKS można rozszerzyć przy użyciu integracji Azure Active Directory (AD). Usługa Azure AD oparta na dekadach zarządzania tożsamościami w przedsiębiorstwie obejmuje wiele dzierżawców, opartych na chmurze katalogów i usługi zarządzania tożsamościami, które łączą podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości. Usługa Azure AD umożliwia integrowanie tożsamości lokalnych z klastrami AKS w celu zapewnienia jednego źródła do zarządzania kontami i zabezpieczeń.

![Integracja Azure Active Directory z klastrami AKS](media/concepts-identity/aad-integration.png)

W przypadku klastrów AKS zintegrowanych z usługą Azure AD można udzielić użytkownikom lub grupom dostępu do zasobów Kubernetes w przestrzeni nazw lub w klastrze. Aby uzyskać `kubectl` kontekst konfiguracji, użytkownik może uruchomić polecenie [AZ AKS Get-Credentials][az-aks-get-credentials] . Gdy użytkownik będzie współdziałać z klastrem AKS przy użyciu programu `kubectl` , zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi Azure AD. Takie podejście zapewnia pojedyncze Źródło poświadczeń zarządzania kontami użytkowników i hasła. Użytkownik może uzyskać dostęp tylko do zasobów zdefiniowanych przez administratora klastra.

Uwierzytelnianie usługi Azure AD w klastrach AKS używa programu OpenID Connect Connect, czyli warstwy tożsamości opartej na protokole OAuth 2,0. Uwierzytelnianie OAuth 2,0 definiuje mechanizmy uzyskiwania i używania tokenów dostępu w celu uzyskiwania dostępu do chronionych zasobów, a ponadto OpenID Connect nawiązuje połączenie jako rozszerzenie procesu autoryzacji OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][openid-connect]. Aby sprawdzić tokeny uwierzytelniania uzyskane z usługi Azure AD za pośrednictwem programu OpenID Connect Connect, klastry AKS używają uwierzytelniania tokenów elementu webhook. Aby uzyskać więcej informacji, zobacz [dokumentację uwierzytelniania tokenu elementu webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, Kubernetes używa kontroli dostępu opartej na rolach (RBAC). Ten mechanizm kontroli pozwala przypisywać użytkowników lub grupy użytkowników, uprawnienia do wykonywania takich czynności jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchamiania obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub przyznawane przez cały klaster AKS. Za pomocą Kubernetes RBAC można utworzyć *role* do definiowania uprawnień, a następnie przypisać te role do użytkowników z *powiązaniami ról*.

Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Kontrola dostępu oparta na rolach (RBAC) na platformie Azure
Jednym z dodatkowych mechanizmów kontroli dostępu do zasobów jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure. Kubernetes RBAC jest przeznaczony do pracy nad zasobami w ramach klastra AKS, a usługa Azure RBAC jest przeznaczona do pracy nad zasobami w ramach subskrypcji platformy Azure. Za pomocą usługi Azure RBAC utworzysz *definicję roli* , która zawiera opis uprawnień do zastosowania. Użytkownik lub grupa ma następnie przypisaną tę definicję roli dla określonego *zakresu*, co może być pojedynczym zasobem, grupą zasobów lub subskrypcją.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Role i ClusterRoles

Przed przypisaniem uprawnień do użytkowników przy użyciu Kubernetes RBAC należy najpierw zdefiniować te uprawnienia jako *rolę*. Kubernetes role *udzielą* uprawnień. Nie ma koncepcji uprawnienia *Odmów* .

Role są używane do udzielania uprawnień w przestrzeni nazw. Jeśli musisz udzielić uprawnień w całym klastrze lub do zasobów klastra poza daną przestrzenią nazw, zamiast tego możesz użyć *ClusterRoles*.

ClusterRole działa w taki sam sposób, aby przyznać uprawnienia do zasobów, ale mogą być stosowane do zasobów w całym klastrze, a nie w określonym obszarze nazw.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu ról w celu udzielenia uprawnień do zasobów należy przypisać te uprawnienia Kubernetes RBAC z *roląbinding*. Jeśli klaster AKS integruje się z Azure Active Directory, powiązania są przyznane użytkownikom usługi Azure AD uprawnienia do wykonywania działań w ramach klastra.

Powiązania ról służą do przypisywania ról dla danego obszaru nazw. Takie podejście umożliwia logicznie segregowanie pojedynczego klastra AKS, dzięki czemu użytkownicy mogą uzyskiwać dostęp do zasobów aplikacji w ich przypisanej przestrzeni nazw. Jeśli musisz powiązać role w całym klastrze lub z zasobami klastra spoza danego obszaru nazw, zamiast tego możesz użyć *ClusterRoleBindings*.

ClusterRoleBinding działa w taki sam sposób, aby powiązać role z użytkownikami, ale można je stosować do zasobów w całym klastrze, a nie w określonym obszarze nazw. Takie podejście umożliwia przyznanie administratorom lub inżynierom pomocy technicznej dostępu do wszystkich zasobów w klastrze AKS.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure AD i Kubernetes RBAC, zobacz [integrowanie Azure Active Directory z AKS][aks-aad].

W przypadku skojarzonych najlepszych rozwiązań zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć związanych z Kubernetes i AKS, zobacz następujące artykuły:

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
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
