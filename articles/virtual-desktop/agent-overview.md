---
title: Wprowadzenie do agenta pulpitu wirtualnego systemu Windows
description: Omówienie agenta pulpitu wirtualnego systemu Windows i procesów aktualizacji.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 371cc78f3ebad638008f4195f164b66a64948c65
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504553"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Wprowadzenie do agenta pulpitu wirtualnego systemu Windows

W środowisku usługi pulpitu wirtualnego systemu Windows istnieją trzy główne składniki: klienta Pulpit zdalny, usługi i maszyn wirtualnych. Te maszyny wirtualne znajdują się w subskrypcji klienta, w której zainstalowano agenta pulpitu wirtualnego systemu Windows i program inicjujący agenta. Agent działa jako pośredni program Communicator między usługą i maszynami wirtualnymi, umożliwiając łączność. W związku z tym, jeśli występują problemy z instalacją, aktualizacją lub konfiguracją agenta, maszyny wirtualne nie będą mogły nawiązywać połączenia z usługą. Program inicjujący agenta to plik wykonywalny, który ładuje agenta. 

W tym artykule przedstawiono krótkie omówienie instalacji agenta i procesów aktualizacji.

>[!NOTE]
>Ta dokumentacja nie jest dla agenta FSLogix ani agenta klienta Pulpit zdalny.


## <a name="initial-installation-process"></a>Proces instalacji początkowej

Agent pulpitu wirtualnego systemu Windows jest początkowo instalowany na jeden z dwóch sposobów. Po zainicjowaniu obsługi maszyn wirtualnych w Azure Portal i witrynie Azure Marketplace program inicjujący agenta i agenta są instalowane automatycznie. Jeśli inicjujesz maszyny wirtualne przy użyciu programu PowerShell, musisz ręcznie pobrać pliki msi agenta i agenta podczas [tworzenia puli hostów pulpitu wirtualnego systemu Windows przy użyciu programu PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Po zainstalowaniu agenta instalowany jest program Windows Virtual Desktop Side-by-Side Stack i Agent monitorowania Genewa. Składnik stosu Side-by-Side jest wymagany, aby użytkownicy bezpiecznie ustanowiły połączenia odwrotne serwera do klientów. Agent monitorowania Genewa monitoruje kondycję agenta. Wszystkie trzy z tych składników są niezbędne do poprawnego działania kompleksowej łączności z użytkownikami.

>[!IMPORTANT]
>Aby pomyślnie zainstalować agenta pulpitu wirtualnego systemu Windows, stos równoległy i agenta monitorowania Genewa, należy odblokować wszystkie adresy URL wymienione na [liście wymaganych adresów URL](safe-url-list.md#virtual-machines). Odblokowanie tych adresów URL jest wymagane do korzystania z usługi pulpitu wirtualnego systemu Windows.

## <a name="agent-update-process"></a>Proces aktualizacji agenta

Usługa pulpitu wirtualnego systemu Windows aktualizuje agenta za każdym razem, gdy zostanie udostępniona aktualizacja. Aktualizacje agentów mogą zawierać nowe funkcje lub poprawki dotyczące wcześniejszych problemów. Po zainstalowaniu początkowej wersji agenta pulpitu wirtualnego systemu Windows Agent regularnie wysyła zapytanie do usługi pulpitu wirtualnego systemu Windows w celu ustalenia, czy jest dostępna nowsza wersja agenta, stosu lub składnika monitorowania. Jeśli nowsza wersja któregokolwiek ze składników została już wdrożona, zaktualizowany składnik zostanie automatycznie zainstalowany.

Nowe wersje agenta są wdrażane w regularnych odstępach czasu weeklong do wszystkich subskrypcji platformy Azure. Te okresy aktualizacji są nazywane "lotach". W przypadku gdy działa lot, maszyny wirtualne w puli hostów mogą otrzymywać aktualizacje agenta w różnym czasie. Wszystkie agenci maszyn wirtualnych we wszystkich subskrypcjach zostaną zaktualizowani do końca okresu wdrożenia. System obsługi lotu pulpitu wirtualnego systemu Windows zwiększa niezawodność usługi, zapewniając stabilność i jakość aktualizacji agenta.


>[!NOTE]
>Ponieważ maszyny wirtualne w puli hostów mogą odbierać aktualizacje agentów w różnym czasie, należy mieć możliwość poinformowania o różnicach między problemami z lotem i niepowodzeniem aktualizacji agenta. Jeśli przejdziesz do dzienników zdarzeń dla maszyny wirtualnej w **Podgląd zdarzeń**  >  **dzienniku systemu Windows**  >   i zobaczysz zdarzenie o nazwie "ID 3277", oznacza to, że aktualizacja agenta nie zadziałała. Jeśli nie widzisz tego zdarzenia, maszyna wirtualna jest w innym locie i zostanie zaktualizowana później.
>- Gdy Agent monitorowania Genewa aktualizuje do najnowszej wersji, stare zadanie GenevaTask jest zlokalizowane i wyłączone przed utworzeniem nowego zadania dla nowego agenta monitorowania. Starsza wersja agenta monitorowania nie została usunięta w przypadku, gdy Najnowsza wersja agenta monitorowania ma problem wymagający przywrócenia wcześniejszej wersji, aby naprawić. Jeśli Najnowsza wersja ma problem, stary Agent monitorowania zostanie uaktywniony w celu kontynuowania dostarczania danych monitorowania. Wszystkie wersje monitora, które są starsze niż Ostatnia z nich została zainstalowana przed aktualizacją, zostaną usunięte z maszyny wirtualnej.
>- Maszyna wirtualna przechowuje trzy wersje stosu równoległego jednocześnie. Pozwala to na szybkie odzyskiwanie, jeśli wystąpił problem z aktualizacją. Najwcześniejsza wersja stosu jest usuwana z maszyny wirtualnej przy każdej aktualizacji stosu.

Ta instalacja aktualizacji zwykle trwa 2-3 minut na nowej maszynie wirtualnej i nie powinna spowodować utraty połączenia lub zamknięcia maszyny wirtualnej. Ten proces aktualizacji dotyczy zarówno pulpitu wirtualnego systemu Windows (klasycznego), jak i najnowszej wersji pulpitu wirtualnego systemu Windows z Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już agenta pulpitu wirtualnego systemu Windows, Oto kilka zasobów, które mogą Ci pomóc:

- Jeśli występują problemy z agentem lub łącznością, zapoznaj się z [tematem Rozwiązywanie problemów z agentem pulpitu wirtualnego systemu Windows](troubleshoot-agent.md).
