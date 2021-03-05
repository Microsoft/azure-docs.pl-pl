---
title: 'Wersja zapoznawcza: wdrażanie zaufanej maszyny wirtualnej uruchamiania'
description: Wdróż maszynę wirtualną korzystającą z zaufanego uruchamiania.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: fca11ce1cfa09fb680c2b288e40fa5f51337bdb8
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200788"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Wdróż maszynę wirtualną z włączonym zaufanym uruchamianiem (wersja zapoznawcza)

[Zaufane uruchomienia](trusted-launch.md) to sposób na zwiększenie bezpieczeństwa maszyn wirtualnych [2. generacji](generation-2.md) . Zaufane uruchomienia chronią przed zaawansowanymi i trwałymi technikami ataków, łącząc technologie infrastruktury, takie jak vTPM i bezpieczny rozruch.

> [!IMPORTANT]
> Zaufane uruchomienie jest obecnie w publicznej wersji zapoznawczej.
> 
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Utwórz maszynę wirtualną z włączonym zaufanym uruchamianiem.

1. Zaloguj się do witryny Azure [Portal](https://aka.ms/TL_preview).
1. Wyszukaj **Virtual Machines**.
1. W obszarze **usługi** wybierz pozycję **maszyny wirtualne**.
1. Na stronie **maszyny wirtualne** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **maszyna wirtualna**.
1. W obszarze **szczegóły projektu** upewnij się, że wybrano poprawną subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wpisz nazwę grupy zasobów lub wybierz istniejącą grupę zasobów z listy rozwijanej.
1. W obszarze **szczegóły wystąpienia** wpisz nazwę maszyny wirtualnej i wybierz region obsługujący [Zaufane uruchomienie](trusted-launch.md#public-preview-limitations).
1. W obszarze **obraz** wybierz [obraz, który obsługuje zaufane uruchomienie](trusted-launch.md#public-preview-limitations). Możesz zobaczyć tylko wersję generacji 1 obrazu, która jest nieporozumiena, przejdź do następnego kroku.
1. Przejdź do karty **Zaawansowane** , wybierając ją w górnej części strony.
1. Przewiń w dół do sekcji **generacja maszyny wirtualnej** , a następnie wybierz pozycję **Gen 2**.
1. Na karcie **Zaawansowane** przewiń w dół do opcji **Zaufane uruchomienia**, a następnie zaznacz pole wyboru **Zaufane uruchomienie** . Spowoduje to wyświetlenie dwóch dodatkowych opcji — Bezpieczny rozruch i vTPM. Wybierz odpowiednie opcje dla danego wdrożenia.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Zrzut ekranu przedstawiający opcje zaufanego uruchamiania.":::

1. Wróć do karty **podstawowe** , w obszarze **obraz** i upewnij się, że zobaczysz następujący komunikat: **ten obraz obsługuje funkcję zaufanego uruchamiania podglądu. Skonfiguruj na karcie Zaawansowane**. Obraz generacji 2 powinien być teraz wybrany.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Zrzut ekranu przedstawiający komunikat potwierdzający, że jest to obraz Gen2, który obsługuje zaufane uruchomienie.":::

1.  Wybierz rozmiar maszyny wirtualnej, który obsługuje zaufane uruchomienie. Zapoznaj się z listą [obsługiwanych rozmiarów](trusted-launch.md#public-preview-limitations).
1.  Wprowadź informacje o **koncie administratora** , a następnie **reguły portów ruchu przychodzącego**.
1.  W dolnej części strony wybierz pozycję **Przegląd + Utwórz** .
1.  Na stronie **Tworzenie maszyny wirtualnej** można zobaczyć szczegóły dotyczące maszyny wirtualnej, która ma zostać wdrożona. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot strony walidacji, w której znajdują się opcje zaufanego uruchamiania.":::


Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="deploy-using-a-template"></a>Wdrażanie przy użyciu szablonu

Zaufane maszyny wirtualne uruchamiania można wdrożyć przy użyciu szablonu szybkiego startu:

System **Linux**:    
[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Wyświetl i Aktualizuj

Zaufaną konfigurację uruchamiania dla istniejącej maszyny wirtualnej można wyświetlić, odwiedzając stronę **Przegląd** dla maszyny wirtualnej w portalu.

Aby zmienić konfigurację zaufanego uruchamiania, w menu po lewej stronie wybierz pozycję **Konfiguracja** w sekcji **Ustawienia** . Można włączyć lub wyłączyć bezpieczne rozruch i vTPM w sekcji **zaufanego uruchamiania** . Gdy skończysz, wybierz pozycję **Zapisz** w górnej części strony. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Zrzut ekranu przedstawiający sposób zmiany konfiguracji zaufanego uruchamiania.":::

Jeśli maszyna wirtualna jest uruchomiona, zostanie wyświetlony komunikat informujący, że maszyna wirtualna zostanie ponownie uruchomiona w celu zastosowania zmodyfikowanej konfiguracji zaufanego uruchamiania. Wybierz pozycję **tak** , a następnie poczekaj na ponowne uruchomienie maszyny wirtualnej, aby zmiany zaczęły obowiązywać.


## <a name="verify-secure-boot-and-vtpm"></a>Weryfikowanie bezpiecznego rozruchu i vTPM

Można sprawdzić, czy na maszynie wirtualnej jest włączony bezpieczny rozruch i vTPM.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: Sprawdź, czy jest uruchomiony bezpieczny rozruch

Użyj protokołu SSH do maszyny wirtualnej, a następnie uruchom następujące polecenie: 

```bash
mokutil --sb-state
```

Jeśli bezpieczny rozruch jest włączony, polecenie zwróci:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: Sprawdzanie, czy vTPM jest włączony

Połącz się z maszyną wirtualną za pośrednictwem protokołu SSH. Sprawdź, czy urządzenie tpm0 jest obecne: 

```bash
ls /dev/tpm0
```

Jeśli vTPM jest włączona, polecenie zwróci:

```output
/dev/tpm0
```

Jeśli vTPM jest wyłączone, polecenie zwróci:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>System Windows: Weryfikowanie, czy bezpieczny rozruch jest uruchomiony

Nawiąż połączenie z maszyną wirtualną przy użyciu pulpitu zdalnego, a następnie uruchom polecenie `msinfo32.exe` .

W okienku po prawej stronie Sprawdź, czy bezpieczny rozruch jest **włączony**.

## <a name="enable-the-azure-security-center-experience"></a>Włącz środowisko Azure Security Center

Aby umożliwić Azure Security Center wyświetlanie informacji o zaufanych maszynach wirtualnych uruchamiania, należy włączyć kilka zasad. Najprostszym sposobem włączenia zasad jest wdrożenie tego [szablonu Menedżer zasobów](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) w ramach subskrypcji. 

Wybierz przycisk poniżej, aby wdrożyć zasady dla subskrypcji:

[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Szablon należy wdrożyć tylko raz na subskrypcję. Automatycznie instaluje `GuestAttestation` i `AzureSecurity` rozszerza wszystkie obsługiwane maszyny wirtualne. Jeśli pojawią się błędy, spróbuj ponownie wdrożyć szablon.

Aby uzyskać zalecenia dotyczące vTPM i bezpiecznego rozruchu dla zaufanych maszyn wirtualnych uruchamiania, zobacz [Dodawanie niestandardowej inicjatywy do subskrypcji](https://docs.microsoft.com/azure/security-center/custom-security-policies#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Podpisywanie w celu bezpiecznego rozruchu w systemie Linux

W niektórych przypadkach może być konieczne zarejestrowanie się w celu bezpiecznego rozruchu UEFI.  Na przykład może być konieczne przechodzenie przez [sposób podpisywania elementów w celu bezpiecznego rozruchu](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) dla Ubuntu. W takich przypadkach należy wprowadzić klucze rejestracji narzędzi MOK dla maszyny wirtualnej. Aby to zrobić, należy użyć konsoli szeregowej platformy Azure w celu uzyskania dostępu do narzędzia MOK.

1. Włącz usługę Azure serial Console dla systemu Linux. Aby uzyskać więcej informacji, zobacz [konsola szeregowa dla systemu Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Wyszukaj **maszyny wirtualne** i wybierz maszynę wirtualną z listy.
1. W menu po lewej stronie w obszarze **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Po prawej stronie zostanie otwarta strona z konsolą szeregową.
1. Zaloguj się do maszyny wirtualnej za pomocą konsoli serialowej platformy Azure. W polu **Nazwa logowania** wprowadź nazwę użytkownika, która została użyta podczas tworzenia maszyny wirtualnej. Na przykład *azureuser*. Po wyświetleniu monitu wprowadź hasło skojarzone z nazwą użytkownika.
1. Po zalogowaniu się Użyj, `mokutil` Aby zaimportować plik klucza publicznego `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Uruchom ponownie maszynę z poziomu konsoli szeregowej platformy Azure, wpisując polecenie `sudo reboot` . Rozpocznie się 10 sekund odliczania.
1. Naciśnij klawisz w górę lub w dół, aby przerwać odliczanie i oczekiwanie w trybie konsoli UEFI. Jeśli czasomierz nie zostanie przerwany, proces rozruchu będzie kontynuowany, a wszystkie zmiany MOK zostaną utracone.
1. Wybierz odpowiednią akcję z menu Narzędzia MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Zrzut ekranu przedstawiający dostępne opcje w menu zarządzania MOK w konsoli szeregowej.":::


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zaufanych maszynach wirtualnych [uruchamiania](trusted-launch.md) i [generacji 2](generation-2.md) .
