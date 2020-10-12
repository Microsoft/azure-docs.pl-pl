---
title: Konfigurowanie usługi Azure Defender dla magazynu
titleSuffix: Azure Storage
description: Skonfiguruj usługę Azure Defender for Storage, aby wykrywać anomalie w działaniu konta i otrzymywać powiadomienia o potencjalnie szkodliwych próbach uzyskania dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: c7e0c9aee1ce6b4a2524ac756673784b63be3b31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289773"
---
# <a name="configure-azure-defender-for-storage"></a>Konfigurowanie usługi Azure Defender dla magazynu

Usługa Azure Defender dla magazynu oferuje dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności eksperta zabezpieczeń ani zarządzania systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniami dotyczącymi sposobu badania i korygowania zagrożeń.

Usługa pozyskuje dzienniki zasobów żądań odczytu, zapisu i usuwania do magazynu obiektów blob oraz Azure Files na potrzeby wykrywania zagrożeń. Aby zbadać alerty z usługi Azure Defender, można wyświetlić powiązane działanie magazynu za pomocą rejestrowania analityka magazynu. Aby uzyskać więcej informacji, zobacz **Konfigurowanie rejestrowania** w obszarze [Monitorowanie konta magazynu w Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Dostępność

Usługa Azure Defender for Storage jest obecnie dostępna dla magazynu obiektów blob, Azure Files i Azure Data Lake Storage Gen2. Typy kont obsługujące usługę Azure Defender obejmują konta ogólnego przeznaczenia w wersji 2, blokowych obiektów blob i magazynu obiektów BLOB. Usługa Azure Defender for Storage jest dostępna we wszystkich chmurach publicznych i chmurach dla instytucji rządowych USA, ale nie w innych regionach w chmurze lub Azure Government.

Konta z hierarchicznymi przestrzeniami nazw, w których włączono obsługę Data Lake Storage transakcji przy użyciu zarówno interfejsów API usługi Azure Blob Storage, jak i interfejsów API Data Lake Storage. Udziały plików platformy Azure obsługują transakcje za pośrednictwem protokołu SMB.

Aby uzyskać szczegółowe informacje o cenach, w tym bezpłatną 30-dniową wersję próbną, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Poniższa lista zawiera podsumowanie dostępności usługi Azure Defender dla magazynu:

- Stan wydania:
  - [BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) (ogólna dostępność)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (ogólna dostępność)
  - Azure Data Lake Storage Gen2 (ogólna dostępność)
- Połączeń<br>
    ✔ Chmury komercyjne<br>
    ✔ US Gov<br>
    ✘ Chiny gov, inne gov

## <a name="set-up-azure-defender"></a>Konfigurowanie usługi Azure Defender

Usługę Azure Defender Storage można skonfigurować na kilka sposobów, co opisano w poniższych sekcjach.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Gdy subskrybujesz warstwę Standardowa w Azure Security Center, usługa Azure Defender zostanie automatycznie skonfigurowana na wszystkich kontach magazynu. Usługę Azure Defender można włączyć lub wyłączyć dla kont magazynu w ramach określonej subskrypcji w następujący sposób:

1. Uruchom **Azure Security Center** w [Azure Portal](https://portal.azure.com).
1. Z menu głównego w obszarze **Zarządzanie**wybierz pozycję **Cennik ustawienia &**.
1. Wybierz subskrypcję, dla której chcesz włączyć lub wyłączyć usługę Azure Defender.
1. Wybierz pozycję **Azure Defender on** , aby włączyć usługę Azure Defender dla subskrypcji.
1. W obszarze **Wybieranie planu usługi Azure Defender według typu zasobu**Znajdź wiersz **magazynu** i wybierz pozycję **włączone** w kolumnie **Plan** .
1. Zapisz zmiany.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

Usługa Azure Defender jest teraz włączona dla wszystkich kont magazynu w tej subskrypcji.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uruchom [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta magazynu. W obszarze **Ustawienia**wybierz pozycję **Zabezpieczenia zaawansowane**.
1. Wybierz pozycję **Włącz usługę Azure Defender dla magazynu**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

Usługa Azure Defender jest teraz włączona dla tego konta magazynu.

### <a name="template"></a>[Szablon](#tab/template)

Użyj szablonu Azure Resource Manager, aby wdrożyć konto usługi Azure Storage za pomocą usługi Azure Defender. Aby uzyskać więcej informacji, zobacz [konto magazynu z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Użyj Azure Policy, aby włączyć usługę Azure Defender na kontach magazynu w ramach określonej subskrypcji lub grupy zasobów.

1. Uruchom stronę **definicje zasad** platformy Azure.
1. Wyszukaj zasady **wdrażanie usługi Azure Defender na kontach magazynu** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

1. Wybierz subskrypcję platformy Azure lub grupę zasobów.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

1. Przypisz zasady.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie usługi Azure Defender dla określonego konta magazynu.

- [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj następujących poleceń cmdlet programu PowerShell:

- [Włącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Eksploruj anomalie zabezpieczeń

Gdy wystąpią anomalie aktywności magazynu, otrzymasz powiadomienie e-mail z informacjami o podejrzanym zdarzeniu zabezpieczeń. Szczegóły zdarzenia obejmują:

- Charakter anomalii
- Nazwa konta magazynu
- Czas zdarzenia
- Typ magazynu
- Możliwe przyczyny
- Kroki badania
- Kroki zaradcze

Wiadomość e-mail zawiera również szczegółowe informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

Bieżące alerty zabezpieczeń można przeglądać i zarządzać nimi z poziomu [kafelka alerty zabezpieczeń](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Center. Kliknięcie określonego alertu zawiera szczegółowe informacje i akcje związane z badaniem bieżącego zagrożenia i rozwiązywanie przyszłych zagrożeń.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usługi Azure Defender dla magazynu w Security Center":::

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty są generowane przez nietypowe i potencjalnie szkodliwe próby dostępu do kont magazynu lub korzystania z nich. Aby uzyskać listę alertów dotyczących usługi Azure Storage, zobacz [alerty dotyczące usługi Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dziennikach na kontach usługi Azure Storage](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Dowiedz się więcej o [Azure Security Center](../../security-center/security-center-intro.md)
