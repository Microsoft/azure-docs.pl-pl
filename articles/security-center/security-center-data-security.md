---
title: Azure Security Center — bezpieczeństwo danych | Microsoft Docs
description: W tym dokumencie wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 8e92c893d97ce9fadfa2d40476631c2b9ba5d177
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999262"
---
# <a name="azure-security-center-data-security"></a>Zabezpieczenia danych Azure Security Center

Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, Azure Security Center zbiera i przetwarza dane związane z zabezpieczeniami, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń itd. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

W tym artykule opisano sposób zarządzania danymi i ich ochrony w Security Center.

## <a name="data-sources"></a>Źródła danych
Security Center analizuje dane z następujących źródeł, aby zapewnić widoczność stanu zabezpieczeń, identyfikować luki w zabezpieczeniach i zalecać środki zaradcze oraz wykrywać aktywne zagrożenia:

- **Usługi platformy Azure**: program korzysta z informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.
- **Ruch sieciowy**: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy/docelowy adres IP/port, rozmiar pakietu i protokół sieciowy.
- **Rozwiązania partnerskie**: program używa alertów zabezpieczeń zintegrowanych rozwiązań partnerskich, takich jak zapory i rozwiązania chroniące przed złośliwym kodem.
- **Maszyny**: program używa szczegółów konfiguracji i informacji o zdarzeniach zabezpieczeń, takich jak dzienniki zdarzeń i inspekcji systemu Windows oraz komunikaty dziennika systemowego z maszyn.


## <a name="data-protection"></a>Ochrona danych

### <a name="data-segregation"></a>Podział danych
Dane są logicznie oddzielone dla każdego składnika w ramach usługi. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

### <a name="data-access"></a>Dostęp do danych
Aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym zdarzenia tworzenia procesów i inne artefakty, które mogą przypadkowo obejmować dane klienta lub dane osobowe z maszyn. 

Przestrzegamy [aneksu do ochrony danych w witrynie Microsoft Online Services](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), który stwierdza, że firma Microsoft nie będzie używać danych klienta ani informacji pochodnych dla jakichkolwiek reklam lub podobnych celów komercyjnych. Danych klienta używamy tylko w razie potrzeby w celu świadczenia usług platformy Azure, włączając w to cele zgodne ze świadczeniem tych usług. Użytkownik zachowuje wszystkie uprawnienia do danych klienta.

### <a name="data-use"></a>Użycie danych
Firma Microsoft używa wzorców i analizy zagrożeń widocznych dla wielu dzierżawców w celu zwiększenia możliwości zapobiegania i wykrywania. jest to zgodne z zobowiązaniami dotyczącymi ochrony prywatności opisanymi w zasadach [zachowania poufności informacji](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Zarządzanie zbieraniem danych z maszyn
W przypadku włączenia usługi Security Center na platformie Azure zbieranie danych jest włączone dla każdej subskrypcji platformy Azure. Możesz również włączyć zbieranie danych dla subskrypcji w Security Center. Gdy zbieranie danych jest włączone, Security Center inicjuje agenta Log Analytics we wszystkich istniejących obsługiwanych maszynach wirtualnych platformy Azure i nowych, które zostały utworzone.

Agent Log Analytics skanuje w poszukiwaniu różnych konfiguracji związanych z zabezpieczeniami i zdarzeń do śledzenia [zdarzeń systemu Windows](https://docs.microsoft.com/windows/win32/etw/event-tracing-portal) (ETW). Ponadto system operacyjny będzie zgłaszać zdarzenia dziennika zdarzeń w trakcie pracy maszyny. Przykłady takich danych to typ systemu operacyjnego i jego wersja, dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwa maszyny, adresy IP, zalogowany użytkownik i identyfikator dzierżawy. Agent Log Analytics odczytuje wpisy dziennika zdarzeń i śledzenia ETW i kopiuje je do obszarów roboczych na potrzeby analizy. Agent Log Analytics włącza również zdarzenia tworzenia procesów i inspekcje wiersza polecenia.

Jeśli nie korzystasz z usługi Azure Defender, możesz również wyłączyć zbieranie danych z maszyn wirtualnych w ramach zasad zabezpieczeń. Zbieranie danych jest wymagane w przypadku subskrypcji chronionych przez usługę Azure Defender. Kolekcja artefaktów i migawki dysków maszyny wirtualnej będzie nadal włączona, nawet jeśli wyłączono zbieranie danych.

Można określić obszar roboczy i region, w którym są przechowywane dane zbierane z maszyn. Domyślnie dane zbierane z maszyn są przechowywane w najbliższym obszarze roboczym, jak pokazano w poniższej tabeli:

| Lokalizacja geograficzna maszyny wirtualnej                              | Lokalizacja geograficzna obszaru roboczego |
|-------------------------------------|---------------|
| Stany Zjednoczone, Brazylia, Afryka Południowa | Stany Zjednoczone |
| Kanada                              | Canada        |
| Europa (z wyjątkiem Zjednoczonego Królestwa)   | Europa        |
| Zjednoczone Królestwo                      | Zjednoczone Królestwo |
| Azja (z wyjątkiem Indii, Japonia, Korea, Chiny)   | Azja i Pacyfik  |
| Korea                              | Azja i Pacyfik  |
| Indie                               | Indie         |
| Japonia                               | Japonia         |
| Chiny                               | Chiny         |
| Australia                           | Australia     |
|||

> [!NOTE]
> **Usługa Azure Defender dla magazynu** przechowuje artefakty w regionach zgodnie z lokalizacją powiązanego zasobu platformy Azure. Dowiedz się więcej w temacie [wprowadzenie do usługi Azure Defender dla magazynu](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Użycie danych

Klienci mogą uzyskać dostęp do Security Center powiązanych danych z następujących strumieni danych:


|Strumień  |Typy danych  |
|---------|---------|
|[Dziennik aktywności platformy Azure](../azure-monitor/platform/activity-log.md)| Wszystkie alerty zabezpieczeń, zatwierdzone Security Center żądania dostępu [just in Time](security-center-just-in-time.md) i wszystkie alerty wygenerowane przez [adaptacyjne kontrolki aplikacji](security-center-adaptive-application.md) |
|[Dzienniki Azure Monitor](../azure-monitor/platform/data-platform.md)|Wszystkie alerty zabezpieczeń.|
|[Azure Resource Graph](../governance/resource-graph/overview.md)|Alerty zabezpieczeń, zalecenia dotyczące zabezpieczeń, wyniki oceny luk w zabezpieczeniach, bezpieczne informacje o punktacji, stan kontroli zgodności i inne.|
|[Interfejs API REST usługi Azure Security Center](https://docs.microsoft.com/rest/api/securitycenter/)|Alerty zabezpieczeń, zalecenia dotyczące zabezpieczeń i inne elementy. .|
|||

## <a name="next-steps"></a>Następne kroki

W tym dokumencie wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center. 

Aby dowiedzieć się więcej na temat usługi Azure Security Center, zobacz:

- [Co to jest usługa Azure Security Center?](security-center-introduction.md)
