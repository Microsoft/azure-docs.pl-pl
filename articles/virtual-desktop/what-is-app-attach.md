---
title: Omówienie dołączania aplikacji MSIX pulpitu wirtualnego systemu Windows — Azure
description: Co to jest dołączanie aplikacji MSIX? Zapoznaj się z tym artykułem.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88556346"
---
# <a name="what-is-msix-app-attach"></a>Co to jest dołączanie aplikacji MSIX?

MSIX to nowy format pakietu, który oferuje wiele funkcji przeznaczonych do ulepszania środowiska tworzenia pakietów dla wszystkich aplikacji systemu Windows. Aby dowiedzieć się więcej na temat MSIX, zobacz [Omówienie usługi MSIX](/windows/msix/overview).

Dołączenie do aplikacji MSIX to sposób dostarczania aplikacji MSIX do maszyn fizycznych i wirtualnych. Jednak dołączenie aplikacji MSIX różni się od zwykłych MSIX, ponieważ jest ono szczególnie przeznaczone dla pulpitu wirtualnego systemu Windows. W tym artykule opisano, co to jest dołączenie do aplikacji MSIX.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Opcje dostarczania aplikacji w programie Virtual Desktop systemu Windows

Możesz dostarczać aplikacje na pulpicie wirtualnym systemu Windows za pomocą jednej z następujących metod:

- Umieszczanie aplikacji w obrazie głównym
- Korzystanie z narzędzi takich jak SCCM lub Intune na potrzeby zarządzania centralnego
- Dynamiczna obsługa aplikacji (AppV, VMWare AppVolumes lub Citrix AppLayering)
- Tworzenie niestandardowych narzędzi lub skryptów przy użyciu narzędzi firmy Microsoft i innych firm

## <a name="what-does-msix-app-attach-do"></a>Do czego służy aplikacja MSIX?

W przypadku wdrożenia pulpitu wirtualnego systemu Windows dołączanie aplikacji MSIX może:

- Utwórz rozdzielenie danych użytkownika, systemu operacyjnego i aplikacji przy użyciu [kontenerów MSIX](/windows/msix/msix-container).
- Usuń potrzebę ponownego pakowania podczas dynamicznego dostarczania aplikacji.
- Skrócenie czasu, przez jaki użytkownik może się zalogować.
- Zmniejsz wymagania dotyczące infrastruktury i kosztów.

Dołączenie do aplikacji MSIX musi być stosowane poza infrastrukturą VDI lub SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Tradycyjna warstwa aplikacji w porównaniu do dołączania aplikacji MSIX

Poniższa tabela zawiera porównanie najważniejszych funkcji dołączania aplikacji MSIX i warstw aplikacji.

| Cecha | Tradycyjna warstwa aplikacji  | Dołączanie aplikacji MSIX  |
|-----|-----------------------------|--------------------|
| Format               | Różne technologie warstw aplikacji wymagają różnych formatów własnościowych. | Działa z natywnym formatem pakowania MSIX.        |
| Ponowne pakowanie | Formaty zastrzeżone wymagają sekwencjonowania i ponownego pakowania na aktualizację.         | Aplikacje opublikowane jako MSIX nie wymagają ponownego pakowania. Jeśli jednak pakiet MSIX nie jest dostępny, nadal obowiązuje ponowne pakowanie. |
| Ekosystem            | Nie dotyczy (na przykład dostawców nie dostarcza aplikacji App-V)  | MSIX to podstawowe technologie firmy Microsoft, dzięki którym partnerzy niezależnego dostawcy oprogramowania i aplikacje wewnętrzne, takie jak pakiet Office, są wdrażane. MSIX można używać zarówno na pulpitach wirtualnych, jak i fizycznych komputerach z systemem Windows. |
| Infrastruktura       | Wymagana jest dodatkowa infrastruktura (serwery, klienci itd.) | Tylko magazyn   |
| Administracja       | Wymaga konserwacji i aktualizacji   | Upraszcza aktualizacje aplikacji |
| Środowisko użytkownika      | Wpływa na czas logowania użytkownika. Istnieje granica między stanem systemu operacyjnego, stanem aplikacji i danymi użytkownika.  | Dostarczone aplikacje są nierozróżniane w zainstalowanych lokalnie aplikacjach. |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dołączaniu do aplikacji MSIX, zapoznaj się z naszym [słownikiem](app-attach-glossary.md) i [często zadawanymi pytaniami](app-attach-faq.md). W przeciwnym razie Zacznij od [przygotowania do dołączenia do aplikacji](app-attach.md).
