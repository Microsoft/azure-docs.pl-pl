---
title: Pojęcia — dostęp i tożsamość w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o dostępie i tożsamości w usłudze Azure Kubernetes Service (AKS), w tym o integracji Azure Active Directory, Kubernetes kontroli dostępu opartej na rolach (RBAC) oraz rolach i powiązaniach.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 5013f8b7dd88340e397fd3d4d4cd93d4b911fbbb
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378231"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)

Istnieją różne sposoby uwierzytelniania, kontroli dostępu/autoryzacji i bezpiecznych klastrów Kubernetes. Za pomocą kontroli dostępu opartej na rolach (RBAC) Kubernetes można udzielić użytkownikom, grupom i kontom usług dostępu tylko do zasobów, których potrzebują. Dzięki usłudze Azure Kubernetes Service (AKS) można dodatkowo ulepszyć strukturę zabezpieczeń i uprawnień przy użyciu Azure Active Directory i kontroli RBAC platformy Azure. Te podejście ułatwiają Zabezpieczanie dostępu do klastra i zapewnianie minimalnych wymaganych uprawnień dla deweloperów i operatorów.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają uwierzytelnianie i przypisywanie uprawnień w programie AKS:

- [Kubernetes kontroli dostępu opartej na rolach (RBAC)](#kubernetes-role-based-access-control-rbac)
  - [Role i ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings i ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Konta usługi Kubernetes](#kubernetes-service-accounts)
- [Integracja z usługą Azure Active Directory](#azure-active-directory-integration)
- [Kontrola dostępu na podstawie ról platformy Azure](#azure-role-based-access-control-azure-rbac)
  - [Uwierzytelnianie RBAC platformy Azure w celu autoryzacji dostępu do zasobu AKS](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Azure RBAC dla autoryzacji Kubernetes (wersja zapoznawcza)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-control-rbac"></a>Kubernetes kontroli dostępu opartej na rolach (RBAC)

Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, Kubernetes używa kontroli dostępu opartej na rolach (RBAC). Ten mechanizm kontroli pozwala przypisywać użytkowników lub grupy użytkowników, uprawnienia do wykonywania takich czynności jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchamiania obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub przyznawane przez cały klaster AKS. Za pomocą Kubernetes RBAC można utworzyć *role* do definiowania uprawnień, a następnie przypisać te role do użytkowników z *powiązaniami ról*.

Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][kubernetes-rbac].


### <a name="roles-and-clusterroles"></a>Role i ClusterRoles

Przed przypisaniem uprawnień do użytkowników przy użyciu Kubernetes RBAC należy najpierw zdefiniować te uprawnienia jako *rolę*. Kubernetes role *udzielą* uprawnień. Nie ma koncepcji uprawnienia *Odmów* .

Role są używane do udzielania uprawnień w przestrzeni nazw. Jeśli musisz udzielić uprawnień w całym klastrze lub do zasobów klastra poza daną przestrzenią nazw, zamiast tego możesz użyć *ClusterRoles*.

ClusterRole działa w taki sam sposób, aby przyznać uprawnienia do zasobów, ale mogą być stosowane do zasobów w całym klastrze, a nie w określonym obszarze nazw.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings i ClusterRoleBindings

Po zdefiniowaniu ról w celu udzielenia uprawnień do zasobów należy przypisać te uprawnienia Kubernetes RBAC z *roląbinding*. Jeśli klaster AKS [integruje się z Azure Active Directory](#azure-active-directory-integration), powiązania są przyznanymi użytkownikom usługi Azure AD uprawnienia do wykonywania działań w ramach klastra, zobacz jak [kontrolować dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory](azure-ad-rbac.md).

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
4. Polecenia kubectl wysyła access_token do APIServer.
5. Serwer interfejsu API jest skonfigurowany z serwerem elementu webhook uwierzytelniania w celu przeprowadzenia walidacji.
6. Serwer elementu webhook uwierzytelniania potwierdza, że podpis tokenu sieci Web JSON jest prawidłowy, sprawdzając publiczny klucz podpisywania usługi Azure AD.
7. Aplikacja serwera używa poświadczeń dostarczonych przez użytkownika do wykonywania zapytań dotyczących członkostwa w grupach zalogowanego użytkownika z usługi MS interfejs API programu Graph.
8. Odpowiedź jest wysyłana do APIServer z informacjami o użytkowniku, takimi jak oświadczenie głównej nazwy użytkownika (UPN) tokenu dostępu i członkostwem w grupie użytkownika na podstawie identyfikatora obiektu.
9. Interfejs API wykonuje decyzję autoryzacji na podstawie roli Kubernetes/Rolebinding.
10. Po autoryzowaniu serwer interfejsu API zwróci odpowiedź do polecenia kubectl.
11. Polecenia kubectl dostarcza użytkownikowi informacje zwrotne.
 
**Dowiedz się, jak zintegrować AKS z usługą AAD [tutaj](managed-aad.md).**

## <a name="azure-role-based-access-control-azure-rbac"></a>Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)

Azure RBAC to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , który zapewnia precyzyjne zarządzanie dostępem do zasobów platformy Azure.

 Usługa Azure RBAC jest przeznaczona do pracy nad zasobami w ramach subskrypcji platformy Azure, a Kubernetes RBAC jest przeznaczony do pracy nad zasobami Kubernetes w ramach klastra AKS. 

Za pomocą usługi Azure RBAC utworzysz *definicję roli* , która zawiera opis uprawnień do zastosowania. Użytkownik lub grupa jest następnie przypisywana do tej definicji roli za pośrednictwem *przypisania roli* dla określonego *zakresu* , który może być pojedynczym zasobem, grupą zasobów lub subskrypcją.

Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?][azure-rbac]

Istnieją dwa poziomy dostępu, które są konieczne, aby w pełni obsługiwać klaster AKS: 
1. [Uzyskaj dostęp do zasobu AKS w ramach subskrypcji platformy Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). Ten proces pozwala kontrolować skalowanie lub Uaktualnianie klastra przy użyciu interfejsów API AKS oraz ściąganie kubeconfig.
2. Dostęp do interfejsu API Kubernetes. Ten dostęp jest kontrolowany przez [KUBERNETES RBAC](#kubernetes-role-based-access-control-rbac) (tradycyjnie) lub [integrację z usługą Azure RBAC z AKS dla autoryzacji Kubernetes](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Uwierzytelnianie RBAC platformy Azure w celu autoryzacji dostępu do zasobu AKS

Korzystając z usługi Azure RBAC, możesz udostępnić użytkownikom (lub tożsamościom) szczegółowy dostęp do zasobów AKS w ramach jednej lub kilku subskrypcji. Można na przykład mieć [rolę współautora usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) , która umożliwia wykonywanie akcji takich jak skalowanie i Uaktualnianie klastra. Inny użytkownik może mieć [rolę administratora klastra usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) , która zapewnia tylko uprawnienie do ściągania kubeconfig administratora.

Alternatywnie można dać użytkownikowi rolę rola [współautora](../role-based-access-control/built-in-roles.md#contributor) , która może obejmować powyższe uprawnienia i wszystkie akcje możliwe dla zasobu AKS z wyjątkiem zarządzania uprawnieniami.

Zobacz więcej, jak używać funkcji RBAC platformy Azure, aby zabezpieczyć dostęp do pliku kubeconfig, który zapewnia dostęp do interfejsu API Kubernetes w [tym miejscu](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC dla autoryzacji Kubernetes (wersja zapoznawcza)

Dzięki integracji z usługą Azure RBAC AKS będzie używać serwera elementu webhook autoryzacji Kubernetes, aby umożliwić zarządzanie uprawnieniami i przypisaniami zasobów klastra K8s zintegrowanych z usługą Azure AD przy użyciu definicji ról platformy Azure i przypisań ról.

![Przepływ autoryzacji usługi Azure RBAC dla Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Jak pokazano na powyższym diagramie, podczas korzystania z integracji z usługą Azure RBAC wszystkie żądania do interfejsu API Kubernetes będą zgodne z tym samym przepływem uwierzytelniania, co zostało wyjaśnione w [sekcji Azure Active Integration](#azure-active-directory-integration). 

Jednak w takim przypadku zamiast polegać wyłącznie na Kubernetes RBAC na potrzeby autoryzacji, żądanie jest faktycznie autoryzowane przez platformę Azure, o ile tożsamość, która dokonała żądania, istnieje w usłudze AAD. Jeśli tożsamość nie istnieje w usłudze AAD, na przykład w ramach konta usługi Kubernetes, kontrola RBAC platformy Azure nie zostanie rozpoczęta i będzie standardowa kontrola RBAC Kubernetes.

W tym scenariuszu można nadać użytkownikom jedną z czterech wbudowanych ról lub tworzyć role niestandardowe w taki sam sposób, jak w przypadku ról Kubernetes, ale w tym przypadku przy użyciu mechanizmów i interfejsów API platformy Azure. 

Ta funkcja pozwoli na przykład nie tylko udzielić użytkownikom uprawnień do zasobu AKS w różnych subskrypcjach, ale skonfigurować i nadać im rolę i uprawnienia, które będą znajdować się w każdym z tych klastrów, które kontrolują dostęp do interfejsu API Kubernetes. Na przykład można udzielić `Azure Kubernetes Service RBAC Viewer` roli w zakresie subskrypcji, a jej odbiorca będzie mogła wyświetlać i pobierać wszystkie obiekty Kubernetes ze wszystkich klastrów, ale nie mogą ich modyfikować.


#### <a name="built-in-roles"></a>Wbudowane role

AKS udostępnia następujące cztery wbudowane role. Są one podobne do [wbudowanych ról Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , ale z kilkoma różnicami, takimi jak obsługa CRDs. Pełną listę akcji dozwolonych przez każdą wbudowaną rolę można znaleźć [tutaj](../role-based-access-control/built-in-roles.md).

| Rola                                | Opis  |
|-------------------------------------|--------------|
| Przeglądarka RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie `Secrets` , ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do `ServiceAccount` poświadczeń w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolne `ServiceAccount` w przestrzeni nazw (w formie eskalacji uprawnień).  |
| Składnik zapisywania RBAC usługi Azure Kubernetes Service | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola pozwala uzyskać dostęp do `Secrets` i uruchamiać zasobniki jako dowolne ServiceAccount w przestrzeni nazw, dzięki czemu można je wykorzystać do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. |
| Administrator RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora, który ma być przyznany w przestrzeni nazw. Zezwala na dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym możliwość tworzenia ról i powiązań ról w przestrzeni nazw. Ta rola nie zezwala na dostęp do zapisu do przydziału zasobów ani do samego obszaru nazw. |
| Administrator klastra RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora do wykonywania dowolnych akcji względem dowolnego zasobu. Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |

**Aby dowiedzieć się, jak włączyć funkcję RBAC na platformie Azure na potrzeby autoryzacji Kubernetes, [Przeczytaj tutaj](manage-azure-rbac.md).**

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
