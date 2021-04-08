---
title: Monitoruj centra Surface Hub za pomocą Azure Monitor | Microsoft Docs
description: Użyj rozwiązania Surface Hub, aby śledzić kondycję centrów Surface Hub i zrozumieć, jak są używane.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 9d81af064b18ca8113599b460f2b9524c7288439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711080"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Monitorowanie urządzeń Surface Hub przy użyciu Azure Monitor do śledzenia ich kondycji

![Symbol Surface Hub](./media/surface-hubs/surface-hub-symbol.png)

W tym artykule opisano, jak można użyć rozwiązania Surface Hub w programie Azure Monitor do monitorowania urządzeń z programem Microsoft Surface Hub. Rozwiązanie pomaga śledzić kondycję centrów Surface Hub oraz zrozumieć, jak są używane.

Każda Surface Hub ma zainstalowany Microsoft Monitoring Agent. Za pośrednictwem agenta można wysyłać dane z Surface Hub do Log Analytics obszaru roboczego w Azure Monitor. Pliki dziennika są odczytywane z centrów Surface Hub i są następnie wysyłane do Azure Monitor. Problemy, takie jak serwery w trybie offline, niesynchronizowane z kalendarzem lub jeśli konto urządzenia nie może się zalogować do programu Skype, są wyświetlane na pulpicie nawigacyjnym Surface Hub w Azure Monitor. Za pomocą danych na pulpicie nawigacyjnym można identyfikować urządzenia, które nie są uruchomione lub które mają inne problemy, a także mogą dotyczyć wykrytych problemów.

## <a name="install-and-configure-the-solution"></a>Instalowanie i Konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie. Aby zarządzać centrami powierzchni w Azure Monitor, potrzebne są następujące elementy:

* Poziom [subskrypcji log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) , który będzie obsługiwał liczbę urządzeń, które mają być monitorowane. Ceny Log Analytics różnią się w zależności od liczby zarejestrowanych urządzeń i ilości przetwarzanych danych. W tym celu należy wziąć pod uwagę podczas planowania wdrożenia Surface Hub.

Następnie dodasz istniejący obszar roboczy Log Analytics lub utworzysz nowy. Szczegółowe instrukcje dotyczące korzystania z jednej z tych metod znajdują się w [obszarze roboczym log Analytics w Azure Portal](../logs/quick-create-workspace.md). Po skonfigurowaniu obszaru roboczego Log Analytics istnieją dwa sposoby rejestrowania urządzeń Surface Hub:

* Automatycznie za poorednictwem usługi Intune
* Ręcznie za poorednictwem **ustawień** na urządzeniu Surface Hub.

## <a name="set-up-monitoring"></a>Konfigurowanie monitorowania
Kondycję i aktywność Surface Hub można monitorować przy użyciu Azure Monitor. Surface Hub można zarejestrować za pomocą usługi Intune lub lokalnie za pomocą **ustawień** na Surface Hub.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Łączenie urządzeń Surface Hub z Azure Monitor przy użyciu usługi Intune
Musisz mieć identyfikator obszaru roboczego i klucz obszaru roboczego dla obszaru roboczego Log Analytics, który będzie zarządzać centrami Surface Hub. Można je pobrać z ustawień obszaru roboczego w Azure Portal.

Usługa Intune to produkt firmy Microsoft, który umożliwia centralne zarządzanie ustawieniami konfiguracji obszaru roboczego Log Analytics, które są stosowane do jednego lub kilku urządzeń. Wykonaj następujące kroki, aby skonfigurować urządzenia przy użyciu usługi Intune:

1. Zaloguj się do usługi Intune.
2. Przejdź do **ustawień**  >  **połączone źródła**.
3. Utwórz lub Edytuj zasady na podstawie szablonu Surface Hub.
4. Przejdź do sekcji Azure Operational Insights zasad i Dodaj *Identyfikator obszaru roboczego* log Analytics i *klucz obszaru roboczego* do zasad.
5. Zapisz zasady.
6. Skojarz zasady z odpowiednią grupą urządzeń.

   ![Zasady usługi Intune](./media/surface-hubs/intune.png)

Następnie usługa Intune synchronizuje ustawienia Log Analytics z urządzeniami w grupie docelowej, rejestrując je w obszarze roboczym Log Analytics.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Łączenie urządzeń Surface Hub z Azure Monitor przy użyciu aplikacji ustawienia
Musisz mieć identyfikator obszaru roboczego i klucz obszaru roboczego dla obszaru roboczego Log Analytics, który będzie zarządzać centrami Surface Hub. Można je pobrać z ustawień obszaru roboczego Log Analytics w Azure Portal.

Jeśli nie korzystasz z usługi Intune do zarządzania środowiskiem, możesz zarejestrować urządzenia ręcznie, korzystając z **ustawień** poszczególnych Surface Hub:

1. W Surface Hub Otwórz pozycję **Ustawienia**.
2. Po wyświetleniu monitu wprowadź poświadczenia administratora urządzenia.
3. Kliknij **to urządzenie**, a następnie w obszarze **monitorowanie** kliknij pozycję **Konfiguruj log Analytics ustawienia**.
4. Wybierz pozycję **Włącz monitorowanie**.
5. W oknie dialogowym Ustawienia Log Analytics wpisz Log Analytics **Identyfikator obszaru roboczego** i wpisz **klucz obszaru roboczego**.  
   ![Zrzut ekranu przedstawia ustawienia zestawu Microsoft Operations Manager z opcją Włącz monitorowanie i pola tekstowe dla identyfikatora obszaru roboczego i klucza obszaru roboczego.](./media/surface-hubs/settings.png)
6. Kliknij przycisk **OK** , aby zakończyć konfigurację.

Zostanie wyświetlone potwierdzenie, że konfiguracja została pomyślnie zastosowana na urządzeniu. Jeśli tak, zostanie wyświetlony komunikat z informacją, że Agent pomyślnie nawiązał połączenie z Azure Monitor. Następnie urządzenie rozpocznie wysyłanie danych do Azure Monitor, w którym można je wyświetlić i wykonać na nim działania.

## <a name="monitor-surface-hubs"></a>Monitorowanie urządzeń Surface Hub
Monitorowanie centrów Surface Hub przy użyciu Azure Monitor jest podobne do monitorowania wszelkich innych zarejestrowanych urządzeń.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Po kliknięciu kafelka Surface Hub zostanie wyświetlony stan kondycji urządzenia.

   ![Pulpit nawigacyjny Surface Hub](./media/surface-hubs/surface-hub-dashboard.png)

Możesz tworzyć [alerty](../alerts/alerts-overview.md) na podstawie istniejących lub niestandardowych wyszukiwań w dziennikach. Korzystając z Azure Monitor danych zbieranych z centrów Surface Hub, można wyszukiwać problemy i alerty dotyczące warunków zdefiniowanych dla urządzeń.

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników w Azure monitor](../logs/log-query-overview.md) , aby wyświetlić szczegółowe dane Surface Hub.
* Utwórz [alerty](../alerts/alerts-overview.md) , aby powiadomić Cię, gdy wystąpią problemy z centrami Surface Hub.
