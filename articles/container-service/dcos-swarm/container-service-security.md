---
title: PRZESTARZAŁE Zabezpieczenia kontenera w Azure Container Service
description: Zagadnienia dotyczące zabezpieczania kontenerów platformy Docker wdrożonych w Azure Container Service i powiązanych usługach platformy Azure.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d90d872175febf775e7d0892e133883f1a8ff8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552392"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>PRZESTARZAŁE Zabezpieczanie kontenerów platformy Docker w Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym artykule przedstawiono zagadnienia i zalecenia dotyczące zabezpieczania kontenerów Docker wdrożonych w usłudze Azure Container Service. Wiele z tych zagadnień zazwyczaj stosuje się do kontenerów Docker wdrożonych na platformie Azure lub innych środowiskach. 

## <a name="image-security"></a>Zabezpieczenia obrazu

Kontenery są kompilowane na podstawie obrazów przechowywanych w co najmniej jednym repozytorium. Te repozytoria mogą należeć do publicznych lub prywatnych rejestrów kontenerów. Przykładem rejestru publicznego jest usługa [Docker Hub](https://hub.docker.com/). Przykładem rejestru prywatnego jest rejestr [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), który można zainstalować w środowisku lokalnym lub w wirtualnej chmurze prywatnej. Dostępne są także usługi prywatnych rejestrów kontenerów oparte na chmurze, w tym usługa [Azure Container Registry](../../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Obrazy publiczne i prywatne
Ogólnie — podobnie jak w przypadku każdego publikowanego publicznie pakietu oprogramowania — publicznie dostępny obraz kontenera nie gwarantuje bezpieczeństwa. Obrazy kontenera składają się z wielu warstw oprogramowania, a każda warstwa oprogramowania może mieć luki w zabezpieczeniach. Kluczowym zagadnieniem jest prawidłowe rozpoznanie źródła obrazu kontenera, w tym właściciela obrazu (w celu określenia, czy jest to źródło wiarygodne), warstw oprogramowania wchodzących w jego skład i wersji oprogramowania. 

Jeśli na przykład przejdziesz do oficjalnego [repozytorium nginx](https://hub.docker.com/_/nginx/) w usłudze Docker Hub, a następnie do karty **Tags** (Tagi), zobaczysz oznaczone kolorami luki w zabezpieczeniach dla każdego obrazu. Każdy kolor oznacza lukę w zabezpieczeniach warstwy oprogramowania obrazu. Aby uzyskać więcej informacji o wyszukiwaniu luk w zabezpieczeniach w usłudze Docker Hub, zobacz [Understanding official repos on Docker Hub (Opis oficjalnych repozytoriów w usłudze Docker Hub)](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/).

![Obrazy nginx w usłudze Docker Hub](./media/container-service-security/docker-hub-nginx.png)

Przedsiębiorstwa bardzo dbają o bezpieczeństwo. Aby ochronić się przed atakami, powinny przechowywać i pobierać obrazy przy użyciu rejestru prywatnego, takiego jak usługa Azure Container Registry lub Docker Trusted Registry. Usługa Azure Container Registry nie tylko oferuje zarządzany rejestr prywatny — obsługuje ona także [uwierzytelnianie oparte na jednostce usługi](../../container-registry/container-registry-authentication.md) za pośrednictwem usługi Azure Active Directory dla podstawowych przepływów uwierzytelniania, w tym oparty na rolach dostęp do uprawnień tylko do odczytu, zapisu i właściciela.

### <a name="image-security-scanning"></a>Skanowanie zabezpieczeń obrazów

Nawet w przypadku używania rejestru prywatnego warto używać rozwiązań do skanowania obrazów w celu przeprowadzenia dodatkowej weryfikacji zabezpieczeń. Każda warstwa oprogramowania w obrazie kontenera jest potencjalnie podatna na luki w zabezpieczeniach niezależnie od innych warstw obrazu kontenera. Coraz więcej firm zaczyna wdrażać obciążenia produkcyjne przy użyciu technologii związanych z kontenerami, a skanowanie obrazów staje się ważne, ponieważ zapobiega atakom na zabezpieczenia organizacji. 

Rozwiązanie do skanowania i monitorowania zabezpieczeń, takie jak między innymi [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) i [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry), mogą być używane do skanowania obrazów kontenerów w rejestrze prywatnym oraz identyfikowania potencjalnych luk w zabezpieczeniach. Ważne jest, aby dobrze zrozumieć poziom szczegółowości oferowany przez poszczególne rozwiązania do skanowania. Na przykład niektóre rozwiązania mogą tylko sprawdzać warstwy obrazu pod kątem występowania znanych luk w zabezpieczeniach. Te rozwiązania mogą nie mieć możliwości zweryfikowania oprogramowania w warstwie obrazu, którą skompilowano przy użyciu określonego oprogramowania menedżera pakietów. Inne rozwiązania oferują głębszą integrację skanowania i mogą wyszukiwać luki w zabezpieczeniach dowolnego oprogramowania w pakiecie.

### <a name="production-deployment-rules-and-audit"></a>Reguły wdrożenia produkcyjnego i inspekcja
Gdy aplikacja jest wdrażana w środowisku produkcyjnym, niezbędne jest skonfigurowanie kilku reguł, dzięki którym będzie można zapewnić, że obrazy używane w środowisku produkcyjnym są bezpieczne i nie mają luk w zabezpieczeniach.

* Z zasady uruchamianie obrazów z lukami w zabezpieczeniach, nawet drobnymi, w środowisku produkcyjnym nie powinno być dozwolone. Ponadto wszystkie obrazy wdrożone w środowisku produkcyjnym powinny w idealnej sytuacji być zapisywane w rejestrze prywatnym, który jest dostępny tylko dla niewielkiej grupy wybranych osób. Ważne jest również, aby przechowywać małą liczbę obrazów produkcyjnych, którą będzie można efektywnie zarządzać.

* Ponieważ trudno jest wskazać źródło oprogramowania na podstawie publicznie dostępnego obrazu kontenera, dobrym rozwiązaniem jest kompilowanie obrazów na podstawie źródła w celu zidentyfikowania źródła warstwy. Gdy w samodzielnie kompilowanym obrazie kontenera pojawi się luka w zabezpieczeniach, klienci będą mogli szybciej znaleźć ścieżkę do rozwiązania. W przypadku obrazu publicznego klienci musieliby znaleźć katalog głównego tego obrazu, aby go naprawić, lub uzyskać inny zabezpieczony obraz od wydawcy.

* Nie ma gwarancji, że dokładnie zeskanowany obraz wdrożony w środowisku produkcyjnym będzie aktualny przez cały cykl życia aplikacji. Luki w zabezpieczeniach można zgłaszać dla warstw obrazu, które nie były wcześniej znane lub pojawiły się po wdrożeniu produkcyjnym. Okresowe inspekcje obrazów wdrożonych w środowisku produkcyjnym są niezbędne do identyfikowania obrazów, które są nieaktualne lub nie były przez pewien czas aktualizowane. Można na przykład korzystać z metodologii wdrażania blue-green i mechanizmów uaktualniania stopniowego, aby aktualizować obrazy kontenerów bez przestojów. Skanowanie obrazu można przeprowadzić za pomocą narzędzi opisanych w poprzedniej sekcji. 

* Potok ciągłej integracji (CI) umożliwiający kompilowanie obrazów oraz zintegrowane skanowanie zabezpieczeń mogą pomóc w obsłudze bezpiecznych rejestrów prywatnych z bezpiecznymi obrazami kontenerów. Skanowanie luk w zabezpieczeniach wbudowane w rozwiązaniu CI gwarantuje, że obrazy, które przejdą wszystkie testy, zostaną wypchnięte do rejestru prywatnego, z poziomu którego odbywa się wdrażanie obciążeń produkcyjnych. Błąd potoku CI zapewnia, że obrazy z lukami w zabezpieczeniach nie zostaną wypchnięte do rejestru prywatnego używanego na potrzeby wdrożeń produkcyjnych. Możliwe jest również automatyzowanie skanowania zabezpieczeń obrazów w przypadku znacznej liczby obrazów. W przeciwnym razie proces ręcznego przeprowadzania inspekcji obrazów w celu wyszukania luk w zabezpieczeniach może być niezwykle żmudny i narażony na błędy.

## <a name="host-level-container-isolation"></a>Izolacja kontenera na poziomie hosta
Aplikacje kontenera wdrażane przez klienta w zasobach platformy Azure są wdrażane na poziomie subskrypcji w grupach zasobów i nie są dostępne w wielu dzierżawach. Oznacza to, że jeśli klient udostępnia subskrypcję innym osobom, nie można utworzyć granic między dwoma wdrożeniami w tej samej subskrypcji. W związku z tym zabezpieczenia na poziomie kontenera nie są gwarantowane. 

Bardzo ważne jest również zrozumienie, że kontenery współużytkują jądro i zasoby hosta (w usłudze Azure Container Service jest to maszyna wirtualna platformy Azure w klastrze). Z tego względu kontenery działające w środowisku produkcyjnym muszą być uruchamiane w trybie użytkownika bez uprawnień. Uruchamianie kontenera przy użyciu uprawnień głównych może naruszyć bezpieczeństwo całego środowiska. W przypadku dostępu na poziomie katalogu głównego do kontenera haker może uzyskać dostęp do pełnych uprawnień głównych na hoście. Dodatkowo ważne jest, aby uruchamiać kontenery w systemach plików tylko do odczytu. Zapobiegnie to sytuacjom, w których osoba z prawami dostępu do kontenera, którego bezpieczeństwo zostało naruszone, będzie pisać złośliwe skrypty w systemie plików i uzyskiwać dostęp do innych plików. Podobnie ważne jest ograniczenie zasobów (np. pamięci, procesora i przepustowości sieci) przydzielanych do kontenera. Pozwala to zapobiec hakerom z zasobów zapobiegają one zajmowaniu i nielegalnym działaniom, takim jak oszustwa kart kredytowych lub wykorzystaniu bitowego monety, co może uniemożliwić uruchamianie innych kontenerów na hoście lub w klastrze.

## <a name="orchestrator-considerations"></a>Zagadnienia dotyczące koordynatora

Każdy koordynator dostępny w usłudze Azure Container Service ma własne względy bezpieczeństwa. Na przykład należy ograniczyć bezpośredni dostęp protokołu SSH do węzłów koordynatora w usłudze Container Service. W zamian należy używać interfejsu użytkownika lub narzędzi wiersza polecenia (takich jak narzędzie `kubectl` w rozwiązaniu Kubernetes) poszczególnych koordynatorów w celu zarządzania środowiskiem kontenera bez uzyskiwania dostępu do hostów.

Aby uzyskać dodatkowe informacje na temat zabezpieczeń poszczególnych koordynatorów, zobacz następujące zasoby:

* **Kubernetes**: [Security Best Practices for Kubernetes Deployment (Kubernetes: najlepsze rozwiązania dotyczące zabezpieczeń na potrzeby wdrażania rozwiązania Kubernetes)](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**: [Securing Your Cluster (DC/OS: zabezpieczanie klastra)](https://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**: [Docker Security (Docker Swarm: zabezpieczenia platformy Docker)](https://www.docker.com/docker-security)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o architekturze platformy Docker i zabezpieczeniach kontenerów, zobacz [Introduction to Container Security (Wprowadzenie do usługi Container Security)](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf).

* Aby uzyskać informacje na temat zabezpieczeń platformy, zobacz [Azure Security Center](https://www.microsoft.com/trustcenter/cloudservices/azure).
