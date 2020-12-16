---
title: Przewodnik po bezpośredniej komunikacji równorzędnej
titleSuffix: Azure
description: Przewodnik po bezpośredniej komunikacji równorzędnej
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592385"
---
# <a name="direct-peering-walkthrough"></a>Przewodnik po bezpośredniej komunikacji równorzędnej

W tej sekcji opisano kroki, które należy wykonać w celu skonfigurowania bezpośredniej komunikacji równorzędnej i zarządzania nią.

## <a name="create-a-direct-peering"></a>Tworzenie bezpośredniej komunikacji równorzędnej
> [!div class="mx-imgBorder"]
> ![Bezpośredni przepływ pracy komunikacji równorzędnej i Stany połączeń](./media/direct-peering.png)

Aby można było zainicjować bezpośrednią komunikację równorzędną, należy wykonać następujące czynności:
1. Przejrzyj [zasady komunikacji równorzędnej](https://peering.azurewebsites.net/peering) firmy Microsoft, aby poznać wymagania dotyczące bezpośredniej komunikacji równorzędnej.
1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej](howto-direct-powershell.md) w celu przesłania żądania komunikacji równorzędnej.
1. Po przesłaniu żądania komunikacji równorzędnej firma Microsoft skontaktuje się z Tobą przy użyciu zarejestrowanego adresu e-mail w celu udostępnienia DOWANIU (list autoryzacji) lub innych informacji.
1. Gdy żądanie komunikacji równorzędnej zostanie zatwierdzone, stan połączenia zmieni się na ProvisioningStarted.
1. Należy:
    1. Ukończ okablowanie zgodnie z DOWANIU
    1. (opcjonalnie) wykonaj test łącza za pomocą 169.254.0.0/16
    1. Skonfiguruj sesję BGP, a następnie powiadom nas.
1. Firma Microsoft inicjuje sesję protokołu BGP z opcją Odmów wszystkich zasad i walidacji na całej trasie.
1. Jeśli powiedzie się, otrzymasz powiadomienie, że stan połączenia komunikacji równorzędnej jest aktywny.
1. Ruch będzie dozwolony przez nową komunikację równorzędną.

Należy pamiętać, że stan połączenia nie należy mylić ze standardowymi Stanami sesji [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) .

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure
Aby przekonwertować starszą bezpośrednią komunikację równorzędną na zasób platformy Azure, należy wykonać następujące czynności:
1. Postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure](howto-legacy-direct-powershell.md)
1. Po przesłaniu żądania konwersji firma Microsoft sprawdzi żądanie i skontaktuje się z Tobą w razie potrzeby.
1. Po zatwierdzeniu zostanie wyświetlona bezpośrednia Komunikacja równorzędna ze stanem połączenia jako aktywny.

## <a name="deprovision-direct-peering"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
Skontaktuj się z zespołem [komunikacji równorzędnej firmy Microsoft](mailto:peering@microsoft.com) w celu anulowania aprowizacji bezpośredniej komunikacji równorzędnej.

Po ustawieniu bezpośredniej komunikacji równorzędnej na potrzeby anulowania aprowizacji stan połączenia zostanie wyświetlony jako **PendingRemove**

> [!NOTE]
> Jeśli uruchamiasz polecenie cmdlet programu PowerShell w celu usunięcia bezpośredniej komunikacji równorzędnej, gdy ConnectionState jest ProvisioningStarted lub ProvisioningCompleted, operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wymaganiach wstępnych dotyczących konfigurowania komunikacji równorzędnej z firmą Microsoft](prerequisites.md).
