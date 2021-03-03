---
title: Zarządzanie zasobem usługi Datadog — rozwiązania partnerskie platformy Azure
description: W tym artykule opisano zarządzanie zasobem usługi Datadog w Azure Portal. Jak skonfigurować Logowanie jednokrotne, usunąć organizację i uzyskać pomoc techniczną.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 1a76f79f31d1f4518c069afb7fccbad5bd22d4e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745297"
---
# <a name="manage-the-datadog-resource"></a>Zarządzanie zasobem usługi Datadog

W tym artykule pokazano, jak zarządzać ustawieniami integracji z platformą Azure za pomocą usługi usługi Datadog.

## <a name="resource-overview"></a>Przegląd zasobów

Aby wyświetlić szczegółowe informacje o zasobie usługi Datadog, w okienku po lewej stronie wybierz pozycję **Przegląd** .

:::image type="content" source="media/manage/resource-overview.png" alt-text="Przegląd zasobów usługi Datadog" border="true" lightbox="media/manage/resource-overview.png":::

Szczegóły obejmują:

- Nazwa grupy zasobów
- Lokalizacja/region
- Subskrypcja
- Tagi
- Link logowania jednokrotnego do organizacji usługi Datadog
- Oferta usługi Datadog/plan
- Okres rozliczeniowy

Zawiera również linki do pulpitów nawigacyjnych usługi Datadog, dzienników i map hostów.

Ekran przegląd zawiera podsumowanie zasobów wysyłania dzienników i metryk do usługi Datadog.

- Typ zasobu — typ zasobu platformy Azure.
- Łączne zasoby — liczba wszystkich zasobów dla typu zasobu.
- Zasoby wysyłające dzienniki — liczba zasobów wysyłających dzienniki do usługi Datadog przez integrację.
- Zasoby wysyłające metryki — liczba zasobów wysyłających metryki do usługi Datadog przez integrację.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Ponowne konfigurowanie reguł dla metryk i dzienników

Aby zmienić reguły konfiguracji metryk i dzienników, w okienku po lewej stronie wybierz pozycję **metryki i dzienniki** .

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Zmodyfikuj konfigurację dzienników i metryk dla zasobu usługi Datadog." border="true":::

