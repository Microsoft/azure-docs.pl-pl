---
title: Ład i zabezpieczenia w przedsiębiorstwie
titleSuffix: Azure Machine Learning
description: 'Bezpiecznie używaj Azure Machine Learning: uwierzytelnianie, autoryzacja, zabezpieczenia sieci, szyfrowanie danych i monitorowanie.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992033"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Zabezpieczenia i zarządzanie dla przedsiębiorstw Azure Machine Learning

Ten artykuł zawiera informacje na temat funkcji zabezpieczeń i zarządzania dostępnych dla Azure Machine Learning. Te funkcje są przydatne dla administratorów, DevOps i MLOps, którzy chcą utworzyć bezpieczną konfigurację zgodną z zasadami firmy. Dzięki Azure Machine Learning i platformie Azure można:

* Ograniczanie dostępu do zasobów i operacji według kont użytkowników lub grup
* Ograniczanie przychodzącej i wychodzącej komunikacji sieciowej
* Szyfruj dane podczas przesyłania i w spoczynku
* Skanuj w poszukiwaniu luk w zabezpieczeniach
* Zastosuj i Przeprowadź inspekcję zasad konfiguracji

## <a name="restrict-access-to-resources-and-operations"></a>Ograniczanie dostępu do zasobów i operacji

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) to Dostawca usługi tożsamości dla Azure Machine Learning. Umożliwia tworzenie obiektów zabezpieczeń (użytkowników, grup, jednostek usługi i tożsamości zarządzanej) służących do _uwierzytelniania_ w zasobach platformy Azure oraz zarządzanie nimi. Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli usługa Azure AD jest skonfigurowana do korzystania z niej.

Oto proces uwierzytelniania Azure Machine Learning korzystania z uwierzytelniania wieloskładnikowego w usłudze Azure AD:

1. Klient loguje się do usługi Azure AD i pobiera token Azure Resource Manager.
1. Klient przedstawia token do Azure Resource Manager i wszystkich Azure Machine Learning.
1. Azure Machine Learning zawiera token usługi Machine Learning dla elementu docelowego obliczeń użytkownika (na przykład Azure Machine Learning klaster obliczeniowy). Ten token jest używany przez element docelowy obliczeń użytkownika do wywołania zwrotnego do usługi Machine Learning po zakończeniu przebiegu. Zakres jest ograniczony do obszaru roboczego.

[![Uwierzytelnianie w Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Każdy obszar roboczy ma skojarzoną w systemie [tożsamość zarządzaną](../active-directory/managed-identities-azure-resources/overview.md) , która ma taką samą nazwę jak obszar roboczy. Ta tożsamość zarządzana służy do bezpiecznego dostępu do zasobów używanych przez obszar roboczy. Ma następujące uprawnienia kontroli RBAC platformy Azure dotyczące dołączonych zasobów:

| Zasób | Uprawnienia |
| ----- | ----- |
| Workspace | Współautor |
| Konto magazynu | Współautor danych obiektu blob usługi Storage |
| Magazyn kluczy | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Azure Container Registry | Współautor |
| Grupa zasobów, która zawiera obszar roboczy | Współautor |
| Grupa zasobów zawierająca Magazyn kluczy (jeśli jest inna niż ta, która zawiera obszar roboczy) | Współautor |

Nie zaleca się, aby administratorzy mogli odwołać dostęp do tożsamości zarządzanej do zasobów wymienionych w powyższej tabeli. Dostęp można przywrócić przy użyciu [operacji ponowna synchronizacja kluczy](how-to-change-storage-access-key.md).

Azure Machine Learning również tworzy dodatkową aplikację (nazwa rozpoczyna się od `aml-` lub `Microsoft-AzureML-Support-App-` ) z dostępem na poziomie współautora w ramach subskrypcji dla każdego regionu obszaru roboczego. Jeśli na przykład masz jeden obszar roboczy w regionie Wschodnie stany USA i jeden w Europie Północnej w tej samej subskrypcji, zobaczysz dwie z tych aplikacji. Te aplikacje umożliwiają Azure Machine Learning ułatwiające zarządzanie zasobami obliczeniowymi.

Możesz również skonfigurować własne tożsamości zarządzane do użycia z usługą Azure Virtual Machines i Azure Machine Learning klastra obliczeniowego. Przy użyciu maszyny wirtualnej zarządzana tożsamość może być używana do uzyskiwania dostępu do obszaru roboczego z zestawu SDK, a nie z konta usługi Azure AD danego użytkownika. W przypadku klastra obliczeniowego zarządzana tożsamość jest używana w celu uzyskania dostępu do zasobów takich jak zabezpieczone magazyny danych, do których użytkownik uruchamiający zadanie szkoleniowe może nie mieć do niego dostępu. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dla Azure Machine Learning obszaru roboczego](how-to-setup-authentication.md).

> [!TIP]
> Istnieją pewne wyjątki dotyczące korzystania z usługi Azure AD i usługi Azure RBAC w ramach Azure Machine Learning:
> * Opcjonalnie można włączyć dostęp __SSH__ do zasobów obliczeniowych, takich jak Azure Machine Learning wystąpienia obliczeniowego i klaster obliczeniowy. Dostęp SSH jest oparty na parach kluczy publicznych/prywatnych, a nie w usłudze Azure AD. Dostęp SSH nie podlega kontroli RBAC platformy Azure.
> * Można przeprowadzić uwierzytelnianie do modeli wdrożonych jako usługi sieci Web (punkty końcowe wnioskowania) przy użyciu uwierzytelniania opartego na __kluczu__ lub __tokenie__. Klucze są ciągami statycznymi, podczas gdy tokeny są pobierane przy użyciu obiektu zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla modeli wdrożonych jako usługa sieci Web](how-to-authenticate-web-service.md).

