---
title: Fortanix poufnego Menedżera przetwarzania w aplikacji zarządzanej platformy Azure
description: Dowiedz się, jak wdrożyć Fortanix poufnego Menedżera przetwarzania danych (CCM) w aplikacji zarządzanej w Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563425"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix poufnego Menedżera przetwarzania w aplikacji zarządzanej platformy Azure

W tym artykule opisano sposób wdrażania aplikacji, która jest zarządzana przez Fortanixe poufnego zarządzania systemem w Azure Portal.

Fortanix to dostawca oprogramowania innych firm z produktami i usługami opartymi na infrastrukturze platformy Azure. Istnieją inni dostawcy innych firm oferujących podobne poufne usługi obliczeniowe na platformie Azure.

> [!NOTE]
>Produkty, do których odwołuje się ten dokument, nie są pod kontrolą firmy Microsoft. Firma Microsoft udostępnia te informacje tylko jako wygoda, a odwołanie do tych produktów innych firm nie oznacza potwierdzenia przez firmę Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

- Prywatny rejestr platformy Docker służący do wypychania przekonwertowanych obrazów aplikacji.
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Wdrażanie poufnego Menedżera przetwarzania za pomocą aplikacji zarządzanej platformy Azure

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. Na pasku wyszukiwania Wyszukaj ciąg "Fortanix poufnego zarządzania przetwarzaniem" i znajdziesz listę Marketplace dla Fortanix CCM. Wybierz pozycję **Fortanix poufne Computing Manager na platformie Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Lista Marketplace.":::

3. Zostanie otwarta strona, na której zostanie utworzona aplikacja zarządzana przez Menedżera CCM. Wybierz pozycję **Utwórz**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Utwórz aplikację.":::

4. Wypełnij wszystkie wymagane pola.
   1. W sekcji Szczegóły zarządzanej aplikacji pole **zarządzane grupy zasobów** będzie miało wartość domyślną, którą użytkownik może zmodyfikować, jeśli są one potrzebne.
   2. W polu **region** wybierz pozycję **Australia Wschodnia**, **Australia Południowo**- **Wschodnia**, **zachodnie stany USA 2**, **Europa Zachodnia**, **Europa Północna**, **Kanada środkowa**, **Kanada Wschodnia**, **Wschodnie stany USA 2 — euap**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Pola wymagane":::

   Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć aplikację zarządzaną przez CCM Fortanix.

5. Przejrzyj szczegóły i po zakończeniu walidacji zaznacz pole wyboru **Akceptuję warunki i postanowienia powyżej** , a następnie wybierz pozycję **Utwórz** , aby utworzyć aplikację zarządzaną.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Przejrzyj szczegóły.":::

6. Zostanie uruchomione wdrożenie Fortanix CCM i zostanie wyświetlone powiadomienie, że wdrożenie jest w toku.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Postęp wdrażania.":::

7. Po zakończeniu wdrażania wybierz przycisk **Przejdź do zasobu** , aby przejść do strony "Omówienie wdrożonej aplikacji zarządzanej przez Menedżera CCM" w celu zarejestrowania węzła obliczeniowego.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Zrzut ekranu pokazujący pomyślne wdrożenie w Azure Portal.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Zrzut ekranu przedstawiający przegląd poufnego zasobu obliczeniowego w Azure Portal.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Rejestrowanie węzła obliczeniowego w Fortanix CCM

1. Wybierz pozycję **poufny Menedżer przetwarzania** w menu nawigacji po lewej stronie. Zaloguj się do Fortanix CCM i Utwórz konto, jak widać na **rysunku 9**.

    Aby uzyskać więcej informacji na temat rejestrowania, logowania i tworzenia konta w programie CCM, zobacz [wprowadzenie CCM](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Zrzut ekranu przedstawiający logowanie do Fortanix poufnego Menedżera przetwarzania danych.":::
    
2. Aby uzyskać token sprzężenia z konsoli zarządzania CCM, najpierw wybierz przycisk **zarejestruj węzeł** . Następnie w oknie rejestrowanie węzła wybierz przycisk **Kopiuj** , aby skopiować token sprzężenia.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Zrzut ekranu pokazujący pobieranie tokenu sprzężenia.":::

3. Teraz, aby zarejestrować agenta węzła, wybierz kartę **komputer z agentem węzła poufnego** , a następnie wybierz pozycję **Dodaj** , aby dodać agenta węzła CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Zrzut ekranu pokazujący Dodawanie agenta węzła.":::

4.  W formularzu Agent węzła CCM Wypełnij wszystkie wymagane pola. Wklej token sprzężenia skopiowany w kroku 2 w **tokenie sprzężenia**. Wybierz pozycję **Recenzja + Prześlij** , aby potwierdzić.

    Aby uzyskać więcej informacji na temat rejestrowania węzła obliczeniowego CCM, zobacz [Rejestrowanie obliczeniowe węzła](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Zrzut ekranu pokazujący rejestrowanie węzła obliczeniowego.":::
    
5. Po zakończeniu walidacji wybierz pozycję **Prześlij** , aby ukończyć tworzenie agenta węzła.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Zrzut ekranu pokazujący, że jest tworzony Agent węzła.":::

6. Aby sprawdzić stan wdrożenia, przejdź do karty **Przegląd** , a następnie wybierz łącze **zarządzane grupy zasobów** .

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Zrzut ekranu pokazujący, że węzeł jest zarejestrowany.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Zrzut ekranu pokazujący sprawdzanie stanu wdrożenia.":::

7. Teraz można zauważyć, że stan wdrożenia nadal trwa i potrwa kilka minut, zanim Agent węzła zostanie pomyślnie zarejestrowany.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Zrzut ekranu pokazujący, że wdrożenie jest w toku.":::

8. Po pomyślnym zarejestrowaniu agenta węzła stan zmieni się na "powodzenie".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Zrzut ekranu pokazujący, że wdrożenie zakończyło się pomyślnie.":::

9. Teraz w aplikacji zarządzanej przez Menedżera CCM przejdź do stron węzłów obliczeniowych i zobaczysz, że węzeł jest w stanie **aktywnym** i zarejestrowano pomyślnie.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Zrzut ekranu pokazujący, że węzeł został pomyślnie zarejestrowany.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użytkownik może również usunąć agenta węzła CCM z poziomu strony agenta węzła komputera poufnego. Aby usunąć agenta węzła, wybierz pozycję Agent węzła, a następnie wybierz przycisk **Usuń** na górnym pasku.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Zrzut ekranu pokazujący usuwanie agenta węzła.":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zarejestrowano węzeł przy użyciu aplikacji zarządzanej przez platformę Azure w celu Fortanixego Menedżera przetwarzania poufnego. Rejestracja węzła umożliwia przekonwertowanie obrazu aplikacji tak, aby był uruchamiany w oparciu o poufną maszynę wirtualną. Aby uzyskać więcej informacji na temat poufnego przetwarzania maszyn wirtualnych na platformie Azure, zobacz [rozwiązania na Virtual Machines](virtual-machine-solutions.md).

Aby dowiedzieć się więcej na temat ofert związanych z poufnym przetwarzaniem na platformie Azure, zobacz informacje [poufne dotyczące platformy Azure](overview.md).

Dowiedz się, jak wykonywać podobne zadania przy użyciu innych ofert innych firm na platformie Azure, takich jak [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) i [Scone](https://sconedocs.github.io).

