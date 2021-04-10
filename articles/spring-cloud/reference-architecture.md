---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Architektura referencyjna chmury ze sprężyną Azure
ms.author: akaleshian
ms.service: spring-cloud
description: Ta architektura referencyjna jest podstawą przy użyciu typowego projektu centrum przedsiębiorstwa i satelity do korzystania z chmury wiosennej platformy Azure.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878395"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Architektura referencyjna chmury ze sprężyną Azure

Ta architektura referencyjna jest podstawą przy użyciu typowego projektu centrum przedsiębiorstwa i satelity do korzystania z chmury wiosennej platformy Azure. W projekcie chmura sprężynowa platformy Azure jest wdrażana w jednej szprychie, która jest zależna od usług udostępnionych hostowanych w centrum. Architektura jest skompilowana ze składnikami, aby osiągnąć założenia w [strukturze Well-Architected Microsoft Azure][16].

Aby zapoznać się z implementacją tej architektury, zobacz repozytorium [architektury referencyjnej chmury Azure wiosny][10] w witrynie GitHub.

Opcje wdrażania dla tej architektury obejmują Azure Resource Manager (ARM), Terraform i interfejs wiersza polecenia platformy Azure. Artefakty w tym repozytorium stanowią podstawę, którą można dostosować do danego środowiska. Możesz grupować zasoby, takie jak Zapora platformy Azure lub Application Gateway, do różnych grup zasobów lub subskrypcji. Takie grupowanie pomaga utrzymać różne funkcje, takie jak infrastruktura IT, zabezpieczenia, zespoły aplikacji biznesowej i tak dalej.

## <a name="planning-the-address-space"></a>Planowanie przestrzeni adresowej

Chmura sprężynowa platformy Azure wymaga dwóch dedykowanych podsieci:

* Środowisko uruchomieniowe usługi
* Aplikacje ze sprężyną rozruchu

Każda z tych podsieci wymaga dedykowanego klastra. Nie można współużytkować tych samych podsieci w wielu klastrach. Minimalny rozmiar każdej podsieci to/28. Liczba wystąpień aplikacji, które mogą być obsługiwane przez chmurę Azure wiosenną, zależy od rozmiaru podsieci. Szczegółowe wymagania dotyczące Virtual Network (VNET) można znaleźć w sekcji [wymagania dotyczące sieci wirtualnej][11] w artykule [Wdrażanie chmury z systemem Azure wiosną w sieci wirtualnej][17].

> [!WARNING]
> Wybrany rozmiar podsieci nie może nakładać się na istniejącą przestrzeń adresową sieci wirtualnej i nie powinien pokrywać się z żadnym z zakresów adresów podsieci równorzędnych lub lokalnych.

## <a name="use-cases"></a>Przypadki zastosowań

Przykładowe typowe zastosowania tej architektury:

* Aplikacje wewnętrzne wdrożone w środowiskach chmury hybrydowej
* Aplikacje połączone zewnętrznie

Te przypadki użycia są podobne z wyjątkiem reguł związanych z zabezpieczeniami i ruchem sieciowym. Ta architektura została zaprojektowana tak, aby obsługiwała wszystkie szczegóły każdego z nich.

## <a name="private-applications"></a>Aplikacje prywatne

Poniższa lista zawiera opis wymagań dotyczących infrastruktury dla aplikacji prywatnych. Te wymagania są typowe w środowiskach o wysokim stopniu regulowanej.