Aby uzyskać więcej informacji, zobacz następujące artykuły:
* [Uwierzytelnianie dla Azure Machine Learning obszaru roboczego](how-to-setup-authentication.md)
* [Zarządzanie dostępem do Azure Machine Learning](how-to-assign-roles.md)
* [Łączenie z usługami magazynu](how-to-access-data.md)
* [Użyj Azure Key Vault dla wpisów tajnych w przypadku szkolenia](how-to-use-secrets-in-runs.md)
* [Używanie tożsamości zarządzanej przez usługę Azure AD z usługą Azure Machine Learning](how-to-use-managed-identities.md)
* [Używanie tożsamości zarządzanej usługi Azure AD z usługą sieci Web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Bezpieczeństwo i izolacja sieci

Aby ograniczyć dostęp sieciowy do zasobów Azure Machine Learning, można użyć [usługi Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). Sieci wirtualnych umożliwiają tworzenie środowisk sieciowych, które są częściowo lub w pełni odizolowane od publicznego Internetu. Pozwala to zmniejszyć obszar ataków na rozwiązanie, a także szanse na eksfiltracji danych.

Możesz użyć bramy wirtualnej sieci prywatnej (VPN) do łączenia poszczególnych klientów lub własnej sieci z siecią wirtualną.

W obszarze roboczym Azure Machine Learning można użyć [prywatnego linku platformy Azure](../private-link/private-link-overview.md) , aby utworzyć prywatny punkt końcowy za sieci wirtualnej. Zapewnia to zestaw prywatnych adresów IP, z których można korzystać w celu uzyskania dostępu do obszaru roboczego z sieci wirtualnej. Niektóre z usług, z których korzysta Azure Machine Learning, mogą również korzystać z prywatnego linku platformy Azure, ale niektóre z nich korzystają z sieciowych grup zabezpieczeń lub routingu zdefiniowanego przez użytkownika.

Więcej informacji można znaleźć w następujących dokumentach:

* [Omówienie izolacji i prywatności sieci wirtualnej](how-to-network-security-overview.md)
* [Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Zabezpieczanie środowiska trenowania](how-to-secure-training-vnet.md)
* [Bezpieczne środowisko wnioskowania](how-to-secure-inferencing-vnet.md)
* [Korzystanie z programu Studio w zabezpieczonej sieci wirtualnej](how-to-enable-studio-virtual-network.md)
* [Używanie niestandardowej usługi DNS](how-to-custom-dns.md)
* [Konfigurowanie zapory](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Szyfrowanie danych

Azure Machine Learning używa wielu zasobów obliczeniowych i magazynów danych na platformie Azure. Aby dowiedzieć się więcej o tym, jak każdy z nich obsługuje szyfrowanie danych podczas przechowywania i przesyłania, zobacz [szyfrowanie danych za pomocą Azure Machine Learning](concept-data-encryption.md).

Podczas wdrażania modeli jako usług sieci Web można włączyć protokół TLS (Transport Layer Security), aby szyfrować dane podczas przesyłania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczonej usługi sieci Web](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Skanowanie pod kątem luk w zabezpieczeniach

Usługa [Azure Security Center](../security-center/security-center-introduction.md) zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. W przypadku usługi Azure Machine Learning należy włączyć skanowanie zasobów [Azure Container Registry](../container-registry/container-registry-intro.md) i zasobów usługi Azure Kubernetes. Aby uzyskać więcej informacji, zobacz [Azure Container Registry skanowania obrazów przez Security Center](../security-center/defender-for-container-registries-introduction.md) i [integrację usług Azure Kubernetes Services z programem Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Inspekcja i zarządzanie zgodnością

[Azure Policy](../governance/policy/index.yml) jest narzędziem do zarządzania, które umożliwia upewnienie się, że zasoby platformy Azure są zgodne z zasadami. Można ustawić zasady, aby zezwalać lub wymuszać określone konfiguracje, na przykład czy obszar roboczy Azure Machine Learning używa prywatnego punktu końcowego. Aby uzyskać więcej informacji na temat Azure Policy, zobacz [dokumentację Azure Policy](../governance/policy/overview.md). Aby uzyskać więcej informacji na temat zasad specyficznych dla Azure Machine Learning, zobacz [Inspekcja i zarządzanie zgodnością z Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie Azure Machine Learning usług sieci Web przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Używanie Azure Machine Learning z zaporą platformy Azure](how-to-access-azureml-behind-firewall.md)
* [Korzystanie z Azure Machine Learning z platformą Azure Virtual Network](how-to-network-security-overview.md)
* [Szyfrowanie danych magazynowanych i przesyłanych](concept-data-encryption.md)
* [Kompilowanie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
