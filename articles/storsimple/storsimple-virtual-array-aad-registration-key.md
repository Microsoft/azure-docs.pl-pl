---
title: Nowe uwierzytelnianie dla macierzy wirtualnych StorSimple
description: Informacje o uwierzytelnianiu w usłudze AAD, skojarzonym nowym kluczu rejestracji usługi i modyfikacjach reguł zapory dotyczących urządzeń StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 75332498ac59dc46a7a079eff4c25e02b2a6cb9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986941"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Użyj nowego uwierzytelniania StorSimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Usługa StorSimple Device Manager działa w Microsoft Azure i łączy się z wieloma wirtualnymi macierzami StorSimple. Do tej pory Usługa StorSimple Device Manager używała usługi Access Control Service (ACS) do uwierzytelniania usługi na urządzeniu StorSimple. Mechanizm ACS zostanie wkrótce uznany za przestarzały i zastąpiony przez uwierzytelnianie Azure Active Directory (AAD).

Informacje zawarte w tym artykule dotyczą tylko macierzy wirtualnych z serii StorSimple 1200. W tym artykule opisano szczegółowe informacje o uwierzytelnianiu w usłudze AAD oraz skojarzonym nowym kluczu rejestracji usługi i modyfikacjach reguł zapory dotyczących urządzeń StorSimple.

Uwierzytelnianie w usłudze AAD odbywa się w macierzach wirtualnych StorSimple (model 1200) z uruchomioną aktualizacją Update 1 lub nowszą.

Ze względu na wprowadzenie uwierzytelniania w usłudze AAD zmiany są wykonywane w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Te zmiany zostały szczegółowo omówione w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL na potrzeby uwierzytelniania usługi AAD

Aby zapewnić, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą uwzględnić nowe adresy URL uwierzytelniania w regułach zapory.

W przypadku używania macierzy wirtualnej StorSimple upewnij się, że w regułach zapory znajduje się następujący adres URL:

| Wzorzec adresu URL                         | Chmura | Składnik/funkcjonalność         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure — publiczna |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | US Government |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę wzorców adresów URL dla macierzy wirtualnych StorSimple, przejdź do [wzorca adresu URL dla reguł zapory](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory wykraczających poza datę wycofania, użytkownicy zobaczą alert krytyczny, że urządzenie StorSimple nie może uwierzytelnić się w usłudze. Usługa nie będzie mogła komunikować się z urządzeniem. Jeśli użytkownicy zobaczą ten alert, muszą uwzględnić nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji na temat alertu, przejdź do pozycji [Korzystanie z alertów w celu monitorowania urządzenia z systemem StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelniania urządzenia

W przypadku używania macierzy wirtualnej StorSimple należy skorzystać z poniższej tabeli, aby określić działanie, które należy wykonać w zależności od używanej wersji oprogramowania urządzenia.

| Jeśli urządzenie jest uruchomione  | Wykonaj następujące czynności                                    |
|----------------------------|--------------------------------------------------------------|
| Aktualizacja 1,0 lub nowsza, która jest w trybie offline. <br> Zostanie wyświetlony alert informujący o tym, że adres URL nie jest allowlisted.| 1. zmodyfikuj reguły zapory w taki sposób, aby zawierały adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication). <br> 2. [Pobierz klucz rejestracji AAD z usługi](#aad-based-registration-keys). <br> 3. wykonaj kroki 1-5, aby [nawiązać połączenie z interfejsem programu Windows PowerShell macierzy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. `Invoke-HcsReRegister` Aby zarejestrować urządzenie za pomocą programu Windows PowerShell, użyj polecenia cmdlet. Podaj klucz uzyskany w poprzednim kroku.|
| Aktualizacja 1,0 lub nowsza, a urządzenie jest w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Aktualizacja 0,6 lub starsza, a urządzenie jest w trybie offline. | 1. [Pobierz aktualizację 1,0 za poorednictwem serwera wykazu](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Zastosuj aktualizację 1,0 za pomocą lokalnego interfejsu użytkownika sieci Web](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Pobierz klucz rejestracji AAD z usługi](#aad-based-registration-keys). <br>4. wykonaj kroki 1-5, aby [nawiązać połączenie z interfejsem programu Windows PowerShell macierzy wirtualnej](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. `Invoke-HcsReRegister` Aby zarejestrować urządzenie za pomocą programu Windows PowerShell, użyj polecenia cmdlet. Podaj klucz uzyskany w poprzednim kroku.|
| Aktualizacja 0,6 lub starsza, a urządzenie jest w trybie online | Zmodyfikuj reguły zapory w taki sposób, aby zawierały adres URL uwierzytelniania.<br> Zainstaluj aktualizację 1,0 za pomocą Azure Portal. |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji oparte na usłudze AAD

Począwszy od aktualizacji 1,0 dla macierzy wirtualnych StorSimple są używane nowe klucze rejestracji oparte na usłudze AAD. Klucze rejestracji są używane do rejestrowania usługi StorSimple Device Manager z urządzeniem.

Nie można użyć nowych kluczy rejestracji usługi AAD, jeśli używasz StorSimple wirtualnych macierzy z uruchomioną aktualizacją 0,6 lub wcześniejszą. Należy ponownie wygenerować klucz rejestracji usługi. Po ponownym wygenerowaniu klucza nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dniach.
- Klucze rejestracji usługi AAD działają tylko z macierzami wirtualnymi serii StorSimple 1200 z aktualizacją Update 1 lub nowszą. Klucz rejestracji usługi AAD z urządzenia z serii StorSimple 8000 nie będzie działał.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiednie klucze rejestracji usługi ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **StorSimple Device Manager** przejdź do pozycji **klucze** **&gt; zarządzania** .
    
    ![Przejdź do kluczy](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kliknij przycisk **Wygeneruj klucz**.

    ![Kliknij pozycję Wygeneruj ponownie](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz nie działa już.

    ![Potwierdź ponowne wygenerowanie](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat wdrażania [macierzy wirtualnej StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
