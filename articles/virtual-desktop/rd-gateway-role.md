---
title: Wdrażanie roli bramy usług pulpitu zdalnego Windows Virtual Desktop — Azure
description: Jak wdrożyć rolę bramy usług pulpitu zdalnego w systemie Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 37d859bccd7fea082e059c7ab19c789276974cd1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445690"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Wdrażanie roli Brama usług pulpitu zdalnego w systemie Windows Virtual Desktop (wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule przedstawiono sposób korzystania z roli bramy usług pulpitu zdalnego (wersja zapoznawcza) w celu wdrażania serwerów bramy Pulpit zdalny w danym środowisku. Role serwera można zainstalować na maszynach fizycznych lub maszynach wirtualnych w zależności od tego, czy tworzysz środowisko lokalne, oparte na chmurze czy hybrydowe.

## <a name="install-the-rd-gateway-role"></a>Instalowanie roli Brama usług pulpitu zdalnego

1. Zaloguj się na serwerze docelowym przy użyciu poświadczeń administracyjnych.

2. W **Menedżer serwera** wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **Dodaj role i funkcje**. Zostanie otwarty Instalator **dodawania ról i funkcji** .

3. W obszarze **przed rozpoczęciem** wybierz pozycję **dalej**.

4. W obszarze **Wybieranie typu instalacji** wybierz pozycję **Instalacja oparta na rolach lub oparta na funkcjach**, a następnie wybierz przycisk **dalej**.

5. W polu **Wybierz serwer docelowy** wybierz opcję **Wybierz serwer z puli serwerów**. W polu **Pula serwerów** wybierz nazwę komputera lokalnego. Po zakończeniu wybierz pozycję **Dalej**.

6. W obszarze **Wybieranie ról serwera**  >  **ról** wybierz pozycję **usługi pulpitu zdalnego**. Po zakończeniu wybierz pozycję **Dalej**.

7. W obszarze **usługi pulpitu zdalnego** wybierz pozycję **Dalej.**

8. W obszarze **Wybieranie usług ról** wybierz opcję tylko **pulpit zdalny Brama** po wyświetleniu monitu o dodanie wymaganych funkcji wybierz pozycję **Dodaj funkcje**. Po zakończeniu wybierz pozycję **Dalej**.

9. W przypadku **usług zasad sieciowych i dostępu sieciowego** wybierz pozycję **dalej**.

10. W polu **rola serwera sieci Web (IIS)** wybierz pozycję **dalej**.

11. W obszarze **Wybieranie usług ról** wybierz pozycję **dalej**.

12. W obszarze **Potwierdź wybrane opcje instalacji** wybierz pozycję **Zainstaluj**. Nie zamykaj Instalatora, gdy wystąpi proces instalacji.

## <a name="configure-rd-gateway-role"></a>Konfigurowanie roli bramy usług pulpitu zdalnego

Po zainstalowaniu roli Brama usług pulpitu zdalnego należy ją skonfigurować.

Aby skonfigurować rolę bramy usług pulpitu zdalnego:

1. Otwórz **Menedżer serwera** a następnie wybierz pozycję **usługi pulpitu zdalnego**.

2. Przejdź do pozycji **serwery**, kliknij prawym przyciskiem myszy nazwę serwera, a następnie wybierz pozycję **Menedżer bramy usług pulpitu zdalnego**.

3. W Menedżerze bramy usług pulpitu zdalnego kliknij prawym przyciskiem myszy nazwę bramy, a następnie wybierz pozycję **Właściwości**.

4. Otwórz kartę **certyfikat SSL** , wybierz pozycję **Importuj certyfikat do bąbelka bramy usług pulpitu zdalnego** , a następnie wybierz pozycję **Przeglądaj i Importuj certyfikat.**...

5. Wybierz nazwę pliku PFX, a następnie wybierz pozycję **Otwórz**.

6. Po wyświetleniu monitu wprowadź hasło dla pliku PFX.

7. Po zaimportowaniu certyfikatu i jego klucza prywatnego, powinien on wyświetlać atrybuty klucza certyfikatu.

>[!NOTE]
>Ponieważ rola bramy usług pulpitu zdalnego powinna być publiczna, zalecamy użycie publicznie wystawionego certyfikatu. Jeśli używasz certyfikatu wystawionego prywatnie, musisz upewnić się, że wszyscy klienci mają wcześniej skonfigurowany łańcuch zaufania certyfikatu.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dodać wysoką dostępność do roli bramy usług pulpitu zdalnego, zobacz temat [Dodawanie wysokiej dostępności do frontonu sieci Web i bramy usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).