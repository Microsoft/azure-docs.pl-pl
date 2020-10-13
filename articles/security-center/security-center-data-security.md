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
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 8a999e2b5706c04b426f758ba46f49fb9f7e3057
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91438777"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center — bezpieczeństwo danych
Aby ułatwić klientom zapobieganie zagrożeniom, wykrywanie ich i reagowanie na nie, Azure Security Center zbiera i przetwarza dane związane z zabezpieczeniami, w tym informacje o konfiguracji, metadane, dzienniki zdarzeń itd. Firma Microsoft przestrzega surowych wymogów z zakresu zabezpieczeń i zgodności — od kodu po działanie usługi.

W tym artykule wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center.

## <a name="data-sources"></a>Źródła danych
Usługa Azure Security Center analizuje dane z następujących źródeł, aby zapewnić wgląd w stan zabezpieczeń, zidentyfikować luki w zabezpieczeniach i polecić rozwiązania oraz wykryć aktywne zagrożenia:

- Usługi platformy Azure: używa informacji o konfiguracji wdrożonych usług platformy Azure, komunikując się z dostawcą zasobów tej usługi.
- Ruch sieciowy: używa próbkowanych metadanych ruchu sieciowego z infrastruktury firmy Microsoft, takich jak źródłowy i docelowy adres IP, źródłowy i docelowy port, rozmiar pakietu i protokół sieciowy.
- Rozwiązania partnerów: używa alertów zabezpieczeń z rozwiązań zintegrowanych partnerów, takich jak zapory i rozwiązania do ochrony przed złośliwym oprogramowaniem.
- Twoje Virtual Machines i serwery: używa informacji o konfiguracji i informacji o zdarzeniach zabezpieczeń, takich jak dzienniki zdarzeń systemu Windows i inspekcji, dzienniki usług IIS i komunikaty dziennika systemowego z maszyn wirtualnych. Ponadto w przypadku tworzenia alertu usługa Azure Security Center może wygenerować migawkę dysku uwzględnionej maszyny wirtualnej i wyodrębnić artefakty maszyny powiązane z alertem z dysku maszyny wirtualnej, takie jak plik rejestru, dla celów przeprowadzania ekspertyz.


## <a name="data-protection"></a>Ochrona danych
**Podział danych**: dane są logicznie oddzielone dla każdego składnika w całej usłudze. Wszystkie dane są otagowane informacjami o organizacji. To tagowanie jest obecne przez cały cykl życia danych i jest wymuszane w każdej warstwie usługi.

**Dostęp do danych**: aby zapewnić zalecenia dotyczące zabezpieczeń i zbadać potencjalne zagrożenia bezpieczeństwa, personel firmy Microsoft może uzyskać dostęp do informacji zbieranych lub analizowanych przez usługi platformy Azure, w tym zdarzeń tworzenia procesów, migawek dysków maszyn wirtualnych i artefaktów, które mogą przypadkowo obejmować dane klienta lub dane osobowe z maszyn wirtualnych. Stosujemy się do [warunków korzystania z usług Microsoft Online Services i zasad zachowania poufności informacji](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), które stanowią, że firma Microsoft nie będzie używać danych klienta ani informacji pochodnych do żadnych celów reklamowych ani zbliżonych celów komercyjnych. Danych klienta używamy tylko w razie potrzeby w celu świadczenia usług platformy Azure, włączając w to cele zgodne ze świadczeniem tych usług. Użytkownik zachowuje wszystkie uprawnienia do danych klienta.

