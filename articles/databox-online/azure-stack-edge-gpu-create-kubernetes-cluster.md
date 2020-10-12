---
title: Tworzenie klastra Kubernetes i zarządzanie nim na urządzeniu z systemem Azure Stack EDGE Pro GPU | Microsoft Docs
description: Zawiera opis sposobu tworzenia klastra Kubernetes i zarządzania nim na urządzeniu z systemem Windows PowerShell Azure Stack w wersji brzegowej Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cb783e5da7364f38944ce31ce49a6a6529658fe3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903209"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Nawiązywanie połączenia z klastrem Kubernetes i zarządzanie nim za pomocą polecenia kubectl na urządzeniu z systemem Azure Stack EDGE Pro GPU

Na urządzeniu Azure Stack EDGE Pro tworzony jest klaster Kubernetes podczas konfigurowania roli obliczeniowej. Po utworzeniu klastra Kubernetes można nawiązać połączenie z klastrem i zarządzać nim lokalnie z komputera klienckiego za pomocą narzędzia macierzystego, takiego jak *polecenia kubectl*.

W tym artykule opisano sposób nawiązywania połączenia z klastrem Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro, a następnie zarządzania nim za pomocą usługi *polecenia kubectl*. 


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z Azure Stack Edge.

2. Twoje urządzenie Azure Stack EDGE Pro zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).

