---
title: Przewodnik po komunikacji równorzędnej z punktem wymiany
titleSuffix: Azure
description: Przewodnik po komunikacji równorzędnej z punktem wymiany
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586826"
---
# <a name="exchange-peering-walkthrough"></a>Przewodnik po komunikacji równorzędnej z punktem wymiany

W tej sekcji opisano kroki, które należy wykonać w celu skonfigurowania komunikacji równorzędnej programu Exchange i zarządzania nią.

## <a name="create-an-exchange-peering"></a>Tworzenie komunikacji równorzędnej programu Exchange
> [!div class="mx-imgBorder"]
> ![Przepływ pracy komunikacji równorzędnej programu Exchange i Stany połączeń](./media/exchange-peering.png)

Aby można było zainicjować obsługę komunikacji równorzędnej programu Exchange, należy wykonać następujące czynności:
1. Przejrzyj [zasady komunikacji równorzędnej](https://peering.azurewebsites.net/peering) firmy Microsoft, aby poznać wymagania dotyczące komunikacji równorzędnej programu Exchange.
1. Znajdź lokalizację komunikacji równorzędnej firmy Microsoft i identyfikator funkcji komunikacji równorzędnej w [PeeringDB](https://www.peeringdb.com/net/694)
1. Zażądaj komunikacji równorzędnej programu Exchange dla lokalizacji komunikacji równorzędnej, korzystając z instrukcji w temacie [Tworzenie i modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md) , aby uzyskać więcej informacji.
1. Po przesłaniu żądania komunikacji równorzędnej firma Microsoft sprawdzi żądanie i skontaktuje się z Tobą w razie potrzeby.
1. Po zatwierdzeniu zmiany stanu połączenia na zatwierdzone
1. Skonfiguruj sesję protokołu BGP na swoim końcu i powiadom firmę Microsoft
1. Zostanie zainicjowana sesja BGP z opcją Odmów wszystkich zasad i sprawdzenia poprawności na całej trasie.
1. Jeśli powiedzie się, otrzymasz powiadomienie, że stan połączenia komunikacji równorzędnej jest aktywny.
1. Ruch będzie dozwolony przez nową komunikację równorzędną.

Należy pamiętać, że stan połączenia nie należy mylić ze standardowymi Stanami sesji [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) .

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure
Aby można było skonwertować starszą komunikację równorzędną programu Exchange do zasobu platformy Azure, należy wykonać następujące czynności:
1. Postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure](howto-legacy-exchange-powershell.md)
1. Po przesłaniu żądania konwersji firma Microsoft sprawdzi żądanie i skontaktuje się z Tobą w razie potrzeby.
1. Po zatwierdzeniu zostanie wyświetlona Komunikacja równorzędna z programem Exchange z stanem połączenia jako aktywny.

## <a name="deprovision-exchange-peering"></a>Anulowanie aprowizacji komunikacji równorzędnej programu Exchange
Skontaktuj się z usługą [komunikacji równorzędnej firmy Microsoft](mailto:peering@microsoft.com) , aby anulować obsługę komunikacji równorzędnej programu Exchange.

Gdy Komunikacja równorzędna programu Exchange jest ustawiona na opcję anulowania aprowizacji, stan połączenia zostanie wyświetlony jako **PendingRemove**

> [!NOTE]
> W przypadku uruchomienia polecenia cmdlet programu PowerShell w celu usunięcia komunikacji równorzędnej programu Exchange, gdy stan połączenia to ProvisioningStarted lub ProvisioningCompleted operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wymaganiach wstępnych dotyczących konfigurowania komunikacji równorzędnej z firmą Microsoft](prerequisites.md).
