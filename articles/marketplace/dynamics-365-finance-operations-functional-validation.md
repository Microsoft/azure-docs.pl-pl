---
title: Weryfikacja funkcjonalna oferty AppSource Dynamics 365 Finanse i operacje w portalu Azure Marketplace.
description: Jak funkcjonalnie zweryfikować ofertę Dynamics 365 Finanse i operacje w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613633"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 — Walidacja funkcjonalna i działania operacji

Aby ukończyć pierwsze publikowanie w [centrum partnerskim](https://partner.microsoft.com/dashboard/home), oferty dla systemu Dynamics 365 Finanse i operacje wymagają dwóch walidacji funkcjonalnej:

- Przekaż wideo demonstracyjne środowiska Dynamics 365, które zawiera podstawowe funkcje.
- Prezentuj zrzuty ekranu przedstawiające środowisko [usług cyklu życia](https://lcs.dynamics.com/) rozwiązania (LCS).

> [!NOTE]
> Kolejne publikowanie ponownych certyfikacji nie wymaga demonstracji. Aby dowiedzieć się więcej, zobacz [dokument zasad AppSource](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Sprawdzanie poprawności

Dostępne są dwie opcje sprawdzania poprawności funkcjonalnej:

- Poznasz 30-minutowy konferencję z nami w godzinach pracy w czasie standardowym (PST), aby przedstawić i zarejestrować środowisko i rozwiązanie [LCS](https://lcs.dynamics.com/) .
- W centrum partnerskim przejdź do [omówienia komercyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >   i przekaż pokazowy adres URL wideo i zrzuty ekranu LCS na karcie zawartość uzupełniająca oferty.

Zespół certyfikacji firmy Microsoft przegląda wideo i pliki, a następnie zatwierdza rozwiązanie lub wysyła wiadomość e-mail z informacjami o następnych krokach.

### <a name="option-1-30-minute-conference-call"></a>Opcja 1:30-minutowe wywołanie konferencji

Aby zaplanować ostateczne wywołanie przeglądu, należy skontaktować się [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) z nazwą oferty oraz z niektórymi potencjalnymi momentami z zakresu od 8 do 5 godzin pacyficznego.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Opcja 2: przekazywanie demonstracyjnego wideo i LCSych zrzutów ekranu

1. Zapisz wideo i przekaż go do wybranej witryny hostingowej. Postępuj zgodnie z następującymi wskazówkami:

    - Widoczne dla zespołu certyfikacji firmy Microsoft.
    - Krótsze niż 20 minut.
    - Obejmuje maksymalnie trzy podstawowe funkcje rozwiązania w środowisku Dynamics 365.

    > [!NOTE]
    > Można użyć istniejącego wideo marketingowego, jeśli spełnia on wytyczne.

2. Wykonaj następujące zrzuty ekranu środowiska [LCS](https://lcs.dynamics.com/) , które pasują do oferty lub rozwiązania, które chcesz opublikować. Muszą być wystarczająco jasne, aby zespół certyfikacji mógł odczytać tekst. Zapisz zrzuty ekranu jako pliki JPG. Możesz podać [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) uprawnienia do środowiska LCS, aby umożliwić nam zweryfikowanie Instalatora zamiast zapewniania zrzutów ekranu.

    1. Przejdź do   >  biblioteki projektów LCS **Business Process Modelarz**  >  . Wykonaj zrzuty ekranu wszystkich kroków procesu. Uwzględnij **diagramy** i **zrecenzowane** kolumny, jak pokazano poniżej:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Pokazuje okno biblioteki projektu.":::

      2. Przejdź do   >    >  **pakietu rozwiązania testowego** zarządzania rozwiązaniami LCS. Wykonaj zrzuty ekranu zawierające Omówienie pakietu i treść pokazaną w poniższych przykładach:

    | Pole | Obraz |
    | --- | --- |
    | Przegląd pakietu | [![Zrzut ekranu przedstawiający okno "przegląd pakietu".](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Osoby zatwierdzające rozwiązanie</li></ul> | [![Ekran przegląd pakietu](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Zawartość pakietu<ul><li>Model</li><li>Pakiet do wdrożenia oprogramowania</li></ul> | [![Ekran zawartości pakietu](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Konfiguracja GER</li><li>Kopia zapasowa bazy danych</li></ul><br>Artefakty nie są wymagane w sekcji **konfiguracji GER** . | [![Ekran zawartości pakietu — dwa](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI model raportu</li><li>Artefakt BPM</li></ul><br>Artefakty nie są wymagane w sekcji **Power BI** . | [![Ekran zawartości pakietu — trzy](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Pakiet danych procesu</li><li>Umowa licencyjna rozwiązania i zasady ochrony prywatności</li></ul><br>Sekcje **Konfiguracja GER** i **model raportu Power BI** są opcjonalne do uwzględnienia w ofertach finansów i operacji. | [![Ekran zawartości pakietu cztery](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Więcej informacji na temat każdej sekcji portalu LCS można znaleźć w [podręczniku użytkownika LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Przekaż do Centrum partnerskiego.

    1. Utwórz dokument tekstowy zawierający pokazowy adres wideo i zrzuty ekranu lub Zapisz zrzuty ekranu jako oddzielne pliki JPG.
    2. Dodaj tekst i wszystkie pliki JPG do pliku. zip w [portalu komercyjnym](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) w centrum partnerskim na karcie **zawartości uzupełniającej** oferty finansów i operacji.

    [![Pokazuje okno biblioteki projektu](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu oferty, zobacz: [Tworzenie oferty usługi Dynamics 365 dla operacji](./partner-center-portal/create-new-operations-offer.md).