---
title: Wprowadzenie do chmury wiosennej platformy Azure
description: Poznaj funkcje i korzyści płynące z chmury Azure wiosennej, aby wdrażać aplikacje ze sprężyną Java na platformie Azure i zarządzać nimi.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: fc3f78131518b4d8740c25ab37c48d4444deef10
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563147"
---
# <a name="what-is-azure-spring-cloud"></a>Co to jest usługa Azure Spring Cloud?

Chmura sprężynowa platformy Azure ułatwia wdrażanie aplikacji mikrousług z rozruchem sprężyny na platformie Azure bez wprowadzania zmian w kodzie.  Usługa zarządza infrastrukturą aplikacji w chmurze, aby deweloperzy mogli skupić się na kodzie.  Chmura sprężynowa platformy Azure umożliwia zarządzanie cyklem życia przy użyciu kompleksowych funkcji monitorowania i diagnostyki, zarządzania konfiguracją, odnajdywania usług, integracji ciągłej i ciągłego wdrażania, a także w przypadku wdrożeń Blue-Green

## <a name="why-use-azure-spring-cloud"></a>Dlaczego warto korzystać z chmury wiosennej platformy Azure?

Wdrażanie aplikacji w chmurze z platformą Azure wiosną ma wiele korzyści.  Można:
* Efektywnie Migruj istniejące aplikacje sprężynowe i Zarządzaj skalowaniem i kosztami chmury.
* Modernizacja aplikacji ze wzorcami chmury wiosennej, aby zwiększyć elastyczność i szybkość dostarczania.
* Uruchamiaj środowisko Java w skali chmury i zwiększaj użycie bez skomplikowanej infrastruktury.
* Szybko Opracowuj i wdrażaj bez zależności kontenerach.
* Wydajnie i bezproblemowo Monitoruj obciążenia produkcyjne.

