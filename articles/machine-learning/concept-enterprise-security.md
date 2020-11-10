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
ms.date: 09/09/2020
ms.openlocfilehash: 2234b1507e6e0fdb0b668fc18a7c8533e3ea7cc1
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441787"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Zabezpieczenia i zarządzanie dla przedsiębiorstw Azure Machine Learning

W tym artykule przedstawiono informacje o funkcjach zabezpieczeń dostępnych dla Azure Machine Learning.

W przypadku korzystania z usługi w chmurze najlepszym rozwiązaniem jest ograniczenie dostępu tylko do użytkowników, którzy ich potrzebują. Zacznij od ustalenia modelu uwierzytelniania i autoryzacji używanego przez usługę. Możesz również ograniczyć dostęp do sieci lub bezpiecznie dołączać zasoby w sieci lokalnej do chmury. Szyfrowanie danych jest również niezbędne, zarówno w czasie spoczynku, jak i podczas przenoszenia danych między usługami. Możesz również utworzyć zasady, aby wymusić pewne konfiguracje lub dziennik w przypadku utworzenia niezgodnych konfiguracji. Na koniec należy mieć możliwość monitorowania usługi i tworzenia dziennika inspekcji dla wszystkich działań.

> [!NOTE]
> Informacje przedstawione w tym artykule współdziałają z Azure Machine Learning Python SDK w wersji 1.0.83.1 lub nowszej.

## <a name="authentication--authorization"></a>Uwierzytelnianie & autoryzacji

Większość uwierzytelniania do Azure Machine Learning zasobów używa usługi Azure Active Directory (Azure AD) do uwierzytelniania i kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby autoryzacji. Wyjątkami do tego są:

* __SSH__ : można włączyć dostęp SSH do niektórych zasobów obliczeniowych, takich jak Azure Machine Learning wystąpienia obliczeniowego. Dostęp SSH korzysta z uwierzytelniania opartego na kluczach. Aby uzyskać więcej informacji na temat tworzenia kluczy SSH, zobacz [Tworzenie kluczy SSH i zarządzanie nimi](../virtual-machines/linux/create-ssh-keys-detailed.md). Aby uzyskać informacje na temat włączania dostępu SSH, zobacz [Create and manage Azure Machine Learning COMPUTE instance](how-to-create-manage-compute-instance.md).
* __Modele wdrożone jako usługi sieci__ Web: wdrożenia usług sieci Web mogą używać __klucza__ lub kontroli dostępu opartej na __tokenach__. Klucze są ciągami statycznymi. Tokeny są pobierane za pomocą konta usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania dla modeli wdrożonych jako usługa sieci Web](how-to-authenticate-web-service.md).

