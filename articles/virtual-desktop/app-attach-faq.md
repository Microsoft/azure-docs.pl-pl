---
title: Windows Virtual Desktop MSIX — często zadawane pytania — Azure
description: Często zadawane pytania dotyczące dołączania aplikacji MSIX dla pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a1a2464735ff8039bb31c43d6956cbf03e694c63
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448410"
---
# <a name="msix-app-attach-faq"></a>Załączanie aplikacji MSIX — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące dołączania aplikacji MSIX dla pulpitu wirtualnego systemu Windows.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Jaka jest różnica między dołączaniem aplikacji MSIX i MSIX?

MSIX to format pakietu dla aplikacji, podczas gdy dołączanie do aplikacji MSIX to funkcja, która dostarcza pakiety MSIX do wdrożenia.

## <a name="does-msix-app-attach-use-fslogix"></a>Czy aplikacja MSIX dołącza użycie FSLogix?

Dołączenie do aplikacji MSIX nie korzysta z FSLogix. Jednak dołączenie i FSLogix aplikacji MSIX są przeznaczone do współdziałania w celu zapewnienia bezproblemowego środowiska użytkownika.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Czy mogę użyć aplikacji MSIX, która jest dołączana poza pulpitem wirtualnym systemu Windows?

Interfejsy API, które są dołączone do aplikacji MSIX, są dostępne dla systemu Windows 10 Enterprise. Te interfejsy API mogą być używane poza pulpitem wirtualnym systemu Windows. Nie istnieje jednak płaszczyzna zarządzania dla MSIX aplikacji poza pulpitem wirtualnym systemu Windows.

## <a name="how-do-i-get-an-msix-package"></a>Jak mogę uzyskać pakiet MSIX?

Dostawca oprogramowania przekaże pakiet MSIX. Możesz również skonwertować Pakiety inne niż MSIX do MSIX. Dowiedz się więcej na [temat przenoszenia istniejących instalatorów do programu MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Które systemy operacyjne obsługują dołączanie aplikacji MSIX?

Wiele sesji systemu Windows 10 Enterprise i Windows 10 Enterprise, wersja 2004 lub nowsza.

## <a name="is-msix-app-attach-currently-generally-available"></a>Czy dołączenie do aplikacji MSIX jest obecnie ogólnie dostępne?

Dołączenie do aplikacji MSIX jest częścią wielosesyjnych systemów Windows 10 Enterprise i Windows 10 Enterprise, w wersji 2004 lub nowszej. Oba systemy operacyjne są obecnie ogólnie dostępne. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Czy mogę użyć dołączania aplikacji MSIX poza pulpitem wirtualnym systemu Windows?

Interfejsy API dołączania aplikacji MSIX i MSIX są częścią wielu sesji systemu Windows 10 Enterprise i Windows 10 Enterprise, w wersji 2004 i nowszych. Obecnie nie zapewniamy oprogramowania do zarządzania do dołączania aplikacji MSIX poza pulpitem wirtualnym systemu Windows.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Czy mogę uruchomić dwie wersje tej samej aplikacji w tym samym czasie?

Aby jednocześnie uruchamiać dwie wersje tych samych aplikacji MSIX, Rodzina pakietów MSIX zdefiniowana w pliku appxmanifest.xml musi być inna dla każdej aplikacji.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Czy po dołączeniu do aplikacji MSIX należy wyłączyć automatyczne aktualizowanie?

Tak. Dołączenie do aplikacji MSIX nie obsługuje aktualizacji automatyczne dla aplikacji MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Jak uprawnienia współpracują z aplikacją MSIX?

Wszystkie maszyny wirtualne w puli hostów używające dołączania aplikacji MSIX muszą mieć uprawnienia do odczytu w udziale plików, w którym są przechowywane obrazy MSIX. Jeśli używa ona również Azure Files, muszą mieć przyznane uprawnienia kontroli dostępu opartej na rolach (RBAC) i nowego systemu plików (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Czy mogę użyć dołączania aplikacji MSIX dla protokołu HTTP lub HTTPs?

Używanie funkcji dołączania aplikacji MSIX za pośrednictwem protokołu HTTP lub HTTPs nie jest obecnie obsługiwane.

## <a name="can-i-restage-the-same-msix-application"></a>Czy mogę przemieścić tę samą aplikację MSIX?

Tak. Można przemieścić aplikacje, które zostały już przemieszczone, i nie powinno to spowodować żadnych błędów.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Czy aplikacja MSIX obsługuje certyfikaty z podpisem własnym?

Tak. Musisz zainstalować certyfikat z podpisem własnym na wszystkich maszynach wirtualnych hosta sesji, na których dołączenie aplikacji MSIX jest używane do hostowania aplikacji z podpisem własnym.


## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat dołączania do aplikacji MSIX, zapoznaj się z [omówieniem](what-is-app-attach.md) i [słownikiem](app-attach-glossary.md). W przeciwnym razie Zacznij od [przygotowania do dołączenia do aplikacji](app-attach.md).
