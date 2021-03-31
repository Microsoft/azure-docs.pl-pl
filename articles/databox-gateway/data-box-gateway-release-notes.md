---
title: Informacje o wersji ogólnych dostępności Azure Data Box Gateway | Microsoft Docs
description: W tym artykule opisano krytyczne problemy i rozwiązania dotyczące Azure Data Box Gateway uruchamiania wersji ogólnie dostępnej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96583016"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Informacje o wersji ogólnych dostępności Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wersji ogólnej dostępności dla Azure Data Box Edge i Azure Data Box Gateway. 

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem Data Box Edge/Data Box Gateway należy uważnie zapoznać się z informacjami zawartymi w informacjach o wersji.

Wersja GA odnosi się do wersji oprogramowania:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Co nowego

- **Nowe obrazy dysków wirtualnych** — nowe pliki VHDX i VMDK są teraz dostępne w Azure Portal. Pobierz te obrazy, aby udostępnić, skonfigurować i wdrożyć nowe Data Box Gateway na urządzeniach. W tej wersji nie można zaktualizować urządzeń Data Box Gateway utworzonych w starszych wersjach zapoznawczych. Aby uzyskać więcej informacji, przejdź do obszaru [Przygotowywanie do wdrożenia Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Obsługa systemu plików NFS** — obsługa systemu plików NFS jest obecnie dostępna w wersji zapoznawczej dla klientów 3.0 i v 4.1, które uzyskują dostęp do Data Box Edge i Data Box Gateway urządzeń.
- **Odporność magazynu** — urządzenie Data Box Edge może wytrzymać awarię jednego dysku z danymi przy użyciu funkcji odporności magazynu. Ta funkcja jest obecnie w wersji zapoznawczej. Odporność magazynu można włączyć, wybierając opcję **odporny** w **ustawieniach magazynu** w lokalnym interfejsie użytkownika sieci Web.


## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji GA

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących Data Box Gateway z uruchomioną wersją programu.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Typy plików | Następujące typy plików nie są obsługiwane: pliki znaków, pliki blokowe, gniazda, potoki, linki symboliczne.  |Skopiowanie tych plików spowoduje utworzenie plików o zerowej długości dla udziału NFS. Te pliki pozostają w stanie błędu i są również zgłaszane w *error.xml*. <br> Symboliczne linki do katalogów powodują, że nigdy nie są oznaczone jako offline. W związku z tym może nie być widoczne szare przekroczenie katalogów wskazujących, że katalogi są w trybie offline, a cała skojarzona zawartość została całkowicie przekazana na platformę Azure. |
| **2.** |Usunięcie | Z powodu błędu w tej wersji, jeśli udział NFS zostanie usunięty, udział nie może zostać usunięty. Stan udziału *zostanie wyświetlony.*  |Dzieje się tak tylko wtedy, gdy udział korzysta z nieobsługiwanej nazwy pliku. |
| **3.** |Kopiuj | Kopiowanie danych kończy się niepowodzeniem z powodu błędu: Żądana operacja nie może zostać zakończona ze względu na ograniczenie systemu plików.  |Alternatywny strumień danych (ADS) skojarzony z rozmiarem pliku większym niż 128 KB nie jest obsługiwany.   |


## <a name="next-steps"></a>Następne kroki

- [Przygotuj się do wdrożenia Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Przygotuj się do wdrożenia Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md).
