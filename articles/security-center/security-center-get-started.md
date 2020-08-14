---
title: Uaktualnij do warstwy Standardowa — Azure Security Center
description: W tym przewodniku szybki start przedstawiono, jak przeprowadzić uaktualnienie do warstwy cenowej standardowa Security Center, aby zwiększyć bezpieczeństwo.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226020"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Szybki start: dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Chociaż warstwa Bezpłatna oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure, warstwa standardowa rozszerza te możliwości do lokalnych i innych chmur. Usługa Security Center w warstwie Standardowa pomaga w wyszukiwaniu i naprawianiu luk w zabezpieczeniach, stosowaniu kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywaniu zagrożeń przy użyciu analizy oraz szybkim reagowaniu podczas ataku. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

W tym artykule opisano uaktualnienie do warstwy Standardowa w celu zwiększenia bezpieczeństwa i zainstalowania agenta Log Analytics na maszynach wirtualnych w celu monitorowania luk w zabezpieczeniach i zagrożeń.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Aby uaktualnić subskrypcję do warstwy Standardowa, musisz mieć przypisaną rolę właściciela subskrypcji, współautora subskrypcji lub administratora zabezpieczeń.

## <a name="enable-your-azure-subscription"></a>Włączanie subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. W menu **Microsoft Azure** wybierz pozycję **Security Center**. **Security Center — przegląd** zostanie otwarty.

   ![Security Center — Przegląd][2]

Okno **Security Center — Przegląd** zapewnia ujednolicony wgląd w stan zabezpieczeń obciążeń chmury hybrydowej, dzięki czemu możesz odnajdywać i oceniać zabezpieczenia obciążeń oraz identyfikować i ograniczać ryzyko. Usługa Security Center automatycznie włącza warstwę Bezpłatna dla wszystkich subskrypcji platformy Azure, które nie zostały wcześniej dołączone przez Ciebie lub innego użytkownika subskrypcji.

Klikając element menu **Subskrypcje**, możesz wyświetlić i filtrować listę subskrypcji. Usługa Security Center rozpocznie teraz ocenę zabezpieczeń tych subskrypcji, aby zidentyfikować luki w zabezpieczeniach. Aby dostosować typy ocen, możesz zmodyfikować zasady zabezpieczeń. Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy.

W ciągu kilku minut od pierwszego uruchomienia usługi Security Center mogą zostać wyświetlone następujące informacje:

- **Zalecenia** dotyczące sposobów poprawy zabezpieczeń subskrypcji platformy Azure. Kliknięcie kafelka **Zalecenia** spowoduje uruchomienie priorytetyzowanej listy.
- Spis zasobów **Obliczenia i aplikacje**, **Sieć**, **Bezpieczeństwo danych** oraz **Tożsamość i dostęp**, które są obecnie oceniane przez usługę Security Center wraz ze stanem zabezpieczeń każdego z nich.

Aby w pełni wykorzystać Security Center, należy wykonać poniższe kroki, aby przeprowadzić uaktualnienie do warstwy Standardowa i zainstalować agenta Log Analytics.


## <a name="upgrade-to-the-standard-tier"></a>Zmiana warstwy na wyższą warstwę Standardowa

Na potrzeby przewodników Szybki start i samouczków usługi Security Center musisz zmienić warstwę na wyższą warstwę Standardowa. Usługa Security Center w warstwie Standardowa jest dostępna w bezpłatnej wersji próbnej. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). 

1. Na pasku bocznym Security Center wybierz opcję **wprowadzenie**.
 
   ![Wprowadzenie](./media/security-center-get-started/get-started-upgrade-tab.png)

    Karta **Uaktualnij** zawiera listę subskrypcji i obszarów roboczych kwalifikujących się do dołączenia.

1. Z listy **Wybierz obszary robocze do włączenia warstwy Standardowa** wybierz obszary robocze do uaktualnienia.


    > [!TIP]
    > W przypadku wybrania obszaru roboczego, który kwalifikuje się do korzystania z bezpłatnej wersji próbnej, następnym krokiem będzie rozpoczęcie korzystania z wersji próbnej. Jeśli obszary robocze nie kwalifikują się do korzystania z wersji próbnej, zostaną uaktualnione i rozpocznie się naliczanie opłat.

1. Wybierz pozycję **Uaktualnij** , aby uaktualnić wybrane obszary robocze do warstwy Standardowa.


## <a name="automate-data-collection"></a>Automatyzacja zbierania danych
Usługa Security Center zbiera dane z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure w celu monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Domyślnie usługa Security Center utworzy nowy obszar roboczy.

Po włączeniu automatycznej aprowizacji program Security Center instaluje agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Używanie automatycznej aprowizacji jest zdecydowanie zalecane.

Aby włączyć automatyczną obsługę administracyjną agenta Log Analytics:

1. W menu głównym Security Center wybierz pozycję **cennik & ustawienia**.
1. W wierszu subskrypcji kliknij subskrypcję, dla której chcesz zmienić ustawienia.
1. Na karcie **Zbieranie danych** ustaw opcję **Automatyczna aprowizacja** na wartość **Wł**.
1. Wybierz pozycję **Zapisz**.
---
  ![Włączanie automatycznej aprowizacji][6]

Dzięki temu nowemu wglądowi w maszyny wirtualne platformy Azure usługa Security Center może udostępniać dodatkowe zalecenia dotyczące stanu aktualizacji systemu, konfiguracji zabezpieczeń systemu operacyjnego i ochrony punktów końcowych, a także generować dodatkowe alerty zabezpieczeń.

  ![Zalecenia][8]

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Kontynuuj uruchamianie warstwy Standardowa i Włącz automatyczną obsługę administracyjną. Jeśli nie zamierzasz kontynuować lub chcesz wrócić do warstwy bezpłatnej:

1. Wróć do menu głównego Security Center i wybierz pozycję **cennik & ustawienia**.
2. Kliknij subskrypcję, którą chcesz zmienić w ramach warstwy Bezpłatna.
3. Wybierz pozycję **warstwa cenowa** i wybierz opcję **bezpłatna** , aby zmienić subskrypcję z warstwy Standardowa na warstwa Bezpłatna.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego Security Center i wybierz pozycję **cennik & ustawienia**.
2. Wyczyść subskrypcję, dla której chcesz wyłączyć automatyczne Inicjowanie obsługi administracyjnej.
3. Na karcie **Zbieranie danych** ustaw opcję **Automatyczna aprowizacja** na wartość **Wył**.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start uaktualniono do warstwy Standardowa i zainicjowano agenta Log Analytics w celu zapewnienia ujednoliconego zarządzania zabezpieczeniami i ochrony przed zagrożeniami w ramach obciążeń chmury hybrydowej. Aby dowiedzieć się więcej na temat korzystania z usługi Security Center, przejdź do przewodnika Szybki start dotyczącego dołączania komputerów z systemem Windows znajdujących się w środowisku lokalnym i w innych chmurach.

> [!div class="nextstepaction"]
> [Szybki start: dołączanie komputerów z systemem Windows do usługi Azure Security Center](quick-onboard-windows-computer.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