* Brak bezpośrednich wychodzących do publicznej sieci Internet z wyjątkiem ruchu płaszczyzny kontroli.
* Ruch wychodzący powinien przechodzić przez centralne sieciowe urządzenie wirtualne (urządzenie WUS) (na przykład Zapora platformy Azure).
* Dane przechowywane w spoczynku powinny być szyfrowane.
* Przesyłane dane powinny być szyfrowane.
* Można używać potoków wdrażania DevOps (na przykład Azure DevOps) i wymagać łączności sieciowej z chmurą wiosenną platformy Azure.
* Inne podejście do zabezpieczeń bez zaufania firmy Microsoft wymaga, aby wpisy tajne, certyfikaty i poświadczenia były przechowywane w bezpiecznym magazynie. Zalecana usługa jest Azure Key Vault.
* Rekordy usługi nazw domen hosta aplikacji (DNS) powinny być przechowywane w usłudze Azure Prywatna strefa DNS.
* Rozpoznawanie nazw hostów lokalnych i w chmurze powinno być dwukierunkowe.
* Należy wymusić zgodność z co najmniej jednym wzorcem zabezpieczeń.
* Zależności usługi platformy Azure powinny komunikować się za pomocą punktów końcowych usługi lub łącza prywatnego.
* Grupy zasobów zarządzane przez wdrożenie chmurowe platformy Azure nie mogą być modyfikowane.
* Podsieci zarządzane przez wdrożenie chmurowe platformy Azure nie mogą być modyfikowane.
* Podsieć musi mieć tylko jedno wystąpienie chmury wiosennej platformy Azure.
* Jeśli [serwer konfiguracji chmurowej platformy Azure][8] jest używany do ładowania właściwości konfiguracji z repozytorium, repozytorium musi być prywatne.

Na poniższej liście przedstawiono składniki, które składają się na projekt:

* Sieć lokalna
  * Usługa nazw domen (DNS)
  * Brama
* Subskrypcja centrum
  * Podsieć zapory platformy Azure
  * Podsieć Application Gateway
  * Podsieć usług udostępnionych
* Połączona subskrypcja
  * Virtual Network element równorzędny
  * Podsieć usługi Azure bastionu

Na poniższej liście opisano usługi platformy Azure w tej architekturze referencyjnej:

* [Chmura Wiosenna platformy Azure][1]: zarządzana usługa, która została zaprojektowana i zoptymalizowana pod kątem aplikacji do rozruchowych sieci opartych na języku Java i. Oparte na sieci aplikacje [steeltoe][9] .

* [Azure Key Vault][2]: Usługa zarządzania poświadczeniami w ramach sprzętu, która ma ścisłą integrację z usługami tożsamości firmy Microsoft i zasobami obliczeniowymi.

* [Azure monitor][3]: cały pakiet usług monitorowania dla aplikacji wdrażanych zarówno na platformie Azure, jak i lokalnie.

* [Azure Security Center][4]: ujednolicony system zarządzania zabezpieczeniami i ochrony przed zagrożeniami dla obciążeń lokalnych, wielu chmur i platformy Azure.

* [Azure Pipelines][5]: w pełni oferowana usługa ciągłej integracji/ciągłego tworzenia oprogramowania (Ci/CD), która umożliwia automatyczne wdrażanie zaktualizowanych aplikacji sieci przewodowego rozruchu w chmurze Azure wiosennej.

Poniższy diagram przedstawia dobrze zaprojektowany projekt Hub i szprych, który spełnia powyższe wymagania:

