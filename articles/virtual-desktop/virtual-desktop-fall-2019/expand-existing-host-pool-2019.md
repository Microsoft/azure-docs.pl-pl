---
title: Rozwiń istniejącą pulę hostów systemu Windows Virtual Desktop (klasyczną) przy użyciu nowych hostów sesji — Azure
description: Jak rozszerzyć istniejącą pulę hostów na nowe hosty sesji na pulpicie wirtualnym systemu Windows (klasyczny).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009175"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Rozszerzanie istniejącej puli hostów na nowe hosty sesji na pulpicie wirtualnym systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../expand-existing-host-pool.md).

W miarę zwiększania użycia w puli hostów może być konieczne rozszerzenie istniejącej puli hostów na nowe hosty sesji w celu obsługi nowego obciążenia.

W tym artykule przedstawiono, jak można rozszerzyć istniejącą pulę hostów na nowe hosty sesji.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co jest potrzebne do rozwinięcia puli hostów

Przed rozpoczęciem upewnij się, że utworzono pulę hostów i maszyny wirtualne hosta sesji (VM), korzystając z jednej z następujących metod:

- [Oferta portalu Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Szablon Azure Resource Manager GitHub](create-host-pools-arm-template.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell-2019.md)

Podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji wymagane są również następujące informacje:

- Prefiks rozmiaru, obrazu i nazwy maszyny wirtualnej
- Przyłączanie do domeny i poświadczenia administratora dzierżawy pulpitu wirtualnego systemu Windows
- Nazwa sieci wirtualnej i nazwa podsieci

Kolejne trzy sekcje to trzy metody, których można użyć do rozszerzenia puli hostów. Możesz to zrobić za pomocą dowolnego narzędzia do wdrażania, z którym masz doświadczenie.

>[!NOTE]
>W fazie wdrażania zostaną wyświetlone komunikaty o błędach dotyczące zasobów maszyn wirtualnych z poprzednimi hostami sesji, jeśli są one obecnie wyłączone. Przyczyną tego błędu jest to, że platforma Azure nie może uruchomić rozszerzenia DSC programu PowerShell w celu sprawdzenia, czy maszyny wirtualne hosta sesji są poprawnie zarejestrowane w istniejącej puli hostów. Można bezpiecznie zignorować te błędy lub uniknąć błędów, uruchamiając wszystkie maszyny wirtualne hosta sesji w istniejącej puli hostów przed rozpoczęciem procesu wdrażania.

## <a name="redeploy-from-azure"></a>Ponowne wdrażanie z platformy Azure

Jeśli masz już utworzoną pulę hostów i maszyny wirtualne hosta sesji przy użyciu szablonu [oferty portalu Azure Marketplace](create-host-pools-azure-marketplace-2019.md) lub [Azure Resource Manager GitHub](create-host-pools-arm-template.md), możesz ponownie wdrożyć ten sam szablon z Azure Portal. Ponowne wdrożenie szablonu automatycznie ponownie wprowadza wszystkie wprowadzone informacje do oryginalnego szablonu z wyjątkiem haseł.

Oto jak ponownie wdrożyć szablon Azure Resource Manager, aby rozszerzyć pulę hostów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Na pasku wyszukiwania w górnej części Azure Portal Wyszukaj pozycję **grupy zasobów** i wybierz element w obszarze **usługi**.
3. Znajdź i wybierz grupę zasobów utworzoną podczas tworzenia puli hostów.
4. W panelu po lewej stronie przeglądarki wybierz pozycję **wdrożenia**.
5. Wybierz odpowiednie wdrożenie dla procesu tworzenia puli hostów:
     - Jeśli utworzono początkową pulę hostów za pomocą oferty portalu Azure Marketplace, wybierz wdrożenie zaczynające się od **usług RDS. WVD-Host-Pool**.
     - Jeśli została utworzona pierwotna Pula hostów z szablonem Azure Resource Manager GitHub, wybierz wdrożenie o nazwie **Microsoft. Template**.
