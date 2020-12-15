---
title: Usuwanie metody płatności dla rozliczeń na platformie Azure
description: Przedstawia sposób usuwania formy płatności używanej przez subskrypcję platformy Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827548"
---
# <a name="delete-an-azure-billing-payment-method"></a>Usuwanie metody płatności dla rozliczeń na platformie Azure

Ten dokument zawiera instrukcje ułatwiające usunięcie formy płatności, takiej jak karta kredytowa, z różnych typów subskrypcji platformy Azure. Możesz usunąć metodę płatności dla:

- Umowa klienta firmy Microsoft (MCA)
- Program subskrypcji usług online firmy Microsoft (MOSP) nazywany również płatnością zgodnie z rzeczywistym użyciem

Niezależnie od typu subskrypcji platformy Azure należy ją anulować, aby można było usunąć skojarzoną z nią metodę płatności.

Usunięcie formy płatności dla innych typów subskrypcji platformy Azure, takich jak Microsoft Partner Agreement i umowa Enterprise Agreement, nie jest obsługiwane.

## <a name="delete-an-mca-payment-method"></a>Usuwanie formy płatności dla umowy MCA

Tylko użytkownik, który utworzył konto Umowy z Klientem Microsoft, może usunąć formę płatności.

Aby usunąć formę płatności dla Umowy z Klientem Microsoft, wykonaj następujące czynności.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com/.
1. Przejdź na stronę **Zarządzanie kosztami i rozliczenia**.
1. W razie potrzeby wybierz zakres rozliczeniowy.
1. Z listy menu po lewej stronie w obszarze **Rozliczenia** wybierz pozycję **Profile rozliczeniowe**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Przykładowy zrzut ekranu przedstawiający profile rozliczeniowe w witrynie Azure Portal" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Z listy profilów rozliczeniowych wybierz ten, który używa formy płatności.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Przykładowy obraz przedstawiający listę profilów rozliczeniowych" :::
1. Z listy menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **Formy płatności**.
1. Na stronie form płatności dla profilu rozliczeniowego w sekcji **Twoje karty kredytowe** zostanie wyświetlona tabela form płatności. Znajdź kartę kredytową, którą chcesz usunąć, wybierz wielokropek ( **…** ), a następnie wybierz pozycję **Usuń**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Przykład przedstawiający miejsce usunięcia karty kredytowej" :::
1. Zostanie wyświetlona strona Usuwanie formy płatności. Platforma Azure sprawdza, czy forma płatności jest używana.
    - Gdy forma płatności nie jest używana, opcja **Usuń** jest włączona. Wybierz ją, aby usunąć dane karty kredytowej.
    - Jeśli metoda płatności jest używana, należy ją zamienić lub odłączyć. Kontynuuj odczytywanie następujących sekcji. Wyjaśniono w nich, jak **odłączyć** formy płatności używane przez subskrypcję.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Odłączanie formy płatności używanej przez profil rozliczeniowy dla umowy MCA

Jeśli Twoja forma płatności jest używana przez profil rozliczeniowy dla umowy MCA, zobaczysz komunikat podobny do poniższego przykładu.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Przykładowy obraz pokazujący, że forma płatności jest używana w ramach Umowy z Klientem Microsoft" :::

Aby odłączyć formę płatności, należy spełnić warunki podane na liście. Jeśli jakiekolwiek warunki nie zostaną spełnione, pojawią się instrukcje objaśniające, jak spełnić warunek. Zostanie również wyświetlony link do lokalizacji, w której można rozwiązać problem z warunkiem.

Gdy wszystkie warunki zostaną spełnione, można odłączyć formę płatności od profilu rozliczeniowego.

> [!NOTE]
> Po odłączeniu domyślnej formy płatności profil rozliczeniowy przejdzie w stan _nieaktywny_. Elementy usunięte w tym procesie nie będą mogły zostać odzyskane. Po ustawieniu profilu rozliczeniowego jako nieaktywnego należy utworzyć nową subskrypcję platformy Azure w celu utworzenia nowych zasobów.

#### <a name="to-detach-a-payment-method"></a>Aby odłączyć formę płatności

