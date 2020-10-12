---
title: Windows Virtual Desktop MSIX — często zadawane pytania — Azure
description: Często zadawane pytania dotyczące dołączania aplikacji MSIX dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556336"
---
# <a name="msix-app-attach-faq"></a>Załączanie aplikacji MSIX — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące dołączania aplikacji MSIX dla pulpitu wirtualnego systemu Windows.

## <a name="does-msix-app-attach-use-fslogix"></a>Czy aplikacja MSIX dołącza użycie FSLogix?

Dołączenie do aplikacji MSIX nie korzysta z FSLogix. Jednak dołączone do aplikacji i FSLogix są przeznaczone do współdziałania w celu zapewnienia bezproblemowego środowiska użytkownika.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Czy mogę użyć dołączania aplikacji MSIX poza pulpitem wirtualnym systemu Windows?

Tak, dołączanie aplikacji MSIX to funkcja, która jest dostępna w systemie Windows 10 Enterprise i może być używana poza pulpitem wirtualnym systemu Windows. Nie istnieje jednak płaszczyzna zarządzania dla MSIX aplikacji poza pulpitem wirtualnym systemu Windows.

## <a name="how-do-i-get-an-msix-package"></a>Jak mogę uzyskać pakiet MSIX?

Dostawca oprogramowania przekaże pakiet MSIX. Możesz również skonwertować Pakiety inne niż MSIX do MSIX. Dowiedz się więcej na [temat przenoszenia istniejących instalatorów do programu MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Które systemy operacyjne obsługują dołączanie aplikacji MSIX?

Wiele sesji dla systemu Windows 10 Enterprise i Windows 10 Enterprise.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o dołączaniu do aplikacji MSIX, zapoznaj się z naszym [słownikiem](app-attach-glossary.md)z [omówieniem](what-is-app-attach.md) . W przeciwnym razie Zacznij od [przygotowania do dołączenia do aplikacji](app-attach.md).