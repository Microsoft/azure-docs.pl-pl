---
title: Uruchomienie systemu operacyjnego — komputer został nieoczekiwanie uruchomiony ponownie lub wystąpił nieoczekiwany błąd
description: W tym artykule przedstawiono kroki rozwiązywania problemów z maszyną wirtualną w przypadku nieoczekiwanego ponownego uruchomienia lub błędu podczas instalowania systemu Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036186"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Uruchomienie systemu operacyjnego — komputer został nieoczekiwanie uruchomiony ponownie lub wystąpił nieoczekiwany błąd

W tym artykule przedstawiono kroki rozwiązywania problemów, w których maszyna wirtualna (VM) napotyka nieoczekiwane ponowne uruchomienie lub błąd podczas instalowania systemu Windows.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla niepowodzenie instalacji systemu Windows z następującym błędem:

**Komputer został nieoczekiwanie uruchomiony ponownie lub wystąpił nieoczekiwany błąd. Nie można przeprowadzić instalacji systemu Windows. Aby zainstalować system Windows, kliknij przycisk OK, aby ponownie uruchomić komputer, a następnie ponownie uruchom instalację.**

![Błąd podczas instalowania systemu Windows: komputer został nieoczekiwanie uruchomiony ponownie lub wystąpił nieoczekiwany błąd. Nie można przeprowadzić instalacji systemu Windows. Aby zainstalować system Windows, kliknij przycisk OK, aby ponownie uruchomić komputer, a następnie ponownie uruchom instalację.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Błąd podczas uruchamiania usług Instalatora systemu Windows: komputer został nieoczekiwanie uruchomiony lub wystąpił nieoczekiwany błąd. Nie można przeprowadzić instalacji systemu Windows. Aby zainstalować system Windows, kliknij przycisk OK, aby ponownie uruchomić komputer, a następnie ponownie uruchom instalację.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Przyczyna

Maszyna próbuje wykonać początkowe rozruch [uogólnionego obrazu](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), ale napotyka problem z powodu niestandardowego pliku odpowiedzi (unattend.xml), który jest przetwarzany. Niestandardowe pliki odpowiedzi nie są obsługiwane na platformie Azure. 

Plik odpowiedzi jest specjalnym plikiem XML, który zawiera definicje i wartości ustawień konfiguracji, które mają być zautomatyzowane podczas instalacji instalacji systemu operacyjnego Windows Server. Opcje konfiguracji obejmują instrukcje dotyczące dzielenia dysków na partycje, gdzie można znaleźć obraz systemu Windows, który ma zostać zastosowany, i innych poleceń, które mają być uruchamiane.

Na platformie Azure niestandardowe pliki odpowiedzi nie są obsługiwane. Jeśli określono niestandardowy plik **Unattend.xml** przy użyciu `sysprep /unattend:<your file’s name>` opcji, ten błąd może wystąpić.

Na platformie Azure Użyj opcji **Wprowadź system out-of-Box Experience (OOBE)** w **Sysprep.exe**lub Użyj `sysprep /oobe` zamiast pliku Unattend.xml.

Ten problem jest najczęściej tworzony podczas korzystania z **Sysprep.exe** z lokalną maszyną wirtualną w celu przekazania UOGÓLNIONEJ maszyny wirtualnej do platformy Azure. W takiej sytuacji warto również zainteresować, jak prawidłowo przekazać uogólnioną maszynę wirtualną.

## <a name="solution"></a>Rozwiązanie

### <a name="replace-unattended-answer-file-option"></a>Zastąp opcję pliku odpowiedzi instalacji nienadzorowanej

Ta sytuacja występuje, gdy obraz został przygotowany do użytku na platformie Azure, ale użył niestandardowego pliku odpowiedzi, który nie jest obsługiwany na platformie Azure, a użyto **narzędzia Sysprep** z flagą podobną do następującego polecenia:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- W poprzednim poleceniu Zamień na `<NameOfYourAnswerFile.XML>` nazwę pliku.

Aby rozwiązać ten problem, postępuj zgodnie [ze wskazówkami platformy Azure dotyczącymi przygotowania/przechwytywania obrazu](../windows/upload-generalized-managed.md) i przygotowania nowego uogólnionego obrazu. W trakcie działania programu Sysprep nie należy używać `/unattend:<answerfile>` flagi. Zamiast tego należy użyć tylko poniższych flag:

`sysprep /oobe /generalize /shutdown`

- **Środowisko** uruchomieniowe (OOBE) to obsługiwane ustawienie dla maszyn wirtualnych platformy Azure.

Możesz również użyć **graficznego interfejsu użytkownika narzędzia przygotowywania systemu** , aby wykonać to samo zadanie co powyższe polecenie, wybierając poniższe opcje:

- Wprowadź wbudowane środowisko
- Generalize
- Zamykanie
 
![Okno narzędzia przygotowania systemu z wybranymi opcjami OOBE, generalize i zamykania.](./media/unexpected-restart-error-during-vm-boot/3.png)
