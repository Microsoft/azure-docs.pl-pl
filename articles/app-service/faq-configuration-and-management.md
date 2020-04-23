---
title: Często zadawane pytania dotyczące konfiguracji
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem usługi Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 810219bca5c75e3ee7e65c6b7302531bca89788d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869960"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące konfiguracji i zarządzania aplikacjami sieci Web na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem [w przypadku funkcji aplikacji sieci Web usługi Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Czy istnieją ograniczenia, o których powinienem wiedzieć, jeśli chcę przenieść zasoby usługi App Service?

Jeśli planujesz przenieść zasoby usługi App Service do nowej grupy zasobów lub subskrypcji, istnieje kilka ograniczeń, o których należy pamiętać. Aby uzyskać więcej informacji, zobacz [Ograniczenia usługi app service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak używać niestandardowej nazwy domeny dla mojej aplikacji internetowej?

Aby uzyskać odpowiedzi na często zadawane pytania dotyczące używania niestandardowej nazwy domeny w aplikacji sieci Web platformy Azure, zobacz nasz siedmiominutowy film wideo [Dodawanie niestandardowej nazwy domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Film zawiera przewodnik po dodaniu niestandardowej nazwy domeny. Opisano w nim sposób używania własnego adresu URL zamiast adresu URL *.azurewebsites.net z aplikacją sieci Web usługi App Service. Można również zobaczyć szczegółowy przewodnik, [jak mapować niestandardową nazwę domeny](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak kupić nową domenę niestandardową dla mojej aplikacji internetowej?

Aby dowiedzieć się, jak kupić i skonfigurować domenę niestandardową dla aplikacji sieci Web usługi App Service, zobacz [Kupowanie i konfigurowanie niestandardowej nazwy domeny w usłudze App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Jak przekazać i skonfigurować istniejący certyfikat TLS/SSL dla mojej aplikacji internetowej?

Aby dowiedzieć się, jak przekazać i skonfigurować istniejący niestandardowy certyfikat TLS/SSL, zobacz [Dodawanie certyfikatu TLS/SSL do aplikacji Usługi App Service](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Jak kupić i skonfigurować nowy certyfikat TLS/SSL na platformie Azure dla mojej aplikacji sieci web?

Aby dowiedzieć się, jak kupić i skonfigurować certyfikat TLS/SSL dla aplikacji sieci Web usługi App Service, zobacz [Dodawanie certyfikatu TLS/SSL do aplikacji usługi App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak przenieść zasoby usługi Application Insights?

Obecnie usługa Azure Application Insights nie obsługuje operacji przenoszenia. Jeśli oryginalna grupa zasobów zawiera zasób usługi Application Insights, nie można przenieść tego zasobu. Jeśli podczas próby przeniesienia aplikacji usługi App Service zostanie uwzględnienie zasobu usługi Application Insights, cała operacja przenoszenia zakończy się niepowodzeniem. Jednak usługa Application Insights i plan usługi app service nie muszą znajdować się w tej samej grupie zasobów co aplikacja, aby aplikacja działała poprawnie.

Aby uzyskać więcej informacji, zobacz [Ograniczenia usługi app service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Gdzie mogę znaleźć listę kontrolną wskazówek i dowiedzieć się więcej o operacjach przenoszenia zasobów?

[Ograniczenia usługi App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) pokazują, jak przenieść zasoby do nowej subskrypcji lub do nowej grupy zasobów w tej samej subskrypcji. Możesz uzyskać informacje o liście kontrolnej przenoszenia zasobów, dowiedzieć się, które usługi obsługują operację przenoszenia i dowiedzieć się więcej o ograniczeniach usługi App Service i innych tematach.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak ustawić strefę czasową serwera dla mojej aplikacji internetowej?

Aby ustawić strefę czasową serwera dla aplikacji sieci web:

1. W witrynie Azure portal w ramach subskrypcji usługi App Service przejdź do menu **Ustawienia aplikacji.**
2. W obszarze **Ustawienia aplikacji**dodaj to ustawienie:
    * Klawisz = WEBSITE_TIME_ZONE
    * Wartość = *Odpowiednia strefa czasowa*
3. Wybierz pozycję **Zapisz**.

W przypadku usług aplikacji uruchamianych w systemie Windows zobacz **kolumnę Strefa czasowa** w [artykule Domyślne strefy czasowe](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) dla zaakceptowanych wartości. W przypadku usług aplikacji uruchamiane w systemie Linux ustaw [nazwę bazy danych TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) jako wartość strefy czasowej. Oto przykład nazwy bazy danych TZ: Ameryka / Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Dlaczego moje ciągłe webjobs czasami nie?

Domyślnie aplikacje sieci web są zwalniane, jeśli są bezczynne przez określony czas. Dzięki temu system oszczędza zasoby. W planach podstawowych i standardowych można włączyć ustawienie **Zawsze włączone,** aby aplikacja internetowa była ładowana przez cały czas. Jeśli aplikacja internetowa działa w sposób ciągły WebJobs, należy włączyć **opcję Zawsze włączone**lub webjobs może nie działać niezawodnie. Aby uzyskać więcej informacji, zobacz [Tworzenie stale uruchomionej usługi WebJob](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak uzyskać wychodzący adres IP dla mojej aplikacji internetowej?

Aby uzyskać listę wychodzących adresów IP dla aplikacji sieci web:

1. W witrynie Azure portal w bloku aplikacji sieci web przejdź do menu **Właściwości.**
2. Wyszukaj **wychodzące adresy IP**.

Pojawi się lista wychodzących adresów IP.

Aby dowiedzieć się, jak uzyskać wychodzący adres IP, jeśli witryna sieci Web jest hostowana w środowisku usługi app service, zobacz [Wychodzące adresy sieciowe](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak uzyskać zarezerwowany lub dedykowany przychodzący adres IP dla mojej aplikacji internetowej?

Aby skonfigurować dedykowany lub zarezerwowany adres IP dla połączeń przychodzących nawiązywać do witryny aplikacji platformy Azure, należy zainstalować i skonfigurować certyfikat TLS/SSL oparty na protokoi.

Należy zauważyć, że aby użyć dedykowanego lub zastrzeżonego adresu IP dla połączeń przychodzących, plan usługi App Service musi znajdować się w planie usługi Podstawowej lub wyższej.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Czy mogę wyeksportować certyfikat usługi App Service do użycia poza platformą Azure, na przykład w witrynie sieci Web hostowanej w innym miejscu? 

Tak, można je wyeksportować do użycia poza platformą Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące certyfikatów usługi App Service i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Czy mogę wyeksportować certyfikat usługi App Service do użycia z innymi usługami w chmurze platformy Azure?

Portal zapewnia pierwszorzędne środowisko wdrażania certyfikatu usługi App Service za pośrednictwem aplikacji usługi Azure Key Vault w usłudze App Service. Jednak otrzymaliśmy żądania od klientów do korzystania z tych certyfikatów poza platformą usługi App Service, na przykład z maszyn wirtualnych platformy Azure. Aby dowiedzieć się, jak utworzyć lokalną kopię certyfikatu usługi App Service, aby można było go używać z innymi zasobami platformy Azure, zobacz [Tworzenie lokalnej kopii pfx certyfikatu usługi App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące certyfikatów usługi App Service i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Dlaczego podczas próby utworzenia kopii zapasowej aplikacji internetowej jest wyświetlany komunikat "Częściowo powiódł się"?

Częstą przyczyną niepowodzenia kopii zapasowej jest to, że niektóre pliki są używane przez aplikację. Pliki, które są w użyciu są zablokowane podczas wykonywania kopii zapasowej. Zapobiega to ich kopii zapasowej i może spowodować stan "Częściowo pomyślnie". Potencjalnie można temu zapobiec, wykluczając pliki z procesu tworzenia kopii zapasowej. Możesz wybrać opcję utworzenia kopii zapasowej tylko tego, co jest potrzebne. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych tylko ważnych części witryny za pomocą aplikacji sieci Web platformy Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak usunąć nagłówek z odpowiedzi HTTP?

Aby usunąć nagłówki z odpowiedzi HTTP, zaktualizuj plik web.config witryny. Aby uzyskać więcej informacji, zobacz [Usuwanie standardowych nagłówków serwerów w witrynach sieci Web platformy Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Czy usługa App Service jest zgodna ze standardem PCI Standard 3.0 i 3.1?

Obecnie funkcja aplikacji sieci Web usługi Azure App Service jest zgodna z standardem ZABEZPIECZEŃ DANYCH PCI (DSS) w wersji 3.0 Poziom 1. PCI DSS w wersji 3.1 znajduje się na naszej mapie drogowej. Trwają już prace nad przyjęciem najnowszego standardu.

Certyfikacja PCI DSS w wersji 3.1 wymaga wyłączenia zabezpieczeń warstwy transportowej (TLS) 1.0. Obecnie wyłączenie protokołu TLS 1.0 nie jest opcją dla większości planów usługi App Service. Jednak jeśli używasz środowiska usługi App Service lub chcesz przeprowadzić migrację obciążenia do środowiska usługi App Service, możesz uzyskać większą kontrolę nad środowiskiem. Wiąże się to z wyłączeniem protokołu TLS 1.0 przez skontaktowanie się z pomocą techniczną platformy Azure. W niedalekiej przyszłości planujemy udostępnić te ustawienia użytkownikom.

Aby uzyskać więcej informacji, zobacz [Zgodność aplikacji sieci Web usługi Microsoft Azure App Service z pci standard 3.0 i 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak korzystać ze środowiska przejściowego i gniazd wdrożeniowych?

W planach usługi aplikacji standardowej i premium podczas wdrażania aplikacji sieci web w usłudze App Service można wdrożyć w osobnym gnieździe wdrażania, a nie w domyślnym miejscu produkcyjnym. Miejsca wdrożenia to aplikacje sieci web na żywo, które mają własne nazwy hostów. Zawartość aplikacji sieci Web i elementy konfiguracji mogą być zamienione między dwoma gniazdami wdrażania, w tym gniazdem produkcyjnym.

Aby uzyskać więcej informacji na temat korzystania z gniazd wdrażania, zobacz [Konfigurowanie środowiska przejściowego w usłudze App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak uzyskać dostęp do dzienników webjob i przeglądać go?

Aby przejrzeć dzienniki webjob:

1. Zaloguj się na swojej [stronie internetowej Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz webjob.
3. Wybierz przycisk **Przełącz dane wyjściowe.**
4. Aby pobrać plik wyjściowy, wybierz łącze **Pobierz.**
5. W przypadku pojedynczych przebiegów wybierz opcję **Pojedyncze wywoływanie**.
6. Wybierz przycisk **Przełącz dane wyjściowe.**
7. Wybierz link do pobrania.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Próbuję użyć połączeń hybrydowych z programem SQL Server. Dlaczego widzę komunikat "System.OverflowException: Arithmetic operation resulted in an overflow"?

Jeśli używasz połączeń hybrydowych do uzyskiwania dostępu do programu SQL Server, aktualizacja platformy Microsoft .NET z 10 maja 2016 r. może spowodować niepowodzenie połączeń. Może zostać wyświetlony ten komunikat:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Rozwiązanie

Wyjątek został spowodowany przez problem z Menedżerem połączeń hybrydowych, który został rozwiązany. Aby rozwiązać ten problem, [należy zaktualizować Menedżera połączeń hybrydowych.](https://go.microsoft.com/fwlink/?LinkID=841308)

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Jak dodać regułę przepisywania adresu URL?

Aby dodać regułę przepisywania adresu URL, utwórz plik web.config z odpowiednimi wpisami konfiguracji w folderze **wwwroot.** Aby uzyskać więcej informacji, zobacz [Usługi aplikacji Azure App Services: Opis przepisywania adresu URL](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak kontrolować ruch przychodzący do usługi App Service?

Na poziomie witryny dostępne są dwie opcje kontrolowania ruchu przychodzącego do usługi App Service:

* Włącz dynamiczne ograniczenia ip. Aby dowiedzieć się, jak włączyć dynamiczne ograniczenia dotyczące adresów IP, zobacz [Ograniczenia dotyczące adresów IP i domen dla witryn sieci Web platformy Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Włącz zabezpieczenia modułu. Aby dowiedzieć się, jak włączyć funkcję Zabezpieczenia modułu, zobacz [Zapora aplikacji sieci Web ModSecurity w witrynach sieci Web platformy Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Jeśli używasz środowiska usługi App Service, możesz użyć [zapory Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak zablokować porty w aplikacji sieci Web usługi App Service?

W środowisku dzierżawy udostępnionej usługi App Service nie jest możliwe blokowanie określonych portów ze względu na charakter infrastruktury. Porty TCP 4020, 4022 i 4024 również mogą być otwarte dla zdalnego debugowania programu Visual Studio.

W środowisku usługi aplikacji masz pełną kontrolę nad ruchem przychodzącym i wychodzącym. Za pomocą sieciowych grup zabezpieczeń można ograniczyć lub zablokować określone porty. Aby uzyskać więcej informacji na temat środowiska usługi App Service, zobacz [Wprowadzenie środowiska usługi app service](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak przechwycić ślad F12?

Masz dwie opcje przechwytywania śledzenia F12:

* F12 Śledzenie HTTP
* Wyjście konsoli F12

### <a name="f12-http-trace"></a>F12 Śledzenie HTTP

1. W programie Internet Explorer przejdź do witryny sieci Web. Przed rozpoczęciem kolejnych czynności należy się zalogować. W przeciwnym razie śledzenia F12 przechwytuje poufnych danych logowania.
2. Naciśnij klawisz F12.
3. Sprawdź, czy wybrano kartę **Sieć,** a następnie wybierz zielony przycisk **Odtwórz.**
4. Wykonaj kroki, które odtwarzają problem.
5. Wybierz czerwony przycisk **Stop.**
6. Wybierz przycisk **Zapisz** (ikonę dysku) i zapisz plik HAR (w programach Internet Explorer i Microsoft Edge) *lub* kliknij prawym przyciskiem myszy plik HAR, a następnie wybierz pozycję **Zapisz jako HAR z zawartością** (w Chrome).

### <a name="f12-console-output"></a>Wyjście konsoli F12

1. Wybierz kartę **Konsola.**
2. Dla każdej karty zawierającej więcej niż zero elementów wybierz kartę **(Błąd**, **Ostrzeżenie**lub **Informacje).** Jeśli karta nie jest zaznaczona, ikona karty jest szara lub czarna po odsunięciu kursora od niej.
3. Kliknij prawym przyciskiem myszy w obszarze wiadomości okienka, a następnie wybierz pozycję **Skopiuj wszystkie**.
4. Wklej skopiowany tekst do pliku, a następnie zapisz plik.

Aby wyświetlić plik HAR, można użyć [przeglądarki HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Dlaczego podczas próby podłączenia aplikacji sieci Web usługi App Service do sieci wirtualnej połączonej z usługą ExpressRoute pojawia się błąd?

Jeśli spróbujesz połączyć aplikację sieci Web platformy Azure z siecią wirtualną, która jest połączona z usługą Azure ExpressRoute, zakończy się niepowodzeniem. Zostanie wyświetlony następujący komunikat: "Brama nie jest bramą sieci VPN".

Obecnie nie można mieć połączeń sieci VPN typu punkt-lokacja z siecią wirtualną połączoną z usługą ExpressRoute. Sieć VPN typu lokacja i usługa ExpressRoute nie mogą współistnieć dla tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Limity i ograniczenia połączeń sieci VPN typu lokacja-lokacja](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak połączyć aplikację sieci Web usługi App Service z siecią wirtualną, która ma bramę routingu statycznego (opartego na zasadach).How do i connect an App Service web app to a virtual network that has a static routing (policy-based) gateway?

Obecnie łączenie aplikacji sieci web usługi App Service z siecią wirtualną, która ma bramę routingu statycznego (opartego na zasadach), nie jest obsługiwane. Jeśli docelowa sieć wirtualna już istnieje, musi mieć włączoną sieć VPN typu punkt-lokacja z dynamiczną bramą routingu, aby można ją było połączyć z aplikacją. Jeśli brama jest ustawiona na routing statyczny, nie można włączyć sieci VPN typu punkt-lokacja. 

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Dlaczego w środowisku usługi app service mogę utworzyć tylko jeden plan usługi app service, mimo że dostępnych jest dwóch pracowników?

Aby zapewnić odporność na uszkodzenia, środowisko usługi app service wymaga, aby każda pula procesów procesowych wymaga co najmniej jednego dodatkowego zasobu obliczeniowego. Dodatkowego zasobu obliczeniowego nie można przypisać obciążenia.

Aby uzyskać więcej informacji, zobacz [Jak utworzyć środowisko usługi app service](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Dlaczego podczas próby utworzenia środowiska usługi app service są widoczne limity czasu?

Czasami tworzenie środowiska usługi aplikacji kończy się niepowodzeniem. W takim przypadku w dziennikach aktywności jest widoczny następujący błąd:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Aby rozwiązać ten problem, upewnij się, że żaden z następujących warunków nie jest spełniony:
* Podsieć jest za mała.
* Podsieć nie jest pusta.
* Usługa ExpressRoute zapobiega wymaganiom łączności sieciowej środowiska usługi app service.
* Zła grupa zabezpieczeń sieci uniemożliwia wymagania dotyczące łączności sieciowej środowiska usługi app service.
* Wymuszone tunelowanie jest włączone.

Aby uzyskać więcej informacji, zobacz [Częste problemy podczas wdrażania (tworzenia) nowego środowiska usługi Azure App Service](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Dlaczego nie mogę usunąć planu usługi app service?

Nie można usunąć planu usługi app service, jeśli wszystkie aplikacje usługi App Service są skojarzone z planem usługi App Service. Przed usunięciem planu usługi app service usuń wszystkie skojarzone aplikacje usługi App Service z planu usługi app service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak zaplanować pewien WebJob?

Można utworzyć zaplanowaną robotę WebJob przy użyciu wyrażeń Cron:

1. Utwórz plik settings.job.
2. W tym pliku JSON, dołącz właściwość harmonogram przy użyciu wyrażenia Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Aby uzyskać więcej informacji na temat zaplanowanych funkcji WebJobs, zobacz [Tworzenie zaplanowanego zadania WebJob przy użyciu wyrażenia Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak przeprowadzić testy penetracyjne dla aplikacji Usługi App Service?

Aby wykonać testy penetracyjne, [prześlij żądanie](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak skonfigurować niestandardową nazwę domeny dla aplikacji sieci Web usługi App Service korzystającej z Usługi Traffic Manager?

Aby dowiedzieć się, jak używać niestandardowej nazwy domeny z aplikacją usługi App Service, która używa usługi Azure Traffic Manager do równoważenia obciążenia, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web platformy Azure za pomocą usługi Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mój certyfikat usługi App Service został oznaczony jako oszustwo. Jak rozwiązać ten problem?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podczas weryfikacji domeny zakupu certyfikatu usługi App Service może zostać wyświetlony następujący komunikat:

"Twój certyfikat został oznaczony pod kątem ewentualnego oszustwa. Wniosek jest obecnie w trakcie rozpatrywana. Jeśli certyfikat nie stanie się użyteczny w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

Jak wskazuje komunikat, ten proces weryfikacji oszustw może potrwać do 24 godzin. W tym czasie będzie nadal widzieć komunikat.

Jeśli certyfikat usługi App Service nadal wyświetla ten komunikat po 24 godzinach, uruchom następujący skrypt programu PowerShell. Skrypt kontaktuje się bezpośrednio z [dostawcą certyfikatów,](https://www.godaddy.com/) aby rozwiązać problem.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak działa uwierzytelnianie i autoryzacja w usłudze App Service?

Aby uzyskać szczegółową dokumentację uwierzytelniania i autoryzacji w usłudze App Service, zobacz dokumenty dotyczące różnych logowania dostawców identyfikujących:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Konto Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak przekierować domyślną domenę *.azurewebsites.net do domeny niestandardowej aplikacji sieci Web platformy Azure?

Podczas tworzenia nowej witryny sieci Web przy użyciu aplikacji sieci Web na platformie Azure domyślna *nazwa witryny*.azurewebsites.net domena jest przypisana do witryny. Jeśli dodasz niestandardową nazwę hosta do witryny i nie chcesz, aby użytkownicy mieli dostęp do domyślnej domeny *.azurewebsites.net, możesz przekierować domyślny adres URL. Aby dowiedzieć się, jak przekierować cały ruch z domeny domyślnej witryny do domeny niestandardowej, zobacz [Przekierowywanie domeny domyślnej do domeny niestandardowej w aplikacjach sieci Web platformy Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak ustalić, która wersja wersji platformy .NET jest zainstalowana w usłudze App Service?

Najszybszym sposobem znalezienia wersji platformy Microsoft .NET zainstalowanej w usłudze App Service jest użycie konsoli Kudu. Dostęp do konsoli Kudu można uzyskać z portalu lub przy użyciu adresu URL aplikacji usługi App Service. Aby uzyskać szczegółowe instrukcje, zobacz [Określanie zainstalowanej wersji platformy .NET w usłudze App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Dlaczego skalowanie automatyczne nie działa zgodnie z oczekiwaniami?

Jeśli skalowanie automatyczne platformy Azure nie jest skalowane lub skalowane w poziomie wystąpienia aplikacji sieci web zgodnie z oczekiwaniami, może być uruchomiony w scenariuszu, w którym celowo zdecydujemy się nie skalować, aby uniknąć nieskończonej pętli z powodu "flapping". Zwykle dzieje się tak, gdy nie ma odpowiedniego marginesu między progami skalowania w poziomie i skalowania. Aby dowiedzieć się, jak uniknąć "machania" i przeczytać o innych najlepszych praktykach skalowania automatycznego, zobacz [Najlepsze rozwiązania skalowania automatycznego.](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Dlaczego skalowanie automatyczne czasami skaluje się tylko częściowo?

Skalowanie automatyczne jest wyzwalane, gdy metryki przekraczają wstępnie skonfigurowane granice. Czasami można zauważyć, że pojemność jest tylko częściowo wypełniona w porównaniu do tego, czego oczekiwano. Taka liczba może wystąpić, gdy liczba wystąpień, które chcesz, nie są dostępne. W tym scenariuszu skalowanie automatyczne częściowo wypełnia się dostępną liczbą wystąpień. Skalowanie automatyczne następnie uruchamia logikę równoważenia, aby uzyskać większą pojemność. Przydziela pozostałe wystąpienia. Należy pamiętać, że może to potrwać kilka minut.

Jeśli nie widzisz oczekiwanej liczby wystąpień po kilku minutach, może to być spowodowane częściowym uzupełnianiem wystarczyło, aby przenieść metryki w granicach. Lub skalowanie automatyczne może mieć skalowane w dół, ponieważ osiągnął dolną granicę metryki.

Jeśli żaden z tych warunków nie ma zastosowania, a problem będzie się powtarzał, prześlij żądanie pomocy technicznej.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak włączyć kompresję HTTP dla mojej zawartości?

Aby włączyć kompresję zarówno dla typów zawartości statycznej, jak i dynamicznej, dodaj następujący kod do pliku web.config na poziomie aplikacji:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Można również określić określone dynamiczne i statyczne typy MIME, które mają być kompresowanych. Aby uzyskać więcej informacji, zobacz naszą odpowiedź na pytanie na forum w [ustawieniach httpCompression w prostej witrynie sieci Web platformy Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak przeprowadzić migrację ze środowiska lokalnego do usługi App Service?

Aby przeprowadzić migrację witryn z serwerów sieci Web systemu Windows i Linux do usługi App Service, można użyć Asystenta migracji usługi Azure App Service. Narzędzie migracji tworzy aplikacje sieci web i bazy danych na platformie Azure w razie potrzeby, a następnie publikuje zawartość. Aby uzyskać więcej informacji, zobacz [Asystent migracji usługi Azure App Service](https://appmigration.microsoft.com/).
