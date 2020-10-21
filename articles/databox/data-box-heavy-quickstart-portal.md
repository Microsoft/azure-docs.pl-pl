---
title: 'Szybki start: urządzenie Microsoft Azure Data Box Heavy | Microsoft Docs'
description: W tym przewodniku Szybki start dowiesz się, jak wdrożyć urządzenie Azure Data Box Heavy przy użyciu witryny Azure Portal. Opisano w nim m.in. sposób podłączania kabli, konfigurowanie urządzenia i kopiowanie danych w celu przekazania ich na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9eda54ad23e06149910fe69ec16588f49829a5a5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122827"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Szybki start: Wdrażanie urządzenia Azure Data Box Heavy za pomocą witryny Azure Portal

W tym przewodniku Szybki start opisano sposób wdrażania urządzenia Azure Data Box Heavy przy użyciu witryny Azure Portal. Przedstawiono między innymi czynności podłączania i konfigurowania urządzenia Data Box Heavy oraz kopiowania na nie danych do przekazania na platformę Azure. Czynności opisane w przewodniku Szybki start należy wykonać w witrynie Azure Portal oraz w lokalnym internetowym interfejsie użytkownika na urządzeniu.

Aby zyskać szczegółowe instrukcje dotyczące wdrażania i śledzenia krok po kroku, zobacz [Samouczek: Zamawianie urządzenia Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem urządzenia należy spełnić następujące wymagania wstępne dotyczące konfiguracji związane z miejscem instalacji, usługą i urządzeniem Data Box.

### <a name="for-installation-site"></a>Miejsce instalacji

Przed rozpoczęciem upewnij się, że:

- Urządzenie mieści się we wszystkich otworach drzwiowych. Wymiary urządzenia — szerokość: 66 cm (26 cali) długość: 122 cm (48 cali) wysokość: 71 cm (28 cali).
- W budynku można skorzystać z windy lub rampy, jeśli urządzenie ma zostać zainstalowane na innym piętrze niż parter.
- Do obsługi urządzenia przeznaczono dwie osoby. Urządzenie ma masę około 227 kg (500 funtów). i ma kółka.
- Masz dostęp do płaskiej powierzchni w centrum danych znajdującej się blisko dostępnego połączenia sieciowego, gdzie umieścisz urządzenie.

### <a name="for-service"></a>Na potrzeby usługi

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Na potrzeby urządzenia

Przed rozpoczęciem upewnij się, że:

- Zapoznano się z [wytycznymi bezpieczeństwa dotyczącymi urządzenia Data Box Heavy](data-box-safety.md).
- Masz komputer hosta podłączony do sieci centrum danych. Za pomocą urządzenia Data Box Heavy zostaną skopiowane dane z tego komputera. Na komputerze hoście musi działać [obsługiwany system operacyjny](data-box-heavy-system-requirements.md).
- Masz komputer przenośny i kabel RJ-45 umożliwiające nawiązanie połączenia z lokalnym interfejsem użytkownika i skonfigurowanie urządzenia. Za pomocą komputera przenośnego skonfigurujesz jednorazowo poszczególne węzły na urządzeniu.
- Twoje centrum danych ma sieć o dużej szybkości i masz co najmniej jedno połączenie 10 GbE.
- Potrzebny jest jeden kabel o przepustowości 40 Gb/s lub po jednym kablu o przepustowości 10 Gb/s dla każdego węzła urządzenia. Wybierz kable, które są zgodne z interfejsem sieciowym Mellanox MCX314A-BCCT:
    - W przypadku kabla o przepustowości 40 Gb/s po stronie urządzenia musi on być zgodny ze standardem QSFP+.
    - W przypadku kabla o przepustowości 10 Gb/s potrzebny jest kabel SFP+ zakończony z jednej strony wtyczką umożliwiającą podłączenie do przełącznika 10-G, a ze strony podłączanej do urządzenia zakończony adapterem QSFP+ do SFP+ (lub adapterem QSA).
- Kable zasilające znajdują się w zasobniku z tyłu urządzenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Zamówienie

Ten krok zajmuje około 5 minut.

1. Utwórz nowy zasób usługi Azure Data Box w witrynie Azure Portal.
2. Wybierz istniejącą subskrypcję, w której włączono tę usługę, a następnie wybierz typ transferu **Import**. Podaj **Kraj źródłowy**, w którym są przechowywane dane, oraz **Docelowy region platformy Azure**, do którego mają zostać przekazane dane.
3. Wybierz pozycję **Data Box Heavy**. Maksymalna dostępna do użycia pojemność tego rozwiązania to 770 TB. W przypadku większych ilości danych możesz utworzyć większą liczbę zamówień.
4. Wprowadź szczegóły zamówienia i informacje dotyczące wysyłki. Jeśli ta usługa jest dostępna w Twoim regionie, podaj adresy e-mail na potrzeby powiadomień, zapoznaj się z podsumowaniem i utwórz zamówienie.

Po utworzeniu zamówienia urządzenie zostanie przygotowane do wysłania.

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Podłączanie kabli do urządzenia i nawiązywanie z nim połączenia

Po przejrzeniu wymagań wstępnych możesz podłączyć kable do urządzenia i nawiązać z nim połączenie.

::: zone-end

## <a name="cable-for-power"></a>Podłączanie kabli zasilających

Ten krok zajmuje około 5 minut.

Po otrzymaniu urządzenia Data Box Heavy wykonaj następujące czynności w celu podłączenia kabli zasilających i włączenia urządzenia.

1. Jeśli urządzenie nosi jakiekolwiek oznaki uszkodzenia lub naruszenia, nie wykonuj dalszych czynności. Skontaktuj się z działem pomocy technicznej firmy Microsoft, aby otrzymać urządzenie zamienne.
2. Przetransportuj urządzenie do miejsca instalacji i zablokuj tylne koła.
3. Podłącz wszystkie cztery kable zasilające do jednostek zasilających z tyłu urządzenia.
4. Włącz węzły urządzenia za pomocą przycisków zasilania na panelu przednim.

## <a name="cable-first-node-for-network"></a>Podłączanie kabli sieciowych do pierwszego węzła

Ten krok zajmuje około 10–15 minut.

1. Użyj kabla sieciowego RJ-45 CAT 6, aby podłączyć komputer hosta do portu zarządzania (MGMT) na urządzeniu.
2. Za pomocą kabla miedzianego Twinax QSFP+ podłącz co najmniej jeden interfejs sieciowy (DATA 1 lub DATA 2) o przepustowości 40 Gb/s (preferowany względem interfejsu 1 Gb/s) do przesyłania danych. W przypadku korzystania z przełącznika 10 Gb/s użyj kabla miedzianego Twinax SFP+ z adapterem QSFP+ do SFP+ (adapter QSA) w celu podłączenia interfejsu sieciowego 40 Gb/s do przesyłania danych.
3. Podłącz przewody do urządzenia tak, jak pokazano poniżej.  

    ![Urządzenie Data Box Heavy z podłączonymi kablami](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Konfigurowanie pierwszego węzła

Ten krok zajmuje około 5–7 minut.

1. Aby uzyskać hasło urządzenia, przejdź do pozycji **Ogólne > Szczegóły urządzenia** w witrynie [Azure Portal](https://portal.azure.com). To samo hasło jest używane w przypadku obu węzłów urządzenia.
2. Przypisz statyczny adres IP 192.168.100.5 i podsieć 255.255.255.0 do karty Ethernet na komputerze używanym do nawiązywania połączenia z urządzeniem Data Box Heavy. Uzyskaj dostęp do lokalnego internetowego interfejsu użytkownika urządzenia pod adresem `https://192.168.100.10`. Nawiązywanie połączenia może potrwać do 5 minut po włączeniu urządzenia.
3. Zaloguj się przy użyciu hasła uzyskanego z witryny Azure Portal. Zostanie wyświetlony komunikat o błędzie, informujący o problemie z certyfikatem zabezpieczeń witryny internetowej. Postępuj zgodnie z instrukcjami dotyczącymi używanej przeglądarki, aby przejść do odpowiedniej strony internetowej.
4. Domyślnie ustawienia sieci dla interfejsów (z wyjątkiem interfejsu MGMT) to DHCP. Jeśli jest taka potrzeba, można skonfigurować te interfejsy jako statyczne i podać adresy IP.

## <a name="cable-and-configure-the-second-node"></a>Podłączanie kabli do drugiego węzła i jego konfigurowanie

Ten krok zajmuje około 15–20 minut.

Postępuj zgodnie z instrukcjami dotyczącymi pierwszego węzła, aby podłączyć kable do drugiego węzła urządzenia i go skonfigurować.  


::: zone target = "docs"

## <a name="copy-data"></a>Kopiowanie danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych i szybkości połączenia sieciowego używanego do kopiowania danych.
 
1. Skopiuj dane do obu węzłów urządzenia, korzystając jednocześnie z obu interfejsów danych 40 GB/s.

    - Jeśli używasz hosta z systemem Windows, skorzystaj z narzędzia do kopiowania plików zgodnego z protokołem SMB, na przykład [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
    - W przypadku hosta z systemem plików NFS skopiuj dane za pomocą polecenia `cp` lub `rsync`.
2. Nawiąż połączenie z udziałami na urządzeniu przy użyciu ścieżki `\\<IP address of your device>\ShareName`. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box Heavy.
3. Upewnij się, że nazwy udziałów, folderów i danych są zgodne z instrukcjami przedstawionymi w temacie [Azure Storage and Data Box service limits (Ograniczenia usług Azure Storage i Data Box Heavy)](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Po bezbłędnym zakończeniu kopiowania przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania.
2. Po pomyślnym zakończeniu procedury **przygotowania do wysłania** na obu węzłach wyłącz urządzenie z poziomu lokalnego internetowego interfejsu użytkownika.

## <a name="ship-to-azure"></a>Wysyłka do platformy Azure

Ta operacja trwa około 15–20 minut.

1. Odłącz kable i umieść je z powrotem w zasobniku z tyłu urządzenia.
2. Uzgodnij odbiór urządzenia z regionalną firmą kurierską.
3. Skontaktuj się z [działem operacji Data Box](mailto:DataBoxOps@microsoft.com), aby poinformować o odbiorze i uzyskać etykietę wysyłki zwrotnej.
4. Zwrotna etykieta wysyłkowa powinna być widoczna na przednim panelu urządzenia.

## <a name="verify-data"></a>Weryfikacja danych

Czas wymagany do ukończenia tej operacji zależy od rozmiaru danych.

1. Gdy urządzenie Data Box Heavy zostanie podłączone do sieci w centrum danych platformy Azure, dane zostaną automatycznie przekazane na platformę Azure.
2. Usługa Data Box powiadomi Cię o zakończeniu kopiowania danych w witrynie Azure Portal.

    1. Sprawdź dzienniki błędów pod kątem ewentualnych niepowodzeń i podejmij odpowiednie działania.
    2. Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Ten krok zajmuje około 2–3 minut.

- Możesz anulować zamówienie urządzenia Data Box Heavy w witrynie Azure Portal przed jego przetworzeniem. Po przetworzeniu zamówienia nie można go anulować. Realizacja zamówienia będzie kontynuowana aż do etapu ukończenia. Aby anulować zamówienie, w sekcji **Przegląd** kliknij pozycję **Anuluj** na pasku poleceń.

- Możesz usunąć zamówienie, gdy jego stan w witrynie Azure Portal to **Ukończono** lub **Anulowano**. Aby usunąć zamówienie, w sekcji **Przegląd** kliknij pozycję **Usuń** na pasku poleceń.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym przewodnikiem Szybki start wdrożono urządzenie Data Box Heavy w celu zaimportowania danych na platformę Azure. Aby dowiedzieć się więcej na temat zarządzania urządzeniem Azure Data Box Heavy, przejdź do kolejnego samouczka:

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box Heavy (Administrowanie urządzeniem Data Box Heavy w witrynie Azure Portal)](data-box-portal-admin.md)

::: zone-end