![Diagram architektury referencyjnej dla aplikacji prywatnych](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Aplikacje publiczne

Poniższa lista zawiera opis wymagań dotyczących infrastruktury dla aplikacji publicznych. Te wymagania są typowe w środowiskach o wysokim stopniu regulowanej.

* Ruch przychodzący powinien być zarządzany przez co najmniej Application Gateway lub z przodu platformy Azure.
* Należy włączyć Standard Azure DDoS Protection.
* Brak bezpośrednich wychodzących do publicznej sieci Internet z wyjątkiem ruchu płaszczyzny kontroli.
* Ruch wychodzący powinien przechodzić do centralnego sieciowego urządzenia wirtualnego (urządzenie WUS) (na przykład zapory platformy Azure).
* Dane przechowywane w spoczynku powinny być szyfrowane.
* Przesyłane dane powinny być szyfrowane.
* Można używać potoków wdrażania DevOps (na przykład Azure DevOps) i wymagać łączności sieciowej z chmurą wiosenną platformy Azure.
* Inne podejście do zabezpieczeń bez zaufania firmy Microsoft wymaga, aby wpisy tajne, certyfikaty i poświadczenia były przechowywane w bezpiecznym magazynie. Zalecana usługa jest Azure Key Vault.
* Rekordy DNS hosta aplikacji powinny być przechowywane w usłudze Azure Prywatna strefa DNS.
* Adresy IP z obsługą routingu powinny być przechowywane w publicznej usłudze DNS platformy Azure.
* Rozpoznawanie nazw hostów lokalnych i w chmurze powinno być dwukierunkowe.
* Należy wymusić zgodność z co najmniej jednym wzorcem zabezpieczeń.
* Zależności usługi platformy Azure powinny komunikować się za pomocą punktów końcowych usługi lub łącza prywatnego.
* Grupy zasobów zarządzane przez wdrożenie chmurowe platformy Azure nie mogą być modyfikowane.
* Podsieci zarządzane przez wdrożenie chmurowe platformy Azure nie mogą być modyfikowane.
* Podsieć musi mieć tylko jedno wystąpienie chmury wiosennej platformy Azure.

Na poniższej liście przedstawiono składniki, które składają się na projekt:

* Sieć lokalna
  * Usługa nazw domen (DNS)
  * Brama
* Subskrypcja centrum
  * Podsieć zapory platformy Azure
  * Podsieć Application Gateway
  * Podsieć usług udostępnionych
* Połączona subskrypcja
  * Virtual Network element równorzędny
  * Podsieć usługi Azure bastionu

Na poniższej liście opisano usługi platformy Azure w tej architekturze referencyjnej:

* [Chmura Wiosenna platformy Azure][1]: zarządzana usługa, która została zaprojektowana i zoptymalizowana pod kątem aplikacji do rozruchowych sieci opartych na języku Java i. Oparte na sieci aplikacje [steeltoe][9] .

* [Azure Key Vault][2]: Usługa zarządzania poświadczeniami w ramach sprzętu, która ma ścisłą integrację z usługami tożsamości firmy Microsoft i zasobami obliczeniowymi.

* [Azure monitor][3]: cały pakiet usług monitorowania dla aplikacji wdrażanych zarówno na platformie Azure, jak i lokalnie.

* [Azure Security Center][4]: ujednolicony system zarządzania zabezpieczeniami i ochrony przed zagrożeniami dla obciążeń lokalnych, wielu chmur i platformy Azure.

* [Azure Pipelines][5]: w pełni oferowana usługa ciągłej integracji/ciągłego tworzenia oprogramowania (Ci/CD), która umożliwia automatyczne wdrażanie zaktualizowanych aplikacji sieci przewodowego rozruchu w chmurze Azure wiosennej.

* [Azure Application Gateway][6]: moduł równoważenia obciążenia odpowiedzialny za ruch aplikacji z odciążeniem Transport Layer Security (TLS) działający w warstwie 7.

* [Zapora aplikacji platformy Azure][7]: funkcja platformy Azure Application Gateway, która zapewnia scentralizowaną ochronę aplikacji przed typowymi atakami i lukami w zabezpieczeniach.

Poniższy diagram przedstawia dobrze zaprojektowany projekt Hub i szprych, który spełnia powyższe wymagania:

![Diagram architektury referencyjnej dla aplikacji publicznych](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Łączność lokalna w chmurze platformy Azure

Aplikacje działające w chmurze Azure wiosną mogą komunikować się z różnymi zasobami platformy Azure, lokalnymi i zewnętrznymi. Korzystając z projektu gwiazdy, aplikacje mogą kierować ruchem zewnętrznym lub do sieci lokalnej przy użyciu trasy Express lub wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja.

## <a name="azure-well-architected-framework-considerations"></a>Zagadnienia dotyczące platformy Azure Well-Architected Framework

[Platforma Azure Well-Architected Framework][16] to zbiór założenia identyfikatorów, które mają być stosowane w celu ustanowienia silnej podstawy infrastruktury. Struktura zawiera następujące kategorie: Optymalizacja kosztów, doskonałości operacyjną, wydajność, niezawodność i bezpieczeństwo.

### <a name="cost-optimization"></a>Optymalizacja kosztów

Ze względu na charakter rozproszonego projektu systemu usługa Infrastructure rozwojem jest rzeczywistością. Ta rzeczywistość powoduje nieoczekiwane i niekontrolowane koszty. Chmura Wiosenna platformy Azure jest tworzona przy użyciu składników, które są skalowane w taki sposób, aby spełniały zapotrzebowanie i zoptymalizować koszt. Rdzeń tej architektury jest usługą Azure Kubernetes Service (AKS). Usługa została zaprojektowana w celu zredukowania złożoności i obciążenia operacyjnego związanego z zarządzaniem Kubernetes, co obejmuje zwiększenie kosztu operacyjnego klastra.

Różne aplikacje i typy aplikacji można wdrażać w jednym wystąpieniu chmury wiosennej platformy Azure. Usługa obsługuje automatyczne skalowanie aplikacji wyzwalanych przez metryki lub harmonogramy, które mogą poprawić wykorzystanie i efektywność kosztów.

Możesz również użyć Application Insights i Azure Monitor do obniżenia kosztów operacyjnych. Mając widoczność zapewnianą przez kompleksowe rozwiązanie rejestrowania, można zaimplementować automatyzację, aby skalować składniki systemu w czasie rzeczywistym. Możesz również analizować dane dziennika, aby ujawnić nieefektywność w kodzie aplikacji, który można rozwiązać, aby zwiększyć całkowity koszt i wydajność systemu.

### <a name="operational-excellence"></a>Efektywność operacyjna

Chmura Wiosnowa platformy Azure dotyczy wielu aspektów doskonałości operacyjnej. Te aspekty można połączyć w celu zapewnienia wydajnego działania usługi w środowiskach produkcyjnych, zgodnie z opisem na poniższej liście:

* Za pomocą Azure Pipelines można zagwarantować, że wdrożenia są niezawodne i spójne, jednocześnie pomagając uniknąć błędów ludzkich.
* Za pomocą Azure Monitor i Application Insights można przechowywać dane dzienników i danych telemetrycznych.
  Można ocenić zebrane dane dziennika i metryki w celu zapewnienia kondycji i wydajności aplikacji. Monitorowanie wydajności aplikacji (APM) jest w pełni zintegrowane z usługą za pomocą agenta języka Java. Ten agent zapewnia wgląd we wszystkie wdrożone aplikacje i zależności bez konieczności dodatkowego kodu. Aby uzyskać więcej informacji, zobacz wpis w blogu bez [nadzoru Monitoruj aplikacje i zależności w chmurze Azure wiosennej][15].
* Za pomocą Azure Security Center można zagwarantować, że aplikacje będą obsługiwać zabezpieczenia, zapewniając platformę do analizowania i oceny dostarczonych danych.
* Usługa obsługuje różne wzorce wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska tymczasowego w chmurze Azure wiosennej][14].

### <a name="reliability"></a>Niezawodność

Chmura sprężynowa platformy Azure została zaprojektowana z AKS jako komponentem fundamentowym. Chociaż AKS zapewnia poziom odporności za pomocą klastrowania, ta architektura referencyjna obejmuje usługi i zagadnienia dotyczące architektury w celu zwiększenia dostępności aplikacji w przypadku awarii składnika.

Na podstawie dobrze zdefiniowanego projektu gwiazdy, podstawą tej architektury zapewnia, że można wdrożyć ją w wielu regionach. W przypadku użycia aplikacji prywatnych architektura korzysta z usługi Azure Prywatna strefa DNS, aby zapewnić ciągłą dostępność podczas awarii geograficznej. W przypadku użycia aplikacji publicznej usługa Azure Front drzwiczki i Azure Application Gateway zapewnia dostępność.

### <a name="security"></a>Zabezpieczenia

Zabezpieczenia tej architektury są rozwiązywane na podstawie zgodności z kontrolkami i kontrolami porównawczymi określonymi przez branżę. Kontrolki w tej architekturze pochodzą z [macierzy kontroli w chmurze][19] (CCM) przez [usługi Cloud Security Alliance][18] (CSA) i [testu porównawczego Microsoft Azure][20] (MAFB) w [Centrum zabezpieczeń internetowych][21] (CIS). W zastosowaniu kontrolek fokus dotyczy podstawowych zasad projektowania zabezpieczeń, sieci i zabezpieczeń aplikacji. Jest odpowiedzialny za obsługę zasad projektowania tożsamości, zarządzania dostępem i magazynów w odniesieniu do infrastruktury docelowej.

#### <a name="governance"></a>Nadzór

Podstawowym aspektem nadzoru, który ten adres architektury jest dzielony przez izolację zasobów sieciowych. W przypadku CCM, DC-08 zaleca się kontrolę ruchu przychodzącego i wychodzącego dla centrum danych. Aby zapewnić kontrolę, architektura używa projektu gwiazdy i gwiazdy przy użyciu sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) do filtrowania ruchu wschód-zachód między zasobami. Architektura filtruje również ruch między centralnymi usługami w centrum i zasobami w szprychie. Architektura używa wystąpienia zapory platformy Azure do zarządzania ruchem internetowym i zasobami w ramach architektury.

