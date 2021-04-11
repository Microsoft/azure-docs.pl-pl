---
title: Najlepsze praktyki operatora — zarządzanie obrazami kontenerów w usłudze Azure Kubernetes Services (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra dotyczącymi sposobu zarządzania i zabezpieczania obrazów kontenerów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 1d2f5465356a94b9ad7014e75aa6fe1515411a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564921"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)

Podczas opracowywania i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), bezpieczeństwo kontenerów i obrazów kontenerów jest kluczem. Kontenery, które zawierają przestarzałe obrazy podstawowe lub Niepoprawione środowiska uruchomieniowe aplikacji, wprowadzają zagrożenie dla bezpieczeństwa i możliwy wektor ataków. Aby zminimalizować te zagrożenia, należy zintegrować narzędzia, które skanują i korygują problemy w kontenerach w czasie kompilacji, a także w środowisku uruchomieniowym. Wcześniej w procesie przechwycono lukę w zabezpieczeniach lub nieaktualną wersję obrazu podstawowego, tym bezpieczniejsze jest klaster. W tym artykule *kontenery* oznaczają zarówno obrazy kontenerów przechowywane w rejestrze kontenerów, jak i uruchomione kontenery.

W tym artykule omówiono sposób zabezpieczania kontenerów w programie AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Skanuj i Koryguj luki w zabezpieczeniach obrazu
> * Automatycznie Wyzwalaj i wdrażaj obrazy kontenerów po zaktualizowaniu obrazu podstawowego

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń klastra][best-practices-cluster-security] i [zabezpieczenia pod][best-practices-pod-security].

Możesz również użyć [zabezpieczeń kontenera w Security Center][security-center-containers] , aby pomóc w skanowaniu kontenerów pod kątem luk w zabezpieczeniach.  Ponadto [Azure Container Registry integrację][security-center-acr] z usługą Security Center, która pomaga chronić obrazy i rejestr przed lukami w zabezpieczeniach.

## <a name="secure-the-images-and-run-time"></a>Zabezpieczanie obrazów i czasu wykonywania

**Wskazówki dotyczące najlepszych** rozwiązań — skanowanie obrazów kontenerów pod kątem luk w zabezpieczeniach i wdrażanie tylko obrazów, które przeszły weryfikację. Regularnie Aktualizuj podstawowe obrazy i środowisko uruchomieniowe aplikacji, a następnie wdróż ponownie obciążenia w klastrze AKS.

Jeden problem z wdrażaniem obciążeń opartych na kontenerach polega na sprawdzeniu bezpieczeństwa obrazów i środowiska uruchomieniowego używanego do tworzenia własnych aplikacji. Jak upewnić się, że nie wprowadzasz luk w zabezpieczeniach do wdrożeń? Przepływ pracy wdrożenia powinien obejmować proces skanowania obrazów kontenera przy użyciu narzędzi, takich jak [TwistLock][twistlock] lub [akwamaryna][aqua], a następnie zezwala na wdrażanie tylko zweryfikowanych obrazów.

![Skanuj i Koryguj obrazy kontenerów, Weryfikuj i wdrażaj](media/operator-best-practices-container-security/scan-container-images-simplified.png)

W rzeczywistym przykładzie można użyć potoku ciągłej integracji i ciągłego wdrażania (CI/CD) w celu zautomatyzowania skanowania obrazu, weryfikacji i wdrożeń. Azure Container Registry obejmuje te luki w zabezpieczeniach.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatycznie Kompiluj nowe obrazy w podstawowej aktualizacji obrazu

**Wskazówki dotyczące najlepszych** rozwiązań — w przypadku używania obrazów podstawowych dla obrazów aplikacji użyj automatyzacji, aby kompilować nowe obrazy po zaktualizowaniu obrazu podstawowego. Ponieważ te obrazy podstawowe zazwyczaj zawierają poprawki zabezpieczeń, zaktualizuj wszelkie podrzędne obrazy kontenerów aplikacji.

Za każdym razem, gdy obraz podstawowy zostanie zaktualizowany, należy również zaktualizować wszystkie obrazy kontenerów podrzędnych. Ten proces kompilacji należy zintegrować z potokami walidacji i wdrażania, takimi jak [Azure Pipelines][azure-pipelines] lub Jenkins. Te potoki sprawdzają, czy aplikacje nadal działają na zaktualizowanych obrazach. Po sprawdzeniu poprawności obrazów kontenera aplikacji wdrożenia AKS można zaktualizować w celu uruchamiania najnowszych, bezpiecznych obrazów.

Azure Container Registry zadania mogą również automatycznie aktualizować obrazy kontenerów po zaktualizowaniu obrazu podstawowego. Ta funkcja umożliwia utworzenie niewielkiej liczby obrazów podstawowych i regularne aktualizowanie ich przy użyciu poprawek usterek i zabezpieczeń.

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