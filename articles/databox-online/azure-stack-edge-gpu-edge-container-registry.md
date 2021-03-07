---
title: Włączanie rejestru kontenera brzegowego na urządzeniu z systemem Azure Stack Edge
description: Zawiera opis sposobu włączania lokalnego rejestru kontenera brzegowego na urządzeniu z systemem Azure Stack brzeg Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f19625f1d558071ccb29487efe56146756c7692c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437540"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Włącz rejestr kontenerów Edge na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób włączania rejestru kontenera brzegowego i używania go z klastra Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro. W przykładzie używanym w artykule szczegółowo przedstawiono sposób wypychania obrazu z rejestru źródłowego, w tym przypadku, rejestru kontenerów firmy Microsoft do rejestru na urządzeniu brzegowym Azure Stack, rejestr kontenerów brzegowych.

### <a name="about-edge-container-registry"></a>Informacje o rejestrze kontenera Edge

Kontenerowe aplikacje obliczeniowe są uruchamiane na obrazach kontenerów, a te obrazy są przechowywane w rejestrach. Rejestry mogą być publiczne, takie jak usługa Docker Hub, prywatna lub zarządzana przez dostawcę usług w chmurze, taka jak Azure Container Registry. Aby uzyskać więcej informacji, zobacz [Informacje o rejestrach, repozytoriach i obrazach](../container-registry/container-registry-concepts.md).

Rejestr kontenerów brzegowych udostępnia repozytorium na brzegu na urządzeniu Azure Stack EDGE Pro. Możesz użyć tego rejestru do przechowywania prywatnych obrazów kontenerów i zarządzania nimi.

W środowisku wielowęzłowym obrazy kontenerów mogą być pobierane i wypychane do rejestru kontenerów brzegowych. Wszystkie aplikacje brzegowe mogą używać rejestru kontenerów Edge do kolejnych wdrożeń.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z Azure Stack Edge.

