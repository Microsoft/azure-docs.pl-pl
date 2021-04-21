---
title: Zagadnienia dotyczące zabezpieczeń wystąpień kontenerów
description: Zalecenia dotyczące zabezpieczania obrazów i wpisów tajnych dla Azure Container Instances oraz ogólne zagadnienia dotyczące zabezpieczeń dla dowolnej platformy kontenerów
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: acccd79ecd1c216f92f19d1cf035682414cd17ca
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750144"
---
# <a name="security-considerations-for-azure-container-instances"></a>Zagadnienia dotyczące zabezpieczeń Azure Container Instances

W tym artykule oprowadzono zagadnienia dotyczące zabezpieczeń związane z używaniem Azure Container Instances do uruchamiania aplikacji kontenera. Tematy obejmują:

> [!div class="checklist"]
> * **Zalecenia dotyczące zabezpieczeń** zarządzania obrazami i wpisami tajnymi dla Azure Container Instances
> * **Zagadnienia dotyczące ekosystemu kontenerów w** całym cyklu życia kontenera dla dowolnej platformy kontenerów

Aby uzyskać kompleksowe zalecenia, które pomogą Ci poprawić poziom bezpieczeństwa wdrożenia, zobacz Punkt odniesienia zabezpieczeń platformy Azure dla [Container Instances](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Zalecenia dotyczące zabezpieczeń Azure Container Instances

### <a name="use-a-private-registry"></a>Korzystanie z rejestru prywatnego

Kontenery są kompilowane na podstawie obrazów przechowywanych w co najmniej jednym repozytorium. Te repozytoria mogą należeć do rejestru publicznego, takiego [Docker Hub](https://hub.docker.com), lub do rejestru prywatnego. Przykładem rejestru prywatnego jest rejestr [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), który można zainstalować w środowisku lokalnym lub w wirtualnej chmurze prywatnej. Można również używać chmurowych usług rejestru kontenerów prywatnych, w tym [Azure Container Registry](../container-registry/container-registry-intro.md). 

Publicznie dostępny obraz kontenera nie gwarantuje bezpieczeństwa. Obrazy kontenerów składają się z wielu warstw oprogramowania, a każda warstwa oprogramowania może mieć luki w zabezpieczeniach. Aby zmniejszyć zagrożenie atakami, należy przechowywać i pobierać obrazy z rejestru prywatnego, takiego jak Azure Container Registry lub Docker Trusted Registry. Oprócz zapewniania zarządzanego rejestru prywatnego usługa Azure Container Registry [](../container-registry/container-registry-authentication.md) uwierzytelnianie oparte na jednostki usługi za pośrednictwem usługi Azure Active Directory przepływów uwierzytelniania podstawowego. To uwierzytelnianie obejmuje dostęp oparty na rolach dla uprawnień tylko do odczytu (ściąganie), zapis (wypychanie) i innych.

### <a name="monitor-and-scan-container-images"></a>Monitorowanie i skanowanie obrazów kontenerów

Skorzystaj z zalet rozwiązań do skanowania obrazów kontenerów w prywatnym rejestrze i identyfikowania potencjalnych luk w zabezpieczeniach. Ważne jest, aby zrozumieć głębokość wykrywania zagrożeń zapewnianą przez różne rozwiązania.

Na przykład program Azure Container Registry opcjonalnie [integruje](../security-center/defender-for-container-registries-introduction.md) się z Azure Security Center w celu automatycznego skanowania wszystkich obrazów systemu Linux wypchnięć do rejestru. Azure Security Center firmy Qualys wykrywa luki w zabezpieczeniach obrazów, klasyfikuje je i udostępnia wskazówki dotyczące korygowania.

Rozwiązania do monitorowania zabezpieczeń i skanowania obrazów, takie jak [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) i [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) są również dostępne za pośrednictwem Azure Marketplace.  

### <a name="protect-credentials"></a>Ochrona poświadczeń

Kontenery można rozłożyć na kilka klastrów i regionów świadczenia usługi Azure. Dlatego należy zabezpieczyć poświadczenia wymagane do logowania lub dostępu do interfejsu API, takie jak hasła lub tokeny. Upewnij się, że tylko uprzywilejowane użytkownicy mogą uzyskać dostęp do tych kontenerów podczas przesyłania i przechowywania. Spis wszystkich wpisów tajnych poświadczeń, a następnie wymaga od deweloperów korzystania z nowych narzędzi do zarządzania wpisami tajnymi, które są przeznaczone dla platform kontenerów.  Upewnij się, że twoje rozwiązanie obejmuje zaszyfrowane bazy danych, szyfrowanie TLS dla danych wpisów tajnych w trakcie przesyłania oraz kontrolę dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md)na platformie Azure z najmniejszymi uprawnieniami. [Azure Key Vault](../key-vault/general/security-overview.md) to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia i hasła) dla konteneryzowanych aplikacji. Ponieważ te dane są poufne i krytyczne dla działalności firmy, bezpieczny dostęp do magazynów kluczy, dzięki czemu tylko autoryzowane aplikacje i użytkownicy mogą uzyskać do nich dostęp.