1. W obszarze Usuwanie formy płatności wybierz link **Odłącz bieżącą formę płatności**.
1. Jeśli spełnione są wszystkie warunki, wybierz pozycję **Odłącz**. W przeciwnym razie przejdź do następnego kroku.
1. Jeśli przycisk Odłącz jest niedostępny, zostanie wyświetlona lista warunków. Wykonaj działania wymienione na liście. Wybierz link widoczny w obszarze Odłączanie domyślnej formy płatności. Oto przykład akcji naprawczej objaśniający działania, które należy wykonać.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Przykład pokazujący akcję naprawczą, która jest wymagana do odłączenia formy płatności dla umowy MCA" :::
1. Po wybraniu linku do działania naprawczego nastąpi przekierowanie do strony platformy Azure, na której zostanie podjęta akcja. Wykonaj odpowiednią akcję naprawczą.
1. W razie potrzeby wykonaj wszystkie pozostałe akcje naprawcze.
1. Przejdź z powrotem do strony **Zarządzanie kosztami i rozliczenia**  >  **Profile rozliczeniowe**  >  **Formy płatności**. Wybierz pozycję **Odłącz**. W dolnej części strony Odłączanie domyślnej formy płatności wybierz pozycję **Odłącz**.

> [!NOTE]
> - Po anulowaniu subskrypcji może upłynąć do 90 dni, zanim będzie można usunąć subskrypcję. Jeśli chcesz, aby czas oczekiwania został skrócony, otwórz wniosek o pomoc techniczną platformy Azure i poproś o natychmiastowe usunięcie subskrypcji.
> - Formę płatności można usunąć tylko po uiszczeniu wszystkich poprzednich opłat w ramach profilu rozliczeniowego. Jeśli jesteś w aktywnym okresie rozliczeniowym, musisz poczekać na zakończenie okresu rozliczeniowego, aby móc usunąć formę płatności. **Upewnij się, że wszystkie inne warunki odłączenia są spełnione podczas oczekiwania na zakończenie okresu rozliczeniowego**.

## <a name="delete-a-mosp-payment-method"></a>Usuwanie formy płatności dla programu subskrypcji usług online firmy Microsoft

Musisz być administratorem konta, aby móc usunąć formę płatności.

Jeśli Twoja forma płatności jest używana przez subskrypcję programu subskrypcji usług online firmy Microsoft, wykonaj następujące czynności.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com/.
1. Przejdź na stronę **Zarządzanie kosztami i rozliczenia**.
1. W razie potrzeby wybierz zakres rozliczeniowy.
1. Z listy menu po lewej stronie w obszarze **Rozliczenia** wybierz pozycję **Formy płatności**.
1. W obszarze Formy płatności wybierz _wiersz_ Twojej formy płatności. Nie wybieraj linku formy płatności. Może nie być wizualnego potwierdzenia, że wybrano formę płatności.
1. Wybierz pozycję **Usuń**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Przykład pokazujący akcję naprawczą, która jest wymagana do odłączenia formy płatności dla programu subskrypcji usług online firmy Microsoft" :::
1. W obszarze Usuwanie formy płatności wybierz pozycję **Usuń**, jeśli zostaną spełnione wszystkie warunki. Jeśli polecenie Usuń jest niedostępne, przejdź do następnego kroku.
1. Zostanie wyświetlona lista warunków. Wykonaj działania wymienione na liście. Wybierz link widoczny w obszarze Usuwanie formy płatności.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Przykładowy obraz pokazujący, że forma płatności jest używana w ramach subskrypcji programu subskrypcji usług online firmy Microsoft" :::
1. Po wybraniu linku do działania naprawczego nastąpi przekierowanie do strony platformy Azure, na której zostanie podjęta akcja. Wykonaj odpowiednią akcję naprawczą.
1. W razie potrzeby wykonaj wszystkie pozostałe akcje naprawcze.
1. Przejdź z powrotem do strony **Zarządzanie kosztami i rozliczenia**  >  **Profile rozliczeniowe**  >  **Formy płatności** i usuń formę płatności.

> [!NOTE]
> Po anulowaniu subskrypcji może upłynąć do 90 dni, zanim będzie można usunąć subskrypcję. Jeśli chcesz, aby czas oczekiwania został skrócony, otwórz wniosek o pomoc techniczną platformy Azure i poproś o natychmiastowe usunięcie subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat anulowania subskrypcji platformy Azure, zobacz [Anulowanie subskrypcji platformy Azure](cancel-azure-subscription.md).
- Aby uzyskać więcej informacji na temat dodawania lub aktualizowania karty kredytowej, zobacz [Dodawanie lub aktualizowanie karty kredytowej dla platformy Azure](change-credit-card.md).