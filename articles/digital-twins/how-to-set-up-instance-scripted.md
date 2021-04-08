---
title: Konfigurowanie wystąpienia i uwierzytelniania (z użyciem skryptów)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji, uruchamiając skrypt wdrażania automatycznego
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 13f5db52917cd9659438c551ac2f01c477cc1c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201298"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (skrypty)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

Ta wersja tego artykułu wykonuje te kroki, uruchamiając przykładowy [ **skrypt wdrażania automatycznego**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) , który usprawnia proces. 
* Aby zapoznać się z ręcznymi krokami interfejsu wiersza polecenia, które są wykonywane przez skrypt w tle, zobacz wersja interfejsu wiersza polecenia w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI)*](how-to-set-up-instance-cli.md).
* Aby zapoznać się z ręcznymi krokami zgodnie z Azure Portal, zobacz wersja portalu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Wymagania wstępne: pobieranie skryptu

Przykładowy skrypt jest zapisywana w programie PowerShell. Jest ona częścią [**kompleksowych przykładowych bliźniaczych reprezentacji na platformie Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), które można pobrać na maszynę, przechodząc do tego linku, a następnie wybierając przycisk *Przeglądaj kod* poniżej tytułu. Spowoduje to przejście do repozytorium GitHub dla przykładów, które można pobrać jako *. Plik ZIP* , wybierając przycisk *Code (kod* ) i Pobierz plik *zip*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="Widok repozytorium Digital-bliźniaczych reprezentacji-Samples w witrynie GitHub. Wybrano przycisk kod, tworząc małe okno dialogowe, w którym jest wyróżniony przycisk Pobierz plik ZIP." lightbox="media/includes/download-repo-zip.png":::

Spowoduje to pobranie *. Folder ZIP* na maszynę jako **digital-twins-samples-master.zip**. Przejdź do folderu na swoim komputerze i rozpakuj go, aby wyodrębnić pliki.

W folderze rozpakowanym skrypt wdrożenia znajduje się w _> bliźniaczych reprezentacjiych > **deploy.ps1**_.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Uruchamianie skryptu wdrażania

W tym artykule używamy przykładowego kodu usługi Azure Digital bliźniaczych reprezentacji, aby wdrożyć wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wymagane częściowo uwierzytelnianie. Może również służyć jako punkt wyjścia do pisania własnych interakcji ze skryptami.

