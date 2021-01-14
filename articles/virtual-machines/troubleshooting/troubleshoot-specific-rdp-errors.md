---
title: Określone komunikaty o błędach protokołu RDP dla maszyn wirtualnych platformy Azure | Microsoft Docs
description: Informacje o konkretnych komunikatach o błędach, które mogą pojawić się podczas próby użycia Pulpit zdalny połączenia z maszyną wirtualną z systemem Windows na platformie Azure
keywords: Błąd pulpitu zdalnego, błąd połączenia pulpitu zdalnego, nie można nawiązać połączenia z maszyną wirtualną, rozwiązywanie problemów z pulpitem zdalnym
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 808443cb805b2dee2426198f9cd4f7ba502d8999
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197510"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Rozwiązywanie problemów z określonymi komunikatami o błędach protokołu RDP dla maszyny wirtualnej z systemem Windows na platformie Azure
W przypadku korzystania z Pulpit zdalny połączenia z maszyną wirtualną z systemem Windows na platformie Azure może zostać wyświetlony konkretny komunikat o błędzie. W tym artykule opisano niektóre typowe komunikaty o błędach, a także kroki rozwiązywania problemów. Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną przy użyciu protokołu RDP, ale nie napotkasz określonego komunikatu o błędzie, zobacz [Przewodnik rozwiązywania problemów dla Pulpit zdalny](troubleshoot-rdp-connection.md).

Aby uzyskać informacje dotyczące konkretnych komunikatów o błędach, zobacz następujące tematy:

