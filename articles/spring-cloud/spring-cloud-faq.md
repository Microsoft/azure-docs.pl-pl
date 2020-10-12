---
title: Często zadawane pytania dotyczące chmury wiosennej platformy Azure | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące chmury wiosennej platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 618e9586e9824e6cea592ff78a1fbd3b991abd83
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945400"
---
# <a name="azure-spring-cloud-faq"></a>Azure Wiosenna — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące chmury wiosennej platformy Azure.

## <a name="general"></a>Ogólne

### <a name="why-azure-spring-cloud"></a>Dlaczego chmura sprężynowa platformy Azure?

Chmura Wiosnowa platformy Azure udostępnia platformę jako usługę (PaaS) dla deweloperów chmury z chmurą. Chmura sprężynowa platformy Azure zarządza infrastrukturą aplikacji, dzięki czemu możesz skupić się na kodzie aplikacji i logice biznesowej. Podstawowe funkcje wbudowane w chmurę Azure wiosną obejmują: Eureka, config Server, serwer rejestru usług, przestawianie usług kompilacji, tworzenie niebiesko-zielone wdrożenie i inne. Ta usługa umożliwia również deweloperom powiązanie aplikacji z innymi usługami platformy Azure, takimi jak Azure Cosmos DB, Azure Database for MySQL i pamięć podręczna Azure dla Redis.

Chmura sprężynowa platformy Azure rozszerza środowisko diagnostyki aplikacji dla deweloperów i operatorów przez integrację Azure Monitor, Application Insights i Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Jak bezpieczny jest chmura Wiosenna platformy Azure?

Bezpieczeństwo i ochrona prywatności należą do najważniejszych priorytetów dla klientów korzystających z platformy Azure i usługi Azure wiosny Cloud. System Azure pomaga upewnić się, że tylko klienci mają dostęp do danych, dzienników lub konfiguracji aplikacji, bezpiecznie szyfrując wszystkie te dane. 

* Wystąpienia usługi w chmurze Azure wiosny są od siebie odizolowane.
* Chmura sprężynowa platformy Azure zapewnia pełną obsługę protokołów TLS/SSL i zarządzania certyfikatami.
* Krytyczne poprawki zabezpieczeń dla środowisk uruchomieniowych w chmurze OpenJDK i wiosny są stosowane do chmury wiosennej platformy Azure najszybciej, jak to możliwe.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>W jakich regionach jest dostępna chmura Wiosenna platformy Azure?

Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA 2, Europa Zachodnia, Europa Północna, Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia i Australia Wschodnia.


### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jakie są znane ograniczenia chmury wiosennej platformy Azure?

Chmura Wiosnowa platformy Azure ma następujące znane ograniczenia:
    
* `spring.application.name` zostanie przesłonięty przez nazwę aplikacji, która jest używana do tworzenia każdej aplikacji.
* `server.port` wartość domyślna to port 1025. Jeśli jakakolwiek inna wartość zostanie zastosowana, zostanie zastąpiona. Należy również przestrzegać tego ustawienia i nie określać portu serwera w kodzie.
* Szablony Azure Portal i Azure Resource Manager nie obsługują przekazywania pakietów aplikacji. Pakiety aplikacji można przekazywać tylko przez wdrożenie aplikacji za pośrednictwem interfejsu wiersza polecenia platformy Azure.

