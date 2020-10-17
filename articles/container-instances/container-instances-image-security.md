---
title: Zagadnienia dotyczące zabezpieczeń dla wystąpień kontenerów
description: Zalecenia dotyczące zabezpieczania obrazów i wpisów tajnych Azure Container Instances i ogólne zagadnienia dotyczące zabezpieczeń dla dowolnej platformy kontenera
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 898bdf77bf4b6636e78f5d735fc8650da4fde2b8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148657"
---
# <a name="security-considerations-for-azure-container-instances"></a>Zagadnienia dotyczące zabezpieczeń Azure Container Instances

W tym artykule przedstawiono zagadnienia dotyczące zabezpieczeń związane z używaniem Azure Container Instances do uruchamiania aplikacji kontenera. Tematy obejmują:

> [!div class="checklist"]
> * **Zalecenia dotyczące zabezpieczeń** dotyczące zarządzania obrazami i wpisami tajnymi dla Azure Container Instances
> * **Zagadnienia dotyczące ekosystemu kontenerów** w całym cyklu życia kontenera dla każdej platformy kontenera

Aby zapoznać się z kompleksowymi zaleceniami, które pomogą ulepszyć stan bezpieczeństwa wdrożenia, zobacz [podstawy zabezpieczeń platformy Azure dla Container Instances](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Zalecenia dotyczące zabezpieczeń Azure Container Instances

### <a name="use-a-private-registry"></a>Korzystanie z rejestru prywatnego

Kontenery są kompilowane na podstawie obrazów przechowywanych w co najmniej jednym repozytorium. Te repozytoria mogą należeć do rejestru publicznego, takiego jak [Docker Hub](https://hub.docker.com)lub do rejestru prywatnego. Przykładem rejestru prywatnego jest rejestr [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), który można zainstalować w środowisku lokalnym lub w wirtualnej chmurze prywatnej. Można również korzystać z usług rejestru prywatnego kontenerów opartych na chmurze, w tym [Azure Container Registry](../container-registry/container-registry-intro.md). 

Publicznie dostępny obraz kontenera nie gwarantuje zabezpieczeń. Obrazy kontenerów składają się z wielu warstw oprogramowania, a każda warstwa oprogramowania może mieć luki w zabezpieczeniach. Aby pomóc w zmniejszeniu zagrożenia atakami, należy przechowywać i pobierać obrazy z rejestru prywatnego, takich jak Azure Container Registry lub zaufany rejestr platformy Docker. Oprócz udostępniania rejestru prywatnego, Azure Container Registry obsługuje [uwierzytelnianie oparte na jednostce usługi](../container-registry/container-registry-authentication.md) za pośrednictwem Azure Active Directory na potrzeby przepływów uwierzytelniania podstawowego. To uwierzytelnianie obejmuje dostęp oparty na rolach na potrzeby uprawnień tylko do odczytu (ściągania), zapisu (wypychania) i innych.

### <a name="monitor-and-scan-container-images"></a>Monitorowanie i skanowanie obrazów kontenerów

Skorzystaj z rozwiązań, aby skanować obrazy kontenerów w rejestrze prywatnym i identyfikować potencjalne luki w zabezpieczeniach. Ważne jest, aby zrozumieć głębokość wykrywania zagrożeń, które są dostępne w różnych rozwiązaniach.

Na przykład, Azure Container Registry opcjonalnie [integruje się z Azure Security Center](../security-center/defender-for-container-registries-introduction.md) , aby automatycznie skanować wszystkie obrazy systemu Linux wypchnięte do rejestru. Azure Security Center zintegrowany skaner Qualys wykrywa luki w zabezpieczeniach obrazu, klasyfikuje je i udostępnia wskazówki dotyczące korygowania.

Rozwiązania do monitorowania zabezpieczeń i skanowania obrazów, takie jak [zabezpieczenia](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) [TwistLock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) i akwamaryna, są również dostępne w witrynie Azure Marketplace.  

### <a name="protect-credentials"></a>Ochrona poświadczeń

Kontenery mogą być rozłożone między kilka klastrów i regionów świadczenia usługi Azure. W związku z tym należy zabezpieczyć poświadczenia wymagane do logowania lub dostępu do interfejsu API, takie jak hasła lub tokeny. Upewnij się, że tylko użytkownicy uprzywilejowani mogą uzyskiwać dostęp do tych kontenerów podczas przesyłania i przechowywania. Wszystkie wpisy tajne poświadczeń, a następnie wymagają, aby deweloperzy korzystali z nowych narzędzi do zarządzania kluczami tajnymi, które są przeznaczone dla platform kontenerów.  Upewnij się, że rozwiązanie obejmuje zaszyfrowane bazy danych, szyfrowanie TLS w celu przesyłania danych tajnych oraz najniższy poziom [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączeń i hasła) dla aplikacji kontenerowych. Ponieważ te dane są poufne i mają krytyczne znaczenie dla działania firmy, bezpieczny dostęp do magazynów kluczy, tak aby tylko autoryzowane aplikacje i użytkownicy mieli do nich dostęp.

## <a name="considerations-for-the-container-ecosystem"></a>Zagadnienia dotyczące ekosystemu kontenerów

Następujące miary zabezpieczeń, zaimplementowane prawidłowo i zarządzane, mogą pomóc w zabezpieczeniu i ochronie ekosystemu kontenerów. Te środki są stosowane w całym cyklu życia kontenera, od programowania przy użyciu wdrożenia produkcyjnego oraz do zakresu koordynatorów kontenerów, hostów i platform. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Używanie zarządzania lukami w ramach cyklu życia projektowania kontenera 

Przy użyciu efektywnego zarządzania lukami w całym cyklu tworzenia kontenera można poprawić szanse, które identyfikują i rozwiązują problemy dotyczące zabezpieczeń, zanim staną się bardziej poważnymi problemami. 

### <a name="scan-for-vulnerabilities"></a>Skanuj w poszukiwaniu luk w zabezpieczeniach 

Nowe luki są wykrywane przez cały czas, dlatego skanowanie w poszukiwaniu i identyfikowanie luk w zabezpieczeniach jest procesem ciągłym. Uwzględnij skanowanie w całym cyklu życia kontenera:

* W celu ostatecznego sprawdzenia potoku programistycznego należy przeprowadzić skanowanie w poszukiwaniu luk w zabezpieczeniach kontenerów przed ich wypchnięciem do rejestru publicznego lub prywatnego. 
* Kontynuuj skanowanie obrazów kontenerów w rejestrze, aby identyfikować wszelkie wady, które były w jakiś sposób pominięte podczas opracowywania i rozwiązywać wszelkie nowo wykryte luki w zabezpieczeniach, które mogą istnieć w kodzie używanym w obrazach kontenera.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Zamapuj luki w zabezpieczeniach obrazu do uruchomionych kontenerów 

Konieczne jest zamapowanie luk w zabezpieczeniach, które zostały zidentyfikowane w obrazach kontenera do uruchamiania kontenerów, dzięki czemu problemy z zabezpieczeniami mogą być ograniczane lub rozwiązywane.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Upewnij się, że w danym środowisku są używane tylko zatwierdzone obrazy 

W ekosystemie kontenerów jest wystarczająca ilość zmian i lotnych danych bez zezwolenia na korzystanie z nieznanych kontenerów. Zezwalaj tylko na zatwierdzone obrazy kontenerów. Mają narzędzia i procesy, które mają być monitorowane pod kątem i uniemożliwiają korzystanie z niezatwierdzonych obrazów kontenerów. 

Efektywny sposób zmniejszenia obszaru ataków i uniemożliwianie deweloperom wykonywania krytycznych pomyłek w zabezpieczeniach polega na kontroli przepływu obrazów kontenera w środowisku deweloperskim. Na przykład można zastanowić się, że jedna dystrybucja systemu Linux jest oparta na podstawowym obrazie, najlepiej z jedną z nich, która jest Leane (Alpine lub CoreOS zamiast Ubuntu), aby zminimalizować powierzchnię potencjalnych ataków. 

Podpisywanie obrazów lub odciski palców mogą zapewnić łańcuch opieki, który umożliwia zweryfikowanie integralności kontenerów. Na przykład Azure Container Registry obsługuje model [zaufania zawartości](https://docs.docker.com/engine/security/trust/content_trust) platformy Docker, który umożliwia wydawcom obrazów podpisywanie obrazów, które są przekazywane do rejestru, oraz odbiorców obrazów do ściągania tylko podpisanych obrazów.

### <a name="permit-only-approved-registries"></a>Zezwalaj tylko na zatwierdzone rejestry 

Rozszerzenie zapewniające, że środowisko używa tylko zatwierdzonych obrazów, jest dozwolone tylko w przypadku korzystania z zatwierdzonych rejestrów kontenerów. Wymaganie korzystania z zatwierdzonych rejestrów kontenerów zmniejsza narażenie na ryzyko przez ograniczenie możliwości wprowadzenia nieznanych luk w zabezpieczeniach lub problemów z zabezpieczeniami. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zapewnij integralność obrazów w całym cyklu życia 

Zarządzanie zabezpieczeniami w całym cyklu życia kontenera polega na zapewnieniu spójności obrazów kontenerów w rejestrze i zmianie ich lub wdrożeniu w środowisku produkcyjnym. 

* Obrazy zawierające luki w zabezpieczeniach, nawet pomocnicze, nie mogą być uruchamiane w środowisku produkcyjnym. W idealnym przypadku wszystkie obrazy wdrożone w środowisku produkcyjnym powinny być zapisane w rejestrze prywatnym dostępnym dla wybranych kilku. Zachowaj niewielką liczbę obrazów produkcyjnych, aby upewnić się, że mogą one być efektywnie zarządzane.

* Ponieważ trudno jest wskazać źródło oprogramowania z publicznie dostępnego obrazu kontenera, Utwórz obrazy ze źródła, aby zapewnić wiedzę o pochodzeniu warstwy. Gdy w samodzielnie kompilowanym obrazie kontenera pojawi się luka w zabezpieczeniach, klienci będą mogli szybciej znaleźć ścieżkę do rozwiązania. W przypadku obrazu publicznego klienci chcą znaleźć katalog główny obrazu publicznego, aby rozwiązać ten problem, lub uzyskać inny bezpieczny obraz od wydawcy. 

* Starannie skanowany obraz wdrożony w środowisku produkcyjnym nie gwarantuje Aktualności przez okres istnienia aplikacji. Luki w zabezpieczeniach można zgłaszać dla warstw obrazu, które nie były wcześniej znane lub pojawiły się po wdrożeniu produkcyjnym. 

  Okresowe inspekcje obrazów wdrożonych w środowisku produkcyjnym w celu zidentyfikowania obrazów, które są nieaktualne lub nie zostały zaktualizowane w czasie. Aby aktualizować obrazy kontenerów bez przestojów, można używać wbudowanych metod wdrażania i stopniowego uaktualniania. Obrazy można skanować przy użyciu narzędzi opisanych w poprzedniej sekcji. 

* Użyj potoku ciągłej integracji z funkcją zintegrowanego skanowania zabezpieczeń, aby utworzyć bezpieczne obrazy i wypchnąć je do rejestru prywatnego. Skanowanie luk w zabezpieczeniach wbudowane w rozwiązaniu CI gwarantuje, że obrazy, które przejdą wszystkie testy, zostaną wypchnięte do rejestru prywatnego, z poziomu którego odbywa się wdrażanie obciążeń produkcyjnych. 

  Awaria potoku CI zapewnia, że zagrożone obrazy nie są przekazywane do rejestru prywatnego używanego do wdrożeń obciążeń produkcyjnych. Automatyzuje także skanowanie zabezpieczeń obrazów, jeśli istnieje znaczna liczba obrazów. W przeciwnym razie proces ręcznego przeprowadzania inspekcji obrazów w celu wyszukania luk w zabezpieczeniach może być niezwykle żmudny i narażony na błędy. 

### <a name="enforce-least-privileges-in-runtime"></a>Wymuszaj najmniejsze uprawnienia w czasie wykonywania 

Pojęcie najniższych uprawnień to podstawowe najlepsze rozwiązanie w zakresie zabezpieczeń, które ma zastosowanie również do kontenerów. Gdy jest wykorzystywana Luka w zabezpieczeniach, zapewnia ona zazwyczaj dostęp do osoby atakującej i uprawnienia takie same jak w przypadku zagrożonych aplikacji lub procesów. Upewnienie się, że kontenery działają z najniższymi uprawnieniami i dostęp wymagany do wykonania zadania zmniejsza ryzyko. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Zmniejszenie podatności na ataki z kontenera przez usunięcie niepotrzebnych uprawnień 

Możesz również zminimalizować potencjalną podatność na ataki, usuwając wszystkie nieużywane lub niepotrzebne procesy lub uprawnienia z środowiska uruchomieniowego kontenera. Kontenery uprzywilejowane są uruchamiane jako główne. Jeśli złośliwy użytkownik lub obciążenie będzie wyjść z uprzywilejowanego kontenera, kontener zostanie uruchomiony jako element główny w tym systemie.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Pretwierdź pliki i elementy wykonywalne, do których kontener może uzyskać dostęp 

Zmniejszenie liczby zmiennych lub nieznanych ułatwia zachowanie stabilnego, niezawodnego środowiska. Ograniczanie kontenerów, dzięki czemu mogą uzyskać dostęp do lub uruchamiać tylko pliki, które są zatwierdzone lub safelisted, oraz plików wykonywalnych jest sprawdzoną metodą ograniczania narażenia na ryzyko.  

Jest to znacznie prostsze zarządzanie Safelist, gdy jest on zaimplementowany od początku. Safelist zapewnia miarę kontroli i możliwości zarządzania, ponieważ dowiesz się, jakie pliki i elementy wykonywalne są wymagane do poprawnego działania aplikacji. 

Safelist nie tylko zmniejsza powierzchnię ataku, ale może także zapewnić podstawę dla anomalii i uniemożliwiać użycie przypadków użycia "sąsiadów" i scenariuszy kontenerów zagadnień. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Wymuś segmentację sieci na uruchomionych kontenerach  

Aby chronić kontenery w jednej podsieci przed zagrożeniami związanymi z bezpieczeństwem w innej podsieci, należy zachować segmentację sieci (lub segmentację nano) lub rozdzielenie między uruchomionymi kontenerami. Obsługa segmentacji sieci może być również niezbędna do korzystania z kontenerów w branżach, które są wymagane do spełnienia mandatów dotyczących zgodności.  

Na przykład narzędzie partnerskie [akwamaryna](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zapewnia zautomatyzowane podejście do segmentacji nano. Akwamaryna monitoruje aktywność sieci kontenera w środowisku uruchomieniowym. Identyfikuje wszystkie przychodzące i wychodzące połączenia sieciowe do/z innych kontenerów, usług, adresów IP i publicznego Internetu. Nano-segmentacja jest automatycznie tworzona na podstawie monitorowanego ruchu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorowanie aktywności kontenera i dostępu użytkowników 

Podobnie jak w przypadku każdego środowiska IT, należy spójnie monitorować aktywność i dostęp użytkowników do ekosystemu kontenerów, aby szybko identyfikować podejrzane lub złośliwe działania. Platforma Azure oferuje rozwiązania do monitorowania kontenerów, w tym:

* [Azure monitor dla kontenerów](../azure-monitor/insights/container-insights-overview.md) monitoruje wydajność obciążeń wdrożonych w środowiskach Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Azure Monitor for Containers zapewnia wgląd w wydajność przez zbieranie metryk pamięci i procesorów z kontrolerów, węzłów i kontenerów dostępnych w Kubernetes za pośrednictwem interfejsu API metryk. 

* [Rozwiązanie do monitorowania kontenerów platformy Azure](../azure-monitor/insights/containers.md) ułatwia wyświetlanie innych hostów platformy Docker i kontenera systemu Windows oraz zarządzanie nimi w jednej lokalizacji. Na przykład:

  * Wyświetlanie szczegółowych informacji o inspekcji, w których są wyświetlane polecenia używane z kontenerami. 
  * Rozwiązywanie problemów z kontenerami przez wyświetlanie i wyszukiwanie scentralizowanych dzienników bez konieczności zdalnego wyświetlania hostów platformy Docker lub Windows.  
  * Znajdowanie kontenerów, które mogą być zakłóceniami i zużywać nadmierne zasoby na hoście.
  * Wyświetlanie informacji o scentralizowanym użyciu procesora CPU, pamięci, magazynu i sieci oraz wydajności dla kontenerów.  

  Rozwiązanie obsługuje koordynatorów kontenerów, w tym Docker Swarm, DC/OS, niezarządzane Kubernetes, Service Fabric i Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorowanie aktywności zasobów kontenera 

Monitoruj aktywność zasobów, na przykład pliki, sieci i inne zasoby, do których mają dostęp kontenery. Monitorowanie wydajności i zużycia zasobów jest przydatne zarówno w przypadku monitorowania sprawności, jak i środków bezpieczeństwa. 

[Azure monitor](../azure-monitor/overview.md) umożliwia podstawowe monitorowanie usług platformy Azure, umożliwiając zbieranie metryk, dzienników aktywności i dzienników diagnostycznych. Na przykład dziennik aktywności informuje o utworzeniu lub zmodyfikowaniu nowych zasobów. 

  Są dostępne metryki, które dostarczają statystyki wydajności dla różnych zasobów, a nawet systemu operacyjnego w maszynie wirtualnej. Można wyświetlić te dane za pomocą jednego z eksploratorów w witrynie Azure Portal i utworzyć alerty na podstawie tych metryk. Usługa Azure Monitor dostarcza najszybszy potok metryk (w zakresie od 5 minut do 1 minuty), dlatego należy jej używać do krytycznych czasowo alertów i powiadomień. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Rejestruj wszystkich użytkowników administracyjnych kontenera do inspekcji 

Należy zachować dokładną historię inspekcji dostępu administracyjnego do ekosystemu kontenera, w tym klaster Kubernetes, rejestr kontenerów i obrazy kontenerów. Te dzienniki mogą być niezbędne do celów inspekcji i będą przydatne jako dowód śledczej po każdym zdarzeniu zabezpieczeń. Rozwiązania platformy Azure obejmują:

* [Integracja usługi Azure Kubernetes z usługą Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) , aby monitorować konfigurację zabezpieczeń środowiska klastra i generować zalecenia dotyczące zabezpieczeń
* [Rozwiązanie do monitorowania kontenerów platformy Azure](../azure-monitor/insights/containers.md)
* Dzienniki zasobów dla [Azure Container Instances](container-instances-log-analytics.md) i [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [podstawy zabezpieczeń platformy Azure dla Container Instances](security-baseline.md) , aby uzyskać kompleksowe zalecenia, które pomogą ulepszyć stan bezpieczeństwa wdrożenia.

* Dowiedz się więcej o korzystaniu z [Azure Security Center](../security-center/container-security.md) na potrzeby wykrywania zagrożeń w czasie rzeczywistym w środowiskach kontenerów.

* Dowiedz się więcej o zarządzaniu usterkami kontenera przy użyciu rozwiązań z [TwistLock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) i [akwamaryna Security](https://www.aquasec.com/solutions/azure-container-security/).