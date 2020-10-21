---
title: Azure Policy rozszerzenie dla Visual Studio Code
description: Dowiedz się, jak używać rozszerzenia Azure Policy Visual Studio Code do wyszukiwania aliasów Azure Resource Manager.
ms.date: 10/20/2020
ms.topic: how-to
ms.openlocfilehash: 233c9158c30d6c373dd6147090894dc83b83da3d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317612"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Użyj rozszerzenia Azure Policy dla Visual Studio Code

> Dotyczy rozszerzenia Azure Policy w wersji **0.1.0** i nowszej

Dowiedz się, jak używać rozszerzenia Azure Policy Visual Studio Code do wyszukiwania [aliasów](../concepts/definition-structure.md#aliases), przeglądania zasobów i zasad, eksportowania obiektów i oceniania definicji zasad. Najpierw opisano sposób instalowania rozszerzenia Azure Policy w programie Visual Studio Code. Następnie przeprowadzimy procedurę wyszukiwania aliasów.

Azure Policy rozszerzenia Visual Studio Code można zainstalować na wszystkich platformach obsługiwanych przez Visual Studio Code. Ta obsługa obejmuje systemy Windows, Linux i macOS.

> [!NOTE]
> Zmiany wprowadzone lokalnie do zasad wyświetlanych w rozszerzeniu Azure Policy dla Visual Studio Code nie są synchronizowane z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- [Program Visual Studio Code](https://code.visualstudio.com)

## <a name="install-azure-policy-extension"></a>Zainstaluj rozszerzenie Azure Policy

Po spełnieniu wymagań wstępnych można zainstalować Azure Policy rozszerzenia dla Visual Studio Code, wykonując następujące czynności:

1. Otwórz program Visual Studio Code.

1. Na pasku menu Przejdź do pozycji **Wyświetl**  >  **rozszerzenia**.

1. W polu wyszukiwania wprowadź **Azure Policy**.

1. Wybierz **Azure Policy** z wyników wyszukiwania, a następnie wybierz pozycję **Zainstaluj**.

1. W razie potrzeby wybierz pozycję **Załaduj ponownie** .

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika w chmurze krajowej wykonaj następujące kroki, aby najpierw ustawić środowisko platformy Azure:

1. Wybierz pozycję **File\Preferences\Settings**.

1. Wyszukaj następujący ciąg: _Azure: Cloud_

1. Wybierz z listy chmurę krajową:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Zrzut ekranu przedstawiający wybór kraju logowania w chmurze platformy Azure dla Visual Studio Code." border="false":::

## <a name="connect-to-an-azure-account"></a>Nawiązywanie połączenia z kontem platformy Azure

Aby oszacować zasoby i aliasy wyszukiwania, musisz nawiązać połączenie z kontem platformy Azure. Wykonaj następujące kroki, aby nawiązać połączenie z platformą Azure z Visual Studio Code:

1. Zaloguj się do platformy Azure przy użyciu rozszerzenia Azure Policy lub palety poleceń.

   - Azure Policy rozszerzenie

     Z poziomu rozszerzenia Azure Policy wybierz pozycję **Zaloguj się do platformy Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Zrzut ekranu przedstawiający wybór kraju logowania w chmurze platformy Azure dla Visual Studio Code." border="false":::

   - Paleta poleceń

     Na pasku menu Przejdź do **widoku**  >  **paleta poleceń**i wprowadź **Azure: Zaloguj się**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Zrzut ekranu przedstawiający wybór kraju logowania w chmurze platformy Azure dla Visual Studio Code." border="false":::

1. Postępuj zgodnie z instrukcjami logowania, aby zalogować się do platformy Azure. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu u dołu okna Visual Studio Code.

## <a name="select-subscriptions"></a>Wybieranie subskrypcji

Po pierwszym zalogowaniu tylko domyślne zasoby i zasady subskrypcji są ładowane przy użyciu rozszerzenia Azure Policy. Aby dodać lub usunąć subskrypcje wyświetlania zasobów i zasad, wykonaj następujące kroki:

1. Uruchom polecenie subskrypcji za pomocą palety poleceń lub stopki okna.

   - Paleta poleceń: 

     Na pasku menu Przejdź do **widoku** > **paleta poleceń**i wprowadź **Azure: wybierz subskrypcje**.

   - Stopka okna

     W stopce okna u dołu ekranu wybierz segment pasujący do **platformy Azure: \<your account\> **.

1. Użyj pola filtr, aby szybko znaleźć subskrypcje według nazwy. Następnie zaznacz lub usuń zaznaczenie każdej subskrypcji, aby ustawić subskrypcje widoczne przez rozszerzenie Azure Policy. Po zakończeniu dodawania lub usuwania subskrypcji do wyświetlenia wybierz **przycisk OK**.

## <a name="search-for-and-view-resources"></a>Wyszukiwanie i wyświetlanie zasobów

Rozszerzenie Azure Policy wyświetla listę zasobów w wybranych subskrypcjach według dostawcy zasobów i grupy zasobów w okienku **zasoby** . Widok TreeView zawiera następujące grupy zasobów w ramach wybranej subskrypcji lub na poziomie subskrypcji:

- **Dostawcy zasobów**
  - Każdy zarejestrowany dostawca zasobów z zasobami i powiązanymi zasobami podrzędnymi, które mają aliasy zasad
- **Grupy zasobów**
  - Wszystkie zasoby według grupy zasobów, w której się znajdują

Domyślnie rozszerzenie filtruje część "dostawca zasobów" według istniejących zasobów i zasobów, które mają aliasy zasad. Zmień to zachowanie w **Settings**  >  **rozszerzeniu**ustawienia  >  **Azure Policy** , aby wyświetlić wszystkich dostawców zasobów bez filtrowania.

Klienci, którzy mają setki lub tysiące zasobów w ramach jednej subskrypcji, mogą preferować możliwość wyszukiwania swoich zasobów. Rozszerzenie Azure Policy umożliwia wyszukanie określonego zasobu, wykonując następujące czynności:

1. Uruchom interfejs wyszukiwania z rozszerzenia Azure Policy lub palety poleceń.

   - Azure Policy rozszerzenie

     W rozszerzeniu Azure Policy Zatrzymaj wskaźnik myszy na panelu **zasoby** i wybierz wielokropek, a następnie wybierz pozycję **Wyszukaj zasoby**.

   - Paleta poleceń:

     Na pasku menu Przejdź do **widoku** > **paleta poleceń**i wprowadź **zasoby: wyszukaj zasoby**.

1. Jeśli wybrano więcej niż jedną subskrypcję do wyświetlania, Użyj filtru, aby wybrać subskrypcję do wyszukania.

1. Użyj filtru, aby wybrać grupę zasobów do przeszukania, która jest częścią poprzednio wybranej subskrypcji.

1. Użyj filtru, aby wybrać zasób do wyświetlenia. Filtr działa zarówno dla nazwy zasobu, jak i dla typu zasobu.

## <a name="discover-aliases-for-resource-properties"></a>Odnajdź aliasy dla właściwości zasobów

Po wybraniu zasobu, niezależnie od tego, czy za pomocą interfejsu wyszukiwania lub wybierając go w widoku drzewa, rozszerzenie Azure Policy otwiera plik JSON reprezentujący ten zasób i wszystkie jego Azure Resource Manager wartości właściwości.

Gdy zasób jest otwarty, wskaźnik myszy nad nazwą właściwości Menedżer zasobów lub wartością wyświetla alias Azure Policy, jeśli taki istnieje. W tym przykładzie zasób jest `Microsoft.Compute/virtualMachines` typem zasobu, a właściwość **właściwości. obszarze storageprofile. elementu imagereference. Offer** jest umieszczana na początku. Umieszczenie kursora powoduje wyświetlenie pasujących aliasów.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Zrzut ekranu przedstawiający wybór kraju logowania w chmurze platformy Azure dla Visual Studio Code." border="false":::

> [!NOTE]
> Rozszerzenie VS Code uwidacznia tylko właściwości trybu Menedżer zasobów i nie wyświetla żadnych właściwości [trybu dostawcy zasobów](../concepts/definition-structure.md#mode) .

## <a name="search-for-and-view-policies-and-assignments"></a>Wyszukiwanie i wyświetlanie zasad i przypisań

Rozszerzenie Azure Policy wyświetla listę typów zasad i przypisań zasad jako element TreeView dla subskrypcji wybranych do wyświetlania w okienku **zasady** . Klienci, którzy mają setki lub tysiące zasad lub przypisań w ramach jednej subskrypcji, mogą należeć do wyszukiwania, aby znaleźć swoje zasady lub przydziały. Rozszerzenie Azure Policy umożliwia wyszukanie określonych zasad lub przypisanie przy użyciu następujących kroków:

1. Uruchom interfejs wyszukiwania z rozszerzenia Azure Policy lub palety poleceń.

   - Azure Policy rozszerzenie

     W rozszerzeniu Azure Policy Zatrzymaj wskaźnik myszy na panelu **zasady** i wybierz wielokropek, a następnie wybierz pozycję **zasady wyszukiwania**.

   - Paleta poleceń:

     Na pasku menu Przejdź do **widoku** > **paleta poleceń**i wprowadź **zasady: zasady wyszukiwania**.

1. Jeśli wybrano więcej niż jedną subskrypcję do wyświetlania, Użyj filtru, aby wybrać subskrypcję do wyszukania.

1. Użyj filtru, aby wybrać typ zasad lub przypisanie do wyszukiwania, które jest częścią poprzednio wybranej subskrypcji.

1. Użyj filtru, aby wybrać zasady lub do wyświetlenia. Filtr działa dla parametru _DisplayName_ dla definicji zasad lub przypisania zasad.

Podczas wybierania zasad lub przypisywania, niezależnie od tego, czy za pomocą interfejsu wyszukiwania, czy przez wybranie go w widoku drzewa, rozszerzenie Azure Policy otwiera plik JSON, który reprezentuje zasady lub przypisanie oraz wszystkie jego wartości właściwości Menedżer zasobów. Rozszerzenie może zweryfikować otwarty schemat JSON Azure Policy.

## <a name="export-objects"></a>Eksportuj obiekty

Obiekty z subskrypcji można eksportować do lokalnego pliku JSON. W okienku **zasoby** lub **zasady** Umieść kursor nad lub wybierz obiekt możliwy do wyeksportowania. Na końcu wyróżnionego wiersza wybierz ikonę Zapisz i wybierz folder, w którym mają zostać zapisane dane JSON.

Następujące obiekty można eksportować lokalnie:

- Okienko zasoby
  - Grupy zasobów
  - Poszczególne zasoby (w grupie zasobów lub w ramach dostawcy zasobów)
- Okienko zasady
  - Przypisania zasad
  - Wbudowane definicje zasad
  - Niestandardowe definicje zasad
  - Inicjatyw

## <a name="on-demand-evaluation-scan"></a>Skanowanie oceny na żądanie

Skanowanie w celu oceny można rozpocząć od rozszerzenia Azure Policy Visual Studio Code. Aby rozpocząć ocenę, wybierz i Przypnij każdy z następujących obiektów: zasób, definicję zasad i przypisanie zasady.

1. Aby przypiąć każdy obiekt, Znajdź go w okienku **zasoby** lub okienku **zasady** , a następnie wybierz ikonę Przypnij do karty Edycja. Przypinanie obiektu powoduje dodanie go do okienka **oceny** rozszerzenia.
1. W okienku **oceny** wybierz jeden z tych obiektów i użyj ikony wybierz do oceny, aby oznaczyć ją jako uwzględnioną w ocenie.
1. W górnej części okienka **oceny** wybierz ikonę oceny uruchomienia. Zostanie otwarte nowe okienko w Visual Studio Code z danymi oceny w formacie JSON.

> [!NOTE]
> Jeśli wybrana definicja zasad jest [AuditIfNotExists](../concepts/effects.md#auditifnotexists) lub [DeployIfNotExists](../concepts/effects.md#deployifnotexists), w okienku **oceny** Użyj ikony znaku plus, aby wybrać _powiązany_ zasób do sprawdzenia istnienia.

Wyniki oceny zawierają informacje o definicji zasad i przypisaniu zasad wraz z właściwością **policyEvaluations. evaluationResult** . Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

## <a name="sign-out"></a>Wyloguj się

Na pasku menu Przejdź do **widoku**  >  **paleta poleceń**, a następnie wprowadź **Azure: Wyloguj się**.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).