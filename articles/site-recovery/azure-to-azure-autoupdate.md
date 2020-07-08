---
title: Automatyczna aktualizacja usługi mobilności w Azure Site Recovery
description: Przegląd automatycznej aktualizacji usługi mobilności podczas replikowania maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985605"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności w replikacji na platformie Azure na platformę Azure

Azure Site Recovery używa comiesięcznego wydania erze, aby rozwiązać wszelkie problemy i ulepszyć istniejące funkcje lub dodać nowe. Aby zachować aktualność w usłudze, należy zaplanować wdrożenie poprawek co miesiąc. Aby uniknąć narzutu związanego z każdym uaktualnieniem, można zezwolić Site Recovery na zarządzanie aktualizacjami składników.

Jak wspomniano w [architekturze odzyskiwania po awarii platformy Azure](azure-to-azure-architecture.md)na platformę Azure, usługa mobilności jest instalowana na wszystkich maszynach wirtualnych platformy Azure, które mają włączoną replikację z jednego regionu platformy Azure do innego. W przypadku korzystania z funkcji Aktualizacje automatyczne każda nowa wersja aktualizuje rozszerzenie usługi mobilności.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak działają aktualizacje automatyczne

Użycie Site Recovery do zarządzania aktualizacjami powoduje wdrożenie globalnego elementu Runbook (używanego przez usługi platformy Azure) za pośrednictwem konta usługi Automation utworzonego w tej samej subskrypcji co magazyn. Każdy magazyn używa jednego konta usługi Automation. Dla każdej maszyny wirtualnej w magazynie element Runbook sprawdza aktywne aktualizacje. Jeśli dostępna jest nowsza wersja rozszerzenia usługi mobilności, aktualizacja zostanie zainstalowana.

Domyślny harmonogram elementu Runbook występuje codziennie o godzinie 12:00 w strefie czasowej zreplikowanej maszyny wirtualnej. Harmonogram elementów Runbook można także zmienić za pomocą konta usługi Automation.

