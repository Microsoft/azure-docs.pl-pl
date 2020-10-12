---
title: Najlepsze rozwiązania dla deweloperów — zabezpieczenia pod kątem usługi Azure Kubernetes Services (AKS)
description: Poznaj najlepsze rozwiązania dla deweloperów dotyczące zabezpieczania zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: fab4943cad1a87bda70a4c4332ab6135ed99bf1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022279"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zabezpieczeń na platformie Azure Kubernetes Service (AKS)

Podczas opracowywania i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), bezpieczeństwo Twojego zasobnika jest kluczowym zagadnieniem. Aplikacje powinny być przeznaczone dla zasad minimalnej liczby wymaganych uprawnień. Bezpieczeństwo prywatnych danych jest na początku przeznaczone dla klientów. Nie chcesz, aby poświadczenia, takie jak parametry połączenia bazy danych, klucze lub wpisy tajne oraz certyfikaty ujawnione na świecie zewnętrznym, w którym osoba atakująca może wykorzystać te klucze tajne do złośliwych celów. Nie dodawaj ich do kodu ani nie osadzaj ich w obrazach kontenera. Takie podejście spowoduje utworzenie ryzyka związanego z narażeniem i ograniczenie możliwości przełączenia tych poświadczeń, ponieważ należy ponownie skompilować obrazy kontenerów.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na sposobach zabezpieczania zespołów w AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Użyj kontekstu zabezpieczeń pod, aby ograniczyć dostęp do procesów i usług lub eskalacji uprawnień
> * Uwierzytelnianie za pomocą innych zasobów platformy Azure przy użyciu tożsamości zarządzanych
> * Żądaj i pobieraj poświadczenia z magazynu cyfrowego, takiego jak Azure Key Vault

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń klastra][best-practices-cluster-security] i [zarządzania obrazami kontenerów][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Bezpieczny dostęp pod dostępem do zasobów

**Wskazówki dotyczące najlepszych** rozwiązań — do uruchamiania jako inny użytkownik lub Grupa i ograniczania dostępu do podstawowych procesów i usług węzła, definiowania ustawień kontekstu zabezpieczeń. Przypisz wymaganą minimalną liczbę uprawnień.

Aby aplikacje działały prawidłowo, należy uruchomić jako zdefiniowany użytkownika lub grupę, a nie jako *element główny*. `securityContext`Dla elementu pod lub kontenera można zdefiniować ustawienia, takie jak *RunAsUser* lub *fsGroup* , aby założyć odpowiednie uprawnienia. Należy przypisać tylko wymagane uprawnienia użytkownika lub grupy, a nie użyć kontekstu zabezpieczeń jako środka do założenia dodatkowych uprawnień. Ustawienia *runAsUser*, eskalacji uprawnień i inne funkcje systemu Linux są dostępne tylko w węzłach i w systemach Linux.

Jeśli użytkownik nie jest użytkownikiem głównym, kontenery nie mogą być powiązane z portami uprzywilejowanymi w obszarze 1024. W tym scenariuszu usługi Kubernetes Services mogą służyć do uzyskania informacji o tym, że aplikacja działa na określonym porcie.

Kontekst zabezpieczeń pod może również definiować dodatkowe możliwości lub uprawnienia do uzyskiwania dostępu do procesów i usług. Można ustawić następujące typowe definicje kontekstu zabezpieczeń:

* **allowPrivilegeEscalation** określa, czy pod warunkiem, że może przyjmować uprawnienia *root* . Zaprojektuj swoje aplikacje, aby to ustawienie było zawsze ustawione na *wartość false*.
* **Możliwości systemu Linux** pozwalają na dostęp do węzła podstawowego węzłów. Weź pod uwagę możliwość przypisywania tych funkcji. Przypisz wymaganą minimalną liczbę uprawnień. Aby uzyskać więcej informacji, zobacz [możliwości systemu Linux][linux-capabilities].
* **Etykiety SELinux** to moduł zabezpieczeń jądra systemu Linux, który umożliwia definiowanie zasad dostępu dla usług, procesów i dostępu do systemu plików. Należy ponownie przypisać najmniejszą liczbę wymaganych uprawnień. Aby uzyskać więcej informacji, zobacz [Opcje SELinux w Kubernetes][selinux-labels]

W poniższym przykładzie manifestu YAML ustawia ustawienia kontekstu zabezpieczeń, aby zdefiniować:

* Pod uruchomieniem jako identyfikator użytkownika *1000* i częścią grupy o identyfikatorze *2000*
* Nie można eskalować uprawnień do użycia `root`
* Umożliwia funkcjom systemu Linux dostęp do interfejsów sieciowych i zegara czasu rzeczywistego hosta (sprzętu)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Aby określić, jakie ustawienia kontekstu zabezpieczeń są potrzebne, Pracuj z operatorem klastra. Spróbuj zaprojektować aplikacje w celu zminimalizowania dodatkowych uprawnień i uzyskania dostępu do programu. Dostępne są dodatkowe funkcje zabezpieczeń umożliwiające ograniczenie dostępu przy użyciu AppArmor i seccomp (Zabezpieczanie obliczeń), które mogą być implementowane przez operatorów klastra. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do kontenera do zasobów][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Ogranicz ekspozycję poświadczeń

**Wskazówki dotyczące najlepszych** rozwiązań — nie Definiuj poświadczeń w kodzie aplikacji. Użyj zarządzanych tożsamości dla zasobów platformy Azure, aby umożliwić usłudze na żądanie dostępu do innych zasobów. Magazyn cyfrowy, taki jak Azure Key Vault, powinien również służyć do przechowywania i pobierania kluczy cyfrowych i poświadczeń. Tożsamości zarządzane pod są przeznaczone wyłącznie dla systemów Linux i obrazów kontenerów.

Aby ograniczyć ryzyko ujawnienia poświadczeń w kodzie aplikacji, należy unikać stosowania poświadczeń stałych lub udostępnionych. Poświadczeń lub kluczy nie należy uwzględniać bezpośrednio w kodzie. Jeśli te poświadczenia są ujawniane, aplikacja musi zostać zaktualizowana i wdrożona ponownie. Lepszym rozwiązaniem jest nadanie podstawom tożsamości i sposobu samodzielnego uwierzytelniania lub automatycznego pobierania poświadczeń z magazynu cyfrowego.

### <a name="use-azure-container-compute-upstream-projects"></a>Korzystanie z projektów nadrzędnych obliczeń kontenerów platformy Azure

> [!IMPORTANT]
> Skojarzone projekty open source AKS nie są obsługiwane przez pomoc techniczną platformy Azure. Są one udostępniane użytkownikom do samodzielnego instalowania w klastrach i zbierania opinii z naszej społeczności.

Następujące [skojarzone projekty typu open source AKS][aks-associated-projects] umożliwiają automatyczne uwierzytelnianie z magazynów lub zażądanie poświadczeń oraz kluczy z magazynu cyfrowego. Te projekty są obsługiwane przez zespół nadrzędny usługi obliczeniowej Azure Container i są częścią [szerszej listy projektów dostępnych do użycia](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Azure Active Directory pod tożsamością][aad-pod-identity]
 * [Dostawca Azure Key Vault dla sterownika CSI magazynu wpisów tajnych](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Użyj tożsamości zarządzanych

Zarządzana tożsamość zasobów platformy Azure pozwala na uwierzytelnianie pod względem usług platformy Azure, które go obsługują, takich jak Storage czy SQL. W obszarze jest przypisana tożsamość platformy Azure, która umożliwia im uwierzytelnianie Azure Active Directory i odbieranie cyfrowego tokenu. Ten token cyfrowy może być przedstawiony innym usługom platformy Azure, które sprawdzają, czy jest on autoryzowany do uzyskiwania dostępu do usługi i wykonując wymagane akcje. Takie podejście oznacza, że dla parametrów połączenia bazy danych nie są wymagane żadne wpisy tajne. Uproszczony przepływ pracy dotyczący tożsamości zarządzanej pod, jest przedstawiony na poniższym diagramie:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Uproszczony przepływ pracy dla tożsamości zarządzanej pod na platformie Azure":::

Przy użyciu tożsamości zarządzanej kod aplikacji nie musi zawierać poświadczeń, aby uzyskać dostęp do usługi, takiej jak usługa Azure Storage. Każdy z nich jest uwierzytelniany przy użyciu własnej tożsamości, dzięki czemu można przeprowadzać inspekcję i przeglądanie dostępu. Jeśli aplikacja łączy się z innymi usługami platformy Azure, Użyj tożsamości zarządzanych, aby ograniczyć ponowne użycie poświadczeń i ryzyko narażenia.

Aby uzyskać więcej informacji o tożsamościach pod, zobacz [Konfigurowanie klastra AKS do użycia z tożsamościami zarządzanymi i aplikacjami][aad-pod-identity] .

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Użyj Azure Key Vault ze sterownikiem CSI magazynu kluczy tajnych

Korzystanie z projektu tożsamości pod Identity umożliwia uwierzytelnianie w celu obsługi usług platformy Azure. W przypadku własnych usług lub aplikacji bez tożsamości zarządzanych dla zasobów platformy Azure można nadal uwierzytelniać się przy użyciu poświadczeń lub kluczy. Magazyn cyfrowy może służyć do przechowywania zawartości tego klucza tajnego.

Gdy aplikacje potrzebują poświadczeń, komunikują się z magazynem cyfrowym, pobierają najnowszą zawartość, a następnie nawiązują połączenie z wymaganą usługą. Azure Key Vault może to być ten magazyn cyfrowy. Uproszczony przepływ pracy służący do pobierania poświadczeń z Azure Key Vault przy użyciu tożsamości zarządzanych pod na poniższym diagramie:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Uproszczony przepływ pracy dla tożsamości zarządzanej pod na platformie Azure":::

Dzięki Key Vault można przechowywać i regularnie przekazywać wpisy tajne, takie jak poświadczenia, klucze konta magazynu lub certyfikaty. Azure Key Vault można zintegrować z klastrem AKS przy użyciu [dostawcy Azure Key Vault dla sterownika CSI magazynu](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)Secret. Sterownik CSI magazynu wpisów tajnych umożliwia klastrowi AKS natywne pobieranie zawartości wpisu tajnego z Key Vault i bezpieczne dostarczanie ich tylko do żądania pod. Pracuj z operatorem klastra, aby wdrożyć sterownik CSI magazynu kluczy tajnych na węzłach procesu roboczego AKS. Możesz użyć tożsamości zarządzanej pod, aby zażądać dostępu do Key Vault i pobrać zawartość klucza tajnego wymaganą przez sterownik CSI magazynu wpisów tajnych.

Azure Key Vault za pomocą sterownika CSI magazynu kluczy tajnych można używać w przypadku węzłów systemu Linux i Kubernetes, które wymagają wersji 1,16 lub nowszej. W przypadku węzłów systemu Windows i Kubernetes jest wymagana wersja 1,18 lub nowsza.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposobach zabezpieczania swoich zasobników. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure z usługą AKS][aad-pod-identity]
* [Integracja Azure Key Vault z usługą AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