Aby uzyskać więcej informacji, zobacz [Konfigurowanie metryk i dzienników](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Wyświetlanie monitorowanych zasobów

Aby wyświetlić listę zasobów emitujących dzienniki do usługi Datadog, w okienku po lewej stronie wybierz pozycję **monitorowane zasoby** .

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Wyświetlanie zasobów monitorowanych przez usługi Datadog" border="true":::

Listę zasobów można filtrować według typu zasobu, nazwy grupy zasobów, lokalizacji i od tego, czy zasób wysyła dzienniki i metryki.

**Usługi Datadog** kolumn wskazuje, czy zasób wysyła dzienniki do usługi Datadog. Jeśli zasób nie wysyła dzienników, to pole wskazuje, dlaczego dzienniki nie są wysyłane do usługi Datadog. Przyczyny mogą być następujące:

- Zasób nie obsługuje wysyłania dzienników. Tylko typy zasobów z kategoriami dzienników monitorowania można skonfigurować do wysyłania dzienników do usługi Datadog.
- Osiągnięto limit pięciu ustawień diagnostycznych. Każdy zasób platformy Azure może mieć maksymalnie pięć ustawień diagnostycznych. Aby uzyskać więcej informacji, zobacz [Ustawienia diagnostyczne](../../azure-monitor/platform/diagnostic-settings.md).
- Błąd. Zasób jest skonfigurowany do wysyłania dzienników do usługi Datadog, ale jest blokowany z powodu błędu.
- Dzienniki nie zostały skonfigurowane. Tylko zasoby platformy Azure, które mają odpowiednie Tagi zasobów, są skonfigurowane do wysyłania dzienników do usługi Datadog.
- Region nie jest obsługiwany. Zasób platformy Azure znajduje się w regionie, który obecnie nie obsługuje wysyłania dzienników do usługi Datadog.
- Agent usługi Datadog nie jest skonfigurowany. Maszyny wirtualne bez zainstalowanego agenta usługi Datadog nie emitują dzienników do usługi Datadog.

## <a name="api-keys"></a>Klucze interfejsu API

Aby wyświetlić listę kluczy interfejsu API dla zasobu usługi Datadog, wybierz **klucze** w lewym okienku. Zostaną wyświetlone informacje o kluczach.

:::image type="content" source="media/manage/keys.png" alt-text="Klucze interfejsu API dla organizacji usługi Datadog." border="true":::

Azure Portal udostępnia widok tylko do odczytu kluczy interfejsu API. Aby zarządzać kluczami, wybierz link portal usługi Datadog. Po wprowadzeniu zmian w portalu usługi Datadog Odśwież widok Azure Portal.

Integracja z usługą Azure usługi Datadog umożliwia zainstalowanie agenta usługi Datadog na maszynie wirtualnej lub usłudze App Service. Jeśli nie wybrano klucza domyślnego, Instalacja agenta usługi Datadog kończy się niepowodzeniem.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Monitorowanie maszyn wirtualnych za pomocą agenta usługi Datadog

Agentów usługi Datadog można zainstalować na maszynach wirtualnych jako rozszerzenie. Przejdź do pozycji **Agent maszyny wirtualnej** w obszarze konfiguracje organizacji usługi Datadog w lewym okienku. Na tym ekranie zostanie wyświetlona lista wszystkich maszyn wirtualnych w ramach subskrypcji.

Dla każdej maszyny wirtualnej wyświetlane są następujące dane:

- Nazwa zasobu — Nazwa maszyny wirtualnej
- Stan zasobu — czy maszyna wirtualna jest zatrzymana lub uruchomiona. Agenta usługi Datadog można zainstalować tylko na maszynach wirtualnych z systemem. Jeśli maszyna wirtualna jest zatrzymana, Instalacja agenta usługi Datadog zostanie wyłączona.
- Wersja agenta — numer wersji agenta usługi Datadog.
- Stan agenta — czy Agent usługi Datadog jest uruchomiony na maszynie wirtualnej.
- Włączone integracje — kluczowe metryki, które są zbierane przez agenta usługi Datadog.
- Install — narzędzie, które służy do instalowania agenta usługi Datadog. Na przykład Chef lub skrypt.
- Wysyłanie dzienników — czy Agent usługi Datadog wysyła dzienniki do usługi Datadog.

Wybierz maszynę wirtualną, na której ma zostać zainstalowany agent usługi Datadog. Wybierz pozycję **Zainstaluj agenta**.

W portalu zostanie wyświetlony monit o potwierdzenie, że chcesz zainstalować agenta przy użyciu klucza domyślnego. Wybierz **przycisk OK** , aby rozpocząć instalację. Na platformie Azure jest wyświetlany stan **instalacji** , dopóki agent nie zostanie zainstalowany i zainicjowany.

Po zainstalowaniu agenta usługi Datadog stan zmieni się na zainstalowane.

Aby sprawdzić, czy Agent usługi Datadog został zainstalowany, wybierz maszynę wirtualną i przejdź do okna rozszerzenia.

Agentów usługi Datadog można odinstalować na maszynie wirtualnej, przechodząc do **agenta maszyny wirtualnej**. Wybierz maszynę wirtualną i **agenta odinstalowywania**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Monitoruj App Services przy użyciu agenta usługi Datadog jako rozszerzenia

Agentów usługi Datadog można zainstalować w usługach App Services jako rozszerzenie. Przejdź do **rozszerzenia App Service** w okienku po lewej stronie. Na tym ekranie zostanie wyświetlona lista wszystkich usług aplikacji w ramach subskrypcji.

Dla każdej usługi aplikacji wyświetlane są następujące elementy danych:

- Nazwa zasobu — Nazwa maszyny wirtualnej.
- Stan zasobu — czy usługa aplikacji jest zatrzymana lub uruchomiona. Agenta usługi Datadog można zainstalować tylko w usługach App Services, na których działa program. Jeśli usługa aplikacji jest zatrzymana, zainstalowanie agenta usługi Datadog jest wyłączone.
- Plan usługi App Service — konkretny plan skonfigurowany dla usługi App Service.
- Wersja agenta — numer wersji agenta usługi Datadog.

Aby zainstalować agenta usługi Datadog, wybierz rozszerzenie App Service i **Install**. Najnowszy Agent usługi Datadog jest zainstalowany w usłudze App Service jako rozszerzenie.

Portal potwierdza, że chcesz zainstalować agenta usługi Datadog. Ponadto ustawienia aplikacji dla określonej usługi App Service są aktualizowane przy użyciu klucza domyślnego. Usługa App Service jest uruchamiana ponownie po zakończeniu instalacji agenta usługi Datadog.

Wybierz **przycisk OK** , aby rozpocząć proces instalacji agenta usługi Datadog. W portalu jest wyświetlany stan **instalacji** , dopóki agent nie zostanie zainstalowany. Po zainstalowaniu agenta usługi Datadog stan zmieni się na zainstalowane.

Aby odinstalować agentów usługi Datadog w usłudze App Service, przejdź do **rozszerzenia App Service**. Wybierz rozszerzenie usługi App Service i **Odinstaluj**

## <a name="reconfigure-single-sign-on"></a>Skonfiguruj ponownie Logowanie jednokrotne

Jeśli chcesz ponownie skonfigurować Logowanie jednokrotne, wybierz pozycję **Logowanie** jednokrotne w okienku po lewej stronie.

Aby nawiązać Logowanie jednokrotne za pomocą usługi Azure Active Directory, wybierz opcję **Włącz logowanie jednokrotne za pomocą Azure Active Directory**.

Portal pobiera odpowiednią aplikację usługi Datadog z Azure Active Directory. Aplikacja pochodzi z nazwy aplikacji w przedsiębiorstwie wybranej podczas konfigurowania integracji. Wybierz nazwę aplikacji usługi Datadog, jak pokazano poniżej:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Skonfiguruj ponownie aplikację logowania jednokrotnego." border="true":::
 
## <a name="disable-or-enable-integration"></a>Wyłączenie lub włączenie integracji

Można zatrzymać wysyłanie dzienników i metryk z platformy Azure do usługi Datadog. Nadal będą naliczane opłaty za inne usługi usługi Datadog, które nie są związane z monitorowaniem metryk i dzienników.

Aby wyłączyć integrację z platformą Azure z usługą usługi Datadog, przejdź do **omówienia**. Wybierz pozycję **Wyłącz** i **przycisk OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Wyłącz zasób usługi Datadog." border="true":::

Aby włączyć integrację z platformą Azure z usługą usługi Datadog, przejdź do **omówienia**. Wybierz pozycję **Włącz** i **OK**. Wybranie pozycji **Włącz** pobiera każdą poprzednią konfigurację metryk i dzienników. Konfiguracja określa, które zasoby platformy Azure emitują metryki i dzienniki do usługi Datadog. Po wykonaniu kroku metryki i dzienniki są wysyłane do usługi Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Włącz zasób usługi Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Usuń zasób usługi Datadog

Przejdź do **omówienia** w okienku po lewej stronie i wybierz pozycję **Usuń**. Potwierdź, że chcesz usunąć zasób usługi Datadog. Wybierz pozycję **Usuń**.

:::image type="content" source="media/manage/delete.png" alt-text="Usuń zasób usługi Datadog" border="true":::

Jeśli tylko jeden zasób usługi Datadog jest mapowany na organizację usługi Datadog, dzienniki i metryki nie są już wysyłane do usługi Datadog. Wszystkie rozliczenia są zatrzymane dla usługi Datadog za pomocą witryny Azure Marketplace.

Jeśli więcej niż jeden zasób usługi Datadog jest mapowany na organizację usługi Datadog, usunięcie zasobów usługi Datadog powoduje zatrzymanie wysyłania dzienników i metryk dla tego zasobu usługi Datadog. Ponieważ organizacja usługi Datadog jest połączona z innymi zasobami platformy Azure, rozliczenia odbywają się w portalu Azure Marketplace.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej

Aby skontaktować się z pomocą techniczną usługi Azure usługi Datadog, wybierz pozycję **nowe support Request** w lewym okienku. Wybierz łącze do portalu usługi Datadog.

:::image type="content" source="media/manage/support-request.png" alt-text="Utwórz nowe żądanie obsługi usługi Datadog" border="true":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, zobacz [Rozwiązywanie problemów z rozwiązaniami usługi Datadog](troubleshoot.md).
