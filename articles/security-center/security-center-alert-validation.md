---
title: Sprawdzanie poprawności alertu w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak sprawdzać poprawność konfiguracji alertów zabezpieczeń w Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 999888b12f10c07f7d42f14289e88030f9542a36
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340822"
---
# <a name="alert-validation-in-azure-security-center"></a>Sprawdzanie poprawności alertu w Azure Security Center
Ten dokument zawiera informacje dotyczące sposobu weryfikacji systemu pod kątem prawidłowej konfiguracji alertów usługi Azure Security Center.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Alerty to powiadomienia generowane przez usługę Security Center w razie wykrycia zagrożeń dotyczących zasobów. Określa ona priorytet i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.
Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń w Security Center](security-center-alerts-overview.md) i [Zarządzanie alertami zabezpieczeń i reagowanie na](security-center-managing-and-responding-alerts.md) nie

## <a name="validate-alerts-on-windows-vms"></a>Weryfikowanie alertów na maszynach wirtualnych z systemem Windows <a name="validate-windows"></a>

Po zainstalowaniu agenta Security Center na komputerze wykonaj następujące kroki na komputerze, na którym ma zostać zaatakowany zasób alertu:

1. Skopiuj plik wykonywalny (na przykład **calc.exe**) na komputer stacjonarny lub inny katalog swojej wygody, a następnie zmień jego nazwę na **ASC_AlertTest_662jfi039N.exe**.
1. Otwórz wiersz polecenia i wykonaj ten plik z argumentem (tylko fałszywą nazwą argumentu), taką jak: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert.

> [!NOTE]
> Podczas przeglądania tego alertu testu dla systemu Windows **upewnij się,** że **Funkcja inspekcji argumentów** pola jest włączona. Jeśli ma **wartość false**, należy włączyć inspekcję argumentów wiersza polecenia. Aby ją włączyć, użyj następującego polecenia: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Weryfikowanie alertów na maszynach wirtualnych z systemem Linux <a name="validate-linux"></a>

Po zainstalowaniu agenta Security Center na komputerze wykonaj następujące kroki na komputerze, na którym ma zostać zaatakowany zasób alertu:
1. Skopiuj plik wykonywalny do wygodnej lokalizacji i zmień jego nazwę na **./asc_alerttest_662jfi039n**, na przykład:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otwórz wiersz polecenia i wykonaj następujący plik:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert.


## <a name="validate-alerts-on-kubernetes"></a>Weryfikowanie alertów w witrynie Kubernetes <a name="validate-kubernetes"></a>

Jeśli zintegrowano usługę Azure Kubernetes z usługą Security Center, możesz sprawdzić, czy alerty działają przy użyciu następującego polecenia polecenia kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Aby uzyskać więcej informacji na temat obrony węzłów i klastrów Kubernetes, zobacz [wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera wprowadzenie do procesu walidacji alertów. Teraz, kiedy znasz już usługę Security Center, zapoznaj się z następującymi artykułami:

* [Weryfikowanie funkcji wykrywania zagrożeń usługi Azure Key Vault w portalu Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w programie Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Opis alertów zabezpieczeń w Azure Security Center](./security-center-alerts-overview.md) — informacje o różnych typach alertów zabezpieczeń.