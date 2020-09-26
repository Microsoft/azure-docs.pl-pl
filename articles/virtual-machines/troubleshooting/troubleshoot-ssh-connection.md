---
title: Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure | Microsoft Docs
description: Jak rozwiązywać problemy, takie jak "połączenie SSH nie powiodło się" lub "odmowa połączenia SSH" dla maszyny wirtualnej platformy Azure z systemem Linux.
keywords: odmówiono połączenia SSH, błąd SSH, usługa Azure SSH, Niepowodzenie połączenia SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 678bad67b454ec0930d2cf30df45ba7b2c822e35
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371460"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Troubleshoot SSH connections to an Azure Linux VM that fails, errors out, or is refused (Rozwiązywanie problemów dotyczących połączeń SSH z maszyną wirtualną z systemem Linux — niepowodzenia, błędy lub odmowa połączenia)
Ten artykuł ułatwia znajdowanie i rozwiązywanie problemów występujących w związku z błędami Secure Shell (SSH), niepowodzeń połączeń SSH lub odrzucanie protokołu SSH podczas próby nawiązania połączenia z maszyną wirtualną z systemem Linux. Możesz Azure Portal użyć rozszerzenia dostępu do interfejsu wiersza polecenia platformy Azure dla systemu Linux w celu rozwiązywania problemów z połączeniami i ich rozwiązywania.


Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Szybkie rozwiązywanie problemów
Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

