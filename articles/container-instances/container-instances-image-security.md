---
title: Zabezpieczenia wystąpień kontenerów
description: Zalecenia dotyczące zabezpieczania obrazów i wpisów tajnych dla wystąpień kontenerów platformy Azure oraz ogólne zagadnienia dotyczące zabezpieczeń dla dowolnej platformy kontenera
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 87fa28cf9bdb546a5f108284023a9f787645a1fd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457998"
---
# <a name="security-considerations-for-azure-container-instances"></a>Zagadnienia dotyczące zabezpieczeń dla wystąpień kontenerów platformy Azure

W tym artykule przedstawiono zagadnienia dotyczące zabezpieczeń dotyczące używania wystąpień kontenera platformy Azure do uruchamiania aplikacji kontenerów. Tematy obejmują:

> [!div class="checklist"]
> * **Zalecenia dotyczące zabezpieczeń** dotyczące zarządzania obrazami i wpisami tajnymi dla wystąpień kontenerów platformy Azure
> * **Zagadnienia dotyczące ekosystemu kontenerów** w całym cyklu życia kontenera dla dowolnej platformy kontenerowej

## <a name="security-recommendations-for-azure-container-instances"></a>Zalecenia dotyczące zabezpieczeń dla wystąpień kontenerów platformy Azure

### <a name="use-a-private-registry"></a>Korzystanie z rejestru prywatnego

Kontenery są kompilowane na podstawie obrazów przechowywanych w co najmniej jednym repozytorium. Te repozytoria mogą należeć do rejestru publicznego, takiego jak [Centrum platformy Docker](https://hub.docker.com)lub do rejestru prywatnego. Przykładem rejestru prywatnego jest rejestr [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), który można zainstalować w środowisku lokalnym lub w wirtualnej chmurze prywatnej. Można również korzystać z usług rejestru kontenerów prywatnych opartych na chmurze, w tym [usługi Azure Container Registry](../container-registry/container-registry-intro.md). 

Publicznie dostępny obraz kontenera nie gwarantuje bezpieczeństwa. Obrazy kontenerów składają się z wielu warstw oprogramowania, a każda warstwa oprogramowania może mieć luki w zabezpieczeniach. Aby zmniejszyć zagrożenie atakami, należy przechowywać i pobierać obrazy z rejestru prywatnego, takiego jak Azure Container Registry lub Docker Trusted Registry. Oprócz zapewnienia zarządzanego rejestru prywatnego usługa Azure Container Registry obsługuje [uwierzytelnianie oparte na jednostkach usługi](../container-registry/container-registry-authentication.md) za pośrednictwem usługi Azure Active Directory dla podstawowych przepływów uwierzytelniania. To uwierzytelnianie obejmuje dostęp oparty na rolach dla uprawnień tylko do odczytu (ściąganie), zapisu (wypychania) i innych.

### <a name="monitor-and-scan-container-images"></a>Monitorowanie i skanowanie obrazów kontenerów

Skorzystaj z rozwiązań do skanowania obrazów kontenerów w rejestrze prywatnym i identyfikowania potencjalnych luk w zabezpieczeniach. Ważne jest, aby zrozumieć głębokość wykrywania zagrożeń, które zapewniają różne rozwiązania.

Na przykład usługa Azure Container Registry opcjonalnie [integruje się z usługą Azure Security Center,](../security-center/azure-container-registry-integration.md) aby automatycznie skanować wszystkie obrazy systemu Linux wypchnięte do rejestru. Zintegrowany skaner Qualys usługi Azure Security Center wykrywa luki w zabezpieczeniach obrazu, klasyfikuje je i zawiera wskazówki dotyczące korygowania.

Funkcje monitorowania zabezpieczeń i skanowania obrazów, takie jak [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) i [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) są również dostępne za pośrednictwem portalu Azure Marketplace.  

### <a name="protect-credentials"></a>Ochrona poświadczeń

Kontenery mogą rozprzestrzeniać się w kilku klastrach i regionach platformy Azure. Dlatego należy zabezpieczyć poświadczenia wymagane do logowania lub dostępu do interfejsu API, takich jak hasła lub tokeny. Upewnij się, że tylko użytkownicy uprzywilejowani mogą uzyskać dostęp do tych kontenerów podczas przesyłania i odpoczynku. Inwentaryzacja wszystkich wpisów tajnych poświadczeń, a następnie wymagają od deweloperów używania nowych narzędzi do zarządzania wpisami tajnymi, które są przeznaczone dla platform kontenerowych.  Upewnij się, że rozwiązanie zawiera zaszyfrowane bazy danych, szyfrowanie TLS dla przesyłanych danych wpisów tajnych oraz [kontrolę dostępu opartą na rolach o najmniejszych](../role-based-access-control/overview.md)uprawnieniach. [Usługa Azure Key Vault](../key-vault/general/secure-your-key-vault.md) to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia i hasła) dla aplikacji konteneryzowanych. Ponieważ te dane są poufne i krytyczne dla firmy, bezpieczny dostęp do magazynów kluczy, dzięki czemu tylko autoryzowane aplikacje i użytkownicy mogą uzyskać do nich dostęp.

