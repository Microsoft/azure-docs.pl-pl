---
title: Azure Active Directory dzienników aktywności w Azure Monitor | Microsoft Docs
description: Wprowadzenie do Azure Active Directory dzienników aktywności w Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73130c906d4d9f0da51db1b666e8562570cce40f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571264"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Dzienniki aktywności usługi Azure AD w usłudze Azure Monitor

Dzienniki aktywności usługi Azure Active Directory (Azure AD) można kierować do kilku punktów końcowych w celu długoterminowego przechowywania i wglądu w dane. Ta funkcja umożliwia:

* Archiwizuj dzienniki aktywności usługi Azure AD na koncie usługi Azure Storage, aby zachować dane przez długi czas.
* Przesyłaj strumieniowo dzienniki aktywności usługi Azure AD do centrum zdarzeń platformy Azure w celu analizy przy użyciu popularnych narzędzi do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), takich jak Splunk i QRadar.
* Integrowanie dzienników aktywności usługi Azure AD z własnymi niestandardowymi rozwiązaniami dzienników przez przesyłanie strumieniowe do centrum zdarzeń.
* Wyślij dzienniki aktywności usługi Azure AD do dzienników Azure Monitor, aby umożliwić rozbudowane wizualizacje, monitorowanie i zgłaszanie alertów dotyczących połączonych danych.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Obsługiwane raporty

Dzienniki inspekcji usługi Azure AD i dzienniki logowania można kierować do konta usługi Azure Storage, centrum zdarzeń, dzienników Azure Monitor lub niestandardowego rozwiązania za pomocą tej funkcji. 

