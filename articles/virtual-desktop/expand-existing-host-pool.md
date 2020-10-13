---
title: Rozwiń istniejącą pulę hostów przy użyciu nowych hostów sesji — Azure
description: Jak rozszerzyć istniejącą pulę hostów na nowe hosty sesji na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929595"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozwiń istniejącą pulę hostów przy użyciu nowych hostów sesji

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

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

4. Wybierz pozycję **hosty sesji** z menu po lewej stronie ekranu.

5. Wybierz pozycję **+ Dodaj** , aby rozpocząć tworzenie puli hostów.

6. Zignoruj kartę podstawowe, a następnie wybierz kartę **Szczegóły maszyny wirtualnej** . W tym miejscu można wyświetlić i edytować szczegóły maszyny wirtualnej, która ma zostać dodana do puli hostów.

7. Wybierz grupę zasobów, w której chcesz utworzyć maszyny wirtualne, a następnie wybierz region. Możesz wybrać bieżący region, który jest używany, lub nowy region.

8. Wprowadź liczbę hostów sesji, które chcesz dodać do puli hostów, do **liczby maszyn wirtualnych**. Na przykład jeśli rozszerzasz pulę hostów o pięć hostów, wprowadź **5**.

    >[!NOTE]
    >Chociaż istnieje możliwość edytowania obrazu i prefiksu maszyn wirtualnych, nie zaleca się ich edytowania w przypadku maszyn wirtualnych z różnymi obrazami w tej samej puli hostów. Edytuj obraz i prefiks tylko wtedy, gdy planujesz usuwanie maszyn wirtualnych ze starszymi obrazami z tej puli.

9. W polu **Informacje o sieci wirtualnej**wybierz sieć wirtualną i podsieć, do której chcesz dołączyć maszyny wirtualne. Możesz wybrać tę samą sieć wirtualną, której aktualnie używają istniejące maszyny, lub wybrać inną, która jest bardziej odpowiednia dla regionu wybranego w kroku 7.

10. W przypadku **konta administratora**wprowadź Active Directory nazwę użytkownika domeny i hasło skojarzone z wybraną siecią wirtualną. Te poświadczenia będą używane do przyłączania maszyn wirtualnych do sieci wirtualnej.

      >[!NOTE]
      >Upewnij się, że nazwy administratorów są zgodne z informacjami podanym w tym miejscu. I że na koncie nie włączono uwierzytelniania wieloskładnikowego.

11. Wybierz kartę **tag** , jeśli masz jakieś znaczniki, które chcesz zgrupować z maszynami wirtualnymi. W przeciwnym razie Pomiń tę kartę.

12. Wybierz kartę **Recenzja + tworzenie** . Przejrzyj wybrane opcje i jeśli wszystko wygląda dobrze, wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

Po rozwinięciu istniejącej puli hostów możesz zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Możesz połączyć się z sesją przy użyciu dowolnego z następujących klientów:

- [Łączenie się z klientem klasycznym systemu Windows](./connect-windows-7-10.md)
- [Łączenie się z klientem internetowym](./connect-web.md)
- [Łączenie się z klientem systemu Android](./connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](./connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](./connect-ios.md)
