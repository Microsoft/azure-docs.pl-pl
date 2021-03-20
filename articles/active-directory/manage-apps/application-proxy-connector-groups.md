---
title: Publikowanie aplikacji w oddzielnych sieciach za pośrednictwem grup łączników — Azure AD
description: Opisuje sposób tworzenia grup łączników w usłudze Azure serwer proxy aplikacji usługi Azure AD i zarządzania nimi.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca42137192fefbc669fca775b0deff2f5226c5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253836"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników

Klienci wykorzystują serwer proxy aplikacji usługi Azure AD w celu uzyskania większej liczby scenariuszy i aplikacji. Dzięki temu serwer proxy aplikacji jest jeszcze bardziej elastyczny przez włączenie większej liczby topologii. Można utworzyć grupy łączników serwera proxy aplikacji, aby umożliwić przypisywanie określonych łączników do określonych aplikacji. Ta funkcja zapewnia większą kontrolę i sposoby optymalizacji wdrożenia serwera proxy aplikacji.

Każdy łącznik serwera proxy aplikacji jest przypisany do grupy łączników. Wszystkie łączniki należące do tej samej grupy łączników działają jako osobne jednostki w celu zapewnienia wysokiej dostępności i równoważenia obciążenia. Wszystkie łączniki należą do grupy łączników. Jeśli nie utworzysz grup, wszystkie łączniki znajdują się w grupie domyślnej. Administrator może tworzyć nowe grupy i przypisywać do nich łączniki w Azure Portal.

Wszystkie aplikacje są przypisywane do grupy łączników. Jeśli nie utworzysz grup, wszystkie aplikacje są przypisywane do grupy domyślnej. Ale jeśli Twoje łączniki są zorganizowane w grupy, można ustawić, aby każda aplikacja działała z określoną grupą łączników. W takim przypadku tylko łączniki w tej grupie obsługują aplikację na żądanie. Ta funkcja jest przydatna, jeśli aplikacje są hostowane w różnych lokalizacjach. Można utworzyć grupy łączników w oparciu o lokalizację, dzięki czemu aplikacje są zawsze obsługiwane przez łączniki, które znajdują się blisko nich.

> [!TIP]
> Jeśli masz duże wdrożenie serwera proxy aplikacji, nie przypisuj żadnych aplikacji do domyślnej grupy łączników. Dzięki temu nowe łączniki nie odbierają ruchu na żywo, dopóki nie zostaną przypisane do aktywnej grupy łączników. Ta konfiguracja umożliwia również umieszczenie łączników w trybie bezczynności przez przeniesienie ich z powrotem do grupy domyślnej, dzięki czemu można przeprowadzić konserwację bez wpływu na użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zgrupować łączniki, należy upewnić się, że [zainstalowano wiele łączników](application-proxy-add-on-premises-application.md). Po zainstalowaniu nowego łącznika automatycznie przyłączana jest **Domyślna** Grupa łączników.

## <a name="create-connector-groups"></a>Tworzenie grup łączników

Wykonaj te kroki, aby utworzyć dowolną liczbę grup łączników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory**  >  **aplikacje przedsiębiorstwa**  >  **serwer proxy aplikacji**.
1. Wybierz pozycję **Nowa grupa łączników**. Zostanie wyświetlony blok Nowa grupa łączników.

   ![Wyświetla ekran, aby wybrać nową grupę łączników](./media/application-proxy-connector-groups/new-group.png)

1. Nadaj nazwę nowej grupie łączników, a następnie użyj menu rozwijanego, aby wybrać łączniki należące do tej grupy.
1. Wybierz pozycję **Zapisz**.

## <a name="assign-applications-to-your-connector-groups"></a>Przypisywanie aplikacji do grup łączników

Wykonaj te kroki dla każdej aplikacji, która została opublikowana z serwerem proxy aplikacji. Możesz przypisać aplikację do grupy łączników podczas pierwszej publikacji lub użyć tych kroków, aby zmienić przypisanie w dowolnym momencie.

