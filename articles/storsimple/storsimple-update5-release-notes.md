---
title: StorSimple 8000 Series Update 5 — informacje o wersji
description: Opisuje nowe funkcje, problemy i obejścia dla StorSimple 8000 serii Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76275167"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series Update 5 — informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla programu StorSimple 8000 Series Update 5. Zawierają także listę StorSimple aktualizacji oprogramowania zawartych w tej wersji.

Aktualizację Update 5 można zastosować do dowolnego urządzenia StorSimple z aktualizacją Update 0,1 do Update 4. Wersja urządzenia skojarzona z aktualizacją Update 5 to 6.3.9600.17845.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple należy zapoznać się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
> * Aktualizacja Update 5 to wymagana aktualizacja, która musi zostać zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz temat jak [zastosować aktualizację Update 5](storsimple-8000-install-update-5.md).
> * Aktualizacja Update 5 zawiera oprogramowanie urządzenia, oprogramowanie układowe dysku, zabezpieczenia systemu operacyjnego i inne aktualizacje systemu operacyjnego. Zainstalowanie tej aktualizacji wymaga około 4 godzin. Aktualizacja oprogramowania układowego dysku to nieprzerwana aktualizacja i skutkuje przestojem urządzenia. Zalecamy zastosowanie aktualizacji Update 5, aby zapewnić aktualność urządzenia.
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ te aktualizacje staną się wkrótce dostępne.

## <a name="whats-new-in-update-5"></a>Co nowego w aktualizacji Update 5

W aktualizacji Update 5 wprowadzono następujące ulepszenia dotyczące kluczy i poprawki błędów.

* **Korzystanie z Azure Active Directory (AAD) do uwierzytelniania za pomocą usługi StorSimple Menedżer urządzeń** — od wersji Update 5, Azure Active Directory służy do uwierzytelniania za pomocą usługi StorSimple Menedżer urządzeń. Stary mechanizm uwierzytelniania będzie przestarzały w grudniu 2017. Wszyscy użytkownicy muszą zawierać nowe adresy URL uwierzytelniania w regułach zapory. Aby uzyskać więcej informacji, przejdź do [adresów URL uwierzytelniania wymienionych w temacie Wymagania dotyczące sieci dla urządzenia StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory, użytkownicy zobaczą alert krytyczny, że urządzenie StorSimple nie może uwierzytelnić się w usłudze. Jeśli użytkownicy zobaczą ten alert, muszą uwzględnić nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [StorSimple alerty sieciowe](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nowa wersja programu StorSimple Snapshot Manager** — Nowa wersja StorSimple Snapshot Manager zostanie wydana z aktualizacją Update 5 i jest zgodna ze wszystkimi urządzeniami StorSimple z aktualizacją Update 4 lub nowszą. Zalecamy zaktualizowanie do tej wersji. Poprzednia wersja StorSimple Snapshot Manager jest używana w przypadku urządzeń StorSimple z uruchomioną aktualizacją Update 3 lub wcześniejszą. [Pobierz odpowiednią wersję StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) i zapoznaj się z tematem [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemy rozwiązane w aktualizacji Update 5

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 5.

| Nie | Promowanie | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Komunikacja zdalna programu Windows PowerShell |W poprzedniej wersji użytkownik Wystąpił błąd podczas próby nawiązania połączenia zdalnego z urządzeniem w chmurze StorSimple za pośrednictwem programu Windows PowerShell. Ten problem był katalogiem głównym i został usunięty w tej wersji. |Nie |Tak |
| 2 |Szablony przepustowości |W starszej wersji wystąpił problem z szablonami przepustowości, które spowodowały zmniejszenie przepustowości niż to, dla którego urządzenie zostało skonfigurowane. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 3 |Tryb failover |W poprzedniej wersji, gdy urządzenie z dużą liczbą woluminów zostało przełączone w tryb failover na inne urządzenie z aktualizacją Update 4, proces zakończy się niepowodzeniem podczas próby zastosowania rekordów kontroli dostępu. Ten problem został rozwiązany w tej wersji. |Tak |Tak |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Znane problemy z aktualizacją Update 5 z poprzednich wersji

W aktualizacji Update 5 nie ma nowych znanych problemów. Aby zapoznać się z listą problemów przeprowadzonych w ramach aktualizacji Update 5 z poprzednich wersji, przejdź do [Update 3 informacje o wersji](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Aktualizacje kontrolerów SCSI (SAS) i oprogramowania układowego dołączonego do urządzeń z aktualizacją Update 5

Ta wersja ma kontroler SAS oraz aktualizacje oprogramowania układowego i sterownika LSI. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [Install Update 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizacje urządzeń w chmurze StorSimple w aktualizacji Update 5

Tej aktualizacji nie można zastosować do urządzenia w chmurze StorSimple (nazywanego również urządzeniem wirtualnym). Nowe urządzenia w chmurze muszą być tworzone przy użyciu obrazu Update 5. Aby uzyskać informacje na temat tworzenia urządzenia w chmurze StorSimple, przejdź do obszaru [wdrażanie i zarządzanie urządzeniem w chmurze StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizację Update 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

