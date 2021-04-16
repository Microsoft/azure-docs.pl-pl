---
title: 'Wersja zapoznawcza: wdrażanie zaufanej maszyny wirtualnej uruchamiania'
description: Wdrażanie maszyny wirtualnej, która używa zaufanego uruchamiania.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565920"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Wdrażanie maszyny wirtualnej z włączonym zaufanym uruchamianiem (wersja zapoznawcza)

[Zaufane uruchamianie](trusted-launch.md) to sposób na zwiększenie bezpieczeństwa maszyn wirtualnych [2.](generation-2.md) generacji. Zaufane uruchamianie chroni przed zaawansowanymi i trwałymi technikami ataku, łącząc technologie infrastruktury, takie jak vTPM i bezpieczny rozruch.

> [!IMPORTANT]
> Zaufane uruchamianie jest obecnie dostępne w publicznej wersji zapoznawczej.
> 
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Wdrażanie przy użyciu portalu

Utwórz maszynę wirtualną z włączonym zaufanym uruchamianiem.

1. Zaloguj się do witryny Azure [Portal.](https://aka.ms/TL_preview)
   > [!NOTE] 
   > Link do portalu jest unikatowy dla zaufanej wersji zapoznawczej uruchamiania.
   >  
2. Wyszukaj **Virtual Machines**.
3. W **obszarze Usługi** wybierz pozycję Maszyny **wirtualne.**
4. Na stronie **Maszyny wirtualne** wybierz pozycję **Dodaj,** a następnie wybierz **pozycję Maszyna wirtualna.**
5. W **obszarze Szczegóły** projektu upewnij się, że wybrano poprawną subskrypcję.
6. W **obszarze Grupa zasobów** wybierz pozycję **Utwórz** nową i wpisz nazwę grupy zasobów lub wybierz istniejącą grupę zasobów z listy rozwijanej.
7. W **obszarze Szczegóły wystąpienia** wpisz nazwę maszyny wirtualnej i wybierz region, który obsługuje zaufane [uruchamianie](trusted-launch.md#public-preview-limitations).
8. W **obszarze Obraz** wybierz obraz gen 2, który obsługuje zaufane [uruchamianie](trusted-launch.md#public-preview-limitations). Upewnij się, że jest wyświetlany następujący komunikat: **Ten obraz obsługuje zaufaną podgląd uruchamiania. Skonfiguruj na karcie Zaawansowane.**
   > [!TIP]
   > Jeśli na liście rozwijanej nie widzisz wersji Gen 2 obrazu,  wybierz pozycję Zobacz wszystkie  obrazy, a następnie zmień filtr generacji maszyny wirtualnej, aby wyświetlać tylko obrazy gen 2. generacji. Znajdź obraz na liście, a  następnie wybierz wersję Gen 2 za pomocą listy rozwijanej Wybierz.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Zrzut ekranu przedstawiający komunikat potwierdzający, że jest to obraz 2. generacji obsługujący zaufane uruchamianie.":::

13. Wybierz rozmiar maszyny wirtualnej, który obsługuje zaufane uruchamianie. Zobacz listę [obsługiwanych rozmiarów.](trusted-launch.md#public-preview-limitations)
14. Wypełnij informacje o **koncie administratora,** a następnie **w polach Reguły portów dla ruchu przychodzącego.** 
1. Przejdź do karty **Zaawansowane,** wybierając ją w górnej części strony.
1. Przewiń w dół do **sekcji vm generation (Generowanie maszyny wirtualnej).** Upewnij się, **że wybrano opcję Gen 2.**
1. Na karcie Zaawansowane **przewiń** w dół do listy **Zaufane uruchamianie,** a następnie zaznacz pole wyboru **Zaufane** uruchamianie. Spowoduje to, że zostaną wyświetlone dwie dodatkowe opcje — Bezpieczny rozruch i vTPM. Wybierz odpowiednie opcje dla wdrożenia.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Zrzut ekranu przedstawiający opcje zaufanego uruchamiania.":::

15. W dolnej części strony wybierz pozycję **Przeglądanie + tworzenie**
16. Na **stronie Tworzenie maszyny wirtualnej** są dostępne szczegółowe informacje o maszynie wirtualnej, która ma zostać wdrożona. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Zrzut ekranu przedstawiający stronę weryfikacji z dołączonymi zaufanymi opcjami uruchamiania.":::


Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="deploy-using-a-template"></a>Wdrażanie przy użyciu szablonu

Zaufane maszyny wirtualne uruchamiania można wdrożyć przy użyciu szablonu szybkiego startu:

**Linux**:    
[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Wyświetlanie i aktualizowanie

Konfigurację zaufanego uruchamiania istniejącej maszyny wirtualnej można wyświetlić, odwiedzając stronę **Przegląd** maszyny wirtualnej w portalu.

Aby zmienić zaufaną konfigurację uruchamiania, w menu po lewej stronie wybierz **pozycję Konfiguracja** w **sekcji** Ustawienia. W sekcji Zaufane uruchamianie można włączyć  lub wyłączyć bezpieczny rozruch i program vTPM. Gdy **wszystko będzie** gotowe, wybierz pozycję Zapisz w górnej części strony. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Zrzut ekranu przedstawiający sposób zmiany konfiguracji zaufanego uruchamiania.":::

Jeśli maszyna wirtualna jest uruchomiona, zostanie wyświetlony komunikat informujący o ponownym uruchomieniu maszyny wirtualnej w celu zastosowania zmodyfikowanej konfiguracji zaufanego uruchamiania. Wybierz **pozycję Tak,** a następnie poczekaj na ponowne uruchomienie maszyny wirtualnej, aby zmiany weszły w życie.


## <a name="verify-secure-boot-and-vtpm"></a>Weryfikowanie bezpiecznego rozruchu i maszyny wirtualnej vTPM

Możesz sprawdzić, czy na maszynie wirtualnej jest włączony bezpieczny rozruch i maszyna wirtualna.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: sprawdź, czy jest uruchomiony bezpieczny rozruch

Na maszynie wirtualnej za pomocą polecenia SSH uruchom następujące polecenie: 

```bash
mokutil --sb-state
```

Jeśli bezpieczny rozruch jest włączyć, polecenie zwróci:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: sprawdź, czy vTPM jest włączony

Połącz się z maszyną wirtualną za pośrednictwem protokołu SSH. Sprawdź, czy urządzenie TPM0 jest obecne: 

```bash
ls /dev/tpm0
```

Jeśli vTPM jest włączona, polecenie zwróci:

```output
/dev/tpm0
```

Jeśli vTPM jest wyłączona, polecenie zwróci:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: sprawdź, czy bezpieczny rozruch jest uruchomiony

Połącz się z maszyną wirtualną przy użyciu pulpitu zdalnego, a następnie `msinfo32.exe` uruchom .

W okienku po prawej stronie sprawdź, czy stan bezpiecznego rozruchu jest **WŁ.**

## <a name="enable-the-azure-security-center-experience"></a>Włączanie Azure Security Center użytkownika

Aby umożliwić Azure Security Center wyświetlania informacji o zaufanych startowych maszyn wirtualnych, należy włączyć kilka zasad. Najprostszym sposobem włączenia zasad jest wdrożenie tego szablonu [Resource Manager subskrypcji.](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) 

Wybierz poniższy przycisk, aby wdrożyć zasady w subskrypcji:

[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Szablon należy wdrożyć tylko raz dla subskrypcji. Automatycznie instaluje rozszerzenia `GuestAttestation` `AzureSecurity` na wszystkich obsługiwanych maszyn wirtualnych. Jeśli występują błędy, spróbuj ponownie uruchomić szablon.

Aby uzyskać zalecenia dotyczące maszyny wirtualnej vTPM i bezpiecznego rozruchu dla maszyn wirtualnych z zaufanym uruchamianiem, zobacz [Dodawanie niestandardowej inicjatywy do subskrypcji](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Podpisywanie bezpiecznego rozruchu w systemie Linux

W niektórych przypadkach może być konieczne podpisanie umowy bezpiecznego rozruchu UEFI.  Na przykład może być konieczne, aby przejść przez jak podpisywać [rzeczy dla bezpiecznego rozruchu](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) dla systemu Ubuntu. W takich przypadkach należy wprowadzić klucze rejestracji klucza mok dla maszyny wirtualnej. W tym celu należy użyć konsoli szeregowej platformy Azure, aby uzyskać dostęp do narzędzia MOK.

1. Włącz konsolę szeregową platformy Azure dla systemu Linux. Aby uzyskać więcej informacji, zobacz [Serial Console for Linux (Konsola szeregowa dla systemu Linux).](/troubleshoot/azure/virtual-machines/serial-console-linux)
1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Wyszukaj pozycję **Maszyny wirtualne** i wybierz maszynę wirtualną z listy.
1. W menu po lewej stronie w **obszarze Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **Konsola szeregowa**. Po prawej stronie zostanie otwarta strona z konsolą szeregową.
1. Zaloguj się do maszyny wirtualnej przy użyciu konsoli szeregowej platformy Azure. W **przypadku logowania** wprowadź nazwę użytkownika u używaną podczas tworzenia maszyny wirtualnej. Na przykład *azureuser*. Po wyświetleniu monitu wprowadź hasło skojarzone z nazwą użytkownika.
1. Po zalogowaniu użyj funkcji , `mokutil` aby zaimportować plik klucza `.der` publicznego.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Uruchom ponownie maszynę z konsoli szeregowej platformy Azure, wpisując `sudo reboot` . Rozpocznie się odliczanie do 10 sekund.
1. Naciśnij klawisz w górę lub w dół, aby przerwać odliczanie i czekać w trybie konsoli UEFI. Jeśli czasomierz nie zostanie przerwany, proces rozruchu będzie kontynuowany i wszystkie zmiany mok zostaną utracone.
1. Wybierz odpowiednią akcję z menu narzędzia MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Zrzut ekranu przedstawiający dostępne opcje w menu zarządzania kluczem mok w konsoli szeregowej.":::


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zaufanych uruchamianych](trusted-launch.md) i [2.](generation-2.md) generacji maszyn wirtualnych.