1. Na pulpicie nawigacyjnym Zarządzanie katalogiem wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** > aplikację, którą chcesz przypisać do grupy łączników > **serwerze proxy aplikacji**.
1. Użyj menu rozwijanego **Grupa łączników** , aby wybrać grupę, która ma być używana przez aplikację.
1. Wybierz pozycję **Zapisz** , aby zastosować zmianę.

## <a name="use-cases-for-connector-groups"></a>Przypadki użycia dla grup łączników

Grupy łączników są przydatne dla różnych scenariuszy, w tym:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Lokacje z wieloma połączonymi centrami danych

Wiele organizacji ma kilka połączonych centrów danych. W takim przypadku należy zachować jak najwięcej ruchu w centrum danych, ponieważ linki między centrami danych są kosztowne i wolno. Łączniki w poszczególnych centrach danych można wdrożyć, aby obsługiwały tylko aplikacje znajdujące się w centrum danych. Takie podejście minimalizuje linki między centrami danych i zapewnia całkowicie przejrzyste środowisko dla użytkowników.

### <a name="applications-installed-on-isolated-networks"></a>Aplikacje zainstalowane w sieciach izolowanych

Aplikacje mogą być hostowane w sieciach, które nie są częścią głównej sieci firmowej. Za pomocą grup łączników można instalować dedykowane łączniki w sieciach izolowanych, aby również izolować aplikacje do sieci. Jest to zwykle wykonywane, gdy dostawca innej firmy utrzymuje określoną aplikację dla organizacji.

Grupy łączników umożliwiają zainstalowanie dedykowanych łączników dla tych sieci, które publikują tylko określone aplikacje, ułatwiając i bardziej bezpieczne zarządzanie aplikacjami na potrzeby innych dostawców.

### <a name="applications-installed-on-iaas"></a>Aplikacje zainstalowane w systemie IaaS

W przypadku aplikacji zainstalowanych w systemie IaaS na potrzeby dostępu do chmury grupy łączników zapewniają wspólną usługę do zabezpieczenia dostępu do wszystkich aplikacji. Grupy łączników nie tworzą dodatkowej zależności w sieci firmowej ani nie pofragmentowanie środowiska aplikacji. Łączniki mogą być instalowane w każdym centrum danych w chmurze i obsługują tylko aplikacje znajdujące się w tej sieci. Aby zapewnić wysoką dostępność, można zainstalować kilka łączników.

Podejmij przykładową organizację, która ma kilka maszyn wirtualnych połączonych z własną IaaS hostowanej sieci wirtualnej. Aby umożliwić pracownikom korzystanie z tych aplikacji, te sieci prywatne są połączone z siecią firmową przy użyciu sieci VPN typu lokacja-lokacja. Zapewnia to dobre środowisko dla pracowników, którzy znajdują się lokalnie. Jednak może to nie być idealne dla pracowników zdalnych, ponieważ wymaga dodatkowej infrastruktury lokalnej do kierowania dostępu, jak widać na poniższym diagramie:

