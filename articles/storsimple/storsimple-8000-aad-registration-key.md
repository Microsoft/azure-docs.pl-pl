---
title: Uwierzytelnianie usługi Azure AD dla StorSimple 8000 w Device Manager
description: Wyjaśnia, jak używać uwierzytelniania opartego na usłudze AAD dla usługi, generować nowy klucz rejestracji i przeprowadzać ręczną rejestrację urządzeń.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805044"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Użyj uwierzytelniania Azure Active Directory (AD) dla StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Usługa StorSimple Device Manager działa w Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Do tej pory Usługa StorSimple Device Manager używała usługi Access Control Service (ACS) do uwierzytelniania usługi na urządzeniu StorSimple. Mechanizm ACS zostanie wkrótce uznany za przestarzały i zastąpiony przez uwierzytelnianie Azure Active Directory (AAD). Aby uzyskać więcej informacji, przejdź do poniższych powiadomień dotyczących zaniechania usługi ACS i używania uwierzytelniania usługi AAD.

- [Przyszłość usługi Azure ACS jest Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Nadchodzące zmiany w Access Control Service firmy Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

W tym artykule opisano szczegółowe informacje o uwierzytelnianiu w usłudze AAD oraz skojarzonym nowym kluczu rejestracji usługi i modyfikacjach reguł zapory dotyczących urządzeń StorSimple. Informacje zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000.

Uwierzytelnianie w usłudze AAD odbywa się na urządzeniu z serii StorSimple 8000 z aktualizacją Update 5 lub nowszym. Ze względu na wprowadzenie uwierzytelniania w usłudze AAD zmiany są wykonywane w:

- Wzorce adresów URL dla reguł zapory.
- Klucz rejestracji usługi.

Te zmiany zostały szczegółowo omówione w poniższych sekcjach.

## <a name="url-changes-for-aad-authentication"></a>Zmiany adresu URL na potrzeby uwierzytelniania usługi AAD

Aby zapewnić, że usługa korzysta z uwierzytelniania opartego na usłudze AAD, wszyscy użytkownicy muszą uwzględnić nowe adresy URL uwierzytelniania w regułach zapory.

W przypadku korzystania z serii StorSimple 8000 upewnij się, że w regułach zapory uwzględniono następujący adres URL:

| Wzorzec adresu URL                         | Chmura | Składnik/funkcjonalność         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure — publiczna |Usługa uwierzytelniania usługi AAD      |
| `https://login.microsoftonline.us` | US Government |Usługa uwierzytelniania usługi AAD      |

Aby uzyskać pełną listę wzorców adresów URL dla urządzeń z serii StorSimple 8000, przejdź do [wzorca adresu URL dla reguł zapory](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory wykraczających poza datę wycofania, a na urządzeniu jest uruchomiona Aktualizacja Update 5, użytkownicy zobaczą alert adresu URL. Użytkownicy muszą uwzględnić nowy adres URL uwierzytelniania. Jeśli na urządzeniu jest uruchomiona wersja wcześniejsza niż aktualizacja 5, użytkownicy zobaczą alert pulsu. W każdym przypadku urządzenie StorSimple nie może uwierzytelnić się w usłudze, a usługa nie może komunikować się z urządzeniem.

## <a name="device-version-and-authentication-changes"></a>Zmiany wersji i uwierzytelniania urządzenia

Jeśli korzystasz z urządzenia z serii StorSimple 8000, Skorzystaj z poniższej tabeli, aby określić, jaka akcja powinna zostać podjęta na podstawie uruchomionej wersji oprogramowania urządzenia.

| Jeśli urządzenie jest uruchomione| Wykonaj następujące czynności                                    |
|--------------------------|------------------------|
| Aktualizacja Update 5 lub nowsza, a urządzenie jest w trybie offline. <br> Zostanie wyświetlony alert informujący, że adres URL nie jest zatwierdzony.|1. zmodyfikuj reguły zapory w taki sposób, aby zawierały adres URL uwierzytelniania. Zobacz [adresy URL uwierzytelniania](#url-changes-for-aad-authentication).<br>2. [Pobierz klucz rejestracji AAD z usługi](#aad-based-registration-keys).<br>3. [Połącz się z interfejsem programu Windows PowerShell dla urządzenia z serii StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` Aby zarejestrować urządzenie za pomocą programu Windows PowerShell, użyj polecenia cmdlet. Podaj klucz uzyskany w poprzednim kroku.|
| Aktualizacja Update 5 lub nowsza oraz urządzenie w trybie online.| Nie jest wymagana żadna akcja.                                       |
| Aktualizacja Update 4 lub wcześniejsza, a urządzenie jest w trybie offline. |1. zmodyfikuj reguły zapory w taki sposób, aby zawierały adres URL uwierzytelniania.<br>2. [Pobierz aktualizację Update 5 za poorednictwem serwera wykazu](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Zastosuj aktualizację Update 5 za pomocą metody hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Pobierz klucz rejestracji AAD z usługi](#aad-based-registration-keys).<br>5. [Połącz się z interfejsem programu Windows PowerShell dla urządzenia z serii StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` Aby zarejestrować urządzenie za pomocą programu Windows PowerShell, użyj polecenia cmdlet. Podaj klucz uzyskany w poprzednim kroku.|
| Aktualizacja Update 4 lub wcześniejsza, a urządzenie jest w trybie online. |Zmodyfikuj reguły zapory w taki sposób, aby zawierały adres URL uwierzytelniania.<br> Zainstaluj aktualizację Update 5 za pomocą Azure Portal.              |
| Resetowanie do wersji fabrycznej przed aktualizacją Update 5.      |W portalu jest wyświetlany klucz rejestracji oparty na usłudze AAD, gdy na urządzeniu jest uruchomione starsze oprogramowanie. Wykonaj kroki opisane w poprzednim scenariuszu, jeśli na urządzeniu jest uruchomiona Aktualizacja Update 4 lub wcześniejsza wersja.              |

## <a name="aad-based-registration-keys"></a>Klucze rejestracji oparte na usłudze AAD

Począwszy od aktualizacji Update 5 dla urządzeń z serii StorSimple 8000, używane są nowe klucze rejestracji oparte na usłudze AAD. Klucze rejestracji są używane do rejestrowania usługi StorSimple Device Manager z urządzeniem.

Nie można użyć nowych kluczy rejestracji usługi AAD, jeśli używasz urządzenia z serii StorSimple 8000 z aktualizacją Update 4 lub starszej wersji (obejmuje to starsze urządzenie, które jest teraz uaktywniane).
W tym scenariuszu należy ponownie wygenerować klucz rejestracji usługi. Po ponownym wygenerowaniu klucza nowy klucz jest używany do rejestrowania wszystkich kolejnych urządzeń. Stary klucz nie jest już prawidłowy.

- Nowy klucz rejestracji usługi AAD wygasa po 3 dniach.
- Klucze rejestracji usługi AAD działają tylko w przypadku urządzeń z serii StorSimple 8000 z uruchomioną aktualizacją Update 5 lub nowszą.
- Klucze rejestracji usługi AAD są dłuższe niż odpowiednie klucze rejestracji usługi ACS.

Wykonaj poniższe kroki, aby wygenerować klucz rejestracji usługi AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Aby wygenerować klucz rejestracji usługi AAD

1. W **StorSimple Device Manager** przejdź do pozycji **klucze** **&gt; zarządzania** . Możesz również użyć paska wyszukiwania, aby wyszukać _klucze_.
    
2. Kliknij przycisk **Wygeneruj klucz**.

    ![Kliknij pozycję Wygeneruj ponownie](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Skopiuj nowy klucz. Starszy klucz nie będzie już działać.

    ![Potwierdź ponowne wygenerowanie](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Jeśli tworzysz urządzenie w chmurze StorSimple w usłudze zarejestrowane na urządzeniu z serii StorSimple 8000, nie Generuj klucza rejestracji, gdy trwa tworzenie. Zaczekaj na zakończenie tworzenia, a następnie Wygeneruj klucz rejestracji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat wdrażania [urządzeń z serii StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).