Konkretne usługi, które Azure Machine Learning opierają się na, takie jak usługi Azure Data Storage, mają własne metody uwierzytelniania i autoryzacji. Aby uzyskać więcej informacji na temat uwierzytelniania usług magazynu, zobacz [nawiązywanie połączenia z usługami magazynu](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

Uwierzytelnianie wieloskładnikowe jest obsługiwane, jeśli Azure Active Directory (Azure AD) jest skonfigurowany do korzystania z niego. Oto proces uwierzytelniania:

1. Klient loguje się do usługi Azure AD i pobiera token Azure Resource Manager.  Nazwy główne użytkowników i usług są w pełni obsługiwane.
1. Klient przedstawia token do Azure Resource Manager i wszystkich Azure Machine Learning.
1. Usługa Machine Learning udostępnia token usługi Machine Learning dla elementu docelowego obliczeń użytkownika (na przykład środowisko obliczeniowe usługi Machine Learning). Ten token jest używany przez element docelowy obliczeń użytkownika do wywołania zwrotnego do usługi Machine Learning po zakończeniu przebiegu. Zakres jest ograniczony do obszaru roboczego.

[![Uwierzytelnianie w Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dla Azure Machine Learning obszaru roboczego](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Kontrola dostępu na podstawie ról platformy Azure

Można utworzyć wiele obszarów roboczych, a każdy obszar roboczy może być współużytkowany przez wiele osób. Można kontrolować, które funkcje lub operacje dostępne dla użytkowników obszaru roboczego mogą uzyskać dostęp, przypisując swoje konto usługi Azure AD do ról RBAC platformy Azure. Poniżej przedstawiono wbudowane role:

* Właściciel
* Współautor
* Czytelnik

Właściciele i Współautorzy mogą używać wszystkich obiektów docelowych obliczeń i magazynów danych, które są dołączone do obszaru roboczego.  

W poniższej tabeli wymieniono niektóre główne operacje Azure Machine Learning i role, które mogą je wykonać:

| Operacja Azure Machine Learning | Właściciel | Współautor | Czytelnik |
| ---- |:----:|:----:|:----:|
| Tworzenie obszaru roboczego | ✓ | ✓ | |
| Udostępnianie obszaru roboczego | ✓ | |  |
| Utwórz element docelowy obliczeń | ✓ | ✓ | |
| Dołącz cel obliczeń | ✓ | ✓ | |
| Dołącz magazyny danych | ✓ | ✓ | |
| Uruchom eksperyment | ✓ | ✓ | |
| Wyświetl przebiegi/metryki | ✓ | ✓ | ✓ |
| Rejestrowanie modelu | ✓ | ✓ | |
| Utwórz obraz | ✓ | ✓ | |
| Wdróż usługę sieci Web | ✓ | ✓ | |
| Wyświetlanie modeli/obrazów | ✓ | ✓ | ✓ |
| Wywoływanie usługi sieci Web | ✓ | ✓ | ✓ |

Jeśli wbudowane role nie spełniają Twoich potrzeb, można utworzyć role niestandardowe. Role niestandardowe kontrolują wszystkie operacje w obszarze roboczym, takie jak tworzenie obliczeń, przesyłanie przebiegu, rejestrowanie magazynu danych lub Wdrażanie modelu. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu lub usuwania dla różnych zasobów obszaru roboczego, takich jak klastry, magazyny danych, modele i punkty końcowe. Rolę można udostępnić na określonym poziomie obszaru roboczego, na określonym poziomie grupy zasobów lub na określonym poziomie subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami i rolami w obszarze roboczym Azure Machine Learning](how-to-assign-roles.md).

> [!IMPORTANT]
> Azure Machine Learning zależy od innych usług platformy Azure, takich jak Azure Blob Storage i Azure Kubernetes Services. Każda usługa platformy Azure ma własne konfiguracje RBAC platformy Azure. Aby osiągnąć żądany poziom kontroli dostępu, może być konieczne zastosowanie konfiguracji RBAC platformy Azure dla Azure Machine Learning i dla usług używanych z Azure Machine Learning.

> [!WARNING]
> Azure Machine Learning jest obsługiwana z Azure Active Directory współpracy między firmami, ale obecnie nie jest obsługiwana w przypadku Azure Active Directory współpracy między firmami.

### <a name="managed-identities"></a>Tożsamości zarządzane

Każdy obszar roboczy ma także skojarzoną [tożsamość zarządzaną](../active-directory/managed-identities-azure-resources/overview.md) przypisaną przez system, która ma taką samą nazwę jak obszar roboczy. Zarządzana tożsamość służy do bezpiecznego dostępu do zasobów używanych przez obszar roboczy. Ma następujące uprawnienia do dołączonych zasobów:

| Zasób | Uprawnienia |
| ----- | ----- |
| Workspace | Współautor |
| Konto magazynu | Współautor danych obiektu blob usługi Storage |
| Magazyn kluczy | Dostęp do wszystkich kluczy, wpisów tajnych, certyfikatów |
| Azure Container Registry | Współautor |
| Grupa zasobów, która zawiera obszar roboczy | Współautor |
| Grupa zasobów zawierająca Magazyn kluczy (jeśli jest inna niż ta, która zawiera obszar roboczy) | Współautor |

Nie zaleca się, aby administratorzy mogli odwołać dostęp do tożsamości zarządzanej do zasobów wymienionych w powyższej tabeli. Dostęp można przywrócić przy użyciu operacji ponowna synchronizacja kluczy.

Azure Machine Learning tworzy dodatkową aplikację (nazwa rozpoczyna się od `aml-` lub `Microsoft-AzureML-Support-App-` ) z dostępem na poziomie współautora w ramach subskrypcji dla każdego regionu obszaru roboczego. Jeśli na przykład masz jeden obszar roboczy w regionie Wschodnie stany USA i jeden w Europie Północnej w tej samej subskrypcji, zobaczysz dwie z tych aplikacji. Te aplikacje umożliwiają Azure Machine Learning ułatwiające zarządzanie zasobami obliczeniowymi.

Opcjonalnie można skonfigurować własne tożsamości zarządzane do użycia z usługą Azure Virtual Machines i Azure Machine Learning klastra obliczeniowego. Przy użyciu maszyny wirtualnej zarządzana tożsamość może być używana do uzyskiwania dostępu do obszaru roboczego z zestawu SDK, a nie z konta usługi Azure AD danego użytkownika. W przypadku klastra obliczeniowego zarządzana tożsamość jest używana w celu uzyskania dostępu do zasobów takich jak zabezpieczone magazyny danych, do których użytkownik uruchamiający zadanie szkoleniowe może nie mieć do niego dostępu. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dla Azure Machine Learning obszaru roboczego](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Bezpieczeństwo i izolacja sieci

Aby ograniczyć dostęp fizyczny do zasobów Azure Machine Learning, możesz użyć usługi Azure Virtual Network (VNet). Sieci wirtualnych umożliwiają tworzenie środowisk sieciowych, które są częściowo lub w pełni odizolowane od publicznego Internetu. Pozwala to zmniejszyć obszar ataków na rozwiązanie, a także szanse na eksfiltracji danych.

Aby uzyskać więcej informacji, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md).

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Szyfrowanie danych

Azure Machine Learning używa różnych zasobów obliczeniowych i magazynów danych. Aby dowiedzieć się więcej o tym, jak każdy z nich obsługuje szyfrowanie danych podczas przechowywania i przesyłania, zobacz [szyfrowanie danych za pomocą Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Dane generowane przez firmę Microsoft

W przypadku korzystania z usług, takich jak automatyczne Machine Learning, firma Microsoft może generować przejściowe, wstępnie przetworzone dane do szkolenia wielu modeli. Te dane są przechowywane w magazynie danych w obszarze roboczym, co umożliwia odpowiednie wymuszanie kontroli dostępu i szyfrowania.

Możesz również zaszyfrować [informacje diagnostyczne zarejestrowane ze wdrożonego punktu końcowego](how-to-enable-app-insights.md) w wystąpieniu usługi Azure Application Insights.

## <a name="monitoring"></a>Monitorowanie

Istnieje kilka scenariuszy monitorowania z Azure Machine Learning, w zależności od roli i sposobu monitorowania.

| Rola | Monitorowanie do użycia |
| ---- | ----- |
| Admin, DevOps, MLOps | [Azure monitor metryki](#azure-monitor), [Dziennik aktywności](#activity-log), [skanowanie luk w zabezpieczeniach](#vulnerability-scanning) |
| Analityk danych, MLOps | [Monitoruj przebiegi](#monitor-runs) |

### <a name="monitor-runs"></a>Monitoruj przebiegi

Możesz monitorować przebiegi eksperymentu w Azure Machine Learning, w tym informacje o rejestrowaniu z poziomu skryptów szkoleniowych. Te informacje można przeglądać za pomocą zestawu SDK, interfejsu wiersza polecenia platformy Azure i programu Studio. Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md)
* [Włączanie dzienników](how-to-track-experiments.md)
* [Wyświetlanie dzienników](how-to-monitor-view-training-logs.md)
* [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Za pomocą metryk Azure Monitor można wyświetlać i monitorować metryki dla Azure Machine Learningego obszaru roboczego. W [Azure Portal](https://portal.azure.com)wybierz swój obszar roboczy, a następnie wybierz pozycję **metryki** :

[![Zrzut ekranu przedstawiający przykładowe metryki dla obszaru roboczego](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metryki obejmują informacje dotyczące uruchamiania, wdrożeń i rejestracji.

Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

### <a name="activity-log"></a>Dziennik aktywności

Możesz wyświetlić dziennik aktywności obszaru roboczego, aby zobaczyć różne operacje wykonywane w obszarze roboczym. Dziennik zawiera podstawowe informacje, takie jak nazwa operacji, inicjator zdarzenia i sygnatura czasowa.

Ten zrzut ekranu przedstawia dziennik aktywności obszaru roboczego:

[![Zrzut ekranu przedstawiający dziennik aktywności obszaru roboczego](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Szczegóły żądania oceniania są przechowywane w Application Insights. Application Insights jest tworzony w ramach subskrypcji podczas tworzenia obszaru roboczego. Zarejestrowane informacje zawierają pola, takie jak:

* HTTPMethod
* UserAgent
* Computetype
* RequestUrl
* Stanu
* IdentyfikatorŻądania
* Czas trwania

> [!IMPORTANT]
> Niektóre akcje w obszarze roboczym Azure Machine Learning nie rejestrują informacji w dzienniku aktywności. Na przykład rozpoczęcie przebiegu szkoleniowego i rejestracja modelu nie są rejestrowane.
>
> Niektóre z tych akcji są wyświetlane w obszarze **działania** obszaru roboczego, ale te powiadomienia nie wskazują, kto zainicjował działanie.

### <a name="vulnerability-scanning"></a>Skanowanie pod kątem luk w zabezpieczeniach

Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. W przypadku usługi Azure Machine Learning należy włączyć skanowanie zasobów Azure Container Registry i zasobów usługi Azure Kubernetes. Zobacz [Azure Container Registry skanowanie obrazów przez Security Center](../security-center/defender-for-container-registries-introduction.md) i [integrację usług Azure Kubernetes Services z usługą Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Inspekcja i zarządzanie zgodnością

[Azure Policy](../governance/policy/index.yml) jest narzędziem do zarządzania, które umożliwia upewnienie się, że zasoby platformy Azure są zgodne z zasadami. Za pomocą Azure Machine Learning można przypisać następujące zasady:

* **Klucz zarządzany przez klienta** : Inspekcja lub wymuszanie, czy obszary robocze muszą używać klucza zarządzanego przez klienta.
* **Link prywatny** : Inspekcja, czy obszary robocze używają prywatnego punktu końcowego do komunikacji z siecią wirtualną.

Aby uzyskać więcej informacji na temat Azure Policy, zobacz [dokumentację Azure Policy](../governance/policy/overview.md).

Aby uzyskać więcej informacji na temat zasad specyficznych dla Azure Machine Learning, zobacz [Inspekcja i zarządzanie zgodnością z Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Blokady zasobów

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie Azure Machine Learning usług sieci Web przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Używanie Azure Machine Learning z zaporą platformy Azure](how-to-access-azureml-behind-firewall.md)
* [Korzystanie z Azure Machine Learning z platformą Azure Virtual Network](how-to-network-security-overview.md)
* [Szyfrowanie danych magazynowanych i przesyłanych](concept-data-encryption.md)
* [Kompilowanie interfejsu API rekomendacji w czasie rzeczywistym na platformie Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