## <a name="considerations-for-the-container-ecosystem"></a>Zagadnienia dotyczące ekosystemu kontenerów

Poniższe środki bezpieczeństwa, dobrze wdrożone i skutecznie zarządzane, mogą pomóc w zabezpieczeniu i ochronie ekosystemu kontenerów. Te środki mają zastosowanie w całym cyklu życia kontenera, od rozwoju przez wdrożenie produkcyjne i do zakresu koordynatorów kontenerów, hostów i platform. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Używanie zarządzania lukami w zabezpieczeniach w ramach cyklu życia tworzenia kontenerów 

Korzystając z skutecznego zarządzania lukami w zabezpieczeniach w całym cyklu życia tworzenia kontenerów, zwiększasz szanse, które identyfikujesz i rozwiązujesz problemy związane z bezpieczeństwem, zanim staną się one poważniejszym problemem. 

### <a name="scan-for-vulnerabilities"></a>Skanowanie w poszukiwaniu luk w zabezpieczeniach 

Nowe luki są odnajdywana przez cały czas, więc skanowanie i identyfikowanie luk w zabezpieczeniach jest procesem ciągłym. Uwzględnij skanowanie luk w zabezpieczeniach w całym cyklu życia kontenera:

* Jako ostateczne sprawdzenie w potoku rozwoju należy wykonać skanowanie luk w zabezpieczeniach na kontenerach przed wypchnięciem obrazów do rejestru publicznego lub prywatnego. 
* Kontynuuj skanowanie obrazów kontenerów w rejestrze, zarówno w celu zidentyfikowania wszelkich wad, które zostały w jakiś sposób pominięte podczas opracowywania, jak i w celu wyeliminowania nowo wykrytych luk w zabezpieczeniach, które mogą istnieć w kodzie używanym w obrazach kontenerów.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Luki w zabezpieczeniach obrazu mapuj na uruchomione kontenery 

Aby problemy z zabezpieczeniami można było ograniczyć lub rozwiązać, należy zidentyfikować środki umożliwiające zidentyfikowanie luk w zabezpieczeniach w obrazach kontenerów do uruchomionych kontenerów.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Upewnij się, że w twoim środowisku są używane tylko zatwierdzone obrazy 

Jest wystarczająco dużo zmian i zmienności w ekosystemie kontenerów, nie zezwalając również na nieznane kontenery. Zezwalaj tylko na zatwierdzone obrazy kontenerów. Posiadaj narzędzia i procesy do monitorowania niezatwierdzonych obrazów kontenerów i zapobiegania im. 

