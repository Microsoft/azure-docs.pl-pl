---
title: 'Szybki start: wprowadzenie do usługi Azure Sentinel'
description: Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak szybko wyświetlać i monitorować działania wykonywane w danym środowisku za pomocą usługi Azure wskaźnikowej.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: affefb302d602e9069a903fa5f6a0fbae78992b9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516957"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Szybki start: wprowadzenie do usługi Azure Sentinel




W tym przewodniku szybki start dowiesz się, jak szybko wyświetlać i monitorować działania wykonywane w danym środowisku za pomocą platformy Azure. Po nawiązaniu połączenia ze źródłami danych na platformie Azure wskażesz błyskawiczną wizualizację i analizę danych, aby dowiedzieć się, co się dzieje między wszystkimi połączonymi źródłami danych. Wskaźnik na platformie Azure przedstawia skoroszyty zapewniające pełną moc narzędzi już dostępnych na platformie Azure, a także tabele i wykresy, które są wbudowane w celu zapewnienia analiz dla dzienników i zapytań. Można używać wbudowanych skoroszytów lub łatwo tworzyć nowe skoroszyty od podstaw lub w oparciu o istniejący skoroszyt. 

## <a name="get-visualization"></a>Pobierz wizualizację

Aby wizualizować i uzyskać analizę tego, co dzieje się w środowisku, najpierw zapoznaj się z omówieniem pulpitu nawigacyjnego, aby uzyskać pomysł dotyczący zabezpieczeń stan organizacji. Możesz kliknąć każdy element tych kafelków, aby przejść do danych pierwotnych, z których zostały utworzone. Aby ułatwić zredukowanie szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, wskaźnik platformy Azure używa techniki Fusion do skorelowania alertów w zdarzenia. **Zdarzenia** to grupy powiązanych alertów, które razem tworzą incydent do działania, który można zbadać i rozwiązać.

- W Azure Portal wybierz pozycję wskaźnik kontroli platformy Azure, a następnie wybierz obszar roboczy, który chcesz monitorować.

  ![Omówienie usługi Azure Sentinel](./media/qs-get-visibility/overview.png)

- Pasek narzędzi na górze informuje o liczbie zdarzeń w wybranym okresie i porównuje go z poprzednimi 24 godzinami. Pasek narzędzi informuje o tych zdarzeniach, które zostały wyzwolone (niewielka liczba reprezentuje zmianę w ciągu ostatnich 24 godzin), a następnie informuje o tych zdarzeniach, o liczbie otwartych, w toku i zamknięciu. Sprawdź, czy liczba zdarzeń nie ma znaczącego wzrostu ani porzucenia. Jeśli jest to możliwe, może to oznaczać, że połączenie zostało zatrzymane na potrzeby raportowania na platformie Azure. Jeśli wystąpi wzrost, wystąpił problem podejrzany. Sprawdź, czy masz nowe alerty.

   ![Liczniki wskaźnikowe platformy Azure](./media/qs-get-visibility/funnel.png)

Główna treść strony Przegląd zapewnia wgląd w informacje o stanie zabezpieczeń obszaru roboczego:

- **Zdarzenia i alerty w czasie**: zawiera listę liczby zdarzeń i liczby alertów, które zostały utworzone na podstawie tych zdarzeń. Jeśli widzisz, że najprawdopodobniej wystąpił nietypowy sposób, zobaczysz dla niego alerty — Jeśli coś się nie dzieje w przypadku wystąpienia w zdarzeniach, ale nie widzisz alertów, może to być przyczyną problemu.

- **Potencjalnie złośliwe zdarzenia**: w przypadku wykrycia ruchu ze źródeł, które są znane jako złośliwe, wskaźnik na platformie Azure będzie ostrzegał o mapie. Jeśli widzisz kolor pomarańczowy, jest to ruch przychodzący: ktoś próbuje uzyskać dostęp do organizacji przy użyciu znanego złośliwego adresu IP. Jeśli zostanie wyświetlone działanie wychodzące (czerwone), oznacza to, że dane z sieci są przesyłane strumieniowo z organizacji do znanego złośliwego adresu IP.

   ![Mapa złośliwego ruchu](./media/qs-get-visibility/map.png)

- **Ostatnie zdarzenia**: Aby wyświetlić ostatnie incydenty, ich ważność oraz liczbę alertów skojarzonych ze zdarzeniem. Jeśli widzisz nagły szczyt w określonym typie alertu, może to oznaczać, że jest aktualnie uruchomiony aktywny atak. Na przykład w przypadku nagłego szczytu 20 zdarzeń typu Pass-the-hash z usługi Microsoft Defender dla tożsamości (dawniej Azure ATP) istnieje możliwość, że ktoś aktualnie próbuje się zaatakować.