Na poniższej liście przedstawiono kontrolkę, która rozwiązuje zabezpieczenia centrum danych w tym odwołaniu:

| Identyfikator kontrolki CSA CCM | Domena kontrolki CSA CCM |
| :----------------- | :----------------------|
| KONTROLERY DOMENY — 08 | Wpis zabezpieczenia dla osób nieautoryzowanych w centrum danych |

#### <a name="network"></a>Sieć

Projektowanie sieci obsługujące tę architekturę pochodzi od tradycyjnego modelu gwiazdy i satelity. Ta decyzja gwarantuje, że izolacja sieci jest podstawą konstrukcji. Kontrolka CCM użycie japońskich ideograficznych-06 zaleca, aby ruch między sieciami i maszynami wirtualnymi był ograniczony i monitorowany między zaufanymi i niezaufanymi środowiskami. Ta architektura przyjmuje kontrolę przez implementację sieciowych grup zabezpieczeń dla ruchu Wschodnie-Zachodnie i zaporę platformy Azure dla ruchu północ-południe. Kontrolka CCM IPY-04 zaleca, aby infrastruktura używała bezpiecznych protokołów sieciowych do wymiany danych między usługami. Usługi platformy Azure obsługujące tę architekturę korzystają ze standardowych protokołów Secure, takich jak TLS dla protokołu HTTP i SQL.

