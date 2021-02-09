---
title: Nowości w platformie Azure — Wskaźnikowanie
description: W tym artykule opisano nowe funkcje na platformie Azure — od ostatnich kilku miesięcy.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 37a9294b156bd41b72e777696f10a1c4ac2bb354
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832763"
---
# <a name="whats-new-in-azure-sentinel"></a>Nowości w platformie Azure — Wskaźnikowanie

W tym artykule wymieniono najnowsze funkcje, które zostały dodane do usługi Azure, oraz nowe funkcje usług pokrewnych, które zapewniają Ulepszone środowisko użytkownika na platformie Azure.

Aby uzyskać informacje o wcześniejszych funkcjach, zobacz nasze [Blogi społeczności Tech](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Notowane funkcje są obecnie dostępne w wersji zapoznawczej. [Postanowienia uzupełniające dotyczące platformy Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zawierają dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze udostępnione.


> [!TIP]
> Nasze zespoły zajmujące się łowiectwem zagrożeń w ramach zapytań, elementy PlayBook, skoroszytów i notesów programu Microsoft, w tym określonych [zapytań polowających](https://github.com/Azure/Azure-Sentinel) [, które](https://github.com/Azure/Azure-Sentinel)zespoły mogą dostosowywać i stosować. 
>
> Możesz również współtworzyć. Dołącz do nas w [społeczności usługi GitHub](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Styczeń 2021 r.

- [Kreator reguł analizy: Ulepszone środowisko edytowania zapytań (publiczna wersja zapoznawcza)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [AZ. SecurityInsights PowerShell — moduł (publiczna wersja zapoznawcza)](#azsecurityinsights-powershell-module-public-preview)
- [Łącznik bazy danych SQL](#sql-database-connector)
- [Ulepszone komentarze dotyczące zdarzeń](#improved-incident-comments)
- [Dedykowane klastry Log Analytics](#dedicated-log-analytics-clusters)
- [Tożsamości zarządzane przez aplikacje logiki](#logic-apps-managed-identities)
- [Ulepszone dostrajanie reguł przy użyciu wykresów z podglądem reguł analizy](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


## <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Kreator reguł analizy: Ulepszone środowisko edytowania zapytań (publiczna wersja zapoznawcza)

Kreator zaplanowanej reguły analizy wskaźnikowej platformy Azure obejmuje teraz następujące udoskonalenia dotyczące pisania i edytowania zapytań:

-   Rozwijane okno edycji, które zapewnia więcej miejsca na ekranie, aby wyświetlić zapytanie.
-   Wyróżnianie kluczowych wyrazów w kodzie zapytania.
-   Rozszerzona obsługa autouzupełniania.
-   Walidacje zapytań w czasie rzeczywistym. Błędy w zapytaniu są teraz wyświetlane jako czerwony blok na pasku przewijania i jako czerwona kropka w nazwie karty **logiki zestawu reguł** . Ponadto nie można zapisać zapytania z błędami.

Aby uzyskać więcej informacji, zobacz [Samouczek: wykrywanie zagrożeń jako](tutorial-detect-threats-built-in.md)wbudowanych.
### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights PowerShell — moduł (publiczna wersja zapoznawcza)

Usługa Azure SecurityInsights obsługuje teraz nowy moduł [AZ.](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell.

Moduł **AZ. SecurityInsights** obsługuje typowe przypadki użycia platformy Azure, takie jak manipulowanie zdarzeniami w celu zmiany Statues, ważności, właściciela i tak dalej, dodawania komentarzy i etykiet do zdarzeń oraz tworzenia zakładek.

Chociaż zalecamy używanie szablonów [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) dla potoku ciągłej integracji/ciągłego wdrażania, moduł **AZ. SecurityInsights** jest przydatny do wykonywania zadań po wdrożeniu i jest przeznaczony dla automatyzacji SOC.  Na przykład Automatyzacja SOC może obejmować kroki konfigurowania łączników danych, tworzenia reguł analizy lub dodawania akcji automatyzacji do reguł analizy.

Aby uzyskać więcej informacji, w tym pełną listę i opis dostępnych poleceń cmdlet, opisów parametrów i przykładów, zobacz [AZ. SecurityInsights PowerShell — dokumentacja](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Łącznik bazy danych SQL

Wskaźnik na platformie Azure przedstawia teraz łącznik usługi Azure SQL Database, który umożliwia przesyłanie strumieniowe dzienników inspekcji i diagnostyki baz danych do usługi Azure wskaźnikowej i ciągłego monitorowania aktywności we wszystkich wystąpieniach.

Azure SQL to w pełni zarządzany aparat bazy danych platformy jako usługi (PaaS), który obsługuje większość funkcji zarządzania bazami danych, takich jak uaktualnianie, stosowanie poprawek, tworzenie kopii zapasowych i monitorowanie, bez zaangażowania użytkowników.

Aby uzyskać więcej informacji, zobacz [łączenie dzienników diagnostyki i inspekcji usługi Azure SQL Database](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Ulepszone komentarze dotyczące zdarzeń

Analitycy wykorzystują Komentarze do zdarzeń do współpracy nad zdarzeniami, dokumentowania procesów i kroków ręcznie lub w ramach element PlayBook. 

Nasze Ulepszone środowisko komentowania zdarzeń umożliwia formatowanie komentarzy i edytowanie lub usuwanie istniejących komentarzy.

Aby uzyskać więcej informacji, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedykowane klastry Log Analytics

Na platformie Azure jest teraz obsługiwane dedykowane klastry Log Analytics jako opcja wdrożenia. Zalecamy rozważenie dedykowanego klastra, jeśli:

- Zyskaj **ponad 1 TB** dziennie do obszaru roboczego wskaźnikowego platformy Azure
- **Posiadanie wielu obszarów roboczych usługi Azure** — Rejestracja na platformie Azure

Dedykowane klastry umożliwiają korzystanie z takich funkcji, jak klucze zarządzane przez klienta, skrytka, podwójne szyfrowanie i szybsze zapytania między obszarami roboczymi, gdy istnieje wiele obszarów roboczych w tym samym klastrze.

Aby uzyskać więcej informacji, zobacz [Azure monitor dzienniki dedykowanych klastrów](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Tożsamości zarządzane przez aplikacje logiki

Usługa Azure element PlayBook obsługuje teraz zarządzane tożsamości dla łącznika Logic Apps wskaźnikowego platformy Azure, dzięki czemu można przyznać uprawnienia bezpośrednio do konkretnej do działania na platformie Azure, zamiast tworzyć dodatkowe tożsamości.

- **Bez tożsamości zarządzanej** łącznik Logic Apps wymaga oddzielnej tożsamości z rolą RBAC kontroli dostępu Azure Osobną tożsamością może być użytkownik usługi Azure AD lub nazwa główna usługi, na przykład zarejestrowana aplikacja usługi Azure AD.

- **Włączenie obsługi tożsamości zarządzanej w aplikacji logiki** spowoduje zarejestrowanie aplikacji logiki w usłudze Azure AD i podaje identyfikator obiektu. Użyj identyfikatora obiektu w wskaźniku kontroli platformy Azure, aby przypisać aplikację logiki z rolą RBAC platformy Azure w obszarze roboczym Azure wskaźnikowym. 

Aby uzyskać więcej informacji, zobacz:

- [Uwierzytelnianie za pomocą tożsamości zarządzanej w Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
- [Dokumentacja dotycząca łącznika Logic Apps Azure wskaźnikowego](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Ulepszone dostrajanie reguł przy użyciu wykresów z podglądem reguł analizy (publiczna wersja zapoznawcza)

Na platformie Azure jest teraz pomocne lepsze dostosowanie reguł analitycznych, co pomaga zwiększyć ich dokładność i zmniejszyć szum.

Po przeprowadzeniu edycji reguły analizy na karcie **Ustawianie logiki reguły** Znajdź obszar **symulacja wyników** po prawej stronie. 

Wybierz pozycję **Testuj z bieżącymi danymi** , aby usługa Azure wskaźnikowa uruchomiła symulację ostatnich 50ych uruchomień reguły analizy. Generowany jest wykres przedstawiający średnią liczbę alertów wygenerowanych przez regułę na podstawie danych nieprzetworzonych zdarzeń. 

Aby uzyskać więcej informacji, zobacz [Definiowanie logiki zapytania reguły i Konfigurowanie ustawień](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Grudzień 2020 r.

- [80 nowe wbudowane zapytania polowające](#80-new-built-in-hunting-queries)
- [Udoskonalenia agenta Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nowe wbudowane zapytania polowające
 
Wbudowane zapytania polowające na platformie Azure sprawdzają analityków SOC, aby ograniczyć przerwy w bieżącym pokryciu wykrywania i powodować zapłon nowych potencjalnych klientów.

Ta aktualizacja dla systemu Azure wskaźnikowego obejmuje nowe zapytania polowające, które zapewniają pokrycie w macierzy MITRE ATT&

- **Kolekcja**
- **Polecenie i kontrola**
- **Dostęp do poświadczeń**
- **Odnajdywanie**
- **Działania**
- **Eksfiltracji**
- **Wpływ**
- **Dostęp wstępny**
- **Trwałość**
- **Eskalacja uprawnień**

Dodane zapytania dotyczące polowań zaprojektowano w celu ułatwienia znalezienia podejrzanych działań w Twoim środowisku. Chociaż mogą zwrócić wiarygodną aktywność i potencjalnie złośliwe działanie, mogą one być przydatne w przypadku wypróbowania Twojego polowania. 

Jeśli po uruchomieniu tych zapytań masz pewność, że możesz chcieć przekonwertować je na reguły analizy lub dodać wyniki polowania do istniejących lub nowych zdarzeń.

Wszystkie dodane zapytania są dostępne za pośrednictwem strony łowiectwa wskaźnikowego platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł [w przypadku zagrożeń związanych z platformą Azure — wskaźnikiem](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Udoskonalenia agenta Log Analytics

Użytkownicy usługi Azure wskaźnikowego korzystają z następujących usprawnień Log Analytics Agent:

- **Obsługa większej liczby systemów operacyjnych**, w tym CentOS 8, RedHat 8 i SUSE Linux 15.
- **Obsługa języka Python 3** oprócz języka Python 2

Usługa Azure CEF używa agenta Log Analytics do wysyłania zdarzeń do obszaru roboczego, w tym zdarzeń zabezpieczeń systemu Windows, zdarzeń dziennika systemowego, dzienników i innych.

> [!NOTE]
> Agent Log Analytics jest czasami określany jako agent pakietu OMS lub Microsoft Monitoring Agent (MMA). 
> 

Aby uzyskać więcej informacji, zobacz [dokumentację log Analytics](/azure/azure-monitor/platform/log-analytics-agent) i informacje o [wersji agenta log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Listopad 2020 r.

- [Monitoruj Logic Apps elementy PlayBook na platformie Azure — wskaźnik](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Łącznik Microsoft 365 Defender (publiczna wersja zapoznawcza)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Monitoruj Logic Apps elementy PlayBook na platformie Azure — wskaźnik

Na platformie Azure jest teraz integrowane z [aplikacjami usługi Azure log](/azure/logic-apps/), usługą w chmurze, która pomaga zaplanować, zautomatyzować i organizować zadania, procesy biznesowe i przepływy pracy.

Użyj aplikacji logiki platformy Azure na platformie Azure jako element PlayBook, która może być wywoływana automatycznie podczas tworzenia zdarzenia lub w przypadku segregowania i pracy z zdarzeniami. 

Aby zapewnić wgląd w kondycję, wydajność i użycie elementy PlayBook, w tym dołączenie dowolnych danych z Azure Logic Apps, dodaliśmy [skoroszyt platformy Azure](/azure/azure-monitor/platform/workbooks-overview) o nazwie **monitorowanie kondycji elementy PlayBook**. 

Użyj skoroszytu **monitorowania kondycji programu elementy PlayBook** , aby monitorować kondycję elementy PlayBook, lub poszukać anomalii w ilości pomyślnych lub nieudanych uruchomień. 

Skoroszyt **monitorowania kondycji elementy PlayBook** jest teraz dostępny w galerii szablonów wskaźnikowych platformy Azure:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Przykładowy skoroszyt monitorowania kondycji elementy PlayBook":::

Aby uzyskać więcej informacji, zobacz:

- [Dokumentacja Logic Apps](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Dokumentacja usługi Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Łącznik Microsoft 365 Defender (publiczna wersja zapoznawcza)
 
Łącznik usługi Microsoft 365 Defender dla platformy Azure (wskaźnik produkcji) umożliwia przesyłanie strumieniowe zaawansowanych dzienników łowiectwa (typu nieprzetworzonych danych zdarzeń) z Microsoft 365 Defender do platformy Azure. 

Dzięki integracji usługi [Microsoft Defender dla punktów końcowych (MDATP)](/windows/security/threat-protection/) na parasol zabezpieczeń programu [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) można teraz zbierać zaawansowane zdarzenia polowania usługi Microsoft Defender for Endpoint przy użyciu łącznika Microsoft 365 Defender i przesyłać je bezpośrednio do nowych, wbudowanych tabel w obszarze roboczym wskaźnikowego platformy Azure. 

Tabele wskaźnikowe platformy Azure są oparte na tym samym schemacie, który jest używany w portalu Microsoft 365 Defender, i zapewnia pełny dostęp do pełnego zestawu zaawansowanych dzienników łowiectwa. 

Aby uzyskać więcej informacji, zobacz [łączenie danych z programu Microsoft 365 Defender z platformą Azure — wskaźnikiem](connect-microsoft-365-defender.md).

> [!NOTE]
> Usługa Microsoft 365 Defender była znana wcześniej jako Microsoft Threat Protection lub MTP. Usługa Microsoft Defender dla punktu końcowego była znana wcześniej jako usługa Microsoft Defender Advanced Threat Protection lub MDATP.
> 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Tablica wskaźnikowa platformy Azure](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Uzyskiwanie wglądu w alerty](quickstart-get-visibility.md)