Poniżej przedstawiono procedurę uruchamiania skryptu wdrażania w Cloud Shell.
1. Przejdź do okna [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce. Zaloguj się przy użyciu tego polecenia:
    ```azurecli-interactive
    az login
    ```
    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure. W przeciwnym razie Otwórz stronę przeglądarki pod adresem *https://aka.ms/devicelogin* i wprowadź kod autoryzacji wyświetlany w terminalu.
 
2. Na pasku ikon Cloud Shell upewnij się, że Cloud Shell jest ustawiony do uruchamiania wersji programu PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Okno Cloud Shell pokazujące wybór wersji programu PowerShell":::

1. Wybierz ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Okno Cloud Shell pokazujące wybór ikony przekazywania":::

    Przejdź do pliku _**deploy.ps1**_ na swojej maszynie (w formacie _Digital-bliźniaczych reprezentacji-Samples-Master > scripts > **deploy.ps1**_) i naciśnij pozycję "Otwórz". Spowoduje to przekazanie pliku do Cloud Shell, aby można było uruchomić go w oknie Cloud Shell.

4. Uruchom skrypt, wysyłając `./deploy.ps1` polecenie w oknie Cloud Shell. Możesz skopiować poniższe polecenie (odwołaj się do Cloud Shell, możesz użyć **klawiszy Ctrl + Shift + v** w systemach Windows i Linux lub **cmd + Shift + v** w macOS. Możesz również użyć menu po kliknięciu prawym przyciskiem myszy.

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Skrypt utworzy wystąpienie usługi Azure Digital bliźniaczych reprezentacji i przypisze rolę *właściciela danych Digital bliźniaczych reprezentacji platformy Azure* w wystąpieniu.

    Gdy skrypt jest uruchamiany w ramach kroków instalacji zautomatyzowanej, zostanie wyświetlony monit o podanie następujących wartości:
    * Dla wystąpienia: *Identyfikator subskrypcji* subskrypcji platformy Azure do użycia
    * Dla wystąpienia: *Lokalizacja* , w której chcesz wdrożyć wystąpienie. Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Dla wystąpienia: nazwa *grupy zasobów* . Możesz użyć istniejącej grupy zasobów lub wprowadzić nową nazwę, która ma zostać utworzona.
    * Dla wystąpienia: *Nazwa* wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Jeśli Twoja subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z określonej nazwy, zostanie wyświetlony monit o wybranie innej nazwy.

Poniżej znajduje się fragment dziennika danych wyjściowych ze skryptu:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Okno Cloud Shell pokazujące dziennik danych wejściowych i wyjściowych za pomocą uruchomienia skryptu wdrażania" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Jeśli skrypt zakończy się pomyślnie, zostanie wyświetlony końcowy wydruk `Deployment completed successfully` . W przeciwnym razie należy rozwiązać ten problem, a następnie uruchomić skrypt. Spowoduje to pominięcie czynności, które zostały już wykonane, i ponowne zażądanie danych wejściowych w miejscu, w którym zostało przerwane.

> [!NOTE]
> Skrypt aktualnie przypisuje wymaganą rolę zarządzania w usłudze Azure Digital bliźniaczych reprezentacji (*właściciel danych Digital bliźniaczych reprezentacji platformy Azure*) do tego samego użytkownika, który uruchamia skrypt z Cloud Shell. Jeśli musisz przypisać tę rolę osobie, która będzie zarządzać wystąpieniem, możesz to zrobić teraz za pomocą Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) lub interfejsu wiersza polecenia ([instrukcje](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>Obecnie występuje **znany problem** z konfiguracją inicjowaną przez skrypty, w której niektórzy użytkownicy (zwłaszcza użytkownicy korzystający z osobistych [kont Microsoft (kont MSA)](https://account.microsoft.com/account)) mogą znaleźć **przypisanie roli do _właściciela danych Digital bliźniaczych reprezentacji platformy Azure_**.
>
>Możesz sprawdzić przypisanie roli za pomocą sekcji [*Weryfikuj przypisanie roli użytkownika*](#verify-user-role-assignment) w dalszej części tego artykułu i — w razie konieczności — Skonfiguruj przypisanie roli ręcznie przy użyciu [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) lub [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Więcej szczegółów dotyczących tego problemu można znaleźć [*w temacie Rozwiązywanie problemów: znane problemy w usłudze Azure Digital bliźniaczych reprezentacji*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Weryfikowanie sukcesu i zbieranie ważnych wartości

Aby sprawdzić, jak utworzyć zasoby i uprawnienia skonfigurowane przez skrypt, możesz sprawdzić je w [Azure Portal](https://portal.azure.com) z poniższymi instrukcjami. Jeśli nie możesz zweryfikować sukcesu któregokolwiek z kroków, spróbuj ponownie wykonać ten krok. Kroki te można wykonać indywidualnie przy użyciu instrukcji [Azure Portal](how-to-set-up-instance-portal.md) lub [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md) .

W tej sekcji pokazano również, jak znaleźć ważne wartości z zasobów skonfigurowanych przez skrypt, który może być potrzebny podczas dalszej pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Te wartości należy zapisać w bezpiecznym miejscu lub powrócić do tej sekcji, aby znaleźć je później, gdy będą potrzebne. Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy również udostępnić te wartości.

### <a name="verify-instance"></a>Weryfikuj wystąpienie

Aby sprawdzić, czy wystąpienie zostało utworzone, przejdź do [strony Digital bliźniaczych reprezentacji na platformie Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) w Azure Portal. Możesz uzyskać dostęp do tej strony, wyszukując pozycję *Azure Digital bliźniaczych reprezentacji* na pasku wyszukiwania portalu.

Ta strona zawiera listę wszystkich wystąpień usługi Azure Digital bliźniaczych reprezentacji. Wyszukaj nazwę nowo utworzonego wystąpienia na liście.

Jeśli weryfikacja nie powiodła się, można ponowić próbę utworzenia wystąpienia przy użyciu [portalu](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) lub [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Uzyskanie wartości wystąpienia

Wybierz nazwę wystąpienia ze [strony Digital bliźniaczych reprezentacji systemu Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) , aby otworzyć stronę *omówienia* tego wystąpienia. Zanotuj jego *nazwę*, *grupę zasobów* i *nazwę hosta*. Może być potrzebne później do zidentyfikowania wystąpienia i nawiązania z nim połączenia.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Wyróżnianie ważnych wartości na stronie przeglądowej wystąpienia":::

### <a name="verify-user-role-assignment"></a>Weryfikuj przypisanie roli użytkownika

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Odwołaj, że skrypt aktualnie przypisuje tę wymaganą rolę temu samemu użytkownikowi, który uruchamia skrypt z Cloud Shell. Jeśli musisz przypisać tę rolę osobie, która będzie zarządzać wystąpieniem, możesz to zrobić teraz za pomocą Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) lub interfejsu wiersza polecenia ([instrukcje](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Jeśli weryfikacja nie powiodła się, możesz również ponownie wykonać własne przypisanie roli przy użyciu [portalu](how-to-set-up-instance-portal.md#set-up-user-access-permissions) lub [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Następne kroki

Przetestuj poszczególne wywołania interfejsu API REST w wystąpieniu przy użyciu poleceń interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji: 
* [odwołanie AZ DT](/cli/azure/ext/azure-iot/dt)
* [*Instrukcje: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-cli.md)

Lub zapoznaj się z tematem jak połączyć aplikację kliencką z wystąpieniem przy użyciu kodu uwierzytelniania:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)