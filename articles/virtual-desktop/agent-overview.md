---
title: Wprowadzenie do agenta Windows Virtual Desktop
description: Omówienie procesów Windows Virtual Desktop agenta i aktualizacji.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520109"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Wprowadzenie do agenta Windows Virtual Desktop

W środowisku Windows Virtual Desktop Service istnieją trzy główne składniki: klient usługi Pulpit zdalny, usługa i maszyny wirtualne. Te maszyny wirtualne znajdują się w subskrypcji klienta, w której Windows Virtual Desktop i program rozruchowy agenta. Agent działa jako pośredni komunikator między usługą i maszynami wirtualnymi, umożliwiając łączność. W związku z tym, jeśli występują problemy z instalacją, aktualizacją lub konfiguracją agenta, maszyny wirtualne nie będą mogły nawiązać połączenia z usługą. Program rozruchowy agenta jest plikiem wykonywalnym, który ładuje agenta. 

Ten artykuł zawiera krótkie omówienie procesów instalacji i aktualizacji agenta.

>[!NOTE]
>Ta dokumentacja nie dotyczy agenta FSLogix ani Pulpit zdalny klienta.


## <a name="initial-installation-process"></a>Proces instalacji początkowej

Agent Windows Virtual Desktop jest początkowo instalowany na jeden z dwóch sposobów. Jeśli aprowizujesz maszyny wirtualne w Azure Portal i Azure Marketplace, agent i program rozruchowy agenta zostaną automatycznie zainstalowane. W przypadku aprowizowania maszyn wirtualnych przy użyciu programu PowerShell należy ręcznie pobrać pliki MSI agenta i agenta rozruchowego podczas tworzenia puli hostów Windows Virtual Desktop [programu PowerShell.](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) Po zainstalowaniu agenta instaluje on Windows Virtual Desktop stosie side-by-side i agent monitorowania Porteny. Składnik stosu side-by-side jest wymagany, aby użytkownicy bezpiecznie ustanawiali połączenia odwrotne serwer-klient. Agent monitorowania Porteny monitoruje kondycję agenta. Wszystkie trzy z tych składników są niezbędne do prawidłowego działania łączności użytkowników końcowych.

>[!IMPORTANT]
>Aby pomyślnie zainstalować agenta usługi Windows Virtual Desktop, stos side-by-side i agenta monitorowania Porteny, należy odblokować wszystkie adresy URL wymienione na liście [Wymagany adres URL.](safe-url-list.md#virtual-machines) Odblokowanie tych adresów URL jest wymagane do korzystania Windows Virtual Desktop usługi.

## <a name="agent-update-process"></a>Proces aktualizacji agenta

Usługa Windows Virtual Desktop aktualizuje agenta zawsze wtedy, gdy aktualizacja stanie się dostępna. Aktualizacje agenta mogą zawierać nowe funkcje lub poprawki dla poprzednich problemów. Zawsze musisz mieć zainstalowaną najnowszą stabilną wersję agenta, aby maszyny wirtualne nie utraciły łączności ani bezpieczeństwa. Po zainstalowaniu początkowej wersji agenta usługi Windows Virtual Desktop agent regularnie wysyła zapytanie do usługi Windows Virtual Desktop, aby ustalić, czy jest dostępna nowsza wersja agenta, stosu lub składnika monitorowania. Jeśli nowsza wersja dowolnego ze składników została już wdrożona, zaktualizowany składnik jest automatycznie instalowany przez system testowy.

Nowe wersje agenta są wdrażane w regularnych odstępach czasu w okresach tygodnia we wszystkich subskrypcjach platformy Azure. Te okresy aktualizacji są nazywane "lotami". W przypadku lotu maszyny wirtualne w puli hostów mogą otrzymywać aktualizację agenta w różnym czasie. Wszyscy agenci maszyn wirtualnych we wszystkich subskrypcjach zostaną zaktualizowani do końca okresu wdrażania. System Windows Virtual Desktop lotu zwiększa niezawodność usługi, zapewniając stabilność i jakość aktualizacji agenta.


Inne ważne rzeczy, o których należy pamiętać:

- Ponieważ maszyny wirtualne w puli hostów mogą otrzymywać aktualizacje agenta w różnym czasie, musisz wiedzieć, jaka jest różnica między problemami z lotem a aktualizacjami agenta, które zakończyły się niepowodzeniem. Jeśli trafisz do dzienników zdarzeń maszyny wirtualnej w aplikacji **dzienników** systemu Windows usługi Podgląd zdarzeń i zobaczysz zdarzenie z etykietą  >    >   "IDENTYFIKATOR 3277", oznacza to, że aktualizacja agenta nie zadziałała. Jeśli nie widzisz tego zdarzenia, maszyna wirtualna jest w innym locie i zostanie zaktualizowana później.
- Po aktualizacji agenta monitorowania Porteny do najnowszej wersji przed utworzeniem nowego zadania dla nowego agenta monitorowania znajduje się i wyłącza stare zadanie Aplikacji Portesk. Wcześniejsza wersja agenta monitorowania nie jest usuwana w przypadku, gdy najnowsza wersja agenta monitorowania ma problem, który wymaga przywrócenia wcześniejszej wersji do rozwiązania. Jeśli w najnowszej wersji występuje problem, stary agent monitorowania zostanie ponownie włączony, aby kontynuować dostarczanie danych monitorowania. Wszystkie wersje monitora, które są starsze niż ostatnia zainstalowana przed aktualizacją, zostaną usunięte z maszyny wirtualnej.
- Maszyna wirtualna przechowuje jednocześnie trzy wersje stosu side-by-side. Umożliwia to szybkie odzyskiwanie, jeśli coś pójdzie nie tak z aktualizacją. Najwcześniejsza wersja stosu jest usuwana z maszyny wirtualnej przy każdej aktualizacji stosu.

Aktualizacja agenta zwykle trwa 2–3 minuty na nowej maszynie wirtualnej i nie powinna powodować utraty połączenia lub zamknięcia maszyny wirtualnej. Ten proces aktualizacji dotyczy zarówno wersji Windows Virtual Desktop (klasycznej), jak i najnowszej wersji Windows Virtual Desktop z Azure Resource Manager.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy lepiej rozumiesz agenta Windows Virtual Desktop, oto kilka zasobów, które mogą Ci pomóc:

- Jeśli występują problemy z agentem lub łącznością, zapoznaj się z przewodnikiem rozwiązywania problemów z Windows Virtual Desktop [agentem.](troubleshoot-agent.md)
