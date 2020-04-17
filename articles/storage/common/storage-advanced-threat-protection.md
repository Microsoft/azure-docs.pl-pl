---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
titleSuffix: Azure Storage
description: Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage, aby wykrywać anomalie w aktywności konta i otrzymywać powiadomienia o potencjalnie szkodliwych próbach uzyskania dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: f390aececdbf9a20f191279892c1856332f2aa8e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482098"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage zapewnia dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Ta warstwa ochrony umożliwia rozwiązywanie zagrożeń bez bycia ekspertem w dziedzinie bezpieczeństwa lub zarządzania systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy występują anomalie w działaniu. Te alerty zabezpieczeń są zintegrowane z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane za pośrednictwem poczty e-mail do administratorów subskrypcji ze szczegółami dotyczące podejrzanych działań i zaleceniami dotyczącymi sposobu badania i korygować zagrożenia.

Usługa pozyskiwania dzienników diagnostycznych odczytu, zapisu i usuwania żądań do magazynu obiektów Blob do wykrywania zagrożeń. Aby zbadać alerty z zaawansowanej ochrony przed zagrożeniami, można wyświetlić powiązane działania magazynu przy użyciu rejestrowania analizy magazynu. Aby uzyskać więcej informacji, zobacz **Konfigurowanie rejestrowania** w [monitorze konta magazynu w witrynie Azure portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Dostępność

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna tylko dla [magazynu obiektów Blob.](https://azure.microsoft.com/services/storage/blobs/) Typy kont obsługujące zaawansowaną ochronę przed zagrożeniami obejmują konta ogólnego przeznaczenia w wersji 2, bloku i magazynu obiektów Blob. Zaawansowana ochrona przed zagrożeniami jest dostępna we wszystkich chmurach publicznych i chmurach instytucji rządowych usa, ale nie w innych suwerennych lub platformach rządowych w chmurach.

Szczegółowe informacje o cenach, w tym bezpłatna 30-dniowa wersja próbna, zobacz [stronę z cennikiem usługi Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="set-up-advanced-threat-protection"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami

Zaawansowaną ochronę przed zagrożeniami można skonfigurować na dowolny z kilku sposobów opisanych w poniższych sekcjach.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uruchom [portal Azure](https://portal.azure.com/).
1. Przejdź do konta usługi Azure Storage. W obszarze **Ustawienia**wybierz pozycję **Zaawansowane zabezpieczenia**.
1. Wybierz **łącze Ustawienia** na stronie konfiguracji zabezpieczeń zaawansowanych.
1. Ustaw **zaawansowane zabezpieczenia** na **ON**.
1. Kliknij **przycisk Zapisz,** aby zapisać nową lub zaktualizowaną zasadę.

    ![Włącz zaawansowaną ochronę przed zagrożeniami usługi Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Po zasubskrybowaniu warstwy Standardowa w usłudze Azure Security Center zaawansowana ochrona przed zagrożeniami jest automatycznie umietrzać na wszystkich kontach magazynu. Zaawansowaną ochronę przed zagrożeniami dla kont magazynu w ramach określonej subskrypcji można włączyć lub wyłączyć w następujący sposób:

1. Uruchom **Usługę Azure Security Center** w [witrynie Azure portal](https://portal.azure.com).
1. W menu głównym kliknij pozycję **Ustawienia & cennika**.
1. Kliknij subskrypcję, którą chcesz włączyć lub wyłączyć ochronę przed zagrożeniami dla swoich kont magazynu.

    ![Wybieranie subskrypcji](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kliknij **pozycję Warstwa cenowa**.
1. W sekcji **Wybierz warstwę cenową według typu zasobu** w **wierszu Konta magazynu** kliknij pozycję **Włączone** lub **Wyłączone**.

    ![Włączanie narzędzia ATP w Centrum zabezpieczeń](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kliknij pozycję **Zapisz**.

### <a name="template"></a>[Szablon](#tab/template)

Użyj szablonu usługi Azure Resource Manager, aby wdrożyć konto usługi Azure Storage z włączoną zaawansowaną ochroną przed zagrożeniami. Aby uzyskać więcej informacji, zobacz [Konto magazynu z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Użyj zasad platformy Azure, aby włączyć zaawansowaną ochronę przed zagrożeniami na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów.

1. Uruchom stronę Zasady platformy Azure **— definicje.**

1. Wyszukaj zasady **Wdrażanie zaawansowanej ochrony przed zagrożeniami na kontach magazynu.**

     ![Zasady wyszukiwania](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Wybierz subskrypcję platformy Azure lub grupę zasobów.

    ![Wybierz subskrypcję lub grupę](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Przypisz zasadę.

    ![Strona definicje zasad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

Polecenia interfejsu API rest umożliwia tworzenie, aktualizowanie lub uzyskanie zaawansowanego ustawienia ochrony przed zagrożeniami dla określonego konta magazynu.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami - Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Wyłączanie zaawansowanej ochrony przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Eksplorowanie anomalii zabezpieczeń

Gdy wystąpią anomalie działania magazynu, otrzymasz powiadomienie e-mail z informacjami o podejrzanym zdarzeniu zabezpieczeń. Szczegóły wydarzenia obejmują:

* Charakter anomalii
* Nazwa konta magazynu
* Czas wydarzenia
* Typ magazynu
* Potencjalne przyczyny
* Etapy dochodzenia
* Kroki korygowania

Wiadomość e-mail zawiera również szczegółowe informacje na temat możliwych przyczyn i zalecane działania w celu zbadania i złagodzenia potencjalnego zagrożenia.

![Poczta e-mail z alertem o zaawansowanej ochronie przed zagrożeniami usługi Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Bieżące alerty zabezpieczeń usługi Azure Security Center można przeglądać i zarządzać nimi z [kafelka Alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md)usługi Azure Security Center. Kliknięcie określonego alertu zawiera szczegółowe informacje i działania dotyczące badania bieżącego zagrożenia i rozwiązywania przyszłych zagrożeń.

![Poczta e-mail z alertem o zaawansowanej ochronie przed zagrożeniami usługi Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub ich wykorzystania. Aby uzyskać listę alertów dla usługi Azure Storage, zobacz sekcję **Magazyn** w [obszarze Ochrona przed zagrożeniami dla usług danych w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [logach na kontach usługi Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Dowiedz się więcej o [usłudze Azure Security Center](../../security-center/security-center-intro.md)
