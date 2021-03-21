---
title: Rozwiązywanie problemów z rolami, których uruchomienie nie powiodło się | Microsoft Docs
description: Poniżej przedstawiono niektóre typowe przyczyny niepowodzenia uruchomienia roli usługi w chmurze. Dostępne są również rozwiązania tych problemów.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743223"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>Rozwiązywanie problemów z rolami usługi w chmurze platformy Azure (klasycznymi), których nie można uruchomić

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Poniżej przedstawiono niektóre typowe problemy i rozwiązania dotyczące ról Cloud Services platformy Azure, których nie można uruchomić.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Brak bibliotek DLL lub zależności
Nieodpowiadające role i role, które są cykliczne między **inicjalizacją**, **zajętą** i **zatrzymywaniem** , mogą być spowodowane brakującymi bibliotekami DLL lub zestawami.

Objawy brakujących bibliotek DLL lub zestawów mogą być następujące:

* Wystąpienie roli jest cykliczne przez **Inicjowanie**, **zajęte** i **Zatrzymywanie** Stanów.
* Wystąpienie roli zostało przeniesione do **gotowe** , ale jeśli przejdziesz do aplikacji sieci Web, Strona nie zostanie wyświetlona.

Istnieje kilka zalecanych metod badania tych problemów.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnozuj brakujące problemy z biblioteką DLL w roli sieci Web
Po przejściu do witryny internetowej wdrożonej w roli sieci Web, a w przeglądarce zostanie wyświetlony komunikat o błędzie serwera podobny do poniższego, może to oznaczać, że brakuje biblioteki DLL.