3. Na urządzeniu została włączona rola obliczeniowa. Klaster Kubernetes został również utworzony na urządzeniu podczas konfigurowania obliczeń na urządzeniu zgodnie z instrukcjami w temacie [Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

4. Masz dostęp do systemu klienta systemu Windows z uruchomionym programem PowerShell 5,0 lub nowszym w celu uzyskania dostępu do urządzenia. Możesz również mieć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . 

5. Masz punkt końcowy interfejsu API Kubernetes ze strony **urządzenie** w lokalnym interfejsie użytkownika sieci Web. Aby uzyskać więcej informacji, zobacz instrukcje w [punkcie końcowym interfejsu API funkcji Get Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)


## <a name="connect-to-powershell-interface"></a>Połącz z interfejsem programu PowerShell

Po utworzeniu klastra Kubernetes można uzyskać dostęp do tego klastra w celu utworzenia przestrzeni nazw i użytkowników oraz przypisywania użytkowników do przestrzeni nazw. Będzie to wymagało połączenia z interfejsem programu PowerShell urządzenia. Wykonaj następujące kroki na kliencie systemu Windows z uruchomionym programem PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-rbac"></a>Konfigurowanie dostępu do klastra za pośrednictwem RBAC

Po utworzeniu klastra Kubernetes można użyć *polecenia kubectl* za pośrednictwem pliku Cmdlines, aby uzyskać dostęp do klastra. 

W tym podejściu utworzysz przestrzeń nazw i użytkownika. Następnie należy skojarzyć użytkownika z przestrzenią nazw. Należy również pobrać plik *konfiguracji* , który umożliwia klientowi Kubernetes bezpośrednie komunikowanie się z klastrem Kubernetes, który został utworzony bez konieczności nawiązywania połączenia z interfejsem programu PowerShell urządzenia z Azure Stack EDGE Pro.

1. Tworzenie przestrzeni nazw. Wpisz:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Dla nazw i nazw użytkowników stosowane są [konwencje nazewnictwa domen DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

    Oto przykładowe dane wyjściowe:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Utwórz użytkownika i Pobierz plik konfiguracji. Wpisz:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Nie można użyć *aseuser* jako nazwy użytkownika, ponieważ jest ona zarezerwowana dla domyślnego użytkownika skojarzonego z przestrzenią nazw IoT dla Azure Stack EDGE Pro.

    Oto przykładowe dane wyjściowe pliku konfiguracji:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Plik konfiguracji jest wyświetlany w postaci zwykłego tekstu. Skopiuj ten plik i Zapisz go jako plik *konfiguracji* . 

    > [!IMPORTANT]
    > Nie zapisuj pliku konfiguracji jako pliku *txt* , Zapisz plik bez rozszerzenia pliku.

4. Plik konfiguracji powinien znajdować się na żywo w `.kube` folderze profilu użytkownika na komputerze lokalnym. Skopiuj plik do tego folderu w profilu użytkownika.

    ![Lokalizacja pliku konfiguracji na kliencie](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. Skojarz przestrzeń nazw z utworzonym użytkownikiem. Wpisz:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Oto przykładowe dane wyjściowe:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Po utworzeniu pliku konfiguracji nie jest potrzebny fizyczny dostęp do klastra. Jeśli klient może wysłać polecenie ping do adresu IP urządzenia w Azure Stack EDGE Pro, powinno być możliwe skierowanie klastra przy użyciu poleceń *polecenia kubectl* .

6. Rozpocznij nową sesję programu PowerShell na kliencie. Nie musisz być połączony z interfejsem urządzenia. Teraz można zainstalować program `kubectl` na kliencie przy użyciu następującego polecenia:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Na przykład, jeśli węzeł główny Kubernetes miał uruchomioną 1.15.2, zainstaluj program v 1.15.2 na komputerze klienckim.

    > [!IMPORTANT]
    > Pobierz klienta, który jest skośny, nie więcej niż jedną wersję pomocniczą z serwera głównego. Wersja klienta, ale może prowadzić do programu głównego do jednej wersji pomocniczej. Na przykład wzorzec v 1.3 powinien współpracować z węzłami v 1.1, v 1.2 i v 1.3 i powinien współpracować z klientami w wersji 1.2, v 1.3 i v 1.4. Aby uzyskać więcej informacji na temat wersji klienta Kubernetes, zobacz [zasady obsługi systemu Kubernetes wersja i wersja](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Aby uzyskać więcej informacji na temat wersji Kubernetes Server w Azure Stack EDGE Pro, przejdź do pozycji Pobierz Kubernetes Server version.<!-- insert link-->
    > Czasami program `kubectl` jest preinstalowany w systemie, jeśli jest uruchomiony program Docker for Windows lub inne narzędzia. Ważne jest, aby pobrać określoną wersję programu `kubectl` zgodnie z opisem w tej sekcji, aby współdziałać z tym klastrem Kubernetes. 

    Instalacja trwa kilka minut.

7. Sprawdź, czy zainstalowana wersja jest pobrana. Należy określić ścieżkę bezwzględną do lokalizacji, w której `kubectl.exe` został zainstalowany w systemie.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Aby uzyskać więcej informacji na temat `kubectl` poleceń używanych do zarządzania klastrem Kubernetes, przejdź do [omówienia usługi polecenia kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Dodaj wpis DNS do pliku hosts w systemie. 

    1. Uruchom program Notepad jako administrator i Otwórz `hosts` plik znajdujący się w lokalizacji `C:\windows\system32\drivers\etc\hosts` . 
    2. Użyj informacji zapisanych na stronie **urządzenia** w lokalnym interfejsie użytkownika w poprzednim kroku, aby utworzyć wpis w pliku Hosts. 

        Na przykład skopiuj ten punkt końcowy, `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` Aby utworzyć następujący wpis z adresem IP urządzenia i domeną DNS: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Aby sprawdzić, czy można nawiązać połączenie z Kubernetes, wpisz:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Teraz możesz wdrażać aplikacje w przestrzeni nazw, a następnie wyświetlać te aplikacje i ich dzienniki.

> [!IMPORTANT]   
> Istnieje wiele poleceń, które nie będą mogły być uruchamiane, na przykład polecenia, które wymagają posiadania dostępu administratora. Można wykonywać tylko operacje, które są dozwolone w przestrzeni nazw.


## <a name="remove-kubernetes-cluster"></a>Usuń klaster Kubernetes

Aby usunąć klaster Kubernetes, należy usunąć konfigurację obliczeniową.

Aby uzyskać szczegółowe instrukcje, przejdź do obszaru [Usuwanie konfiguracji obliczeniowej](azure-stack-edge-j-series-manage-compute.md#remove-compute-configuration).
   

## <a name="next-steps"></a>Następne kroki

- [Wdróż bezstanową aplikację na Azure Stack Edge](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
