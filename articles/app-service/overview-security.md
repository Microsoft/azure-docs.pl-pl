---
title: Zabezpieczenia
description: Dowiedz się, jak usługa App Service pomaga zabezpieczyć aplikację i jak dodatkowo zablokować aplikację przed zagrożeniami.
keywords: usługa aplikacji azure, aplikacja internetowa, aplikacja mobilna, aplikacja API, aplikacja funkcji, bezpieczeństwo, bezpieczne, zabezpieczone, zgodność, zgodny, certyfikat, certyfikaty, https, ftps, tls, zaufanie, szyfrowanie, szyfrowanie, szyfrowane, ograniczenie IP, uwierzytelnianie, autoryzacja, authn, autho, msi, tożsamość usługi zarządzanej, tożsamość zarządzana, tajne, łatanie, łatanie, łata, wersja, izolacja, izolacja sieci, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 8a098b1924bf7c2866f6afd7452b8dd3b93f3109
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535659"
---
# <a name="security-in-azure-app-service"></a>Zabezpieczenia w usłudze aplikacji platformy Azure

W tym artykule pokazano, jak [usługa Azure App Service](overview.md) pomaga zabezpieczyć aplikację internetową, aplikację mobilną zaplecza, aplikację interfejsu API i aplikację [funkcji](/azure/azure-functions/). Pokazuje również, jak można dodatkowo zabezpieczyć aplikację za pomocą wbudowanych funkcji usługi App Service.

Składniki platformy usługi App Service, w tym maszyny wirtualne platformy Azure, magazyn, połączenia sieciowe, struktury sieci web, funkcje zarządzania i integracji, są aktywnie zabezpieczone i wzmocnione. Usługa App Service przechodzi ciągłe przeprowadzanie intensywnych kontroli zgodności, aby upewnić się, że:

