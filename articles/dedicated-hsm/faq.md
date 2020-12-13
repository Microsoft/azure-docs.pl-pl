---
title: Często zadawane pytania — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Znajdź odpowiedzi na często zadawane pytania dotyczące modułu zabezpieczeń dedykowanego sprzętu platformy Azure, takich jak podstawowe informacje, współdziałanie, wysoka dostępność i pomoc techniczna.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: keithp
ms.openlocfilehash: 1f349931fb479be3b3ba936b3d0a665427357f29
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369042"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Znajdź odpowiedzi na często zadawane pytania dotyczące Microsoft Azure dedykowanego modułu HSM.

## <a name="the-basics"></a>Podstawowe informacje

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: co to jest sprzętowy moduł zabezpieczeń (HSM)?

Sprzętowy moduł zabezpieczeń (HSM) to fizyczne urządzenie obliczeniowe służące do zabezpieczania kluczy kryptograficznych i zarządzania nimi. Klucze przechowywane w sprzętowych modułów zabezpieczeń mogą być używane na potrzeby operacji kryptograficznych. Materiał klucza pozostaje bezpieczny w odpornych na uszkodzenia modułach sprzętowych. Moduł HSM zezwala tylko uwierzytelnionym i autoryzowanym aplikacjom na używanie kluczy. Materiał klucza nigdy nie pozostawia granicy ochrony modułu HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: co to jest oferta dedykowanego modułu HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to usługa oparta na chmurze, która udostępnia sprzętowych modułów zabezpieczeń hostowane w centrach danych platformy Azure, które są połączone bezpośrednio z siecią wirtualną klienta. Te sprzętowych modułów zabezpieczeń są dedykowanymi urządzeniami sieciowymi (firmy Thales Network Luna HSM 7). Są one wdrażane bezpośrednio w prywatnej przestrzeni adresów IP klientów, a firma Microsoft nie ma dostępu do funkcji kryptograficznych sprzętowych modułów zabezpieczeń. Tylko klient ma pełną kontrolę administracyjną i kryptograficzną na tych urządzeniach. Klienci są odpowiedzialni za zarządzanie urządzeniem i mogą uzyskać pełne dzienniki aktywności bezpośrednio z urządzeń. Dedykowany sprzętowych modułów zabezpieczeń pomagają klientom spełnić wymagania dotyczące zgodności/przepisów, takie jak FIPS 140-2 Level 3, HIPAA, PCI-DSS i eIDAS i wiele innych.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: jaki sprzęt jest używany do dedykowanego modułu HSM?

Firma Microsoft połączyła się z usługą firmy Thales w celu dostarczenia dedykowanej usługi modułu HSM platformy Azure. Określone urządzenie to [firmy Thales Network Luna HSM 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). To urządzenie nie tylko udostępnia zweryfikowane oprogramowanie układowe FIPS 140-2 Level 3, ale oferuje również małe opóźnienia, wysoką wydajność i wysoką pojemność za pośrednictwem 10 partycji. 

### <a name="q-what-is-an-hsm-used-for"></a>P: co to jest moduł HSM?

Sprzętowych modułów zabezpieczeń są używane do przechowywania kluczy kryptograficznych, które są używane na potrzeby funkcji kryptograficznych, takich jak TLS (Transport Layer Security), szyfrowanie danych, PKI (infrastruktura kluczy publicznych), DRM (Digital Rights Management) i podpisywanie dokumentów.

### <a name="q-how-does-dedicated-hsm-work"></a>P: jak działa dedykowany moduł HSM?

Klienci mogą inicjować obsługę sprzętowych modułów zabezpieczeń w określonych regionach przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Klient określa sieć wirtualną, z którą zostanie nawiązane połączenie sprzętowych modułów zabezpieczeń i po zainicjowaniu obsługi sprzętowych modułów zabezpieczeń będzie dostępna w wyznaczonej podsieci przy użyciu przypisanych adresów IP w prywatnej przestrzeni adresów IP klienta. Następnie klienci mogą łączyć się z usługą sprzętowych modułów zabezpieczeń za pomocą protokołu SSH w celu zarządzania urządzeniami i ich administrowania, konfigurowania połączeń klienta HSM, inicjowania sprzętowych modułów zabezpieczeń, tworzenia partycji, definiowania i przypisywania ról, takich jak oficer partycji, oficer kryptograficzny i użytkownik kryptograficzny. Następnie klient będzie używać firmy Thales dostarczonych narzędzi klienta/zestawu SDK/oprogramowania modułu HSM do wykonywania operacji kryptograficznych z ich aplikacji.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: jakie oprogramowanie jest dostarczane z dedykowaną usługą HSM?

