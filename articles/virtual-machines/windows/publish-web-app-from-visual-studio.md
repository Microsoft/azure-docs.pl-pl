---
title: Publikowanie aplikacji sieci Web na maszynie wirtualnej platformy Azure z poziomu programu Visual Studio
description: Publikowanie aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure z poziomu programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: bdc03e8c136606ab7768705b0c8dbcc97782966f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "87088397"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikowanie aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure z poziomu programu Visual Studio

W tym dokumencie opisano sposób publikowania aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure przy użyciu funkcji publikowania **Microsoft Azure Virtual Machines** w programie Visual Studio 2019.  

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było opublikować projekt ASP.NET na maszynie wirtualnej platformy Azure przy użyciu programu Visual Studio, należy prawidłowo skonfigurować maszynę wirtualną.

- Komputer musi być skonfigurowany do uruchamiania aplikacji sieci Web ASP.NET i zainstalowania narzędzia webdeploy. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej ASP.NET za pomocą narzędzia webdeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- Maszyna wirtualna musi mieć skonfigurowaną nazwę DNS. Aby uzyskać więcej informacji, zobacz [Tworzenie w pełni kwalifikowanej nazwy domeny w Azure Portal dla maszyny wirtualnej z systemem Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikowanie aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure przy użyciu programu Visual Studio
W poniższej sekcji opisano sposób publikowania istniejącej aplikacji sieci Web ASP.NET na maszynie wirtualnej platformy Azure.

1. Otwórz rozwiązanie aplikacji sieci Web w programie Visual Studio 2019.
2. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Publikuj...**
3. Za pomocą strzałki znajdującej się po prawej stronie, przewiń opcje publikowania do momentu znalezienia **Microsoft Azure Virtual Machines**.  

   ![Strona publikacji — Strzałka w prawo]

4. Wybierz ikonę **Microsoft Azure Virtual Machines** i wybierz pozycję **Publikuj**.

   ![Strona publikowania — ikona Microsoft Azure maszyny wirtualnej]

5. Wybierz odpowiednie konto (z subskrypcją platformy Azure połączoną z maszyną wirtualną).  
   - Jeśli użytkownik jest zalogowany do programu Visual Studio, lista kont zostanie wypełniona wszystkimi kontami uwierzytelnionymi.  
   - Jeśli użytkownik nie jest zalogowany lub konto, którego potrzebujesz nie ma na liście, wybierz pozycję "Dodaj konto..." i postępuj zgodnie z monitami, aby się zalogować.  
   ![Selektor konta platformy Azure]  

6. Wybierz odpowiednią maszynę wirtualną z listy istniejących Virtual Machines.

   > [!Note]
   > Wypełnianie tej listy może zająć trochę czasu.

   ![Wybór maszyny wirtualnej platformy Azure]

7. Kliknij przycisk OK, aby rozpocząć publikowanie.

8. Po wyświetleniu monitu o podanie poświadczeń Podaj nazwę użytkownika i hasło konta użytkownika na docelowej maszynie wirtualnej skonfigurowanej z prawami do publikowania. Te poświadczenia są zwykle nazwą użytkownika administratora i hasłem używanym podczas tworzenia maszyny wirtualnej.  

   ![Logowanie do narzędzia webdeploy]

9. Zaakceptuj certyfikat zabezpieczeń.

   ![Błąd certyfikatu]

10. Obejrzyj okno dane wyjściowe, aby sprawdzić postęp operacji publikowania.

    ![Okno wyniku]

11. Jeśli publikowanie powiedzie się, zostanie uruchomiona przeglądarka otwierająca adres URL nowo opublikowanej witryny.

**To wszystko!**

Aplikacja sieci Web została pomyślnie opublikowana na maszynie wirtualnej platformy Azure.

## <a name="publish-page-options"></a>Opcje publikowania strony

Po zakończeniu działania Kreatora publikacji strona publikowanie zostanie otwarta w dokumencie z wybranym nowym profilem publikacji.

### <a name="re-publish"></a>Opublikuj ponownie

Aby opublikować aktualizacje aplikacji sieci Web, wybierz przycisk **Publikuj** na stronie Publikuj.  
- Jeśli zostanie wyświetlony monit, wprowadź nazwę użytkownika i hasło.  
- Publikowanie rozpocznie się natychmiast.

![Strona publikowania — przycisk Publikuj]

### <a name="modify-publish-profile-settings"></a>Modyfikowanie ustawień profilu publikowania

Aby wyświetlić i zmodyfikować ustawienia publikowania profilu, wybierz pozycję **Ustawienia..**..  

![Strona publikowanie — przycisk Ustawienia]

Twoje ustawienia powinny wyglądać następująco:  

![Ustawienia publikowania — strona połączenia]

#### <a name="save-user-name-and-password"></a>Zapisz nazwę użytkownika i hasło
- Należy unikać przekazywania informacji uwierzytelniania przy każdej publikacji. W tym celu Wypełnij pola **Nazwa użytkownika** i **hasło** , a następnie wybierz pole **Zapisz hasło** .
- Użyj przycisku **Weryfikuj połączenie** , aby potwierdzić, że wprowadzono odpowiednie informacje.

#### <a name="deploy-to-clean-web-server"></a>Wdróż na czystym serwerze sieci Web

- Jeśli chcesz mieć pewność, że serwer sieci Web ma czystą kopię aplikacji sieci Web po każdym przekazaniu i że żadne inne pliki nie pochodzą z poprzedniego wdrożenia, możesz zaznaczyć pole wyboru **Usuń dodatkowe pliki w miejscu docelowym** na karcie **Ustawienia** .

- Ostrzeżenie: Publikowanie przy użyciu tego ustawienia powoduje usunięcie wszystkich plików istniejących na serwerze sieci Web (katalogu wwwroot). Upewnij się, że znasz stan maszyny przed opublikowaniem jej przy włączonej tej opcji. 

![Ustawienia publikowania — strona ustawień]

## <a name="next-steps"></a>Następne kroki

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynie wirtualnej platformy Azure

Aby skonfigurować potok ciągłego dostarczania przy użyciu Azure Pipelines, zobacz [wdrażanie na maszynie wirtualnej z systemem Windows](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Strona publikacji — Strzałka w prawo]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Strona publikowania — ikona Microsoft Azure maszyny wirtualnej]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor konta platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Wybór maszyny wirtualnej platformy Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Logowanie do narzędzia webdeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Błąd certyfikatu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Okno Dane wyjściowe]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Strona publikowania — przycisk Publikuj]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Strona publikowanie — przycisk Ustawienia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Ustawienia publikowania — strona połączenia]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Ustawienia publikowania — strona ustawień]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