![Błąd serwera w aplikacji "/".](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnozuj problemy, wyłączając błędy niestandardowe
Więcej informacji o błędach można wyświetlić przez skonfigurowanie web.config dla roli sieci Web w celu ustawienia niestandardowego trybu błędu na off i ponowne wdrożenie usługi.

Aby wyświetlić bardziej szczegółowe błędy bez używania Pulpit zdalny:

1. Otwórz rozwiązanie w Microsoft Visual Studio.
2. W **Eksplorator rozwiązań** znajdź plik web.config i otwórz go.
3. W pliku web.config zlokalizuj sekcję system. Web i Dodaj następujący wiersz:

    ```xml
    <customErrors mode="Off" />
    ```
4. Zapisz plik.
5. Ponownie spakować i ponownie Wdróż usługę.

Po ponownym wdrożeniu usługi zostanie wyświetlony komunikat o błędzie z nazwą brakującego zestawu lub biblioteki DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnozuj problemy, wyświetlając błąd zdalnie
Możesz użyć Pulpit zdalny, aby uzyskać dostęp do roli i wyświetlić więcej pełnych informacji o błędzie. Aby wyświetlić błędy przy użyciu Pulpit zdalny, wykonaj następujące kroki:

1. Upewnij się, że zainstalowano zestaw Azure SDK 1,3 lub nowszy.
2. Podczas wdrażania rozwiązania przy użyciu programu Visual Studio Włącz Pulpit zdalny. Aby uzyskać więcej informacji, zobacz [włączanie Podłączanie pulpitu zdalnego dla roli na platformie Azure Cloud Services przy użyciu programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. W Microsoft Azure Portal, gdy wystąpienie wskazuje stan **gotowe**, zdalny do wystąpienia. Aby uzyskać więcej informacji na temat używania pulpitu zdalnego z Cloud Services, zobacz [zdalne w wystąpieniach roli](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń określonych podczas konfiguracji Pulpit zdalny.
6. Otwórz okno polecenia.
7. Wpisz `IPconfig`.
8. Zanotuj wartość adres IPV4.
9. Otwórz program Internet Explorer.
10. Wpisz adres i nazwę aplikacji sieci Web. Na przykład `http://<IPV4 Address>/default.aspx`.

Przechodzenie do witryny sieci Web spowoduje teraz zwrócenie bardziej jawnych komunikatów o błędach:

* Błąd serwera w aplikacji "/".
* Opis: Wystąpił nieobsługiwany wyjątek podczas wykonywania bieżącego żądania sieci Web. Przejrzyj ślad stosu, aby uzyskać więcej informacji o błędzie i o tym, skąd pochodzi w kodzie.
* Szczegóły wyjątku: System. IO. FIleNotFoundException: nie można załadować pliku lub zestawu "Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35" lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku.

Na przykład:

![Jawny błąd serwera w aplikacji "/"](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnozowanie problemów przy użyciu emulatora obliczeń
Za pomocą emulatora obliczeń Microsoft Azure można diagnozować i rozwiązywać problemy dotyczące brakujących zależności i błędów web.config.

Aby uzyskać najlepsze wyniki przy użyciu tej metody diagnostyki, należy użyć komputera lub maszyny wirtualnej, która ma czystą instalację systemu Windows. Aby najlepiej symulować środowisko platformy Azure, należy użyć systemu Windows Server 2008 R2 x64.

1. Zainstaluj autonomiczną wersję [zestawu Azure SDK](https://azure.microsoft.com/downloads/).
2. Na komputerze deweloperskim Skompiluj projekt usługi w chmurze.
3. W Eksploratorze Windows przejdź do folderu bin\Debug w projekcie usługi w chmurze.
4. Skopiuj folder. CSX i plik. cscfg na komputer, który jest używany do debugowania problemów.
5. Na czystym komputerze otwórz okno wiersza polecenia zestawu Azure SDK i wpisz `csrun.exe /devstore:start` .
6. W wierszu polecenia wpisz polecenie `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` .
7. Po uruchomieniu roli zostaną wyświetlone szczegółowe informacje o błędzie w programie Internet Explorer. Możesz również użyć standardowych narzędzi do rozwiązywania problemów systemu Windows, aby dodatkowo zdiagnozować problem.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnozowanie problemów przy użyciu IntelliTrace
W przypadku ról procesów roboczych i sieci Web, które używają .NET Framework 4, możesz użyć [IntelliTrace](/visualstudio/debugger/intellitrace), który jest dostępny w Microsoft Visual Studio Enterprise.

Wykonaj następujące kroki, aby wdrożyć usługę z włączonym IntelliTrace:

1. Upewnij się, że zainstalowano zestaw Azure SDK 1,3 lub nowszy.
2. Wdróż rozwiązanie przy użyciu programu Visual Studio. Podczas wdrażania zaznacz pole wyboru **Włącz role IntelliTrace for .NET 4** .
3. Po uruchomieniu wystąpienia Otwórz **Eksplorator serwera**.
4. Rozwiń węzeł **\\ Cloud Services platformy Azure** i Znajdź wdrożenie.
5. Rozwiń wdrożenie, dopóki nie zobaczysz wystąpień roli. Kliknij prawym przyciskiem myszy jedno z wystąpień.
6. Wybierz pozycję **Wyświetl dzienniki IntelliTrace**. Zostanie otwarte **podsumowanie IntelliTrace** .
7. Znajdź sekcję wyjątki podsumowania. Jeśli istnieją wyjątki, sekcja będzie zawierać etykiety **danych wyjątków**.
8. Rozwiń **dane wyjątku** i Wyszukaj błędy **System. IO. FileNotFoundException** podobne do następujących:

![Dane wyjątku, brak pliku lub zestawu](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Brak bibliotek DLL i zestawów
Aby rozwiązać brakujące błędy bibliotek DLL i zestawów, wykonaj następujące kroki:

1. Otwórz rozwiązanie w programie Visual Studio.
2. W **Eksplorator rozwiązań** Otwórz folder **References** .
3. Kliknij zestaw zidentyfikowany w błędzie.
4. W okienku **Właściwości** Znajdź opcję **Kopiuj lokalną właściwość** i ustaw wartość na **true**.
5. Wdróż ponownie usługę w chmurze.

Po sprawdzeniu, czy wszystkie błędy zostały poprawione, można wdrożyć usługę bez sprawdzania, czy pole wyboru **Włącz role IntelliTrace for .NET 4** .

## <a name="next-steps"></a>Następne kroki
Zobacz więcej [artykułów do rozwiązywania problemów](../index.yml?product=cloud-services&tag=top-support-issue) dotyczących usług Cloud Services.

Aby dowiedzieć się, jak rozwiązywać problemy z rolą usługi w chmurze, korzystając z danych diagnostycznych dotyczących komputerów z usługą Azure PaaS, zobacz [Seria blogów Piotr Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
