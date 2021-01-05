---
title: Service Fabric tworzenia projektu w następnym kroku
description: Dowiedz się więcej o projekcie aplikacji, który właśnie został utworzony w programie Visual Studio.  Dowiedz się, jak tworzyć usługi przy użyciu samouczków i dowiedzieć się więcej na temat opracowywania usług dla Service Fabric.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760439"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Twoja aplikacja Service Fabric i następne kroki
Aplikacja Service Fabric platformy Azure została utworzona. Ten artykuł zawiera wiele zasobów, kilka dodatkowych informacji, które mogą być interesujące, oraz potencjalne [następne kroki](#next-steps).

Nowi użytkownicy mogą znaleźć [samouczki, wskazówki i przykłady](#get-started-with-tutorials-walk-throughs-and-samples) przydatne. Przydatne może być również badanie [struktury utworzonego projektu aplikacji](#the-application-project). Uwzględniono również opisy [modeli programowania](#learn-more-about-the-programming-models)Service Fabric, komunikacji z [usługą](#learn-about-service-communication), [zabezpieczeń aplikacji](#learn-about-configuring-application-security)i [cyklu życia aplikacji](#learn-about-the-application-lifecycle).

Więcej doświadczonych użytkowników warto znaleźć w sekcji Service Fabric [najlepszych](#learn-about-best-practices) rozwiązań, aby dowiedzieć się, jak korzystać z aplikacji platformy i struktury z maksymalną skutecznością.

Dla osób z pytaniami lub opiniami lub którzy chcą zgłosić problem, zapoznaj się z [odpowiednią sekcją](#have-questions-or-feedback--need-to-report-an-issue).

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Rozpocznij pracę z samouczkami, instruktażami i przykładami
Możemy zaczynać?  

Pracuj w samouczku dotyczącym aplikacji .NET. Dowiedz się, w jaki sposób [utworzyć aplikację](service-fabric-tutorial-create-dotnet-app.md) za pomocą frontonu ASP.NET Core i zapasowego zaplecza, [wdrożyć aplikację](service-fabric-tutorial-deploy-app-to-party-cluster.md) w klastrze, [skonfigurować Ci/CD/](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)na [bieżąco i skonfigurować monitorowanie i diagnostykę](service-fabric-tutorial-monitoring-aspnet.md).

Można też wypróbować jedno z poniższych przewodników i utworzyć swoje pierwsze...
- [Usługa Reliable Services C# w systemie Windows](service-fabric-reliable-services-quick-start.md) 
- [Usługa Reliable Actors C# w systemie Windows](service-fabric-reliable-actors-get-started.md) 
- [Usługa wykonywalna gościa w systemie Windows](quickstart-guest-app.md) 
- [Aplikacja kontenera dla systemu Windows](service-fabric-get-started-containers.md) 

Warto również zainteresować się próbą wykonania naszych [przykładowych aplikacji](/samples/browse/?products=azure).

## <a name="the-application-project"></a>Projekt aplikacji
Każda nowa aplikacja zawiera projekt aplikacji. Może istnieć jeden lub dwa dodatkowe projekty, w zależności od wybranego typu usługi.

Projekt aplikacji składa się z:

* Zestaw odwołań do usług, które składają się na aplikację.
* Trzy Profile publikowania (lokalne, 5-węzłowe, lokalne i w chmurze), których można użyć do obsługi preferencji pracy z różnymi środowiskami — takich jak preferencje związane z punktem końcowym klastra oraz czy domyślnie mają być wykonywane wdrożenia uaktualniania.
* Trzy pliki parametrów aplikacji (takie same jak powyżej), których można użyć do obsługi konfiguracji aplikacji specyficznych dla środowiska, takich jak liczba partycji do utworzenia dla usługi. Dowiedz się, jak [skonfigurować aplikację dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).
* Skrypt wdrażania, którego można użyć do wdrożenia aplikacji z wiersza polecenia lub w ramach zautomatyzowanego potoku ciągłej integracji i wdrażania. Dowiedz się więcej o [wdrażaniu aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikacji, który opisuje aplikację. Manifest można znaleźć w folderze ApplicationPackageRoot. Dowiedz się więcej na temat [manifestów aplikacji i usług](service-fabric-application-model.md).

## <a name="learn-more-about-the-programming-models"></a>Dowiedz się więcej o modelach programowania
Service Fabric oferuje wiele sposobów zapisywania usług i zarządzania nimi.  Poniżej przedstawiono omówienie i pojęcia dotyczące [bezstanowych i](service-fabric-reliable-services-introduction.md)stanowych Reliable Services, [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontenerów](service-fabric-containers-overview.md), [plików wykonywalnych gościa](service-fabric-guest-executables-introduction.md)i [bezstanowych i stanowych ASP.NET Core usług](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Informacje o komunikacji z usługą
Service Fabric aplikacja składa się z różnych usług, gdzie każda usługa wykonuje wyspecjalizowane zadanie. Te usługi mogą komunikować się ze sobą i mogą znajdować się aplikacje klienckie poza klastrem, który nawiązuje połączenie z usługami i komunikują się z nimi. Dowiedz się [, jak skonfigurować komunikację z usługami i między](service-fabric-connect-and-communicate-with-services.md) nimi w Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Dowiedz się więcej o konfigurowaniu zabezpieczeń aplikacji
Można zabezpieczyć aplikacje działające w klastrze pod różnymi kontami użytkowników. Service Fabric również zabezpiecza zasoby, które są używane przez aplikacje w czasie wdrażania w ramach kont użytkowników — na przykład pliki, katalogi i certyfikaty. Dzięki temu uruchomione aplikacje, nawet w udostępnianym środowisku hostowanym, są bezpieczniejsze od siebie nawzajem.  Dowiedz się, jak [skonfigurować zasady zabezpieczeń dla aplikacji](service-fabric-application-runas-security.md).

Aplikacja może zawierać informacje poufne, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. Dowiedz się, jak zarządzać wpisami [tajnymi w aplikacji](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Dowiedz się więcej o cyklu życia aplikacji
Podobnie jak w przypadku innych platform, aplikacja Service Fabric zwykle przechodzi przez następujące fazy: projektowanie, programowanie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. [Ten artykuł](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i ich użycia przez różne role w fazie cyklu życia Service Fabric aplikacji.

## <a name="learn-about-best-practices"></a>Poznaj najlepsze rozwiązania
Service Fabric zawiera kilka artykułów opisujących [najlepsze rozwiązania](./service-fabric-best-practices-overview.md). Skorzystaj z tych informacji, aby upewnić się, że klaster i aplikacja działają prawidłowo.
Omówione tematy obejmują:
* [Bezpieczeństwo](./service-fabric-best-practices-security.md)
* [Sieć](./service-fabric-best-practices-networking.md)
* [Planowanie i skalowanie obliczeń](./service-fabric-best-practices-capacity-scaling.md)
* [Infrastruktura jako kod](./service-fabric-best-practices-infrastructure-as-code.md)
* [Monitorowanie i diagnostyka](./service-fabric-best-practices-monitoring.md)
* [Projekt aplikacji](./service-fabric-best-practices-applications.md)

Uwzględniono również [listę kontrolną gotowości do produkcji](./service-fabric-production-readiness-checklist.md) , która integruje wszystkie najlepsze rozwiązania w postaci łatwego w użyciu.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Masz pytania lub uwagi?  Potrzebujesz zgłosić problem?
Zapoznaj się z [typowymi pytaniami](service-fabric-common-questions.md) i Znajdź odpowiedzi na temat tego, co Service Fabric może zrobić, i jak powinno być używane.

[Przewodniki dotyczące rozwiązywania problemów](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) ułatwiają diagnozowanie i rozwiązywanie typowych problemów z klastrami Service Fabric.

[Opcje pomocy technicznej](service-fabric-support.md) zawierają listę forów w witrynie STACKOVERFLOW i MSDN w celu zadawania pytań, a także opcji zgłaszania problemów, uzyskiwania pomocy technicznej i przesyłania opinii o produkcie.


## <a name="next-steps"></a>Następne kroki
- [Utwórz klaster systemu Windows na platformie Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Wizualizuj klaster, w tym wdrożone aplikacje i układ fizyczny, przy użyciu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Wersja i uaktualnienie usług](service-fabric-application-upgrade-tutorial.md)