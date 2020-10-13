---
title: StorSimple 8000 Series Update 4 — Informacje o wersji | Microsoft Docs
description: Opisuje nowe funkcje, problemy i obejścia dla StorSimple 8000 serii Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847286"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 — Informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla StorSimple 8000 serii Update 4. Zawierają także listę StorSimple aktualizacji oprogramowania zawartych w tej wersji. 

Aktualizację Update 4 można zastosować do wszystkich urządzeń StorSimple z systemem lub aktualizacji 0,1 za poorednictwem aktualizacji 3,1. Wersja urządzenia skojarzona z aktualizacją Update 4 to 6.3.9600.17820.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple zapoznaj się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
> * Aktualizacja Update 4 obejmuje oprogramowanie urządzenia, oprogramowanie układowe USM, sterownik LSI i oprogramowanie układowe, oprogramowanie układowe dysków, Storport i Spaceport, zabezpieczenia oraz inne aktualizacje systemu operacyjnego. Zainstalowanie tej aktualizacji wymaga około 4 godzin. Aktualizacja oprogramowania układowego dysku to nieprzerwana aktualizacja i skutkuje przestojem urządzenia. Zalecamy zastosowanie aktualizacji Update 4, aby zapewnić aktualność urządzenia. 
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ staną się one dostępne wkrótce.

## <a name="whats-new-in-update-4"></a>Co nowego w aktualizacji Update 4

W aktualizacji Update 4 wprowadzono następujące ulepszenia i poprawki błędów.

* **Inteligentniejsze algorytmy automatycznego odzyskiwania miejsca** — w Update 4 algorytmy odzyskiwania automatycznego miejsca są rozszerzane, aby dostosować cykle odzyskiwania miejsca w oparciu o oczekiwane odzyskane miejsce dostępne w chmurze. 

* **Ulepszenia wydajności dla woluminów przypiętych lokalnie** — Aktualizacja Update 4 poprawiła wydajność woluminów przypiętych lokalnie w scenariuszach, które mają wysoką pozyskiwanie danych (dane porównywalne do rozmiaru woluminu).