Firmy Thales udostępnia wszystkie programy dla urządzenia HSM po udostępnieniu go firmie Microsoft. Oprogramowanie jest dostępne w [portalu obsługi klienta firmy Thales](https://supportportal.gemalto.com/csm/). Klienci korzystający z dedykowanej usługi HSM muszą zostać zarejestrowani do obsługi firmy Thales i mają identyfikator klienta, który umożliwia dostęp i pobieranie odpowiedniego oprogramowania. Obsługiwane oprogramowanie klienckie jest w wersji 7,2, która jest zgodna z zweryfikowaną wersją oprogramowania układowego FIPS 140-2 Level 3 7.0.3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>P: Jakie dodatkowe koszty mogą być naliczane za pomocą dedykowanej usługi HSM?

Przy użyciu dedykowanej usługi HSM będą naliczane dodatkowe koszty. 
* Korzystanie z dedykowanego lokalnego urządzenia kopii zapasowej jest możliwe w przypadku dedykowanej usługi HSM, jednak spowoduje to naliczenie dodatkowego kosztu i powinno być bezpośrednio źródłem z firmy Thales.
* Dedykowany moduł HSM jest dostarczany z licencją 10 partycji. Jeśli klient wymaga więcej partycji, wiąże się to z dodatkowymi kosztami dla dodatkowych licencji pochodzących bezpośrednio z usługi firmy Thales.
* Dedykowany moduł HSM wymaga infrastruktury sieciowej (VNET, VPN Gateway itp.) i zasobów, takich jak maszyny wirtualne na potrzeby konfiguracji urządzeń. Te dodatkowe zasoby będą naliczane dodatkowe koszty i nie są uwzględniane w cenach dedykowanych usługi HSM.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: czy dedykowany moduł HSM platformy Azure oferuje uwierzytelnianie oparte na hasłach i PED?

W tej chwili dedykowany moduł HSM platformy Azure udostępnia tylko sprzętowych modułów zabezpieczeń z uwierzytelnianiem opartym na hasłach.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: czy usługa Azure dedykowany Host HSM sprzętowych modułów zabezpieczeń dla mnie?

Firma Microsoft oferuje tylko firmy Thales Network Luna HSM 7 za pośrednictwem dedykowanej usługi HSM i nie może hostować żadnych urządzeń udostępnianych przez klienta.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: czy dedykowany moduł HSM platformy Azure obsługuje funkcje płatności (PIN/EFT)?

Dedykowana usługa HSM platformy Azure używa urządzeń firmy Thales Network Luna HSM 7. Te urządzenia nie obsługują funkcji specyficznych dla modułu HSM (na przykład numeru PIN lub EFT) ani certyfikatów. Jeśli chcesz, aby usługa dedykowanego modułu HSM platformy Azure obsługiwała sprzętowych modułów zabezpieczeń płatności w przyszłości, Przekaż opinię do przedstawiciela konta Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: które regiony platformy Azure są dostępne w ramach dedykowanego modułu HSM?

Począwszy od dnia 2019 marca, dedykowany moduł HSM jest dostępny w 14 regionach wymienionych poniżej. Dalsze regiony są planowane i można je omówić za pośrednictwem przedstawiciela konta Microsoft.

* East US
* Wschodnie stany USA 2
* Zachodnie stany USA
* Zachodnie stany USA 2
* South Central US
* Southeast Asia
* Azja Wschodnia
* Indie Środkowe
* Indie Południowe
* Japonia Wschodnia
* Japonia Zachodnia
* Europa Północna
* West Europe
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia
* Szwajcaria Północna
* Szwajcaria Zachodnia
* US Gov Wirginia
* US Gov Teksas

## <a name="interoperability"></a>Współdziałanie

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: jak moja aplikacja nawiązuje połączenie z dedykowanym modułem HSM?

Do wykonywania operacji kryptograficznych z aplikacji należy używać firmy Thales dostarczonych/SDK/oprogramowania klienta modułu HSM. Oprogramowanie jest dostępne w [portalu obsługi klienta firmy Thales](https://supportportal.gemalto.com/csm/). Klienci korzystający z dedykowanej usługi HSM muszą zostać zarejestrowani do obsługi firmy Thales i mają identyfikator klienta, który umożliwia dostęp i pobieranie odpowiedniego oprogramowania.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: czy aplikacja może połączyć się z dedykowanym modułem HSM z innej sieci wirtualnej w regionie lub w różnych regionach?

Tak, aby nawiązać łączność między sieciami wirtualnymi, należy użyć [komunikacji równorzędnej](../virtual-network/virtual-network-peering-overview.md) sieci wirtualnych w obrębie regionu. W przypadku łączności między regionami należy użyć [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Czy można synchronizować dedykowany moduł HSM z lokalnym sprzętowych modułów zabezpieczeń?

Tak, możesz synchronizować lokalne sprzętowych modułów zabezpieczeń z dedykowanym modułem HSM. Połączenie [sieci VPN typu punkt-punkt lub punkt-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md) może służyć do nawiązywania połączeń z siecią lokalną.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Czy można szyfrować dane używane przez inne usługi platformy Azure przy użyciu kluczy przechowywanych w dedykowanym module HSM?

Nie. Dedykowany sprzętowych modułów zabezpieczeń platformy Azure są dostępne tylko w sieci wirtualnej.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Czy można zaimportować klucze z istniejącego lokalnego modułu HSM do dedykowanego modułu HSM?

Tak, jeśli masz lokalne firmy Thales Network Luna HSM 7 sprzętowych modułów zabezpieczeń. Istnieje wiele metod. Zapoznaj się z [dokumentacją modułu HSM firmy Thales](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: jakie systemy operacyjne są obsługiwane przez dedykowane oprogramowanie klienckie modułu HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Wirtualne: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Jak mogę skonfigurować moją aplikację kliencką w celu utworzenia konfiguracji o wysokiej dostępności z wieloma partycjami z wielu sprzętowych modułów zabezpieczeń?

Aby zapewnić wysoką dostępność, należy skonfigurować konfigurację aplikacji klienta HSM do używania partycji z każdego modułu HSM. Zapoznaj się z dokumentacją oprogramowania klienckiego modułu HSM firmy Thales.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: jakie mechanizmy uwierzytelniania są obsługiwane przez dedykowany moduł HSM?

Dedykowany moduł HSM platformy Azure używa urządzeń SafeNet Network HSM 7 (model A790) i obsługują uwierzytelnianie oparte na hasłach.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: jakie zestawy SDK, interfejsy API i oprogramowanie klienckie są dostępne do użycia w dedykowanym module HSM?

PKCS # 11, Java (JCA/JCE), Microsoft CAPI i CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Czy można zaimportować/zmigrować klucze z Luna 5/6 sprzętowych modułów zabezpieczeń do platformy Azure dedykowanej sprzętowych modułów zabezpieczeń?

Tak. Skontaktuj się z przedstawicielem usługi firmy Thales, aby zapoznać się z odpowiednim przewodnikiem migracji firmy Thales. 

## <a name="using-your-hsm"></a>Korzystanie z modułu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Jak mogę decydować, czy używać Azure Key Vault czy dedykowanego modułu HSM platformy Azure?

Dedykowany moduł HSM platformy Azure to odpowiedni wybór dla przedsiębiorstw przeprowadzających migrację do aplikacji lokalnych platformy Azure korzystających z sprzętowych modułów zabezpieczeń. Dedykowany sprzętowych modułów zabezpieczeń to opcja migracji aplikacji z minimalnymi zmianami. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działającym na maszynie wirtualnej lub w aplikacji sieci Web platformy Azure, mogą korzystać z dedykowanego modułu HSM. Ogólnie rzecz biorąc, oprogramowanie pakowane w systemie IaaS (infrastruktura jako usługa), które obsługuje sprzętowych modułów zabezpieczeń jako magazyn kluczy, może korzystać z dedykowanego modułu HSM, takie jak Brama aplikacji lub Usługa Traffic Manager dla kluczy o kluczowym obsłudze TLS, ADCS (Active Directory certyfikatów) lub podobne narzędzia infrastruktury PKI, narzędzia/aplikacje służące do podpisywania dokumentów, podpisywania kodu lub SQL Server (IaaS) skonfigurowane z TDE (przezroczystą szyfrowanie bazy danych) z kluczem głównym w module HSM przy użyciu dostawcy EKM (Extensible Key Management). Azure Key Vault jest odpowiednie dla aplikacji w chmurze lub do szyfrowania w środowiskach REST, w których dane klienta są przetwarzane przez PaaS (platforma jako usługa) lub SaaS (oprogramowanie jako usługa), takie jak klucz klienta pakietu Office 365, Azure Information Protection, Azure Disk Encryption Azure Data Lake Store szyfrowanie z kluczem zarządzanym przez klienta, szyfrowanie usługi Azure Storage za pomocą klucza zarządzanego przez klienta oraz usługę Azure SQL z kluczem zarządzanym przez klienta.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: jakie scenariusze użycia najlepiej pasują do dedykowanego modułu HSM platformy Azure?

Dedykowany moduł HSM platformy Azure jest najbardziej odpowiedni dla scenariuszy migracji. Oznacza to, że w przypadku migrowania aplikacji lokalnych na platformę Azure, która już korzysta z usługi sprzętowych modułów zabezpieczeń. Zapewnia to niską opcję migracji na platformę Azure przy minimalnych zmianach w aplikacji. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działającym na maszynie wirtualnej platformy Azure lub w aplikacji sieci Web, można użyć dedykowanego modułu HSM. Ogólnie rzecz biorąc, aplikacje opakowane w sposób pozostały działające w modelach IaaS (infrastruktura jako usługa), które obsługują sprzętowych modułów zabezpieczeń jako magazyn kluczy mogą korzystać z dedykowanego modułu HSM, takiego jak:

* Application Gateway lub Traffic Manager dla protokołów szyfrowania TLS
* ADCS (Active Directory usługi certyfikatów)
* Podobne narzędzia infrastruktury PKI
* Narzędzia/aplikacje używane do podpisywania dokumentów
* Podpisywanie kodu
* SQL Server (IaaS) skonfigurowany z TDE (przezroczystą szyfrowanie bazy danych) z kluczem głównym w module HSM przy użyciu dostawcy EKM (Extensible Key Management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: można używać dedykowanego modułu HSM z kluczem klienta pakietu Office 365, Azure Information Protection, Azure Data Lake Store, szyfrowania dysku, szyfrowania usługi Azure Storage i Azure SQL TDE?

Nie. Dedykowany moduł HSM jest udostępniany bezpośrednio w prywatnej przestrzeni adresów IP klienta, dlatego nie jest on dostępny przez inne platformy Azure ani usługi firmy Microsoft.

## <a name="administration-access-and-control"></a>Administracja, dostęp i kontrola

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>P: czy klient uzyskuje pełną kontrolę wyłączną na sprzętowych modułów zabezpieczeń za pomocą dedykowanego "?

Tak. Każde urządzenie HSM jest w pełni przeznaczone dla jednego klienta, a nikt nie ma kontroli administracyjnej po zainicjowaniu aprowizacji i zmianie hasła administratora.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: jaki poziom dostępu ma firma Microsoft dla mojego modułu HSM?

Firma Microsoft nie ma żadnej kontroli administracyjnej ani kryptograficznej nad modułem HSM. Firma Microsoft ma dostęp do poziomu monitora za pośrednictwem połączenia portu szeregowego w celu pobrania podstawowych danych telemetrycznych, takich jak temperatura i kondycja składnika. Dzięki temu firma Microsoft może udostępniać aktywne powiadomienia o problemach z kondycją. W razie potrzeby klient może wyłączyć to konto.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>P: co to jest konto "Administrator dzierżawy", które jest używane przez firmę Microsoft, chcę, aby użytkownik administratora miał wartość "admin" na firmy Thales Luna sprzętowych modułów zabezpieczeń?

Urządzenie HSM jest dostarczane z domyślnym użytkownikiem administratora z normalnym hasłem domyślnym. Firma Microsoft nie chciała używać domyślnych haseł, gdy wszystkie urządzenia znajdują się w puli oczekującej na zainicjowanie obsługi przez klientów. Nie spełnia to rygorystycznych wymagań w zakresie zabezpieczeń. Z tego powodu ustawimy silne hasło, które jest odrzucane w czasie aprowizacji. Ponadto podczas aprowizacji należy utworzyć nowego użytkownika w roli administratora o nazwie "Administrator dzierżawy". Ten użytkownik ma domyślne hasło i klienci zmieniają ten sposób jako pierwszą akcję podczas pierwszego logowania na nowo zainicjowanym urządzeniu. Ten proces zapewnia wysoki poziom bezpieczeństwa i utrzymuje nasze obietnice związane z wyłączną kontrolą administracyjną dla naszych klientów. Należy zauważyć, że użytkownik "Administrator dzierżawy" może być używany do resetowania hasła użytkownika administratora, jeśli klient woli korzystać z tego konta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: czy w dedykowanym module HSM można korzystać z firmy Microsoft lub osób z kluczami dostępu firmy Microsoft?

Nie. Firma Microsoft nie ma dostępu do kluczy przechowywanych w dedykowanym module HSM przydzielonym przez klienta.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Czy mogę uaktualnić oprogramowanie/oprogramowanie układowe na sprzętowych modułów zabezpieczeń przydzielonym do mnie?

Klient ma pełną kontrolę administracyjną, w tym uaktualnienie oprogramowania/oprogramowania układowego, jeśli określone funkcje są wymagane przez różne wersje oprogramowania układowego. Przed wprowadzeniem zmian zapoznaj się z firmą Microsoft w sprawie Twojego uaktualnienia, kontaktując się z firmą HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Jak mogę zarządzać dedykowanym modułem HSM?

Można zarządzać dedykowanymi sprzętowych modułów zabezpieczeńami, uzyskując dostęp do nich przy użyciu protokołu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Jak mogę zarządzać partycjami w dedykowanym module HSM?

Oprogramowanie klienckie firmy Thales HSM służy do zarządzania sprzętowych modułów zabezpieczeń i partycjami.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Jak mogę Monitoruj mój moduł HSM?

Klient ma pełny dostęp do dzienników aktywności modułu HSM za pośrednictwem dziennika systemowego i protokołu SNMP. Klient będzie musiał skonfigurować serwer dziennika systemu lub serwer SNMP w celu odbierania dzienników lub zdarzeń z sprzętowych modułów zabezpieczeń.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Czy można uzyskać pełny dziennik dostępu wszystkich operacji modułu HSM z dedykowanego modułu HSM?

Tak. Dzienniki z urządzenia HSM można wysyłać do serwera dziennika systemowego

## <a name="high-availability"></a>Wysoka dostępność

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: czy jest możliwe skonfigurowanie wysokiej dostępności w tym samym regionie lub w wielu regionach?

Tak. Konfiguracja wysokiej dostępności i konfiguracja odbywają się w oprogramowaniu klienckim modułu HSM udostępnianym przez firmy Thales. Sprzętowych modułów zabezpieczeń z tej samej sieci wirtualnej lub innych sieci wirtualnych w tym samym regionie lub w różnych regionach lub lokalnych sprzętowych modułów zabezpieczeń podłączonych do sieci wirtualnej przy użyciu sieci VPN typu lokacja-lokacja lub punktu typu punkt-punkt można dodać do tej samej konfiguracji wysokiej dostępności. Należy zauważyć, że to synchronizuje tylko materiał klucza i nie konkretne elementy konfiguracji, takie jak role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Czy można dodać sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu dedykowanego modułu HSM platformy Azure?

Tak. Muszą spełniać wymagania dotyczące wysokiej dostępności dla SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Czy można dodać Luna 5/6 sprzętowych modułów zabezpieczeń z sieci lokalnych do grupy o wysokiej dostępności z dedykowanym modułem HSM platformy Azure?

Nie.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: ile sprzętowych modułów zabezpieczeń można dodać do tej samej konfiguracji wysokiej dostępności z jednej pojedynczej aplikacji?

16 Członkowie grupy HA mają niezrównane testy z pełną przepustowością.

## <a name="support"></a>Pomoc techniczna

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: Jaka jest umowa SLA dla dedykowanej usługi HSM?

Dla dedykowanej usługi HSM nie jest dostępna żadna konkretna gwarancja czasu przestoju. Firma Microsoft zapewnia dostęp na poziomie sieci do urządzenia, a tym samym stosuje się standardowe umowy SLA sieci platformy Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: w jaki sposób sprzętowych modułów zabezpieczeń jest używany w dedykowanym module HSM platformy Azure?

Centra danych platformy Azure mają rozległe i proceduralne mechanizmy kontroli zabezpieczeń. Oprócz tego dedykowanych sprzętowych modułów zabezpieczeń są hostowane w bardziej ograniczonym obszarze dostępu centrum danych. Te obszary mają dodatkowe fizyczne mechanizmy kontroli dostępu i kamery wideo w celu zwiększenia bezpieczeństwa.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: co się stanie w przypadku naruszenia zabezpieczeń lub zdarzenia naruszenia sprzętu?

Dedykowana usługa HSM używa urządzeń firmy Thales Network Luna HSM 7. Te urządzenia obsługują wykrywanie włamania fizycznego i logicznego. Jeśli kiedykolwiek dojdzie do naruszenia zdarzenia, sprzętowych modułów zabezpieczeń są automatycznie zerowane.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Jak mogę upewnij się, że klucze w sprzętowych modułów zabezpieczeń dedykowane nie zostaną utracone z powodu błędu lub złośliwego ataku z niejawnym testerem?

Zdecydowanie zaleca się używanie lokalnego urządzenia do tworzenia kopii zapasowych modułu HSM do regularnego wykonywania kopii zapasowej sprzętowych modułów zabezpieczeń na potrzeby odzyskiwania po awarii. Konieczne będzie użycie połączenia sieci VPN typu peer-to-peer lub typu lokacja-lokacja z lokalną stacją roboczą podłączoną do urządzenia z kopią zapasową modułu HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Jak mogę uzyskać pomoc techniczną dla dedykowanego modułu HSM?

Pomoc techniczna jest świadczona przez firmę Microsoft i firmy Thales.  Jeśli masz problem z dostępem do sprzętu lub sieci, Zgłoś żądanie pomocy technicznej w firmie Microsoft i jeśli masz problem z konfiguracją modułu HSM, oprogramowaniem i programowaniem aplikacji, Zgłoś żądanie pomocy technicznej z firmy Thales. Jeśli masz nieokreślony problem, Zgłoś żądanie pomocy technicznej z firmą Microsoft, a następnie firmy Thales je w razie potrzeby. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-network-luna-hsm-7"></a>P: Jak mogę uzyskać oprogramowania klienckiego, dokumentacji i dostępu do wskazówek dotyczących integracji dla firmy Thales Network Luna HSM 7?

Po zarejestrowaniu się w usłudze zostanie dostarczony identyfikator firmy Thales klienta, który umożliwia rejestrację w portalu obsługi klienta firmy Thales. Umożliwi to dostęp do całego oprogramowania i dokumentacji oraz włączanie żądań pomocy technicznej bezpośrednio z firmy Thales.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Jeśli znaleziono lukę w zabezpieczeniach, a poprawka jest wydana przez firmy Thales, która jest odpowiedzialna za uaktualnianie/poprawianie systemu operacyjnego/oprogramowania układowego?

Firma Microsoft nie ma możliwości łączenia się z usługą sprzętowych modułów zabezpieczeń przydzieloną klientom. Klienci muszą uaktualnić i poprawić ich sprzętowych modułów zabezpieczeń.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: co zrobić, jeśli chcę uruchomić ponownie moduł HSM?

Moduł HSM ma zainstalowaną opcję ponownego uruchamiania wiersza polecenia, jednak występują problemy z tym, że ponowne uruchomienie komputera nie odpowiada sporadycznie i z tego powodu zaleca się ponowne uruchomienie, aby zgłosić żądanie pomocy technicznej z firmą Microsoft w celu fizycznego ponownego uruchomienia urządzenia. 

## <a name="cryptography-and-standards"></a>Kryptografia i standardy

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: Czy można bezpiecznie przechowywać klucze szyfrowania dla najważniejszych danych w dedykowanym module HSM?

Tak, dedykowane zasady HSM firmy Thales Network Luna modułu HSM 7, które korzystają z zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: jakie klucze kryptograficzne i algorytmy są obsługiwane przez dedykowany moduł HSM?

Dedykowane zasady usługi HSM firmy Thales Network Luna HSM 7. Obsługują one szeroką gamę typów kluczy kryptograficznych i algorytmy, w tym: Pełna obsługa Suite B

* Szyfrowanie
  * RSA
  * DSA
  * Diffie-Hellman
  * Krzywa eliptyczna
  * Kryptografia (ECDSA, ECDH, Ed25519, ECIES) z nazwanymi, zdefiniowanymi przez użytkownika i Brainpool krzywych, KCDSA
* Symetrycznego
  * AES — GCM
  * Potrójny algorytm DES
  * DES
  * ARIA, INICJATOR
  * WERSJI
  * RC4
  * RC5
  * CAST
  * Skrót/skrót komunikatu/HMAC: SHA-1, SHA-2, SM3
  * Kluczowe wyprowadzanie: tryb licznika SP 800-108
  * Zawijanie kluczy: SP 800-38F
  * Generowanie liczb losowych: FIPS 140-2 zatwierdzone DRBG (tryb SP 800-90), zgodne z BSI DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: czy dedykowany poziom modułu HSM FIPS 140-2 na poziomie 3 został sprawdzony?

Tak. Dedykowane zasady usługi HSM firmy Thales Network Luna modułu HSM 7, które korzystają z zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: co należy zrobić, aby upewnić się, że działa dedykowany moduł HSM w trybie sprawdzania poprawności poziomu 3 FIPS 140-2?

Dedykowane usługi HSM mają postanowienia firmy Thales Network Luna HSM 7. Te urządzenia korzystają z zweryfikowanych sprzętowych modułów zabezpieczeń poziomu 3 FIPS 140-2. Domyślna wdrożona konfiguracja, system operacyjny i oprogramowanie układowe są również zweryfikowane w trybie FIPS. Nie trzeba podejmować żadnych działań w przypadku zgodności ze standardem FIPS 140-2 Level 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: jak klient zapewnia, że gdy moduł HSM jest niezainicjowany, cały materiał klucza jest wyczyszczony?

Przed zażądaniem anulowania aprowizacji klient musi mieć zero modułów HSM przy użyciu firmy Thales dostarczonych narzędzi klienta modułu HSM.

## <a name="performance-and-scale"></a>Wydajność i skalowanie

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: ile operacji kryptograficznych jest obsługiwanych na sekundę z dedykowanym modułem HSM?

Dedykowane postanowienia modułu HSM firmy Thales Network Luna HSM 7 sprzętowych modułów zabezpieczeń. Oto podsumowanie maksymalnej wydajności niektórych operacji: 

* RSA-2048:10 000 transakcji na sekundę
* P256 ECC: 20 000 transakcji na sekundę
* AES-GCM: 17 000 transakcji na sekundę

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: ile partycji można utworzyć w dedykowanym module HSM?

A790 użyty model SafeNet Luna HSM 7 obejmuje licencję na 10 partycji w kosztach usługi. Na urządzeniu obowiązuje limit 100 partycji i dodanie partycji do tego limitu spowoduje policzenie dodatkowych kosztów licencjonowania i wymaga instalacji nowego pliku licencji na urządzeniu.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: ile kluczy może być obsługiwanych w dedykowanym module HSM?

Maksymalna liczba kluczy jest funkcją dostępnej pamięci. A790 używany model SafeNet Luna 7 ma 32MB pamięci. Poniższe liczby są również stosowane do par kluczy, jeśli są używane klucze asymetryczne.

* RSA-2048-19 000
* ECC-P256-91 000

Pojemność zależy od określonych atrybutów klucza ustawionych w szablonie generowania klucza i liczby partycji.
