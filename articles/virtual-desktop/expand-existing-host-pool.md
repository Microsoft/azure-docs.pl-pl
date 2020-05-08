---
title: Rozwiń istniejącą pulę hostów przy użyciu nowych hostów sesji — Azure
description: Jak rozszerzyć istniejącą pulę hostów na nowe hosty sesji na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: be76c665e1f5319b3e1ff1976e44fee9cd90ea6b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607202"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozwiń istniejącą pulę hostów przy użyciu nowych hostów sesji

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W miarę zwiększania użycia w puli hostów może być konieczne rozszerzenie istniejącej puli hostów na nowe hosty sesji w celu obsługi nowego obciążenia.

W tym artykule przedstawiono, jak można rozszerzyć istniejącą pulę hostów na nowe hosty sesji.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co jest potrzebne do rozwinięcia puli hostów

Przed rozpoczęciem upewnij się, że utworzono pulę hostów i maszyny wirtualne hosta sesji (VM), korzystając z jednej z następujących metod:

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](./create-host-pools-powershell.md)

Podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji wymagane są również następujące informacje:

- Prefiks rozmiaru, obrazu i nazwy maszyny wirtualnej
- Poświadczenia administratora przyłączania do domeny
- Nazwa sieci wirtualnej i nazwa podsieci

## <a name="add-virtual-machines-with-the-azure-portal"></a>Dodaj maszyny wirtualne z Azure Portal

Aby rozszerzyć pulę hostów przez dodanie maszyn wirtualnych:

1. Zaloguj się do Portalu Azure.

2. Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows**.

3. W menu po lewej stronie ekranu wybierz pozycję **Pule hostów**, a następnie wybierz nazwę puli hostów, do której chcesz dodać maszyny wirtualne.

4. Z menu po lewej stronie ekranu wybierz pozycję **maszyny wirtualne** .

5. Wybierz pozycję **+ Dodaj** , aby rozpocząć tworzenie puli hostów.

6. Zignoruj kartę podstawowe, a następnie wybierz kartę **Szczegóły maszyny wirtualnej** . W tym miejscu można wyświetlić i edytować szczegóły maszyny wirtualnej, która ma zostać dodana do puli hostów.

7. Wybierz grupę zasobów, w której chcesz utworzyć maszyny wirtualne, a następnie wybierz region. Możesz wybrać bieżący region, który jest używany, lub nowy region.
   
8. Wprowadź nową łączną liczbę hostów sesji na **liczbę maszyn wirtualnych**. Na przykład, jeśli rozszerzasz pulę hostów z pięciu sesji na osiem, wprowadź **8**. 
   
    >[!NOTE]
    >Nie można edytować rozmiaru ani obrazu maszyn wirtualnych, ponieważ ważne jest, aby upewnić się, że wszystkie maszyny wirtualne w puli hostów mają ten sam rozmiar.
    
9. W polu **Informacje o sieci wirtualnej**wybierz sieć wirtualną i podsieć, do której chcesz dołączyć maszyny wirtualne. Możesz wybrać tę samą sieć wirtualną, której aktualnie używają istniejące maszyny, lub wybrać inną, która jest bardziej odpowiednia dla regionu wybranego w kroku 7.

10. W przypadku **konta administratora**wprowadź Active Directory nazwę użytkownika domeny i hasło skojarzone z wybraną siecią wirtualną. Te poświadczenia będą używane do przyłączania maszyn wirtualnych do sieci wirtualnej.

      >[!NOTE]
      >Upewnij się, że nazwy administratorów są zgodne z informacjami podanym w tym miejscu. I że na koncie nie włączono uwierzytelniania wieloskładnikowego.

11. Wybierz kartę **tag** , jeśli masz jakieś znaczniki, które chcesz zgrupować z maszynami wirtualnymi. W przeciwnym razie Pomiń tę kartę. 

12. Wybierz kartę **Recenzja + tworzenie** . Przejrzyj wybrane opcje, a jeśli wszystko wygląda dobrze, wybierz pozycję **Utwórz**. 

## <a name="next-steps"></a>Następne kroki

Po rozwinięciu istniejącej puli hostów możesz zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Możesz połączyć się z sesją przy użyciu dowolnego z następujących klientów:

- [Łączenie się z klientem klasycznym systemu Windows](./connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](./connect-web.md)
- [Łączenie się z klientem systemu Android](./connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](./connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](./connect-ios.md)