Na poniższej liście przedstawiono kontrolki CCM, które dotyczą zabezpieczeń sieci w tym odwołaniu:

| Identyfikator kontrolki CSA CCM | Domena kontrolki CSA CCM |
| :----------------- | :----------------------|
| IPY – 04             | Protokoły sieciowe      |
| UŻYCIE JAPOŃSKICH IDEOGRAFICZNYCH — 06             | Bezpieczeństwo sieci       |

Implementacja sieci jest bardziej zabezpieczona przez Definiowanie formantów z MAFB. Kontrolki zapewniają, że ruch do środowiska jest ograniczony przez publiczny Internet.

Na poniższej liście przedstawiono kontrolki CIS, które dotyczą zabezpieczeń sieci w tym odwołaniu:

| Identyfikator kontrolki CIS | Opis kontrolki CIS |
| :------------- | :---------------------- |
| 6,2 | Upewnij się, że dostęp SSH jest ograniczony z Internetu. |
| 6.3 | Upewnij się, że żadna baza danych SQL nie zezwala na ruch przychodzący 0.0.0.0/0 (dowolny adres IP). |
| 6.5 | Upewnij się, że Network Watcher jest włączona. |
| 6.6 | Upewnij się, że ruch przychodzący przy użyciu protokołu UDP jest ograniczony z Internetu. |

