---
title: Windows Virtual Desktop aplikacji MSIX — często zadawane pytania — Azure
description: Często zadawane pytania dotyczące dołączania aplikacji MSIX na Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835366"
---
# <a name="msix-app-attach-faq"></a>Często zadawane pytania dotyczące dołączania aplikacji MSIX

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące dołączania aplikacji MSIX Windows Virtual Desktop.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Jaka jest różnica między dołączania aplikacji MSIX i MSIX?

MSIX to format pakietów dla aplikacji, natomiast dołączanie aplikacji MSIX to funkcja, która dostarcza pakiety MSIX do wdrożenia.

## <a name="does-msix-app-attach-use-fslogix"></a>Czy dołączanie aplikacji MSIX korzysta z produktu FSLogix?

Dołączanie aplikacji MSIX nie używa produktu FSLogix. Jednak dołączanie aplikacji MSIX i fsLogix zostały zaprojektowane tak, aby działały razem w celu zapewnienia bezproblemowego obsługi użytkownika.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>Czy mogę użyć dołączania aplikacji MSIX poza Windows Virtual Desktop?

Interfejsy API dołączane do aplikacji POWER MSIX są dostępne dla Windows 10 Enterprise. Tych interfejsów API można używać poza Windows Virtual Desktop. Nie ma jednak płaszczyzny zarządzania dla dołączania aplikacji MSIX poza Windows Virtual Desktop.

## <a name="how-do-i-get-an-msix-package"></a>Jak mogę uzyskać pakiet MSIX?

Dostawca oprogramowania poda pakiet MSIX. Można również konwertować pakiety inne niż MSIX na MSIX. Aby dowiedzieć się [więcej, zobacz How to move your existing installers to MSIX (Jak](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)przenieść istniejące instalatory do msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Które systemy operacyjne obsługują dołączanie aplikacji MSIX?

Windows 10 Enterprise i Windows 10 Enterprise wiele sesji, wersja 2004 lub nowsza.

## <a name="is-msix-app-attach-currently-generally-available"></a>Czy dołączanie aplikacji MSIX jest obecnie ogólnie dostępne?

Dołączanie aplikacji MSIX jest częścią Windows 10 Enterprise i Windows 10 Enterprise wielu sesji w wersji 2004 lub nowszej. Oba systemy operacyjne są obecnie ogólnie dostępne. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Czy mogę używać dołączania aplikacji MSIX poza Windows Virtual Desktop?

Interfejsy API dołączania aplikacji MSIX i MSIX Windows 10 Enterprise i Windows 10 Enterprise wiele sesji w wersji 2004 i nowszych. Obecnie nie zapewniamy oprogramowania do zarządzania dla dołączania aplikacji MSIX poza Windows Virtual Desktop.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>Czy można uruchomić dwie wersje tej samej aplikacji w tym samym czasie?

Aby dwie wersje tych samych aplikacji MSIX działały jednocześnie, rodzina pakietów MSIX zdefiniowana w pliku appxmanifest.xml musi być inna dla każdej aplikacji.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>Czy należy wyłączyć automatyczną aktualizację podczas korzystania z dołączania aplikacji MSIX?

Tak. Dołączanie aplikacji MSIX nie obsługuje automatycznej aktualizacji dla aplikacji MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Jak działają uprawnienia w przypadku dołączania aplikacji MSIX?

Wszystkie maszyny wirtualne w puli hostów, która korzysta z dołączania aplikacji MSIX, muszą mieć uprawnienia do odczytu w udziałach plików, w których są przechowywane obrazy MSIX. Jeśli korzysta ona również Azure Files, musi mieć przyznane uprawnienia zarówno kontroli dostępu opartej na rolach (RBAC), jak i uprawnień systemu plików NTFS (New Technology File System).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>Czy mogę użyć dołączania aplikacji MSIX dla protokołu HTTP lub HTTPS?

Dołączanie aplikacji MSIX za pośrednictwem protokołu HTTP lub HTTPS nie jest obecnie obsługiwane.

## <a name="can-i-restage-the-same-msix-application"></a>Czy mogę przechowywać tę samą aplikację MSIX?

Tak. Możesz przechowywać aplikacje, które zostały już przechowane, i nie powinno to powodować żadnych błędów.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>Czy dołączanie aplikacji MSIX obsługuje certyfikaty z podpisem własnym?

Tak. Certyfikat z podpisem własnym należy zainstalować na wszystkich komputerach wirtualnych hosta sesji, na których dołączyć aplikację MSIX do hostować aplikację z podpisem własnym.

## <a name="what-applications-can-i-repackage-to-msix"></a>Jakie aplikacje można ponownie pakować do MSIX?

Każda aplikacja używa różnych funkcji systemu operacyjnego, języków programowania i platform. Aby ponownie spakowania aplikacji, postępuj zgodnie z instrukcjami w te tematu Jak przenieść [istniejące instalatory do pliku MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) Listę rzeczy potrzebnych do ponownego spakowywania aplikacji można znaleźć na stronie Prepare to package a desktop application (Przygotowanie do [spakowania aplikacji klasycznej).](/windows/msix/desktop/desktop-to-uwp-prepare) 

Niektórych aplikacji nie można nakładać na warstwy, co oznacza, że nie można ich ponownie pakić do pliku MSIX. Oto lista aplikacji, których nie można ponownie spakowania:

- Sterowniki 
- Active-X lub Silverlight
- Klienci sieci VPN
- Programy antywirusowe

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat dołączania aplikacji MSIX, zapoznaj się z naszym [omówieniem](what-is-app-attach.md) i [słownikiem](app-attach-glossary.md). W przeciwnym razie rozpoczynanie pracy [z konfigurowaniem dołączania aplikacji.](app-attach.md)