- **Anomalie źródła danych**: analityki danych firmy Microsoft utworzyły modele, które stale przeszukują dane ze źródeł danych pod kątem anomalii. Jeśli nie ma żadnych anomalii, nic nie zostanie wyświetlone. W przypadku wykrycia anomalii należy szczegółowo szczegółowe je, aby zobaczyć, co się stało. Na przykład kliknij pozycję skok w działaniu platformy Azure. Możesz kliknąć **Wykres** , aby zobaczyć, kiedy nastąpiło przekroczenie, a następnie odfiltrować działania, które wystąpiły w tym okresie, aby zobaczyć, co spowodowało skok.

   ![Nietypowe źródła danych](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Używanie wbudowanych skoroszytów<a name="dashboards"></a>

Wbudowane skoroszyty zapewniają zintegrowane dane z połączonych źródeł danych, co pozwala na głębokie szczegółowe zdarzeń generowanych w ramach tych usług. Wbudowane skoroszyty obejmują usługi Azure AD, zdarzenia aktywności platformy Azure i lokalne, które mogą być danymi z zdarzeń systemu Windows z serwerów, od alertów z pierwszej strony, w tym dzienników ruchu zapory, pakietu Office 365 i niezabezpieczonych protokołów opartych na zdarzeniach systemu Windows. Skoroszyty są oparte na Azure Monitor skoroszytach, aby zapewnić lepszą szerszym i elastyczność projektowania własnych skoroszytów. Aby uzyskać więcej informacji, zobacz [skoroszyty](../azure-monitor/platform/workbooks-overview.md).

1. W obszarze **Ustawienia**wybierz pozycję **skoroszyty**. W obszarze **zainstalowane**można zobaczyć wszystkie zainstalowane skoroszyty. W obszarze **wszystko**można zobaczyć całą galerię wbudowanych skoroszytów, które są dostępne do zainstalowania. 
2. Wyszukaj konkretny skoroszyt, aby zobaczyć całą listę i opis poszczególnych ofert. 
3. Przy założeniu, że używasz usługi Azure AD, aby rozpocząć korzystanie z platformy Azure, zalecamy zainstalowanie co najmniej następujących skoroszytów:
   - **Azure AD**: Użyj jednego lub obu następujących elementów:
       - **Logowania za pomocą usługi Azure AD** analizują logowania w czasie, aby sprawdzić, czy występują anomalie. Te skoroszyty zawierają nieudane logowania w aplikacjach, urządzeniach i lokalizacjach, dzięki czemu możesz zauważyć, że w przypadku nietypowego działania wystąpią. Zwróć uwagę na wiele nieudanych logowań. 
       - **Dzienniki inspekcji usługi Azure AD** analizują działania administracyjne, takie jak zmiany użytkowników (Dodawanie, usuwanie itp.), tworzenie grup i modyfikacje.  

   - Dodaj skoroszyt dla zapory. Na przykład Dodaj skoroszyt Palo Alto. Skoroszyt analizuje ruch związany z zaporą, zapewniając korelacje między zdarzeniami danych i zagrożeniami zapory, a następnie wyróżnia podejrzane zdarzenia w różnych jednostkach. Skoroszyty zawierają informacje o trendach w ruchu i umożliwiają przechodzenie do szczegółów i filtrowanie wyników. 

      ![Palo Alto Dashboard](./media/qs-get-visibility/palo-alto-week-query.png)


Możesz dostosować skoroszyty, edytując ![ przycisk Edytuj głównej kwerendy zapytania ](./media/qs-get-visibility/edit-query-button.png) . Możesz kliknąć przycisk Log Analytics przycisk ![ ](./media/qs-get-visibility/go-to-la-button.png) , aby przejść do [log Analytics, aby edytować zapytanie](../azure-monitor/log-query/get-started-portal.md)w tym miejscu, a następnie wybrać wielokropek (...) i wybrać opcję **Dostosuj dane kafelka**, co umożliwia edytowanie filtru czasu głównego lub usuwanie określonych kafelków ze skoroszytu.

Aby uzyskać więcej informacji na temat pracy z zapytaniami, zobacz [Samouczek: dane wizualne w log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Dodawanie nowego kafelka

Jeśli chcesz dodać nowy kafelek, możesz dodać go do istniejącego skoroszytu, który został utworzony lub wbudowany arkusz kontrolny platformy Azure. 
1. W Log Analytics Utwórz kafelek przy użyciu instrukcji znalezionych w [samouczku: dane wizualne w log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po utworzeniu kafelka w obszarze **Przypnij**wybierz skoroszyt, w którym ma się pojawić kafelek.

## <a name="create-new-workbooks"></a>Utwórz nowe skoroszyty
Możesz utworzyć nowy skoroszyt od podstaw lub użyć wbudowanego skoroszytu jako podstawy dla nowego skoroszytu.

1. Aby utworzyć nowy skoroszyt od podstaw, zaznacz opcję **skoroszyty** , a następnie pozycję **+ nowy skoroszyt**.
2. Wybierz subskrypcję, w której jest tworzony skoroszyt, i nadaj jej nazwę opisową. Każdy skoroszyt jest zasobem platformy Azure, podobnie jak inne, i można przypisać role IT (RBAC), aby zdefiniować i ograniczyć dostęp do nich. 
3. Aby umożliwić wyświetlanie w skoroszytach do przypinania wizualizacji do, należy ją udostępnić. Kliknij przycisk **Udostępnij** , a następnie **Zarządzaj użytkownikami**. 
 
1. Użyj **uprawnień Sprawdź dostęp** i **rola** , tak jak w przypadku innych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [udostępnianie skoroszytów platformy Azure za pomocą funkcji RBAC platformy Azure](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Przykłady nowych skoroszytów

Poniższe przykładowe zapytanie umożliwia porównanie trendów ruchu w tygodniach. Można łatwo przełączać dostawcę urządzenia i źródło danych, na których uruchomiono zapytanie. W tym przykładzie używamy SecurityEvent z systemu Windows, możesz przełączyć go do uruchamiania w usłudze Azure lub CommonSecurityLog na dowolnej innej zaporze.

```console
 |where DeviceVendor == "Palo Alto Networks":
  // week over week query
  SecurityEvent
  | where TimeGenerated > ago(14d)
  | summarize count() by bin(TimeGenerated, 1d)
  | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Możesz chcieć utworzyć zapytanie, które zawiera dane z wielu źródeł. Można utworzyć zapytanie, które przeszukuje Azure Active Directory dzienniki inspekcji dla nowo utworzonych użytkowników, a następnie sprawdza dzienniki platformy Azure, aby sprawdzić, czy użytkownik rozpoczął wprowadzanie zmian przypisań ról w ciągu 24 godzin od utworzenia. Podejrzane działanie będzie widoczne na tym pulpicie nawigacyjnym:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Możesz tworzyć różne skoroszyty w oparciu o rolę osoby, która przegląda dane i czego szukają. Na przykład można utworzyć skoroszyt dla administratora sieci, który zawiera dane zapory. Można również tworzyć skoroszyty na podstawie tego, jak często mają być przeglądane informacje, czy istnieją jakieś elementy, które mają być codziennie sprawdzane, i innych elementów, które mają zostać sprawdzone po godzinie, na przykład możesz chcieć zajrzeć do logowania za pomocą usługi Azure AD co godzinę, aby wyszukać anomalie. 

## <a name="create-new-detections"></a>Utwórz nowe wykrycia

Generuj wykrycia dla [źródeł danych połączonych z platformą Azure wskaźnikiem,](connect-data-sources.md) aby zbadać zagrożenia w organizacji.

Podczas tworzenia nowego wykrywania należy skorzystać z wbudowanych wykryć spreparowanych przez badaczy zabezpieczeń firmy Microsoft, które są dostosowane do połączonych źródeł danych.

Aby wyświetlić wszystkie gotowe do użycia wykrycia, przejdź do pozycji **Analiza** , a następnie pozycję **Szablony reguł**. Ta karta zawiera wszystkie wbudowane reguły kontrolki Azure — wskaźnik.

   ![Używanie wbudowanych wykryć do znajdowania zagrożeń przy użyciu platformy Azure — wskaźnik](media/tutorial-detect-built-in/view-oob-detections.png)

Aby uzyskać więcej informacji na temat uzyskiwania gotowych wykryć, zobacz [Samouczek: uzyskiwanie wbudowanej analizy](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start pokazano, jak rozpocząć korzystanie z platformy Azure. Przejdź do samouczka dotyczącego [sposobu wykrywania zagrożeń](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Utwórz niestandardowe reguły wykrywania zagrożeń](tutorial-detect-threats-custom.md) , aby zautomatyzować odpowiedzi na zagrożenia.

