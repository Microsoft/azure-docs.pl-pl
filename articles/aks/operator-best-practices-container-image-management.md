---
title: Najlepsze praktyki operatora — zarządzanie obrazami kontenerów w usłudze Azure Kubernetes Services (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi sposobu zarządzania i zabezpieczania obrazów kontenerów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105123"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)

Zabezpieczenia kontenera i kontenera są głównym priorytetem podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS). Kontenery z nieaktualnymi obrazami podstawowymi lub niepoprawkami środowiska uruchomieniowe aplikacji wprowadzają zagrożenie bezpieczeństwa i możliwym do ataku wektor. 

Zminimalizować ryzyko dzięki integracji i uruchamianiu narzędzi do skanowania i korygowania w kontenerach w ramach kompilacji i środowiska uruchomieniowego. Im wcześniej przechwytuje lukę w zabezpieczeniach lub nieaktualny obraz podstawowy, tym bezpieczniejsze jest klaster. 

W tym artykule *"kontenery"* oznacza:
* Obrazy kontenerów przechowywane w rejestrze kontenerów.
* Uruchomione kontenery.

W tym artykule omówiono sposób zabezpieczania kontenerów w programie AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Skanuj i Koryguj luki w zabezpieczeniach obrazu.
> * Automatycznie Wyzwalaj i wdrażaj obrazy kontenerów po zaktualizowaniu obrazu podstawowego.

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń klastra][best-practices-cluster-security] i [zabezpieczenia pod][best-practices-pod-security].

Możesz również użyć [zabezpieczeń kontenera w Security Center][security-center-containers] , aby pomóc w skanowaniu kontenerów pod kątem luk w zabezpieczeniach. [Azure Container Registry integracja][security-center-acr] z usługą Security Center pomaga chronić obrazy i rejestr przed lukami w zabezpieczeniach.

## <a name="secure-the-images-and-run-time"></a>Zabezpieczanie obrazów i czasu wykonywania

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Skanuj obrazy kontenerów pod kątem luk w zabezpieczeniach. Wdrażaj tylko sprawdzone obrazy. Regularnie Aktualizuj podstawowe obrazy i środowisko uruchomieniowe aplikacji. Wdróż ponownie obciążenia w klastrze AKS.

Podczas przyjmowania obciążeń opartych na kontenerach warto sprawdzić zabezpieczenia obrazów i środowiska uruchomieniowego używane do tworzenia własnych aplikacji. Jak uniknąć wprowadzania luk w zabezpieczeniach do wdrożeń? 
* Dołącz do przepływu pracy wdrożenia proces skanowania obrazów kontenera przy użyciu narzędzi, takich jak [TwistLock][twistlock] lub [akwamaryna][aqua].
* Zezwalaj na wdrażanie tylko zweryfikowanych obrazów.

![Skanuj i Koryguj obrazy kontenerów, Weryfikuj i wdrażaj](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Na przykład można użyć potoku ciągłej integracji i ciągłego wdrażania (CI/CD) w celu zautomatyzowania skanowania obrazu, weryfikacji i wdrożeń. Azure Container Registry obejmuje te luki w zabezpieczeniach.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatycznie Kompiluj nowe obrazy w podstawowej aktualizacji obrazu

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> W przypadku używania obrazów podstawowych dla obrazów aplikacji należy użyć automatyzacji, aby kompilować nowe obrazy po zaktualizowaniu obrazu podstawowego. Ze względu na to, że zaktualizowane obrazy podstawowe zazwyczaj zawierają poprawki zabezpieczeń, zaktualizuj wszelkie podrzędne obrazy kontenerów aplikacji.

Za każdym razem, gdy obraz podstawowy jest aktualizowany, należy również zaktualizować wszystkie podrzędne obrazy kontenerów. Zintegruj ten proces kompilacji z potokami walidacji i wdrażania, takimi jak [Azure Pipelines][azure-pipelines] lub Jenkins. Te potoki zadbają o to, aby aplikacje nadal działały na zaktualizowanych obrazach. Po sprawdzeniu poprawności obrazów kontenera aplikacji wdrożenia AKS można zaktualizować w celu uruchamiania najnowszych, bezpiecznych obrazów.

Azure Container Registry zadania mogą również automatycznie aktualizować obrazy kontenerów po zaktualizowaniu obrazu podstawowego. Korzystając z tej funkcji, można utworzyć kilka podstawowych obrazów i aktualizować je za pomocą poprawek usterek i zabezpieczeń.

Aby uzyskać więcej informacji o aktualizacjach obrazu podstawowego, zobacz Automatyzacja kompilowania obrazów [w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań][acr-base-image-update].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposobie zabezpieczania kontenerów. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Automatyzacja kompilacji obrazów w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md