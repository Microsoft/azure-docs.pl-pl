---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows — Azure
description: Przegląd rozwiązywania problemów podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539113"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Omówienie rozwiązywania problemów, opinie i pomoc techniczna dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązania problemów.

## <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problemy lub zasugerować funkcje pulpitu wirtualnego systemu Windows z integracją Azure Resource Manager, odwiedź [społeczność Tech. systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Możesz użyć społeczności technicznej do omówienia najlepszych praktyk lub zasugerować i zagłosować nowe funkcje.

Po wprowadzeniu monitu z prośbą o pomoc lub zaproponowania nowej funkcji upewnij się, że opiszesz swój temat w jak największej szczegółowości. Szczegółowe informacje mogą pomóc innym użytkownikom odpowiedzieć na pytanie lub poznać funkcję, której dotyczy głos.

## <a name="escalation-tracks"></a>Śledzenie eskalacji

Przed wykonaniem jakichkolwiek innych czynności upewnij się, że Sprawdź [stronę stanu platformy Azure](https://status.azure.com/status) i [Azure Service Health](https://azure.microsoft.com/features/service-health/) , aby upewnić się, że usługa platformy Azure działa prawidłowo.

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które mogą wystąpić podczas konfigurowania środowiska przy użyciu programu Pulpit zdalny Client. Po skonfigurowaniu środowiska możesz użyć naszej nowej [usługi diagnostycznej](diagnostics-role-service.md) , aby zidentyfikować problemy dla typowych scenariuszy.

| **Wykonaj**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Ustawienia puli hostów sesji Azure Virtual Network (VNET) i usługi Express Route               | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz odpowiednią usługę (w obszarze Kategoria sieci). |
| Tworzenie maszyny wirtualnej puli hostów sesji, gdy Azure Resource Manager szablony udostępniane z pulpitem wirtualnym systemu Windows nie są używane | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), a następnie wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi. <br> <br> Problemy z szablonami Azure Resource Manager udostępnianymi z pulpitem wirtualnym systemu Windows znajdują się w sekcji Azure Resource Manager szablonów błędów [tworzenia puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji usług pulpitu wirtualnego systemu Windows z Azure Portal    | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> W przypadku problemów z zarządzaniem przy użyciu programu PowerShell Usługi pulpitu zdalnego/Windows Virtual Desktop, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **Konfiguracja i zarządzanie** dla typu problemu, a następnie wybierz pozycję **problemy Konfigurowanie środowiska przy użyciu programu PowerShell** dla typu problemu. |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązane z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz odpowiedni typ problemu.|
| Wdrażanie kontenerów profilów FSLogix i zarządzanie nimi | Zobacz [Przewodnik rozwiązywania problemów dotyczących produktów FSLogix](/fslogix/fslogix-trouble-shooting-ht/) i jeśli nie rozwiąże to problemu, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **FSLogix** dla typu problemu, a następnie wybierz odpowiedni podtyp problemu. |
| Klient pulpitu zdalnego działa nieprawidłowo przy uruchomieniu                                                 | Zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md) i jeśli nie rozwiąże problemu,  [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu.  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Połączony, ale brak kanału informacyjnego                                                                 | Rozwiązywanie problemów przy użyciu [użytkownika nawiązuje połączenie, ale nic nie jest wyświetlane (brak kanału informacyjnego)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) w obszarze [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji,  [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję **pulpit zdalny klienci** dla typu problemu. |
| Problemy z odnajdywaniem źródła danych ze względu na sieć                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md) , a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Problemy z metodami lub narzędziami uwierzytelniania innych firm | Sprawdź, czy dostawca innej firmy obsługuje scenariusze pulpitów wirtualnych systemu Windows i podejścia do nich w odniesieniu do znanych problemów. |
| Problemy z używaniem Log Analytics dla pulpitu wirtualnego systemu Windows | Aby uzyskać problemy ze schematem diagnostyki, [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>W przypadku zapytań, wizualizacji lub innych problemów w Log Analytics wybierz odpowiedni typ problemu w obszarze Log Analytics. |
| Problemy z używaniem aplikacji M365 | Skontaktuj się z centrum administracyjnym M365, korzystając z jednej z [opcji pomocy centrum administracyjnego M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Rozwiązywanie problemów związanych z agentem pulpitu wirtualnego systemu Windows lub łącznością sesji można znaleźć w temacie [Rozwiązywanie typowych problemów z systemem Windows Virtual Desktop Agent](troubleshoot-agent.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
