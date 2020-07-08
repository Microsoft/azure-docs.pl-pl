---
title: PRZESTARZAŁE Monitorowanie klastra DC/OS platformy Azure — Zarządzanie operacjami
description: Monitoruj klaster Azure Container Service DC/OS z Log Analytics.
author: keikhara
ms.service: container-service
ms.topic: conceptual
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 1ab8d1cf3eb38a17f0b3d6c8137e37237498a527
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277317"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>PRZESTARZAŁE Monitorowanie klastra DC/OS Azure Container Service z Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics to oparte na chmurze rozwiązanie do zarządzania działem IT firmy Microsoft, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.Rozwiązanie kontenera to rozwiązanie w Log Analytics, które ułatwia wyświetlanie spisu kontenerów, wydajności i dzienników w jednej lokalizacji. Można przeprowadzać inspekcję, rozwiązywać problemy z kontenerami, wyświetlając dzienniki w scentralizowanej lokalizacji i wyszukując zakłócenia zużywające nadmiar kontenera na hoście.

![](media/container-service-monitoring-oms/image1.png)

Aby uzyskać więcej informacji o rozwiązaniu kontenera, zobacz [log Analytics rozwiązania kontenera](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Konfigurowanie Log Analytics z platformy DC/OS


W tym artykule przyjęto założenie, że skonfigurowano system DC/OS i wdrożono w nim proste aplikacje kontenera sieci Web.

### <a name="pre-requisite"></a>Wymagania wstępne
- [Subskrypcja Microsoft Azure](https://azure.microsoft.com/free/) — możesz bezpłatnie uzyskać subskrypcję.  
- Log Analytics Konfiguracja obszaru roboczego — Zobacz sekcję "krok 3" poniżej
- Zainstalowano [interfejs wiersza polecenia DC/OS](https://docs.mesosphere.com/1.12/cli) .

1. Na pulpicie nawigacyjnym DC/OS kliknij pozycję Universe i wyszukaj ciąg "OMS", jak pokazano poniżej.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywany Log Analytics.

   ![](media/container-service-monitoring-oms/image2.png)

2. Kliknij przycisk **Zainstaluj**. Zobaczysz okno podręczne z informacjami o wersji oraz **pakietem instalacyjnym** lub przyciskiem **Instalacja zaawansowana** . Po kliknięciu przycisku **Instalacja zaawansowana**, która prowadzi do strony **Właściwości konfiguracji specyficznej** dla pakietu OMS.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. W tym miejscu zostanie wyświetlony monit o wprowadzenie `wsid` identyfikatora obszaru roboczego log Analytics i `wskey` (klucz podstawowy dla identyfikatora obszaru roboczego). Aby uzyskać oba te elementy `wsid` i `wskey` , musisz utworzyć konto w usłudze <https://mms.microsoft.com> .
   Postępuj zgodnie z instrukcjami, aby utworzyć konto. Po zakończeniu tworzenia konta musisz uzyskać swoją `wsid` `wskey` rolę i kliknąć pozycje **Ustawienia**, a następnie **połączone źródła**, a następnie **serwery z systemem Linux**, jak pokazano poniżej.

   ![](media/container-service-monitoring-oms/image5.png)

4. Wybierz żądaną liczbę wystąpień, a następnie kliknij przycisk "Przejrzyj i zainstaluj". Zazwyczaj chcesz mieć liczbę wystąpień równą liczbie maszyn wirtualnych w klastrze agentów. Log Analytics Agent dla systemu Linux jest instalowany jako osobne kontenery na każdej maszynie wirtualnej, która chce zbierać informacje dotyczące monitorowania i rejestrowania informacji.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Konfigurowanie prostego pulpitu nawigacyjnego Log Analytics

Po zainstalowaniu agenta Log Analytics dla systemu Linux na maszynach wirtualnych następnym krokiem jest skonfigurowanie Log Analytics pulpitu nawigacyjnego. Pulpit nawigacyjny można skonfigurować za pomocą Azure Portal.

### <a name="azure-portal"></a>Azure Portal 

Zaloguj się do Azure Portal pod adresem <https://portal.microsoft.com/> . Przejdź do **witryny Marketplace**, wybierz pozycję **monitorowanie i zarządzanie** , a następnie kliknij pozycję **Zobacz wszystko**. Następnie wpisz `containers` ciąg wyszukiwania. W wynikach wyszukiwania zostaną wyświetlone "kontenery". Wybierz pozycję **Containers (kontenery** ) i kliknij pozycję **Utwórz**.

![](media/container-service-monitoring-oms/image9.png)

Po kliknięciu przycisku **Utwórz**zostanie wyświetlony link do obszaru roboczego. Wybierz swój obszar roboczy lub jeśli go nie masz, Utwórz nowy obszar roboczy.

![](media/container-service-monitoring-oms/image10.PNG)

Po wybraniu obszaru roboczego kliknij pozycję **Utwórz**.

![](media/container-service-monitoring-oms/image11.png)

Aby uzyskać więcej informacji na temat rozwiązania kontenera Log Analytics, zapoznaj się z [log Analytics rozwiązania kontenera](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Jak skalować Log Analytics agenta za pomocą usługi ACS DC/OS 

Jeśli konieczne jest zainstalowanie programu Log Analytics Agent jako krótkiej liczby węzłów lub skalowanie zestawu skalowania maszyn wirtualnych, dodając więcej maszyn wirtualnych, można to zrobić przez przeskalowanie `msoms` usługi.

Możesz przejść do karty Marathon lub usług interfejsu użytkownika DC/OS i skalować liczbę węzłów w górę.

![](media/container-service-monitoring-oms/image12.PNG)

To wdrożenie zostanie wdrożone w innych węzłach, w których jeszcze nie wdrożono agenta Log Analytics.

## <a name="uninstall-ms-oms"></a>Odinstalowywanie pakietu MS OMS

Aby odinstalować pakiet MS OMS, wprowadź następujące polecenie:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Daj nam znać!!!
Co działa? Co brakuje? Co jeszcze potrzebujesz, aby było to przydatne dla Ciebie? Daj nam znać <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Następne kroki

 Teraz, po skonfigurowaniu Log Analytics do monitorowania kontenerów,[Zobacz pulpit nawigacyjny kontenerów](../../azure-monitor/insights/containers.md).
