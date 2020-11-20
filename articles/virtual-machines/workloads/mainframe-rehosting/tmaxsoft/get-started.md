---
title: Rozpocznij pracę z usługą TmaxSoft OpenFrame na platformie Azure Virtual Machines
description: Przehostaj obciążenia systemu mainframe firmy IBM z/OS przy użyciu środowiska TmaxSoft OpenFrame na platformie Azure Virtual Machines.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: 215a17ced6be4cc8792ac1a06115450bfbccac99
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963270"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Rozpoczynanie pracy z usługą TmaxSoft OpenFrame na platformie Azure

Weź istniejące zasoby mainframe i przenieś je do Microsoft Azure za pomocą TmaxSoft OpenFrame. To popularne rozwiązanie do hostowania tworzy środowisko emulacji na platformie Azure, co umożliwia szybkie Migrowanie aplikacji. Nie jest wymagane ponowne formatowanie.

## <a name="openframe-rehosting-environment"></a>Środowisko rehostowania OpenFrame

Skonfiguruj środowisko OpenFrame na platformie Azure na potrzeby tworzenia, demonstracji, testowania lub obciążeń produkcyjnych. Jak widać na poniższej ilustracji, OpenFrame zawiera wiele składników, które tworzą środowisko emulacji mainframe na platformie Azure. Na przykład OpenFrame Usługi online zastąpić oprogramowanie typu mainframe, takie jak IBM Customer Information Control System (CICS). OpenFrame Batch, z jego składnikiem TJES, zastępuje podsystem wpisów zadań programu IBM mainframe (JES). 

![Proces ponownego hostowania OpenFrame](media/openframe-01.png)

> [!NOTE]
> Aby uruchomić środowisko OpenFrame na platformie Azure, musisz mieć ważną licencję produktu lub licencję próbną od TmaxSoft.

## <a name="openframe-components"></a>Składniki OpenFrame

Następujące składniki są częścią środowiska OpenFrame na platformie Azure:

- **Narzędzia migracji** , w tym OFMiner, rozwiązanie, które analizuje zasoby komputerów mainframe, a następnie migruje je do platformy Azure.
- **Kompilatory**, w tym OFCOBOL, kompilator, który interpretuje programy COBOL komputera mainframe; OFPLI, który interpretuje programy PL/I firmy mainframe; i OFASM, kompilator, który interpretuje programy asemblera programu mainframe.
- Składniki **frontonu** , w tym rozwiązanie firmy Java Enterprise User (JEUS), serwer aplikacji sieci Web, który jest certyfikowany dla języka Java Enterprise Edition 6. OFGW i składnik bramy OpenFrame, który udostępnia odbiornik 3270.
- Środowisko **aplikacji** . OpenFrame Base to oprogramowanie pośredniczące, które zarządza całym systemem. Typ serwera OpenFrame C (OSC) zastępuje oprogramowanie pośredniczące i IBM CICS.
- **Relacyjna baza danych**, taka jak Tibero (pokazana), Oracle Database, Microsoft SQL Server, IBM DB2 lub MySQL. Aplikacje OpenFrame używają protokołu Open Database Connectivity (ODBC) do komunikacji z bazą danych.
- **Zabezpieczenia** za pośrednictwem TACF, modułu usługi, który kontroluje dostęp użytkowników do systemów i zasobów. 
- **OFManager** to rozwiązanie, które udostępnia funkcje OpenFrame i zarządzania w środowisku sieci Web.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Następne kroki

- [Zainstaluj program TmaxSoft OpenFrame na platformie Azure](./install-openframe-azure.md)
