---
title: Archiwum nowości w programie Azure Security Center
description: Opis nowości i zmian w Azure Security Center z sześciu miesięcy temu i wcześniejszych.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2020
ms.author: memildin
ms.openlocfilehash: f24785336642570ae3f21d96462317890526a4c9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578950"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Co nowego w Azure Security Center?

Podstawowe [Informacje o nowościach Azure Security Center?](release-notes.md) Strona informacje o wersji zawiera aktualizacje dla ostatnich sześciu miesięcy, a ta strona zawiera starsze elementy.

Ta strona zawiera informacje na temat:

- Nowe funkcje
- Poprawki błędów
- Funkcje uznane za przestarzałe


## <a name="august-2020"></a>Sierpień 2020 r.

Aktualizacje w sierpniu obejmują:

- [Spis zasobów — zaawansowany nowy widok stan zabezpieczeń zasobów](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Dodano obsługę domyślnych ustawień zabezpieczeń Azure Active Directory (w przypadku uwierzytelniania wieloskładnikowego)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Dodano zalecenie dotyczące jednostek usługi](#service-principals-recommendation-added)
- [Ocena luk w zabezpieczeniach maszyn wirtualnych — zaleceń i zasad skonsolidowanych](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nowe zasady zabezpieczeń AKS dodane do inicjatywy ASC_default — wyłącznie do użytku przez klientów prywatnej wersji zapoznawczej](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Spis zasobów — zaawansowany nowy widok stan zabezpieczeń zasobów

Security Center spisu zasobów (obecnie w wersji zapoznawczej) umożliwia wyświetlenie stan zabezpieczeń zasobów, z którymi nawiązano połączenie Security Center.

Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu korygowania tych luk w zabezpieczeniach. Jeśli którykolwiek z zasobów ma zaległe zalecenia, zostaną one wyświetlone w spisie.

Widok i jego Filtry umożliwiają Eksplorowanie danych stan zabezpieczeń i podejmowanie dalszych działań w oparciu o Twoje wyniki.

Dowiedz się więcej na temat [spisu zasobów](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Dodano obsługę domyślnych ustawień zabezpieczeń Azure Active Directory (w przypadku uwierzytelniania wieloskładnikowego)

Security Center dodaliśmy pełną obsługę [zabezpieczeń domyślnych](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), bezpłatna ochrona zabezpieczeń firmy Microsoft.

Wartości domyślne zabezpieczeń zapewniają wstępnie skonfigurowane ustawienia zabezpieczeń tożsamości, aby chronić organizację przed typowymi atakami związanymi z tożsamościami. Zabezpieczenia domyślne chronią już więcej niż 5 000 000 dzierżawców; dzierżawy 50 000 są również chronione przez Security Center.

Security Center teraz zawiera zalecenie dotyczące zabezpieczeń, za każdym razem, gdy zidentyfikuje subskrypcję platformy Azure bez włączonej wartości domyślnej zabezpieczeń. Do tej pory Security Center zalecane włączenie uwierzytelniania wieloskładnikowego przy użyciu dostępu warunkowego, który jest częścią licencji Premium Azure Active Directory (AD). W przypadku klientów korzystających bezpłatnie z usługi Azure AD zalecamy włączenie ustawień domyślnych zabezpieczeń. 

Naszym celem jest zachęcanie większej liczby klientów do zabezpieczania środowisk w chmurze za pomocą usługi MFA i łagodzenie jednego z największych zagrożeń, które są również najbardziej wpływem na [bezpieczny wynik](secure-score-security-controls.md).

Dowiedz się więcej o [domyślnych ustawieniach zabezpieczeń](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Dodano zalecenie dotyczące jednostek usługi

Dodano nowe zalecenie, aby zalecić Security Center klientom używającym certyfikatów zarządzania do zarządzania swoimi subskrypcjami przełączać się do podmiotów usługi.

Zaleca się, **Aby Ochrona Twoich subskrypcji zamiast certyfikatów zarządzania była chroniona** przy użyciu jednostek usługi lub Azure Resource Manager do bezpieczniejszego zarządzania subskrypcjami. 

Więcej informacji o [obiektach głównych aplikacji i usług znajduje się w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych — zaleceń i zasad skonsolidowanych

Security Center przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione rozwiązanie do oceny luk w zabezpieczeniach. Jeśli nie zostanie odnalezione rozwiązanie do oceny luk w zabezpieczeniach, Security Center zapewnia zalecenie upraszczające wdrożenie.

Po znalezieniu luk w zabezpieczeniach Security Center zawiera zalecenia podsumowujące wyniki, które należy zbadać i skorygować w razie potrzeby.

Aby zapewnić spójne środowisko dla wszystkich użytkowników, niezależnie od używanego typu skanera, zostały ujednolicone cztery zalecenia w następujących dwóch:

|Ujednolicone zalecenie|Zmień opis|
|----|:----|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych**|Zastępuje dwa następujące zalecenia:<br> **•** Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys (obecnie przestarzałe) (dołączone do warstwy Standardowa)<br> **•** Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych (obecnie przestarzałe) (warstwy Standardowa i bezpłatna)|
|**Luki w zabezpieczeniach maszyn wirtualnych należy skorygować**|Zastępuje dwa następujące zalecenia:<br>**•** Koryguj luki w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys) (obecnie przestarzałe)<br>**•** Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach (obecnie przestarzałe)|
|||

Teraz użyjesz tego samego zalecenia do wdrożenia rozszerzenia oceny luk w zabezpieczeniach Security Center lub rozwiązania z licencją prywatną ("BYOL") od partnera, takiego jak Qualys lub Rapid7.

Ponadto po znalezieniu luk w zabezpieczeniach i zaraportowaniu ich do Security Center, jedno zalecenie będzie powiadamiać o wynikach niezależnie od rozwiązania do oceny luk w zabezpieczeniach, które je określiło.

#### <a name="updating-dependencies"></a>Aktualizowanie zależności

Jeśli masz skrypty, zapytania lub automatyzacje odwołujące się do poprzednich zaleceń lub kluczy zasad/nazw, Użyj poniższych tabel, aby zaktualizować odwołania:

##### <a name="before-august-2020"></a>Przed 2020 sierpnia

|Zalecenie|Zakres|
|----|:----|
|**Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)**<br>Klucz: 550e890b-e652-4d22-8274-60b3bdb24c63|Wbudowane|
|**Koryguj luki w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)**<br>Klucz: 1195afff-c881-495e-9bc5-1486211ae03f|Wbudowane|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych**<br>Klucz: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Klucz: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zasady|Zakres|
|----|:----|
|**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Wbudowane|
|**Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach**<br>Identyfikator zasad: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od sierpnia 2020

|Zalecenie|Zakres|
|----|:----|
|**Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych**<br>Klucz: ffff0522-1e88-47fc-8382-2a80ba848f5d|Wbudowane + BYOL|
|**Luki w zabezpieczeniach maszyn wirtualnych należy skorygować**<br>Klucz: 1195afff-c881-495e-9bc5-1486211ae03f|Wbudowane + BYOL|
||||

|Zasady|Zakres|
|----|:----|
|[**Ocena luk w zabezpieczeniach powinna być włączona na maszynach wirtualnych**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Identyfikator zasad: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Wbudowane + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nowe zasady zabezpieczeń AKS dodane do inicjatywy ASC_default — wyłącznie do użytku przez klientów prywatnej wersji zapoznawczej

Aby zapewnić, że obciążenia Kubernetes są zabezpieczone domyślnie, Security Center dodaje zasady na poziomie Kubernetes i zalecenia dotyczące ograniczania funkcjonalności, w tym opcje wymuszania z Kubernetes Admission Control.

Wczesna Faza tego projektu obejmuje prywatną wersję zapoznawczą oraz dodanie nowych (domyślnie wyłączonych) zasad do inicjatywy ASC_default.

Można bezpiecznie zignorować te zasady i nie będzie to miało wpływu na Twoje środowisko. Jeśli chcesz je włączyć, Utwórz konto w wersji zapoznawczej, https://aka.ms/SecurityPrP a następnie wybierz jedną z następujących opcji:

1. **Pojedynczej wersji zapoznawczej** — aby dołączyć tylko do tej prywatnej wersji zapoznawczej. Jawnie wskaż "ciągłe skanowanie ASC" jako wersję zapoznawczą, do której chcesz dołączyć.
1. **Program** będący w toku — zostanie dodany do tego i przyszłych wersji zapoznawczych. Musisz ukończyć profil i umowę o ochronie prywatności.


## <a name="july-2020"></a>Lipiec 2020 r.

Aktualizacje w lipcu obejmują:
- [Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrona przed zagrożeniami dla usługi Azure Storage rozwinięta w celu uwzględnienia Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Udoskonalenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżone dokumenty](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptacyjne kontrolki aplikacji zostały zaktualizowane z nowym zaleceniem i obsługą symboli wieloznacznych w regułach ścieżki](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sześć zasad usługi SQL Advanced Data Security zostało wycofane](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Ocena luk w zabezpieczeniach dla maszyn wirtualnych jest teraz dostępna dla obrazów spoza witryny Marketplace

Podczas wdrażania rozwiązania do oceny luk w zabezpieczeniach Security Center poprzednio wykonać sprawdzenie poprawności przed wdrożeniem. Sprawdzenie, czy chcesz potwierdzić jednostkę SKU docelowej maszyny wirtualnej. 

W ramach tej aktualizacji sprawdzanie zostało usunięte i teraz można wdrożyć narzędzia do oceny luk w zabezpieczeniach na komputerach z systemem Windows i Linux. Niestandardowe obrazy są modyfikowane przy użyciu ustawień domyślnych portalu Marketplace.

Chociaż teraz można wdrożyć zintegrowane rozwiązanie do oceny luk w zabezpieczeniach (obsługiwane przez Qualys) na wielu maszynach, pomoc techniczna jest dostępna tylko w przypadku korzystania z systemu operacyjnego wymienionego w temacie [wdrażanie skanera zintegrowanej luki w zabezpieczeniach w warstwie Standardowa](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

Dowiedz się więcej o [zintegrowanym skanerze luk w zabezpieczeniach dla maszyn wirtualnych (wymaga usługi Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Dowiedz się więcej o korzystaniu z rozwiązania do oceny luk w zabezpieczeniach w ramach usługi Qualys lub Rapid7 w [ramach wdrażania rozwiązania partnerskiego do skanowania](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Storage rozwinięta w celu uwzględnienia Azure Files i Azure Data Lake Storage Gen2 (wersja zapoznawcza)

Ochrona przed zagrożeniami dla usługi Azure Storage wykrywa potencjalnie szkodliwe działanie na kontach usługi Azure Storage. Security Center wyświetla alerty w przypadku wykrycia prób uzyskania dostępu do kont magazynu lub korzystania z nich. 

Dane można chronić, niezależnie od tego, czy są przechowywane jako kontenery obiektów blob, udziały plików czy jeziora danych.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osiem nowych zaleceń dotyczących włączania funkcji ochrony przed zagrożeniami

Dodano osiem nowych zaleceń w celu zapewnienia prostego sposobu włączania funkcji ochrony przed zagrożeniami Azure Security Center dla następujących typów zasobów: maszyny wirtualne, plany App Service, serwery Azure SQL Database, serwery SQL na maszynach, konta usługi Azure Storage, klastry usługi Azure Kubernetes, rejestry Azure Container Registry i magazyny Azure Key Vault.

Nowe zalecenia są następujące:

- **Zaawansowane zabezpieczenia danych powinny być włączone na serwerach Azure SQL Database**
- **Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w planach Azure App Service**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w rejestrach Azure Container Registry**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w magazynach Azure Key Vault**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona w klastrach usługi Azure Kubernetes**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona na kontach usługi Azure Storage**
- **Zaawansowana ochrona przed zagrożeniami powinna być włączona na maszynach wirtualnych**

Te nowe rekomendacje należą do **usługi Enable Azure Defender** Security Control.

Zalecenia obejmują również możliwość szybkiej naprawy. 

> [!IMPORTANT]
> Korygowaniem dowolne z tych zaleceń spowoduje naliczenie opłat za ochronę odpowiednich zasobów. Opłaty zostaną naliczone natychmiast, jeśli masz powiązane zasoby w bieżącej subskrypcji. Lub w przyszłości, jeśli dodasz je w późniejszym czasie.
> 
> Jeśli na przykład nie masz żadnych klastrów usługi Azure Kubernetes w subskrypcji i włączysz ochronę przed zagrożeniami, nie zostaną naliczone żadne opłaty. Jeśli w przyszłości dodasz klaster w ramach tej samej subskrypcji, zostanie on automatycznie objęty ochroną, a opłaty będą naliczane w tym czasie.

Więcej informacji na ten temat znajduje się na [stronie informacje o zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).

Dowiedz się więcej o [ochronie przed zagrożeniami w programie Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Udoskonalenia zabezpieczeń kontenerów — szybsze skanowanie rejestru i odświeżone dokumenty

W ramach ciągłych inwestycji w domenie zabezpieczeń kontenerów mamy przyjemność podzielić znaczącą poprawę wydajności w odniesieniu do dynamicznych skanów obrazów kontenerów przechowywanych w Azure Container Registry Security Center. Skany są zwykle wykonywane zazwyczaj w ciągu około dwóch minut. W niektórych przypadkach może to potrwać do 15 minut.

Aby zwiększyć przejrzystość i wskazówki dotyczące możliwości zabezpieczeń kontenerów Azure Security Center, odświeżamy również strony dokumentacji zabezpieczeń kontenerów. 

Dowiedz się więcej o zabezpieczeniach kontenerów Security Center w następujących artykułach:

- [Przegląd funkcji zabezpieczeń kontenera Security Center](container-security.md)
- [Szczegóły integracji z Azure Container Registry](defender-for-container-registries-introduction.md)
- [Szczegóły integracji z usługą Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Instrukcje dotyczące skanowania rejestrów i ograniczania funkcjonalności hostów platformy Docker](container-security.md)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami dla klastrów usługi Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Alerty zabezpieczeń z funkcji ochrony przed zagrożeniami dla hostów usługi Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Zalecenia dotyczące zabezpieczeń dla kontenerów](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptacyjne kontrolki aplikacji zostały zaktualizowane z nowym zaleceniem i obsługą symboli wieloznacznych w regułach ścieżki

Funkcja adaptacyjnych kontrolek aplikacji otrzymała dwie istotne aktualizacje:

* Nowe zalecenie identyfikuje potencjalnie wiarygodne zachowanie, które wcześniej nie było dozwolone. Nowe zalecenie, **dozwolonych reguły w ramach adaptacyjnych zasad kontroli aplikacji należy zaktualizować**, monituje o dodanie nowych reguł do istniejących zasad w celu zmniejszenia liczby fałszywych dodatnich w ramach adaptacyjnych kontroli aplikacji.

* Reguły ścieżek obsługują teraz symbole wieloznaczne. W ramach tej aktualizacji można skonfigurować dozwolone reguły ścieżek przy użyciu symboli wieloznacznych. Istnieją dwa obsługiwane scenariusze:

    * Używanie symbolu wieloznacznego na końcu ścieżki, aby zezwolić na wszystkie pliki wykonywalne w tym folderze i podfolderach

    * Użycie symbolu wieloznacznego w środku ścieżki w celu włączenia znanej nazwy pliku wykonywalnego ze zmianą nazwy folderu (np. osobiste foldery użytkowników z znanym plikiem wykonywalnym, automatycznie wygenerowanymi nazwami folderów itp.).


[Dowiedz się więcej na temat adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sześć zasad usługi SQL Advanced Data Security zostało wycofane

Sześć zasad związanych z zaawansowanymi zabezpieczeniami danych dla maszyn SQL jest przestarzałych:

- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Zaawansowane ustawienia zabezpieczeń danych dla wystąpienia zarządzanego SQL powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń
- Powiadomienia e-mail do administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL
- Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server

Dowiedz się więcej o [zasadach wbudowanych](./policy-reference.md).



## <a name="june-2020"></a>Czerwiec 2020 r.

Aktualizacje w czerwcu obejmują:
- [Interfejs API usługi Secure Score (wersja zapoznawcza)](#secure-score-api-preview)
- [Zaawansowane zabezpieczenia danych dla maszyn SQL (Azure, innych chmur i lokalnych) (wersja zapoznawcza)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Dwa nowe zalecenia dotyczące wdrażania agenta Log Analytics na maszynach usługi Azure ARC (wersja zapoznawcza)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nowe zasady tworzenia eksportu ciągłego i konfiguracji automatyzacji przepływu pracy na dużą skalę](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nowe zalecenie dotyczące używania sieciowych grup zabezpieczeń do ochrony maszyn wirtualnych niepołączonych z Internetem](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nowe zasady umożliwiające ochronę przed zagrożeniami i zaawansowane zabezpieczenia danych](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Interfejs API usługi Secure Score (wersja zapoznawcza)

Teraz możesz uzyskać dostęp do oceny za pośrednictwem [interfejsu API protokołu Secure Score](/rest/api/securitycenter/securescores/) (obecnie w wersji zapoznawczej). Metody interfejsu API zapewniają elastyczność umożliwiającą wykonywanie zapytań dotyczących danych i Tworzenie własnego mechanizmu raportowania z bezpiecznymi wynikami w czasie. Na przykład możesz użyć interfejsu API **Secure Scores** , aby uzyskać ocenę dla określonej subskrypcji. Ponadto można użyć interfejsu API **kontroli** zabezpieczeń, aby wyświetlić listę kontrolek bezpieczeństwa i bieżący wynik subskrypcji.

Aby zapoznać się z przykładowymi narzędziami zewnętrznymi z bezpiecznym interfejsem API oceny, zapoznaj [się z obszarem "bezpieczeństwo" w naszej społeczności usługi GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Dowiedz się więcej o [bezpiecznych kontrolach oceny i zabezpieczeń w Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Zaawansowane zabezpieczenia danych dla maszyn SQL (Azure, innych chmur i lokalnych) (wersja zapoznawcza)

Usługa Advanced Data Security Azure Security Center dla maszyn SQL chroni teraz serwery SQL hostowane na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych. Rozszerza to ochronę natywnych serwerów SQL platformy Azure, aby w pełni obsługiwać środowiska hybrydowe.

Zaawansowane zabezpieczenia danych zapewniają ocenę luk w zabezpieczeniach i zaawansowaną ochronę przed zagrożeniami dla maszyn SQL, wszędzie tam, gdzie się znajdują.

Konfiguracja obejmuje dwie czynności:

1. Wdrożenie agenta Log Analytics na komputerze-hoście SQL Server w celu udostępnienia połączenia z kontem platformy Azure.

1. Włączanie opcjonalnego pakietu na stronie cennika i ustawienia Security Center.

Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych dla maszyn SQL](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dwa nowe zalecenia dotyczące wdrażania agenta Log Analytics na maszynach usługi Azure ARC (wersja zapoznawcza)

Dodano dwa nowe zalecenia ułatwiające wdrożenie [agenta log Analytics](../azure-monitor/agents/log-analytics-agent.md) na maszynach usługi Azure Arc i zapewnienie ich ochrony przez Azure Security Center:

- **Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)**
- **Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)**

Te nowe zalecenia pojawią się w tych samych czterech kontrolach zabezpieczeń, co istniejące (powiązane) zalecenie, **Agent monitorowania powinien być zainstalowany na maszynach**: skoryguj konfiguracje zabezpieczeń, Zastosuj adaptacyjną kontrolę aplikacji, Zastosuj aktualizacje systemu i Włącz program Endpoint Protection.

Zalecenia obejmują również funkcję szybkiej naprawy, która ułatwia przyspieszenie procesu wdrożenia. 

Dowiedz się więcej na temat tych dwóch nowych zaleceń w tabeli [zaleceń dotyczących obliczeń i aplikacji](recommendations-reference.md#recs-compute) .

Dowiedz się więcej o tym, jak Azure Security Center używa agenta w [ramach agentów log Analytics?](faq-data-collection-agents.md#what-is-the-log-analytics-agent)

Dowiedz się więcej [na temat rozszerzeń dla maszyn usługi Azure Arc](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nowe zasady tworzenia eksportu ciągłego i konfiguracji automatyzacji przepływu pracy na dużą skalę

Automatyzacja procesów monitorowania i reagowania na zdarzenia w organizacji może znacznie poprawić czas potrzebny do zbadania i ograniczenia zdarzeń związanych z bezpieczeństwem.

Aby wdrożyć konfiguracje automatyzacji w organizacji, Użyj wbudowanych zasad platformy Azure "DeployIfdNotExist", aby utworzyć i skonfigurować procedury automatyzacji [ciągłego eksportowania](continuous-export.md) i [przepływu pracy](workflow-automation.md) :

Zasady można znaleźć w usłudze Azure Policy:


|Cel  |Zasady  |Identyfikator zasad  |
|---------|---------|---------|
|Eksport ciągły do centrum zdarzeń|[Wdróż eksportowanie alertów i zaleceń usługi Azure Security Center do centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Eksport ciągły do obszaru roboczego Log Analytics|[Wdróż eksport do obszaru roboczego usługi Log Analytics w celu uzyskania alertów i zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automatyzacja przepływu pracy dla alertów zabezpieczeń|[Wdróż automatyzację przepływu pracy dla alertów usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatyzacja przepływu pracy dla zaleceń dotyczących zabezpieczeń|[Wdróż automatyzację przepływów pracy dla zaleceń usługi Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Wprowadzenie do [szablonów automatyzacji przepływu pracy](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Dowiedz się więcej o korzystaniu z dwóch zasad eksportu w obszarze [Konfigurowanie automatyzacji przepływu pracy na dużą skalę przy użyciu dostarczonych zasad](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) i [Skonfiguruj eksport ciągły](continuous-export.md#set-up-a-continuous-export).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nowe zalecenie dotyczące używania sieciowych grup zabezpieczeń do ochrony maszyn wirtualnych niepołączonych z Internetem

Kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń" obejmuje teraz następujące nowe zalecenia:

- **Maszyny wirtualne, które nie są dostępne w Internecie, powinny być chronione przy użyciu sieciowych grup zabezpieczeń**

Istniejące zalecenie, dostępne **z Internetu maszyny wirtualne powinny być chronione za pomocą sieciowych grup zabezpieczeń**, nie rozróżniać się między maszynami wirtualnymi internetowymi i niepołączonymi z Internetem. Dla obu tych celów zostało wygenerowane zalecenie o wysokiej ważności, jeśli maszyna wirtualna nie została przypisana do sieciowej grupy zabezpieczeń. To nowe zalecenie oddziela maszyny niepołączone z Internetem, aby zmniejszyć liczbę fałszywych ostrzeżeń i uniknąć niepotrzebnych alertów o wysokiej ważności.

Dowiedz się więcej z tabeli [zalecenia dotyczące sieci](recommendations-reference.md#recs-networking) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nowe zasady umożliwiające ochronę przed zagrożeniami i zaawansowane zabezpieczenia danych

Nowe zasady poniżej zostały dodane do inicjatywy default ASC i zostały zaprojektowane z myślą o włączeniu ochrony przed zagrożeniami lub zaawansowanej ochrony danych dla odpowiednich typów zasobów.

Zasady można znaleźć w usłudze Azure Policy:


| Zasady                                                                                                                                                                                                                                                                | Identyfikator zasad                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Zaawansowane zabezpieczenia danych powinny być włączone na serwerach Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona na kontach usługi Azure Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w magazynach Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w planach Azure App Service](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w rejestrach Azure Container Registry](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona w klastrach usługi Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Zaawansowana ochrona przed zagrożeniami powinna być włączona na Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Dowiedz się więcej o [ochronie przed zagrożeniami w programie Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Maj 2020 r.

Aktualizacje w programie mogą obejmować:
- [Reguły pomijania alertów (wersja zapoznawcza)](#alert-suppression-rules-preview)
- [Ocena luk w zabezpieczeniach maszyn wirtualnych jest teraz ogólnie dostępna](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Zmiany dostępu do maszyny wirtualnej just-in-Time (JIT)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Zalecenia niestandardowe zostały przeniesione do oddzielnej kontroli zabezpieczeń](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Przełącznik dodany do wyświetlania zaleceń w kontrolkach lub jako płaska lista](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Rozszerzona kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń"](#expanded-security-control-implement-security-best-practices)
- [Zasady niestandardowe z metadanymi niestandardowymi są teraz ogólnie dostępne](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Funkcje analizy zrzutów awaryjnych migrowane do wykrywania ataków bezplikowych](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Reguły pomijania alertów (wersja zapoznawcza)

Ta nowa funkcja (obecnie dostępna w wersji zapoznawczej) umożliwia zmniejszenie zmęczenia alertów. Użyj reguł, aby automatycznie ukrywać alerty, które są znane jako niewielkiej ilości lub związane z normalnymi działaniami w organizacji. Pozwala to skupić się na najbardziej odpowiednich zagrożeniach. 

Alerty zgodne z włączonymi regułami pomijania będą nadal generowane, ale ich stan zostanie ustawiony na odrzucony. Stan można sprawdzić w Azure Portal lub uzyskać dostęp do Security Center alertów zabezpieczeń.

Reguły pomijania definiują kryteria, dla których alerty powinny być automatycznie odrzucane. Zazwyczaj można użyć reguły pomijania, aby:

- Pomijaj alerty, które zostały zidentyfikowane jako fałszywe pozytywne

- Pomijanie alertów, które są wyzwalane zbyt często, aby być przydatne

Dowiedz się więcej [na temat pomijania alertów z ochrony przed zagrożeniami w usłudze Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych jest teraz ogólnie dostępna

Warstwa standardowa Security Center obejmuje teraz zintegrowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. To rozszerzenie jest obsługiwane przez Qualys, ale raportuje jego wyniki bezpośrednio z powrotem do Security Center. Nie potrzebujesz licencji Qualys, a nawet konta Qualys — wszystko, co jest obsługiwane bezproblemowo w Security Center.

Nowe rozwiązanie może stale skanować maszyny wirtualne, aby znaleźć luki w zabezpieczeniach i przedstawić wyniki w Security Center. 

Aby wdrożyć rozwiązanie, należy zastosować nowe zalecenie dotyczące zabezpieczeń:

"Włącz wbudowane rozwiązanie do oceny luk w zabezpieczeniach na maszynach wirtualnych (obsługiwane przez Qualys)"

Dowiedz się więcej [na temat oceny zintegrowanej luki w zabezpieczeniach Security Center dla maszyn wirtualnych](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Zmiany dostępu do maszyny wirtualnej just-in-Time (JIT)

Security Center zawiera opcjonalną funkcję ochrony portów zarządzania maszynami wirtualnymi. Zapewnia to obronę przed najbardziej powszechnymi atakami na ataki.

Ta aktualizacja wprowadza następujące zmiany w tej funkcji:

- Zalecenie, które zalecić włączenie JIT na maszynie wirtualnej, zostało zmienione. Wcześniej na maszynach wirtualnych należy zastosować kontrolę dostępu do sieci just-in-Time, która jest teraz: "porty zarządzania maszyn wirtualnych należy chronić za pomocą kontroli dostępu just in Time do sieci".

- Zalecenie jest wyzwalane tylko wtedy, gdy istnieją otwarte porty zarządzania.

Dowiedz się więcej o [funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Zalecenia niestandardowe zostały przeniesione do oddzielnej kontroli zabezpieczeń

Jedna kontrola zabezpieczeń wprowadzona z ulepszonym zabezpieczonym wynikiem to "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Wszelkie niestandardowe zalecenia utworzone dla subskrypcji zostały automatycznie umieszczone w tym formancie. 

Aby ułatwić znalezienie niestandardowych zaleceń, przeniesiono je do dedykowanej kontroli zabezpieczeń, "zalecenia niestandardowe". Ten formant nie ma wpływu na Twój bezpieczny wynik.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Przełącznik dodany do wyświetlania zaleceń w kontrolkach lub jako płaska lista

Funkcje kontroli zabezpieczeń są logicznymi grupami powiązanych zaleceń dotyczących zabezpieczeń. Odzwierciedlają one podatne na ataki. Kontrolka jest zestawem zaleceń dotyczących zabezpieczeń, z instrukcjami, które ułatwiają zaimplementowanie tych zaleceń.

Aby od razu sprawdzić, jak dobrze organizacja zabezpiecza poszczególne osoby atakujące, zapoznaj się z wynikami każdej kontroli zabezpieczeń.

Domyślnie Twoje zalecenia są wyświetlane w obszarze zabezpieczenia. W ramach tej aktualizacji można także wyświetlić je jako listę. Aby wyświetlić je jako prostą listę posortowaną według stanu kondycji odpowiednich zasobów, Użyj nowego przełącznika "Grupuj według kontrolek". Przełącznik znajduje się nad listą w portalu.

Formanty zabezpieczeń — i ten przełącznik — są częścią nowego bezpiecznego wyniku. Pamiętaj, aby wysłać nam swoją opinię z portalu.

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza) w Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Grupuj według kontrolek przełącznik dla zaleceń":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Rozszerzona kontrola zabezpieczeń "implementacja najlepszych rozwiązań w zakresie zabezpieczeń" 

Jedna kontrola zabezpieczeń wprowadzona w ramach udoskonalonego bezpiecznego wyniku to "Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń". Gdy zalecenie jest w tej kontrolce, nie ma to wpływu na bezpieczny wynik. 

W przypadku tej aktualizacji trzy zalecenia zostały przeniesione z kontrolek, w których zostały one pierwotnie umieszczone, i do tej kontroli najlepszych rozwiązań. Ten krok został osiągnięty, ponieważ wykryto, że ryzyko tych trzech zaleceń jest mniejsze niż początkowo zostało przemyślane.

Ponadto wprowadzono dwa nowe rekomendacje i dodano je do tej kontrolki.

Trzy zaleceń, które przeniesiono:

- Usługę **MFA należy włączyć na kontach z uprawnieniami do odczytu w ramach subskrypcji** (wcześniej w formancie "Włącz usługę MFA").
- **Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji** (wcześniej w kontrolce "Zarządzanie dostępem i uprawnieniami").
- Dla subskrypcji (początkowo w kontrolce "Zarządzaj dostępem i uprawnieniami") **należy wyznaczyć maksymalnie 3 właścicieli** .

Dwa nowe rekomendacje dodane do kontrolki są następujące:

- **Rozszerzenie konfiguracji gościa powinno być zainstalowane na maszynach wirtualnych z systemem Windows (wersja zapoznawcza)** — używanie [Azure Policy konfiguracji gościa](../governance/policy/concepts/guest-configuration.md) zapewnia widoczność w obrębie maszyn wirtualnych do ustawień serwera i aplikacji (tylko system Windows).

- **Funkcja Windows Defender Exploit Guard powinna być włączona na maszynach (wersja zapoznawcza)** — usługa Windows Defender Exploit guard wykorzystuje Azure Policy agenta konfiguracji gościa. Funkcja Exploit Guard ma cztery składniki przeznaczone do blokowania urządzeń przed szeroką gamą wektorów ataków i blokowania zachowań często używanych w atakach złośliwego oprogramowania, a jednocześnie umożliwia przedsiębiorstwom zrównoważenie zagrożeń bezpieczeństwa i wymagań dotyczących produktywności (tylko system Windows).

Dowiedz się więcej o funkcji Windows Defender Exploit Guard w temacie [Tworzenie i wdrażanie zasad funkcji Exploit Guard](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Dowiedz się więcej o kontrolkach zabezpieczeń w [ulepszonym zabezpieczeniu (wersja zapoznawcza)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Zasady niestandardowe z metadanymi niestandardowymi są teraz ogólnie dostępne

Zasady niestandardowe są teraz częścią środowiska zaleceń Security Center, bezpieczeństwa i oceny zgodności z przepisami. Ta funkcja jest teraz ogólnie dostępna i umożliwia zwiększenie zakresu oceny zabezpieczeń w organizacji w Security Center. 

Utwórz niestandardową inicjatywę w usłudze Azure Policy, Dodaj do niej zasady i Dołącz ją do Azure Security Center i Wizualizuj ją jako zalecenia.

Teraz dodano również opcję edytowania niestandardowych metadanych rekomendacji. Opcje metadanych obejmują ważność, kroki korygowania, informacje o zagrożeniach i nie tylko.  

Dowiedz się więcej o [ulepszaniu niestandardowych zaleceń ze szczegółowymi informacjami](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Funkcje analizy zrzutów awaryjnych migrowane do wykrywania ataków bezplikowych 

Firma Microsoft integruje możliwości wykrywania zrzutów awaryjnych systemu Windows () w [wykrywanie ataków bezplikowych](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Analiza wykrywania ataków bezplikowych zapewnia ulepszone wersje następujących alertów zabezpieczeń dla maszyn z systemem Windows: odnaleziono iniekcję kodu, wykryto zamaskowanego modułu systemu Windows, wykryty wykryto kod powłoki oraz podejrzany segment kodu.

Niektóre zalety tego przejścia:

- **Proaktywne i czasowe wykrywanie złośliwego oprogramowania** — podejście, które może oczekiwać na wystąpienie awarii, a następnie uruchomienie analizy, aby znaleźć złośliwe artefakty. Korzystanie z funkcji wykrywania ataków bez plików umożliwia aktywne zidentyfikowanie zagrożeń w pamięci, gdy są one uruchomione. 

- **Ulepszone alerty** — alerty zabezpieczeń związane z wykrywaniem ataków bez plików obejmują wzbogacania, które nie są dostępne, na przykład informacje o aktywnych połączeniach sieciowych. 

- **Agregacja alertów** — gdy serwer sieci wykryje wiele wzorców ataków w ramach pojedynczego zrzutu awaryjnego, wyzwoliło wiele alertów zabezpieczeń. Wykrywanie ataków bez plików łączy wszystkie zidentyfikowane wzorce ataku z tego samego procesu w jeden alert, usuwając konieczność skorelowania wielu alertów.

- **Obniżone wymagania w obszarze roboczym log Analytics** — Zrzuty awaryjne zawierające potencjalnie poufne dane nie będą już przekazywane do log Analytics obszaru roboczego.






## <a name="april-2020"></a>Kwiecień 2020 r.

Aktualizacje w kwietniu obejmują:
- [Dynamiczne pakiety zgodności są teraz ogólnie dostępne](#dynamic-compliance-packages-are-now-generally-available)
- [Zalecenia dotyczące tożsamości zawarte obecnie w Azure Security Center warstwy Bezpłatna](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamiczne pakiety zgodności są teraz ogólnie dostępne

Pulpit nawigacyjny zgodności z przepisami Azure Security Center obejmuje teraz **dynamiczne pakiety zgodności** (teraz ogólnie dostępne) do śledzenia dodatkowych standardów branżowych i prawnych.

Dynamiczne pakiety zgodności można dodać do swojej subskrypcji lub grupy zarządzania ze strony Security Center zasady zabezpieczeń. Po dodaniu standardowego lub testu porównawczego Standard pojawia się na pulpicie nawigacyjnym zgodności z przepisami, który ma wszystkie skojarzone dane zgodności zamapowane jako oceny. Raport podsumowujący dla dowolnych ze standardów, które zostały dołączone, będzie dostępny do pobrania.

Teraz możesz dodawać standardy, takie jak:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-V2020**
- **Oficjalne Królestwo brytyjskie i Zjednoczone Królestwo NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nowy)** (czyli bardziej kompletna reprezentacja usługi Azure CIS 1.1.0)

Ponadto został niedawno dodany **test testów zabezpieczeń platformy Azure**, oparte na platformie Azure wskazówki dotyczące zabezpieczeń i zgodności w oparciu o typowe struktury zgodności. Dodatkowe standardy będą obsługiwane na pulpicie nawigacyjnym, gdy staną się dostępne.  
 
Dowiedz się więcej o [dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Zalecenia dotyczące tożsamości zawarte obecnie w Azure Security Center warstwy Bezpłatna

Zalecenia dotyczące zabezpieczeń dotyczące tożsamości i dostępu w Azure Security Center warstwy Bezpłatna są teraz ogólnie dostępne. Jest to część nakładu pracy, aby udostępnić funkcje usługi Cloud Security stan Management (CSPM). Do tej pory te zalecenia były dostępne tylko w warstwie cenowej standardowa.

Przykłady zaleceń dotyczących tożsamości i dostępu to:

- "Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji".
- "Dla subskrypcji należy wyznaczyć maksymalnie trzech właścicieli".
- "Przestarzałe konta powinny zostać usunięte z subskrypcji".

Jeśli masz subskrypcje w bezpłatnej warstwie cenowej, będzie to miało wpływ na ich bezpieczeństwo, ponieważ nigdy nie oceniono ich pod kątem bezpieczeństwa tożsamości i dostępu.

Dowiedz się więcej o [zaleceniach dotyczących tożsamości i dostępu](recommendations-reference.md#recs-identityandaccess).

Dowiedz się więcej o [monitorowaniu tożsamości i dostępu](security-center-identity-access.md).



## <a name="march-2020"></a>Marzec 2020 r.

Aktualizacje w marcu obejmują:

- [Automatyzacja przepływu pracy jest teraz ogólnie dostępna](#workflow-automation-is-now-generally-available)
- [Integracja Azure Security Center z centrum administracyjnym systemu Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Ochrona usługi Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Udoskonalone środowisko just in Time](#improved-just-in-time-experience)
- [Dwie zalecenia dotyczące zabezpieczeń dla aplikacji sieci Web przestarzałe](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Automatyzacja przepływu pracy jest teraz ogólnie dostępna

Funkcja automatyzacji przepływu pracy Azure Security Center jest teraz ogólnie dostępna. Służy do automatycznego wyzwalania Logic Apps na temat alertów zabezpieczeń i zaleceń. Ponadto są dostępne wyzwalacze ręczne dla alertów i wszystkie zalecenia z opcją szybkie rozwiązanie.

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie i może poprawić bezpieczeństwo, zapewniając, że etapy procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Dowiedz się więcej na temat [tworzenia Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integracja Azure Security Center z centrum administracyjnym systemu Windows

Teraz można przenieść lokalne serwery z systemem Windows z centrum administracyjnego systemu Windows bezpośrednio do Azure Security Center. Security Center następnie przejdziesz do jednego okienka Glass, aby wyświetlić informacje o zabezpieczeniach dla wszystkich zasobów centrum administratora systemu Windows, w tym serwerów lokalnych, maszyn wirtualnych i dodatkowych obciążeń PaaS.

Po przeniesieniu serwera z centrum administracyjnego systemu Windows do Azure Security Center można:

- Wyświetlanie alertów zabezpieczeń i zaleceń w Security Center rozszerzeniu centrum administracyjnego systemu Windows.
- Zapoznaj się z zabezpieczeniami stan i Pobierz dodatkowe szczegółowe informacje o zarządzanych serwerach centrum administracyjnego systemu Windows w Security Center w ramach Azure Portal (lub za pośrednictwem interfejsu API).

Dowiedz się więcej o tym [, jak zintegrować Azure Security Center z centrum administracyjnym systemu Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrona usługi Azure Kubernetes Service

Azure Security Center rozszerza funkcje zabezpieczeń kontenerów w celu ochrony usługi Azure Kubernetes Service (AKS).

Popularna Kubernetes platformy Open Source została przyjęta tak, aby była teraz standardem branżowym dla aranżacji kontenerów. Pomimo tej rozległej implementacji nadal nie można zrozumieć, jak zabezpieczyć środowisko Kubernetes. Obronność obszarów ataku aplikacji kontenerowej wymaga ekspertyzy, aby zapewnić, że infrastruktura została skonfigurowana bezpiecznie i stale monitorowana pod kątem potencjalnych zagrożeń.

Ochrona Security Center obejmuje:

- **Odnajdywanie i widoczność** — ciągłe wykrywanie zarządzanych wystąpień AKS w ramach subskrypcji zarejestrowanych do Security Center.
- **Zalecenia dotyczące zabezpieczeń** — zalecenia z możliwością podejmowania działań, które ułatwiają zgodność z najlepszymi rozwiązaniami w zakresie zabezpieczeń dla AKS. Te zalecenia są zawarte w zabezpieczonym wyniku, aby upewnić się, że są one widoczne jako część stan zabezpieczeń organizacji. Przykładem zalecenia powiązanego z AKSem jest "kontrola dostępu oparta na rolach powinna być używana do ograniczania dostępu do klastra usługi Kubernetes Service".
- **Ochrona przed zagrożeniami** — dzięki ciągłej analizie wdrożenia AKS Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie klastra hosta i AKS.

Dowiedz się więcej o [integracji usług Azure Kubernetes Services z usługą Security Center](defender-for-kubernetes-introduction.md).

Dowiedz się więcej o [funkcjach zabezpieczeń kontenera w Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Udoskonalone środowisko just in Time

Funkcje, operacje i interfejs użytkownika dla narzędzi just in Time Azure Security Center, które zabezpieczają porty zarządzania, zostały ulepszone w następujący sposób: 

- **Pole uzasadnienia** — podczas żądania dostępu do maszyny wirtualnej za pomocą strony just-in-time w Azure Portal jest dostępne nowe pole opcjonalne, aby wprowadzić uzasadnienie żądania. Informacje wprowadzane do tego pola mogą być śledzone w dzienniku aktywności. 
- **Automatyczne oczyszczanie nadmiarowych reguł just-in-Time (JIT)** — za każdym razem, gdy aktualizujesz zasady JIT, narzędzie do czyszczenia zostanie automatycznie uruchomione, aby sprawdzić poprawność całego zestawu reguł. Narzędzie szuka niezgodności między regułami w zasadach i regułami w sieciowej grupy zabezpieczeń. Jeśli narzędzie do czyszczenia znajdzie niezgodność, określa przyczynę i, gdy jest to bezpieczne, usuwa wbudowane reguły, które nie są już potrzebne. Oczyszczarka nigdy nie usuwa reguł, które zostały utworzone. 

Dowiedz się więcej o [funkcji dostępu JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dwie zalecenia dotyczące zabezpieczeń dla aplikacji sieci Web przestarzałe

Dwie zalecenia dotyczące zabezpieczeń związane z aplikacjami sieci Web są przestarzałe: 

- Reguły dla aplikacji sieci Web w IaaS sieciowych grup zabezpieczeń powinny być zaostrzone.
    (Powiązane zasady: reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web na IaaS powinny być zaostrzone)

- Dostęp do App Services powinien być ograniczony.
    (Powiązane zasady: dostęp do App Services powinien być ograniczony [wersja zapoznawcza])

Te rekomendacje nie będą już wyświetlane na liście Security Center zaleceń. Powiązane zasady nie będą już uwzględniane w ramach inicjatywy o nazwie "Security Center domyślne".

Dowiedz się więcej o [zaleceniach dotyczących zabezpieczeń](recommendations-reference.md).




## <a name="february-2020"></a>Luty 2020 r.

### <a name="fileless-attack-detection-for-linux-preview"></a>Wykrywanie ataków bezplikowych dla systemu Linux (wersja zapoznawcza)

Gdy osoby atakujące zwiększą wykorzystanie metod stealthier, aby uniknąć wykrywania, Azure Security Center rozszerza wykrywanie ataków bez plików dla systemu Linux, a nie tylko z systemem Windows. Ataki bezplikowe wykorzystują luki w zabezpieczeniach, wprowadzają złośliwe ładunki do niegroźnych procesów systemu i ukrywają w pamięci. Techniki te:

- Minimalizowanie lub eliminowanie śladów złośliwego oprogramowania na dysku
- znacznie zmniejsz szanse wykrywania przez rozwiązania skanujące złośliwe oprogramowanie oparte na dyskach

Aby wyeliminować to zagrożenie, Azure Security Center wydane wykrywanie ataków bezplików dla systemu Windows w październiku 2018 i obecnie rozszerzono wykrywanie ataków bezplikowych w systemie Linux. 



## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="enhanced-secure-score-preview"></a>Ulepszony bezpieczny wynik (wersja zapoznawcza)

Rozszerzona wersja funkcji bezpiecznego oceny Azure Security Center jest teraz dostępna w wersji zapoznawczej. W tej wersji wiele zaleceń jest pogrupowanych w kontrolki zabezpieczeń, które lepiej odzwierciedlają zagrożone powierzchnie ataków (na przykład ograniczanie dostępu do portów zarządzania).

Zapoznaj się z informacjami o bezpiecznych zmianach oceny w fazie wersji zapoznawczej i ustal inne korygowanie, które pomogą Ci zapewnić dalsze bezpieczeństwo środowiska.

Dowiedz się więcej o [ulepszonym zabezpieczeniu (wersja zapoznawcza)](secure-score-security-controls.md).



## <a name="november-2019"></a>Listopad 2019 r.

Aktualizacje w listopadzie obejmują:
 - [Ochrona przed zagrożeniami dla Azure Key Vault w regionach Ameryka Północna (wersja zapoznawcza)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Ochrona przed zagrożeniami w usłudze Azure Storage obejmuje osłanianie reputacji złośliwego oprogramowania](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatyzacja przepływu pracy z Logic Apps (wersja zapoznawcza)](#workflow-automation-with-logic-apps-preview)
 - [Szybka poprawka dla zasobów zbiorczych jest ogólnie dostępna](#quick-fix-for-bulk-resources-generally-available)
 - [Skanuj obrazy kontenerów dla luk w zabezpieczeniach (wersja zapoznawcza)](#scan-container-images-for-vulnerabilities-preview)
 - [Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)](#additional-regulatory-compliance-standards-preview)
 - [Ochrona przed zagrożeniami dla usługi Azure Kubernetes Service (wersja zapoznawcza)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Ocena luk w zabezpieczeniach maszyn wirtualnych (wersja zapoznawcza)](#virtual-machine-vulnerability-assessment-preview)
 - [Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Obsługa zasad niestandardowych (wersja zapoznawcza)](#support-for-custom-policies-preview)
 - [Rozszerzanie zakresu Azure Security Center przy użyciu platformy dla społeczności i partnerów](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Zaawansowane integracje z eksportem zaleceń i alertów (wersja zapoznawcza)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Dołączanie serwerów premium do Security Center z centrum administracyjnego systemu Windows (wersja zapoznawcza)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Ochrona przed zagrożeniami dla Azure Key Vault w regionach Ameryka Północna (wersja zapoznawcza)

Azure Key Vault to podstawowa usługa służąca do ochrony danych i poprawiania wydajności aplikacji w chmurze, oferując możliwość centralnego zarządzania kluczami, wpisami tajnymi, kluczami kryptograficznymi i zasadami w chmurze. Ponieważ Azure Key Vault przechowuje wrażliwe i krytyczne dane biznesowe, wymaga maksymalnego poziomu zabezpieczeń dla magazynów kluczy i przechowywanych w nich danych.

Azure Security Center obsługa ochrony przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do magazynów kluczy lub korzystania z nich. Ta nowa warstwa ochrony pozwala klientom na rozwiązywanie zagrożeń związanych z ich magazynami kluczy bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń. Ta funkcja jest dostępna w publicznej wersji zapoznawczej w regionach Ameryka Północna.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Ochrona przed zagrożeniami w usłudze Azure Storage obejmuje osłanianie reputacji złośliwego oprogramowania

Ochrona przed zagrożeniami dla usługi Azure Storage oferuje nowe wykrycia obsługiwane przez funkcję analizy zagrożeń firmy Microsoft w celu wykrywania przeładowania złośliwego oprogramowania do usługi Azure Storage przy użyciu analizy reputacji i podejrzanego dostępu z aktywnego węzła zakończenia sieci Tor (anonymizing proxy). Można teraz wyświetlać wykryte złośliwe oprogramowanie na kontach magazynu przy użyciu Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatyzacja przepływu pracy z Logic Apps (wersja zapoznawcza)

Organizacje z centralnie zarządzanymi zabezpieczeniami i działem IT/Operations implementują wewnętrzne procesy przepływu pracy, aby zapewnić wymaganą akcję w organizacji w przypadku wykrycia niezgodności w swoich środowiskach. W wielu przypadkach te przepływy pracy są powtarzalnymi procesami, a Automatyzacja może znacznie usprawnić procesy w organizacji.

Dzisiaj wprowadzamy nową funkcję w Security Center, która umożliwia klientom Tworzenie konfiguracji automatyzacji wykorzystujących Azure Logic Apps i tworzenie zasad, które będą automatycznie wyzwalać je na podstawie określonych ustaleń ASC, takich jak zalecenia lub alerty. Aplikację logiki platformy Azure można skonfigurować w taki sposób, aby wykonać dowolną akcję niestandardową obsługiwaną przez rozległą społeczność łączników aplikacji logiki lub użyć jednego z szablonów dostarczonych przez Security Center, takich jak wysyłanie wiadomości e-mail lub otwieranie biletu usługi usługi ServiceNow™.

Aby uzyskać więcej informacji o automatycznym i ręcznym Security Center możliwości uruchamiania przepływów pracy, zobacz [Automatyzacja przepływu pracy](workflow-automation.md).

Aby dowiedzieć się więcej na temat tworzenia Logic Apps, zobacz [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Szybka poprawka dla zasobów zbiorczych jest ogólnie dostępna

Dzięki wielu zadań, które użytkownik otrzymuje w ramach bezpiecznego oceny, zdolność do skutecznego korygowania problemów w ramach dużej floty może stać się trudne.

Aby uprościć korygowanie nieprawidłowych konfiguracji zabezpieczeń i mieć możliwość szybkiego korygowania zaleceń dotyczących ilości zasobów i zwiększenia bezpiecznego wyniku, należy użyć funkcji szybkiego rozwiązywania problemów.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która spowoduje skonfigurowanie ustawienia w Twoim imieniu.

Szybka poprawka jest ogólnie dostępna dla klientów jako część strony rekomendacje Security Center.

Zapoznaj się ze wskazówkami dotyczącymi szybkiego rozwiązywania problemów w [przewodniku dotyczącym zabezpieczeń](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skanuj obrazy kontenerów dla luk w zabezpieczeniach (wersja zapoznawcza)

Azure Security Center teraz można skanować obrazy kontenerów w Azure Container Registry w celu uzyskania luk w zabezpieczeniach.

Skanowanie obrazów działa przez analizowanie pliku obrazu kontenera, a następnie sprawdzenie, czy istnieją znane luki w zabezpieczeniach (obsługiwane przez Qualys).

Skanowanie jest automatycznie wyzwalane podczas wypychania nowych obrazów kontenera do Azure Container Registry. Znalezione luki w zabezpieczeniach będą przedstawiane jako zalecenia dotyczące Security Center zaleceń i zawartych w zestawieniu zabezpieczeń platformy Azure wraz z informacjami na temat sposobu ich poprawek w celu zmniejszenia dopuszczalnej powierzchni ataków.


### <a name="additional-regulatory-compliance-standards-preview"></a>Dodatkowe standardy zgodności z przepisami (wersja zapoznawcza)

Pulpit nawigacyjny zgodności z przepisami zapewnia wgląd w stan zgodności na podstawie ocen Security Center. Pulpit nawigacyjny pokazuje, jak środowisko jest zgodne z kontrolkami i wymaganiami określonymi przez określone standardy prawne i testy porównawcze oraz zawiera zalecenia dotyczące sposobu rozwiązywania tych wymagań.

Pulpit nawigacyjny zgodności z przepisami ma w tym przypadku cztery wbudowane standardy: Azure CIS 1.1.0, PCI-DSS, ISO 27001 i SOC-TSP. Teraz ogłaszamy publiczną wersję zapoznawczą dodatkowych obsługiwanych standardów: NIST SP 800-53 R4, SWIFT CSP CSCF V2020, Kanada Federal PBMM i Zjednoczone Królestwo z Wielkiej Brytanii NHS. Firma Microsoft udostępnia również zaktualizowaną wersję usługi Azure CIS 1.1.0, która obejmuje więcej kontroli z poziomu standardowej i rozszerzanej rozszerzalności.

[Dowiedz się więcej o dostosowywaniu zestawu standardów na pulpicie nawigacyjnym zgodności z przepisami](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Kubernetes Service (wersja zapoznawcza)

Kubernetes szybko staje się nowym standardem wdrażania oprogramowania i zarządzania nim w chmurze. Kilka osób ma rozległe doświadczenie z Kubernetes i wiele koncentruje się na ogólnej inżynierii i administracji oraz zapomina aspektu zabezpieczeń. Środowisko Kubernetes należy skonfigurować uważnie, aby było bezpieczne, co sprawia, że żadne drzwi nie będące częścią ataku nie są otwarte. Security Center rozszerza swoją obsługę w przestrzeni kontenerów na jedną z najszybszych rosnących usług na platformie Azure — Azure Kubernetes Service (AKS).

Nowe możliwości w tej publicznej wersji zapoznawczej obejmują:

- **Odnajdowanie & widoczność** — ciągłe wykrywanie zarządzanych wystąpień AKS w ramach zarejestrowanych subskrypcji Security Center.
- **Zalecane zalecenia dotyczące oceny** — elementy poddaje się do działania, aby pomóc klientom w zgodności z najlepszymi rozwiązaniami w zakresie bezpieczeństwa dla AKS i zwiększyć ich bezpieczny wynik. Zalecenia obejmują elementy takie jak "kontrola dostępu oparta na rolach" powinna służyć do ograniczania dostępu do klastra usługi Kubernetes Service ".
- **Wykrywanie zagrożeń** — Analiza oparta na hoście i klastrze, taka jak "wykryto uprzywilejowany kontener".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Ocena luk w zabezpieczeniach maszyn wirtualnych (wersja zapoznawcza)

Aplikacje zainstalowane na maszynach wirtualnych mogą często mieć luki w zabezpieczeniach, które mogą spowodować naruszenie maszyny wirtualnej. Ogłaszamy, że Security Center warstwa standardowa zawiera wbudowaną ocenę luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych opłat. Ocena luk w zabezpieczeniach, obsługiwana przez Qualys w publicznej wersji zapoznawczej, umożliwia ciągłe skanowanie wszystkich zainstalowanych aplikacji na maszynie wirtualnej w celu znalezienia luk w zabezpieczeniach i zaprezentowania wyników w środowisku portalu Security Center. Security Center bierze pod uwagę wszystkie operacje wdrażania, dzięki czemu użytkownik nie wymaga dodatkowej pracy. W przyszłości planujemy udostępnienie opcji oceny luk w zabezpieczeniach w celu obsługi unikatowych potrzeb firmy klientów.

[Dowiedz się więcej o ocenach luk w zabezpieczeniach Virtual Machines platformy Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Zaawansowane zabezpieczenia danych dla serwerów SQL w systemie Azure Virtual Machines (wersja zapoznawcza)

Azure Security Center obsługa ochrony przed zagrożeniami i oceny luk w zabezpieczeniach usługi SQL baz danych uruchomionych na maszynach wirtualnych IaaS jest teraz w wersji zapoznawczej.

[Ocena luk w zabezpieczeniach](../azure-sql/database/sql-vulnerability-assessment.md) to łatwa do skonfigurowania usługa umożliwiająca odnajdywanie i śledzenie potencjalnych luk w zabezpieczeniach bazy danych oraz pomagająca w ich usuwaniu. Zapewnia wgląd w stan zabezpieczeń w ramach bezpiecznej oceny platformy Azure i zawiera kroki rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.

[Zaawansowana ochrona przed zagrożeniami](../azure-sql/database/threat-detection-overview.md) wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do programu SQL Server lub korzystania z niego. Nieustannie monitoruje bazę danych pod kątem podejrzanych działań i zapewnia zorientowane na działania alerty zabezpieczeń dotyczące nietypowych wzorców dostępu do bazy danych. Te alerty zawierają szczegóły podejrzanych działań i zalecane akcje do zbadania i ograniczenia zagrożenia.


### <a name="support-for-custom-policies-preview"></a>Obsługa zasad niestandardowych (wersja zapoznawcza)

Azure Security Center obsługuje teraz zasady niestandardowe (w wersji zapoznawczej).

Nasi klienci chcą zwiększyć swoje bieżące oceny zabezpieczeń w Security Center z uwzględnieniem własnych ocen zabezpieczeń opartych na zasadach utworzonych w programie Azure Policy. Obecnie jest to możliwe dzięki obsłudze zasad niestandardowych.

Te nowe zasady będą częścią środowiska zaleceń Security Center, bezpieczeństwa i oceny zgodności z przepisami. Dzięki obsłudze zasad niestandardowych można teraz utworzyć inicjatywę niestandardową w Azure Policy, a następnie dodać ją jako zasadę w Security Center i wizualizować ją jako rekomendacje.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Rozszerzanie zakresu Azure Security Center przy użyciu platformy dla społeczności i partnerów

Użyj Security Center, aby otrzymywać zalecenia nie tylko od firmy Microsoft, ale również z istniejących rozwiązań od partnerów, takich jak Check Point, Tenable i CyberArk z wieloma innymi integracją.  Prosty przepływ dołączania do usługi Security Center umożliwia łączenie istniejących rozwiązań z Security Center, co pozwala na wyświetlanie zaleceń stan dotyczących zabezpieczeń w jednym miejscu, uruchamianie ujednoliconych raportów i korzystanie ze wszystkich Security Center możliwości w przypadku zaleceń wbudowanych i partnerskich. Możesz również wyeksportować zalecenia dotyczące Security Center do produktów partnerskich.

[Dowiedz się więcej o skojarzeniu Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Zaawansowane integracje z eksportem zaleceń i alertów (wersja zapoznawcza)

Aby włączyć scenariusze na poziomie przedsiębiorstwa na Security Center, można teraz używać alertów Security Center i zaleceń w dodatkowych miejscach poza Azure Portal lub interfejsem API. Można je bezpośrednio wyeksportować do centrum zdarzeń i Log Analytics obszary robocze. Poniżej przedstawiono kilka przepływów pracy, które można utworzyć wokół tych nowych funkcji:

- Za pomocą funkcji eksportowania do Log Analytics obszaru roboczego można tworzyć niestandardowe pulpity nawigacyjne z Power BI.
- Za pomocą eksportu do centrum zdarzeń można eksportować Security Center alerty i zalecenia dotyczące rozwiązań Siem innych firm, do rozwiązania innych firm w czasie rzeczywistym lub Eksplorator danych platformy Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Dołączanie serwerów premium do Security Center z centrum administracyjnego systemu Windows (wersja zapoznawcza)

Centrum administracyjne systemu Windows to portal zarządzania dla serwerów z systemem Windows, które nie są wdrożone na platformie Azure, oferując im kilka funkcji zarządzania platformy Azure, takich jak aktualizacje kopii zapasowych i system. Niedawno dodaliśmy możliwość dołączenia tych serwerów spoza platformy Azure do ochrony przez funkcję ASC bezpośrednio w środowisku centrum administracyjnego systemu Windows.

Dzięki temu nowemu doświadczeniu użytkownicy będą mogli dołączyć serwer WAC do Azure Security Center i włączyć wyświetlanie alertów zabezpieczeń i zaleceń bezpośrednio w środowisku centrum administracyjnego systemu Windows.


## <a name="september-2019"></a>Wrzesień 2019 r.

Aktualizacje we wrześniu obejmują:

 - [Zarządzanie regułami przy użyciu ulepszeń kontroli aplikacji](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrola zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Zarządzanie regułami przy użyciu ulepszeń kontroli aplikacji

Ulepszono obsługę zarządzania regułami dotyczącymi maszyn wirtualnych przy użyciu adaptacyjnych kontrolek aplikacji. Azure Security Center funkcje adaptacyjnego sterowania aplikacjami ułatwiają kontrolowanie, które aplikacje mogą być uruchamiane na maszynach wirtualnych. Oprócz ogólnej poprawy zarządzania regułami nowe korzyści umożliwiają kontrolowanie, które typy plików będą chronione po dodaniu nowej reguły.

[Dowiedz się więcej na temat adaptacyjnych kontrolek aplikacji](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrola zalecenia dotyczącego zabezpieczeń kontenera przy użyciu Azure Policy

Azure Security Center zalecenia dotyczące rozwiązywania luk w zabezpieczeniach kontenerów mogą teraz być włączane lub wyłączane za pośrednictwem Azure Policy.

Aby wyświetlić włączone zasady zabezpieczeń, w Security Center otworzyć stronę zasady zabezpieczeń.


## <a name="august-2019"></a>Sierpień 2019 r.

Aktualizacje w sierpniu obejmują:

 - [Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Korygowanie z jednym kliknięciem w celu zwiększenia stan zabezpieczeń (wersja zapoznawcza)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Zarządzanie wieloma dzierżawami](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure 

Dostęp do maszyny wirtualnej just-in-Time (JIT) dla zapory platformy Azure jest teraz ogólnie dostępny. Korzystaj z niego, aby zabezpieczyć środowiska chronione przez zaporę platformy Azure oprócz środowisk chronionych sieciowej grupy zabezpieczeń.

Dostęp JIT do maszyny wirtualnej zmniejsza narażenie na ataki typu "Sieć" dzięki zapewnianiu kontrolowanego dostępu do maszyn wirtualnych tylko w razie potrzeby przy użyciu reguł sieciowej grupy zabezpieczeń i zapory platformy Azure.

Po włączeniu JIT dla maszyn wirtualnych należy utworzyć zasady określające porty, które mają być chronione, jak długo porty pozostają otwarte i zatwierdzone adresy IP, z których można uzyskać dostęp do tych portów. Te zasady pomagają zachować kontrolę nad tym, co użytkownicy mogą robić, gdy żądają dostępu.

Żądania są rejestrowane w dzienniku aktywności platformy Azure, dzięki czemu można łatwo monitorować i przeprowadzać inspekcję dostępu. Strona just in Time ułatwia również szybkie identyfikowanie istniejących maszyn wirtualnych z włączoną obsługą JIT oraz maszyn wirtualnych, na których jest zalecana metoda JIT.

[Dowiedz się więcej o zaporze platformy Azure](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Korygowanie z jednym kliknięciem w celu zwiększenia stan zabezpieczeń (wersja zapoznawcza)

Security score to narzędzie, które pomaga ocenić stan bezpieczeństwa obciążeń. Przegląda zalecenia dotyczące zabezpieczeń i ustala ich priorytety, dlatego wiesz, które zalecenia należy wykonać w pierwszej kolejności. Ułatwia to znalezienie najważniejszych luk w zabezpieczeniach, aby określić priorytety badania.

Aby uprościć korygowanie nieprawidłowych konfiguracji zabezpieczeń i pomóc szybko poprawić swój Bezpieczny wynik, dodaliśmy nową funkcję, która pozwala skorygować zalecenie dotyczące zbiorczych zasobów w jednym kliknięciem.

Ta operacja umożliwi wybranie zasobów, do których chcesz zastosować korygowanie, i uruchomienie akcji korygowania, która spowoduje skonfigurowanie ustawienia w Twoim imieniu.

Zapoznaj się ze wskazówkami dotyczącymi szybkiego rozwiązywania problemów w [przewodniku dotyczącym zabezpieczeń](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Zarządzanie wieloma dzierżawami

Security Center teraz obsługuje scenariusze zarządzania między dzierżawcami w ramach usługi Azure Lighthouse. Dzięki temu można uzyskać wgląd w stan zabezpieczeń wielu dzierżawców i zarządzać nimi w Security Center. 

[Dowiedz się więcej na temat środowisk zarządzania między dzierżawcami](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="updates-to-network-recommendations"></a>Aktualizacje zaleceń dotyczących sieci

Azure Security Center (ASC) uruchomił nowe zalecenia dotyczące sieci i udoskonalono niektóre z nich. Teraz użycie Security Center zapewnia jeszcze większą ochronę sieci dla zasobów. 

[Dowiedz się więcej na temat zaleceń dotyczących sieci](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="adaptive-network-hardening---generally-available"></a>Adaptacyjne ograniczanie sieci — ogólnie dostępne

Jednym z największych powierzchni ataku dla obciążeń działających w chmurze publicznej są połączenia z i z publicznego Internetu. Nasi klienci mogą wiedzieć, które reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) powinny być stosowane, aby upewnić się, że obciążenia platformy Azure są dostępne tylko dla wymaganych zakresów źródłowych. Korzystając z tej funkcji, Security Center uczenie ruchu sieciowego i wzorców łączności obciążeń platformy Azure i zapewnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu. Dzięki temu Klient może lepiej skonfigurować zasady dostępu do sieci i ograniczyć ich narażenie na ataki. 

[Dowiedz się więcej o ograniczaniu funkcjonalności sieci adaptacyjnej](security-center-adaptive-network-hardening.md).