![Diagram przedstawiający sieć usługi Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Za pomocą grup łączników usługi Azure serwer proxy aplikacji usługi Azure AD można włączyć wspólną usługę do zabezpieczenia dostępu do wszystkich aplikacji bez konieczności tworzenia dodatkowej zależności w sieci firmowej:

![Usługa Azure AD IaaS wielu dostawców chmury](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Wiele lasów — różne grupy łączników dla każdego lasu

Większość klientów, którzy wdrożyły serwer proxy aplikacji, używają funkcji logowania jednokrotnego (SSO), wykonując ograniczone delegowanie protokołu Kerberos (KCD). Aby to osiągnąć, komputery łącznika muszą być przyłączone do domeny, która może delegować użytkowników do aplikacji. KCD obsługuje możliwości między lasami. Jednak w przypadku firm, które mają różne środowiska wielu lasów bez relacji zaufania między nimi, nie można używać jednego łącznika dla wszystkich lasów. 

W takim przypadku poszczególne łączniki można wdrożyć na las i ustawić, aby obsługiwały aplikacje, które zostały opublikowane w taki sposób, aby obsługiwały tylko użytkowników tego konkretnego lasu. Każda grupa łączników reprezentuje inny Las. Chociaż dzierżawca i większość tego środowiska są ujednolicone dla wszystkich lasów, użytkownicy mogą być przypisani do aplikacji lasów przy użyciu grup usługi Azure AD.

### <a name="disaster-recovery-sites"></a>Lokacje odzyskiwania po awarii

Istnieją dwa różne podejścia, które można wykonać za pomocą lokacji odzyskiwania po awarii (DR), w zależności od implementacji witryn:

* Jeśli lokacja odzyskiwania po awarii jest wbudowana w tryb aktywny-aktywny, gdzie jest dokładnie taka sama jak lokacja główna i ma te same ustawienia sieci i usługi AD, można utworzyć łączniki w lokacji odzyskiwania po awarii w tej samej grupie łączników, co w lokacji głównej. Dzięki temu usługa Azure AD może wykrywać przełączenia w tryb failover.
* Jeśli lokacja odzyskiwania po awarii jest oddzielona od lokacji głównej, można utworzyć inną grupę łączników w witrynie DR, a 1) mieć aplikacje do tworzenia kopii zapasowych lub 2) ręcznie przekierowanie istniejącej aplikacji do grupy łączników odzyskiwania po awarii zgodnie z potrzebami.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsługiwanie wielu firm z poziomu pojedynczej dzierżawy

Istnieje wiele różnych sposobów implementacji modelu, w którym jeden dostawca usług wdraża i utrzymuje usługi związane z usługą Azure AD dla wielu firm. Grupy łączników ułatwiają administratorom dzielenie łączników i aplikacji na różne grupy. Jednym ze sposobów, który jest odpowiedni dla małych firm, jest posiadanie pojedynczej dzierżawy usługi Azure AD, gdy różne firmy mają własną nazwę domeny i sieci. Jest to również prawdziwe dla M&scenariusze i sytuacje, w których jeden dział IT obsługuje kilka firm w celu zapewnienia prawnego lub biznesowego.

## <a name="sample-configurations"></a>Konfiguracje przykładowe

Przykłady, które można zaimplementować, obejmują następujące grupy łączników.

### <a name="default-configuration--no-use-for-connector-groups"></a>Konfiguracja domyślna — Brak użycia dla grup łączników

Jeśli nie używasz grup łączników, Twoja konfiguracja będzie wyglądać następująco:

![Przykładowa usługa Azure AD nie ma grup łączników](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Ta konfiguracja jest wystarczająca dla małych wdrożeń i testów. Również będzie działała prawidłowo, jeśli organizacja ma topologię sieci płaskiej.

### <a name="default-configuration-and-an-isolated-network"></a>Konfiguracja domyślna i sieć izolowana

Ta konfiguracja jest ewolucją wartości domyślnej, w której istnieje określona Aplikacja działająca w izolowanej sieci, takiej jak IaaS Virtual Network:

![Przykładowa usługa Azure AD nie ma grup łączników i sieci izolowanej](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Zalecana konfiguracja — kilka określonych grup i grupy domyślnej dla bezczynnego

Zalecana konfiguracja dla dużych i złożonych organizacji ma mieć domyślną grupę łączników jako grupę, która nie obsługuje żadnych aplikacji i jest używana w przypadku bezczynnych lub nowo zainstalowanych łączników. Wszystkie aplikacje są obsługiwane przy użyciu dostosowanych grup łączników. Dzięki temu można wykonać wszystkie złożone scenariusze opisane powyżej.

W poniższym przykładzie firma ma dwa centra danych, a i B, z dwoma łącznikami, które obsługują poszczególne lokacje. Każda lokacja ma różne aplikacje, które działają na tym komputerze.

![Przykład firmy z 2 centrami danych i 2 łącznikami](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
* [Włączanie logowania jednokrotnego](what-is-single-sign-on.md)
