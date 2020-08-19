---
title: Konfigurowanie wystąpienia i uwierzytelniania (z użyciem skryptów)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji, uruchamiając skrypt wdrażania automatycznego
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 86597521f762237b5c4bc9a7a5268d7dae1303b4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587997"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (skrypty)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

Ta wersja tego artykułu wykonuje te kroki, uruchamiając przykładowy [ **skrypt wdrażania automatycznego** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) , który usprawnia proces. 
* Aby zapoznać się z ręcznymi krokami interfejsu wiersza polecenia, które są wykonywane przez skrypt w tle, zobacz wersja interfejsu wiersza polecenia w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI)*](how-to-set-up-instance-cli.md).
* Aby zapoznać się z ręcznymi krokami zgodnie z Azure Portal, zobacz wersja portalu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (Portal)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Uruchamianie skryptu wdrażania

W tym artykule używamy przykładowego kodu usługi Azure Digital bliźniaczych reprezentacji, aby wdrożyć wystąpienie usługi Azure Digital bliźniaczych reprezentacji i wymagane częściowo uwierzytelnianie. Może również służyć jako punkt wyjścia do pisania własnych interakcji ze skryptami.

Przykładowy skrypt jest zapisywana w programie PowerShell. Jest częścią [bliźniaczych reprezentacji cyfrowych platformy Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), którą można pobrać na maszynę, przechodząc do tego linku, a następnie wybierając przycisk *Pobierz zip* poniżej tytułu.

W folderze pobrane przykładowe skrypt wdrożenia znajduje się w _Azure_Digital_Twins_samples.zip > skrypty > **deploy.ps1** _.

