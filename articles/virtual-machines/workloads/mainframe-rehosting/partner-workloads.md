---
title: Obciążenia komputerów mainframe obsługiwane w usłudze Azure | Microsoft Docs
description: Użyj emulatora komputera mainframe i innych usług od partnerów firmy Microsoft, aby ponownie hostować obciążenia komputerów mainframe, takie jak systemy oparte na ibm Z, przy użyciu Microsoft Azure.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 05/09/2020
ms.topic: article
ms.openlocfilehash: 0b3dd754e226436cb26cbb808bf37e4262efa344
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599557"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Obciążenia komputerów mainframe obsługiwane na platformie Azure

Wymienione tutaj rozwiązania mogą ułatwić migrację obciążenia komputera mainframe do Microsoft Azure. Niektóre obciążenia można migrować ze względną łatwością. Inne obciążenia, które zależą od starszego oprogramowania systemowego, mogą być ponownie hostowane. 

Aby uzyskać więcej pomocy dotyczącej emulacji komputerów mainframe i usług, zapoznaj się z centrum [migracji mainframe platformy Azure.](https://azure.microsoft.com/migration/mainframe/)

## <a name="migrate-mainframe-closer-to-azure"></a>Migrowanie mainframe bliżej platformy Azure

- [AsNA VisualOWA DLA kompilatora .NET](https://asna.com/us/products/visual-rpg) dla .NET Framework za Visual Studio wtyczki.
- [Środowisko deweloperskie COBOL firmy Asysco AMT](https://www.asysco.com/cobol/) (komputery mainframe Unisys, ibm i inne dialekty COBOL, takie jak Micro Focus COBOL).
- [Oparta na chmurze architektura wdrożenia Asysco AMT GO](https://www.asysco.com/amt-go/) dla obciążeń wysokiej klasy.
- [Asysco AMT Transform](https://www.asysco.com/amt-transform/) — umożliwia konwertowanie danych, kodu, skryptów, zabezpieczeń, interfejsów i innych artefaktów komputerów mainframe.
- Narzędzia do tworzenia i integracji w technologii [NetCOBOL.](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/)
- [Narzędzia do tworzenia i integracji aplikacji Micro Focus Visual COBOL.](https://www.microfocus.com/products/visual-cobol/)
- [Starsza wersja kompilatora Micro Focus PL/I](https://www.microfocus.com/campaign/download/pli-modernization/) dla platformy .NET, obsługująca składnię mainframe PL/I, typy danych i zachowanie.
- [Platforma integracji komputerów](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) mainframe z programem Micro Focus Enterprise Server.
- [Nowoczesne systemy CTU (COBOL-To-Universal) —](https://modernsystems.com/automatic-cobol-to-java-conversion/) narzędzia do tworzenia i integracji.
- [Narzędzia do tworzenia i integracji NTT Data Enterprise COBOL.](https://us.nttdata.com/en/digital/application-development-and-modernization)
- [NTT Open PL/I](https://us.nttdata.com/en/digital/application-development-and-modernization) starszego kompilatora dla platformy .NET, obsługujący składnię PL/I mainframe, typy danych i zachowanie.
- [Narzędzia do tworzenia i integracji kompilatora COBOL oprogramowania Raincode.](https://www.raincode.com/products/cobol/)
- [Kompilator JĘZYKA PL/I raincode](https://www.raincode.com/products/pli/) dla platformy .NET obsługuje składnię PL/I mainframe, typy danych i zachowanie.
- [Kompilator ASM370](https://www.raincode.com/technical-landscape/asm370/) kodu przeciwdeszczowego dla składni mainframe Assembler 370 i HLASM.

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>Wdrażanie środowiska emulacji do przetwarzania online i przetwarzania wsadowego

- [Architektura wdrażania asysco AMT GO](https://www.asysco.com/amt-go/) obsługująca rozwiązania CICS, IMS, TIP, HVTIP i inne typowe środowiska komputerów mainframe.
- [Platforma integracji mainframe micro Focus Enterprise Server.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/)
- [NtT DATA Mainframe Rehosting Development Environment native](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) transaction processing environment (Środowisko przetwarzania transakcji natywnych NTT DATA Mainframe).
- [NTT DATA Batch Processing Environment](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (BPE), w tym możliwości transakcji JCL.
- [Emulator cics programu Raincode](https://www.raincode.com/technical-landscape/cics/) dla platform .NET i Azure.
- [Interpreter JCL](https://www.raincode.com/products/jcl/) zgodny z wtyczką Raincode JCL.

## <a name="code-conversion"></a>Konwersja kodu

- [Technologia konwersji systemu Asysco](https://asysco.com/) obejmująca kod źródłowy, dane, partie, planowanie, monitory TP, interfejsy, zabezpieczenia, zarządzanie i inne.
- [Asysco AMT Services](https://www.asysco.com/migration-services/) end-to-end services for migration projects, including inventory and analysis, design training, re wytchniaki, go-live, and post-migration support.
- [Narzędzia do tworzenia](https://www.bluage.com/) cyfrowych starszych aplikacji biznesowych i baz danych.
- [Heirloom Computing services](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) to convert mainframe COBOL, CICS, and VSAM to Java (Usługi przetwarzania heirloom do konwersji mainframe COBOL, CICS i VSAM na język Java).
- Kontener oprogramowania mainframe zdefiniowanego programowo [LzLabs](https://www.lzlabs.com/) do migrowania aplikacji mainframe do komputerów z systemem Linux lub prywatnych, publicznych i hybrydowych środowisk chmurowych.

## <a name="modernization-services"></a>Usługi modernizacji

Firma Microsoft współpracuje z globalnymi integratorami systemów(GSI), którzy mogą pomóc dużym organizacjom w projektowaniu i kompilowania rozwiązań oraz zarządzaniu nimi. 

- [Centrum migracji komputerów mainframe platformy Azure](https://azure.microsoft.com/migration/mainframe/)
