---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows — Azure
description: Przegląd rozwiązywania problemów podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07857feb2e5552429c445b22c4c4b2f121f29a93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976493"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązania problemów.

## <a name="report-issues-during-public-preview"></a>Zgłoś problemy w trakcie publicznej wersji zapoznawczej

Aby zgłosić problemy lub zasugerować funkcje w publicznej wersji zapoznawczej dla wersji 2020 ze sprężyną, odwiedź [społeczność Tech. systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Możesz użyć społeczności technicznej do omówienia najlepszych praktyk lub zasugerować i zagłosować nowe funkcje. W przypadku zgłaszania problemu związanego z publiczną wersją zapoznawczą upewnij się, że typ problemu jest oznaczony jako **Wiosenna aktualizacja 2020 (wersja zapoznawcza)**.

Po wprowadzeniu monitu z prośbą o pomoc lub zaproponowania nowej funkcji upewnij się, że opiszesz swój temat w jak największej szczegółowości. Szczegółowe informacje mogą pomóc innym użytkownikom odpowiedzieć na pytanie lub poznać funkcję, której dotyczy głos.

## <a name="escalation-tracks"></a>Śledzenie eskalacji

Przed wykonaniem jakichkolwiek innych czynności upewnij się, że Sprawdź [stronę stanu platformy Azure](https://status.azure.com/status) i [Azure Service Health](https://azure.microsoft.com/features/service-health/) , aby upewnić się, że usługa platformy Azure działa prawidłowo.

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które mogą wystąpić podczas konfigurowania środowiska przy użyciu programu Pulpit zdalny Client. Po skonfigurowaniu środowiska możesz użyć naszej nowej [usługi diagnostycznej](diagnostics-role-service.md) , aby zidentyfikować problemy dla typowych scenariuszy.

| **Problem**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Ustawienia puli hostów sesji Azure Virtual Network (VNET) i usługi Express Route               | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz odpowiednią usługę (w obszarze Kategoria sieci). |
| Tworzenie maszyny wirtualnej puli hostów sesji, gdy Azure Resource Manager szablony udostępniane z pulpitem wirtualnym systemu Windows nie są używane | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi. <br> <br> Problemy z szablonami Azure Resource Manager udostępnianymi z pulpitem wirtualnym systemu Windows znajdują się w sekcji Azure Resource Manager szablonów błędów [tworzenia puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji usług pulpitu wirtualnego systemu Windows z Azure Portal    | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> W przypadku problemów z zarządzaniem przy użyciu programu PowerShell Usługi pulpitu zdalnego/Windows Virtual Desktop, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **Konfiguracja i zarządzanie** dla typu problemu, a następnie wybierz pozycję **problemy Konfigurowanie środowiska przy użyciu programu PowerShell** dla typu problemu. |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązane z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz odpowiedni typ problemu.|
| Wdrażanie kontenerów profilów FSLogix i zarządzanie nimi | Zobacz [Przewodnik rozwiązywania problemów dotyczących produktów FSLogix](/fslogix/fslogix-trouble-shooting-ht/) i jeśli nie rozwiąże to problemu, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **FSLogix** dla typu problemu, a następnie wybierz odpowiedni podtyp problemu. |
| Klient pulpitu zdalnego działa nieprawidłowo przy uruchomieniu                                                 | Zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md) i jeśli nie rozwiąże problemu, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu.  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Połączony, ale brak kanału informacyjnego                                                                 | Rozwiązywanie problemów przy użyciu [użytkownika nawiązuje połączenie, ale nic nie jest wyświetlane (brak kanału informacyjnego)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) w obszarze [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu. |
| Problemy z odnajdywaniem źródła danych ze względu na sieć                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md) , a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Problemy z metodami uwierzytelniania innych firm | Sprawdź, czy dostawca innej firmy obsługuje scenariusze pulpitów wirtualnych systemu Windows i podejścia do nich w odniesieniu do znanych problemów. |
| Problemy z używaniem Log Analytics dla pulpitu wirtualnego systemu Windows | Aby uzyskać problemy ze schematem diagnostyki, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>W przypadku zapytań, wizualizacji lub innych problemów w Log Analytics wybierz odpowiedni typ problemu w obszarze Log Analytics. |
| Problemy z używaniem aplikacji M365 | Skontaktuj się z centrum administracyjnym M365, korzystając z jednej z [opcji pomocy centrum administracyjnego M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