Chmura sprężynowa platformy Azure obsługuje zarówno aplikacje z [rozruchem ze sprężyną](https://spring.io/projects/spring-boot) Java, jak i ASP.NET Core [steeltoe](https://steeltoe.io/) . Pomoc techniczna steeltoe jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej pozwalają na eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją. Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz [często zadawane pytania](https://azure.microsoft.com/support/faq/) lub plik [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="service-overview"></a>Omówienie usługi

W ramach ekosystemu platformy Azure chmura sprężynowa platformy Azure umożliwia łatwe powiązanie z innymi usługami platformy Azure, w tym z magazynem, bazami danych, monitorowaniem i innymi.  

  ![Omówienie chmury Azure wiosennej](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Chmura sprężynowa platformy Azure to w pełni zarządzana usługa dla aplikacji do rozruchu sprężynowego, która umożliwia skoncentrowanie się na tworzeniu i uruchamianiu aplikacji bez konieczności zarządzania infrastrukturą.

* Po prostu Wdróż swój JARs lub kod, a chmura Wiosenna platformy Azure automatycznie łączy aplikacje ze środowiskiem uruchomieniowym usługi wiosny i wbudowanym cyklem życia aplikacji.

* Monitorowanie jest proste. Po wdrożeniu można monitorować wydajność aplikacji, naprawiać błędy i szybko ulepszać aplikacje. 

* Pełna integracja z ekosystemami i usługami platformy Azure.

* Chmura Wiosenna platformy Azure jest gotowa do użycia w pełni zarządzanej infrastrukturze, wbudowanego zarządzania cyklem życia i łatwość monitorowania.

## <a name="documentation-overview"></a>Przegląd dokumentacji
Ta dokumentacja zawiera sekcje, w których wyjaśniono, jak rozpocząć pracę i korzystać z usług w chmurze Azure wiosennej.

* Rozpoczęcie pracy
    * [Uruchamianie pierwszej aplikacji](spring-cloud-quickstart.md)
    * [Inicjowanie obsługi administracyjnej usługi w chmurze platformy Azure](spring-cloud-quickstart-provision-service-instance.md)
    * [Konfigurowanie serwera konfiguracji]()
    * [Kompilowanie i wdrażanie aplikacji](spring-cloud-quickstart-deploy-apps.md)
    * [Użycie dzienników metryk i śledzenia](spring-cloud-quickstart-logs-metrics-tracing.md)
* Porady
    * [Programowanie](spring-cloud-tutorial-prepare-app-deployment.md): przygotowanie istniejącej aplikacji ze sprężyną Java do wdrożenia w chmurze Azure wiosennej. Po poprawnym skonfigurowaniu usługa Azure Wiosenna Cloud oferuje niezawodne usługi do monitorowania, skalowania i aktualizowania aplikacji w chmurze ze sprężyną Java.
    * [Wdrażanie](spring-cloud-howto-staging-environment.md): jak skonfigurować wdrożenie przejściowe przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Wdrażanie niebieskie/zielone to wzorzec ciągłego dostarczania usługi Azure DevOps, który polega na utrzymywaniu działającej istniejącej wersji (niebieska) podczas wdrażania nowej (zielona).
    * [Skonfiguruj aplikacje](spring-cloud-howto-start-stop-delete.md): uruchamianie, zatrzymywanie i usuwanie aplikacji w chmurze platformy Azure. Zmień stan aplikacji w chmurze wiosennej platformy Azure za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.
    * [Skalowanie](spring-cloud-tutorial-scale-manual.md): Skaluj dowolną aplikację mikrousług przy użyciu pulpitu nawigacyjnego chmury z chmurą platformy Azure w Azure Portal lub przy użyciu ustawień skalowania automatycznego. Publiczne adresy IP są dostępne do komunikowania się z zasobami zewnętrznymi, na przykład bazami danych, magazynem i magazynami kluczy.
    * [Monitoruj aplikacje](spring-cloud-tutorial-distributed-tracing.md): narzędzia do śledzenia rozproszonego umożliwiające łatwe debugowanie i monitorowanie złożonych problemów. Chmura ze sprężyną Azure integruje się z chmurą Sleuth z platformą Application Insights Azure. Ta integracja zapewnia zaawansowane możliwości śledzenia rozproszonego na podstawie Azure Portal.
    * [Bezpieczne aplikacje](spring-cloud-howto-enable-system-assigned-managed-identity.md): zasoby platformy Azure zapewniają automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.
    * [Integracja z innymi usługami platformy Azure](spring-cloud-tutorial-bind-cosmos.md): Zamiast ręcznego konfigurowania aplikacji do rozruchu sprężynowego można automatycznie powiązać wybrane usługi platformy Azure z aplikacjami, na przykład wiążąc aplikację z bazą danych Azure Cosmos DB.
    * [Automatyzowanie](spring-cloud-howto-cicd.md): ciągłej integracji i ciągłego dostarczania narzędzi umożliwia szybkie wdrażanie aktualizacji istniejących aplikacji z minimalnym nakładem pracy i ryzykiem. Usługa Azure DevOps pomaga organizować i kontrolować te kluczowe zadania. 
    * [Rozwiązywanie problemów](spring-cloud-howto-self-diagnose-solve.md): Diagnostyka chmurowa Azure sprężynowa zapewnia interaktywny interfejs do rozwiązywania problemów z aplikacjami. Nie jest wymagana żadna konfiguracja. Po znalezieniu problemów Diagnostyka chmurowa platformy Azure identyfikuje problemy i prowadzi do informacji ułatwiających rozwiązywanie problemów i ich rozwiązywanie.
    * [Migrowanie](https://docs.microsoft.com/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): jak przeprowadzić migrację istniejącej aplikacji w chmurze lub aplikacji do rozruchu sprężynowego do uruchomienia w chmurze Azure wiosennej.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, Wypełnij [chmurę z przewodnikiem Szybki Start](spring-cloud-quickstart.md)

Przykłady są dostępne w serwisie GitHub: [przykłady chmur w chmurze platformy Azure](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