**Użycie danych**: firma Microsoft używa wzorców i analizy zagrożeń obecnych w wielu dzierżawach, aby zwiększyć możliwości wykrywania zagrożeń i zapobiegania im — robimy to zgodnie ze zobowiązaniami co do prywatności opisanymi w [zasadach zachowania poufności informacji](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Lokalizacja danych

**Obszary robocze**: obszar roboczy jest określony dla następujących Georegiony, a dane zbierane z maszyn wirtualnych platformy Azure, w tym typy danych alertów, są przechowywane w najbliższym obszarze roboczym.

| Lokalizacja geograficzna maszyny wirtualnej                              | Lokalizacja geograficzna obszaru roboczego |
|-------------------------------------|---------------|
| Stany Zjednoczone, Brazylia, Afryka Południowa | Stany Zjednoczone |
| Kanada                              | Kanada        |
| Europa (z wyjątkiem Zjednoczonego Królestwa)   | Europa        |
| Zjednoczone Królestwo                      | Zjednoczone Królestwo |
| Azja (z wyjątkiem Indii, Japonia, Korea, Chiny)   | Azja i Pacyfik  |
| Korea                              | Azja i Pacyfik  |
| Indie                               | Indie         |
| Japonia                               | Japonia         |
| Chiny                               | Chiny         |
| Australia                           | Australia     |


Migawki dysków maszyny wirtualnej są przechowywane na tym samym koncie magazynu jako dysk maszyny wirtualnej.

Dla maszyn wirtualnych i serwerów działających w innych środowiskach, na przykład lokalnie, można określić obszar roboczy i region, w którym będą przechowywane zebrane dane.

**Magazyn usługi Azure Security Center**: informacje dotyczące alertów zabezpieczeń, w tym alerty partnerów, są przechowywane regionalnie zgodnie z lokalizacją powiązanego zasobu platformy Azure, a informacje dotyczące stanu kondycji zabezpieczeń i zalecenia są przechowywane centralnie na terenie Stanów Zjednoczonych lub Europy zgodnie z lokalizacją klienta. Artefakty maszyny są przechowywane centralnie w tym samym regionie, co maszyna wirtualna.

## <a name="manage-data-collection-from-virtual-machines"></a>Zarządzanie zbieraniem danych z maszyn wirtualnych

W przypadku włączenia usługi Security Center na platformie Azure zbieranie danych jest włączone dla każdej subskrypcji platformy Azure. Zbieranie danych można również włączyć dla subskrypcji w sekcji Zasady zabezpieczeń usługi Azure Security Center. Gdy zbieranie danych jest włączone, Azure Security Center inicjuje agenta Log Analytics we wszystkich istniejących obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych.
Agent Log Analytics skanuje w poszukiwaniu różnych konfiguracji związanych z zabezpieczeniami i zdarzeń do śledzenia [zdarzeń systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Ponadto system operacyjny będzie zgłaszać zdarzenia dziennika zdarzeń w trakcie pracy maszyny. Przykłady takich danych to typ systemu operacyjnego i jego wersja, dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, nazwa maszyny, adresy IP, zalogowany użytkownik i identyfikator dzierżawy. Agent Log Analytics odczytuje wpisy dziennika zdarzeń i śledzenia ETW i kopiuje je do obszarów roboczych na potrzeby analizy. Agent Log Analytics włącza również zdarzenia tworzenia procesów i inspekcje wiersza polecenia.

Jeśli nie korzystasz z usługi Azure Defender, możesz również wyłączyć zbieranie danych z maszyn wirtualnych w ramach zasad zabezpieczeń. Zbieranie danych jest wymagane w przypadku subskrypcji chronionych przez usługę Azure Defender. Kolekcja artefaktów i migawki dysków maszyny wirtualnej będzie nadal włączona, nawet jeśli wyłączono zbieranie danych.

## <a name="data-consumption"></a>Użycie danych

Klienci mogą używać danych związanych z usługą Security Center pochodzących z różnych strumieni danych, jak pokazano poniżej:

* **Aktywność platformy Azure**: wszystkie alerty zabezpieczeń, zatwierdzone Security Center żądań [just-in-Time](security-center-just-in-time.md) oraz wszystkie alerty wygenerowane przez [adaptacyjne kontrolki aplikacji](security-center-adaptive-application.md).
* **Dzienniki Azure monitor**: wszystkie alerty zabezpieczeń.


> [!NOTE]
> Zalecenia dotyczące zabezpieczeń można także wykorzystywać za pośrednictwem interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Security Resource Provider REST API Reference (Dokumentacja interfejsu API REST dostawcy zasobów zabezpieczeń)](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx).

## <a name="see-also"></a>Zobacz też
W tym dokumencie wyjaśniono, jak zarządzane i chronione są dane w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat usługi Azure Security Center, zobacz:

* [Azure Security Center Przewodnik planowania i](security-center-planning-and-operations-guide.md) obsługi — Dowiedz się, jak planować i zrozumieć zagadnienia dotyczące projektowania, aby przyjąć Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat zarządzania alertami zabezpieczeń i reagowania na nie
* [Monitorowanie rozwiązań partnerskich za pomocą Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Blog dotyczący zabezpieczeń platformy Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/) — wpisy w blogu dotyczące zabezpieczeń i zgodności platformy Azure