Poniżej przedstawiono procedurę uruchamiania skryptu wdrażania w Cloud Shell.
1. Przejdź do okna [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce. Zaloguj się przy użyciu tego polecenia:
    ```azurecli-interactive
    az login
    ```
    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure. W przeciwnym razie Otwórz stronę przeglądarki pod adresem *https://aka.ms/devicelogin* i wprowadź kod autoryzacji wyświetlany w terminalu.
 
2. Po zalogowaniu się przejdź do paska ikon okna Cloud Shell. Wybierz ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Okno Cloud Shell pokazujące wybór opcji przekazywania":::

    Przejdź do pliku _**deploy.ps1**_ na swojej maszynie i naciśnij pozycję "Otwórz". Spowoduje to przekazanie pliku do Cloud Shell, aby można było uruchomić go w oknie Cloud Shell.

3. Uruchom skrypt, wysyłając `./deploy.ps1` polecenie w oknie Cloud Shell. Gdy skrypt jest uruchamiany w ramach kroków instalacji zautomatyzowanej, zostanie wyświetlony monit o podanie następujących wartości:
    * Dla wystąpienia: *Identyfikator subskrypcji* subskrypcji platformy Azure do użycia
    * Dla wystąpienia: *Lokalizacja* , w której chcesz wdrożyć wystąpienie. Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Dla wystąpienia: nazwa *grupy zasobów* . Możesz użyć istniejącej grupy zasobów lub wprowadzić nową nazwę, która ma zostać utworzona.
    * Dla wystąpienia: *Nazwa* wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Nazwa nowego wystąpienia musi być unikatowa w obrębie regionu subskrypcji (co oznacza, że jeśli subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z wybranej nazwy, zostanie wyświetlony monit o wybranie innej nazwy).
    * W przypadku rejestracji aplikacji: *Nazwa wyświetlana aplikacji usługi Azure AD* , która ma zostać skojarzona z rejestracją. Ta rejestracja aplikacji to miejsce, w którym można skonfigurować uprawnienia dostępu do [interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-use-apis-sdks.md). Później aplikacja kliencka będzie uwierzytelniana względem rejestracji aplikacji i w wyniku przyznania skonfigurowanych uprawnień dostępu do interfejsów API.
    * Rejestracja aplikacji: *adres URL odpowiedzi aplikacji usługi Azure AD* dla aplikacji usługi Azure AD. Możesz użyć `http://localhost` .

Skrypt utworzy wystąpienie usługi Azure Digital bliźniaczych reprezentacji, przypisze rolę użytkownika platformy Azure *(wersja zapoznawcza)* na tym wystąpieniu i skonfiguruje rejestrację aplikacji usługi Azure AD do użycia.

Poniżej znajduje się fragment dziennika danych wyjściowych ze skryptu:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Okno Cloud Shell pokazujące dziennik danych wejściowych i wyjściowych za pomocą uruchomienia skryptu wdrażania" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Jeśli skrypt zakończy się pomyślnie, zostanie wyświetlony końcowy wydruk `Deployment completed successfully` . W przeciwnym razie należy rozwiązać ten problem, a następnie uruchomić skrypt. Spowoduje to pominięcie czynności, które zostały już wykonane, i ponowne zażądanie danych wejściowych w miejscu, w którym zostało przerwane.

Po zakończeniu działania skryptu masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji z uprawnieniami do zarządzania nim oraz że skonfigurowano uprawnienia aplikacji klienckiej do uzyskiwania do niej dostępu.

> [!NOTE]
> Skrypt aktualnie przypisuje wymaganą rolę zarządzania w ramach usługi Azure Digital bliźniaczych reprezentacji (*wersja zapoznawcza usługi Azure Digital bliźniaczych reprezentacji*) do tego samego użytkownika, który uruchamia skrypt z Cloud Shell. Jeśli musisz przypisać tę rolę osobie, która będzie zarządzać wystąpieniem, możesz to zrobić teraz za pomocą Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) lub interfejsu wiersza polecenia ([instrukcje](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Zbierz ważne wartości

Istnieje kilka ważnych wartości zasobów skonfigurowanych przez skrypt, które mogą być potrzebne w przypadku kontynuowania pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. W tej sekcji użyjesz [Azure Portal](https://portal.azure.com) , aby zebrać te wartości. Należy je zapisać w bezpiecznym miejscu lub wrócić do tej sekcji, aby znaleźć je później, gdy będą potrzebne.

Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy również udostępnić te wartości.

### <a name="collect-instance-values"></a>Zbierz wartości wystąpień

W [Azure Portal](https://portal.azure.com)Znajdź wystąpienie usługi Azure Digital bliźniaczych reprezentacji, wyszukując nazwę wystąpienia na pasku wyszukiwania portalu.

Wybranie tej opcji spowoduje otwarcie strony *przeglądowej* wystąpienia. Zanotuj jego *nazwę*, *grupę zasobów*i *nazwę hosta*. Może być potrzebne później do zidentyfikowania wystąpienia i nawiązania z nim połączenia.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Wyróżnianie ważnych wartości na stronie przeglądowej wystąpienia":::

### <a name="collect-app-registration-values"></a>Zbieranie wartości rejestracji aplikacji 

Istnieją dwie ważne wartości rejestracji aplikacji, które będą potrzebne później do [zapisania kodu uwierzytelniania aplikacji klienta dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-authenticate-client.md). 

Aby je znaleźć, Skorzystaj z [tego linku](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , aby przejść do strony Przegląd rejestracji aplikacji usługi Azure AD w Azure Portal. Ta strona zawiera wszystkie rejestracje aplikacji, które zostały utworzone w ramach subskrypcji.

Powinna zostać wyświetlona właśnie utworzona Rejestracja aplikacji na tej liście. Wybierz go, aby otworzyć jego szczegóły:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Widok portalu ważnych wartości rejestracji aplikacji":::

Zanotuj *Identyfikator* *aplikacji (klienta)* na stronie **użytkownika** . Jeśli nie jesteś osobą, która będzie pisać kod dla aplikacji klienckich, musisz udostępnić te wartości osobie, która będzie.

## <a name="verify-success"></a>Weryfikowanie sukcesu

Jeśli chcesz sprawdzić tworzenie zasobów i uprawnień skonfigurowanych przez skrypt, możesz przyjrzeć się im w [Azure Portal](https://portal.azure.com).

### <a name="verify-instance"></a>Weryfikuj wystąpienie

Aby sprawdzić, czy wystąpienie zostało utworzone, przejdź do [strony Digital bliźniaczych reprezentacji na platformie Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) w Azure Portal. Ta strona zawiera listę wszystkich wystąpień usługi Azure Digital bliźniaczych reprezentacji. Wyszukaj nazwę nowo utworzonego wystąpienia na liście.

### <a name="verify-user-role-assignment"></a>Weryfikuj przypisanie roli użytkownika

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Odwołaj, że skrypt aktualnie przypisuje tę wymaganą rolę temu samemu użytkownikowi, który uruchamia skrypt z Cloud Shell. Jeśli musisz przypisać tę rolę osobie, która będzie zarządzać wystąpieniem, możesz to zrobić teraz za pomocą Azure Portal ([instrukcje](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) lub interfejsu wiersza polecenia ([instrukcje](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).
>
> Możesz również użyć portalu lub interfejsu wiersza polecenia, aby ponownie wykonać własne przypisanie roli w przypadku wystąpienia problemów z konfiguracją skryptu.

### <a name="verify-app-registration"></a>Weryfikowanie rejestracji aplikacji

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Najpierw sprawdź, czy ustawienia uprawnień Digital bliźniaczych reprezentacji systemu Azure zostały prawidłowo ustawione podczas rejestracji. W tym celu wybierz pozycję *manifest* z paska menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj tych pól w obszarze `requiredResourceAccess` . Wartości powinny być zgodne z poniższymi zrzutu ekranu:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Inne możliwe kroki dla organizacji

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak połączyć aplikację kliencką z wystąpieniem, pisząc kod uwierzytelniania aplikacji klienta:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)