Skutecznym sposobem zmniejszenia powierzchni ataku i uniemożliwienia deweloperom popełniania krytycznych błędów zabezpieczeń jest kontrolowanie przepływu obrazów kontenerów do środowiska programistycznego. Na przykład można usankcjonować pojedynczą dystrybucję Linuksa jako obraz podstawowy, najlepiej taki, który jest chudy (Alpine lub CoreOS zamiast Ubuntu), aby zminimalizować powierzchnię potencjalnych ataków. 

Podpisywanie obrazów lub pobieranie odcisków palców może zapewnić łańcuch opieki, który umożliwia weryfikację integralności kontenerów. Na przykład usługa Azure Container Registry obsługuje model [zaufania zawartości](https://docs.docker.com/engine/security/trust/content_trust) platformy Docker, który umożliwia wydawcom obrazów podpisywanie obrazów wypychanych do rejestru i konsumentów obrazów do ściągania tylko podpisanych obrazów.

### <a name="permit-only-approved-registries"></a>Zezwalaj tylko na zatwierdzone rejestry 

Rozszerzenie zapewniając, że środowisko używa tylko zatwierdzonych obrazów jest zezwolenie tylko na korzystanie z zatwierdzonych rejestrów kontenerów. Wymóg stosowania zatwierdzonych rejestrów kontenerów zmniejsza narażenie na ryzyko, ograniczając możliwość wprowadzenia nieznanych luk w zabezpieczeniach lub problemów z bezpieczeństwem. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zapewnienie integralności obrazów w całym cyklu życia 

Częścią zarządzania zabezpieczeniami w całym cyklu życia kontenera jest zapewnienie integralności obrazów kontenerów w rejestrze i ich zmiany lub wdrożenia w środowisku produkcyjnym. 

* Obrazy z lukami, nawet niewielkie, nie powinny być dopuszczone do uruchamiania w środowisku produkcyjnym. W idealnym przypadku wszystkie obrazy wdrożone w produkcji powinny być zapisywane w rejestrze prywatnym dostępnym dla wybranych nielicznych. Zachowaj małą liczbę obrazów produkcyjnych, aby zapewnić ich skuteczne zarządzanie.

* Ponieważ trudno jest wskazać pochodzenie oprogramowania z publicznie dostępnego obrazu kontenera, twórz obrazy ze źródła, aby zapewnić znajomość pochodzenia warstwy. Gdy w samodzielnie kompilowanym obrazie kontenera pojawi się luka w zabezpieczeniach, klienci będą mogli szybciej znaleźć ścieżkę do rozwiązania. W razie publicznego obrazu klienci musieliby znaleźć katalog główny obrazu publicznego, aby go naprawić lub uzyskać inny bezpieczny obraz od wydawcy. 

* Dokładnie zeskanowany obraz wdrożony w produkcji nie jest gwarantowany, aby być aktualne przez cały okres istnienia aplikacji. Luki w zabezpieczeniach można zgłaszać dla warstw obrazu, które nie były wcześniej znane lub pojawiły się po wdrożeniu produkcyjnym. 

  Okresowo przeprowadzaj inspekcję obrazów wdrożonych w produkcji w celu zidentyfikowania obrazów, które są nieaktualne lub nie zostały zaktualizowane od jakiegoś czasu. Można użyć niebiesko-zielonych metodologii wdrażania i mechanizmów uaktualniania stopniowego, aby zaktualizować obrazy kontenerów bez przestojów. Obrazy można skanować za pomocą narzędzi opisanych w poprzedniej sekcji. 

* Użyj potoku ciągłej integracji (CI) ze zintegrowanym skanowaniem zabezpieczeń, aby tworzyć bezpieczne obrazy i wypychać je do rejestru prywatnego. Skanowanie luk w zabezpieczeniach wbudowane w rozwiązaniu CI gwarantuje, że obrazy, które przejdą wszystkie testy, zostaną wypchnięte do rejestru prywatnego, z poziomu którego odbywa się wdrażanie obciążeń produkcyjnych. 

  Błąd potoku ciągłej integracji gwarantuje, że obrazy podlegające usłyszeniu nie są wypychane do rejestru prywatnego, który jest używany do wdrożeń obciążenia produkcyjnego. Automatyzuje również skanowanie zabezpieczeń obrazu, jeśli istnieje znaczna liczba obrazów. W przeciwnym razie proces ręcznego przeprowadzania inspekcji obrazów w celu wyszukania luk w zabezpieczeniach może być niezwykle żmudny i narażony na błędy. 

### <a name="enforce-least-privileges-in-runtime"></a>Wymuszanie najmniejszych uprawnień w czasie wykonywania 

Pojęcie najmniejszych uprawnień jest podstawową najlepszą praktyką w zakresie zabezpieczeń, która ma również zastosowanie do kontenerów. Gdy luka w zabezpieczeniach jest wykorzystana, zazwyczaj daje osobie atakującej dostęp i uprawnienia równe tym, które zostały naruszone przez aplikację lub proces. Zapewnienie, że kontenery działają z najniższymi uprawnieniami i dostępem wymaganym do wykonania zadania, zmniejsza ryzyko. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Zmniejszanie powierzchni ataku kontenera przez usunięcie niepotrzebnych uprawnień 

Można również zminimalizować potencjalnej powierzchni ataku, usuwając wszelkie nieużywane lub niepotrzebne procesy lub uprawnienia ze środowiska wykonawczego kontenera. Kontenery uprzywilejowane są uruchamiane jako katalog główny. Jeśli złośliwy użytkownik lub obciążenie ucieknie w kontenerze uprzywilejowanym, kontener zostanie uruchomiony jako katalog główny w tym systemie.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Wstępnie zaaprowuj pliki i pliki wykonywalne, do których kontener może uzyskać dostęp lub uruchomić 

Zmniejszenie liczby zmiennych lub niewiadomych pomaga utrzymać stabilne i niezawodne środowisko. Ograniczenie kontenerów, dzięki czemu mogą uzyskać dostęp lub uruchomić tylko wstępnie zatwierdzone lub safelisted plików i plików wykonywalnych jest sprawdzoną metodą ograniczania narażenia na ryzyko.  

Dużo łatwiej jest zarządzać safelistą, gdy jest wdrażana od samego początku. Safelist zapewnia miarę kontroli i zarządzania, jak dowiedzieć się, jakie pliki i pliki wykonywalne są wymagane dla aplikacji do poprawnego działania. 

Safelist nie tylko zmniejsza powierzchni ataku, ale może również zapewnić linię bazową dla anomalii i zapobiec przypadkach użycia "hałaśliwy sąsiad" i scenariusze wyprysków kontenera. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Wymuszanie segmentacji sieci na uruchomionych kontenerach  

Aby chronić kontenery w jednej podsieci przed zagrożeniami bezpieczeństwa w innej podsieci, należy zachować segmentację sieci (lub nanosegmentację) lub segregację między uruchomionymi kontenerami. Utrzymanie segmentacji sieci może być również konieczne do korzystania z kontenerów w branżach, które są wymagane do spełnienia wymogów zgodności.  

Na przykład narzędzie partnerskie [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zapewnia zautomatyzowane podejście do nanosegmentacji. Aqua monitoruje działania sieci kontenerów w czasie wykonywania. Identyfikuje wszystkie przychodzące i wychodzące połączenia sieciowe do/z innych kontenerów, usług, adresów IP i publicznego Internetu. Nanosegmentacja jest tworzona automatycznie na podstawie monitorowaego ruchu. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorowanie aktywności kontenera i dostępu użytkowników 

Podobnie jak w przypadku każdego środowiska IT, należy konsekwentnie monitorować aktywność i dostęp użytkowników do ekosystemu kontenerów, aby szybko zidentyfikować wszelkie podejrzane lub złośliwe działania. Platforma Azure udostępnia rozwiązania do monitorowania kontenerów, w tym:

* [Usługa Azure Monitor dla kontenerów](../azure-monitor/insights/container-insights-overview.md) monitoruje wydajność obciążeń wdrożonych w środowiskach platformy Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Usługa Azure Monitor dla kontenerów zapewnia wgląd w wydajność, zbierając metryki pamięci i procesora z kontrolerów, węzłów i kontenerów, które są dostępne w usłudze Kubernetes za pośrednictwem interfejsu API metryk. 

* [Rozwiązanie do monitorowania kontenerów platformy Azure](../azure-monitor/insights/containers.md) ułatwia wyświetlanie innych hostów kontenerów platformy Docker i Windows w jednej lokalizacji i zarządzanie nimi. Przykład:

  * Wyświetlanie szczegółowych informacji inspekcji, które pokazują polecenia używane z kontenerami. 
  * Rozwiązywanie problemów z kontenerami, wyświetlając i wyszukując scentralizowane dzienniki bez konieczności zdalnego wyświetlania hostów platformy Docker lub Windows.  
  * Znajdź kontenery, które mogą być hałaśliwe i zużywają nadmiar zasobów na hoście.
  * Wyświetlanie scentralizowanych informacji o procesorze, pamięci, pamięci masowej i użyciu sieci i wydajności kontenerów.  

  Rozwiązanie obsługuje orchestrators kontenerów, w tym Docker Swarm, DC/OS, niezarządzane Kubernetes, sieci szkieletowej usług i Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorowanie aktywności zasobów kontenera 

Monitoruj aktywność zasobów, takich jak pliki, sieć i inne zasoby, do których dostęp mają kontenery. Monitorowanie aktywności i zużycia zasobów jest przydatne zarówno do monitorowania wydajności, jak i jako środek bezpieczeństwa. 

[Usługa Azure Monitor](../azure-monitor/overview.md) umożliwia monitorowanie podstawowych usług platformy Azure, umożliwiając zbieranie metryk, dzienników aktywności i dzienników diagnostycznych. Na przykład dziennik aktywności informuje o utworzeniu lub zmodyfikowaniu nowych zasobów. 

  Są dostępne metryki, które dostarczają statystyki wydajności dla różnych zasobów, a nawet systemu operacyjnego w maszynie wirtualnej. Można wyświetlić te dane za pomocą jednego z eksploratorów w witrynie Azure Portal i utworzyć alerty na podstawie tych metryk. Usługa Azure Monitor dostarcza najszybszy potok metryk (w zakresie od 5 minut do 1 minuty), dlatego należy jej używać do krytycznych czasowo alertów i powiadomień. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Rejestrowanie dostępu wszystkich użytkowników administracyjnych kontenerów do inspekcji 

Obsługa dokładnej ścieżki inspekcji dostępu administracyjnego do ekosystemu kontenerów, w tym klastra kubernetes, rejestru kontenerów i obrazów kontenerów. Te dzienniki mogą być niezbędne do celów inspekcji i będą przydatne jako dowody kryminalistyczne po każdym incydencie związanym z bezpieczeństwem. Rozwiązania platformy Azure obejmują:

* [Integracja usługi Azure Kubernetes z usługą Azure Security Center](../security-center/azure-kubernetes-service-integration.md) w celu monitorowania konfiguracji zabezpieczeń środowiska klastra i generowania zaleceń dotyczących zabezpieczeń
* [Rozwiązanie do monitorowania kontenerów platformy Azure](../azure-monitor/insights/containers.md)
* Dzienniki zasobów dla [wystąpień kontenerów platformy Azure](container-instances-log-analytics.md) i [rejestru kontenerów platformy Azure](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [używaniu usługi Azure Security Center](../security-center/container-security.md) do wykrywania zagrożeń w czasie rzeczywistym w środowiskach konteneryzowanych.

* Dowiedz się więcej o zarządzaniu lukami w zabezpieczeniach kontenerów dzięki rozwiązaniom [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) i [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)