1. [Zresetuj konfigurację protokołu SSH](#reset-the-ssh-configuration).
2. [Zresetuj poświadczenia](#reset-ssh-credentials-for-a-user) dla użytkownika.
3. Sprawdź, czy reguły [sieciowej grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md) zezwalają na ruch protokołu SSH.
   * Upewnij się, że istnieje [reguła sieciowej grupy zabezpieczeń](#check-security-rules) zezwalająca na ruch SSH (domyślnie port TCP 22).
   * Nie można użyć przekierowania/mapowania portów bez użycia modułu równoważenia obciążenia platformy Azure.
4. Sprawdź [kondycję zasobów maszyny wirtualnej](../../service-health/resource-health-overview.md).
   * Upewnij się, że maszyna wirtualna jest w dobrej kondycji.
   * Jeśli [Diagnostyka rozruchu jest włączona](boot-diagnostics.md), upewnij się, że maszyna wirtualna nie zgłasza błędów rozruchu w dziennikach.
5. [Uruchom ponownie maszynę wirtualną](#restart-a-vm).
6. [Wdróż ponownie maszynę wirtualną](#redeploy-a-vm).

Kontynuuj odczytywanie, aby zapoznać się z bardziej szczegółowymi krokami rozwiązywania problemów.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Dostępne metody rozwiązywania problemów z połączeniami SSH
Możesz zresetować poświadczenia lub konfigurację protokołu SSH przy użyciu jednej z następujących metod:

* [Azure Portal](#use-the-azure-portal) — doskonały, jeśli chcesz szybko zresetować konfigurację SSH lub klucz SSH i nie masz zainstalowanych narzędzi platformy Azure.
* [Konsola szeregowa maszyny wirtualnej platformy Azure](https://aka.ms/serialconsolelinux) — konsola szeregowa maszyny wirtualnej będzie działała niezależnie od konfiguracji SSH i zapewnia interaktywną konsolę dla maszyny wirtualnej. W rzeczywistości sytuacje, w których zaprojektowano konsolę szeregową, aby pomóc w rozwiązaniu problemu. Szczegółowe informacje można znaleźć poniżej.
* [Interfejs wiersza polecenia platformy Azure](#use-the-azure-cli) — Jeśli jesteś już w wierszu poleceń, szybko Zresetuj konfigurację SSH lub poświadczenia. Jeśli pracujesz z klasyczną maszyną wirtualną, możesz użyć [klasycznego interfejsu wiersza polecenia platformy Azure](#use-the-azure-classic-cli).
* [Rozszerzenie VMAccessForLinux platformy Azure](#use-the-vmaccess-extension) — tworzenie i ponowne używanie plików definicji JSON w celu zresetowania konfiguracji SSH lub poświadczeń użytkownika.

Po każdym kroku rozwiązywania problemów spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Jeśli nadal nie można nawiązać połączenia, wypróbuj następny krok.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Azure Portal umożliwia szybkie Resetowanie konfiguracji SSH lub poświadczeń użytkownika bez instalowania narzędzi na komputerze lokalnym.

Aby rozpocząć, wybierz maszynę wirtualną w Azure Portal. Przewiń w dół do sekcji **Pomoc techniczna i rozwiązywanie problemów** , a następnie wybierz pozycję **zresetuj hasło** , jak w poniższym przykładzie:

![Zresetuj konfigurację SSH lub poświadczenia w Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Resetowanie konfiguracji SSH
Aby zresetować konfigurację protokołu SSH, wybierz `Reset configuration only` w sekcji **tryb** tak jak w poprzednim zrzucie ekranu, a następnie wybierz pozycję **Aktualizuj**. Po zakończeniu tej akcji spróbuj ponownie uzyskać dostęp do maszyny wirtualnej.

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Aby zresetować poświadczenia istniejącego użytkownika, wybierz jedną `Reset SSH public key` lub `Reset password` w sekcji **tryb** jak w poprzednim zrzucie ekranu. Określ nazwę użytkownika i klucz SSH lub nowe hasło, a następnie wybierz pozycję  **Aktualizuj**.

Możesz również utworzyć użytkownika z uprawnieniami sudo na maszynie wirtualnej z tego menu. Wprowadź nową nazwę użytkownika i skojarzone hasło lub klucz SSH, a następnie wybierz pozycję **Aktualizuj**.

### <a name="check-security-rules"></a>Sprawdź reguły zabezpieczeń

Użyj [weryfikacji przepływu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) , aby potwierdzić, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące zasady grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń "Zezwalaj" istnieje i ma priorytet dla portu SSH (domyślnie 22). Aby uzyskać więcej informacji, zobacz Używanie obowiązujących [reguł zabezpieczeń w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Sprawdzanie routingu

Użyj funkcji [następnego przeskoku](../../network-watcher/diagnose-vm-network-routing-problem.md) Network Watcher, aby upewnić się, że trasa nie uniemożliwia kierowania ruchu do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące trasy, aby zobaczyć wszystkie efektywne trasy dla interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z efektywnych tras w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Korzystanie z konsoli szeregowej maszyny wirtualnej platformy Azure
[Konsola szeregowa maszyny wirtualnej platformy Azure](./serial-console-linux.md) zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych z systemem Linux. Konsoli programu można użyć do rozwiązywania problemów z połączeniem SSH w interakcyjnej powłoki. Upewnij się, że spełniono [wymagania wstępne](./serial-console-linux.md#prerequisites) dotyczące korzystania z konsoli szeregowej, i spróbuj użyć poniższych poleceń, aby jeszcze bardziej rozwiązać problemy z łącznością SSH.

### <a name="check-that-ssh-is-running"></a>Sprawdź, czy jest uruchomiony protokół SSH
Aby sprawdzić, czy na maszynie wirtualnej jest uruchomiony protokół SSH, można użyć następującego polecenia:

```console
ps -aux | grep ssh
```

Jeśli istnieją jakieś dane wyjściowe, protokół SSH jest uruchomiony.

### <a name="check-which-port-ssh-is-running-on"></a>Sprawdź, który port SSH jest uruchomiony

Możesz użyć następującego polecenia, aby sprawdzić, który port SSH jest uruchomiony:

```console
sudo grep Port /etc/ssh/sshd_config
```

Dane wyjściowe będą wyglądać następująco:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Jeśli jeszcze tego nie zrobiono, zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

Jeśli utworzono i przekazano niestandardowy obraz dysku systemu Linux, upewnij się, że jest zainstalowany [Agent Microsoft Azure Linux](../extensions/agent-linux.md) w wersji 2.0.5 lub nowszej. W przypadku maszyn wirtualnych utworzonych przy użyciu obrazów galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Możesz najpierw spróbować zresetować konfigurację SSH do wartości domyślnych i ponownie uruchomić serwer SSH na maszynie wirtualnej. Nie powoduje to zmiany nazwy konta użytkownika, hasła lub kluczy SSH.
Poniższy przykład używa [AZ VM User Reset-SSH](/cli/azure/vm/user) do resetowania konfiguracji SSH na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Poniższy przykład używa [AZ VM User Update](/cli/azure/vm/user) do resetowania poświadczeń dla `myUsername` do wartości określonej w `myPassword` , na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

W przypadku korzystania z uwierzytelniania za pomocą klucza SSH można zresetować klucz SSH dla danego użytkownika. Poniższy przykład używa **AZ VM Access Set-Linux-User** do aktualizowania klucza SSH przechowywanego w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie `myUsername` na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Użyj rozszerzenia VMAccess
Rozszerzenie dostępu do maszyny wirtualnej dla systemu Linux odczytuje w pliku JSON, który definiuje akcje do wykonania. Te akcje obejmują Resetowanie SSHD, resetowanie klucza SSH lub Dodawanie użytkownika. Nadal używasz interfejsu wiersza polecenia platformy Azure, aby wywołać rozszerzenie VMAccess, ale w razie potrzeby możesz ponownie użyć plików JSON na wielu maszynach wirtualnych. Takie podejście umożliwia utworzenie repozytorium plików JSON, które następnie można wywołać dla danego scenariusza.

### <a name="reset-sshd"></a>Resetuj SSHD
Utwórz plik o nazwie `settings.json` z następującą zawartością:

```json
{
    "reset_ssh":True
}
```

Korzystając z interfejsu wiersza polecenia platformy Azure, wywołaj `VMAccessForLinux` rozszerzenie w celu zresetowania połączenia usługi sshd, określając plik JSON. Poniższy przykład używa [AZ VM Extension Set](/cli/azure/vm/extension) do resetowania SSHD na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Jeśli SSHD wydaje się działać prawidłowo, można zresetować poświadczenia dla użytkownika Giver. Aby zresetować hasło dla użytkownika, Utwórz plik o nazwie `settings.json` . Poniższy przykład resetuje poświadczenia dla programu `myUsername` do wartości określonej w `myPassword` . Wprowadź następujące wiersze do `settings.json` pliku przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Aby zresetować klucz SSH dla użytkownika, należy najpierw utworzyć plik o nazwie `settings.json` . Poniższy przykład resetuje poświadczenia dla `myUsername` do wartości określonej w `myPassword` , na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Wprowadź następujące wiersze do `settings.json` pliku przy użyciu własnych wartości:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Po utworzeniu pliku JSON Użyj interfejsu wiersza polecenia platformy Azure, aby wywołać `VMAccessForLinux` rozszerzenie w celu zresetowania poświadczeń użytkownika ssh przez określenie pliku JSON. Poniższy przykład resetuje poświadczenia na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure
Jeśli jeszcze tego nie zrobiono, [Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure i nawiąż połączenie z subskrypcją platformy Azure](/cli/azure/install-classic-cli). Upewnij się, że używasz trybu Menedżer zasobów w następujący sposób:

```azurecli
azure config mode arm
```

Jeśli utworzono i przekazano niestandardowy obraz dysku systemu Linux, upewnij się, że jest zainstalowany [Agent Microsoft Azure Linux](../extensions/agent-linux.md) w wersji 2.0.5 lub nowszej. W przypadku maszyn wirtualnych utworzonych przy użyciu obrazów galerii to rozszerzenie dostępu jest już zainstalowane i skonfigurowane.

### <a name="reset-ssh-configuration"></a>Resetowanie konfiguracji SSH
Sama konfiguracja SSHD może być błędnie skonfigurowana lub usługa napotkała błąd. Możesz zresetować SSHD, aby upewnić się, że sama konfiguracja SSH jest prawidłowa. Resetowanie SSHD powinno być pierwszym krokiem rozwiązywania problemów.

Poniższy przykład resetuje SSHD na maszynie wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` . Użyj własnych nazw maszyn wirtualnych i grup zasobów w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reset SSH credentials for a user (Resetowanie poświadczeń protokołu SSH użytkownika)
Jeśli SSHD wydaje się działać prawidłowo, można zresetować hasło dla użytkownika Giver. Poniższy przykład resetuje poświadczenia dla `myUsername` do wartości określonej w `myPassword` , na maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

W przypadku korzystania z uwierzytelniania za pomocą klucza SSH można zresetować klucz SSH dla danego użytkownika. Poniższy przykład aktualizuje klucz SSH zapisany w `~/.ssh/id_rsa.pub` dla użytkownika o nazwie w `myUsername` maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a>Ponowne uruchamianie maszyny wirtualnej
Jeśli zresetowano konfigurację SSH i poświadczenia użytkownika lub wystąpił błąd podczas wykonywania tej czynności, możesz spróbować ponownie uruchomić maszynę wirtualną, aby rozwiązać podstawowe problemy z przetwarzaniem.

### <a name="azure-portal"></a>Azure Portal
Aby ponownie uruchomić maszynę wirtualną przy użyciu Azure Portal, wybierz maszynę wirtualną, a następnie wybierz pozycję **Uruchom ponownie** tak, jak w poniższym przykładzie:

![Uruchom ponownie maszynę wirtualną w Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Poniższy przykład używa [AZ VM restart](/cli/azure/vm) , aby ponownie uruchomić maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Poniższy przykład powoduje ponowne uruchomienie maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Ponowne wdrażanie maszyny wirtualnej
Możesz ponownie wdrożyć maszynę wirtualną w innym węźle na platformie Azure, co może poprawić wszystkie podstawowe problemy z siecią. Aby uzyskać informacje o ponownym wdrażaniu maszyny wirtualnej, zobacz [ponowne wdrażanie maszyny wirtualnej w nowym węźle platformy Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> Po zakończeniu tej operacji dane o dyskach tymczasowych zostaną utracone i zostaną zaktualizowane dynamiczne adresy IP skojarzone z maszyną wirtualną.
>
>

### <a name="azure-portal"></a>Azure Portal
Aby ponownie wdrożyć maszynę wirtualną przy użyciu Azure Portal, wybierz maszynę wirtualną i przewiń w dół do sekcji **Pomoc techniczna i rozwiązywanie problemów** . Wybierz pozycję **Wdróż** ponownie tak, jak w poniższym przykładzie:

![Ponowne wdrażanie maszyny wirtualnej w Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W poniższym przykładzie użyto [AZ VM redeploy](/cli/azure/vm) , aby ponownie wdrożyć maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

Poniższy przykład ponownie wdraża maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` . Użyj własnych wartości w następujący sposób:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Spróbuj wykonać poniższe kroki, aby rozwiązać najczęstsze błędy połączeń SSH dla maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. Po każdym kroku spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

* Zresetuj dostęp zdalny z [Azure Portal](https://portal.azure.com). Na Azure Portal wybierz maszynę wirtualną, a następnie wybierz pozycję **Zresetuj zdalny...**.
* Uruchom ponownie maszynę wirtualną. Na [Azure Portal](https://portal.azure.com)wybierz maszynę wirtualną, a następnie wybierz pozycję **Uruchom ponownie**.

* Wdróż ponownie maszynę wirtualną w nowym węźle platformy Azure. Aby uzyskać informacje o sposobie ponownego wdrażania maszyny wirtualnej, zobacz [ponowne wdrażanie maszyny wirtualnej w nowym węźle platformy Azure](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    Po zakończeniu tej operacji dane dysku tymczasowych zostaną utracone, a dynamiczne adresy IP skojarzone z maszyną wirtualną zostaną zaktualizowane.
* Postępuj zgodnie z instrukcjami w temacie [jak zresetować hasło lub SSH dla maszyn wirtualnych opartych](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) na systemie Linux, aby:

  * Zresetuj hasło lub klucz SSH.
  * Utwórz konto użytkownika *sudo* .
  * Zresetuj konfigurację protokołu SSH.
* Sprawdź kondycję zasobów maszyny wirtualnej pod kątem problemów z platformą.<br>
     Wybierz maszynę wirtualną i przewiń w dół **Ustawienia**  >  **Sprawdź kondycję**.

## <a name="additional-resources"></a>Zasoby dodatkowe
* Jeśli po wykonaniu kolejnych kroków nadal nie można SSH z maszyną wirtualną, zobacz [bardziej szczegółowe kroki rozwiązywania problemów](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby zapoznać się z dodatkowymi krokami w celu rozwiązania problemu.
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
* Aby uzyskać więcej informacji na temat rozwiązywania problemów z maszynami wirtualnymi utworzonymi przy użyciu klasycznego modelu wdrażania, zobacz [jak zresetować hasło lub SSH dla maszyn wirtualnych opartych](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)na systemie Linux.