## <a name="considerations-for-the-container-ecosystem"></a>Zagadnienia dotyczące ekosystemu kontenerów

Następujące środki zabezpieczeń, wdrożone dobrze i efektywnie zarządzane, mogą pomóc w zabezpieczania i ochronie ekosystemu kontenerów. Te miary mają zastosowanie w całym cyklu życia kontenera, od procesów tworzenia do wdrożenia produkcyjnego, po szereg orkiestratorów kontenerów, hostów i platform. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Korzystanie z zarządzania lukami w zabezpieczeniach w ramach cyklu projektowania kontenerów 

Dzięki efektywnemu zarządzaniu lukami w zabezpieczeniach w całym cyklu projektowania kontenerów można zwiększyć prawdopodobieństwo, że zidentyfikuje się i rozwiąże problemy związane z zabezpieczeniami, zanim staną się bardziej poważnym problemem. 

### <a name="scan-for-vulnerabilities"></a>Skanowanie w poszukiwaniu luk w zabezpieczeniach 

Przez cały czas są odkrywane nowe luki w zabezpieczeniach, więc skanowanie i identyfikowanie luk w zabezpieczeniach jest procesem ciągłym. Uwzględniaj skanowanie luk w zabezpieczeniach w całym cyklu życia kontenera:

* W celu ostatecznego sprawdzenia potoku projektowego należy wykonać skanowanie kontenerów pod poszukiwaniu luk w zabezpieczeniach przed wypchnięciem obrazów do rejestru publicznego lub prywatnego. 
* Kontynuuj skanowanie obrazów kontenerów w rejestrze zarówno w celu zidentyfikowania wszelkich błędów, które zostały pominięte podczas opracowywania, jak i w celu rozwiązania wszelkich nowo odnalezionych luk w zabezpieczeniach, które mogą istnieć w kodzie używanym w obrazach kontenerów.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapowanie luk w zabezpieczeniach obrazów na uruchomione kontenery 

Musisz mieć możliwość mapowania luk w zabezpieczeniach zidentyfikowanych w obrazach kontenerów na uruchomione kontenery, aby można było ograniczyć lub rozwiązać problemy z zabezpieczeniami.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Upewnij się, że w Twoim środowisku są używane tylko zatwierdzone obrazy 

W ekosystemie kontenerów istnieje wystarczająca ilość zmian i zmienności bez zezwalania na nieznane kontenery. Zezwalaj tylko na zatwierdzone obrazy kontenerów. Mieć dostępne narzędzia i procesy do monitorowania niezatwierdzonych obrazów kontenerów i zapobiegania im. 

Efektywnym sposobem zmniejszenia powierzchni ataku i uniemożliwienie deweloperom popełniania krytycznych błędów zabezpieczeń jest kontrolowanie przepływu obrazów kontenerów do środowiska projektowego. Na przykład można oficjalne rozpowszechnianie systemu Linux jako obrazu podstawowego, najlepiej takiego, który jest bazą danych (Alpine lub CoreOS, a nie Ubuntu), aby zminimalizować powierzchnię potencjalnych ataków. 