Chmura sprężynowa platformy Azure wymaga ruchu związanego z zarządzaniem z platformy Azure po wdrożeniu w zabezpieczonym środowisku. Aby to osiągnąć, należy zezwolić na reguły sieci i aplikacji wymienione w obszarze [obowiązków klienta na potrzeby uruchamiania chmury wiosennej platformy Azure w sieci wirtualnej](./spring-cloud-vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Zabezpieczenia aplikacji

Ten podmiot zabezpieczeń projektu obejmuje podstawowe składniki tożsamości, ochrony danych, zarządzania kluczami i konfiguracji aplikacji. Zgodnie z projektem aplikacja wdrożona w chmurze Azure wiosennej jest uruchamiana z użyciem najniższych uprawnień wymaganych do działania. Zestaw kontroli autoryzacji jest bezpośrednio związany z ochroną danych podczas korzystania z usługi. Zarządzanie kluczami wzmacnia to podejście zabezpieczeń aplikacji warstwowych.

Na poniższej liście przedstawiono kontrolki CCM, które dotyczą zarządzania kluczami w tym odwołaniu:

| Identyfikator kontrolki CSA CCM | Domena kontrolki CSA CCM |
| :----------------- | :--------------------- |
| EKM – 01 | Uprawnienie do szyfrowania i zarządzania kluczami |
| EKM-02 | Szyfrowanie i generowanie klucza zarządzania kluczami |
| EKM-03 | Szyfrowanie i ochrona danych wrażliwych na zarządzanie kluczami |
| EKM – 04 | Szyfrowanie i magazynowanie zarządzania kluczami oraz dostęp |

Z programu CCM, EKM-02 i EKM-03 zaleca się zasady i procedury umożliwiające zarządzanie kluczami oraz używanie protokołów szyfrowania do ochrony poufnych danych. EKM-01 zaleca, aby wszystkie klucze kryptograficzne miały rozpoznawalnych właścicieli, aby można było nimi zarządzać. EKM-04 zaleca korzystanie z algorytmów standardowych.

Na poniższej liście przedstawiono kontrolki WNP, które dotyczą zarządzania kluczami w tym odwołaniu:

| Identyfikator kontrolki CIS | Opis kontrolki CIS |
| :------------- | :---------------------- |
| 8.1 | Upewnij się, że data wygaśnięcia została ustawiona na wszystkie klucze. |
| 8.2 | Upewnij się, że data wygaśnięcia jest ustawiona dla wszystkich wpisów tajnych. |
| 8.4 | Upewnij się, że magazyn kluczy jest możliwy do odzyskania. |

Formanty CIS 8,1 i 8,2 zalecają Ustawianie dat wygaśnięcia dla poświadczeń, aby upewnić się, że obracanie jest wymuszane. Usługa CIS Control 8,4 zapewnia, że zawartość magazynu kluczy może zostać przywrócona, aby zachować ciągłość działania.

Aspekty zabezpieczeń aplikacji ustawiają podstawę do korzystania z tej architektury referencyjnej do obsługi obciążenia sprężynowego na platformie Azure.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tą architekturą referencyjną za pomocą wdrożeń ARM, Terraform i interfejsu wiersza polecenia platformy Azure dostępnych w repozytorium [architektury referencyjnej chmury wiosennej platformy Azure][10] .

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/