1. Twoje urządzenie Azure Stack EDGE Pro zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Na urządzeniu została włączona rola obliczeniowa. Klaster Kubernetes został również utworzony na urządzeniu podczas konfigurowania obliczeń na urządzeniu zgodnie z instrukcjami w temacie [Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Masz punkt końcowy interfejsu API Kubernetes ze strony **urządzenie** w lokalnym interfejsie użytkownika sieci Web. Aby uzyskać więcej informacji, zapoznaj się z instrukcjami w artykule [pobieranie punktu końcowego interfejsu API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Masz dostęp do systemu klienckiego z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). W przypadku korzystania z klienta systemu Windows system powinien uruchomić program PowerShell w wersji 5,0 lub nowszej, aby uzyskać dostęp do urządzenia.

    1. Jeśli chcesz ściągnąć i wypchnąć własne obrazy kontenerów, upewnij się, że system ma zainstalowany program Docker Client. W przypadku korzystania z klienta systemu Windows [Zainstaluj program Docker Desktop w systemie Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Włącz rejestr kontenerów jako dodatek

Pierwszym krokiem jest włączenie rejestru kontenera brzegowego jako dodatku.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Aby włączyć rejestr kontenerów jako dodatek, wpisz: 

    `Set-HcsKubernetesContainerRegistry`
    
    Wykonanie tej operacji może potrwać kilka minut.

    Oto przykładowe dane wyjściowe tego polecenia:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Aby uzyskać szczegółowe informacje o rejestrze kontenera, wpisz:

    `Get-HcsKubernetesContainerRegistryInfo`

    Oto przykład z tego polecenia:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Zanotuj nazwę użytkownika i hasło z danych wyjściowych `Get-HcsKubernetesContainerRegistryInfo` . Te poświadczenia są używane do logowania się do rejestru kontenerów brzegowych podczas wypychania obrazów.         


## <a name="manage-container-registry-images"></a>Zarządzanie obrazami rejestru kontenerów

Możesz chcieć uzyskać dostęp do rejestru kontenerów spoza urządzenia Azure Stack Edge. Możesz również wypchnąć lub ściągnąć obrazy w rejestrze.

Wykonaj następujące kroki, aby uzyskać dostęp do rejestru kontenera Edge:

1. Pobierz szczegóły punktu końcowego dla rejestru kontenerów brzegowych.
    1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **urządzenie**.
    1. Zlokalizuj **punkt końcowy rejestru kontenera Edge**.
        ![Punkt końcowy rejestru kontenera brzegowego na stronie urządzenia](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Skopiuj ten punkt końcowy i utwórz odpowiedni wpis DNS w `C:\Windows\System32\Drivers\etc\hosts` pliku klienta, aby połączyć się z punktem końcowym rejestru kontenera brzegowego. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Dodaj wpis DNS dla punktu końcowego rejestru kontenera brzegowego](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Pobierz certyfikat rejestru kontenera brzegowego z lokalnego interfejsu użytkownika. 
    1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **Certyfikaty**.
    1. Znajdź wpis dla **certyfikatu rejestru kontenera Edge**. Z prawej strony tego wpisu wybierz pozycję **Pobierz** , aby pobrać certyfikat rejestru kontenera brzegowego w systemie klienckim, który będzie używany do uzyskiwania dostępu do urządzenia. 

        ![Pobieranie kontenera krawędzi — certyfikat punktu końcowego rejestru](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Zainstaluj pobrany certyfikat na komputerze klienckim. W przypadku korzystania z klienta systemu Windows wykonaj następujące kroki: 
    1. Wybierz certyfikat i w **Kreatorze importu certyfikatów** wybierz pozycję przechowuj lokalizację jako **maszynę lokalną**. 

        ![Zainstaluj certyfikat 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Zainstaluj certyfikat na komputerze lokalnym w zaufanym magazynie głównym. 

        ![Zainstaluj certyfikat 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Po zainstalowaniu certyfikatu uruchom ponownie klienta platformy Docker w systemie.

1. Zaloguj się do rejestru kontenerów Edge. Wpisz:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Podaj punkt końcowy rejestru kontenera brzegowego ze strony **urządzenia** oraz nazwę użytkownika i hasło, które zostały uzyskane z danych wyjściowych `Get-HcsKubernetesContainerRegistryInfo` . 

1. Użyj poleceń Docker push lub pull do wypychania lub ściągania obrazów kontenerów z rejestru kontenerów.
 
    1. Pobierz obraz z obrazu programu Microsoft Container Registry. Wpisz:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Utwórz alias obrazu, który został pobrany z w pełni kwalifikowaną ścieżką do rejestru.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Wypchnij obraz do rejestru.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Uruchom obraz, który został wypchnięci do rejestru.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Oto przykładowe dane wyjściowe poleceń ściągania i wypychania:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Przeglądaj w celu `http://localhost:8080` wyświetlenia uruchomionego kontenera. W takim przypadku zobaczysz serwer webnginx z systemem.

    ![Wyświetlanie uruchomionego kontenera](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Aby zatrzymać i usunąć kontener, naciśnij klawisz `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Używanie obrazów rejestru kontenera brzegowego za pośrednictwem Kubernetesch

Teraz można wdrożyć obraz, który został wypchnięci w rejestrze kontenerów krawędzi z poziomu Kubernetesowych.

1. Aby wdrożyć obraz, należy skonfigurować dostęp do klastra za pośrednictwem *polecenia kubectl*. Utwórz obszar nazw, użytkownika, Udziel użytkownikowi dostępu do przestrzeni nazw i Pobierz plik *konfiguracji* . Upewnij się, że możesz nawiązać połączenie z Kubernetesami. 
    
    Wykonaj wszystkie kroki opisane w [sekcji Łączenie z klastrem Kubernetes i zarządzanie nim za pośrednictwem programu polecenia kubectl na urządzeniu z systemem Azure Stack brzeg Pro GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Oto przykładowe dane wyjściowe dla przestrzeni nazw na urządzeniu, z którego użytkownik może uzyskać dostęp do klastra Kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Wpisy tajne ściągania obrazów są już ustawione we wszystkich przestrzeniach nazw Kubernetes na urządzeniu. Wpisy tajne można uzyskać za pomocą `get secrets` polecenia. Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Wdróż element pod względem przestrzeni nazw za pomocą polecenia kubectl. Użyj poniższego `yaml` . 

    Zastąp obraz: `<image-name>` obraz jest wypychany do rejestru kontenerów. Zapoznaj się z wpisami tajnymi w przestrzeniach nazw przy użyciu imagePullSecrets o nazwie: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Zastosuj wdrożenie w przestrzeni nazw utworzonej przy użyciu polecenia Zastosuj. Sprawdź, czy kontener jest uruchomiony. Oto przykładowe dane wyjściowe:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Usuwanie obrazów rejestru kontenera

Magazyn Container Registry Edge jest hostowany w udziale lokalnym w ramach urządzenia Azure Stack EDGE Pro, które jest ograniczone przez dostępny magazyn na urządzeniu. Odpowiedzialność za usuwanie nieużywanych obrazów platformy Docker z rejestru kontenerów przy użyciu interfejsu API platformy Docker HTTP v2 ( https://docs.docker.com/registry/spec/api/) .  

Aby usunąć co najmniej jeden obraz kontenera, wykonaj następujące kroki:

1. Ustaw nazwę obrazu na obraz, który chcesz usunąć.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Ustaw nazwę użytkownika i hasło dla rejestru kontenerów jako poświadczenia PS

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Wyświetl listę tagów skojarzonych z obrazem

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Wyświetl listę skrótu skojarzonego ze znacznikiem, który chcesz usunąć. Używa $tags z danych wyjściowych powyższego polecenia. Jeśli masz wiele tagów, wybierz jedną z nich i użyj w następnym poleceniu.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Usuń obraz przy użyciu podsumowania obrazu: tag

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Po usunięciu nieużywanych obrazów, miejsce skojarzone z obrazami, do których nie istnieje odwołanie, jest automatycznie odzyskiwane przez proces, który działa nocnie. 

## <a name="next-steps"></a>Następne kroki

- [Wdróż bezstanową aplikację na Azure Stack Edge](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
