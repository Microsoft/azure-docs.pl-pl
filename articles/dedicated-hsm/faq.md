---
title: Często zadawane pytania — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące różnych tematów w dedykowanym modułie HSM platformy Azure
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9e759372f01d2a1b48562aef2bace1e8435a67
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683326"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Znajdź odpowiedzi na często zadawane pytania dotyczące dedykowanego modułu HSM platformy Microsoft Azure.

## <a name="the-basics"></a>Podstawy

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Pyt.: Co to jest sprzętowy moduł zabezpieczeń (HSM)?

Sprzętowy moduł zabezpieczeń (HSM) to fizyczne urządzenie komputerowe służące do ochrony kluczy kryptograficznych i zarządzania nimi. Klucze przechowywane w modułach HSM mogą być używane do operacji kryptograficznych. Kluczowy materiał pozostaje bezpiecznie w odpornych na manipulacje, odpornych na manipulacje modułów sprzętowych. Moduł HSM zezwala tylko uwierzytelnionym i autoryzowanym aplikacjom na używanie kluczy. Materiał klucza nigdy nie opuszcza granicy ochrony modułu HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Pyt.: Co to jest oferta usługi Azure Dedykowany moduł HSM?

Dedykowany moduł HSM platformy Azure to usługa oparta na chmurze, która zapewnia moduły HSM hostowane w centrach danych platformy Azure, które są bezpośrednio połączone z siecią wirtualną klienta. Te moduły HSM są dedykowanymi urządzeniami sieciowymi (SafeNet Network FIRMY Gemalto HSM 7 Model A790). Są one wdrażane bezpośrednio w prywatnej przestrzeni adresów IP klientów, a firma Microsoft nie ma dostępu do funkcji kryptograficznych modułów HSM. Tylko klient ma pełną kontrolę administracyjną i kryptograficzną nad tymi urządzeniami. Klienci są odpowiedzialni za zarządzanie urządzeniem i mogą uzyskać pełne dzienniki aktywności bezpośrednio ze swoich urządzeń. Dedykowane moduły HSM pomagają klientom spełnić wymagania dotyczące zgodności/przepisów, takie jak FIPS 140-2 Level 3, HIPAA, PCI-DSS i eIDAS i wiele innych.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Pyt.: Jaki sprzęt jest używany do dedykowanego modułu HSM?

Firma Microsoft nawiązała współpracę z firmą Gemalto w celu dostarczenia usługi dedykowanego modułu HSM platformy Azure. Szczególnym urządzeniem jest [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). To urządzenie nie tylko zapewnia oprogramowanie układowe FIPS 140-2 Level 3, ale także oferuje małe opóźnienia, wysoką wydajność i wysoką pojemność za pośrednictwem partycji 10. 

### <a name="q-what-is-an-hsm-used-for"></a>Pyt.: Do czego służy moduł HSM?

Moduły HSM są używane do przechowywania kluczy kryptograficznych, które są używane do funkcji kryptograficznych, takich jak TLS (zabezpieczenia warstw transportowych), szyfrowanie danych, infrastruktura PKI (infrastruktura kluczy publicznych), DRM (zarządzanie prawami cyfrowymi) i podpisywanie dokumentów.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Jak działa dedykowany moduł HSM?

Klienci mogą aprowizować moduły HSM w określonych regionach przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Klient określa, do jakiej sieci wirtualnej będą połączone moduły HSM, a po zainicjowaniu obsługi administracyjnej moduły HSM będą dostępne w wyznaczonej podsieci przy przypisanych adresach IP w prywatnej przestrzeni adresów IP klienta. Następnie klienci mogą łączyć się z modułami HSM przy użyciu funkcji SSH do zarządzania urządzeniami i administrowania nimi, konfigurowania połączeń klientów HSM, inicjowania modułów HSM, tworzenia partycji, definiowania i przypisywania ról, takich jak oficer partycji, oficer kryptograficzny i użytkownik kryptograficzny. Następnie klient użyje Gemalto dostarczone narzędzia klienta HSM / SDK / oprogramowanie do wykonywania operacji kryptograficznych z ich aplikacji.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Jakie oprogramowanie jest dostarczane z dedykowaną usługą HSM?

