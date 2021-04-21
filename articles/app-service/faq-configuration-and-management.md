---
title: Często zadawane pytania o konfigurację
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem dla Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4b6ceef837d985db004e9d925b554c54c287424
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834538"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące konfiguracji i zarządzania Web Apps platformie Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem Web Apps [funkcji Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Czy istnieją ograniczenia, o których należy wiedzieć, jeśli chcę przenieść App Service zasobów?

Jeśli planujesz przeniesienie App Service zasobów do nowej grupy zasobów lub subskrypcji, należy pamiętać o kilku ograniczeniach. Aby uzyskać więcej informacji, zobacz [App Service ograniczenia.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak mogę używać niestandardowej nazwy domeny dla mojej aplikacji internetowej?

Odpowiedzi na często zadawane pytania dotyczące używania niestandardowej nazwy domeny z aplikacją internetową platformy Azure można znaleźć w naszym 7-minutowym filmie [Dodawanie niestandardowej nazwy domeny.](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) Film wideo zawiera wskazówki dotyczące dodawania niestandardowej nazwy domeny. Opisano w nim, jak używać własnego adresu URL zamiast adresu URL *.azurewebsites.net z aplikacją App Service internetową. Możesz również zobaczyć szczegółowy przewodnik mapowania [niestandardowej nazwy domeny](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak mogę zakup nowej domeny niestandardowej dla mojej aplikacji internetowej?

Aby dowiedzieć się, jak kupić i skonfigurować domenę niestandardową dla aplikacji internetowej usługi App Service, zobacz Kupowanie i konfigurowanie niestandardowej nazwy [domeny](manage-custom-dns-buy-domain.md)w App Service .


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Jak mogę przekazać i skonfigurować istniejący certyfikat protokołu TLS/SSL dla mojej aplikacji internetowej?

Aby dowiedzieć się, jak przekazać i skonfigurować istniejący niestandardowy certyfikat TLS/SSL, zobacz Dodawanie certyfikatu [TLS/SSL do aplikacji App Service ssl.](configure-ssl-certificate.md)


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Jak mogę zakup i skonfigurowanie nowego certyfikatu TLS/SSL na platformie Azure dla mojej aplikacji internetowej?

Aby dowiedzieć się, jak kupić i skonfigurować certyfikat TLS/SSL dla aplikacji internetowej usługi App Service, zobacz Dodawanie certyfikatu [TLS/SSL](configure-ssl-certificate.md)do aplikacji App Service internetowej.


## <a name="how-do-i-move-application-insights-resources"></a>Jak mogę przenieść Application Insights zasobów?

Obecnie usługa Azure Application Insights nie obsługuje operacji przenoszenia. Jeśli oryginalna grupa zasobów zawiera Application Insights zasobów, nie można przenieść tego zasobu. Jeśli dołączysz zasób Application Insights podczas próby przeniesienia aplikacji App Service, cała operacja przenoszenia zakończy się niepowodzeniem. Jednak Application Insights i App Service nie muszą być w tej samej grupie zasobów co aplikacja, aby aplikacja działała poprawnie.

Aby uzyskać więcej informacji, zobacz [App Service ograniczenia.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Gdzie można znaleźć listę kontrolną wskazówek i dowiedzieć się więcej o operacjach przenoszenia zasobów?

[App Service ograniczenia](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) pokazują, jak przenieść zasoby do nowej subskrypcji lub do nowej grupy zasobów w tej samej subskrypcji. Możesz uzyskać informacje na temat listy kontrolnej przenoszenia zasobów, dowiedzieć się, które usługi obsługują operację przenoszenia, oraz dowiedzieć się więcej na temat ograniczeń App Service innych tematów.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak mogę ustawić strefę czasową serwera dla mojej aplikacji internetowej?

Aby ustawić strefę czasową serwera dla aplikacji internetowej:

1. W Azure Portal w subskrypcji App Service przejdź do menu **Ustawienia** aplikacji.
2. W **obszarze Ustawienia aplikacji** dodaj to ustawienie:
    * Klucz = WEBSITE_TIME_ZONE
    * Wartość = *strefa czasowa, której potrzebujesz*
3. Wybierz pozycję **Zapisz**.

W przypadku usług App Services uruchomionych w systemie Windows zobacz dane wyjściowe polecenia systemu `tzutil /L` Windows. Użyj wartości z drugiego wiersza każdego wpisu. Na przykład: "Tonga Standard Time". Niektóre z tych wartości są również wymienione w kolumnie **Strefa czasowa** w [domyślnych strefach czasowych](/windows-hardware/manufacture/desktop/default-time-zones).

W przypadku usług App Services uruchomionych w systemie Linux ustaw wartość z bazy [danych IANA TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Na przykład: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Dlaczego moje ciągłe usługi WebJob czasami nie powiodą się?

Domyślnie aplikacje internetowe są zwalniane, jeśli są bezczynne przez określony czas. Dzięki temu system może oszczędzać zasoby. W planach Podstawowy i Standardowy można włączyć ustawienie **Zawsze wł.,** aby aplikacja internetowa była ładowana przez cały czas. Jeśli aplikacja internetowa uruchamia ciągłe usługi WebJob, należy włączyć zawsze włącz lub te usługi mogą nie działać niezawodnie.  Aby uzyskać więcej informacji, zobacz [Create a continuously running WebJob (Tworzenie stale działającegoob WebJob).](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak mogę uzyskać adres IP ruchu wychodzącego dla mojej aplikacji internetowej?

Aby uzyskać listę wychodzących adresów IP dla aplikacji internetowej:

1. W Azure Portal aplikacji internetowej przejdź do **menu** Właściwości.
2. Wyszukaj adresy **IP ruchu wychodzącego.**

Zostanie wyświetlona lista wychodzących adresów IP.

Aby dowiedzieć się, jak uzyskać adres IP ruchu wychodzącego, jeśli witryna internetowa jest hostowana w App Service Environment, zobacz [Wychodzące adresy sieciowe.](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak mogę uzyskać zastrzeżony lub dedykowany adres IP ruchu przychodzącego dla mojej aplikacji internetowej?

Aby skonfigurować dedykowany lub zastrzeżony adres IP dla wywołań przychodzących do witryny internetowej aplikacji platformy Azure, zainstaluj i skonfiguruj certyfikat TLS/SSL oparty na adresie IP.

Należy pamiętać, że aby używać dedykowanego lub zastrzeżonego adresu IP dla wywołań przychodzących, plan App Service musi być w planie usługi w chmurze w chmurze podstawowej lub wyższej.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Czy mogę wyeksportować certyfikat App Service do użycia poza platformą Azure, na przykład w przypadku witryny internetowej hostowanej w innym miejscu? 

Tak, można je wyeksportować do użycia poza platformą Azure. Aby uzyskać więcej informacji, zobacz często zadawane pytania [dotyczące App Service certyfikatów i domen niestandardowych.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Czy mogę wyeksportować certyfikat App Service do użycia z innymi usługami w chmurze platformy Azure?

Portal zapewnia najwyższej klasy środowisko do wdrażania certyfikatu App Service za pośrednictwem Azure Key Vault do App Service aplikacji. Otrzymujemy jednak żądania od klientów dotyczące używania tych certyfikatów poza platformą App Service, na przykład z usługą Azure Virtual Machines. Aby dowiedzieć się, jak utworzyć lokalną kopię PFX certyfikatu App Service w celu używania certyfikatu z innymi zasobami platformy Azure, zobacz Tworzenie lokalnej kopii PFX certyfikatu App Service [wirtualnej.](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)

Aby uzyskać więcej informacji, zobacz często zadawane pytania [dotyczące App Service certyfikatów i domen niestandardowych.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Dlaczego podczas próby kopii zapasowej aplikacji internetowej pojawia się komunikat "Częściowe powodzenie"?

Częstą przyczyną niepowodzenia tworzenia kopii zapasowej jest to, że niektóre pliki są w użyciu przez aplikację. Pliki, które są w użyciu, są blokowane podczas wykonywania kopii zapasowej. Zapobiega to tworzeniu kopii zapasowej tych plików i może mieć stan "Częściowe powodzenie". Można temu zapobiec, wykluczając pliki z procesu tworzenia kopii zapasowej. Możesz wybrać opcję kopii zapasowej tylko tego, co jest potrzebne. Aby uzyskać więcej informacji, zobacz Backup just the important parts of your site with Azure web apps (Tworzenie kopii zapasowych tylko ważnych części [witryny za pomocą aplikacji internetowych platformy Azure).](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak mogę usunąć nagłówek z odpowiedzi HTTP?

Aby usunąć nagłówki z odpowiedzi HTTP, zaktualizuj plik web.config lokacji. Aby uzyskać więcej informacji, zobacz [Remove standard server headers on your Azure websites (Usuwanie standardowych nagłówków serwera w witrynach internetowych platformy Azure).](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Czy App Service zgodne ze standardami PCI Standard 3.0 i 3.1?

Obecnie funkcja Web Apps usługi Azure App Service jest zgodnie z normą PCI Data Security Standard (DSS) w wersji 3.0 poziom 1. PCI DSS wersji 3.1 znajduje się w naszym harmonogramie działania. Trwa planowanie sposobu wdrożenia najnowszego standardu.

PCI DSS wersji 3.1 wymaga wyłączenia Transport Layer Security (TLS) 1.0. Obecnie wyłączenie TLS 1.0 nie jest opcją dla większości App Service planów. Jeśli jednak korzystasz z App Service Environment lub chcesz migrować obciążenie do App Service Environment, możesz uzyskać większą kontrolę nad środowiskiem. Obejmuje to wyłączenie obsługi TLS 1.0 przez skontaktowanie się z pomoc techniczna platformy Azure. W najbliższej przyszłości planujemy udostępnić te ustawienia użytkownikom.

Aby uzyskać więcej informacji, zobacz Microsoft Azure App Service zgodność aplikacji internetowej ze standardami [PCI Standard 3.0 i 3.1.](https://support.microsoft.com/help/3124528)

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak mogę używać środowiska przejściowego i miejsc wdrożenia?

W planach usługi App Service standardowych i Premium podczas wdrażania aplikacji internetowej w programie App Service można wdrożyć w osobnym miejscu wdrożenia, a nie w domyślnym miejscu produkcyjnym. Miejsca wdrożenia to aplikacje internetowe na żywo, które mają własne nazwy hostów. Zawartość i elementy konfiguracji aplikacji internetowej można zamienić między dwoma miejscami wdrożenia, w tym z miejsca produkcyjnego.

Aby uzyskać więcej informacji na temat korzystania z miejsc wdrożenia, [zobacz Konfigurowanie środowiska przejściowego w programie App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak mogę i przeglądać dzienniki usługi WebJob?

Aby przejrzeć dzienniki usługi WebJob:

1. Zaloguj się do witryny **internetowej Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Wybierz usługę WebJob.
3. Wybierz przycisk **Przełącz dane wyjściowe.**
4. Aby pobrać plik wyjściowy, wybierz **link** Pobierz.
5. W przypadku poszczególnych przebiegów wybierz **pozycję Indywidualne wywołanie**.
6. Wybierz przycisk **Przełącz dane wyjściowe.**
7. Wybierz link pobierania.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Próbuję użyć połączeń hybrydowych z SQL Server. Dlaczego widzę komunikat "System.OverflowException: Operacja arytmetyczna zakończyła się przepełnieniem"?

Jeśli używasz połączeń hybrydowych do uzyskiwania SQL Server, aktualizacja Microsoft .NET 10 maja 2016 r. może spowodować niepowodzenie połączeń. Może zostać wyświetlony ten komunikat:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Rozwiązanie

Wyjątek został spowodowany przez problem z Menedżer połączeń hybrydowych, który został naprawiony. Pamiętaj, aby [zaktualizować Menedżer połączeń hybrydowych,](https://go.microsoft.com/fwlink/?LinkID=841308) aby rozwiązać ten problem.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Jak mogę dodać regułę ponownego pisali adresy URL?

Aby dodać regułę ponownego tworzenia adresów URL, utwórz plik web.config z odpowiednimi wpisami konfiguracji w **folderze wwwroot.** Aby uzyskać więcej informacji, zobacz [Azure App Services: Understanding URL rewrite (Azure App Services: Opis ponownego pisać adresy URL).](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak mogę kontrolować ruch przychodzący do App Service?

Na poziomie lokacji dostępne są dwie opcje kontrolowania ruchu przychodzącego do App Service:

* Włącz dynamiczne ograniczenia adresów IP. Aby dowiedzieć się, jak włączyć dynamiczne ograniczenia adresów IP, zobacz [Ograniczenia adresów IP i domen dla witryn internetowych platformy Azure.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)
* Włącz zabezpieczenia modułu. Aby dowiedzieć się, jak włączyć zabezpieczenia modułu, zobacz [ModSecurity web application firewall on Azure websites (Zapora aplikacji internetowej ModSecurity w witrynach internetowych platformy Azure).](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)

Jeśli używasz usługi App Service Environment, możesz użyć zapory [Barracuda.](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak mogę blokować porty w App Service internetowej?

W środowisku App Service dzierżawy nie można zablokować określonych portów ze względu na charakter infrastruktury. Porty TCP 4020, 4022 i 4024 mogą być również otwarte Visual Studio debugowania zdalnego.

W App Service Environment masz pełną kontrolę nad ruchem przychodzącym i wychodzącym. Sieciowe grupy zabezpieczeń mogą być używane do ograniczania lub blokowania określonych portów. Aby uzyskać więcej informacji na App Service Environment, zobacz [Introducing App Service Environment (Wprowadzenie do App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/)).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak mogę przechwytywać ślad F12?

Dostępne są dwie opcje przechwytywania śledzenia F12:

* Śledzenie F12 HTTP
* Dane wyjściowe konsoli F12

### <a name="f12-http-trace"></a>Śledzenie F12 HTTP

1. W Internet Explorer przejdź do swojej witryny internetowej. Przed rozpoczęciem kolejnych kroków należy się zalogować. W przeciwnym razie ślad F12 przechwyci poufne dane logowania.
2. Naciśnij klawisz F12.
3. Sprawdź, czy **wybrano** kartę Sieć, a następnie wybierz zielony przycisk **Odtwarzania.**
4. Wykonaj kroki, które odtąd odtąd występuje problem.
5. Wybierz czerwony przycisk **Zatrzymaj.**
6. Wybierz  przycisk Zapisz (ikona *dysku)* i zapisz plik HAR (w programach Internet Explorer i Microsoft Edge) lub kliknij prawym przyciskiem myszy plik HAR, a następnie wybierz pozycję Zapisz jako **plik HAR** z zawartością (w przeglądarce Chrome).

### <a name="f12-console-output"></a>Dane wyjściowe konsoli F12

1. Wybierz **kartę Konsola.**
2. Dla każdej karty zawierającej więcej niż zero elementów wybierz kartę **(Błąd,** **Ostrzeżenie** lub **Informacje).** Jeśli karta nie jest zaznaczona, ikona karty jest szara lub czarna po odsunięcie kursora od niego.
3. Kliknij prawym przyciskiem myszy w obszarze komunikatu okienka, a następnie wybierz pozycję **Kopiuj wszystkie .**
4. Wklej skopiowany tekst do pliku, a następnie zapisz plik.

Aby wyświetlić plik HAR, możesz użyć przeglądarki [HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Dlaczego otrzymuję błąd podczas próby połączenia aplikacji internetowej App Service z siecią wirtualną, która jest połączona z usługą ExpressRoute?

Próba połączenia aplikacji internetowej platformy Azure z siecią wirtualną, która jest połączona z Azure ExpressRoute, zakończy się niepowodzeniem. Zostanie wyświetlony następujący komunikat: "Brama nie jest bramą sieci VPN".

Obecnie nie można mieć połączeń sieci VPN typu punkt-lokacja z siecią wirtualną połączeniową z usługą ExpressRoute. Sieć VPN typu punkt-lokacja i usługa ExpressRoute nie mogą współistnieć dla tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz ExpressRoute and site-to-site VPN connections limits and limitations (Limity i ograniczenia połączeń sieci VPN typu [lokacja-lokacja i usługi ExpressRoute).](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak mogę połączyć App Service internetową z siecią wirtualną, która ma bramę routingu statycznego (opartą na zasadach)?

Obecnie łączenie aplikacji App Service z siecią wirtualną, która ma bramę routingu statycznego (opartą na zasadach), nie jest obsługiwana. Jeśli docelowa sieć wirtualna już istnieje, musi mieć włączoną sieć VPN typu punkt-lokacja z bramą routingu dynamicznego, aby można było połączyć ją z aplikacją. Jeśli brama jest ustawiona na routing statyczny, nie można włączyć sieci VPN typu punkt-lokacja. 

Aby uzyskać więcej informacji, zobacz [Integrate an app with an Azure virtual network (Integrowanie aplikacji z siecią wirtualną platformy Azure).](web-sites-integrate-with-vnet.md)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Dlaczego w mojej App Service Environment mogę utworzyć tylko jeden plan App Service, mimo że mam dostępnych dwóch pracowników?

Aby zapewnić odporność na uszkodzenia, App Service Environment każda pula procesów roboczych wymaga co najmniej jednego dodatkowego zasobu obliczeniowego. Nie można przypisać obciążenia do dodatkowego zasobu obliczeniowego.

Aby uzyskać więcej informacji, [zobacz Jak utworzyć App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Dlaczego podczas próby utworzenia App Service Environment aplikacji widzę limity czasu?

Czasami tworzenie App Service Environment kończy się niepowodzeniem. W takim przypadku w dziennikach aktywności zostanie wyświetlony następujący błąd:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Aby rozwiązać ten problem, upewnij się, że żaden z następujących warunków nie jest prawdziwy:
* Podsieć jest za mała.
* Podsieć nie jest pusta.
* ExpressRoute zapobiega wymaganiach dotyczących łączności sieciowej App Service Environment.
* Zła sieciowa grupa zabezpieczeń uniemożliwia wymagania dotyczące łączności sieciowej App Service Environment.
* Wymuszone tunelowanie jest włączone.

Aby uzyskać więcej informacji, zobacz [Częste problemy podczas wdrażania (tworzenia) nowego Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Dlaczego nie mogę usunąć planu App Service?

Nie można usunąć planu App Service, jeśli jakiekolwiek App Service są skojarzone z App Service planem. Przed usunięciem planu App Service usuń wszystkie skojarzone App Service z App Service planu.

## <a name="how-do-i-schedule-a-webjob"></a>Jak mogę zaplanować zadań WebJob?

Zaplanowane zadanie WebJob można utworzyć przy użyciu wyrażeń Cron:

1. Utwórz plik settings.job.
2. W tym pliku JSON dołącz właściwość schedule przy użyciu wyrażenia Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Aby uzyskać więcej informacji o zaplanowanych zadaniach WebJob, zobacz [Create a scheduled WebJob by using a Cron expression (Tworzenie zaplanowanego zadania WebJob przy użyciu wyrażenia Cron).](webjobs-create.md#CreateScheduledCRON)

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak mogę test penetracyjnych dla mojej App Service testów penetracyjnych?

Aby przeprowadzić testowanie penetracyjnych, [prześlij żądanie](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak mogę skonfigurować niestandardową nazwę domeny dla aplikacji App Service internetowej, która używa Traffic Manager?

Aby dowiedzieć się, jak używać niestandardowej nazwy domeny z aplikacją usługi App Service, która używa usługi Azure Traffic Manager do równoważenia obciążenia, zobacz Konfigurowanie niestandardowej nazwy domeny dla aplikacji internetowej platformy Azure za pomocą usługi [Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mój certyfikat usługi App Service został oznaczony jako oszustwo. Jak rozwiązać ten problem?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podczas weryfikacji domeny zakupu certyfikatu App Service może zostać wyświetlony następujący komunikat:

"Twój certyfikat został oflagowany w przypadku możliwego oszustwa. Żądanie jest obecnie w trakcie przeglądu. Jeśli certyfikat nie stanie się użyteczny w ciągu 24 godzin, skontaktuj się z pomoc techniczna platformy Azure".

Jak wskazuje komunikat, ukończenie tego procesu weryfikacji oszustwa może potrwać do 24 godzin. W tym czasie komunikat będzie nadal wyświetlany.

Jeśli twój App Service nadal wyświetla ten komunikat po 24 godzinach, uruchom następujący skrypt programu PowerShell. Skrypt kontaktuje się [bezpośrednio z dostawcą certyfikatów](https://www.godaddy.com/) w celu rozwiązania problemu.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak działa uwierzytelnianie i autoryzacja w App Service?

Aby uzyskać szczegółową dokumentację dotyczącą uwierzytelniania i autoryzacji w App Service, zobacz dokumentację dotyczącą logowania różnych dostawców identyfikacji:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Konto Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak mogę przekierować domyślną domenę *.azurewebsites.net do domeny niestandardowej mojej aplikacji internetowej platformy Azure?

Podczas tworzenia nowej witryny internetowej przy użyciu usługi Web Apps platformie Azure do witryny jest przypisywana domyślna nazwa azurewebsites.net .azurewebsites.net witryny. Jeśli dodasz niestandardową nazwę hosta do witryny i nie chcesz, aby użytkownicy mogli uzyskać dostęp do domyślnej domeny *.azurewebsites.net, możesz przekierować domyślny adres URL. Aby dowiedzieć się, jak przekierować cały ruch z domeny domyślnej witryny internetowej do domeny niestandardowej, zobacz Przekierowywanie domeny domyślnej do domeny niestandardowej w [aplikacjach internetowych platformy Azure.](https://zainrizvi.io/blog/block-default-azure-websites-domain/)

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak mogę, która wersja programu .NET jest zainstalowana w programie App Service?

Najszybszym sposobem na znalezienie wersji programu Microsoft .NET zainstalowanej w programie App Service jest użycie konsoli Kudu. Dostęp do konsoli Kudu można uzyskać z portalu lub przy użyciu adresu URL App Service aplikacji. Aby uzyskać szczegółowe instrukcje, [zobacz Określanie zainstalowanej wersji programu .NET w programie App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Dlaczego skalowanie automatyczne nie działa zgodnie z oczekiwaniami?

Jeśli skalowanie automatyczne na platformie Azure nie skaluje wystąpienia aplikacji internetowej w oczekiwany sposób ani nie skaluje go w oczekiwany sposób, możesz działać w scenariuszu, w którym celowo nie zdecydowaliśmy się na skalowanie, aby uniknąć nieskończonej pętli z powodu "chybnego" działania. Zwykle dzieje się tak, gdy nie ma odpowiedniego marginesu między progami skalowania w zewnątrz i skalowania w zewnątrz. Aby dowiedzieć się, jak uniknąć "nieschemki" i zapoznać się z innymi najlepszymi rozwiązaniami w zakresie skalowania automatycznego, zobacz Najlepsze rozwiązania dotyczące [skalowania automatycznego.](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Dlaczego autoskalowanie czasami jest skalowane tylko częściowo?

Autoskalowanie jest wyzwalane, gdy metryki przekraczają wstępnie skonfigurowane granice. Czasami można zauważyć, że pojemność jest tylko częściowo wypełniona w porównaniu z oczekiwanymi. Może się to zdarzyć, gdy liczba wystąpień, które mają być niedostępne. W tym scenariuszu autoskalowanie częściowo wypełnia dostępną liczbę wystąpień. Następnie autoskalowanie uruchamia logikę ponownego równoważenia w celu uzyskania większej pojemności. Przydziela pozostałe wystąpienia. Pamiętaj, że może to potrwać kilka minut.

Jeśli nie widzisz oczekiwanej liczby wystąpień po kilku minutach, może to być spowodowane tym, że częściowe wystąpienie było wystarczające, aby wyprowadzić metryki w granice. Skalowanie automatyczne mogło też zostać przeskalowane w dół, ponieważ osiągnęło dolną granicę metryk.

Jeśli żaden z tych warunków nie ma zastosowania, a problem będzie się powtarzać, prześlij wniosek o pomoc techniczną.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak mogę włączyć kompresję HTTP dla mojej zawartości?

Aby włączyć kompresję zarówno dla typów zawartości statycznej, jak i dynamicznej, dodaj następujący kod do pliku web.config aplikacji:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Można również określić określone dynamiczne i statyczne typy MIME, które mają być kompresowane. Aby uzyskać więcej informacji, zobacz naszą odpowiedź na pytanie na forum w ustawieniach [httpCompression w prostej witrynie internetowej platformy Azure.](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak mogę przeprowadzić migrację ze środowiska lokalnego do App Service?

Aby migrować witryny z serwerów internetowych z systemami Windows i Linux App Service, można użyć Azure App Service Migration Assistant. Narzędzie do migracji tworzy aplikacje internetowe i bazy danych na platformie Azure zgodnie z potrzebami, a następnie publikuje zawartość. Aby uzyskać więcej informacji, [zobacz Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Dlaczego mój certyfikat został wystawiony na 11 miesięcy, a nie na cały rok?

W przypadku wszystkich certyfikatów wystawionych po 01/09/2020 maksymalny czas trwania wynosi teraz 397 dni. Certyfikaty wystawione przed 01/09/2020 mają maksymalną ważność równą 825 dni do czasu odnowienia, ponownego wprowadzenia klucza itp. Ta zmiana wpłynie na każdy certyfikat odnowiony po 01/09/2020, a użytkownicy mogą zauważyć krótszy okres ważności odnowionych certyfikatów.
Firma GoDaddy zaimplementowała usługę subskrypcji, która spełnia nowe wymagania przy zachowaniu istniejących certyfikatów klientów. Trzydzieści dni przed wygaśnięciem nowo wystawionego certyfikatu usługa automatycznie wystawia drugi certyfikat, który wydłuża czas trwania do pierwotnej daty wygaśnięcia. Usługa App Service współpracuje z usługą GoDaddy w celu upewnienia się, że nasi klienci uzyskają pełny czas trwania swoich certyfikatów.