- Zasoby aplikacji są [zabezpieczone z](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) zasobów platformy Azure innych klientów.
- [Wystąpienia maszyn wirtualnych i oprogramowanie środowiska wykonawczego są regularnie aktualizowane w](overview-patch-os-runtime.md) celu wyeliminowania nowo wykrytych luk w zabezpieczeniach. 
- Komunikacja wpisów tajnych (takich jak parametry połączenia) między aplikacją a innymi zasobami platformy Azure (takimi jak [baza danych SQL)](https://azure.microsoft.com/services/sql-database/)pozostaje na platformie Azure i nie przekracza żadnych granic sieci. Wpisy tajne są zawsze szyfrowane podczas przechowywania.
- Cała komunikacja za 100 [hybrid connection](app-service-hybrid-connections.md)000 000 000 000 000 000 000 000 000 000 000 000 00 
- Połączenia z narzędziami do zdalnego zarządzania, takimi jak Azure PowerShell, Azure CLI, Azure SDK, REST API, są szyfrowane.
- 24-godzinne zarządzanie zagrożeniami chroni infrastrukturę i platformę przed złośliwym oprogramowaniem, rozproszoną odmową usługi (DDoS), man-in-the-middle (MITM) i innymi zagrożeniami.

Aby uzyskać więcej informacji na temat zabezpieczeń infrastruktury i platformy na platformie Azure, zobacz [Centrum zaufania platformy Azure](https://azure.microsoft.com/overview/trusted-cloud/).

W poniższych sekcjach pokazano, jak dalej chronić aplikację usługi App Service przed zagrożeniami.

## <a name="https-and-certificates"></a>HTTPS i certyfikaty

Usługa App Service umożliwia zabezpieczenie aplikacji za pomocą [protokołu HTTPS](https://wikipedia.org/wiki/HTTPS). Po utworzeniu aplikacji jej domyślna\<nazwa domeny ( app_name>.azurewebsites.net) jest już dostępna przy użyciu protokołu HTTPS. Jeśli [skonfigurujesz domenę niestandardową dla aplikacji,](app-service-web-tutorial-custom-domain.md)należy ją również [zabezpieczyć certyfikatem TLS/SSL,](configure-ssl-bindings.md) aby przeglądarki klienckie mogły nawiązywać zabezpieczone połączenia HTTPS z domeną niestandardową. Usługa App Service obsługuje kilka typów certyfikatów:

- Bezpłatny certyfikat zarządzany usługi app service
- Certyfikat usługi app service
- Certyfikat innej firmy
- Certyfikat zaimportowany z usługi Azure Key Vault

Aby uzyskać więcej informacji, zobacz [Dodawanie certyfikatu TLS/SSL w usłudze Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protokoły niezabezpieczone (HTTP, TLS 1.0, FTP)

Aby zabezpieczyć aplikację przed wszystkimi połączeniami niezaszyfrowanymi (HTTP), usługa App Service udostępnia konfigurację jednym kliknięciem w celu wymuszenia protokołu HTTPS. Niezabezpieczone żądania są odrzucane, zanim jeszcze dotrą do kodu aplikacji. Aby uzyskać więcej informacji, zobacz [Wymuszanie protokołu HTTPS](configure-ssl-bindings.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 nie jest już uważany za bezpieczny według standardów branżowych, takich jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Usługa App Service umożliwia wyłączenie przestarzałych protokołów przez [wymuszanie protokołu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

Usługa App Service obsługuje zarówno ftp, jak i FTPS do wdrażania plików. Jednak ftps powinny być stosowane zamiast FTP, jeśli w ogóle jest to możliwe. Jeśli jeden lub oba z tych protokołów nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statyczne ograniczenia adresów IP

Domyślnie aplikacja usługi App Service akceptuje żądania ze wszystkich adresów IP z Internetu, ale można ograniczyć ten dostęp do małego podzbioru adresów IP. Usługa App Service w systemie Windows umożliwia zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać indywidualne adresy IP lub zakres adresów IP zdefiniowanych przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [Statyczne ograniczenia adresów IP usługi Azure App Service](app-service-ip-restrictions.md).

W usłudze App Service w systemie Windows można również dynamicznie ograniczać adresy IP, konfigurując _plik web.config_. Aby uzyskać więcej informacji, zobacz [Dynamic IP Security \<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja klienta

Usługa Azure App Service zapewnia uwierzytelnianie pod klucz i autoryzację użytkowników lub aplikacji klienckich. Po włączeniu może zalogować się użytkowników i aplikacji klienckich z małym lub żadnym kodem aplikacji. Możesz zaimplementować własne rozwiązanie uwierzytelniania i autoryzacji lub zezwolić usłudze App Service na obsługę go za Ciebie. Moduł uwierzytelniania i autoryzacji obsługuje żądania sieci web przed przekazaniem ich do kodu aplikacji i odrzuca nieautoryzowane żądania, zanim dotrą do kodu.

Uwierzytelnianie i autoryzacja usługi app service obsługuje wielu dostawców uwierzytelniania, w tym usługę Azure Active Directory, konta Microsoft, Facebook, Google i Twitter. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

Podczas uwierzytelniania w usłudze zaplecza usługa App Service udostępnia dwa różne mechanizmy w zależności od potrzeb:

- **Tożsamość usługi** — zaloguj się do zasobu zdalnego przy użyciu tożsamości samej aplikacji. Usługa App Service umożliwia łatwe tworzenie [tożsamości zarządzanej,](overview-managed-identity.md)której można używać do uwierzytelniania przy użyciu innych usług, takich jak [Usługa Azure SQL Database](/azure/sql-database/) lub Azure Key [Vault.](/azure/key-vault/) Aby zapoznać się z kompleksowym samouczkiem tego podejścia, zobacz [Bezpieczne połączenie usługi Azure SQL Database z usługi App Service przy użyciu tożsamości zarządzanej.](app-service-web-tutorial-connect-msi.md)
- **W imieniu (OBO)** — należy wprowadzić delegowany dostęp do zasobów zdalnych w imieniu użytkownika. Z usługi Azure Active Directory jako dostawcy uwierzytelniania, aplikacja usługi App Service można wykonać delegowane logowania do usługi zdalnej, takich jak [Microsoft Graph INTERFEJSU API](../active-directory/develop/microsoft-graph-intro.md) lub aplikacji zdalnego interfejsu API w usłudze app service. Aby zapoznać się z kompleksowym samouczkiem tego podejścia, zobacz [Uwierzytelnij i autoryzuj użytkowników end-to-end w usłudze Azure App Service.](app-service-web-tutorial-auth-aad.md)

## <a name="connectivity-to-remote-resources"></a>Łączność z zasobami zdalnymi

Istnieją trzy typy zasobów zdalnych, do których aplikacja może potrzebować dostępu: 

- [Zasoby platformy Azure](#azure-resources)
- [Zasoby wewnątrz sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network)
- [Zasoby lokalne](#on-premises-resources)

W każdym z tych przypadków usługa App Service umożliwia nawiązywać bezpieczne połączenia, ale nadal należy przestrzegać najlepszych rozwiązań w zakresie zabezpieczeń. Na przykład zawsze używaj połączeń szyfrowanych, nawet jeśli zasób zaplecza zezwala na połączenia niezaszyfrowane. Ponadto upewnij się, że usługa zaplecza platformy Azure zezwala na minimalny zestaw adresów IP. Wychodzące adresy IP aplikacji można znaleźć pod adresem [Przychodzące i wychodzące adresy IP w usłudze Azure App Service.](overview-inbound-outbound-ips.md)

### <a name="azure-resources"></a>Zasoby platformy Azure

Gdy aplikacja łączy się z zasobami platformy Azure, takimi jak [baza danych SQL](https://azure.microsoft.com/services/sql-database/) i usługa Azure [Storage,](/azure/storage/)połączenie pozostaje na platformie Azure i nie przekracza żadnych granic sieci. Jednak połączenie przechodzi przez sieć współużytkowaną na platformie Azure, więc zawsze upewnij się, że połączenie jest szyfrowane. 

Jeśli aplikacja jest hostowana w [środowisku usługi App Service,](environment/intro.md)należy [połączyć się z obsługiwanymi usługami platformy Azure przy użyciu punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Zasoby wewnątrz sieci wirtualnej platformy Azure

Aplikacja może uzyskiwać dostęp do zasobów w [sieci wirtualnej platformy Azure](/azure/virtual-network/) za pośrednictwem [integracji z siecią wirtualną.](web-sites-integrate-with-vnet.md) Integracja jest ustanawiana za pomocą sieci wirtualnej przy użyciu sieci VPN typu punkt-lokacja. Aplikacja może następnie uzyskać dostęp do zasobów w sieci wirtualnej przy użyciu ich prywatnych adresów IP. Jednak połączenie typu punkt-lokacja nadal przechodzi przez sieci udostępnione na platformie Azure. 

Aby całkowicie odizolować łączność zasobów od sieci udostępnionych na platformie Azure, utwórz aplikację w [środowisku usługi App Service](environment/intro.md). Ponieważ środowisko usługi app service jest zawsze wdrażane w dedykowanej sieci wirtualnej, łączność między aplikacją a zasobami w sieci wirtualnej jest w pełni odizolowana. Aby zapoznać się z innymi aspektami zabezpieczeń sieci w środowisku usługi app service, zobacz [Izolacja sieci](#network-isolation).

### <a name="on-premises-resources"></a>Zasoby lokalne

Można bezpiecznie uzyskać dostęp do zasobów lokalnych, takich jak bazy danych, na trzy sposoby: 

- [Połączenia hybrydowe](app-service-hybrid-connections.md) — ustanawia połączenie punkt-punkt z zasobem zdalnym za pośrednictwem tunelu TCP. Tunel TCP jest ustanawiany przy użyciu protokołu TLS 1.2 z kluczami sygnatury dostępu współdzielonego (SAS).
- [Integracja sieci wirtualnej](web-sites-integrate-with-vnet.md) z siecią VPN typu lokacja-lokacja — jak opisano w [obszarze Zasoby wewnątrz sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network), ale sieć wirtualna może być połączona z siecią lokalną za pośrednictwem sieci VPN typu [lokacja lokacja lokacja.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) W tej topologii sieci aplikacja może łączyć się z zasobami lokalnymi, takimi jak inne zasoby w sieci wirtualnej.
- [Środowisko usługi App Service](environment/intro.md) z siecią VPN typu lokacja lokacja — jak opisano w [obszarze Zasoby wewnątrz sieci wirtualnej platformy Azure](#resources-inside-an-azure-virtual-network), ale sieć wirtualna może być połączona z siecią lokalną za pośrednictwem sieci VPN typu [lokacja lokacja lokacja.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) W tej topologii sieci aplikacja może łączyć się z zasobami lokalnymi, takimi jak inne zasoby w sieci wirtualnej.

## <a name="application-secrets"></a>Tajemnice aplikacji

Nie przechowuj wpisów tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API i klucze prywatne w kodzie lub plikach konfiguracyjnych. Powszechnie akceptowane podejście jest dostęp do nich jako [zmienne środowiskowe](https://wikipedia.org/wiki/Environment_variable) przy użyciu wzorca standardowego w wybranym języku. W usłudze App Service sposobem definiowania zmiennych środowiskowych są [ustawienia aplikacji](configure-common.md#configure-app-settings) (a zwłaszcza w przypadku aplikacji .NET, [parametry połączenia).](configure-common.md#configure-connection-strings) Ustawienia aplikacji i parametry połączenia są przechowywane zaszyfrowane na platformie Azure i są odszyfrowywane tylko przed wstrzyknięciem do pamięci procesu aplikacji po uruchomieniu aplikacji. Klucze szyfrowania są regularnie obracane.

Alternatywnie można zintegrować aplikację usługi App Service z [usługą Azure Key Vault](/azure/key-vault/) w celu zaawansowanego zarządzania wpisami tajnymi. Uzyskując [dostęp do usługi Key Vault za pomocą tożsamości zarządzanej,](../key-vault/tutorial-web-application-keyvault.md)aplikacja usługi App Service może bezpiecznie uzyskiwać dostęp do potrzebnych wpisów tajnych.

## <a name="network-isolation"></a>Izolacja sieciowa

Z wyjątkiem warstwy cenowej **Izolowane** wszystkie warstwy uruchamiają aplikacje w udostępnionej infrastrukturze sieciowej w usłudze App Service. Na przykład publiczne adresy IP i moduły równoważenia obciążenia frontonu są współużytkowane innym dzierżawcom. Warstwa **Izolowane** zapewnia pełną izolację sieci, uruchamiając aplikacje w dedykowanym [środowisku usługi aplikacji.](environment/intro.md) Środowisko usługi aplikacji działa we własnym wystąpieniu [usługi Azure Virtual Network](/azure/virtual-network/). To pozwala: 

- Obsługa aplikacji za pośrednictwem dedykowanego publicznego punktu końcowego z dedykowanymi frontami.
- Obsługa aplikacji wewnętrznej przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB), który umożliwia dostęp tylko z wewnątrz sieci wirtualnej platformy Azure. Równoważenie obciążenia sieciowego ma adres IP z podsieci prywatnej, co zapewnia całkowitą izolację aplikacji z Internetu.
- [Użyj równoważenia obciążenia sieciowego za zaporą aplikacji sieci web (WAF).](environment/integrate-with-application-gateway.md) WAF oferuje ochronę na poziomie przedsiębiorstwa dla aplikacji publicznych, takich jak ochrona przed atakami DDoS, filtrowanie URI i zapobieganie iniekcji SQL.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do środowisk usługi Azure App Service](environment/intro.md). 
