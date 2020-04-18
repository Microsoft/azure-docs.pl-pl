---
title: Rozszerzenie zasad platformy Azure dla kodu programu Visual Studio
description: Dowiedz się, jak używać rozszerzenia zasad platformy Azure dla programu Visual Studio Code do wyszukiwania aliasów Menedżera zasobów.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 5e31af652f2746adbf0eda386bdb178c752f5f84
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641019"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Używanie rozszerzenia zasad platformy Azure dla kodu programu Visual Studio

> Dotyczy rozszerzenia usługi Azure Policy w wersji **0.0.21** i nowszej

Dowiedz się, jak używać rozszerzenia zasad platformy Azure dla programu Visual Studio Code do wyszukiwania [aliasów](../concepts/definition-structure.md#aliases) i przeglądania zasobów i zasad. Najpierw opiszemy sposób instalowania rozszerzenia zasad platformy Azure w programie Visual Studio Code. Następnie przejdziemy przez to, jak wyszukać aliasy.

Rozszerzenie zasad platformy Azure dla programu Visual Studio Code można zainstalować na wszystkich platformach, które są obsługiwane przez program Visual Studio Code. Ta obsługa obejmuje systemy Windows, Linux i macOS.

> [!NOTE]
> Zmiany wprowadzone lokalnie do zasad wyświetlane w rozszerzeniu zasad platformy Azure dla programu Visual Studio Code nie są synchronizowane z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym artykule wymagane są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Instalowanie rozszerzenia usługi Azure Policy

Po spełnieniu wymagań wstępnych można zainstalować rozszerzenie zasad platformy Azure dla programu Visual Studio Code, wykonując następujące kroki:

1. Otwórz program Visual Studio Code.

1. Na pasku menu przejdź do **opcji Wyświetl** > **rozszerzenia**.

1. W polu wyszukiwania wprowadź usługę **Azure Policy**.

1. Wybierz **pozycję Zasady platformy Azure** z wyników wyszukiwania, a następnie wybierz pozycję **Zainstaluj**.

1. W razie potrzeby wybierz **opcję Przeładuj ponownie.**

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika chmury krajowej wykonaj następujące kroki, aby najpierw ustawić środowisko platformy Azure:

1. Wybierz **pozycję Plik\Preferencje\Ustawienia**.

1. Wyszukiwanie w następującym ciągu: _Azure: Chmura_

1. Wybierz chmurę narodową z listy:

   ![Ustawianie domyślnego logowania się w chmurze platformy Azure dla programu Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Łączenie się z kontem platformy Azure

Aby ocenić zasoby i aliasy wyszukiwania, należy połączyć się z kontem platformy Azure. Wykonaj następujące kroki, aby połączyć się z platformą Azure z programu Visual Studio Code:

1. Zaloguj się do platformy Azure za pomocą rozszerzenia zasad platformy Azure lub palety poleceń.

   - Rozszerzenie zasad platformy Azure

     W rozszerzeniu zasad platformy Azure wybierz pozycję **Zaloguj się na platformie Azure**.

     ![Logowanie się w chmurze platformy Azure dla kodu programu Visual Studio z rozszerzenia zasad platformy Azure](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta poleceń

     Na pasku menu przejdź do **polecenia Wyświetl** > **paletę poleceń**i wprowadź polecenie **Azure: Zaloguj się**.

     ![Logowanie się w chmurze platformy Azure dla kodu programu Visual Studio z palety poleceń](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Postępuj zgodnie z instrukcjami logowania, aby zalogować się na platformie Azure. Po nawiązaniu połączenia nazwa konta platformy Azure jest wyświetlana na pasku stanu u dołu okna Kod programu Visual Studio.

## <a name="select-subscriptions"></a>Wybierz subskrypcje

Podczas pierwszego logowania tylko domyślne zasoby i zasady subskrypcji są ładowane przez rozszerzenie zasad platformy Azure. Aby dodać lub usunąć subskrypcje z wyświetlania zasobów i zasad, wykonaj następujące kroki:

1. Uruchom polecenie subskrypcji z palety poleceń lub stopki okna.

   - Paleta poleceń: 

     Na pasku menu przejdź do **sekcji Wyświetl** > **paletę poleceń**i wprowadź pozycję **Azure: Select Subscriptions**.

   - Stopka okienna

     W stopce okna u dołu ekranu wybierz segment zgodny z **platformą Azure: \<Twoje konto\>**.

1. Użyj pola filtru, aby szybko znaleźć subskrypcje według nazwy. Następnie sprawdź lub usuń czek z każdej subskrypcji, aby ustawić subskrypcje wyświetlane przez rozszerzenie zasad platformy Azure. Po zakończeniu dodawania lub usuwania subskrypcji do wyświetlenia wybierz przycisk **OK**.

## <a name="search-for-and-view-resources"></a>Wyszukiwanie i wyświetlanie zasobów

Rozszerzenie zasad platformy Azure zawiera listę zasobów w wybranych subskrypcjach według dostawcy zasobów i grupy zasobów w okienku **Zasoby.** Treeview obejmuje następujące grupowania zasobów w ramach wybranej subskrypcji lub na poziomie subskrypcji:

- **Dostawcy zasobów**
  - Każdy zarejestrowany dostawca zasobów z zasobami i powiązanymi zasobami podrzędnymi, które mają aliasy zasad
- **Grupy zasobów**
  - Wszystkie zasoby według grupy zasobów, w których się

Domyślnie rozszerzenie filtruje część "Dostawca zasobów" według istniejących zasobów i zasobów, które mają aliasy zasad. Zmień to zachowanie w zasadach**azure** **rozszerzeń** >  **ustawień,** > aby wyświetlić wszystkich dostawców zasobów bez filtrowania.

Klienci z setkami lub tysiącami zasobów w jednej subskrypcji mogą preferować przeszukiwalny sposób lokalizowania swoich zasobów. Rozszerzenie zasad platformy Azure umożliwia wyszukiwanie określonego zasobu za pomocą następujących kroków:

1. Uruchom interfejs wyszukiwania z rozszerzenia zasad platformy Azure lub palety poleceń.

   - Rozszerzenie zasad platformy Azure

     W rozszerzeniu zasady platformy Azure umieść wskaźnik myszy na panelu **Zasoby** i wybierz wielokropek, a następnie wybierz pozycję **Szukaj zasobów**.

   - Paleta poleceń:

     Na pasku menu przejdź do **polecenia Wyświetl** > **paletę poleceń**i wprowadź **zasoby: Wyszukaj zasoby**.

1. Jeśli do wyświetlenia jest wybrana więcej niż jedna subskrypcja, użyj filtru, aby wybrać subskrypcję do wyszukania.

1. Użyj filtru, aby wybrać grupę zasobów do wyszukiwania, która jest częścią wcześniej wybranej subskrypcji.

1. Użyj filtru, aby wybrać zasób do wyświetlenia. Filtr działa zarówno dla nazwy zasobu, jak i dla typu zasobu.

## <a name="discover-aliases-for-resource-properties"></a>Odnajduj aliasy właściwości zasobów

Po wybraniu zasobu, czy za pośrednictwem interfejsu wyszukiwania lub wybierając go w treeview, rozszerzenie zasad platformy Azure otwiera plik JSON reprezentujący ten zasób i wszystkie jego wartości właściwości Menedżera zasobów.

Gdy zasób jest otwarty, najechanie kursorem na nazwę lub wartość właściwości Menedżera zasobów wyświetla alias zasad platformy Azure, jeśli istnieje. W tym przykładzie zasób jest typem zasobu, `Microsoft.Compute/virtualMachines` a **właściwość properties.storageProfile.imageReference.offer** jest najechać kursorem. Kursowanie wskaźnika zawiera pasujące aliasy.

![Hover rozszerzenia zasad platformy Azure pokazuje alias właściwości Menedżera zasobów](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Wyszukiwanie i wyświetlanie zasad i przypisań

Rozszerzenie zasad platformy Azure zawiera listę typów zasad i przypisań zasad jako widok drzewa dla subskrypcji wybranych do wyświetlenia w okienku **Zasady.** Klienci z setkami lub tysiącami zasad lub przypisań w jednej subskrypcji mogą preferować możliwość wyszukiwania, aby zlokalizować swoje zasady lub przypisania. Rozszerzenie usługi Azure Policy umożliwia wyszukiwanie określonych zasad lub przypisania za pomocą następujących kroków:

1. Uruchom interfejs wyszukiwania z rozszerzenia zasad platformy Azure lub palety poleceń.

   - Rozszerzenie zasad platformy Azure

     W rozszerzeniu Zasady platformy Azure umieść wskaźnik myszy na panelu **Zasady** i wybierz wielokropek, a następnie wybierz pozycję **Wyszukaj zasady**wyszukiwania .

   - Paleta poleceń:

     Na pasku menu przejdź do **opcji Wyświetl** > **paletę poleceń**i wprowadź **pozycja Zasady: Wyszukaj zasady**.

1. Jeśli do wyświetlenia jest wybrana więcej niż jedna subskrypcja, użyj filtru, aby wybrać subskrypcję do wyszukania.

1. Użyj filtru, aby wybrać typ lub przypisanie zasad do wyszukiwania, które jest częścią wcześniej wybranej subskrypcji.

1. Użyj filtru, aby wybrać zasady lub wyświetlić. Filtr działa dla _displayName_ dla definicji zasad lub przypisania zasad.

Podczas wybierania zasad lub przypisania, czy za pośrednictwem interfejsu wyszukiwania lub wybierając go w treeview, rozszerzenie zasad platformy Azure otwiera JSON, który reprezentuje zasady lub przypisania i wszystkie jego wartości właściwości Menedżera zasobów. Rozszerzenie może sprawdzić poprawność otwartego schematu JSON usługi Azure Policy.

## <a name="sign-out"></a>Wyloguj

Na pasku menu przejdź do **polecenia Wyświetl** > **paletę poleceń,** a następnie wprowadź polecenie **Azure: Wyloguj .**

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [korygować niezgodne zasoby](remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).