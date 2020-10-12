---
title: Korzystanie z usług typu "platforma jako usługa" (PaaS) w Azure DevTest Labs
description: Dowiedz się, jak korzystać z usług typu "platforma jako usługa" (pass) w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478743"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Korzystanie z usług typu "platforma jako usługa" (PaaS) w Azure DevTest Labs
PaaS jest obsługiwana w DevTest Labs za pośrednictwem funkcji środowiska. Środowiska w DevTest Labs są obsługiwane przez wstępnie skonfigurowane szablony Azure Resource Manager w repozytorium git. Środowiska mogą zawierać zarówno zasoby PaaS, jak i IaaS. Umożliwiają one tworzenie złożonych systemów, które mogą obejmować zasoby platformy Azure, takie jak maszyny wirtualne, bazy danych, sieci wirtualne i aplikacje sieci Web, które są dostosowane do współdziałania ze sobą. Te szablony umożliwiają spójne wdrożenie i ulepszone zarządzanie środowiskami przy użyciu kontroli kodu źródłowego. 

Prawidłowo skonfigurowany system umożliwia następujące scenariusze: 

- Deweloperzy mający niezależne i wiele środowisk
- Asynchroniczne testowanie w różnych konfiguracjach
- Integracja z potokami przejściowymi i produkcyjnymi bez wprowadzania zmian w szablonach
- Posiadanie zarówno maszyn deweloperskich, jak i środowisk w ramach tego samego laboratorium poprawia łatwość zarządzania i raportowania kosztów.  

Dostawca zasobów DevTest Labs tworzy zasoby w imieniu użytkownika laboratorium, dlatego nie trzeba udzielać użytkownikowi laboratorium żadnych dodatkowych uprawnień umożliwiających korzystanie z zasobów PaaS. Na poniższej ilustracji przedstawiono klaster Service Fabric jako środowisko w środowisku laboratoryjnym.

