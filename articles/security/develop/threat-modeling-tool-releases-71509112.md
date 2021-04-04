---
title: Microsoft Threat Modeling Tool wersja 9/12/2018
titleSuffix: Azure
description: Zapoznaj się z informacjami o wersji Microsoft Threat Modeling Tool wydanych w dniu 9/12/2018. Uwagi obejmują zmiany funkcji i poprawki błędów.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 7b0afdd90f2a1413c7f8364fc4518c4d1116454a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913589"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA Release 7.1.50911.2-9/12/2018

Przyjemnością ogłaszamy, że Microsoft Threat Modeling Tool jest teraz dostępna do pobrania jako obsługiwana ogólnie wersja. Ta wersja zawiera ważne aktualizacje prywatności i zabezpieczeń, a także poprawki błędów, aktualizacje funkcji i ulepszenia stabilności. Istniejący użytkownicy wersji zapoznawczej 2017 będą monitowani o aktualizację do najnowszej wersji za pomocą technologii ClickOnce podczas otwierania klienta. W przypadku nowych użytkowników narzędzia można [pobrać klienta programu](https://aka.ms/threatmodelingtool).

W tej wersji zakończymy obsługę wersji zapoznawczej 2017 i zaleca się, aby wszyscy użytkownicy aktualizacji w wersji zapoznawczej mieli dostęp do wersji GA. W dniu lub po 15 2018 października ustawimy minimalną wymaganą wersję ClickOnce dla Threat Modeling Tool, a wszyscy klienci wersji zapoznawczej będą musieli przeprowadzić uaktualnienie.

Microsoft Threat Modeling Tool 2016, który jest dostępny w [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), jest nadal obsługiwany do 1 2019 października w przypadku krytycznych poprawek zabezpieczeń.

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="azure-stencil-updates"></a>Aktualizacje wzornika platformy Azure

Dodatkowe wzorniki platformy Azure i związane z nimi zagrożenia i środki zaradcze zostały dodane do wzornika z tą wersją. Wprowadzono znaczące zmiany w obszarach koncentracji "Azure App Services", "oferty usługi Azure Database" i "Azure Storage".

![Aktualizacje wzornika platformy Azure](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Usunięto funkcję integracji z usługą OneDrive

Funkcje "Zapisz w usłudze OneDrive", "Otwórz z usługi OneDrive" i "Udostępnij link" w wersji zapoznawczej zostały usunięte. Użytkownicy usługi OneDrive są zachęcani do używania klienta [usługi OneDrive dla systemu Windows](https://onedrive.live.com/about/en-us/download/) firmy Microsoft w celu uzyskania dostępu do plików przechowywanych w usłudze OneDrive za pomocą standardowych okien dialogowych zapisywania i otwierania plików.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Znaczące usterki zgłoszone przez klientów

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>W wersji zapoznawczej usługi TMT narzędzie ulega awarii podczas korzystania z standardowego szablonu

- Gdy wzornik generyczny (na przykład "ogólny przepływ danych") zostanie dodany do powierzchni rysowania i wygeneruje zagrożenia, narzędzie może ulec awarii. Ten problem został rozwiązany.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>W wersji zapoznawczej TMT, gdy zapisuję raport lub kopiujesz zagrożenia, poziomy ryzyka są niepoprawne

- Jeśli użytkownik zmodyfikuje poziom ryzyka dla określonych zagrożeń, a następnie zapisze raport lub skopiuje ryzyko, poziom ryzyka może przywrócić wartość "wysoki". Ten problem został rozwiązany.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Użytkownicy ekranów o wysokiej rozdzielczości mogą mieć niewielki tekst we właściwościach zagrożeń

#### <a name="issue"></a>Problem

W widoku analizy narzędzia, jeśli użytkownik ma ekran o wysokiej rozdzielczości, który jest ustawiany domyślnie przez powiększanie w celu zapewnienia czytelności w systemie Windows, w sekcji "ewentualne ograniczenia" zagrożenia mogą pojawić się małe teksty.

![Znany problem z ekranami o wysokiej rozdzielczości](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Obejście

Użytkownik może kliknąć tekst łagodzenia i użyć standardowej kontrolki powiększenia systemu Windows (Crtl — kółko myszy), aby zwiększyć powiększenie tej sekcji.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Otwieranie plików w sekcji "ostatnio otwierane modele" w oknie głównym może się nie powieść

#### <a name="issue"></a>Problem

Funkcja "Open from OneDrive" w wersji zapoznawczej została usunięta. Użytkownicy z "ostatnio otwartymi modelami", które zostały zapisane w usłudze OneDrive, otrzymają następujący błąd.

![Zrzuty ekranu pokazują błąd: Wystąpił błąd podczas otwierania odwołania do obiektu pliku, który nie jest ustawiony na wystąpienie obiektu.](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Obejście

Użytkownicy usługi OneDrive są zachęcani do używania klienta [usługi OneDrive dla systemu Windows](https://onedrive.live.com/about/en-us/download/) firmy Microsoft w celu uzyskania dostępu do plików przechowywanych w usłudze OneDrive za pomocą standardu i okna dialogowego "Otwieranie modelu".

![Zrzut ekranu pokazuje, że usługa OneDrive została wybrana w oknie dialogowym Otwórz model.](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Czy Moja organizacja korzysta z wersji 2016 narzędzia, czy mogę użyć zestawu wzorników platformy Azure?

Tak, możesz! [Zestaw wzorników platformy Azure jest dostępny w witrynie GitHub](https://github.com/Microsoft/threat-modeling-templates/)i można go załadować w wersji 2016 narzędzia. Aby utworzyć nowy model przy użyciu zestawu wzorników platformy Azure, użyj okna dialogowego "szablon dla nowych modeli" na ekranie głównym menu. TMT 2016 nie może renderować linków znalezionych w polach "możliwe środki zaradcze" zestawu wzorników platformy Azure, dlatego można zobaczyć linki wyświetlane jako Tagi HTML.

![Aktualizacje wzornika platformy Azure w kliencie 2016](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - Microsoft Windows 10
- Wymagana wersja platformy .NET
  - .NET 3.5.2
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](threat-modeling-tool.md)i zawiera informacje na [temat korzystania z narzędzia](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
