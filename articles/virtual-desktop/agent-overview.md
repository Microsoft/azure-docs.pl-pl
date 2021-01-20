---
title: Wprowadzenie do agenta pulpitu wirtualnego systemu Windows
description: Omówienie agenta pulpitu wirtualnego systemu Windows i procesów aktualizacji.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 325502255e84e38a39ca5b90ee4126354c0d425b
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601243"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Wprowadzenie do agenta pulpitu wirtualnego systemu Windows

W środowisku usługi pulpitu wirtualnego systemu Windows istnieją trzy główne składniki: klienta Pulpit zdalny, usługi i maszyn wirtualnych. Te maszyny wirtualne znajdują się w subskrypcji klienta, w której zainstalowano agenta pulpitu wirtualnego systemu Windows i program inicjujący agenta. Agent działa jako pośredni program Communicator między usługą i maszynami wirtualnymi, umożliwiając łączność. W związku z tym, jeśli występują problemy z instalacją, aktualizacją lub konfiguracją agenta, maszyny wirtualne nie będą mogły nawiązywać połączenia z usługą. Program inicjujący agenta to plik wykonywalny, który ładuje agenta. 

W tym artykule przedstawiono krótkie omówienie instalacji agenta i procesów aktualizacji.

>[!NOTE]
>Ta dokumentacja nie jest dla agenta FSLogix ani agenta klienta Pulpit zdalny.


## <a name="initial-installation-process"></a>Proces instalacji początkowej

Agent pulpitu wirtualnego systemu Windows jest początkowo instalowany na jeden z dwóch sposobów. Po zainicjowaniu obsługi maszyn wirtualnych w Azure Portal i witrynie Azure Marketplace program inicjujący agenta i agenta są instalowane automatycznie. Jeśli inicjujesz maszyny wirtualne przy użyciu programu PowerShell, musisz ręcznie pobrać pliki msi agenta i agenta podczas [tworzenia puli hostów pulpitu wirtualnego systemu Windows przy użyciu programu PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Po zainstalowaniu agenta można także zainstalować jednocześnie stos klasyczny pulpitu wirtualnego (Side-by-Side) systemu Windows i agenta monitorowania Genewa. Składnik stosu Side-by-Side jest wymagany, aby użytkownicy bezpiecznie ustanowiły połączenia odwrotne serwera do klientów. Agent monitorowania Genewa monitoruje kondycję agenta. Wszystkie trzy z tych składników są niezbędne do poprawnego działania kompleksowej łączności z użytkownikami.

>[!IMPORTANT]
>Aby pomyślnie zainstalować agenta pulpitu wirtualnego systemu Windows, stos równoległy i agenta monitorowania Genewa, należy odblokować wszystkie adresy URL wymienione na [liście wymaganych adresów URL](safe-url-list.md#virtual-machines). Odblokowanie tych adresów URL jest wymagane do korzystania z usługi pulpitu wirtualnego systemu Windows.

## <a name="agent-update-process"></a>Proces aktualizacji agenta

Usługa pulpitu wirtualnego systemu Windows automatycznie aktualizuje agenta za każdym razem, gdy zostanie udostępniona aktualizacja. Aktualizacje agentów mogą zawierać nowe funkcje lub naprawić poprzednie problemy. Po zainstalowaniu początkowej wersji agenta pulpitu wirtualnego systemu Windows Agent regularnie wysyła zapytanie do usługi pulpitu wirtualnego systemu Windows w celu ustalenia, czy jest dostępna nowsza wersja agenta i jego składników. W przypadku nowej wersji program inicjujący agenta automatycznie pobiera najnowszą wersję agenta, stos równoległy i agenta monitorowania Genewa.

>[!NOTE]
>- Gdy Agent monitorowania Genewa aktualizuje do najnowszej wersji, stare zadanie GenevaTask jest zlokalizowane i wyłączone przed utworzeniem nowego zadania dla nowego agenta monitorowania. Starsza wersja agenta monitorowania nie została usunięta w przypadku, gdy Najnowsza wersja agenta monitorowania ma problem wymagający przywrócenia wcześniejszej wersji, aby naprawić. Jeśli Najnowsza wersja ma problem, stary Agent monitorowania zostanie uaktywniony w celu kontynuowania dostarczania danych monitorowania. Wszystkie wersje monitora, które są starsze niż Ostatnia z nich została zainstalowana przed aktualizacją, zostaną usunięte z maszyny wirtualnej.
>- Maszyna wirtualna przechowuje trzy wersje stosu równoległego jednocześnie. Pozwala to na szybkie odzyskiwanie, jeśli wystąpił problem z aktualizacją. Najwcześniejsza wersja stosu jest usuwana z maszyny wirtualnej przy każdej aktualizacji stosu.

Ta instalacja aktualizacji zwykle trwa 2-3 minut na nowej maszynie wirtualnej i nie powinna spowodować utraty połączenia lub zamknięcia maszyny wirtualnej. Ten proces aktualizacji dotyczy zarówno pulpitu wirtualnego systemu Windows (klasycznego), jak i najnowszej wersji pulpitu wirtualnego systemu Windows z Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już agenta pulpitu wirtualnego systemu Windows, Oto kilka zasobów, które mogą Ci pomóc:

- Jeśli występują problemy z agentem lub łącznością, zapoznaj się z [tematem Rozwiązywanie problemów z agentem pulpitu wirtualnego systemu Windows](troubleshoot-agent.md).