![Service Fabric klastra jako środowiska](./media/create-environment-service-fabric-cluster/cluster-created.png)

Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz [Tworzenie środowisk z wieloma maszynami wirtualnymi i PaaS zasobów za pomocą szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs ma publiczne repozytorium szablonów Azure Resource Manager, których można użyć do tworzenia środowisk bez konieczności nawiązywania połączenia z zewnętrznym źródłem GitHub przez siebie. Aby uzyskać więcej informacji na temat środowisk publicznych, zobacz [Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

W dużych organizacjach zespoły programistyczne zwykle udostępniają środowiska, takie jak dostosowane/izolowane środowiska testowe. Mogą istnieć środowiska, które mają być używane przez wszystkie zespoły w ramach jednostki biznesowej lub dzielenia. Grupa IT może chcieć udostępnić swoje środowiska, które mogą być używane przez wszystkie zespoły w organizacji.  

## <a name="customizations"></a>Dostosowania

#### <a name="sandbox"></a>Piaskownica 
Właściciel laboratorium może dostosować środowiska laboratoryjne, aby zmienić rolę użytkownika z **czytnika** na **współautora** w ramach grupy zasobów. Ta funkcja znajduje się na stronie **ustawień laboratorium** w obszarze **Konfiguracja i zasady** laboratorium. Ta zmiana roli umożliwia użytkownikowi dodawanie lub usuwanie zasobów w danym środowisku. Jeśli chcesz dodatkowo ograniczyć dostęp, użyj zasad platformy Azure. Ta funkcja umożliwia dostosowanie zasobów lub konfiguracji bez dostępu na poziomie subskrypcji.

#### <a name="custom-tokens"></a>Tokeny niestandardowe
Istnieje kilka niestandardowych informacji laboratorium, które znajdują się poza grupą zasobów i są specyficzne dla środowisk, do których ten szablon może uzyskać dostęp. Oto niektóre z nich: 

- Identyfikacja sieci laboratorium
- Lokalizacja
- Konto magazynu, w którym są przechowywane pliki szablonów Menedżer zasobów. 
 
#### <a name="lab-virtual-network"></a>Sieć wirtualna laboratorium
W artykule [łączącym środowiska z siecią wirtualną laboratorium](connect-environment-lab-virtual-network.md) opisano, jak zmodyfikować szablon Menedżer zasobów, aby używał `$(LabSubnetId)` tokenu. Po utworzeniu środowiska `$(LabSubnetId)` token jest zastępowany pierwszym znacznikiem podsieci, gdzie opcja **tworzenia maszyny wirtualnej** jest ustawiona na **wartość true**. Pozwala naszym środowisku używać wcześniej utworzonych sieci. Jeśli chcesz używać tych samych szablonów Menedżer zasobów w środowiskach testowych jako przejściowych i produkcyjnych, użyj `$(LabSubnetId)` jako wartości domyślnej w parametrze Menedżer zasobów szablonu. 

#### <a name="environment-storage-account"></a>Konto magazynu środowiska
DevTest Labs obsługuje użycie [zagnieżdżonych szablonów Menedżer zasobów](../azure-resource-manager/templates/linked-templates.md). W artykule [[Deploying nested Azure Resource Manager templates for test](deploy-nested-template-environments.md) Environments wyjaśniono, jak używać  `_artifactsLocation` `_artifactsLocationSasToken` tokenów i do tworzenia identyfikatora URI dla szablonu Menedżer zasobów w tym samym folderze co lub w zagnieżdżonym folderze szablonu głównego. Aby uzyskać więcej informacji na temat tych dwóch tokenów, zobacz sekcję **artefakty wdrożenia** w [podręczniku Azure Resource Manager – Best Practices Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Doświadczenie użytkownika

## <a name="developer"></a>Deweloper
Deweloperzy używają tego samego przepływu pracy do tworzenia maszyny wirtualnej w celu utworzenia określonego środowiska. Wybierają one środowisko a obraz maszyny, a następnie wprowadzają niezbędne informacje wymagane przez szablon. Każdy deweloper ma środowisko umożliwiające wdrożenie zmian i udoskonalone debugowanie pętli wewnętrznej. Środowisko można utworzyć w dowolnym momencie przy użyciu najnowszego szablonu.  Ta funkcja pozwala zniszczyć i odtworzyć środowiska w celu skrócenia przestojów spowodowanych ręcznym tworzeniem systemu lub odzyskiwaniem po testowaniu błędów.  

### <a name="testing"></a>Testowanie
Środowiska DevTest Labs pozwalają na asynchroniczne testowanie określonego kodu i konfiguracji asynchronicznie. Typowym celem jest skonfigurowanie środowiska przy użyciu kodu z poszczególnych żądań ściągnięcia i rozpoczęcie testów automatycznych. Po przeprowadzeniu zautomatyzowanego testowania testy ręczne można wykonać w odniesieniu do określonego środowiska. Zazwyczaj ten proces jest wykonywany jako część potoku ciągłej integracji/ciągłego wdrażania. 

## <a name="management-experience"></a>Środowisko zarządzania

### <a name="cost-tracking"></a>Śledzenie kosztów
Funkcja śledzenia kosztów obejmuje zasoby platformy Azure w różnych środowiskach w ramach ogólnego trendu kosztu. Koszt według zasobów nie powoduje rozdzielenia różnych zasobów w środowisku, ale program wyświetla środowisko jako pojedynczy koszt.

### <a name="security"></a>Zabezpieczenia
Prawidłowo skonfigurowana subskrypcja platformy Azure z DevTest Labs może [ograniczyć dostęp do zasobów platformy Azure tylko za pomocą laboratorium](devtest-lab-add-devtest-user.md). Dzięki środowiskom właściciel laboratorium może pozwolić użytkownikom na dostęp do zasobów PaaS z zatwierdzonymi konfiguracjami bez zezwalania na dostęp do innych zasobów platformy Azure. W scenariuszu, w którym użytkownicy laboratorium dostosowują środowiska, właściciel laboratorium może zezwolić na dostęp współautora. Dostęp współautora umożliwia użytkownikowi laboratorium Dodawanie lub usuwanie zasobów platformy Azure tylko w ramach zarządzanej grupy zasobów. Pozwala to na łatwiejsze śledzenie i zarządzanie, a następnie umożliwienie użytkownikom dostępu do subskrypcji.

### <a name="automation"></a>Automatyzacja
Automatyzacja to kluczowy składnik dla dużej skali, obowiązujący ekosystem. Automatyzacja jest niezbędna do obsługi zarządzania lub śledzenia wielu środowisk w ramach subskrypcji i laboratoriów.

### <a name="cicd-pipeline"></a>Potok ciągłej integracji/ciągłego wdrażania
Usługi PaaS Services w DevTest Labs mogą pomóc w ulepszaniu potoku ciągłej integracji i ciągłego wdrażania, mając skoncentrowane wdrożenia, w których dostęp jest kontrolowany przez laboratorium.

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje o środowiskach można znaleźć w następujących artykułach: 

- 
- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w programie Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Tworzenie środowiska z niezależnym klastrem Service Fabric w programie Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Podłącz środowisko do sieci wirtualnej laboratorium w Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integruj środowiska z potokami CI/DevOps na platformie Azure](integrate-environments-devops-pipeline.md)
 