> [!NOTE]
> Począwszy od [pakietu zbiorczego aktualizacji 35](site-recovery-whats-new.md#updates-march-2019), można wybrać istniejące konto usługi Automation, które ma być używane na potrzeby aktualizacji. Przed aktualizacją pakietu zbiorczego 35, Site Recovery utworzyć konto usługi Automation domyślnie. Tę opcję można wybrać tylko po włączeniu replikacji dla maszyny wirtualnej. Nie jest dostępna dla maszyny wirtualnej, która ma już włączoną replikację. Wybrane ustawienie ma zastosowanie do wszystkich maszyn wirtualnych platformy Azure chronionych w tym samym magazynie.

Włączenie funkcji Aktualizacje automatyczne nie wymaga ponownego uruchomienia maszyn wirtualnych platformy Azure lub ma wpływ na trwającą replikację.

Rozliczenia zadania na koncie usługi Automation bazują na liczbie minut wykonywania zadań w miesiącu. Wykonanie zadania trwa kilka sekund do około minuty i jest objęte bezpłatną jednostką. Domyślnie 500 minut jest uwzględniana jako wolne jednostki dla konta usługi Automation, jak pokazano w poniższej tabeli:

| Bezpłatne jednostki (w każdym miesiącu) | Price |
|---|---|
| Czas wykonywania zadania 500 minuty | ₹ 0.14/minutę

## <a name="enable-automatic-updates"></a>Włącz aktualizacje automatyczne

Istnieje kilka sposobów, Site Recovery mogą zarządzać aktualizacjami rozszerzeń:

- [Zarządzanie w ramach kroku włączania replikacji](#manage-as-part-of-the-enable-replication-step)
- [Przełącz ustawienia aktualizacji rozszerzenia w magazynie](#toggle-the-extension-update-settings-inside-the-vault)
- [Ręczne zarządzanie aktualizacjami](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Zarządzanie w ramach kroku włączania replikacji

Po włączeniu replikacji dla maszyny wirtualnej, rozpoczynając [od widoku maszyny wirtualnej](azure-to-azure-quickstart.md) lub [z magazynu usługi Recovery Services](azure-to-azure-how-to-enable-replication.md), można zezwolić Site Recovery na zarządzanie aktualizacjami Site Recovery rozszerzenia lub zarządzać nimi ręcznie.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Ustawienia rozszerzenia":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełącz ustawienia aktualizacji rozszerzenia w magazynie

1. W magazynie Recovery Services przejdź do pozycji **Zarządzaj**  >  **infrastrukturą Site Recovery**.
1. W obszarze **dla**  >  **opcji Ustawienia aktualizacji rozszerzenia**Virtual Machines Azure  >  **Zezwalaj Site Recovery na zarządzanie**, wybierz pozycję **włączone**.

   Aby zarządzać rozszerzeniem ręcznie, wybierz pozycję **wyłączone**.

1. Wybierz pozycję **Zapisz**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Ustawienia aktualizacji rozszerzeń":::

> [!IMPORTANT]
> Po wybraniu opcji **zezwól Site Recovery na zarządzanie**, ustawienie jest stosowane do wszystkich maszyn wirtualnych w magazynie.

> [!NOTE]
> Każda opcja powiadamia użytkownika o koncie usługi Automation używanym do zarządzania aktualizacjami. Jeśli ta funkcja jest używana w magazynie po raz pierwszy, domyślnie tworzone jest nowe konto usługi Automation. Alternatywnie możesz dostosować ustawienie i wybrać istniejące konto usługi Automation. Wszystkie kolejne operacje mają na celu włączenie replikacji w tym samym magazynie będą używały wcześniej utworzonego konta usługi Automation. Obecnie menu rozwijane będzie wyświetlać tylko konta usługi Automation, które znajdują się w tej samej grupie zasobów co magazyn.

### <a name="manage-updates-manually"></a>Ręczne zarządzanie aktualizacjami

1. Jeśli na maszynach wirtualnych są zainstalowane nowe aktualizacje usługi mobilności, zobaczysz następujące powiadomienie: **dostępna jest nowa aktualizacja agenta Site Recovery replikacji. Kliknij, aby zainstalować.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno zreplikowane elementy":::

1. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny wirtualnej.
1. Wybierz Maszyny wirtualne, które chcesz uaktualnić, a następnie wybierz przycisk **OK**. Usługa mobilności aktualizacji rozpocznie się dla każdej wybranej maszyny wirtualnej.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista replikowanych elementów maszyn wirtualnych":::

## <a name="common-issues-and-troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli wystąpi problem z aktualizacjami automatycznymi, zobaczysz powiadomienie o błędzie w obszarze **problemy z konfiguracją** na pulpicie nawigacyjnym magazynu.

Jeśli nie możesz włączyć funkcji Aktualizacje automatyczne, zobacz następujące typowe błędy i zalecane akcje:

- **Błąd**: nie masz uprawnień do utworzenia konta Uruchom jako platformy Azure (nazwy głównej usługi) i Udziel roli współautor do jednostki usługi.

  **Zalecana akcja**: Upewnij się, że konto zalogowane jest przypisane jako współautor, i spróbuj ponownie. Więcej informacji o przypisywaniu uprawnień znajduje się w sekcji wymagane uprawnienia w temacie [How to: Use the Portal Service, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Aby rozwiązać większość problemów po włączeniu funkcji Aktualizacje automatyczne, wybierz pozycję **napraw**. Jeśli przycisk Napraw nie jest dostępny, Sprawdź komunikat o błędzie wyświetlany w okienku Ustawienia aktualizacji rozszerzenia.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Przycisk naprawy usługi Site Recovery w ustawieniach aktualizacji rozszerzenia":::

- **Błąd**: konto Uruchom jako nie ma uprawnień dostępu do zasobu usługi Recovery Services.

  **Zalecana akcja**: Usuń, a następnie [Utwórz ponownie konto Uruchom jako](/azure/automation/automation-create-runas-account). Lub upewnij się, że aplikacja Azure Active Directory konta Uruchom jako usługi Automation ma dostęp do zasobu usługi Recovery Services.

- **Błąd**: nie można odnaleźć konta Uruchom jako. Jeden z tych elementów został usunięty lub nie został utworzony — aplikacja Azure Active Directory, nazwa główna usługi, rola, zasób certyfikatu usługi Automation, zasób połączenia usługi Automation lub odcisk palca nie są identyczne między certyfikatem a połączeniem.

  **Zalecana akcja**: Usuń, a następnie [Utwórz ponownie konto Uruchom jako](/azure/automation/automation-create-runas-account).

- **Błąd**: certyfikat Uruchom jako platformy Azure używany przez konto usługi Automation wkrótce wygaśnie.

  Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa w jednym roku od daty utworzenia. Można go odnowić w dowolnym momencie przed wygaśnięciem jego ważności. Jeśli zarejestrowano się w celu otrzymywania powiadomień pocztą e-mail, otrzymasz również wiadomości e-mail, gdy wymagana jest akcja z Twojej strony. Ten błąd zostanie wyświetlony dwa miesiące przed datą wygaśnięcia i zmieni się na błąd krytyczny, jeśli certyfikat wygasł. Po wygaśnięciu certyfikatu funkcja autoaktualizacji nie będzie działać, dopóki nie odnowisz tego samego.

  **Zalecana akcja**: Aby rozwiązać ten problem, wybierz pozycję **napraw** , a następnie **odnów certyfikat**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="Odnów certyfikat":::

  > [!NOTE]
  > Po odnowieniu certyfikatu Odśwież stronę, aby wyświetlić bieżący stan.
