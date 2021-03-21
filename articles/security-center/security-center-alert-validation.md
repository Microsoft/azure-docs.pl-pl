---
title: Sprawdzanie poprawności alertu w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak sprawdzać poprawność konfiguracji alertów zabezpieczeń w Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b90ca39d7bf01b84400464240bb581a5e7bc922a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602387"
---
# <a name="alert-validation-in-azure-security-center"></a>Sprawdzanie poprawności alertu w Azure Security Center
Ten dokument zawiera informacje dotyczące sposobu weryfikacji systemu pod kątem prawidłowej konfiguracji alertów usługi Azure Security Center.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Alerty to powiadomienia generowane przez usługę Security Center w razie wykrycia zagrożeń dotyczących zasobów. Określa ona priorytet i wyświetla listę alertów wraz z informacjami wymaganymi do szybkiego zbadania problemu. Usługa Security Center udostępnia również zalecenia dotyczące sposobu korygowania ataku.
Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń w Security Center](security-center-alerts-overview.md) i [Zarządzanie alertami zabezpieczeń i reagowanie na](security-center-managing-and-responding-alerts.md) nie


## <a name="generate-sample-azure-defender-alerts"></a>Generowanie przykładowych alertów usługi Azure Defender

W przypadku korzystania z nowych, zapoznawczych alertów, zgodnie z opisem w temacie [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md), można utworzyć przykładowe alerty za pomocą kilku kliknięć na stronie Alerty zabezpieczeń w Azure Portal.

Użyj przykładowych alertów, aby:

- Oceń wartość i możliwości usługi Azure Defender
- Sprawdź poprawność konfiguracji wykonanych dla alertów zabezpieczeń (takich jak integracje SIEM, Automatyzacja przepływu pracy i powiadomienia e-mail)

Aby utworzyć przykładowe alerty:

1. Jako użytkownik z współautor **roli lub** **współautorem subskrypcji** na pasku narzędzi na stronie Alerty wybierz pozycję **Utwórz przykładowe alerty**.
1. Wybierz subskrypcję.
1. Wybierz odpowiedni plan usługi Azure Defender/s, dla którego chcesz zobaczyć alerty. 
1. Wybierz pozycję **Utwórz przykładowe alerty**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Procedura tworzenia przykładowych alertów w Azure Security Center":::
    
    Zostanie wyświetlone powiadomienie z informacją o tym, że tworzone są przykładowe alerty:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Powiadomienie, że przykładowe alerty są generowane.":::

    Po kilku minutach alerty zostaną wyświetlone na stronie Alerty zabezpieczeń. Pojawią się one również w dowolnym miejscu, w którym skonfigurowano otrzymywanie alertów zabezpieczeń Azure Security Center (połączone rozwiązań Siem, powiadomienia e-mail itd.).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Przykładowe alerty na liście alertów zabezpieczeń":::

    > [!TIP]
    > Alerty dotyczą symulowanych zasobów.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Symulowanie alertów na maszynach wirtualnych platformy Azure (Windows) <a name="validate-windows"></a>

Po zainstalowaniu agenta Security Center na komputerze wykonaj następujące kroki na komputerze, na którym ma zostać zaatakowany zasób alertu:

1. Skopiuj plik wykonywalny (na przykład **calc.exe**) na komputer stacjonarny lub inny katalog swojej wygody, a następnie zmień jego nazwę na **ASC_AlertTest_662jfi039N.exe**.
1. Otwórz wiersz polecenia i wykonaj ten plik z argumentem (tylko fałszywą nazwą argumentu), taką jak: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert.

> [!NOTE]
> Podczas przeglądania tego alertu testu dla systemu Windows **upewnij się,** że **Funkcja inspekcji argumentów** pola jest włączona. Jeśli ma **wartość false**, należy włączyć inspekcję argumentów wiersza polecenia. Aby ją włączyć, użyj następującego polecenia: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Symulowanie alertów na maszynach wirtualnych platformy Azure (Linux) <a name="validate-linux"></a>

Po zainstalowaniu agenta Security Center na komputerze wykonaj następujące kroki na komputerze, na którym ma zostać zaatakowany zasób alertu:
1. Skopiuj plik wykonywalny do wygodnej lokalizacji i zmień jego nazwę na **./asc_alerttest_662jfi039n**, na przykład:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otwórz wiersz polecenia i wykonaj następujący plik:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien zostać wyświetlony alert.


## <a name="simulate-alerts-on-kubernetes"></a>Symulowanie alertów w witrynie Kubernetes <a name="validate-kubernetes"></a>

Jeśli zintegrowano usługę Azure Kubernetes z usługą Security Center, możesz sprawdzić, czy alerty działają przy użyciu następującego polecenia polecenia kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Aby uzyskać więcej informacji na temat obrony węzłów i klastrów Kubernetes, zobacz [wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera wprowadzenie do procesu walidacji alertów. Teraz, kiedy znasz już usługę Security Center, zapoznaj się z następującymi artykułami:

* [Weryfikowanie funkcji wykrywania zagrożeń usługi Azure Key Vault w portalu Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w programie Security Center.
* [Opis alertów zabezpieczeń w Azure Security Center](./security-center-alerts-overview.md) — informacje o różnych typach alertów zabezpieczeń.