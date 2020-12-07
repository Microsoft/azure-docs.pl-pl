---
title: Dodatkowa ochrona przed zagrożeniami w Azure Security Center
description: Dowiedz się więcej o ochronie przed zagrożeniami dostępną w programie Azure Security Center poza usługą Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754338"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Dodatkowa ochrona przed zagrożeniami w Azure Security Center
Również wbudowane funkcje [ochrony usługi Azure Defender](azure-defender.md)Azure Security Center oferują również następujące możliwości ochrony przed zagrożeniami.

> [!TIP]
> Aby włączyć funkcje ochrony przed zagrożeniami Security Center, należy włączyć usługę Azure Defender w ramach subskrypcji zawierającej odpowiednie obciążenia.
>
> Ochronę przed zagrożeniami dla **Azure Database for MariaDB/MySQL/PostgreSQL** można włączyć tylko na poziomie zasobów.


## <a name="threat-protection-for-azure-network-layer"></a>Ochrona przed zagrożeniami dla warstwy sieci platformy Azure <a name="network-layer"></a>
Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center korzysta z modeli uczenia maszynowego do identyfikowania i flagowania złośliwych działań w zakresie ruchu. Security Center używa również bazy danych Microsoft Threat Intelligence do wzbogacania adresów IP.

Niektóre konfiguracje sieciowe ograniczają Security Center generowania alertów dotyczących podejrzanych działań w sieci. Aby Security Center wygenerować alerty sieciowe, upewnij się, że:
- Maszyna wirtualna ma publiczny adres IP (lub znajduje się w usłudze równoważenia obciążenia z publicznym adresem IP).
- Ruch wychodzący z sieci maszyny wirtualnej nie jest blokowany przez rozwiązanie identyfikatorów zewnętrznych.

Aby uzyskać listę alertów warstwy sieci platformy Azure, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Ochrona przed zagrożeniami dla Azure Resource Manager (wersja zapoznawcza)<a name ="management-layer"></a>
Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

Listę alertów dotyczących usługi Azure Defender dla Menedżer zasobów można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-resourcemanager).


>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security. Aby skorzystać z tych analiz, należy aktywować licencję Cloud App Securityową. Jeśli masz licencję na Cloud App Security, te alerty są domyślnie włączone. Aby wyłączyć alerty:
>
> 1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
> 1. Wybierz subskrypcję, którą chcesz zmienić.
> 1. Wybierz pozycję **wykrywanie zagrożeń**.
> 1. Wyczyść pole wyboru **zezwalaj Microsoft Cloud App Security na dostęp do danych** i wybierz pozycję **Zapisz**.


>[!NOTE]
>Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft jeszcze nie wdrożona Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Cloud App Security jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej Cloud App Security. Aby uzyskać więcej informacji, zobacz [Magazyn danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).

1. Ustaw obszar roboczy, w którym jest instalowany Agent. Upewnij się, że obszar roboczy znajduje się w tej samej subskrypcji, której używasz w Security Center i że masz uprawnienia do odczytu/zapisu w obszarze roboczym.

1. Włącz **usługę Azure Defender** i wybierz pozycję **Zapisz**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)<a name="cosmos-db"></a>

Alerty Azure Cosmos DB są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont Azure Cosmos DB lub ich wykorzystania.

Aby uzyskać więcej informacji, zobacz:

* [Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Lista alertów dotyczących ochrony przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Ochrona przed zagrożeniami dla innych usług firmy Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Ochrona przed zagrożeniami dla usługi Azure WAF <a name="azure-waf"></a>

Usługa Azure Application Gateway oferuje zaporę aplikacji internetowej (WAF), która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Application Gateway WAF opiera się na podstawowym zestawie reguł 3,0 lub 2.2.9 w projekcie zabezpieczeń aplikacji sieci Web. WAF jest automatycznie aktualizowana w celu ochrony przed nowymi lukami w zabezpieczeniach. 

Jeśli masz licencję na usługę Azure WAF, alerty WAF są przesyłane strumieniowo do Security Center bez konieczności dodatkowej konfiguracji. Aby uzyskać więcej informacji na temat alertów wygenerowanych przez WAF, zobacz [zasady i reguły reguł KSR aplikacji sieci Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Ochrona przed zagrożeniami dla Azure DDoS Protection <a name="azure-ddos"></a>

Ataki rozproszonego typu "odmowa usługi" (DDoS) są znane, aby można je było łatwo wykonywać. Stają się one doskonałym problemem z zabezpieczeniami, szczególnie w przypadku przeniesienia aplikacji do chmury. 

Atak DDoS próbuje wymusić wyczerpanie zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Ataki DDoS mogą wskazywać każdy punkt końcowy, który można uzyskać za pomocą Internetu.

Aby chronić przed atakami DDoS, Kup licencję na Azure DDoS Protection i upewnij się, że korzystasz z najlepszych rozwiązań dotyczących projektowania aplikacji. DDoS Protection oferuje różne warstwy usług. Aby uzyskać więcej informacji, zobacz [Azure DDoS Protection przegląd](../ddos-protection/ddos-protection-overview.md).

Listę alertów Azure DDoS Protection można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat alertów zabezpieczeń z tych funkcji ochrony przed zagrożeniami, zobacz następujące artykuły:

* [Tabela referencyjna dla wszystkich alertów Azure Security Center](alerts-reference.md)
* [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](security-center-alerts-overview.md)
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Ciągłe eksportowanie danych Security Center](continuous-export.md)