### <a name="what-pricing-tiers-are-available"></a>Jakie warstwy cenowe są dostępne? 
Z którego z nich korzystać i jakie są limity w ramach każdej warstwy?
* Chmura Wiosnowa platformy Azure oferuje dwie warstwy cenowe: podstawowa i standardowa. Warstwa Podstawowa jest przeznaczona do tworzenia i testowania oraz do wypróbowania chmury wiosennej platformy Azure. Warstwa standardowa jest zoptymalizowana pod kątem uruchamiania ruchu produkcyjnego ogólnego przeznaczenia. Zobacz [szczegóły cennika usługi Azure wiosny Cloud](https://azure.microsoft.com/pricing/details/spring-cloud/) dla limitów i porównania poziomu funkcji.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak mogę przekazać Opinie i zgłosić problemy?

Jeśli wystąpią problemy z chmurą Azure wiosną, Utwórz [żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Aby przesłać żądanie funkcji lub przekazać opinię, przejdź do obszaru [opinii na platformie Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Opracowywanie zawartości

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jestem deweloperem chmury w chmurze, ale nowym na platformie Azure. Co to jest najszybszy sposób, aby dowiedzieć się, jak utworzyć aplikację w chmurze ze sprężyną Azure?

Aby szybko rozpocząć pracę z chmurą wiosenną platformy Azure, postępuj zgodnie z instrukcjami w [przewodniku szybki start: uruchamianie aplikacji w chmurze ze sprężyną Azure przy użyciu Azure Portal](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jakie środowisko uruchomieniowe języka Java obsługuje chmurę Azure?

Chmura sprężynowa platformy Azure obsługuje środowisko Java 8 i 11. Zobacz [środowisko uruchomieniowe Java i wersje systemu operacyjnego](#java-runtime-and-os-versions)
::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Gdzie mogę wyświetlić sprężynowe dzienniki i metryki aplikacji w chmurze?

Znajdź metryki na karcie Przegląd aplikacji i karcie [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Chmura sprężynowa platformy Azure obsługuje eksportowanie dzienników aplikacji i metryk w chmurze z usługą Azure Storage, centrum EventHub i [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Nazwa tabeli w Log Analytics to *AppPlatformLogsforSpring*. Aby dowiedzieć się, jak włączyć tę funkcję, zobacz [usługi diagnostyczne](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Czy chmura Wiosenna platformy Azure obsługuje śledzenie rozproszone?

Tak. Aby uzyskać więcej informacji, zobacz [Samouczek: używanie rozproszonego śledzenia w chmurze Azure wiosennej](spring-cloud-tutorial-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Jakie typy zasobów obsługuje usługa powiązania usługi?

Obecnie są obsługiwane trzy usługi:
* Azure Cosmos DB
* Azure Database for MySQL
* Pamięć podręczna platformy Azure dla Redis.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Czy mogę wyświetlać, dodawać i przenosić woluminy trwałe z wewnątrz aplikacji?

Tak.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Czy po usunięciu/przeniesieniu wystąpienia usługi w chmurze Azure wiosny czy zasoby rozszerzeń będą również usuwane lub przenoszone?

Jest to zależne od logiki dostawców zasobów, którzy są właścicielami zasobów rozszerzenia. Zasoby rozszerzenia `Microsoft.AppPlatform` wystąpienia nie należą do tej samej przestrzeni nazw, więc zachowanie zależy od dostawcy zasobów. Na przykład operacja usuwania/przenoszenia nie zostanie przeniesiona do zasobów **ustawień diagnostycznych** . Jeśli nowe wystąpienie chmury Azure wiosny jest obsługiwane przy użyciu tego samego identyfikatora zasobu, który został usunięty, lub jeśli poprzednie wystąpienie chmury sieci platformy Azure zostanie przeniesione z powrotem, poprzednie zasoby **ustawień diagnostycznych** kontynuują jego rozszerzanie.

Ustawienia diagnostyczne chmury wiosennej można usunąć za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Środowisko uruchomieniowe Java i wersje systemu operacyjnego

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Które wersje środowiska uruchomieniowego Java są obsługiwane w chmurze Azure wiosennej?

Chmura sprężynowa platformy Azure obsługuje wersje języka Java LTS z najnowszymi kompilacjami, obecnie 2020 czerwca, Java 8 kompilacja 252 i Java 11 Build 7. Zobacz [Instalowanie JDK dla platformy Azure i Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Kto utworzył te środowiska uruchomieniowe Java?

Systemy Azul. Kompilacje zestawu JDK Azul Zulu for Azure-Enterprise Edition to bezpłatne, wieloplatformowe i gotowe do zastosowań produkcyjnych dystrybucje zestawu OpenJDK dla platformy Azure i usługi Azure Stack, wspierane przez firmy Microsoft i Azul Systems. Zawierają one wszystkie składniki do kompilowania i uruchamiania aplikacji Java SE.

### <a name="how-often-will-java-runtimes-get-updated"></a>Jak często są aktualizowane środowiska uruchomieniowe języka Java?

Wersje LTS i MTS JDK zawierają kwartalne aktualizacje zabezpieczeń, poprawki błędów i krytyczne aktualizacje poza pasmem oraz poprawki, które są wymagane. Ta obsługa obejmuje wersje załączane do języka Java 7 i 8 aktualizacji zabezpieczeń oraz poprawki błędów raportowane w nowszych wersjach środowiska Java, takich jak Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Jak długo będą obsługiwane wersje LTS języka Java 8 i Java 11?

Zobacz [obsługę długoterminową języka Java dla platformy Azure i Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Środowisko Java 8 LTS będzie obsługiwane do grudnia 2030.
* Środowisko Java 11 LTS będzie obsługiwane do września 2027.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Jak mogę pobrać obsługiwane środowisko uruchomieniowe języka Java na potrzeby lokalnego tworzenia oprogramowania?

Zobacz [Install the JDK for Azure i Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Jakie są zasady wycofywania dla starszych środowisk uruchomieniowych języka Java?

Publiczne powiadomienie zostanie wysłane przez 12 miesięcy, zanim zostanie wycofana jakakolwiek stara wersja środowiska uruchomieniowego. Przeprowadzenie migracji do nowszej wersji będzie możliwe przez 12 miesięcy.

* Administratorzy subskrypcji otrzymają wiadomość e-mail z powiadomieniem, gdy zostanie wycofana wersja języka Java.
* Informacje o wycofaniu zostaną opublikowane w dokumentacji.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Jak mogę uzyskać pomoc techniczną dotyczącą problemów na poziomie środowiska uruchomieniowego Java?

Możesz otworzyć bilet pomocy technicznej w ramach pomocy technicznej platformy Azure.  Zobacz [, jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Co to jest system operacyjny, aby uruchomić moje aplikacje?

Używana jest Najnowsza wersja programu Ubuntu LTS, a obecnie [Ubuntu 20,04 LTS (system fossa)](https://releases.ubuntu.com/focal/) to domyślny system operacyjny.

### <a name="how-often-are-os-security-patches-applied"></a>Jak często są stosowane poprawki zabezpieczeń systemu operacyjnego?

Poprawki zabezpieczeń stosowane w chmurze Azure wiosennej są przeprowadzone do produkcji miesięcznie.
Krytyczne poprawki zabezpieczeń (ocena w >= 9) dotyczące usługi Azure wiosny są dostępne najszybciej, jak to możliwe.
::: zone-end

## <a name="deployment"></a>wdrażania

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Czy usługa Azure Wiosenna Cloud obsługuje wdrożenie Blue-Green?
Tak. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska przejściowego](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Czy mogę uzyskać dostęp do programu Kubernetes w celu manipulowania kontenerami aplikacji?

Nie.  Chmura ze sprężyną platformy Azure jest abstrakcyjna dla deweloperów z podstawowej architektury, co pozwala skoncentrować się na kodzie aplikacji i logice biznesowej.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Czy chmura sprężynowa platformy Azure obsługuje tworzenie kontenerów ze źródła?

Tak. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji w chmurze wiosny z poziomu kodu źródłowego](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Czy chmura sprężynowa platformy Azure obsługuje skalowanie automatyczne w wystąpieniach aplikacji?

Nie.

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jakie są najlepsze rozwiązania dotyczące migrowania istniejących mikrousług w chmurze do usługi Azure wiosny?

Podczas migrowania istniejących mikrousług w chmurze wiosny do chmury wiosennej platformy Azure dobrym pomysłem jest przestrzeganie następujących najlepszych rozwiązań:
* Należy rozwiązać wszystkie zależności aplikacji.
* Przygotuj wpisy konfiguracji, zmienne środowiskowe i JVM parametry, aby można było je porównać z wdrożeniem w chmurze Azure wiosennej.
* Jeśli chcesz użyć powiązania usługi, przejdź przez usługi platformy Azure i upewnij się, że ustawiono odpowiednie uprawnienia dostępu.
* Zalecamy usunięcie lub wyłączenie usług osadzonych, które mogą powodować konflikt z usługami zarządzanymi przez chmurę z usługą Azure wiosną, taką jak nasza usługa odnajdowania usług, serwer konfiguracji i tak dalej.
* Zalecamy używanie oficjalnych, stabilnych bibliotek sprężyny Pivot. Wersje nieoficjalne, beta lub rozwidlenia z nieoficjalnymi wersjami bibliotek sprężynowych nie są objęte umową dotyczącą poziomu usług.

Po migracji Monitoruj metryki procesora/pamięci RAM i ruch sieciowy, aby upewnić się, że wystąpienia aplikacji są odpowiednio skalowane.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>Wersje .NET Core

### <a name="which-net-core-versions-are-supported"></a>Które wersje .NET Core są obsługiwane?

.NET Core 3,1 i nowsze wersje.

### <a name="how-long-will-net-core-31-be-supported"></a>Jak długo będzie obsługiwana platforma .NET Core 3,1?

Do 3 grudnia 2022. Zobacz [zasady pomocy technicznej platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Jaki wpływ na rejestr usługi jest sporadycznie niedostępny?

W niektórych rzadko występujących scenariuszach mogą pojawić się pewne błędy, takie jak 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
z dzienników aplikacji. Ten problem wprowadzono przez strukturę sprężynową z bardzo niską szybkością spowodowaną niestabilnością sieci lub innymi problemami z siecią. 

Nie powinno mieć żadnych wpływów na środowisko użytkownika, Eureka klient ma zarówno puls, jak i zasady ponawiania prób, aby zadbać o to. Można to rozważyć jako jeden błąd przejściowy i pominąć go bezpiecznie.

Poprawimy tę część i unikamy tego błędu z aplikacji użytkowników w krótkim przyszłości.


## <a name="next-steps"></a>Następne kroki

Jeśli masz więcej pytań, zobacz [Przewodnik rozwiązywania problemów z chmurą Azure wiosną](spring-cloud-troubleshoot.md).