* **Przywracanie oparte na mapę cieplną** — we wcześniejszych wersjach, po odzyskaniu po awarii (Dr), dane zostały przywrócone z chmury w oparciu o wzorce dostępu, dzięki czemu można uzyskać niską wydajność. 

    Nowa funkcja jest zaimplementowana w aktualizacji Update 4, która śledzi często używane dane w celu utworzenia mapę cieplną, gdy urządzenie jest używane przed odzyskiwaniem po awarii (większość użytych fragmentów danych ma wysoką temperaturę). Po rozpoczęciu odzyskiwania po awarii program StorSimple używa mapę cieplną do automatycznego przywracania i ponownego ich zadanego w chmurze. 

    Wszystkie operacje przywracania są teraz mapę cieplną oparte na przywracaniu. Aby uzyskać więcej informacji na temat wykonywania zapytań i anulowania zadań przywracania i uzupełniania opartych na mapę cieplną, przejdź do [program Windows PowerShell dla usługi StorSimple dokumentacja poleceń cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **Narzędzie diagnostyczne StorSimple** — w wersji Update 4 narzędzie do diagnostyki StorSimple jest udostępniane, aby umożliwić łatwą diagnostykę i rozwiązywanie problemów dotyczących kondycji składników systemu, sieci, wydajności i sprzętu. To narzędzie jest uruchamiane za pośrednictwem program Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji, przejdź do obszaru [Rozwiązywanie problemów przy użyciu narzędzia Diagnostyka StorSimple](storsimple-8000-diagnostics.md).

* **Narzędzie migracji StorSimple opartego na interfejsie użytkownika** — przed tą wersją migracja danych z serii 5000-7000 wymagała od użytkowników wykonywania części przepływu pracy migracji przy użyciu interfejsu Azure PowerShell. W tej wersji jest dostępne łatwe w użyciu narzędzie do migracji StorSimple oparte na interfejsie użytkownika umożliwiające obsługę tego samego przepływu pracy migracji. To narzędzie umożliwia również konsolidację zasobników odzyskiwania. 

* **Zmiany związane ze standardem FIPS** — ta wersja jest domyślnie włączona na wszystkich urządzeniach z serii StorSimple 8000 dla kont Microsoft Azure government w chmurze publicznej platformy Azure.

* **Aktualizuj zmiany** — w tej wersji rozwiązano błędy związane z błędami aktualizacji.

* **Alert dotyczący błędów dysku** — nowy Alert ostrzegający użytkownika o nieoczekiwanych awariach dysków zostanie dodany w tej wersji. Jeśli wystąpi ten alert, skontaktuj się z pomoc techniczna firmy Microsoft, aby dostarczyć dysk zastępczy. Aby uzyskać więcej informacji, przejdź do [alertów sprzętowych na urządzeniu StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Zmiany zastępcze kontrolera** — polecenie cmdlet umożliwiające użytkownikowi Wysyłanie zapytań o stan procesu zastępującego kontroler jest dodawane w tej wersji. Aby uzyskać więcej informacji, przejdź do [polecenia cmdlet, aby zbadać stan zastępowania kontrolera](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemy rozwiązane w aktualizacji Update 4

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 4.    

| Nie | Promowanie | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Tryb failover |W starszej wersji, po przejściu w tryb failover, wystąpił problem związany z oczyszczaniem zaobserwowany w lokacji klienta. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpił problem z utworzeniem powiązanego woluminu dla woluminów przypiętych lokalnie, które mogłyby spowodować błędy tworzenia woluminu. Ten problem był katalogiem głównym i został usunięty w tej wersji. |Tak |Nie |
| 3 |Pakiet pomocy technicznej |W poprzedniej wersji wystąpiły problemy związane z pakietem pomocy technicznej, które spowodują powstanie wyjątku System. OutOfMemory lub innych błędów spowodowanych błędem tworzenia pakietu dla pomocy technicznej. Te błędy zostały rozwiązane w tej wersji. |Tak |Tak |
| 4 |Monitorowanie |W poprzedniej wersji wystąpił problem związany z wykresami monitorowania dla woluminów przypiętych lokalnie, w których użycie zostało pokazane w EB. Ten błąd jest rozwiązywany w tej wersji. |Tak |Tak |
| 5 |Migracja |W poprzedniej wersji istniały pewne problemy związane z niezawodnością migracji z serii 5000-7000 do 8000. Te problemy zostały rozwiązane w tej wersji. |Tak |Tak |
| 6 |Aktualizacja |W poprzednich wersjach, jeśli wystąpił błąd aktualizacji, kontrolery przejdą w tryb odzyskiwania i w związku z tym użytkownik nie mógł wykonać aktualizacji i będzie musiał skontaktować się z pomoc techniczna firmy Microsoft. <br> To zachowanie zostało zmienione w tej wersji. Jeśli użytkownik ma niepowodzenie aktualizacji, gdy na obu kontrolerach działa ta sama wersja (Aktualizacja Update 4), kontrolery nie przechodzą w tryb odzyskiwania. Jeśli użytkownik napotka ten błąd, zalecamy zaczekanie na chwilę, a następnie ponów próbę aktualizacji. Ponowna próba może się powieść. Jeśli próba nie powiedzie się, należy skontaktować się z pomoc techniczna firmy Microsoft. |Tak |Tak |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Znane problemy z aktualizacją Update 4 z poprzednich wersji

W aktualizacji Update 4 nie ma nowych znanych problemów. Aby zapoznać się z listą problemów przeprowadzonych w ramach aktualizacji Update 4 z poprzednich wersji, przejdź do [Update 3 informacje o wersji](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Aktualizacje kontrolera SCSI (SAS) i oprogramowania układowego dołączonego do urządzeń z aktualizacją Update 4

Ta wersja ma kontroler SAS oraz aktualizacje oprogramowania układowego i sterownika LSI. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [Instalowanie aktualizacji Update 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizacje urządzeń wirtualnych w aktualizacji Update 4

Tej aktualizacji nie można zastosować do urządzenia w chmurze StorSimple (nazywanego również urządzeniem wirtualnym). Należy utworzyć nowe urządzenia wirtualne. 

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizację Update 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