Podpisywanie obrazów lub pobieranie odcisków palców może zapewnić łańcuch kontroli, który umożliwia sprawdzenie integralności kontenerów. Na przykład program Azure Container Registry model zaufania [](https://docs.docker.com/engine/security/trust/content_trust) do zawartości platformy Docker, który umożliwia wydawcom obrazów podpisywanie obrazów wypychanych do rejestru, a użytkownikom obrazów ściąganie tylko podpisanych obrazów.

### <a name="permit-only-approved-registries"></a>Zezwalanie tylko na zatwierdzone rejestry 

Rozszerzeniem zapewniającym, że środowisko używa tylko zatwierdzonych obrazów, jest zezwolenie tylko na korzystanie z zatwierdzonych rejestrów kontenerów. Wymaganie użycia zatwierdzonych rejestrów kontenerów zmniejsza narażenie na ryzyko przez ograniczenie możliwości wprowadzenia nieznanych luk w zabezpieczeniach lub problemów z zabezpieczeniami. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zapewnianie integralności obrazów w całym cyklu życia 

Częścią zarządzania zabezpieczeniami w całym cyklu życia kontenera jest zapewnienie integralności obrazów kontenerów w rejestrze i podczas ich modyfikowania lub wdrażania w środowisku produkcyjnym. 

* Obrazy z lukami w zabezpieczeniach, nawet drobnymi, nie powinny być uruchamiane w środowisku produkcyjnym. W idealnym przypadku wszystkie obrazy wdrożone w środowisku produkcyjnym powinny być zapisywane w rejestrze prywatnym dostępnym dla wybranych osób. Zachowaj małą liczbę obrazów produkcyjnych, aby zapewnić efektywne zarządzanie nimi.

* Ponieważ trudno jest wskazać źródło oprogramowania z publicznie dostępnego obrazu kontenera, skompilować obrazy ze źródła, aby zapewnić wiedzę o pochodzeniu warstwy. Gdy w samodzielnie kompilowanym obrazie kontenera pojawi się luka w zabezpieczeniach, klienci będą mogli szybciej znaleźć ścieżkę do rozwiązania. W przypadku obrazu publicznego klienci musieliby znaleźć katalog główny obrazu publicznego, aby go naprawić, lub uzyskać inny bezpieczny obraz od wydawcy. 

* Nie ma gwarancji, że dokładnie zeskanowany obraz wdrożony w środowisku produkcyjnym będzie aktualny przez cały okres istnienia aplikacji. Luki w zabezpieczeniach można zgłaszać dla warstw obrazu, które nie były wcześniej znane lub pojawiły się po wdrożeniu produkcyjnym. 

  Okresowo przeprowadzaj inspekcję obrazów wdrożonych w środowisku produkcyjnym, aby zidentyfikować obrazy, które są aktualne lub nie zostały zaktualizowane przez pewien czas. Aby zaktualizować obrazy kontenerów bez przestojów, można użyć metodologii wdrażania niebieski-zielony i mechanizmów uaktualniania stopniowego. Obrazy można skanować przy użyciu narzędzi opisanych w poprzedniej sekcji. 

* Użyj potoku ciągłej integracji ze zintegrowanym skanowaniem zabezpieczeń, aby tworzyć bezpieczne obrazy i wypychać je do rejestru prywatnego. Skanowanie luk w zabezpieczeniach wbudowane w rozwiązaniu CI gwarantuje, że obrazy, które przejdą wszystkie testy, zostaną wypchnięte do rejestru prywatnego, z poziomu którego odbywa się wdrażanie obciążeń produkcyjnych. 

  Błąd potoku ci gwarantuje, że obrazy narażone na zagrożenia nie są wypychane do rejestru prywatnego używanego na potrzeby wdrożeń obciążeń produkcyjnych. Automatyzuje również skanowanie zabezpieczeń obrazów, jeśli istnieje znaczna liczba obrazów. W przeciwnym razie proces ręcznego przeprowadzania inspekcji obrazów w celu wyszukania luk w zabezpieczeniach może być niezwykle żmudny i narażony na błędy. 

### <a name="enforce-least-privileges-in-runtime"></a>Wymuszanie najmniejszych uprawnień w środowisku uruchomieniowym 

Koncepcja najmniejszych uprawnień jest podstawowym najlepszym rozwiązaniem w zakresie zabezpieczeń, które ma również zastosowanie do kontenerów. Wykorzystanie luki w zabezpieczeniach zwykle zapewnia osobie atakującej dostęp i uprawnienia takie same jak w przypadku aplikacji lub procesu, której bezpieczeństwo zostało naruszone. Zapewnienie, że kontenery działają z najniższymi uprawnieniami i dostępem wymaganym do zadania, zmniejsza narażenie na ryzyko. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Zmniejsz powierzchnię ataku na kontener, usuwając niepotrzebnych uprawnień 

Możesz również zminimalizować potencjalną powierzchnię ataku, usuwając wszystkie nieużywane lub niepotrzebne procesy bądź uprawnienia ze środowiska uruchomieniowego kontenera. Kontenery uprzywilejowane działają jako główne. W przypadku ucieczki złośliwego użytkownika lub obciążenia w uprzywilejowanym kontenerze kontener zostanie uruchomiony jako użytkownik główny w tym systemie.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Wstępnie zatrowuj pliki i pliki wykonywalne, do których kontener może uzyskać dostęp lub do niego uruchomić 

Zmniejszenie liczby zmiennych lub nieznanych danych pomaga w utrzymaniu stabilnego i niezawodnego środowiska. Ograniczanie kontenerów tak, aby umożliwiały dostęp lub uruchamianie tylko wstępnie zatwierdzonych lub bezpiecznych plików i plików wykonywalnych, jest sprawdzoną metodą ograniczania narażenia na ryzyko.  

Zarządzanie listą bezpiecznych od początku jest znacznie łatwiejsze. Lista bezpiecznych aplikacji zapewnia miarę kontroli i możliwości zarządzania, gdy dowiesz się, jakie pliki i pliki wykonywalne są wymagane do poprawnego działania aplikacji. 

Lista bezpiecznych aplikacji nie tylko zmniejsza powierzchnię ataku, ale może również stanowić punkt odniesienia dla anomalii i zapobiegać przypadkom użycia scenariuszy "hałaśliwego sąsiada" i awarii kontenera. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Wymuszanie segmentacji sieci w uruchomionych kontenerach  

Aby ułatwić ochronę kontenerów w jednej podsieci przed zagrożeniami bezpieczeństwa w innej podsieci, należy zachować segmentację sieci (lub nanosegmentację) lub segregację między uruchomionymi kontenerami. Utrzymanie segmentacji sieci może być również konieczne do korzystania z kontenerów w branżach, które są wymagane do spełnienia wymagań dotyczących zgodności.  

Na przykład narzędzie partnerskie [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zapewnia zautomatyzowane podejście do segmentacji nano. Akwamaryna monitoruje działania sieci kontenerów w środowisku uruchomieniowym. Identyfikuje ona wszystkie przychodzące i wychodzące połączenia sieciowe do/z innych kontenerów, usług, adresów IP i publicznego Internetu. Segmentacja Nano jest tworzona automatycznie na podstawie monitorowanego ruchu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorowanie aktywności kontenera i dostępu użytkowników 

Podobnie jak w przypadku każdego środowiska IT, należy stale monitorować aktywność i dostęp użytkowników do ekosystemu kontenerów, aby szybko identyfikować wszelkie podejrzane lub złośliwe działania. Platforma Azure udostępnia rozwiązania do monitorowania kontenerów, w tym:

* [Azure Monitor kontenerów monitoruje](../azure-monitor/containers/container-insights-overview.md) wydajność obciążeń wdrożonych w środowiskach Kubernetes hostowanych w Azure Kubernetes Service (AKS). Azure Monitor kontenerów zapewnia widoczność wydajności dzięki zbieraniu metryk pamięci i procesora z kontrolerów, węzłów i kontenerów, które są dostępne na platformie Kubernetes za pośrednictwem interfejsu API metryk. 

* Rozwiązanie [do monitorowania kontenerów platformy Azure](../azure-monitor/containers/containers.md) ułatwia wyświetlanie innych hostów kontenerów platformy Docker i systemu Windows oraz zarządzanie nimi w jednej lokalizacji. Na przykład:

  * Wyświetl szczegółowe informacje inspekcji, które pokazują polecenia używane z kontenerami. 
  * Rozwiązywanie problemów z kontenerami przez wyświetlanie i wyszukiwanie scentralizowanych dzienników bez konieczności zdalnego wyświetlania hostów platformy Docker lub systemu Windows.  
  * Znajdowanie kontenerów, które mogą być hałaśliwe i zużywać nadmiarowe zasoby na hoście.
  * Wyświetlanie scentralizowanych informacji o użyciu procesora CPU, pamięci, magazynie i sieci oraz wydajności kontenerów.  

  Rozwiązanie obsługuje orkiestraty kontenerów, w tym Docker Swarm, DC/OS, niezarządzane rozwiązania Kubernetes, Service Fabric i Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorowanie aktywności zasobów kontenera 

Monitoruj aktywność zasobów, takich jak pliki, sieć i inne zasoby, do których twoje kontenery mają dostęp. Monitorowanie aktywności zasobów i zużycia jest przydatne zarówno w przypadku monitorowania wydajności, jak i jako miara zabezpieczeń. 

[Azure Monitor](../azure-monitor/overview.md) podstawowe monitorowanie usług platformy Azure, umożliwiając zbieranie metryk, dzienników aktywności i dzienników diagnostycznych. Na przykład dziennik aktywności informuje o utworzeniu lub zmodyfikowaniu nowych zasobów. 

  Są dostępne metryki, które dostarczają statystyki wydajności dla różnych zasobów, a nawet systemu operacyjnego w maszynie wirtualnej. Można wyświetlić te dane za pomocą jednego z eksploratorów w witrynie Azure Portal i utworzyć alerty na podstawie tych metryk. Usługa Azure Monitor dostarcza najszybszy potok metryk (w zakresie od 5 minut do 1 minuty), dlatego należy jej używać do krytycznych czasowo alertów i powiadomień. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Rejestrowanie całego dostępu użytkownika administracyjnego kontenera na czas inspekcji 

Zachowaj dokładny dziennik inspekcji dostępu administracyjnego do ekosystemu kontenerów, w tym klastra Kubernetes, rejestru kontenerów i obrazów kontenerów. Te dzienniki mogą być niezbędne do celów inspekcji i będą przydatne jako dowód śledczy po każdym zdarzeniu zabezpieczeń. Rozwiązania platformy Azure obejmują:

* [Integracja Azure Kubernetes Service z Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) w celu monitorowania konfiguracji zabezpieczeń środowiska klastra i generowania zaleceń dotyczących zabezpieczeń
* [Rozwiązanie do monitorowania kontenerów platformy Azure](../azure-monitor/containers/containers.md)
* Dzienniki zasobów dla [Azure Container Instances](container-instances-log-analytics.md) i [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Następne kroki

* Zobacz punkt [odniesienia zabezpieczeń platformy Azure Container Instances,](security-baseline.md) aby uzyskać kompleksowe zalecenia, które pomogą Ci poprawić poziom bezpieczeństwa wdrożenia.

* Dowiedz się więcej o [używaniu Azure Security Center](../security-center/container-security.md) do wykrywania zagrożeń w czasie rzeczywistym w środowiskach konteneryzowanych.

* Dowiedz się więcej na temat zarządzania lukami w zabezpieczeniach kontenerów za pomocą rozwiązań [firm Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) i [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)