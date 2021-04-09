---
title: Tworzenie alertów dotyczących Change Tracking Azure Automation i spisu
description: W tym artykule opisano sposób konfigurowania alertów platformy Azure w celu powiadamiania o stanie zmian wykrytych przez Change Tracking i spis.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594509"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Jak utworzyć alerty dla Change Tracking i spisu

Alerty w usłudze Azure proaktywnie powiadamiają o wynikach z zadań elementów Runbook, problemów z kondycją usługi lub innymi scenariuszami związanymi z Twoim kontem usługi Automation. Azure Automation nie obejmuje wstępnie skonfigurowanych reguł alertów, ale można utworzyć własne na podstawie generowanych przez siebie danych. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów na podstawie zmian zidentyfikowanych przez Change Tracking i spis.

Jeśli nie masz doświadczenia w Azure Monitor alertów, zobacz [Omówienie alertów w usłudze Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md) przed rozpoczęciem. Aby dowiedzieć się więcej o alertach korzystających z zapytań dzienników, zobacz [alerty dzienników w Azure monitor](../../azure-monitor/alerts/alerts-unified-log.md).

## <a name="create-alert"></a>Tworzenie alertu

Poniższy przykład pokazuje, że plik **c:\windows\System32\drivers\etc\HOSTS** został zmodyfikowany na komputerze. Ten plik jest ważny, ponieważ system Windows używa go do rozpoznawania nazw hostów na adresy IP. Ta operacja ma pierwszeństwo przed usługą DNS i może spowodować problemy z łącznością. Może również prowadzić do przekierowywania ruchu do złośliwych lub w inny sposób niebezpiecznych witryn sieci Web.

![Wykres przedstawiający zmianę pliku hosts](./media/configure-alerts/changes.png)

Skorzystajmy z tego przykładu, aby omówić kroki tworzenia alertów dotyczących zmian.

1. Na stronie **śledzenie zmian** na koncie usługi Automation wybierz pozycję **log Analytics**.

2. W wyszukiwaniu dzienników Znajdź zmiany zawartości w pliku **hosts** z zapytaniem `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . To zapytanie szuka zmian zawartości plików z w pełni kwalifikowanymi nazwami ścieżek zawierającymi wyraz `hosts` . Możesz również poszukać określonego pliku, zmieniając część ścieżki na jej w pełni kwalifikowaną postać, na przykład używając `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Gdy zapytanie zwróci wyniki, wybierz pozycję **Nowa reguła alertu** w przeszukiwaniu dzienników, aby otworzyć stronę **tworzenie alertów** . Możesz również przejść do tej strony za pomocą **Azure monitor** w Azure Portal.

4. Sprawdź ponownie zapytanie i zmodyfikuj logikę alertów. W takim przypadku alert ma być wyzwalany, jeśli istnieje nawet jedna zmiana została wykryta między wszystkimi maszynami w środowisku.

    ![Zmień, aby wykonać zapytanie dotyczące śledzenia zmian w pliku hosts](./media/configure-alerts/change-query.png)

5. Po ustawieniu logiki alertu Przypisz grupy akcji, aby wykonać działania w odpowiedzi na wyzwolenie alertu. W takim przypadku konfigurujemy wiadomości e-mail do wysłania i zostanie utworzony bilet zarządzania usługami IT (narzędzia ITSM).

Wykonaj poniższe kroki, aby skonfigurować alerty w celu poinformowania o stanie wdrożenia aktualizacji. Jeśli jesteś nowym tematem alertów platformy Azure, zobacz [Omówienie usługi Azure Alerts](../../azure-monitor/alerts/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurowanie grup akcji dla alertów

Po skonfigurowaniu alertów można skonfigurować grupę akcji, która jest grupą akcji do użycia w ramach wielu alertów. Akcje mogą obejmować powiadomienia e-mail, elementy Runbook, webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../../azure-monitor/alerts/action-groups.md).

1. Wybierz Alert, a następnie wybierz pozycję **Utwórz nowy** w obszarze **grupy akcji**.

2. Wprowadź pełną nazwę i krótką nazwę grupy akcji. Update Management używa krótkiej nazwy podczas wysyłania powiadomień przy użyciu określonej grupy.

3. W obszarze **Akcje** wprowadź nazwę określającą akcję, na przykład **powiadomienie e-mail**.

4. W **polu Typ akcji** wybierz odpowiedni typ, na przykład **adres E-mail/wiadomość SMS/wypychanie/głos**.

5. Wybierz ikonę ołówka, aby edytować szczegóły akcji.

6. Wypełnij okienko dla danego typu akcji. Na przykład w przypadku wysyłania wiadomości e-mail pocztą **e-mail/SMS/wypychania/głosu** wprowadź nazwę akcji, zaznacz pole wyboru **adres e-mail** , wprowadź prawidłowy adres e-mail, a następnie wybierz przycisk **OK**.

    ![Konfigurowanie grupy akcji dla poczty e-mail](./media/configure-alerts/configure-email-action-group.png)

7. W okienku Dodawanie grupy akcji wybierz przycisk **OK**.

8. W przypadku wiadomości e-mail z alertami można dostosować temat wiadomości e-mail. Wybierz pozycję **Dostosuj akcje** w obszarze **Utwórz regułę**, a następnie wybierz pozycję **temat wiadomości e-mail**.

9. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach w Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Dowiedz się więcej na temat [zapytań dzienników](../../azure-monitor/logs/log-query-overview.md) dotyczących pobierania i analizowania danych z obszaru roboczego log Analytics.

* Zarządzanie [użyciem i kosztami za pomocą dzienników Azure monitor](../../azure-monitor/logs/manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie i zgłaszanie alertów dotyczących użycia danych.