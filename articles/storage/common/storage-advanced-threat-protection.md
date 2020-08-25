---
title: Skonfiguruj zaawansowaną ochronę przed zagrożeniami
titleSuffix: Azure Storage
description: Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla usługi Azure Storage, aby wykrywać anomalie w działaniu konta i otrzymywać powiadomienia o potencjalnie szkodliwych próbach uzyskania dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e037607d1f86e6df4d3f5b12e29ba8fde447ebc9
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757935"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.

Usługa pobiera dzienniki zasobów żądań odczytu, zapisu i usuwania do usługi BLOB Storage oraz do Azure Files (wersja zapoznawcza) na potrzeby wykrywania zagrożeń. Aby zbadać alerty z zaawansowanej ochrony przed zagrożeniami, można wyświetlić powiązane działanie magazynu za pomocą rejestrowania analityka magazynu. Aby uzyskać więcej informacji, zobacz **Konfigurowanie rejestrowania** w obszarze [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Dostępność

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage jest obecnie dostępna dla magazynu obiektów blob, Azure Files (wersja zapoznawcza) i Azure Data Lake Storage Gen2 (wersja zapoznawcza). Typy kont obsługujące zaawansowaną ochronę przed zagrożeniami obejmują konta ogólnego przeznaczenia w wersji 2, blokowych obiektów blob i magazynu obiektów BLOB. Zaawansowana ochrona przed zagrożeniami jest dostępna we wszystkich chmurach publicznych i chmurach dla instytucji rządowych USA, ale nie w regionach w chmurze lub Azure Government.

Konta z hierarchicznymi przestrzeniami nazw, w których włączono obsługę Data Lake Storage transakcji przy użyciu zarówno interfejsów API usługi Azure Blob Storage, jak i interfejsów API Data Lake Storage. Udziały plików platformy Azure obsługują transakcje za pośrednictwem protokołu SMB.

Aby uzyskać szczegółowe informacje o cenach, w tym bezpłatną 30-dniową wersję próbną, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Poniższa lista zawiera podsumowanie dostępności zaawansowanej ochrony przed zagrożeniami dla usługi Azure Storage:

- Stan wydania:
  - [BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (ogólna dostępność)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (wersja zapoznawcza obsługuje transakcje SMB i REST)
  - Azure Data Lake Storage Gen2 (wersja zapoznawcza)
- Połączeń<br>
    ✔ Chmury komercyjne<br>
    ✔ US Gov<br>
    ✘ Chiny gov, inne gov

## <a name="set-up-advanced-threat-protection"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami

Można skonfigurować zaawansowaną ochronę przed zagrożeniami na kilka sposobów, które opisano w poniższych sekcjach.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Gdy subskrybujesz warstwę Standardowa w Azure Security Center, zaawansowana ochrona przed zagrożeniami jest automatycznie skonfigurowana na wszystkich kontach magazynu. Możesz włączyć lub wyłączyć zaawansowaną ochronę przed zagrożeniami dla kont magazynu w ramach określonej subskrypcji w następujący sposób:

1. Uruchom **Azure Security Center** w [Azure Portal](https://portal.azure.com).
1. W menu głównym kliknij pozycję **Cennik ustawienia &**.
1. Kliknij subskrypcję, którą chcesz włączyć lub wyłączyć ochronę przed zagrożeniami dla swoich kont magazynu.

    ![Wybieranie subskrypcji](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Kliknij pozycję **warstwa cenowa**.
1. W sekcji **Wybieranie warstwy cenowej według typu zasobu** w wierszu **konta magazynu** kliknij pozycję **włączone** lub **wyłączone**.

    ![Włącz ATP w Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Kliknij pozycję **Zapisz**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uruchom [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta usługi Azure Storage. W obszarze **Ustawienia**wybierz pozycję **Zabezpieczenia zaawansowane**.
1. Wybierz link **Ustawienia** na stronie Konfiguracja zabezpieczeń zaawansowanych.
1. Ustaw **Zaawansowane zabezpieczenia** **na włączone**.
1. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady.

    ![Włączanie zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Szablon](#tab/template)

Użyj szablonu Azure Resource Manager, aby wdrożyć konto usługi Azure Storage z włączoną funkcją zaawansowanej ochrony przed zagrożeniami. Aby uzyskać więcej informacji, zobacz [konto magazynu z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Użyj Azure Policy, aby włączyć zaawansowaną ochronę przed zagrożeniami na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów.

1. Uruchom stronę **definicje zasad** platformy Azure.

1. Wyszukaj zasady **Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu** .

     ![Zasady wyszukiwania](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Wybierz subskrypcję platformy Azure lub grupę zasobów.

    ![Wybierz subskrypcję lub grupę](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Przypisz zasady.

    ![Strona definicji zasad](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie zaawansowanej ochrony przed zagrożeniami dla określonego konta magazynu.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Eksploruj anomalie zabezpieczeń

Gdy wystąpią anomalie aktywności magazynu, otrzymasz powiadomienie e-mail z informacjami o podejrzanym zdarzeniu zabezpieczeń. Szczegóły zdarzenia obejmują:

* Charakter anomalii
* Nazwa konta magazynu
* Czas zdarzenia
* Typ magazynu
* Możliwe przyczyny
* Kroki badania
* Kroki zaradcze

Wiadomość e-mail zawiera również szczegółowe informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Bieżące alerty zabezpieczeń można przeglądać i zarządzać nimi z poziomu [kafelka alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknięcie określonego alertu zawiera szczegółowe informacje i akcje związane z badaniem bieżącego zagrożenia i rozwiązywanie przyszłych zagrożeń.

![Adres e-mail alertu zaawansowanej ochrony przed zagrożeniami w usłudze Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Aby uzyskać listę alertów dotyczących usługi Azure Storage, zobacz sekcję dotyczącą **magazynu** w temacie [Ochrona przed zagrożeniami dla usług danych w Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dziennikach na kontach usługi Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Dowiedz się więcej o [Azure Security Center](../../security-center/security-center-intro.md)