6. Wybierz pozycję **Wdróż** ponownie.

     >[!NOTE]
     >Jeśli szablon nie zostanie automatycznie wdrożony po wybraniu opcji **ponowne wdrażanie**, wybierz pozycję **szablon** w panelu po lewej stronie przeglądarki, a następnie wybierz pozycję **Wdróż**.

7. Wybierz grupę zasobów zawierającą bieżące maszyny wirtualne hosta sesji w istniejącej puli hostów.

     >[!NOTE]
     >Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że należy wybrać inną grupę zasobów, mimo że wprowadzona nazwa jest poprawna, wybierz inną grupę zasobów, a następnie wybierz oryginalną grupę zasobów.

8. Wprowadź następujący adres URL dla *_artifactsLocation*: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Wprowadź nową łączną liczbę wystąpień hosta sesji na hoście *usług pulpitu zdalnego*. Na przykład, jeśli rozszerzasz pulę hostów z pięciu sesji na osiem, wprowadź **8**.
10. Wprowadź to samo hasło domeny, które zostało użyte dla istniejącej nazwy UPN domeny. Nie zmieniaj nazwy użytkownika, ponieważ spowoduje to wystąpienie błędu podczas uruchamiania szablonu.
11. Wprowadź to samo hasło administratora dzierżawy, które zostało użyte dla identyfikatora użytkownika lub aplikacji, który został wprowadzony dla *nazwy UPN lub identyfikatora aplikacji administratora dzierżawy*. Ponownie nie zmieniaj nazwy użytkownika.
12. Ukończ przesyłanie, aby rozszerzyć pulę hostów.

## <a name="run-the-azure-marketplace-offering"></a>Uruchamianie oferty portalu Azure Marketplace

Postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace-2019.md) do momentu osiągnięcia [oferty usługi Azure Marketplace w celu aprowizacji nowej puli hostów](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Po uzyskaniu tego punktu należy wprowadzić następujące informacje dla każdej karty:

### <a name="basics"></a>Podstawy

Wszystkie wartości w tej sekcji powinny być zgodne z informacjami podanymi podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji, z wyjątkiem *domyślnego użytkownika pulpitu*:

1.    W obszarze *subskrypcja* wybierz subskrypcję, w której najpierw utworzono pulę hostów.
2.    W obszarze *Grupa zasobów* wybierz tę samą grupę zasobów, w której znajdują się maszyny wirtualne hosta sesji istniejącej puli hostów.
3.    W polu *region* wybierz ten sam region, w którym znajdują się maszyny wirtualne hosta sesji istniejącej puli hostów.
4.    W polu *Nazwa Hostpool* wprowadź nazwę istniejącej puli hostów.
5.    W *polu Typ pulpitu* wybierz typ pulpitu pasujący do istniejącej puli hostów.
6.    W przypadku *domyślnych użytkowników pulpitu* wprowadź rozdzieloną przecinkami listę wszelkich dodatkowych użytkowników, którzy chcą zalogować się do klientów pulpitu wirtualnego systemu Windows i uzyskać dostęp do pulpitu po zakończeniu oferty portalu Azure Marketplace. Na przykład jeśli chcesz przypisać user3@contoso.com i user4@contoso.com uzyskać dostęp, wprowadź user3@contoso.com , user4@contoso.com .
7.    Wybierz pozycję **Dalej: Skonfiguruj maszynę wirtualną**.

>[!NOTE]
>Z wyjątkiem *domyślnych użytkowników pulpitu* wszystkie pola muszą być zgodne z tym, co zostało skonfigurowane w istniejącej puli hostów. W przypadku niezgodności, która spowoduje powstanie nowej puli hostów.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z informacjami podanymi podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji, z wyjątkiem łącznej liczby maszyn wirtualnych. Liczba wprowadzonych maszyn wirtualnych to liczba maszyn wirtualnych w rozwiniętej puli hostów:

1. Wybierz rozmiar maszyny wirtualnej odpowiadający istniejącym maszynom wirtualnym hosta sesji.

    >[!NOTE]
    >Jeśli określony rozmiar maszyny wirtualnej nie jest wyświetlany w selektorze rozmiaru maszyny wirtualnej, jest to spowodowane tym, że nie zostało to jeszcze zrobione w narzędziu Azure Marketplace. Aby zażądać rozmiaru maszyny wirtualnej, Utwórz żądanie lub zagłosuj na istniejące żądanie na [forum Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Dostosuj *profil użycia*, *łącznego użytkownika* i *liczbę parametrów maszyn wirtualnych* , aby wybrać łączną liczbę hostów sesji, które mają być używane w puli hostów. Na przykład, jeśli rozszerzasz pulę hostów z pięciu sesji na osiem, skonfiguruj te opcje, aby uzyskać dostęp do 8 maszyn wirtualnych.
3. Wprowadź prefiks nazw maszyn wirtualnych. Na przykład w przypadku wprowadzenia nazwy "prefiks" maszyny wirtualne będą nazywane "prefiksem-0", "prefiksem-1" i tak dalej.
4. Wybierz pozycję **Dalej: ustawienia maszyny wirtualnej**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z informacjami podanymi podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji:

1. W polu *Źródło obrazu* i *wersja systemu operacyjnego obrazu* wprowadź te same informacje, które zostały podane podczas pierwszego utworzenia puli hostów.
2. W przypadku *przyłączania do domeny usługi Active Directory UPN* i skojarzonych haseł wprowadź te same informacje, które zostały podane podczas pierwszego utworzenia puli hostów w celu dołączenia do maszyn wirtualnych do domeny Active Directory. Te poświadczenia będą używane do tworzenia konta lokalnego na maszynach wirtualnych. Możesz zresetować te konta lokalne, aby zmienić ich poświadczenia później.
3. W polu Informacje o sieci wirtualnej wybierz tę samą sieć wirtualną i podsieć, w której znajdują się maszyny wirtualne hosta sesji puli hostów.
4. Wybierz pozycję **Dalej: Skonfiguruj informacje o pulpicie wirtualnym systemu Windows**.

### <a name="windows-virtual-desktop-information"></a>Informacje o pulpicie wirtualnym systemu Windows

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z informacjami podanymi podczas pierwszego tworzenia puli hostów i maszyn wirtualnych hosta sesji:

1. W polu *Nazwa grupy dzierżawy pulpitu wirtualnego systemu Windows* wprowadź nazwę grupy dzierżawców zawierającej dzierżawcę. Pozostaw to ustawienie domyślne, chyba że podano konkretną nazwę grupy dzierżawców.
2. W polu *Nazwa dzierżawy pulpitu wirtualnego systemu Windows* wprowadź nazwę dzierżawy, w której chcesz utworzyć pulę hostów.
3. Określ te same poświadczenia, które zostały użyte podczas pierwszego utworzenia puli hostów i maszyn wirtualnych hosta sesji. Jeśli używasz nazwy głównej usługi, wprowadź identyfikator wystąpienia Azure Active Directory, w którym znajduje się nazwa główna usługi.
4. Wybierz pozycję **Dalej: przegląd + Utwórz**.

## <a name="run-the-github-azure-resource-manager-template"></a>Uruchamianie szablonu Azure Resource Manager GitHub

Postępuj zgodnie z instrukcjami w temacie [Uruchamianie szablonu Azure Resource Manager, aby zainicjować obsługę nowej puli hostów](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) i podać wszystkie te same wartości parametrów, z wyjątkiem *liczby wystąpień hosta sesji usług pulpitu zdalnego*. Wprowadź liczbę maszyn wirtualnych hosta sesji, które mają być używane w puli hostów po uruchomieniu szablonu. Na przykład, jeśli rozszerzasz pulę hostów z pięciu sesji na osiem, wprowadź **8**.

## <a name="next-steps"></a>Następne kroki

Po rozwinięciu istniejącej puli hostów możesz zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować je w ramach sesji użytkownika. Możesz połączyć się z sesją przy użyciu dowolnego z następujących klientów:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-10-2019.md)
- [Łączenie się z klientem internetowym](connect-web-2019.md)
- [Łączenie się z klientem systemu Android](connect-android-2019.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos-2019.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios-2019.md)