* **Dzienniki inspekcji**: [raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.
* **Dzienniki logowania**: przy użyciu [raportu działań dotyczącego logowań](concept-sign-ins.md) można określić, kto wykonał zadania zgłoszone w dziennikach inspekcji.

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z tej funkcji są potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* [Licencja](https://azure.microsoft.com/pricing/details/active-directory/) usługi Azure AD w warstwie Bezpłatna, Podstawowa, Premium 1 lub Premium 2, aby uzyskać dostęp do dzienników inspekcji usługi Azure AD w witrynie Azure Portal. 
* Dzierżawa usługi Azure AD.
* Użytkownik będący **administratorem globalnym** lub **administratorem zabezpieczeń** dla tej dzierżawy usługi Azure AD.
* [Licencja](https://azure.microsoft.com/pricing/details/active-directory/) usługi Azure AD w warstwie Premium 1 lub Premium 2, aby uzyskać dostęp do dzienników logowania usługi Azure AD w witrynie Azure Portal. 

W zależności od tego, gdzie chcesz przekierować dane dziennika inspekcji, będziesz potrzebować dowolnego spośród poniższych elementów:

* Konto usługi Azure Storage, do którego masz uprawnienia *ListKeys*. Zalecamy używanie konta magazynu ogólnego, a nie konta magazynu obiektów blob. Informacje o cenach magazynu można znaleźć na stronie [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Przestrzeń nazw usługi Azure Event Hubs potrzeby integracji z rozwiązaniami innych firm.
* Obszar roboczy usługi Azure Log Analytics do wysyłania dzienników do Azure Monitor dzienników.

## <a name="cost-considerations"></a>Kwestie związane z kosztami

Jeśli masz już licencję usługi Azure AD, potrzebujesz subskrypcji platformy Azure do skonfigurowania magazynu konta i centrum zdarzeń. Subskrypcja platformy Azure jest dostępna bezpłatnie, ale trzeba płacić za korzystanie z zasobów platformy Azure, w tym konto magazynu używane na potrzeby archiwizacji oraz centrum zdarzeń używane do przesyłania strumieniowego. Ilość danych, a w związku z tym koszt, może różnić się znacznie w zależności od rozmiaru dzierżawy. 

### <a name="storage-size-for-activity-logs"></a>Rozmiar magazynu dla dzienników aktywności

Każde zdarzenie dziennika inspekcji używa około 2 KB magazynu danych. Dzienniki zdarzeń logowania dotyczą około 4 KB magazynu danych. W przypadku dzierżawy z 100 000 użytkowników, którzy generują około 1,5 miliona zdarzeń dziennie, będziesz potrzebować około 3 GB magazynu danych na dzień. Ponieważ operacje zapisu są przetwarzane w partiach w około pięciominutowych odstępach, możesz oczekiwać około 9000 operacji zapisu miesięcznie. 


Poniższa tabela zawiera oszacowanie kosztów w zależności od rozmiaru dzierżawy w przypadku konta magazynu ogólnego przeznaczenia w wersji 2 w regionie Zachodnie stany USA z okresem przechowywania co najmniej jeden rok. Aby utworzyć bardziej dokładne oszacowanie dla ilości danych, którą przewidujesz dla aplikacji, użyj [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Kategoria dziennika | Liczba użytkowników | Zdarzenia dziennie | Ilość danych na miesiąc (szac.) | Koszt za miesiąc (szac.) | Koszt za rok (szac.) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Inspekcja | 100 000 | 1,5&nbsp;mln | 90 GB | 1,93 USD | 23,12 USD |
| Inspekcja | 1000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Logowania | 1000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Logowania | 100 000 | 15&nbsp;mln | 1,7 TB | 35,41 USD | 424,92 USD |
 









### <a name="event-hub-messages-for-activity-logs"></a>Komunikaty centrum zdarzeń dotyczące dzienników aktywności

Zdarzenia są przetwarzane w partiach w około 5-minutowych odstępach i wysyłane jako pojedynczy komunikat zawierający wszystkie zdarzenia, które wystąpiły w tym przedziale czasu. Maksymalny rozmiar komunikatu w centrum zdarzeń wynosi 256 KB, a jeśli łączny rozmiar wszystkich komunikatów w danym przedziale czasu przekracza ten wolumen, wysyłane jest wiele komunikatów. 

Na przykład w przypadku dużej dzierżawy z ponad 100 000 użytkowników przeważnie występuje 18 zdarzeń na sekundę, co oznacza szybkość wynoszącą 5400 zdarzeń co pięć minut. Ponieważ dzienniki inspekcji mają rozmiar około 2 KB na zdarzenie, odpowiada to 10,8 MB danych. W związku z tym 43 komunikaty są wysyłane do centrum zdarzeń w ciągu tego pięciominutowego interwału. 

Poniższa tabela zawiera szacowane koszty miesięcznie w przypadku podstawowego centrum zdarzeń w regionie zachodnie stany USA, w zależności od ilości danych zdarzeń, które mogą się różnić od dzierżawców do dzierżawców w przypadku wielu czynników, takich jak zachowanie logowania użytkownika itd. Aby obliczyć dokładne oszacowanie ilości danych przewidywanej dla aplikacji, użyj [kalkulatora cen Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategoria dziennika | Liczba użytkowników | Zdarzenia na sekundę | Zdarzenia na pięciominutowy interwał | Wolumen na interwał | Komunikaty na interwał | Komunikaty na miesiąc | Koszt za miesiąc (szac.) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Inspekcja | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Inspekcja | 1000 | 0.1 | 52 | 104 KB | 1 | 8640 | 10,80 USD |
| Logowania | 100 000 | 18000 | 5 400 000 | 10,8 GB | 42188 | 364 504 320 | $23,9 |  
| Logowania | 1000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="azure-monitor-logs-cost-considerations"></a>Zagadnienia dotyczące kosztów Azure Monitor dzienników



| Kategoria dziennika | Liczba użytkowników | Zdarzenia dziennie | Liczba zdarzeń miesięcznie (30 dni) | Koszt miesięcznie w USD (EST.) |
|:-|--|--|--|-:|
| Inspekcja i logowania | 100 000 | 16 500 000 | 495 000 000 | $1093,00 |
| Inspekcja | 100 000 | 1 500 000 | 45 000 000 | $246,66 |
| Logowania | 100 000 | 15 000 000 | 450 000 000 | $847,28 |










Aby zapoznać się z kosztami związanymi z zarządzaniem dziennikami Azure Monitor, zobacz [Zarządzanie kosztami, kontrolując ilość danych i przechowywanie w dziennikach Azure monitor](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Ta sekcja zawiera odpowiedzi na często zadawane pytania i znane problemy z dziennikami usługi Azure AD w usłudze Azure Monitor.

**Pytanie: Które dzienniki są uwzględnione?**

**Odpowiedź:** Za pomocą tej funkcji można przekierowywać dzienniki logowań i dzienniki inspekcji, chociaż zdarzenia inspekcji związane z usługąB2C nie są obecnie uwzględniane. Aby dowiedzieć się, jakie typy dzienników i które oparte na funkcjach dzienniki są obecnie obsługiwane, zapoznaj się ze [schematem dziennika inspekcji](reference-azure-monitor-audit-log-schema.md) i [schematem dziennika logowania](reference-azure-monitor-sign-ins-log-schema.md). 

---

**P: jak wkrótce po akcji zostaną wyświetlone odpowiednie dzienniki w centrum zdarzeń?**

**Odpowiedź**: Dzienniki powinny być widoczne w centrum zdarzeń w ciągu dwóch do pięciu minut po wykonaniu akcji. Aby uzyskać więcej informacji na temat usługi Event Hubs, zobacz [Co to jest usługa Azure Event Hubs?](../../event-hubs/event-hubs-about.md)

---

**P: jak wkrótce po akcji zostaną wyświetlone odpowiednie dzienniki na koncie magazynu?**

**Odpowiedź:** W przypadku kont usługi Azure Storage opóźnienie wynosi od 5 do 15 minut po wykonaniu akcji.

---

**P: co się stanie, jeśli administrator zmieni okres przechowywania ustawienia diagnostycznego?**

Odp **.: nowe** zasady przechowywania zostaną zastosowane do dzienników zebranych po zmianie. Nie dotyczy to dzienników zebranych przed zmianą zasad.

---

**Pytanie: Ile będzie kosztować przechowywanie danych?**

**Odpowiedź:** koszt przechowywania danych zależy od rozmiaru dzienników oraz wybranego okresu przechowywania. Aby uzyskać listę szacowanych kosztów dzierżaw, które zależą od woluminu wygenerowanych dzienników, przejdź do sekcji [Rozmiar magazynu dla dzienników aktywności](#storage-size-for-activity-logs).

---

**Pytanie: Ile będzie kosztować przesyłanie strumieniowe danych do centrum zdarzeń?**

**Odpowiedź:** Koszty przesyłania strumieniowego zależą od liczby komunikatów odbieranych na minutę. W tym artykule omówiono sposób obliczania kosztów i przedstawiono listę szacowanych kosztów w oparciu o liczbę komunikatów. 

---

**Pytanie: Jak mogę zintegrować dzienniki aktywności usługi Azure AD z moim systemem SIEM?**

**Odpowiedź**: Możesz to zrobić na dwa sposoby:

- Za pomocą usługi Azure Monitor i usługi Event Hubs prześlij strumieniowo dzienniki do systemu SIEM. Najpierw [prześlij strumieniowo dzienniki do centrum zdarzeń](tutorial-azure-monitor-stream-logs-to-event-hub.md), a następnie [skonfiguruj narzędzie SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) za pomocą skonfigurowanego centrum zdarzeń. 

- Użyj [interfejsu API raportowania programu Graph](concept-reporting-api.md) w celu uzyskania dostępu do danych i wypchnięcia ich do systemu SIEM przy użyciu własnych skryptów.

---

**Pytanie: Jakie narzędzia SIEM są obecnie obsługiwane?** 

Odp.  **: obecnie** Azure monitor jest obsługiwany przez [Splunk](./howto-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](./howto-integrate-activity-logs-with-arcsight.md), LogRhythm i Logz.IO. Aby uzyskać więcej informacji na temat sposobu działania łączników, zobacz [Stream Azure monitoring data to an event hub for consumption by an external tool (Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń do użycia przez zewnętrzne narzędzie)](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

---

**Pytanie: Jak mogę zintegrować dzienniki aktywności usługi Azure AD z moim wystąpieniem usługi Splunk?**

**Odpowiedź**: Najpierw [przekieruj dzienniki aktywności usługi Azure AD do centrum zdarzeń](./tutorial-azure-monitor-stream-logs-to-event-hub.md), a następnie postępuj zgodnie z podanymi instrukcjami, aby [zintegrować dzienniki aktywności z usługą Splunk](./howto-integrate-activity-logs-with-splunk.md).

---

**Pytanie: Jak mogę zintegrować dzienniki aktywności usługi Azure AD z moją logiką Sumo?** 

**Odpowiedź**: Najpierw [przekieruj dzienniki aktywności usługi Azure AD do centrum zdarzeń](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), a następnie postępuj zgodnie z podanymi instrukcjami, aby [zainstalować aplikację usługi Azure AD i wyświetlić pulpity nawigacyjne w logice SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**Pytanie: Czy można uzyskać dostęp do danych z centrum zdarzeń bez użycia zewnętrznego narzędzia SIEM?** 

Odp **.: tak**. Aby uzyskać dostęp do dzienników z aplikacji niestandardowej, możesz użyć [interfejsu API usługi Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md). 

---


## <a name="next-steps"></a>Następne kroki

* [Archiwizowanie dzienników aktywności na koncie magazynu](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Kierowanie dzienników aktywności do centrum zdarzeń](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Integrowanie dzienników aktywności z Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)