Gemalto dostarcza całe oprogramowanie dla urządzenia HSM po zainicjowaniu przez firmę Microsoft. Oprogramowanie jest dostępne na [portalu obsługi klienta Gemalto.](https://supportportal.gemalto.com/csm/) Klienci korzystający z dedykowanej usługi HSM muszą być zarejestrowani w celu uzyskania pomocy technicznej firmy Gemalto i mieć identyfikator klienta, który umożliwia dostęp do odpowiedniego oprogramowania i pobieranie go. Obsługiwane oprogramowanie klienckie jest w wersji 7.2, która jest zgodna z fips 140-2 Poziom 3 zatwierdzone oprogramowanie układowe w wersji 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Pyt.: Czy dedykowany moduł HSM platformy Azure oferuje uwierzytelnianie oparte na hasłach i PED?

W tej chwili dedykowany moduł HSM platformy Azure zapewnia tylko modułom HSM uwierzytelnianie oparte na hasłach.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Pyt.: Czy dedykowany moduł HSM platformy Azure będzie hostował moje moduły HSM?

Firma Microsoft oferuje tylko moduł HSM sieci Gemalto SafeNet Luna za pośrednictwem dedykowanej usługi HSM i nie może obsługiwać żadnych urządzeń dostarczonych przez klienta.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>Pyt.: Czy usługa Azure Dedicated HSM obsługuje funkcje płatności (PIN/EFT)?

Usługa dedykowanego modułu HSM platformy Azure korzysta z urządzeń SafeNet Luna Network HSM 7 (model A790). Urządzenia te nie obsługują funkcji specyficznych dla hsm płatności (takich jak PIN lub EFT) lub certyfikatów. Jeśli chcesz, aby usługa dedykowanego modułu HSM platformy Azure obsługiwała moduły HSM płatności w przyszłości, przekaż opinię przedstawicielowi konta Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Pyt.: Które regiony platformy Azure jest dedykowany moduł HSM dostępne w?

Od końca marca 2019 r. dedykowany moduł HSM jest dostępny w 14 regionach wymienionych poniżej. Dalsze regiony są planowane i można je omówić za pośrednictwem przedstawiciela konta Microsoft.

* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Zachodnie stany USA 2
* Południowo-środkowe stany USA
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Indie Środkowe
* Indie Południowe
* Japonia Wschodnia
* Japonia Zachodnia
* Europa Północna
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia
* Szwajcaria Północna
* Szwajcaria Zachód
* US Gov Wirginia
* US Gov Teksas

## <a name="interoperability"></a>Współdziałanie

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Pyt.: W jaki sposób moja aplikacja łączy się z dedykowanym modułem HSM?

Do wykonywania operacji kryptograficznych z aplikacji można użyć narzędzia klienta HSM/SDK/software firmy Gemalto. Oprogramowanie jest dostępne na [portalu obsługi klienta Gemalto.](https://supportportal.gemalto.com/csm/) Klienci korzystający z dedykowanej usługi HSM muszą być zarejestrowani w celu uzyskania pomocy technicznej firmy Gemalto i mieć identyfikator klienta, który umożliwia dostęp do odpowiedniego oprogramowania i pobieranie go.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Pyt.: Czy aplikacja może łączyć się z dedykowanym modułem HSM z innej sieci wirtualnej w regionach lub między regionami?

Tak, należy użyć [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) w regionie, aby ustanowić łączność między sieciami wirtualnymi. W przypadku łączności międzyregionowej należy użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Pyt.: Czy można zsynchronizować dedykowany moduł HSM z lokalnymi modułami HSM?

Tak, lokalne moduły HSM można synchronizować z dedykowanym modułem HSM. [Sieć VPN typu punkt-punkt lub łączność typu punkt-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md) może służyć do nawiązywać łączność z siecią lokalną.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Pyt.: Czy można szyfrować dane używane przez inne usługi platformy Azure przy użyciu kluczy przechowywanych w dedykowanym modułie HSM?

Nie. Dedykowane moduły HSM platformy Azure są dostępne tylko z sieci wirtualnej.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Pyt.: Czy można zaimportować klucze z istniejącego lokalnego modułu HSM do dedykowanego modułu HSM?

Tak, jeśli masz lokalne moduły HSM Gemalto SafeNet. Istnieje wiele metod. Zapoznaj się z dokumentacją modułu HSM gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Pyt.: Jakie systemy operacyjne są obsługiwane przez dedykowane oprogramowanie klienckie HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Wirtualny: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Pyt.: Jak skonfigurować aplikację kliencką, aby utworzyć konfigurację wysokiej dostępności z wieloma partycjami z wielu modułów HSM?

Aby mieć wysoką dostępność, należy skonfigurować konfigurację aplikacji klienckiej modułu HSM, aby używać partycji z każdego modułu HSM. Zapoznaj się z dokumentacją oprogramowania klienta modułu HSM firmy Gemalto.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Pyt.: Jakie mechanizmy uwierzytelniania są obsługiwane przez dedykowany moduł HSM?

Dedykowany moduł HSM platformy Azure używa urządzeń HSM 7 safenet network (model A790) i obsługuje uwierzytelnianie oparte na hasłach.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Pyt.: Jakie pakiety SDK, interfejsy API, oprogramowanie klienckie jest dostępne do użycia z dedykowanym modułem HSM?

PKCS#11, Java (JCA/JCE), Microsoft CAPI i CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Pyt.: Czy mogę importować/migrować klucze z modułów HSM 5/6 Luna do dedykowanych modułów HSM platformy Azure?

Tak. Zapoznaj się z przewodnikiem po migracji Gemalto. 

## <a name="using-your-hsm"></a>Korzystanie z modułu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Pyt.: Jak zdecydować, czy używać usługi Azure Key Vault lub azure dedykowany moduł HSM?

Dedykowany moduł HSM platformy Azure jest odpowiednim wyborem dla przedsiębiorstw migrujących do lokalnych aplikacji platformy Azure korzystających z modułów HSM. Dedykowane moduły HSM stanowią opcję migracji aplikacji z minimalnymi zmianami. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji uruchomionej w maszynie wirtualnej platformy Azure lub aplikacji sieci Web, mogą używać dedykowanego modułu HSM. Ogólnie rzecz biorąc, oprogramowanie opakowane termokurczliwe działające w modelach IaaS (infrastruktura jako usługa), które obsługują moduły HSM jako magazyn kluczy, mogą używać funkcji Dedykacyjny moduł HSM, takich jak brama aplikacji lub menedżer ruchu dla bezkluczowych protokołów TLS, ADCS (Usługi certyfikatów Active Directory) lub podobne narzędzia infrastruktury kluczy publicznych, narzędzia/aplikacje używane do podpisywania dokumentów, podpisywania kodu lub programu SQL Server (IaaS) skonfigurowane z TDE (szyfrowanie przezroczystej bazy danych) z kluczem głównym w systemie HSM przy użyciu dostawcy zarządzania kluczami EKM (rozszerzalny klucz). Usługa Azure Key Vault jest odpowiednia dla aplikacji typu "urodzony w chmurze" lub do szyfrowania w spoczynku scenariuszy, w których dane klienta są przetwarzane przez PaaS (platforma jako usługa) lub SaaS (Oprogramowanie jako usługa), takie jak klucz klienta usługi Office 365, ochrona informacji platformy Azure, szyfrowanie dysków azure, szyfrowanie usługi Azure Data Lake Store z kluczem zarządzanym przez klienta, szyfrowanie usługi Azure Storage z kluczem zarządzanym przez klienta i azure SQL z kluczem zarządzanym przez klienta.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Pyt.: Jakie scenariusze użycia najlepiej pasują do dedykowanego modułu HSM platformy Azure?

Dedykowany moduł HSM platformy Azure jest najbardziej odpowiedni dla scenariuszy migracji. Oznacza to, że w przypadku migracji lokalnych aplikacji na platformę Azure, które już korzystają z modułów HSM. Zapewnia to opcję o niskim tarciu do migracji na platformę Azure przy minimalnych zmianach w aplikacji. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji uruchomionej w usłudze Azure VM lub aplikacji sieci Web, można użyć dedykowanego modułu HSM. Ogólnie rzecz biorąc, oprogramowanie opakowane w folię termokurczliwą działające w modelach IaaS (infrastruktura jako usługa), które obsługuje moduły HSM jako magazynu kluczy, można użyć dedykatu HSM, takich jak:

* Brama aplikacji lub menedżer ruchu dla bezkluzywowego protokołu TLS
* USŁUGI ADCS (Usługi certyfikatów Active Directory)
* Podobne narzędzia infrastruktury kluczy publicznych
* Narzędzia/aplikacje używane do podpisywania dokumentów
* Podpisywanie kodu
* Program SQL Server (IaaS) skonfigurowany z funkcją TDE (transparent database encryption) z kluczem głównym w systemie HSM przy użyciu dostawcy EKM (ekm (ektensible key management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Pyt.: Czy dedykowany moduł HSM może być używany z kluczem klienta usługi Office 365, usługą Azure Information Protection, magazynem usługi Azure Data Lake Store, szyfrowaniem dysków, szyfrowaniem usługi Azure Storage, usługą Azure SQL TDE?

Nie. Dedykowany moduł HSM jest aprowizowana bezpośrednio do prywatnej przestrzeni adresów IP klienta, więc nie jest dostępny dla innych usług platformy Azure lub firmy Microsoft.

## <a name="administration-access-and-control"></a>Administrowanie, dostęp i kontrola

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Pyt.: Czy klient uzyskać pełną wyłączną kontrolę nad modułami HSM z dedykowanych modułów HSM?

Tak. Każde urządzenie HSM jest w pełni dedykowane jednemu klientowi i nikt inny nie ma kontroli administracyjnej po zainicjowaniu obsługi administracyjnej i zmianie hasła administratora.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Pyt.: Jaki poziom dostępu ma firma Microsoft do mojego modułu HSM?

Firma Microsoft nie ma żadnej kontroli administracyjnej ani kryptograficznej nad modułem HSM. Firma Microsoft ma dostęp do poziomu monitora za pośrednictwem połączenia portu szeregowego w celu pobrania podstawowych danych telemetrycznych, takich jak temperatura i kondycja składnika. Dzięki temu firma Microsoft może zapewnić proaktywne powiadamianie o problemach zdrowotnych. W razie potrzeby klient może wyłączyć to konto.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>Pyt.: Co to jest "tenantadmin" konto Microsoft używa, jestem przyzwyczajony do administratora użytkownika jest "admin" na SafeNet HSMs?

Urządzenie HSM jest dostarczane z domyślnym użytkownikiem administratora ze zwykłym domyślnym hasłem. Firma Microsoft nie chce mieć domyślnych haseł w użyciu, gdy każde urządzenie znajduje się w puli oczekujących na aprowizję przez klientów. Nie spełniałoby to naszych rygorystycznych wymogów bezpieczeństwa. Z tego powodu ustawiamy silne hasło, które jest odrzucane w czasie inicjowania obsługi administracyjnej. Ponadto w czasie inicjowania obsługi administracyjnej tworzymy nowego użytkownika w roli administratora o nazwie "tenantadmin". Ten użytkownik ma domyślne hasło i klienci zmieniają to jako pierwszą akcję podczas pierwszego logowania do nowo aprowizowanego urządzenia. Proces ten zapewnia wysoki stopień bezpieczeństwa i podtrzymuje naszą obietnicę wyłącznej kontroli administracyjnej dla naszych klientów. Należy zauważyć, że użytkownik "tenantadmin" może służyć do resetowania hasła użytkownika administratora, jeśli klient woli używać tego konta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Pyt.: Czy firma Microsoft lub ktokolwiek z nich w kluczach dostępu firmy Microsoft w dedykowanym modułze HSM?

Nie. Firma Microsoft nie ma dostępu do kluczy przechowywanych w dedykowanym modułie HSM przydzielonym klientowi.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Czy mogę uaktualnić oprogramowanie/oprogramowanie układowe w przydzielonych mi modułach HSM?

Aby uzyskać najlepszą pomoc techniczną, firma Microsoft zdecydowanie zaleca, aby nie uaktualniać oprogramowania/oprogramowania układowego w systemie HSM. Jednak klient ma pełną kontrolę administracyjną, w tym uaktualnienie oprogramowania/ oprogramowania układowego, jeśli określone funkcje są wymagane z różnych wersji oprogramowania układowego. Przed wprowadzeniem zmian implikacje muszą być rozumiane jako mogą na przykład wpływać na status zweryfikowany fips. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Jak zarządzać dedykowanym modułem HSM?

Dedykowane moduły HSM można zarządzać, uzyskując do nich dostęp za pomocą funkcji SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Pyt.: Jak zarządzać partycjami w dedykowanym modułie HSM?

Oprogramowanie klienta modułu HSM Gemalto służy do zarządzania modułami HSM i partycjami.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Jak monitorować moduł HSM?

Klient ma pełny dostęp do dzienników aktywności HSM za pośrednictwem syslog i SNMP. Klient musi skonfigurować serwer syslog lub serwer SNMP, aby odbierać dzienniki lub zdarzenia z modułów HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Pyt.: Czy można uzyskać pełny dziennik dostępu wszystkich operacji HSM z dedykowanego modułu HSM?

Tak. Dzienniki z urządzenia HSM można wysyłać do serwera syslog

## <a name="high-availability"></a>Wysoka dostępność

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Pyt.: Czy można skonfigurować wysoką dostępność w tym samym regionie lub w wielu regionach?

Tak. Wysoka konfiguracja dostępności i konfiguracja są wykonywane w oprogramowaniu klienta HSM dostarczonym przez Gemalto. Moduły HSM z tej samej sieci wirtualnej lub innych sieci wirtualnych w tym samym regionie lub w różnych regionach lub w lokalnych modułach HSM połączonych z siecią wirtualną przy użyciu sieci VPN lokacja-lokacja lub punkt-punkt można dodać do tej samej konfiguracji o wysokiej dostępności. Należy zauważyć, że synchronizuje tylko materiał klucza, a nie określonych elementów konfiguracji, takich jak role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Pyt.: Czy można dodać moduły HSM z mojej sieci lokalnej do grupy o wysokiej dostępności z dedykowanym modułem HSM platformy Azure?

Tak. Muszą one spełniać wysokie wymagania dotyczące dostępności dla safenet luna sieci HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Pyt.: Czy mogę dodać moduły HSM Luna 5/6 z sieci lokalnych do grupy o wysokiej dostępności za pomocą dedykowanego modułu HSM platformy Azure?

Nie.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Pyt.: Ile modułów HSM można dodać do tej samej konfiguracji wysokiej dostępności z jednej aplikacji?

16 członków grupy HA nie było, badania na pełnym gazie z doskonałymi wynikami.

## <a name="support"></a>Pomoc techniczna

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Pyt.: Co to jest usługa SLA dla dedykowanego modułu HSM?

Dla usługi dedykowanego modułu HSM nie udzielana jest żadna konkretna gwarancja czasu pracy. Firma Microsoft zapewni dostęp do urządzenia na poziomie sieci, a tym samym zastosowanie mają standardowe umowy SLA dotyczące sieci platformy Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Pyt.: Jak są używane moduły HSM w dedykowanym modułie HSM platformy Azure chronione?

Centra danych platformy Azure mają rozbudowane fizyczne i proceduralne mechanizmy kontroli zabezpieczeń. Oprócz tego dedykowane moduły HSM są hostowane w dalszym obszarze ograniczonego dostępu centrum danych. Obszary te mają dodatkowe fizyczne elementy sterujące dostępem i nadzór kamery wideo dla zwiększenia bezpieczeństwa.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Pyt.: Co się stanie, jeśli występuje naruszenie zabezpieczeń lub zdarzenie manipulacji sprzętowej?

Dedykowana usługa HSM wykorzystuje urządzenia SafeNet Network HSM 7. Urządzenia te obsługują fizyczne i logiczne wykrywanie sabotażu. Jeśli kiedykolwiek występuje zdarzenie sabotażu, moduły HSM są automatycznie wyzerowane.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Pyt.: Jak upewnić się, że klucze w dedykowanych modułach HSM nie zostaną utracone z powodu błędu lub złośliwego ataku poufnych informacji?

Zdecydowanie zaleca się używanie lokalnego urządzenia kopii zapasowej hsm do wykonywania regularnych okresowych kopii zapasowych modułów HSM w celu odzyskiwania po awarii. Należy użyć połączenia sieci VPN typu peer-to-peer lub lokacja-lokacja z lokalną stacją roboczą podłączoną do urządzenia kopii zapasowej modułu HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Pyt.: Jak uzyskać pomoc techniczną dla dedykowanego modułu HSM?

Pomoc techniczna jest świadczona zarówno przez firmę Microsoft, jak i Gemalto.  Jeśli masz problem ze sprzętem lub dostępem do sieci, należy zgłosić żądanie pomocy technicznej z firmą Microsoft i jeśli masz problem z konfiguracją modułu HSM, programem i tworzeniem aplikacji, należy zgłosić żądanie pomocy technicznej z gemalto. Jeśli masz nieokreślony problem, zgłoś prośbę o pomoc techniczną w firmie Microsoft, a następnie gemalto może zostać w razie potrzeby zaangażowana. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>Pyt.: Jak uzyskać oprogramowanie klienckie, dokumentację i dostęp do wskazówek dotyczących integracji dla modułu HSM SafeNet Luna 7?

Po zarejestrowaniu się w usłudze zostanie podany identyfikator klienta Gemalto, który umożliwia rejestrację w portalu obsługi klienta Gemalto. Umożliwi to dostęp do całego oprogramowania i dokumentacji, a także umożliwi bezpośrednie korzystanie z pomocy technicznej w gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Jeśli wykryto lukę w zabezpieczeniach i gemalto wydał poprawkę, która jest odpowiedzialna za uaktualnienie/łatanie systemu operacyjnego/oprogramowania układowego?

Firma Microsoft nie ma możliwości łączenia się z modułami HSM przydzielonymi klientom. Klienci muszą uaktualnić i załatać swoje moduły HSM.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: Co zrobić, jeśli muszę ponownie uruchomić moduł HSM?

Moduł HSM ma opcję ponownego uruchomienia wiersza polecenia, jednak występują problemy z zawieszaniem się ponownego uruchomienia sporadycznie i z tego powodu zaleca się najbezpieczniejszy ponowny rozruch, aby zgłosić żądanie pomocy technicznej z firmą Microsoft, aby urządzenie zostało fizycznie ponownie uruchomione. 

## <a name="cryptography-and-standards"></a>Kryptografia i standardy

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Pyt.: Czy przechowywanie kluczy szyfrowania dla moich najważniejszych danych w dedykowanym modułze HSM jest bezpieczne?

Tak, dedykowane urządzenia HSM SafeNet Network HSM 7, które używają modułów HSM FIPS 140-2 Poziomu 3, zostały zatwierdzone przez moduły HSM. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Pyt.: Jakie klucze kryptograficzne i algorytmy są obsługiwane przez dedykowany moduł HSM?

Dedykowane urządzenia HSM świadczące usługi SafeNet Network HSM 7. Obsługują szeroki zakres typów kluczy kryptograficznych i algorytmów, w tym: obsługa full suite B

* Asymetrycznego:
  * RSA
  * Dsa
  * Diffie-Hellman
  * Krzywa eliptyczna
  * Kryptografia (ECDSA, ECDH, Ed25519, ECIES) z krzywymi nazwanymi, zdefiniowanymi przez użytkownika i brainpool, KCDSA
* Symetrycznego:
  * AES-GCM
  * Potrójny DES
  * DES
  * ARIA, NASIONA
  * RC2 (rc2)
  * RC4
  * Rc5 (rc5)
  * CAST
  * Skrót/Skrót/HMAC: SHA-1, SHA-2, SM3
  * Wyprowadzanie klucza: tryb licznika SP800-108
  * Zawijanie kluczy: SP800-38F
  * Generowanie liczb losowych: ZATWIERDZONE PRZEZ FIPS 140-2 DRBG (tryb SP 800-90 CTR), zgodne z BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: Czy dedykowany moduł HSM FIPS 140-2 poziom 3 jest zatwierdzony?

Tak. Dedykowane urządzenia HSM świadczące usługi SafeNet Network HSM 7, które używają modułów HSM FIPS 140-2 Level 3, zostały zatwierdzone przez moduły HSM.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: Co należy zrobić, aby upewnić się, że działa dedykowany moduł HSM w trybie zweryfikowanym POZIOMEM 3 FIPS 140-2?

Dedykowana usługa HSM udostępnia urządzenia SafeNet Luna Network HSM 7. Urządzenia te wykorzystują moduły HSM z walidą FIPS 140-2 Poziomu 3. Domyślna wdrożona konfiguracja, system operacyjny i oprogramowanie układowe są również sprawdzane fips. Nie trzeba podejmować żadnych działań w przypadku zgodności z fips 140-2 poziom 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Pyt.: W jaki sposób klient zapewnia, że gdy moduł HSM jest wyrejestrowany, wszystkie kluczowe materiały są usuwane?

Przed żądaniem anulowania obsługi administracyjnej klient musi zerować moduł HSM przy użyciu narzędzia klienta programu Gemalto pod warunkiem, że hsm.

## <a name="performance-and-scale"></a>Wydajność i skalowanie

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Pyt.: Ile operacji kryptograficznych są obsługiwane na sekundę z dedykowanym modułem HSM?

Dedykowane urządzenia HSM SafeNet Network HSM 7 (model A790). Oto podsumowanie maksymalnej wydajności dla niektórych operacji: 

* RSA-2048: 10 000 transakcji na sekundę
* ECC P256: 20 000 transakcji na sekundę
* AES-GCM: 17 000 transakcji na sekundę

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Pyt.: Ile partycji można utworzyć w dedykowanym modułie HSM?

SafeNet Luna HSM 7 model A790 używane zawiera licencję na 10 partycji w kosztach usługi. Urządzenie ma limit 100 partycji, a dodanie partycji do tego limitu wiązałoby się z dodatkowymi kosztami licencjonowania i wymagało instalacji nowego pliku licencji na urządzeniu.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Pyt.: Ile kluczy może być obsługiwanych w dedykowanym modułie HSM?

Maksymalna liczba klawiszy jest funkcją dostępnej pamięci. SafeNet Luna 7 model A790 w użyciu ma 32MB pamięci. Następujące numery mają również zastosowanie do par kluczy, jeśli używasz kluczy asymetrycznych.

* RSA-2048 - 19 000
* ECC-P256 - 91 000

Pojemność będzie się różnić w zależności od określonych atrybutów klucza ustawionego w szablonie generowania klucza i liczby partycji.