* [Sesja zdalna została rozłączona z powodu braku dostępnych serwerów licencji pulpit zdalny w celu udostępnienia licencji](#rdplicense).
* [Pulpit zdalny nie może znaleźć komputera "name"](#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych](#rdpauth).
* [Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają](#wincred).
* [Ten komputer nie może nawiązać połączenia z komputerem zdalnym](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Sesja zdalna została rozłączona z powodu braku dostępnych serwerów licencji Pulpit zdalny w celu udostępnienia licencji.
Przyczyna: okres prolongaty licencjonowania 120 dla roli serwera Pulpit zdalny wygasł i należy zainstalować licencje.

Aby obejść ten sposób, Zapisz lokalną kopię pliku RDP z portalu i Uruchom to polecenie w wierszu polecenia programu PowerShell, aby nawiązać połączenie. Ten krok powoduje wyłączenie licencjonowania tylko dla tego połączenia:

```powershell
mstsc <File name>.RDP /admin
```

Jeśli nie potrzebujesz więcej niż dwóch jednoczesnych połączeń Pulpit zdalny z maszyną wirtualną, możesz użyć Menedżer serwera, aby usunąć rolę serwera Pulpit zdalny.

Aby uzyskać więcej informacji, zobacz wpis w blogu na [maszynie wirtualnej platformy Azure nie powiodło się z opcją "Brak dostępnych serwerów licencji pulpit zdalny"](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Pulpit zdalny nie może znaleźć komputera "name".
Przyczyna: klient Pulpit zdalny na komputerze nie może rozpoznać nazwy komputera w ustawieniach pliku RDP.

Możliwe rozwiązania:

* Jeśli korzystasz z intranetu organizacji, upewnij się, że komputer ma dostęp do serwera proxy i może wysłać do niego ruch HTTPS.
* Jeśli używasz lokalnie przechowywanego pliku RDP, spróbuj użyć takiego, który jest generowany przez portal. Ten krok zapewnia, że masz poprawną nazwę DNS maszyny wirtualnej lub usługę w chmurze i Port punktu końcowego maszyny wirtualnej. Oto przykładowy plik RDP wygenerowany przez portal:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

Część adresu tego pliku RDP ma:

* W pełni kwalifikowana nazwa domeny usługi w chmurze zawierającej maszynę wirtualną ("tailspin-azdatatier.cloudapp.net" w tym przykładzie).
* Zewnętrzny port TCP punktu końcowego dla Pulpit zdalny ruchu (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych.
Przyczyna: docelowa maszyna wirtualna nie może zlokalizować urzędu zabezpieczeń w części nazwy użytkownika poświadczeń.

Jeśli nazwa użytkownika ma postać *SecurityAuthority* \\ *username* (przykład: CORP\User1.), część *SecurityAuthority* to nazwa komputera maszyny wirtualnej (dla urzędu zabezpieczeń lokalnych) lub nazwa domeny Active Directory.

Możliwe rozwiązania:

* Jeśli konto jest lokalne dla maszyny wirtualnej, upewnij się, że nazwa maszyny wirtualnej jest wpisana poprawnie.
* Jeśli konto znajduje się w domenie Active Directory, Sprawdź pisownię nazwy domeny.
* Jeśli jest to konto domeny Active Directory, a nazwa domeny jest wpisana prawidłowo, sprawdź, czy kontroler domeny jest dostępny w tej domenie. Jest to typowy problem z sieciami wirtualnymi platformy Azure, które zawierają kontrolery domeny, dla których kontroler domeny jest niedostępny, ponieważ nie został uruchomiony. Jako obejście można użyć konta administratora lokalnego zamiast konta domeny.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają.
Przyczyna: docelowa maszyna wirtualna nie może zweryfikować nazwy konta i hasła.

Komputer z systemem Windows może sprawdzać poprawność poświadczeń konta lokalnego lub konta domeny.

* W przypadku kont lokalnych użyj składni  \\ *nazwy użytkownika* ComputerName (przykład: SQL1\Admin4798).
* W przypadku kont domeny użyj składni *nazwa_domeny* \\ *nazwy użytkownika* (przykład: CONTOSO\peterodman).

Jeśli Twoja maszyna wirtualna została podwyższona do kontrolera domeny w nowym lesie Active Directory, konto administratora lokalnego, które zostało zalogowane, jest konwertowane na równoważne konto z tym samym hasłem w nowym lesie i domenie. Konto lokalne jest następnie usuwane.

Na przykład po zalogowaniu się przy użyciu konta lokalnego DC1\DCAdmin, a następnie podwyższeniu poziomu maszyny wirtualnej jako kontrolera domeny w nowym lesie dla domeny corp.contoso.com, konto lokalne DC1\DCAdmin zostanie usunięte i zostanie utworzone nowe konto domeny (CORP\DCAdmin) z tym samym hasłem.

Upewnij się, że nazwa konta jest nazwą, którą maszyna wirtualna może zweryfikować jako prawidłowe konto, a hasło jest poprawne.

Jeśli musisz zmienić hasło konta administratora lokalnego, zobacz [jak zresetować hasło lub usługę pulpit zdalny dla maszyn wirtualnych z systemem Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ten komputer nie może nawiązać połączenia z komputerem zdalnym.
Przyczyna: konto używane do nawiązania połączenia nie ma uprawnień do logowania Pulpit zdalny.

Każdy komputer z systemem Windows ma grupę lokalną użytkownicy Pulpit zdalny, która zawiera konta i grupy, które mogą logować się do niego zdalnie. Członkowie lokalnej grupy administratorów mają również dostęp, nawet jeśli te konta nie są wymienione w lokalnej grupie Użytkownicy Pulpit zdalny. W przypadku komputerów przyłączonych do domeny lokalna grupa administratorów zawiera również administratorów domeny dla domeny.

Upewnij się, że konto używane do nawiązania połączenia ma Pulpit zdalny prawa do logowania. Aby obejść ten element, należy użyć konta domeny lub administratora lokalnego do nawiązania połączenia przez Pulpit zdalny. Aby dodać żądane konto do grupy lokalnej Użytkownicy Pulpit zdalny, użyj przystawki programu Microsoft Management Console (**Narzędzia systemowe > Użytkownicy i grupy lokalne > grup > pulpit zdalny użytkowników**).

## <a name="next-steps"></a>Następne kroki
Jeśli żaden z tych błędów nie wystąpił i masz nieznany problem z połączeniem przy użyciu protokołu RDP, zobacz [Przewodnik rozwiązywania problemów dla Pulpit zdalny](troubleshoot-rdp-connection.md).

* Aby uzyskać informacje na temat rozwiązywania problemów z dostępem do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Jeśli masz problemy z używaniem Secure Shell (SSH) do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną z systemem Linux na platformie Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
